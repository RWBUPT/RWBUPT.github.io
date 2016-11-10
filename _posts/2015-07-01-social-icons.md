---
layout: post
title: "Social icons"
date: 2015-07-01 16:25:06
description: How to use existing and add new social icons
---

You can have social icons which could lead to your social profile.
Out-of-the box it has: 

<ul class="social-media">
  <li>
    <a title="Github"
      href="https://github.com/{{ site.social.github }}"
      target="_blank"><i class="fa fa-github fa-2x"></i></a>
  </li>
  <li>
    <a title="StackOverflow"
      href="http://stackoverflow.com/users/1252056/{{ site.social.stackoverflow }}"
      target="_blank"><i class="fa fa-stack-overflow fa-2x"></i></a>
  </li>
  <li>
    <a title="LinkedIn"
      href="https://www.linkedin.com/in/{{ site.social.linkedin }}"
      target="_blank"><i class="fa fa-linkedin fa-2x"></i></a>
  </li>
  <li>
    <a title="Instagram"
      href="https://instagram.com/{{ site.social.instagram }}"
      target="_blank"><i class="fa fa-instagram fa-2x"></i></a>
  </li>
  <li>
    <a title="Last.fm"
      href="http://lastfm.com/user/{{ site.social.lastfm }}"
      target="_blank"><i class="fa fa-lastfm fa-2x"></i></a>
  </li>
  <li>
    <a title="RSS"
      href="{{site.url}}/{{ site.social.rss }}"
      target="_blank"><i class="fa fa-rss fa-2x"></i></a>
  </li>
</ul>

They could be setup in `_config.yml`.

To add more icons do following steps:

 - choose an icon you want to use: [Font Awesome Icons](https://fortawesome.github.io/Font-Awesome/icons/)
 - add variable in `_config.yml`
 - add icon in `social.html` with check if variable exists:
 
{% highlight html %}
{% raw %}
 {% if site.social.rss %}
  <li>
    <a title="{{ site.social.<your_social_variable> }}" 
       href="{{site.url}}/{{ site.social.<your_social_variable> }}" 
       target="_blank"><font_awesome_icon></i></a>
  </li>
{% endif %}
{% endraw %}
{% endhighlight html %}

<div id="disqus_thread"></div>
<script type="text/javascript">
    /* * * CONFIGURATION VARIABLES: EDIT BEFORE PASTING INTO YOUR WEBPAGE * * */
    var disqus_shortname = 'example'; // required: replace example with your forum shortname

    // The following are highly recommended additional parameters. Remove the slashes in front to use.
    // var disqus_identifier = 'unique_dynamic_id_1234';
    // var disqus_url = 'http://example.com/permalink-to-page.html';

    /* * * DON'T EDIT BELOW THIS LINE * * */
    (function() {
        var dsq = document.createElement('script'); dsq.type = 'text/javascript'; dsq.async = true;
        dsq.src = 'http://' + disqus_shortname + '.disqus.com/embed.js';
        (document.getElementsByTagName('head')[0] || document.getElementsByTagName('body')[0]).appendChild(dsq);
    })();
</script>
<noscript>Please enable JavaScript to view the <a href="http://disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>
<a href="http://disqus.com" class="dsq-brlink">blog comments powered by <span class="logo-disqus">Disqus</span></a>
参见官方文档。