---
layout: home
---

<div class="index-content blog">
    <div class="section">

        <div class="entry" style="margin-bottom:50px;margin-left:30px;margin-top:30px">
            <blockquote>
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
</style>
