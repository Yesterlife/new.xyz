---
layout: home
---

<div class="index-content blog">
    <div class="section">
        <ul class="artical-cate">
            <li class="on"><a href="/"><span>Blog</span></a></li>
            <li><a href="/opinion"><span>Opinion</span></a></li>
        </ul>

        <div class="cate-bar"><span id="cateBar"></span></div>

        <ul class="artical-list">
        {% for post in site.categories.blog %}
            <li>
                <h2><a href="{{ post.url }}">{{ post.title }}</a></h2>
                <div class="title-desc">{{ post.description }}</div>
            </li>
        {% endfor %}
        </ul>
    </div>
    <div class="aside">
       <div class="content">
            <div class="avatar">
                <span></span>
            </div>
            <h3 class="text-center">An front-end engineer in BeiJing</h3>
            <p class="text-center">
                Life is a struggle.
                <br/>
                Go out for a walk, to enjoy the nature.
                <br/>
                Wanna go fast, go alone. Wanna go far, go together
                <br/>
            </p>
            <div class="friends">
                <h3 class="text-center">Friends</h3>
                <div class="text-center">
                    {% for friend in site.friends %}
                    <a href="{{ friend.link }}" class="friend-link">{{ friend.name }}</a>
                    {% endfor %}
                </div>
            </div>
       </div>
    </div>
</div>
