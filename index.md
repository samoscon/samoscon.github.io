# Hello there!
<p>I am Dirk Van Meirvenne living in Belgium. I offer on github a framework to develop a ticketing system (for concerts, events, etc.) or a subscriptions system (to subscribe to the activities e.g. of a sports club). This framework has been build on top of a MVC controller framework.</p>

# Get in touch
<ul>
 <li><a href="https://samosconsulting.be/">My professional website</a></li>
</ul>

# My Blog
<ul>
 {% for post in site.posts %}
 <li>
  <a href="{{ post.url }}">{{ post.title }}</a>
 </li>
 {% endfor %}
</ul>
