---
layout: home
---

<div class="index-content blog">
    <div class="section" style="padding-top:0">

        <div class="entry" style="margin-bottom:50px;">
            <img src="/images/2016_08/home.jpg" style="margin:0 auto;"/>
            <blockquote style="margin-left:30px;margin-top:30px;">
                <h4>Hey，长岛，这里是关于你的记忆与回溯</h4>
            </blockquote>
        </div>

        <ul class="artical-list">
        {% for post in site.categories.happy %}
            <li>
                <h2><a href="{{ post.url }}">{{ post.title }}</a></h2>
                <div class="title-desc">{{ post.description }}</div>
            </li>
        {% endfor %}
        </ul>
    </div>
</div>
<style>
.github-corner {
    display: none;
}

.home-menu-icon, .home-follow {
    background: rgba(0,0,0,.3);
}
</style>
