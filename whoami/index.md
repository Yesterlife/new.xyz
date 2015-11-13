---
layout: home
---

<div class="index-content project">
    <div class="section">
        <ul class="artical-cate">
            <li><a href="/"><span>Blog</span></a></li>
            <li><a href="/opinion"><span>Opinion</span></a></li>
            <li class="on"><a href="/whoami"><span>Me</span></a></li>
        </ul>

        <div class="cate-bar"><span id="cateBar"></span></div>

        <ul class="artical-list">
            <li>
                <div class="avatar">
                    <span></span>
                </div>
                <h3 class="text-center">FE@BEI_JING</h3>
                <blockquote>
                <div style="font-style:italic">
                    <p>
    Your work is going to fill a large part of your life, and the only way to be truly satisfied is to do what you believe is great work. And the only way to do great work is to love what you do. If you haven’t found it yet, keep looking. Don’t settle. As with all matters of the heart, you’ll know when you find it.
                    </p>
                    <p>
If you want to live your life in a creative way, as an artist, you have to not look back too much. You have to be willing to take whatever you’ve done and whoever you were and throw them away.
                    </p>
                    <p style="text-align:right">-- Steve Jobs</p>
                </div>
                </blockquote>
                <div class="friends">
                    <h3 class="text-center">Friends</h3>
                    <div class="text-center">
                        {% for friend in site.friends %}
                        <a href="{{ friend.link }}" class="friend-link">{{ friend.name }}</a>
                        {% endfor %}
                    </div>
                </div>
            </li>
        </ul>
    </div>
    <div class="aside">
    </div>
</div>
