---
layout: post
author: dirkvm
---
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Controller Framework 1.0.30 — Client Application Developer Guide</title>
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

@media (max-width:760px) { main { padding:22px 18px 50px; } nav ol { columns:1; } h1 { font-size:1.8rem; } }
</style>
</head>
<body>
<main>
<header>
<span class="badge">Controller Framework 1.0.30</span>
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
<defs><marker id="arrow" markerWidth="10" markerHeight="10" refX="9" refY="3" orient="auto"><path d="M0,0 L10,3 L0,6 Z" fill="#2457a6"/></marker></defs>
<rect x="25" y="145" width="150" height="75" rx="10" fill="#f6f8fa" stroke="#8b98a7"/>
<text x="100" y="174" text-anchor="middle" font-size="18">Browser</text><text x="100" y="198" text-anchor="middle" font-size="14">HTTP request</text>
<rect x="220" y="115" width="180" height="135" rx="10" fill="#edf2fb" stroke="#2457a6"/>
<text x="310" y="146" text-anchor="middle" font-size="18">Controller</text><text x="310" y="172" text-anchor="middle" font-size="14">Init + routing</text><text x="310" y="195" text-anchor="middle" font-size="14">Command execution</text><text x="310" y="218" text-anchor="middle" font-size="14">Rendering</text>
<rect x="450" y="55" width="185" height="75" rx="10" fill="#f6f8fa" stroke="#8b98a7"/>
<text x="542" y="85" text-anchor="middle" font-size="18">Command</text><text x="542" y="108" text-anchor="middle" font-size="14">Business/application logic</text>
<rect x="450" y="155" width="185" height="75" rx="10" fill="#f6f8fa" stroke="#8b98a7"/>
<text x="542" y="185" text-anchor="middle" font-size="18">Domain model</text><text x="542" y="208" text-anchor="middle" font-size="14">Member / DomainObject</text>
<rect x="450" y="255" width="185" height="75" rx="10" fill="#f6f8fa" stroke="#8b98a7"/>
<text x="542" y="285" text-anchor="middle" font-size="18">View / Renderer</text><text x="542" y="308" text-anchor="middle" font-size="14">HTML / AJAX / download</text>
<rect x="685" y="155" width="140" height="75" rx="10" fill="#f6f8fa" stroke="#8b98a7"/>
<text x="755" y="185" text-anchor="middle" font-size="18">PDO</text><text x="755" y="208" text-anchor="middle" font-size="14">MySQL / MariaDB</text>
<rect x="855" y="155" width="120" height="75" rx="10" fill="#f6f8fa" stroke="#8b98a7"/>
<text x="915" y="185" text-anchor="middle" font-size="17">Database</text><text x="915" y="208" text-anchor="middle" font-size="13">application data</text>
<line x1="175" y1="182" x2="220" y2="182" stroke="#2457a6" stroke-width="3" marker-end="url(#arrow)"/>
<line x1="400" y1="182" x2="450" y2="95" stroke="#2457a6" stroke-width="3" marker-end="url(#arrow)"/>
<line x1="400" y1="182" x2="450" y2="192" stroke="#2457a6" stroke-width="3" marker-end="url(#arrow)"/>
<line x1="400" y1="182" x2="450" y2="292" stroke="#2457a6" stroke-width="3" marker-end="url(#arrow)"/>
<line x1="635" y1="192" x2="685" y2="192" stroke="#2457a6" stroke-width="3" marker-end="url(#arrow)"/>
<line x1="825" y1="192" x2="855" y2="192" stroke="#2457a6" stroke-width="3" marker-end="url(#arrow)"/>
</svg>
</div>

<p>The normal Release 30 setup uses the <strong>Application Controller</strong> variant. Requests are mapped through <code>controls.xml</code> to a Command. The Command executes and places response data in the Request. A RenderComponent then turns that result into HTML, a redirect, JSON, a download, an agenda response or another response type.</p>

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
<div class="warning"><strong>Important:</strong> the SQL dump is a starting point. Review database credentials, permissions, indexes, foreign keys and application-specific fields before using it in production.</div>

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
<p>The main configuration file is <code>config/app_options.ini</code>. Release 30 requires both a <code>[config]</code> section and a <code>[globals]</code> section.</p>

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

<div class="warning"><strong>Protect the INI file.</strong> It contains database and SMTP credentials. It must not be downloadable through the web server.</div>

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
<defs><marker id="arr2" markerWidth="10" markerHeight="10" refX="9" refY="3" orient="auto"><path d="M0,0 L10,3 L0,6 Z" fill="#2457a6"/></marker></defs>
<g font-family="Arial">
<rect x="20" y="85" width="150" height="70" rx="9" fill="#f6f8fa" stroke="#8b98a7"/><text x="95" y="112" text-anchor="middle">HTTP Request</text><text x="95" y="135" text-anchor="middle" font-size="13">path + input</text>
<rect x="205" y="85" width="150" height="70" rx="9" fill="#edf2fb" stroke="#2457a6"/><text x="280" y="112" text-anchor="middle">Request</text><text x="280" y="135" text-anchor="middle" font-size="13">HttpRequest</text>
<rect x="390" y="85" width="150" height="70" rx="9" fill="#edf2fb" stroke="#2457a6"/><text x="465" y="112" text-anchor="middle">Command</text><text x="465" y="135" text-anchor="middle" font-size="13">execute()</text>
<rect x="575" y="85" width="150" height="70" rx="9" fill="#f6f8fa" stroke="#8b98a7"/><text x="650" y="112" text-anchor="middle">Request state</text><text x="650" y="135" text-anchor="middle" font-size="13">responses + status</text>
<rect x="760" y="85" width="215" height="70" rx="9" fill="#f6f8fa" stroke="#8b98a7"/><text x="867" y="112" text-anchor="middle">RenderComponent</text><text x="867" y="135" text-anchor="middle" font-size="13">view / forward / data</text>
<line x1="170" y1="120" x2="205" y2="120" stroke="#2457a6" stroke-width="3" marker-end="url(#arr2)"/>
<line x1="355" y1="120" x2="390" y2="120" stroke="#2457a6" stroke-width="3" marker-end="url(#arr2)"/>
<line x1="540" y1="120" x2="575" y2="120" stroke="#2457a6" stroke-width="3" marker-end="url(#arr2)"/>
<line x1="725" y1="120" x2="760" y2="120" stroke="#2457a6" stroke-width="3" marker-end="url(#arr2)"/>
<text x="500" y="205" text-anchor="middle" font-size="15">Command status selects the renderer; the renderer produces the final HTTP response.</text>
</g></svg>
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

<div class="note"><strong>Important distinction:</strong> request state is not automatically a trust boundary. Values that originate from GET, POST, cookies or other client-controlled input must be validated before being used for SQL, headers, redirects, file operations or security decisions. A value being stored in the framework's <code>Request</code> object does not make it trusted.</div>

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
<p>If query parameters are required, put a named array into the request as <code>forwardqueryparams</code>. Release 30 builds the query string with <code>http_build_query()</code>.</p>
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

<div class="warning"><strong>Header safety:</strong> values used for <code>Location</code> and <code>Content-Disposition</code> must be trusted and validated by the application. Never pass raw user-controlled values directly into these headers. Download filenames must not contain CR, LF or other HTTP header control characters.</div>

<h2 id="sessions">11. Login and sessions</h2>
<p>Release 30 centralizes login/session management in <code>LoginManager</code>. The LoginManager is lazily obtained from the Registry and is responsible for starting the PHP session when needed.</p>

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
<p>Release 30 implements persistent login with a selector/validator token stored in the database. Only a hash of the validator is stored server-side. The remember-me lifetime is 30 days and tokens are rotated after successful reuse.</p>
<p>When remember-me authentication is active, <code>$_SESSION['rememberMe']</code> is set to <code>true</code>. The session does not expire because of the normal inactivity timer; the persistent authentication is bounded by the remember-token mechanism.</p>

<h3>11.4 Inactivity timeout</h3>
<ul>
<li>Normal user sessions: <strong>60 minutes</strong> of inactivity.</li>
<li>Administrator sessions: <strong>15 minutes</strong> of inactivity.</li>
<li>Remember-me sessions: no inactivity timeout; the remember-token mechanism provides the persistent login period.</li>
</ul>

<p>After successful authentication the framework regenerates the PHP session ID. Logout clears the session, removes the session cookie and removes remember-me tokens/cookie.</p>

<h3>11.5 Passwords</h3>
<p>Release 30 uses PHP's <code>password_hash()</code> and <code>password_verify()</code>. Legacy SHA-256 password hashes can be accepted during migration and are automatically replaced by a modern password hash after a successful legacy login.</p>

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

<div class="warning"><strong>Protect state changes.</strong> Use CSRF validation for POST operations that change passwords, records, settings, payments or other security-sensitive application state. Do not rely on the session cookie alone.</div>

<h2 id="database">13. Database and domain objects</h2>
<p>The framework uses PDO and provides a DomainObject/Mapper abstraction. The application's model classes normally extend framework domain classes, while application-specific Mapper classes provide the table and object mapping.</p>

<h3>13.1 DomainObject basics</h3>
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

<h3>13.2 Mapper</h3>
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

<h3>13.3 Allowed fields</h3>
<p>Release 30 validates update fields against an allow-list. A specialized Mapper should extend the allowed fields supplied by its parent.</p>
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

<h3>13.4 findAll() and free SQL</h3>
<p><code>findAll(string $selectclause = '')</code> intentionally permits an application-defined SQL fragment. This is a flexible API, but it is not a parameterized value.</p>
<pre><code>$members = Member::findAll(
    "WHERE active = 1 ORDER BY name"
);</code></pre>
<div class="danger warning"><strong>Security rule:</strong> never build this clause directly from <code>$_GET</code>, <code>$_POST</code>, cookies or other untrusted input. <code>PDO::prepare()</code> cannot make a SQL fragment safe when the fragment itself is concatenated into the SQL statement. Validate/whitelist application-controlled choices before constructing the clause.</div>

<h2 id="members">14. Member model</h2>
<p>The framework provides a generic <code>controllerframework\members\Member</code> abstraction and <code>MemberMapper</code>. A client application normally subclasses these classes.</p>

<pre><code>namespace model;

class Member extends \controllerframework\members\Member
{
    public function initiatePassword(string $pwd = ''): string
    {
        return '...';
    }
}</code></pre>

<pre><code>namespace model;

final class MemberMapper
    extends \controllerframework\members\MemberMapper
{
}</code></pre>

<h3>14.1 Member type implementations</h3>
<p>Membership-specific behavior can be implemented through subclasses such as <code>Member_RGLR</code>. The classification stored in the member row determines the concrete type implementation.</p>
<pre><code>class Member_RGLR
    extends \controllerframework\members\MemberTypeImplementation
{
    public function getYearlyParticipationFee(
        \model\Member $member
    ): int {
        return 350;
    }
}</code></pre>

<h2 id="mail">15. Mail</h2>
<p>The framework uses Symfony Mailer. SMTP settings are supplied through the global configuration constants.</p>

<h3>15.1 Immediate mail</h3>
<pre><code>\controllerframework\mail\Mailer::sendMail(
    'Subject',
    '&lt;p&gt;Hello&lt;/p&gt;',
    'one@example.org, two@example.org',
    'recipient@example.org'
);</code></pre>
<p>The framework constructs an HTML message, adds the configured sender/reply-to information and sends through the configured SMTP transport.</p>

<h3>15.2 Mail queue</h3>
<p><code>MailQueue::add()</code> stores a mail in the <code>mail_queue</code> table. <code>MailerQueue</code> can then be used by an application-side scheduled process to send queued messages.</p>
<pre><code>$id = \controllerframework\mail\MailQueue::add(
    'Newsletter',
    '&lt;p&gt;Hello members&lt;/p&gt;',
    'website@example.org',
    'alice@example.org, bob@example.org'
);</code></pre>
<p>The supplied SQL schema includes queue status, attempts and timestamps so the client application can implement a scheduled queue processor. <code>MailQueue::add()</code> only stores the message; <code>MailerQueue::sendMail()</code> is the low-level helper used by such a processor to send one queued message.</p>

<div class="warning"><strong>SMTP credentials:</strong> keep mail credentials in protected configuration and use the encryption/port required by the SMTP provider. Do not commit real passwords to source control.</div>

<h2 id="audit">16. Audit tracing</h2>
<p>The framework provides <code>AuditableItem</code> and <code>AuditableItemTrait</code>. A class can implement the interface and use the trait, then call <code>notifyAuditTrace()</code>.</p>

<pre><code>class MyService implements
    \controllerframework\audit\AuditableItem
{
    use \controllerframework\audit\AuditableItemTrait;

    public function updateSomething(): void
    {
        $this->notifyAuditTrace(
            __FUNCTION__,
            ['important application event']
        );

        // ...
    }
}</code></pre>

<p><code>AuditTrace</code> writes to <code>logfile.txt</code> under the configured <code>loggingpath</code>. Make sure that directory is writable by PHP and is not unnecessarily exposed as a public download location.</p>

<h2 id="errors">17. Error handling</h2>
<p><code>Controller::run()</code> registers <code>ErrorHandler</code>. The handler distinguishes CLI execution from web execution and uses the configured environment.</p>
<table>
<tr><th>Environment</th><th>Behavior</th></tr>
<tr><td><code>development</code></td><td>Displays a more detailed exception message, file and line information.</td></tr>
<tr><td><code>production</code></td><td>Returns HTTP 500 and displays a generic error message.</td></tr>
</table>
<p>Application code should still handle expected business errors through Command statuses such as <code>CMD_ERROR</code> rather than throwing exceptions for normal user input.</p>

<h2 id="security">18. Security rules for client developers</h2>
<h3>18.1 Treat all client input as untrusted</h3>
<p>This includes GET parameters, POST fields, cookies, HTTP headers and values returned by external clients.</p>

<h3>18.2 SQL</h3>
<ul>
<li>Use prepared statements and bound parameters for values.</li>
<li>Do not concatenate user input into SQL.</li>
<li>Do not place user input in <code>findAll()</code>'s free SQL clause.</li>
<li>Keep Mapper table names and allowed update fields application-controlled.</li>
</ul>

<h3>18.3 HTML output</h3>
<p>Escape untrusted values when inserting them into HTML. The framework example uses <code>htmlspecialchars(..., ENT_QUOTES, 'UTF-8')</code>.</p>

<h3>18.4 HTTP headers and redirects</h3>
<ul>
<li>Do not use arbitrary user input as a redirect destination.</li>
<li>Do not use arbitrary user input as a download filename.</li>
<li>Prevent CR/LF and other header control characters in filenames.</li>
<li>Keep payment callback/redirect URLs under application control.</li>
</ul>

<p>When a protected command is requested without a valid login, the framework temporarily stores the original application path in the <code>originalPath</code> cookie so that the application can return the user to the requested path after authentication. Client applications should not populate or modify this cookie themselves. Redirect targets must remain under application control.</p>

<h3>18.5 Sessions</h3>
<ul>
<li>The framework starts sessions through <code>LoginManager</code>.</li>
<li>Session cookies are configured as Secure, HttpOnly and SameSite=Lax.</li>
<li>Session IDs are regenerated after successful authentication.</li>
<li>Do not expose or copy session identifiers into application data.</li>
</ul>

<h3>18.6 CSRF</h3>
<p>Validate CSRF tokens for state-changing POST operations.</p>

<h3>18.7 Production</h3>
<ul>
<li>Set <code>environment=production</code>.</li>
<li>Disable <code>display_errors</code>.</li>
<li>Protect <code>app_options.ini</code>.</li>
<li>Protect logs and uploaded/private files.</li>
<li>Use HTTPS so Secure cookies and credentials are protected in transit.</li>
</ul>

<h2 id="workflow">19. Recommended development workflow</h2>
<ol>
<li>Install the framework with Composer.</li>
<li>Copy/adapt the example application structure.</li>
<li>Create and protect <code>config/app_options.ini</code>.</li>
<li>Configure the database and mail transport.</li>
<li>Set up the member table and the remember-token table if login persistence is required.</li>
<li>Define application paths in <code>controls.xml</code>.</li>
<li>Create a Command for each application use case.</li>
<li>Select the correct login level in each protected Command.</li>
<li>Put command results into the Request.</li>
<li>Select a view or data renderer in <code>controls.xml</code>.</li>
<li>Use DomainObject/Mapper classes for persistent application entities.</li>
<li>Add CSRF validation to state-changing forms.</li>
<li>Test normal login, logout, inactivity timeout and remember-me.</li>
<li>Run Composer validation and security auditing before deployment.</li>
<li>Set production configuration and verify that no credentials or development diagnostics are exposed.</li>
</ol>

<h2 id="reference">20. Quick reference</h2>
<table>
<tr><th>Task</th><th>Typical API/configuration</th></tr>
<tr><td>Start framework</td><td><code>Controller::run()</code></td></tr>
<tr><td>Get Registry</td><td><code>Registry::instance()</code></td></tr>
<tr><td>Get request</td><td><code>Registry::instance()-&gt;getRequest()</code></td></tr>
<tr><td>Get database</td><td><code>Registry::instance()-&gt;getDb()</code></td></tr>
<tr><td>Get LoginManager</td><td><code>Registry::instance()-&gt;getLoginManager()</code></td></tr>
<tr><td>Read/write request data</td><td><code>$request-&gt;get()</code> / <code>$request-&gt;set()</code></td></tr>
<tr><td>Successful command</td><td><code>return self::CMD_OK;</code></td></tr>
<tr><td>Error command</td><td><code>return self::CMD_ERROR;</code></td></tr>
<tr><td>Public command</td><td><code>new NoLoginRequired()</code></td></tr>
<tr><td>User command</td><td><code>new UserLogin()</code></td></tr>
<tr><td>Admin command</td><td><code>new AdminLogin()</code></td></tr>
<tr><td>Load one object</td><td><code>MyObject::find($id)</code></td></tr>
<tr><td>Load collection</td><td><code>MyObject::findAll()</code></td></tr>
<tr><td>Update object</td><td><code>$object-&gt;update([...])</code></td></tr>
<tr><td>Delete object</td><td><code>$object-&gt;delete()</code></td></tr>
<tr><td>CSRF token</td><td><code>$this-&gt;getCsrfToken()</code></td></tr>
<tr><td>Validate CSRF</td><td><code>$this-&gt;validateCsrfToken($request)</code></td></tr>
<tr><td>Immediate mail</td><td><code>Mailer::sendMail()</code></td></tr>
<tr><td>Queue mail</td><td><code>MailQueue::add()</code></td></tr>
</table>

<h2>21. Final design principle</h2>
<p>The Controller Framework deliberately separates <strong>framework infrastructure</strong> from <strong>application responsibility</strong>. The framework provides routing, command execution, rendering, sessions, authentication, database mapping, CSRF support, mail and audit facilities. The client application remains responsible for its domain rules, authorization details beyond the supplied login levels, validation of business input and the safe use of flexible APIs.</p>

<div class="success"><strong>Release 30 baseline:</strong> build application code around the framework APIs described here, keep untrusted input out of SQL fragments and HTTP headers, protect state-changing requests with CSRF tokens, and deploy with production error handling and protected configuration.</div>

<footer>
<p>Controller Framework 1.0.30 — Client Application Developer Guide</p>
<p>This document describes the APIs and example architecture present in Release 30. Application-specific frameworks layered on top of the Controller Framework may add additional commands, models, renderers, database tables and conventions.</p>
</footer>
</main>
</body>
</html><p class="h1">High level overview</p>

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
