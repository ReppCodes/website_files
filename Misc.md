---
layout: page
title: Miscellaneous
---

This is where you'll find all the various rambling that doesn't fit into its own section.

<section>
 
    {%for post in site.posts %}
        {% if post.path contains 'misc' %}

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