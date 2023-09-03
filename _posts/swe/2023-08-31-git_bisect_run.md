---
title: The Magic of Git Bisect Run
published: true
tags: git, bash, automation
---

***
## Git
I think of myself as pretty good at git.  I bisect, I cherry-pick, I have even been known to poke my nose into the reflog from time to time. So when I read about [git bisect](https://git-scm.com/docs/git-bisect) for the first time, I was pretty interested. Despite pretty heavy usage, it had never even occurred to me that git could automatically run down bugs for me. At its core, all it does is hold your hand through a binary search of the commits between `start` and `stop`, and lets you mark each commit `good` or `bad`. Each step shrinks the problem space, and you've got your commit in `O(log n)` time! I just recently found an opportunity to use it in anger, and it's backwards enough (and useful enough) that I wanted to write it up and share it.

As part of my day job I often have to go to our product's [upstream](https://github.com/postgres/postgres) and backport in a fix or feature that we want to have. This often involves a *lot* of archaeology, to understand what exactly the goals and design were, and to fit it into and around the various conflicts with our codebase.  In this particular case, however, all I knew was that the current `main` branch of upstream had fixed a bug I was seeing, but the current version we were on had not. The usual approach of trawling mailing lists was not coming up with much, and a review showed me about 4500 commits between `HEAD` and where we sat. This was not getting done the hard way. In this hour of need, I remembered git bisect. It turns out that 4500 commits can be handled in about 12 rounds of bisection. Sounds like a plan, let's do that.

## Uphill Both Ways 
There were a couple of issues with applying git bisect to my problem though. The first of which is that the bug I was hunting was only observable from within a SQL repl, after having loaded specific objects into the database in a specific order.  This would be a huge pain to do any kind of manually, even leaving out the compilation and init steps to get a working postgres cluster stood up.  So clearly we needed to automate this somehow. Manually doing steps 12 times is *not* why we all took up programming. Makes for way worse blog posts too.

Luckily, git bisect takes a subcommand `run`, which will execute any shell command you care to, with arguments. Naturally, this mostly means `git bisect run bash /path/to/script.bash`, because what else would you do? So now I could script up calls to `make`, `psql`, and whatever else I needed. Presto, automatic testing for whether a commit exhibits my bug.

## And backwards
The second issue I ran into was that git bisect was really designed with the paradigm that you used to have a working build, and someone came a long and broke it. They really, really want you to be looking for errors, not the lack of them. So with `git bisect run`, your newer commit is always the "good" one, and the older commit is always the "bad" one. And the really frustrating one, your `run` command returning 0 will always mean that the commit being tested is **before** the one you want.  So if I ran my script and it threw the error I was running down the patch for, git bisect would punt me in the wrong direction.  This means that I had to catch the errors in my script, replace them with an `exit 0`, and then slap an `exit 1` at the end to mean "success".  It got confusing quick, but ultimately it's manageable enough.

## In the snow
The final hurdle I ran into is that, because my script included a pile of compile, init, and other steps, any of which could fail if the commit happened to be bad, I had to ignore any errors from those, but  without ruining the binary search we were conducting. Luckily, `git bisect run` has a special error code, `125`, that means exactly that. It's like they knew what they were doing or something.

So, back to my script and wrapping every other command that could fail in error checking and `exit 125` statements. Not too bad, and I was getting practice writing square brackets, so whatever.  We got there.

## But We Made It
When it was all said and done, I got the script put together, made the holy invocations, and in about 25 minutes I had the SHA for the exact commit that had fixed my bug in upstream. Beats the hell out of reading 4500 commit messages, doesn't it? Let's take a look at the actual spot we landed, to make it happen.

* We type our pleas into the terminal
```bash
git bisect start --term-new fixed --term-old broken
git checkout master
git bisect fixed
git checkout 07b39083c2aca003c4b1f289d7dc2368b5e2297a
git bisect broken
git bisect run bash /home/ajr/bisect_part_test.bash
```

* With the supporting texts

```bash
LD_LIBRARY_PATH=/usr/local/pgsql/
PATH=/usr/local/pgsql/bin:$PATH
./configure -enable-cassert --enable-debug --enable-depend --enable-tap-tests CFLAGS='-ggdb3 -O0 -fno-omit-frame-pointer' >/dev/null 2>&1
if [ $? -ne 0 ]; then
  echo "unrelated nope"
  exit 125
fi
echo "configured"

bear -- make -j8 -s
if [ $? -ne 0 ]; then
  echo "unrelated nope"
  exit 125
fi
echo "compiled"

make install -s
if [ $? -ne 0 ]; then
  echo "unrelated nope"
  exit 125
fi
echo "installed"

rm -rf /usr/local/pgsql/data
initdb -D /usr/local/pgsql/data >/dev/null 2>&1
if [ $? -ne 0 ]; then
  echo "unrelated nope"
  exit 125
fi
echo "cluster initialized"

pg_ctl -D /usr/local/pgsql/data -l /tmp/logfile start >/dev/null 2>&1
if [ $? -ne 0 ]; then
  echo "unrelated nope"
  exit 125
fi
echo "cluster started"

createdb ajr >/dev/null 2>&1
if [ $? -ne 0 ]; then
  echo "unrelated nope"
  exit 125
fi
echo "db created"

# this is the exit code we care about. flip error code because git bisect has opinions...
psql -d ajr -v ON_ERROR_STOP=1 -f /home/ajr/minimal_repro.sql
if [ $? -ne 0 ]; then
  exit 0
fi
echo "query ran"

pg_ctl -D /usr/local/pgsql/data -l /tmp/logfile stop >/dev/null 2>&1
if [ $? -ne 0 ]; then
  echo "unrelated nope"
  exit 125
fi
echo "cluster stopped"

exit 1
```

About 20 minutes later, git bisect run had recompiled Postgres 12 times, and informed me that one of two possible commits had fixed my bug in upstream.  Sure enough, I found some interesting lazy-loading of relcache partition statistics that would **definitely** address the behavior I was seeing.  Et voila, we get to skip straight to backporting and propagating the upstream changes to all the Greenplum-only code.  Thanks git!
