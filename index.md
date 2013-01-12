---
layout: page
title: Yet another developer blog
tagline: some notes, reminders, findings and sharing
---
{% include JB/setup %}

<div class="row">
  <div class="nine columns">
    <div>
        {% for post in site.posts %}	
            <h3><a href="{{ post.url }}">{{ post.title }}</a></h3>
            <p>
                {{ post.content | strip_html | truncatewords:75}}
                <a href="{{ post.url }}"><strong>Read more.</strong></a><br/>
            </p>
            <p>
                <strong>
                    {{ post.date | date: "%B %e, %Y" }}
                </strong>
                | {{ post.category }}
                | <a href="http://thibaultlaurens.github.com{{ post.url }}/#disqus_thread" data-disqus-identifier="{{ post.url }}">comments</a>
            </p>
            {% if forloop.last %}
            {% else %}
                <hr>
            {% endif %}
            			
        {% endfor %}
    </div>
  </div>
  
  <div class="two columns offset-by-one">
              <a href="categories.html"><h4>Category</h4></a>
              <strong><ul>
                {% assign categories_list = site.categories %}
                {% include JB/categories_list %}
              </ul> </strong>
  </div>

  <div class="two columns offset-by-one">
              <h4>Blogroll</h4>
              <ul>
                  <strong><li><a target="_blank" title="Los Techies" href="http://lostechies.com/">Los Techies</a></li></strong>
                  <strong><li><a target="_blank" title="Scott Hanselman" href="http://www.hanselman.com/blog/">Scott Hanselman</a></li></strong>
                  <strong><li><a target="_blank" title="Haacked" href="http://haacked.com/">Phil Haack</a></li></strong>
                  <strong><li><a target="_blank" title="Martin Fowler" href="http://martinfowler.com">Martin Fowler</a></li></strong>
                  <strong><li><a target="_blank" title="Ian Robinson" href="http://iansrobinson.com">Ian Robinson</a></li></strong>
                  <strong><li><a target="_blank" title="High Scalability" href="http://highscalability.com/">High Scalability</a></li></strong>
                  <strong><li><a target="_blank" title="Coding Horror" href="http://www.codinghorror.com/blog/">Coding Horror</a></li></strong>
                  <strong><li><a target="_blank" title="Dr Dobbs" href="http://www.drdobbs.com/">Dr Dobbs</a></li></strong>
                  <strong><li><a target="_blank" title="Udi Dahan" href="http://www.udidahan.com/?blog=true">Udi Dahan</a></li></strong>
                  <strong><li><a target="_blank" title="David Catuhe" href="http://blogs.msdn.com/b/eternalcoding/">David Catuhe</a></li></strong>
                  <strong><li><a target="_blank" title="Julien Dollon" href="http://julien.dollon.net/">Julien Dollon</a></li></strong>
                  <strong><li><a target="_blank" title="Geek Monkey" href="http://geekmonkey.org/">Geek Monkey</a></li></strong>
                  <strong><li><a target="_blank" title="Alex Maccaw" href="http://blog.alexmaccaw.com/">Alex Maccaw</a></li></strong>
                  <strong><li><a target="_blank" title="How To Node" href="http://howtonode.org/">How To Node</a></li></strong>
                  <!--<strong><li><a target="_blank" title="" href=""></a></li></strong>-->
              </ul>
  </div>

  

</div>

