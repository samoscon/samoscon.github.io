---
layout: post
author: dirkvm
---
<p class="h1">High level overview</p>

<p>The memberactivities framework requires a specific set-up in your client project. An example of this set-up can be found under the ‘vendor/samoscon/membersactivities-framework/example’ directory.</p>
<p>The below illustration provides an overview how the different files in the example collaborate with the framework.</p>
<p><img src="/assets/1 high level overview_html_42ddbf75.gif" name="Image1" align="center" width="95%" height="95%" border="1"/></p><br/>

<p class="h2 text-primary mt-2">The controller framework</p>

<p class="h3 text-primary mt-3">The Controller - the basics</p>

<p>The membersactivities is based on an <a href="https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller">MVC (Model - View - Controller) framework</a>. Each time a client browser sends a request to the webhost, the request will be forwarded to a Controller that will handle the request.</p>

<p>Therefor your client project needs an <b>index.php</b> file in the root folder of the client project with the following content:</p>
<p><img src="/assets/IndexFile.gif" name="Image3" align="center" width="60%" height="60%" border="1"/></p><br/>

<p>Each url request to your domain should be directed to the index.php file followed by a path name. This means that a url should be constructed as [domainname].index.php/[pathname]?[parameters]. You can avoid the use of the index.php in each url by parameterizing your webhost server access file. In an Apache 2 server, this would mean you can add an <b>.htaccess</b> file in the root folder of your project with the following content included:</p>
<p><img src="/assets/htaccess.gif" name="Image" align="center" width="60%" height="60%" border="1"/></p><br/>

<p>With this redirect, your url request can have the structure [domainname]/[pathname]?[parameters] (e.g. “app.myticketing.com/admin” or “app.myticketing.com/editMember?id=234585”).</p>

<p class="h3 text-primary mt-3">The Controller::Run</p>

<p>When the index.php initiates the Controller to “run”, The Controller will execute 2 distinct stages. In a first stage, the Controller will “initiate the application”. This initialisation is executed in 2 different steps, i.e. </p>

<p>1. the set-up of global variables and the passwords for your database, mailserver, paymentsystem (in the example based on mollie), Google Wallet Tickets API, etc., etc.  . This set-up is done through a file <i>app_options.ini</i> which is located in a folder <b>/config</b> in the root folder of your project. The content of this file will be	discussed in more detail in a later chapter. The app_options.ini file should contain at least the following lines:</p>
<p><img src="/assets/app_options_basic.jpg" name="Image4" align="center" width="60%" height="60%" border="1"/></p><br/>

<p>2. the definition of the	control flow of the application. Default this flow will be defined through a XML file <b>controls.xml</b> which	is located in a folder <b>MVCFramework</b> in the root folder of your project. (Note that the name of the file and folder have been set in the config section of app_options.ini). For now, it is sufficient to understand that the controls file should at a minimum contain something like:</p>
<p><img src="/assets/controls_file_basic.jpg" name="Image5" align="center" width="60%" height="60%" border="1"/></p><br/>

<p>This short controls.xml example shows you how the control flow binds a path “/” to 1 Command class “DefaultCommand” and 1 view “defaultView”. The methodology and full syntax to define the control flow will be discussed in more detail in a later chapter.</p>

<p>Once the application has been initialized, the Controller will in a second stage “handle the Request”, i.e. :</p>
<p>	1. the	Command class that is linked to the path in the url request will be	executed to create a Response. Commands in your client project must inherit from a Command class in the Controllerframework and therefore need at least the following content:</p>
<p><img src="/assets/ExampleCommand.gif" name="Image2" align="center" width="60%" height="60%" border="1"/>
</p><br/>
