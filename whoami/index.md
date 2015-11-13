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
                <h3 class="text-center">AN FE_GITHUBBER @BEI_JING</h3>
                <p class="text-center">
                    The life of a programmer is mostly a never-ending struggle.

                    Solving problems in an always-changing technical landscape means that programmers are always learning new things.

                    In this case, “learning new things” is a euphemism for “battling against our own ignorance.”

                    Even if a programmer is just fixing a bug in code that uses a familiar technology, sometimes the software we create is so complex that simply understanding what is going on can take an entire day. If you write code, you will struggle.
                </p>
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
