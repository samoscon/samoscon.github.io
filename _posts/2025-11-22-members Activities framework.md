---
layout: post
author: dirkvm
---
<p class="h1">High level overview</p>
<p>The memberactivities framework requires a specific set-up in your
client project. An example of this set-up can be found under the
‘vendor/samoscon/membersactivities-framework/example’ directory.</p>
<p>The below illustration provides an overview how the different
files in the example collaborate with the framework.</p>
<p><img src="/assets/1 high level overview_html_42ddbf75.gif" name="Image1" align="center" width="95%" height="95%" border="1"/>
</p><br/>
<p class="h2 text-primary mt-2">The controller framework</p>
<p class="h3 text-primary mt-3">The Controller - the basics</p>
<p>The membersactivities is based on an <a href="https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller">MVC (Model - View -
Controller) framework</a>. Each time a client browser sends a request to
the webhost, the request will be forwarded to a Controller that will
handle the request. 
</p>
<p>Therefor your client project needs an <b>index.php</b> file in the
root folder of the client project with the following content:</p>
<p><img src="/assets/IndexFile.gif" name="Image3" align="center" width="60%" height="60%" border="1"/>
</p><br/>
<p>Each url request to your
domain should be directed to the index.php file followed by a path
name. This means that a url should be constructed as
[domainname].index.php/[pathname]?[parameters]. You can avoid the use
of the index.php in each url by parameterizing your webhost server
access file. In an Apache 2 server, this would mean you can add an
<b>.htaccess</b> file in the root
folder of your project with the following content included:</p>
<p>...</p>
<p><i>RewriteEngine on</i></p>
<p><i>RewriteCond %{SERVER_PORT} ^80$</i></p>
<p><i>RewriteRule ^.*$ https://%{SERVER_NAME}%{REQUEST_URI}[R=301,L]</i></p>
<p><br/></p>
<p><i>RewriteCond %{REQUEST_FILENAME} !-f</i></p>
<p><i>RewriteCond %{REQUEST_FILENAME} !-d</i></p>
<p><i>RewriteRule ^(.*)\/?.*$ index.php/$1 [L,QSA,NC]</i></p>
<p>...</p>

<p>With this redirect, your url request can have the structure [domainname]/[pathname]?[parameters] (e.g. “app.myticketing.com/admin” or “app.myticketing.com/editMember?id=234585”).</p>
<p class="h3 text-primary mt-3">The Controller::Run</p>
<p><cite><span style="font-style: normal">When the index.php
initiates the Controller to “run”, The Controller will execute 2
distinct stages. In a first stage, the Controller will “initiate
the application”. This initialisation is executed in 2 different
steps, i.e. </span></cite>
</p>
<ol>
	<li><p><cite><span style="font-style: normal">the set-up of global
	variables and the passwords for your database, mailserver,
	paymentsystem (in the example based on mollie), Google Wallet
	Tickets API, etc., etc.  . This set-up is done through a file
	‘</span></cite><cite><span style="font-style: normal"><span style="font-weight: normal">app_options.ini’
	which is located in a folder ‘</span></span></cite><cite><span style="font-style: normal"><b>/config</b></span></cite><cite><span style="font-style: normal"><span style="font-weight: normal">’
	in the root folder of your project. The content of this file will be
	discussed in more detail in a later chapter</span></span></cite></p></li>
	<li><p><cite><span style="font-style: normal">the definition of the
	control flow of the application. Default this flow will be defined
	through a XML file ‘</span></cite><cite><span style="font-style: normal"><b>controls.xml’
	</b></span></cite><cite><span style="font-style: normal"><span style="font-weight: normal">which
	is located i</span></span></cite><cite><span style="font-style: normal"><span style="font-weight: normal">n
	a folder ‘</span></span></cite><cite><span style="font-style: normal"><b>/</b></span></cite><cite><span style="font-style: normal"><b>MVCFramework</b></span></cite><cite><span style="font-style: normal"><span style="font-weight: normal">’
	in the root folder of your project. </span></span></cite><cite><span style="font-style: normal"><span style="font-weight: normal">For
	now, it is sufficient to understand </span></span></cite><cite><span style="font-style: normal"><span style="font-weight: normal">that
	the file should at a minimum contain </span></span></cite><cite><span style="font-style: normal"><span style="font-weight: normal">something
	like</span></span></cite><cite><span style="font-style: normal"><span style="font-weight: normal">:</span></span></cite></p></li>
</ol>
<p><br/>
<br/>

</p>
<p><cite><i><span style="font-weight: normal">&lt;?xml version=&quot;1.0&quot;
encoding=&quot;UTF-8&quot;?&gt;</span></i></cite></p>
<p><cite><i><span style="font-weight: normal">&lt;control&gt;</span></i></cite></p>
<p><cite>    <i><span style="font-weight: normal">&lt;</span></i></cite><cite><i><b>command</b></i></cite><cite><i><span style="font-weight: normal">
path=&quot;/&quot; class=&quot;\commands\DefaultCommand&quot;&gt;</span></i></cite></p>
<p><cite>        <i><span style="font-weight: normal">&lt;view
name=&quot;/defaultView&quot; /&gt;</span></i></cite></p>
<p><cite>        <i><span style="font-weight: normal">&lt;status
value=&quot;CMD_OK&quot;&gt;</span></i></cite></p>
<p><cite>            <i><span style="font-weight: normal">&lt;forward
path=&quot;/</span></i></cite><cite><i><span style="font-weight: normal">example</span></i></cite><cite><i><span style="font-weight: normal">&quot;
/&gt;</span></i></cite></p>
<p><cite>        <i><span style="font-weight: normal">&lt;/status&gt;</span></i></cite></p>
<p><cite>        <i><span style="font-weight: normal">&lt;status
value=&quot;CMD_ERROR&quot;&gt;</span></i></cite></p>
<p><cite>            <i><span style="font-weight: normal">&lt;view
name=&quot;/errorView&quot; /&gt;</span></i></cite></p>
<p><cite>        <i><span style="font-weight: normal">&lt;/status&gt;</span></i></cite></p>
<p><cite>    <i><span style="font-weight: normal">&lt;/</span></i></cite><cite><i><b>command</b></i></cite><cite><i><span style="font-weight: normal">&gt;</span></i></cite></p>
<p><cite>    <i><span style="font-weight: normal">&lt;</span></i></cite><cite><i><b>command</b></i></cite><cite><i><span style="font-weight: normal">
path=&quot;/</span></i></cite><cite><i><span style="font-weight: normal">example</span></i></cite><cite><i><span style="font-weight: normal">&quot;
class=&quot;</span></i></cite><cite><i><span style="font-weight: normal">\commands\ExampleCommand</span></i></cite><cite><i><span style="font-weight: normal">&quot;&gt;</span></i></cite></p>
<p><cite>        <i><span style="font-weight: normal">&lt;view
name=&quot;/</span></i></cite><cite><i><span style="font-weight: normal">example</span></i></cite><cite><i><span style="font-weight: normal">View&quot;
/&gt;</span></i></cite></p>
<p><cite>        <i><span style="font-weight: normal">&lt;status
value=&quot;CMD_OK&quot;&gt;</span></i></cite></p>
<p><cite>            <i><span style="font-weight: normal">&lt;forward
path=&quot;/</span></i></cite><cite><i><span style="font-weight: normal">ect.,
etc., etc.</span></i></cite><cite><i><span style="font-weight: normal">&quot;
/&gt;</span></i></cite></p>
<p><cite>        <i><span style="font-weight: normal">&lt;/status&gt;</span></i></cite></p>
<p><cite>        <i><span style="font-weight: normal">&lt;status
value=&quot;CMD_ERROR&quot;&gt;</span></i></cite></p>
<p><cite>            <i><span style="font-weight: normal">&lt;view
name=&quot;/errorView&quot; /&gt;</span></i></cite></p>
<p><cite>        <i><span style="font-weight: normal">&lt;/status&gt;</span></i></cite></p>
<p><cite>    <i><span style="font-weight: normal">&lt;/</span></i></cite><cite><i><b>command</b></i></cite><cite><i><span style="font-weight: normal">&gt;</span></i></cite></p>
<p><cite><i><span style="font-weight: normal">&lt;/control&gt;</span></i></cite></p>
<p><br/>
<br/>

</p>
<p><cite><span style="font-style: normal"><span style="font-weight: normal">This
short controls.xml example shows you how the control flow binds </span></span></cite><cite><span style="font-style: normal"><span style="font-weight: normal">a</span></span></cite><cite><span style="font-style: normal"><span style="font-weight: normal">
path “/” to 1 Command class “DefaultCommand” and 1 view
“defaultView”.  In the  example this defaultView is a login
screen. The DefaultCommand will </span></span></cite><cite><span style="font-style: normal"><span style="font-weight: normal">in
this case </span></span></cite><cite><span style="font-style: normal"><span style="font-weight: normal">be
checking the username/password combination and will return a status
depending on the result of the test. If the result is OK, the control
flow will forward the user to a new path (i.e. a new command and
view) named ‘example’. </span></span></cite><cite><span style="font-style: normal"><span style="font-weight: normal">If
not, the control flow will go to an error screen. This is basically
how the control flow works. </span></span></cite><cite><span style="font-style: normal"><span style="font-weight: normal">The
methodology and </span></span></cite><cite><span style="font-style: normal"><span style="font-weight: normal">full
</span></span></cite><cite><span style="font-style: normal"><span style="font-weight: normal">syntax
to define the control flow </span></span></cite><cite><span style="font-style: normal"><span style="font-weight: normal">will
be discussed in more detail in a later chapter. </span></span></cite>
</p>
<p><cite><span style="font-style: normal"><span style="font-weight: normal">Once
the application has been initialized, the Controller will in a second
stage “handle the Request”, i.e. :</span></span></cite>
</p>
<p>
	1. the	Command class that is linked to the path in the url request will be	executed to create a Response. Commands in your client project must inherit from a
	Command class in the Controllerframework and therefore need at least the following content:
</p>
<p><img src="/assets/ExampleCommand.gif" name="Image2" align="center" width="60%" height="60%" border="1"/>
</p><br/>
