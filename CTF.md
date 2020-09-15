---
layout: page
title: CTF Writeups
---

I enjoy offensive cybersecurity a great deal, and spend quite a bit of time reading up and going after various CTFs.  This section of my website
will initially house CTF writeups from HackTheBox.  If and when I get a bit more serious about it, I'll also post whatever bug bounty or other security research
findings I have here.  Stay tuned!

<section>
 
    {%for post in site.posts %}
        {% if post.path contains 'ctf' %}

            {% capture year %}{{ post.date | date: '%Y' }}{% endcapture %}
            {% capture nyear %}{{ post.next.date | date: '%Y' }}{% endcapture %}

                {% if year != nyear %}
                    <h3>{{ post.date | date: '%Y' }}</h3>
                {% endif %}
                <ul>

                <li><time>{{ post.date | date:"%d %b" }} - </time>
                
                <a href="{{ post.url | prepend: site.baseurl | append: ".html" | replace: '//', '/' }}">
                    {{ post.title }}
                </a>

                </li>

                <p>{{ post.content | strip_html | truncatewords:50 }}</p>
                </ul>
        {% endif %}

    {% endfor %}
        

</section>