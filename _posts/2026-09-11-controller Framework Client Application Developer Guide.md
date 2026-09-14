---
layout: post
author: dirkvm
---

---

layout: post
author: dirkvm
--------------

<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Controller Framework 1.0.31 — Client Application Developer Guide</title>
<style>
:root { --ink:#17202a; --muted:#5d6875; --line:#d9dee5; --panel:#f6f8fa; --accent:#2457a6; --warn:#fff7df; --danger:#fff0f0; --good:#edf8f0; }
* { box-sizing:border-box; }
body { margin:0; font-family:Arial,Helvetica,sans-serif; color:var(--ink); line-height:1.58; background:#fff; }
main { max-width:1120px; margin:auto; padding:36px 42px 80px; }
header { border-bottom:1px solid var(--line); padding-bottom:24px; margin-bottom:30px; }
h1 { font-size:2.3rem; margin:.1em 0 .25em; }
h2 { margin-top:2.2em; padding-bottom:.3em; border-bottom:1px solid var(--line); }
h3 { margin-top:1.6em; }
h4 { margin-top:1.2em; }
p, li { max-width:92ch; }
.lead { font-size:1.15rem; color:var(--muted); }
.badge { display:inline-block; background:#edf2fb; color:var(--accent); padding:4px 9px; border-radius:999px; font-weight:bold; font-size:.85rem; }
nav { background:var(--panel); border:1px solid var(--line); padding:18px 24px; border-radius:10px; }
nav ol { columns:2; }
code, pre { font-family:Consolas,Monaco,monospace; }
code { background:#f1f3f5; padding:1px 4px; border-radius:4px; }
pre {
    background:#111827;
    color:#f3f4f6;
    padding:17px;
    overflow:auto;
    border-radius:8px;
    line-height:1.45;
    white-space:pre;
}
pre code {
    background:transparent;
    color:#f3f4f6;
    padding:0;
    border-radius:0;
    font-family:Consolas,Monaco,monospace;
    white-space:inherit;
}
.note, .warning, .success { padding:15px 18px; border-left:5px solid; margin:18px 0; border-radius:5px; }
.note { background:#f3f7ff; border-color:var(--accent); }
.warning { background:var(--warn); border-color:#c48a00; }
.success { background:var(--good); border-color:#39834d; }
table { border-collapse:collapse; width:100%; margin:18px 0 26px; }
th, td { border:1px solid var(--line); padding:9px 11px; vertical-align:top; text-align:left; }
th { background:var(--panel); }
.diagram { border:1px solid var(--line); border-radius:10px; padding:15px; margin:20px 0; overflow:auto; background:#fff; }
.small { color:var(--muted); font-size:.92rem; }
footer { border-top:1px solid var(--line); margin-top:50px; padding-top:20px; color:var(--muted); }

@media print {
body { background:#fff; }
main { max-width:none; padding:0; }
nav { break-inside:avoid; }
pre {
color:#111;
background:#f3f3f3;
border:1px solid #ccc;
white-space:pre-wrap;
}
pre code { color:#111; background:transparent; }
h2, h3 { break-after:avoid; }
table, .diagram, .warning, .note, .success { break-inside:avoid; }
}

@media (max-width:760px) {
main { padding:22px 18px 50px; }
nav ol { columns:1; }
h1 { font-size:1.8rem; }
} </style>

</head>

<body>
<main>

<header>
<span class="badge">Controller Framework 1.0.31</span>
<h1>Client Application Developer Guide</h1>
<p class="lead">A practical guide to building a PHP 8.3 application on top of the Controller Framework.</p>
<p><strong>Audience:</strong> developers who build the application-specific commands, models, views, configuration and business logic on top of the framework.</p>
</header>

<nav>
<strong>Contents</strong>
<ol>
<li><a href="#overview">Framework overview</a></li>
<li><a href="#requirements">Requirements and installation</a></li>
<li><a href="#structure">Recommended application structure</a></li>
<li><a href="#bootstrap">Application bootstrap</a></li>
<li><a href="#configuration">Configuration</a></li>
<li><a href="#routing">Paths and controls.xml</a></li>
<li><a href="#commands">Commands</a></li>
<li><a href="#request">Request object</a></li>
<li><a href="#rendering">Rendering</a></li>
<li><a href="#datarequests">Data requests</a></li>
<li><a href="#sessions">Login and sessions</a></li>
<li><a href="#csrf">CSRF protection</a></li>
<li><a href="#accesstoken">Access tokens</a></li>
<li><a href="#database">Database and domain objects</a></li>
<li><a href="#members">Members</a></li>
<li><a href="#mail">Mail</a></li>
<li><a href="#audit">Audit tracing</a></li>
<li><a href="#errors">Error handling</a></li>
<li><a href="#security">Security rules</a></li>
<li><a href="#workflow">Recommended development workflow</a></li>
<li><a href="#reference">Quick reference</a></li>
</ol>
</nav>

<h2 id="overview">1. Framework overview</h2>

<p>The Controller Framework is an MVC-oriented PHP framework based on the Command, Registry, Strategy, Template Method and related design patterns. The framework supplies the application infrastructure; the client application supplies the domain model, application commands and views.</p>

<div class="diagram">
<svg viewBox="0 0 1000 390" width="100%" role="img" aria-label="Controller Framework architecture">
<defs>
<marker id="arrow" markerWidth="10" markerHeight="10" refX="9" refY="3" orient="auto">
<path d="M0,0 L10,3 L0,6 Z" fill="#2457a6"/>
</marker>
</defs>

<rect x="25" y="145" width="150" height="75" rx="10" fill="#f6f8fa" stroke="#8b98a7"/>
<text x="100" y="174" text-anchor="middle" font-size="18">Browser</text>
<text x="100" y="198" text-anchor="middle" font-size="14">HTTP request</text>

<rect x="220" y="115" width="180" height="135" rx="10" fill="#edf2fb" stroke="#2457a6"/>
<text x="310" y="146" text-anchor="middle" font-size="18">Controller</text>
<text x="310" y="172" text-anchor="middle" font-size="14">Init + routing</text>
<text x="310" y="195" text-anchor="middle" font-size="14">Command execution</text>
<text x="310" y="218" text-anchor="middle" font-size="14">Rendering</text>

<rect x="450" y="55" width="185" height="75" rx="10" fill="#f6f8fa" stroke="#8b98a7"/>
<text x="542" y="85" text-anchor="middle" font-size="18">Command</text>
<text x="542" y="108" text-anchor="middle" font-size="14">Business/application logic</text>

<rect x="450" y="155" width="185" height="75" rx="10" fill="#f6f8fa" stroke="#8b98a7"/>
<text x="542" y="185" text-anchor="middle" font-size="18">Domain model</text>
<text x="542" y="208" text-anchor="middle" font-size="14">Member / DomainObject</text>

<rect x="450" y="255" width="185" height="75" rx="10" fill="#f6f8fa" stroke="#8b98a7"/>
<text x="542" y="285" text-anchor="middle" font-size="18">View / Renderer</text>
<text x="542" y="308" text-anchor="middle" font-size="14">HTML / AJAX / download</text>

<rect x="685" y="155" width="140" height="75" rx="10" fill="#f6f8fa" stroke="#8b98a7"/>
<text x="755" y="185" text-anchor="middle" font-size="18">PDO</text>
<text x="755" y="208" text-anchor="middle" font-size="14">MySQL / MariaDB</text>

<rect x="855" y="155" width="120" height="75" rx="10" fill="#f6f8fa" stroke="#8b98a7"/>
<text x="915" y="185" text-anchor="middle" font-size="17">Database</text>
<text x="915" y="208" text-anchor="middle" font-size="13">application data</text>

<line x1="175" y1="182" x2="220" y2="182" stroke="#2457a6" stroke-width="3" marker-end="url(#arrow)"/>
<line x1="400" y1="182" x2="450" y2="95" stroke="#2457a6" stroke-width="3" marker-end="url(#arrow)"/>
<line x1="400" y1="182" x2="450" y2="192" stroke="#2457a6" stroke-width="3" marker-end="url(#arrow)"/>
<line x1="400" y1="182" x2="450" y2="292" stroke="#2457a6" stroke-width="3" marker-end="url(#arrow)"/>
<line x1="635" y1="192" x2="685" y2="192" stroke="#2457a6" stroke-width="3" marker-end="url(#arrow)"/>
<line x1="825" y1="192" x2="855" y2="192" stroke="#2457a6" stroke-width="3" marker-end="url(#arrow)"/>
</svg>
</div>

<p>The normal Release 31 setup uses the <strong>Application Controller</strong> variant. Requests are mapped through <code>controls.xml</code> to a Command. The Command executes and places response data in the Request. A RenderComponent then turns that result into HTML, a redirect, JSON, a download, an agenda response or another response type.</p>

<h2 id="requirements">2. Requirements and installation</h2>

<ul>
<li>PHP <strong>8.3 through versions below 9.0</strong>, as required by the Composer constraint <code>^8.3</code>.</li>
<li>Composer.</li>
<li>MySQL/MariaDB-compatible PDO database connectivity.</li>
<li>Apache or another web server capable of running the PHP application.</li>
<li>The Composer dependency <code>samoscon/controller-framework</code>.</li>
<li>Symfony Mailer is included as a framework dependency for mail functionality.</li>
</ul>

<h3>2.1 Install with Composer</h3>

<pre><code>{
  "require": {
    "samoscon/controller-framework": "^1.0"
  }
}</code></pre>

<p>Then run:</p>

<pre><code>composer install</code></pre>

<p>For a client application, the framework is normally installed below <code>vendor/</code>. The example application shipped with the framework can be used as a starting point.</p>

<h3>2.2 Initial database</h3>

<p>The example package contains <code>example/DatabaseSetup.sql</code>. It defines the standard <code>member</code> table and the tables required by the framework's remember-me and mail-queue facilities.</p>

<div class="warning">
<strong>Important:</strong> the SQL dump is a starting point. Review database credentials, permissions, indexes, foreign keys and application-specific fields before using it in production.
</div>

<h2 id="structure">3. Recommended application structure</h2>

<p>The framework expects application-specific code to live outside the framework source tree. A typical application based on the supplied example looks like this:</p>

<pre><code>project-root/
├── config/
│   └── app_options.ini
├── MVCFramework/
│   ├── controls.xml
│   ├── commands/
│   │   ├── DefaultCommand.php
│   │   └── admin/
│   ├── model/
│   │   ├── Member.php
│   │   ├── MemberMapper.php
│   │   └── Member_RGLR.php
│   └── views/
│       ├── defaultView.php
│       ├── errorView.php
│       ├── login/
│       └── admin/
├── assets/
├── vendor/
└── index.php</code></pre>

<p>The exact directory names are configurable through <code>app_options.ini</code>, but the example follows this arrangement.</p>

<h2 id="bootstrap">4. Application bootstrap</h2>

<p>The application entry point can be very small. The essential operation is to load Composer's autoloader and run the framework Controller.</p>

<pre><code>&lt;?php

include __DIR__ . '/vendor/autoload.php';

spl_autoload_register(function ($class_name) {
    if (preg_match('/\\\\/', $class_name)) {
        $class_name = str_replace('\\', DIRECTORY_SEPARATOR, $class_name);
    }

    $file = __DIR__ . DIRECTORY_SEPARATOR .
            'MVCFramework' . DIRECTORY_SEPARATOR .
            $class_name . '.php';

    if (file_exists($file)) {
        require_once $file;
    }
});

controllerframework\controllers\Controller::run();</code></pre>

<p>The supplied example additionally catches <code>Throwable</code> during application initialization and displays a controlled message. In production, do not expose detailed exception information to users.</p>

<h2 id="configuration">5. Configuration</h2>

<p>The main configuration file is <code>config/app_options.ini</code>. Release 31 requires both a <code>[config]</code> section and a <code>[globals]</code> section.</p>

<h3>5.1 Framework configuration</h3>

<pre><code>[config]
environment=development
templatepath=/MVCFramework/views
controlsfile=/MVCFramework/controls.xml
loggingpath=/assets/logging/</code></pre>

<table>
<tr><th>Setting</th><th>Purpose</th></tr>
<tr><td><code>environment</code></td><td>Controls development versus production exception presentation. Use <code>production</code> on a public production site.</td></tr>
<tr><td><code>templatepath</code></td><td>Base path for application PHP views.</td></tr>
<tr><td><code>controlsfile</code></td><td>Path to the XML command/render configuration.</td></tr>
<tr><td><code>loggingpath</code></td><td>Path used by <code>AuditTrace</code> for its logfile.</td></tr>
</table>

<h3>5.2 Global application values</h3>

<pre><code>[globals]
APP=BM
_LOGO=assets/logo.jpg
_APPDIR=https://example.org/
_HOMEPAGE=https://example.org/
_ASSETDIR=https://example.org/assets/
_RAND=12345
_MINLEVELTOLOGIN='A'
_CONTROLSFILE='/MVCFramework/controls.xml'

_DBUSER=...
_DBPASSWORD="..."
_DBNAME=...
_DBHOST=localhost

_MAILHOST="mail.example.org"
_MAILHOSTPORT=587
_MAILUSERNAME="website@example.org"
_MAILPASSWORD="..."
_MAILTO=website@example.org
_MAILTONAME=Example Organisation
_MAILFROM=info@example.org
_MAILFROMNAME=Example Organisation
_MAILREPLYTO=team@example.org</code></pre>

<p>Values in <code>[globals]</code> are defined as PHP constants by <code>InitController</code>, so application code can use constants such as <code>APP</code>, <code>_DBNAME</code> and <code>_MAILFROM</code>.</p>

<div class="warning">
<strong>Protect the INI file.</strong> It contains database and SMTP credentials. It must not be downloadable through the web server.
</div>

<h3>5.3 Production settings</h3>

<p>Set <code>environment=production</code> in production. The development example may enable <code>display_errors</code>; this must be disabled or removed in production.</p>

<h2 id="routing">6. Paths and controls.xml</h2>

<p>In the Application Controller configuration, <code>controls.xml</code> is the central routing and rendering description. Each <code>&lt;command&gt;</code> connects a request path with a Command class and one or more renderers.</p>

<pre><code>&lt;command path="/admin"
         class="\commands\admin\AdminHomeCommand"&gt;

    &lt;view name="/admin/adminhome" /&gt;

    &lt;status value="CMD_OK"&gt;
        &lt;forward path="/thenameofanotherpath" /&gt;
    &lt;/status&gt;

    &lt;status value="CMD_ERROR"&gt;
        &lt;view name="/errorView" /&gt;
    &lt;/status&gt;

&lt;/command&gt;</code></pre>

<h3>6.1 Request lifecycle</h3>

<div class="diagram">
<svg viewBox="0 0 1000 260" width="100%" role="img" aria-label="Request lifecycle">
<defs>
<marker id="arr2" markerWidth="10" markerHeight="10" refX="9" refY="3" orient="auto">
<path d="M0,0 L10,3 L0,6 Z" fill="#2457a6"/>
</marker>
</defs>

<g font-family="Arial">

<rect x="20" y="85" width="150" height="70" rx="9" fill="#f6f8fa" stroke="#8b98a7"/>
<text x="95" y="112" text-anchor="middle">HTTP Request</text>
<text x="95" y="135" text-anchor="middle" font-size="13">path + input</text>

<rect x="205" y="85" width="150" height="70" rx="9" fill="#edf2fb" stroke="#2457a6"/>
<text x="280" y="112" text-anchor="middle">Request</text>
<text x="280" y="135" text-anchor="middle" font-size="13">HttpRequest</text>

<rect x="390" y="85" width="150" height="70" rx="9" fill="#edf2fb" stroke="#2457a6"/>
<text x="465" y="112" text-anchor="middle">Command</text>
<text x="465" y="135" text-anchor="middle" font-size="13">execute()</text>

<rect x="575" y="85" width="150" height="70" rx="9" fill="#f6f8fa" stroke="#8b98a7"/>
<text x="650" y="112" text-anchor="middle">Request state</text>
<text x="650" y="135" text-anchor="middle" font-size="13">responses + status</text>

<rect x="760" y="85" width="215" height="70" rx="9" fill="#f6f8fa" stroke="#8b98a7"/>
<text x="867" y="112" text-anchor="middle">RenderComponent</text>
<text x="867" y="135" text-anchor="middle" font-size="13">view / forward / data</text>

<line x1="170" y1="120" x2="205" y2="120" stroke="#2457a6" stroke-width="3" marker-end="url(#arr2)"/>
<line x1="355" y1="120" x2="390" y2="120" stroke="#2457a6" stroke-width="3" marker-end="url(#arr2)"/>
<line x1="540" y1="120" x2="575" y2="120" stroke="#2457a6" stroke-width="3" marker-end="url(#arr2)"/>
<line x1="725" y1="120" x2="760" y2="120" stroke="#2457a6" stroke-width="3" marker-end="url(#arr2)"/>

<text x="500" y="205" text-anchor="middle" font-size="15">
Command status selects the renderer; the renderer produces the final HTTP response.
</text>

</g>
</svg>
</div>

<p>The framework validates XML status names against the constants defined by <code>Command</code>. An unknown status causes initialization to fail.</p>

<h2 id="commands">7. Commands</h2>

<p>A client Command is the main unit of application logic. It extends <code>controllerframework\controllers\Command</code>. The framework calls <code>execute()</code>; client code implements <code>doExecute()</code> and <code>getLevelOfLoginRequired()</code>.</p>

<pre><code>namespace commands\admin;

class AdminHomeCommand
    extends \controllerframework\controllers\Command
{
    public function doExecute(
        \controllerframework\registry\Request $request
    ): int {
        $request->set('title', 'Administration');
        return self::CMD_OK;
    }

    protected function getLevelOfLoginRequired(): void {
        $this->setLoginLevel(
            new \controllerframework\sessions\AdminLogin()
        );
    }
}</code></pre>

<h3>7.1 Command status constants</h3>

<table>
<tr><th>Constant</th><th>Meaning</th></tr>
<tr><td><code>CMD_DEFAULT</code></td><td>Default renderer selection.</td></tr>
<tr><td><code>CMD_OK</code></td><td>Normal successful result.</td></tr>
<tr><td><code>CMD_ERROR</code></td><td>Application error result.</td></tr>
<tr><td><code>CMD_INSUFFICIENT_DATA</code></td><td>Insufficient input/data.</td></tr>
<tr><td><code>CMD_ADMIN</code></td><td>Administrator-specific result/forward.</td></tr>
<tr><td><code>CMD_CHANGE_PASSWORD</code></td><td>Password must be changed.</td></tr>
<tr><td><code>CMD_CONTINUE</code></td><td>Continue processing according to application configuration.</td></tr>
</table>

<h3>7.2 Putting response data in the Request</h3>

<pre><code>$this->addResponses($request, [
    'title' => 'Member administration',
    'message' => 'Welcome'
]);

return self::CMD_OK;</code></pre>

<p>The view can subsequently read these values through <code>$request-&gt;get('title')</code> and <code>$request-&gt;get('message')</code>.</p>

<h3>7.3 Command decorators</h3>

<p><code>CommandDecorator</code> lets an application command wrap another Command. The decorator can add application-specific behavior before/after delegating to another command. The supplied example uses this pattern to adapt framework functionality supplied by another application framework.</p>

<h2 id="request">8. Request object</h2>

<p>The framework abstracts HTTP and CLI requests behind <code>controllerframework\registry\Request</code>. For web requests, <code>HttpRequest</code> is used; for CLI execution, <code>CliRequest</code> is selected.</p>

<table>
<tr><th>Method</th><th>Purpose</th></tr>
<tr><td><code>getPath()</code></td><td>Returns the current application path.</td></tr>
<tr><td><code>setPath($path)</code></td><td>Sets the request path.</td></tr>
<tr><td><code>get($key)</code></td><td>Reads a value from request state.</td></tr>
<tr><td><code>set($key, $value)</code></td><td>Stores a value in request state.</td></tr>
<tr><td><code>addFeedback($msg)</code></td><td>Adds a feedback message.</td></tr>
<tr><td><code>getFeedback()</code></td><td>Returns feedback messages.</td></tr>
<tr><td><code>getFeedbackString()</code></td><td>Returns feedback as a string.</td></tr>
<tr><td><code>setCmdStatus($status)</code></td><td>Stores the command result status.</td></tr>
<tr><td><code>getCmdStatus()</code></td><td>Returns the command result status.</td></tr>
<tr><td><code>forward($path)</code></td><td>Performs the framework's HTTP/CLI forward operation.</td></tr>
</table>

<div class="note">
<strong>Important distinction:</strong> request state is not automatically a trust boundary. Values that originate from GET, POST, cookies or other client-controlled input must be validated before being used for SQL, headers, redirects, file operations or security decisions. A value being stored in the framework's <code>Request</code> object does not make it trusted.
</div>

<h2 id="rendering">9. Rendering</h2>

<p>The framework separates command execution from response rendering through the <code>RenderComponent</code> interface.</p>

<h3>9.1 ViewRenderComponent</h3>

<p>A view renderer includes a PHP view file. The configured <code>templatepath</code> is combined with the view name from <code>controls.xml</code>.</p>

<pre><code>&lt;view name="/admin/adminhome" /&gt;</code></pre>

<p>With the example configuration, this corresponds to:</p>

<pre><code>MVCFramework/views/admin/adminhome.php</code></pre>

<h3>9.2 ForwardRenderComponent</h3>

<p>For a redirect/forward:</p>

<pre><code>&lt;status value="CMD_OK"&gt;
    &lt;forward path="/activity" /&gt;
&lt;/status&gt;</code></pre>

<p>If query parameters are required, put a named array into the request as <code>forwardqueryparams</code>. Release 31 builds the query string with <code>http_build_query()</code>.</p>

<pre><code>$request->set('forwardqueryparams', [
    'id' => $member->getId(),
    'mode' => 'edit'
]);</code></pre>

<h2 id="datarequests">10. Data-request renderers</h2>

<p>The framework includes generic renderers for non-HTML responses.</p>

<table>
<tr><th>Renderer</th><th>Request values</th><th>Output</th></tr>
<tr><td><code>AjaxRenderComponent</code></td><td><code>results</code> as an array</td><td>JSON</td></tr>
<tr><td><code>DownloadRenderComponent</code></td><td><code>filename</code>, <code>columnNames</code>, <code>results</code></td><td>CSV download</td></tr>
<tr><td><code>AgendaRenderComponent</code></td><td><code>filename</code>, <code>results</code></td><td>iCalendar response</td></tr>
<tr><td><code>MollieRenderComponent</code></td><td><code>results</code></td><td>HTTP Location redirect</td></tr>
</table>

<h3>10.1 AJAX example</h3>

<pre><code>&lt;command path="/api/memberlist"
         class="\commands\MemberListCommand"&gt;
    &lt;datarequest type="Ajax" /&gt;
&lt;/command&gt;</code></pre>

<p>The command should put an array into <code>results</code> and return the configured status.</p>

<h3>10.2 CSV download example</h3>

<pre><code>$request->set('filename', 'members.csv');
$request->set('columnNames', 'id;name;email');
$request->set('results', [
    [1, 'Alice', 'alice@example.org'],
    [2, 'Bob', 'bob@example.org']
]);

return self::CMD_OK;</code></pre>

<div class="warning">
<strong>Header safety:</strong> values used for <code>Location</code> and <code>Content-Disposition</code> must be trusted and validated by the application. Never pass raw user-controlled values directly into these headers. Download filenames must not contain CR, LF or other HTTP header control characters.
</div>

<h2 id="sessions">11. Login and sessions</h2>

<p>Release 31 centralizes login/session management in <code>LoginManager</code>. The LoginManager is lazily obtained from the Registry and is responsible for starting the PHP session when needed.</p>

<h3>11.1 Login levels</h3>

<table>
<tr><th>Class</th><th>Behavior</th></tr>
<tr><td><code>NoLoginRequired</code></td><td>Always validates successfully. Use for public commands.</td></tr>
<tr><td><code>UserLogin</code></td><td>Requires an active member and allows a normal user session.</td></tr>
<tr><td><code>AdminLogin</code></td><td>Requires an active member with role <code>A</code>.</td></tr>
</table>

<pre><code>protected function getLevelOfLoginRequired(): void {
    $this->setLoginLevel(
        new \controllerframework\sessions\UserLogin()
    );
}</code></pre>

<h3>11.2 Normal login</h3>

<p>The normal application login flow typically validates the username and password through <code>LoginManager</code>, then calls:</p>

<pre><code>$user = $loginManager->login($memberid, true);</code></pre>

<p>The second argument controls whether remember-me is enabled.</p>

<h3>11.3 Remember-me</h3>

<p>Release 31 implements persistent login with a selector/validator token stored in the database. Only a hash of the validator is stored server-side. The remember-me lifetime is 30 days and tokens are rotated after successful reuse.</p>

<p>When remember-me authentication is active, <code>$_SESSION['rememberMe']</code> is set to <code>true</code>. The session does not expire because of the normal inactivity timer; the persistent authentication is bounded by the remember-token mechanism.</p>

<h3>11.4 Inactivity timeout</h3>

<ul>
<li>Normal user sessions: <strong>60 minutes</strong> of inactivity.</li>
<li>Administrator sessions: <strong>15 minutes</strong> of inactivity.</li>
<li>Remember-me sessions: no inactivity timeout; the remember-token mechanism provides the persistent login period.</li>
</ul>

<p>After successful authentication the framework regenerates the PHP session ID. Logout clears the session, removes the session cookie and removes remember-me tokens/cookie.</p>

<h3>11.5 Passwords</h3>

<p>Release 31 uses PHP's <code>password_hash()</code> and <code>password_verify()</code>. Legacy SHA-256 password hashes can be accepted during migration and are automatically replaced by a modern password hash after a successful legacy login.</p>

<h2 id="csrf">12. CSRF protection</h2>

<p>Use the framework's <code>Csrf</code> helper for state-changing forms. The token is generated with <code>random_bytes(32)</code>, stored in the PHP session and compared with <code>hash_equals()</code>.</p>

<h3>12.1 In a Command</h3>

<p><code>Command</code> provides protected helpers:</p>

<pre><code>$token = $this->getCsrfToken();

if (!$this->validateCsrfToken($request)) {
    $request->set('errorcode', 'InvalidCsrfToken');
    return self::CMD_ERROR;
}</code></pre>

<h3>12.2 In the HTML form</h3>

<pre><code>&lt;form method="post"&gt;
    &lt;input type="hidden"
           name="csrf_token"
           value="&lt;?= htmlspecialchars(
               $this-&gt;getCsrfToken(),
               ENT_QUOTES,
               'UTF-8'
           ) ?&gt;"&gt;

    &lt;button type="submit"&gt;Save&lt;/button&gt;
&lt;/form&gt;</code></pre>

<p>In a normal client view, obtain the token through the application command/view design you use. The framework's token parameter name is <code>csrf_token</code>.</p>

<div class="warning">
<strong>Protect state changes.</strong> Use CSRF validation for POST operations that change passwords, records, settings, payments or other security-sensitive application state. Do not rely on the session cookie alone.
</div>

<h2 id="accesstoken">13. Access tokens</h2>

<p>Controller Framework 1.0.31 introduces the <code>AccessToken</code> class in the <code>controllerframework\security</code> namespace.</p>

<p><code>AccessToken</code> provides an additional token-based security mechanism for protecting specific URLs or application operations. It is particularly useful when an application needs to protect an internal URL against unauthorized direct access, in addition to the normal authentication and authorization mechanisms.</p>

<h3>13.1 Generating an access token</h3>

<p>An access token is generated from a purpose and an identifier:</p>

<pre><code>use controllerframework\security\AccessToken;

$accessToken = AccessToken::generate(
    'mollie-order',
    (string) $orderid
);</code></pre>

<p>The <strong>purpose</strong> identifies what the token is intended for. The <strong>identifier</strong> binds the token to a specific application object or operation.</p>

<p>The token generation mechanism uses a server-side secret salt and an HMAC-SHA-256 calculation. The resulting token is deterministic for the same purpose and identifier.</p>

<h3>13.2 Validating an access token</h3>

<p>The receiving Command must validate the token before performing the protected operation:</p>

<pre><code>use controllerframework\security\AccessToken;

if (!AccessToken::validate(
    'mollie-order',
    (string) $orderid,
    $accessToken
)) {
    throw new \RuntimeException(
        'Invalid access token.'
    );
}</code></pre>

<p>The validation operation uses a timing-safe comparison through <code>hash_equals()</code>.</p>

<h3>13.3 Example: protecting an internal payment URL</h3>

<p>A typical application flow can generate an access token when creating an order:</p>

<pre><code>$accessToken = AccessToken::generate(
    'mollie-order',
    (string) $orderid
);

$request->set('forwardqueryparams', [
    'id' => $orderid,
    'amount' => $amount,
    'access_token' => $accessToken
]);

return self::CMD_OK;</code></pre>

<p>The receiving Command validates the token before accessing the order or creating the Mollie payment:</p>

<pre><code>$orderid = $request->get('id');
$accessToken = $request->get('access_token');

if (!AccessToken::validate(
    'mollie-order',
    (string) $orderid,
    $accessToken
)) {
    throw new \RuntimeException(
        'Invalid access token.'
    );
}

// Only after validation:
// - load the order
// - verify the order data
// - create the payment
// - continue with the application flow</code></pre>

<div class="warning">
<strong>Validate before processing.</strong> An access token must be validated before the protected database operation or other sensitive operation is performed. Do not load or modify protected application data first and validate the token afterwards.
</div>

<h3>13.4 AccessToken is not authentication</h3>

<p>An <code>AccessToken</code> is an additional security mechanism. It does not replace normal user authentication, authorization, CSRF protection or HTTPS.</p>

<p>The purpose of an access token is to demonstrate that the caller possesses a token that corresponds to the expected purpose and identifier. The application must still enforce all other security requirements that apply to the operation.</p>

<h3>13.5 Token secret</h3>

<p>The security of <code>AccessToken</code> depends on the confidentiality of the server-side salt used by the framework. The salt must therefore never be exposed to clients or committed to publicly accessible configuration or source code.</p>

<h3>13.6 No server-side token storage</h3>

<p><code>AccessToken</code> does not require a database table or other server-side token storage. The token is calculated from the purpose, identifier and server-side secret.</p>

<p>Because the token is deterministic, generating the same token again for the same purpose and identifier produces the same value. The <code>AccessToken</code> class itself does not provide token expiration, one-time use or token revocation.</p>

<div class="note">
<strong>Important:</strong> if an application requires expiry, one-time use or revocation, these requirements must be implemented separately by the application. An access token should therefore not be interpreted as a short-lived session token or as a replacement for an authenticated session.
</div>

<h2 id="database">14. Database and domain objects</h2>

<p>The framework uses PDO and provides a DomainObject/Mapper abstraction. The application's model classes normally extend framework domain classes, while application-specific Mapper classes provide the table and object mapping.</p>

<h3>14.1 DomainObject basics</h3>

<pre><code>class Activity extends \controllerframework\db\DomainObject
{
    public static function getInstance(array $row): DomainObject
    {
        $activity = new self($row['id']);
        $activity->initProperties($row);
        return $activity;
    }
}</code></pre>

<p>Domain objects expose:</p>

<pre><code>Activity::find($id);
Activity::findAll();
Activity::insert([...]);

$activity->update([...]);
$activity->delete();

$activity->getId();</code></pre>

<h3>14.2 Mapper</h3>

<p>A specialized Mapper supplies the database table name and object construction logic.</p>

<pre><code>final class ActivityMapper
    extends \controllerframework\db\Mapper
{
    protected function tablename(): string {
        return 'activity';
    }

    protected function doCreateObject(
        string $classname,
        array $row
    ): \controllerframework\db\DomainObject {
        return $classname::getInstance($row);
    }
}</code></pre>

<h3>14.3 Allowed fields</h3>

<p>Release 31 validates update fields against an allow-list. A specialized Mapper should extend the allowed fields supplied by its parent.</p>

<pre><code>protected function getAllowedFields(): array
{
    return array_merge(
        parent::getAllowedFields(),
        [
            'name',
            'email',
            'active'
        ]
    );
}</code></pre>

<h3>14.4 findAll() and free SQL</h3>

<p><code>findAll(string $selectclause = '')</code> intentionally permits an application-defined
