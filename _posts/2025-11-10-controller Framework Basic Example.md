---
layout: post
author: dirkvm
---

<html lang="en">
<head>
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Controller Framework 1.0.31 — Basic Implementation User Guide</title>
<style>
:root { --text:#1f2937; --muted:#64748b; --border:#dbe2ea; --panel:#f8fafc; --code:#111827; }
* { box-sizing:border-box; }
body { margin:0; font-family:system-ui,-apple-system,BlinkMacSystemFont,"Segoe UI",sans-serif; color:var(--text); background:#fff; line-height:1.62; }
main { max-width:1100px; margin:auto; padding:42px 28px 80px; }
h1 { font-size:2.4rem; line-height:1.15; margin-bottom:.5rem; }
h2 { margin-top:3rem; padding-top:.5rem; border-bottom:1px solid var(--border); padding-bottom:.45rem; }
h3 { margin-top:2rem; }
.lead { font-size:1.15rem; color:#475569; }
.meta { color:var(--muted); }
.note,.warning,.success { padding:16px 18px; border-left:4px solid #64748b; background:var(--panel); margin:20px 0; border-radius:6px; }
.warning { border-left-color:#b45309; }
.success { border-left-color:#15803d; }
pre { background:var(--code); color:#f3f4f6; padding:18px; overflow:auto; border-radius:8px; font-size:.9rem; line-height:1.5; }
code { font-family:"SFMono-Regular",Consolas,"Liberation Mono",monospace; }
:not(pre)>code { background:#f1f5f9; padding:.12em .3em; border-radius:4px; }
table { width:100%; border-collapse:collapse; margin:18px 0; }
th,td { border:1px solid var(--border); padding:9px 11px; text-align:left; vertical-align:top; }
th { background:var(--panel); }
.tree { background:#0f172a; color:#e2e8f0; padding:20px; border-radius:8px; }
.flow { display:flex; gap:10px; flex-wrap:wrap; align-items:center; margin:20px 0; }
.box { border:1px solid #94a3b8; border-radius:8px; padding:12px 16px; background:#fff; }
.arrow { color:#64748b; font-weight:bold; }
.toc a { color:#2563eb; text-decoration:none; }
.small { font-size:.9rem; color:var(--muted); }
footer { margin-top:60px; padding-top:20px; border-top:1px solid var(--border); color:var(--muted); }
@media print { pre { white-space:pre-wrap; } main { max-width:none; } }
</style>
</head>
<body>
<main>

<h1>Controller Framework 1.0.31<br>Basic Implementation User Guide</h1>
<p class="lead">A step-by-step tutorial that starts with an empty PHP application and ends with a login-protected screen displaying:</p>
<p><strong>Hi [name of User], welcome to our login protected environment.</strong></p>
<p>and a <strong>Logout</strong> button.</p>
<p class="meta">Based on Controller Framework 1.0.31 and its Application Controller implementation.</p>

<div class="success">
<strong>What you will build</strong><br>
A minimal PHP 8.3 web application with Composer, the Controller Framework,
a MySQL/MariaDB database, a public login screen, CSRF protection, a
<code>UserLogin</code>-protected welcome screen and the framework's
<code>LogoutCommand</code>.
</div>

<h2 id="toc">Contents</h2>
<ol class="toc">
<li><a href="#concept">The basic concept</a></li>
<li><a href="#prerequisites">Prerequisites</a></li>
<li><a href="#structure">Create the application structure</a></li>
<li><a href="#composer">Install the framework with Composer</a></li>
<li><a href="#bootstrap">Create the application bootstrap</a></li>
<li><a href="#config">Configure the framework</a></li>
<li><a href="#database">Create the database</a></li>
<li><a href="#model">Create the minimal Member model</a></li>
<li><a href="#commands">Create the Commands</a></li>
<li><a href="#controls">Connect everything in controls.xml</a></li>
<li><a href="#views">Create the login and welcome views</a></li>
<li><a href="#run">Run and test the application</a></li>
<li><a href="#flow">Understand what happens internally</a></li>
<li><a href="#extend">Where to go from here</a></li>
</ol>

<h2 id="concept">1. The basic concept</h2>
<p>The Application Controller variant of the framework follows a simple request lifecycle:</p>
<div class="flow">
<div class="box">Browser request</div><div class="arrow">→</div>
<div class="box"><code>index.php</code></div><div class="arrow">→</div>
<div class="box"><code>Controller::run()</code></div><div class="arrow">→</div>
<div class="box">Init</div><div class="arrow">→</div>
<div class="box">Command</div><div class="arrow">→</div>
<div class="box">Renderer</div><div class="arrow">→</div>
<div class="box">HTML response</div>
</div>
<p>The important principle is that <code>controls.xml</code> connects a URL path to a
Command and to the renderer used for its result. The Command contains the application
logic; the View contains the presentation.</p>

<h2 id="prerequisites">2. Prerequisites</h2>
<ul>
<li>PHP <strong>8.3 or later within the framework's <code>^8.3</code> requirement</strong>.</li>
<li>Composer.</li>
<li>MySQL or MariaDB.</li>
<li>A web server capable of rewriting application paths to <code>index.php</code>.</li>
<li>HTTPS. Release 30 deliberately configures the session cookie as <code>Secure</code>.</li>
</ul>
<div class="warning"><strong>Important:</strong> the tutorial uses development configuration.
For a production installation, use <code>environment=production</code> and do not expose PHP
errors to visitors.</div>

<h2 id="structure">3. Create the application structure</h2>
<p>Start with an empty web application directory. Create this structure:</p>
<div class="tree"><pre style="background:transparent;padding:0;margin:0">basic-app/
├── composer.json
├── index.php
├── .htaccess
├── DatabaseSetup.sql
├── config/
│   ├── app_options.ini
│   └── .htaccess
├── assets/
│   └── logging/
└── MVCFramework/
    ├── controls.xml
    ├── commands/
    │   ├── LoginCommand.php
    │   └── WelcomeCommand.php
    ├── model/
    │   ├── Member.php
    │   ├── MemberMapper.php
    │   └── Member_RGLR.php
    └── views/
        ├── loginView.php
        ├── welcomeView.php
        └── includes/
            └── head.php</pre></div>

<p>This keeps framework-independent client code under <code>MVCFramework/</code>, while
Composer installs the framework itself under <code>vendor/</code>.</p>

<h2><i>Note: Complete minimal example</i></h2>
<p>A ready-to-copy version of all files used in this tutorial is supplied as <a href="/assets/controller-framework-1.0.30-basic-example.zip">a ZIP archive</a>
alongside this guide. It contains the same application structure and code shown below.</p>

<h2 id="composer">4. Install the framework with Composer</h2>
<p>Create <code>composer.json</code>:</p>
<pre><code>{
    "name": "example/controller-framework-app",
    "require": {
        "php": "^8.3",
        "samoscon/controller-framework": "^1.0.31"
    }
}</code></pre>
<p>Run:</p>
<pre><code>composer install</code></pre>
<p>Composer creates <code>vendor/autoload.php</code>. Your application will load this
before starting the Controller Framework.</p>

<h2 id="bootstrap">5. Create the application bootstrap</h2>
<p>The application's only real entry point is <code>index.php</code>:</p>
<pre><code>&lt;?php

require __DIR__ . '/vendor/autoload.php';

spl_autoload_register(function (string $className): void {
    $file = __DIR__
        . '/MVCFramework/'
        . str_replace('\\', '/', $className)
        . '.php';

    if (is_file($file)) {
        require_once $file;
    }
});

controllerframework\controllers\Controller::run();</code></pre>

<p>There are two autoloading responsibilities:</p>
<ol>
<li>Composer loads the framework classes.</li>
<li>The small client autoloader loads your own classes from <code>MVCFramework/</code>.</li>
</ol>

<p><code>Controller::run()</code> starts the framework. Internally it registers the
<code>ErrorHandler</code>, initializes the application and then handles the current request.</p>

<h2 id="config">6. Configure the framework</h2>
<p>Create <code>config/app_options.ini</code>. The minimal configuration contains a
<code>[config]</code> section and a <code>[globals]</code> section because
<code>InitController</code> requires both.</p>
<pre><code>[config]
environment=development
templatepath=/MVCFramework/views
controlsfile=/MVCFramework/controls.xml
loggingpath=/assets/logging/

[globals]
APP=BASIC
_APPDIR=https://your-domain.example/
_HOMEPAGE=https://your-domain.example/
_MINLEVELTOLOGIN=U

_DBUSER=your_database_user
_DBPASSWORD="your_database_password"
_DBNAME=your_database_name
_DBHOST=localhost</code></pre>

<p>For the complete framework mail/error configuration, add the mail globals shown in the
supplied example project.</p>

<div class="note">
<strong>What happens during initialization?</strong>
<code>InitController</code> determines the application root, loads
<code>config/app_options.ini</code>, creates the application configuration, defines the
configured globals, locates <code>controls.xml</code>, compiles it with
<code>RenderCompiler</code>, and stores the resulting command map in the
<code>Registry</code>.
</div>

<p>Protect the configuration directory from direct HTTP access with <code>config/.htaccess</code>.</p>

<h2 id="database">7. Create the database</h2>
<p>The framework's authentication uses the client application's <code>member</code> table.
The remember-me implementation also uses <code>remember_tokens</code>.</p>
<p>For this tutorial, create both tables and one test user.</p>
<pre><code>CREATE TABLE member (
    id INT UNSIGNED NOT NULL AUTO_INCREMENT,
    description VARCHAR(190) DEFAULT 'description',
    classification VARCHAR(4) NOT NULL DEFAULT 'RGLR',
    parent_id INT UNSIGNED DEFAULT 0,
    name VARCHAR(45) DEFAULT 'name',
    lastname VARCHAR(45) DEFAULT 'lastname',
    email VARCHAR(255) DEFAULT NULL,
    role VARCHAR(5) NOT NULL DEFAULT 'U',
    password VARCHAR(256) DEFAULT NULL,
    ownpwd TINYINT(1) DEFAULT 0,
    active TINYINT(1) DEFAULT 0,
    subscriptionuntil DATE DEFAULT NULL,
    PRIMARY KEY (id),
    UNIQUE KEY uq_member_email (email)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

CREATE TABLE remember_tokens (
    id BIGINT UNSIGNED NOT NULL AUTO_INCREMENT,
    member_id INT UNSIGNED NOT NULL,
    selector CHAR(24) CHARACTER SET ascii COLLATE ascii_bin NOT NULL,
    token_hash CHAR(64) CHARACTER SET ascii COLLATE ascii_bin NOT NULL,
    expires_at DATETIME NOT NULL,
    created_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
    last_used_at DATETIME NULL DEFAULT NULL,
    PRIMARY KEY (id),
    UNIQUE KEY uq_remember_selector (selector),
    KEY idx_remember_member (member_id),
    KEY idx_remember_expires (expires_at),
    CONSTRAINT fk_remember_member
        FOREIGN KEY (member_id) REFERENCES member(id)
        ON DELETE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;</code></pre>

<p>Insert a test member. The password below is a PHP <code>password_hash()</code> result
for the tutorial password <code>Demo123!</code>:</p>
<pre><code>INSERT INTO member
    (classification, name, lastname, email, role, password, ownpwd, active)
VALUES
    (
        'RGLR',
        'Demo User',
        'Example',
        'demo@example.com',
        'U',
        '$2y$12$CM39.EaqzfUqdivsgnL8bu2Kyz1jMasOI9A8xWMoujgAYk94lvLTu',
        1,
        1
    );</code></pre>

<div class="warning"><strong>Test credentials only.</strong> Do not use this password in a
real application.</div>

<h2 id="model">8. Create the minimal Member model</h2>
<p>There is one framework-specific requirement that is easy to miss:
<code>User::getInstance()</code> and the member framework expect the concrete client
member class to be <code>\model\Member</code>.</p>

<h3>8.1 Member.php</h3>
<pre><code>&lt;?php

namespace model;

class Member extends \controllerframework\members\Member
{
    public function initiatePassword(string $pwd = ''): string
    {
        return 'Your temporary password is ' . htmlspecialchars(
            $pwd,
            ENT_QUOTES,
            'UTF-8'
        );
    }
}</code></pre>

<p>The method is required because the framework's abstract <code>Member</code> declares
<code>initiatePassword()</code>.</p>

<h3>8.2 MemberMapper.php</h3>
<pre><code>&lt;?php

namespace model;

final class MemberMapper extends \controllerframework\members\MemberMapper
{
}</code></pre>

<p>The framework derives the mapper for <code>\model\Member</code> as
<code>\model\MemberMapper</code>.</p>

<h3>8.3 Member_RGLR.php</h3>
<pre><code>&lt;?php

namespace model;

final class Member_RGLR
    extends \controllerframework\members\MemberTypeImplementation
{
    public function getYearlyParticipationFee(Member $member): int
    {
        return 0;
    }
}</code></pre>

<p>The <code>classification</code> value <code>RGLR</code> causes the framework to look
for <code>\model\Member_RGLR</code>. For this minimal example, the membership fee is irrelevant,
so the implementation returns zero.</p>

<h2 id="commands">9. Create the Commands</h2>

<h3>9.1 LoginCommand</h3>
<p>The login screen is public, so it uses <code>NoLoginRequired</code>. It performs the
credential check only when the form is submitted.</p>
<pre><code>&lt;?php

namespace commands;

use controllerframework\controllers\Command;
use controllerframework\registry\Request;
use controllerframework\sessions\NoLoginRequired;

final class LoginCommand extends Command
{
    public function doExecute(Request $request): int
    {
        $this->reg->getLoginManager();

        $passwordIsValid = true;

        if ($_SERVER['REQUEST_METHOD'] === 'POST') {
            if (!$this->validateCsrfToken($request)) {
                $request->set('errorcode', 'InvalidCsrfToken');
                return self::CMD_ERROR;
            }

            $username = trim((string) $request->get('username'));
            $password = (string) $request->get('password');

            $memberId = $this->reg->getLoginManager()
                ->validateUsername($username);

            if ($memberId !== false) {
                $passwordIsValid = $this->reg->getLoginManager()
                    ->validatePassword((int) $memberId, $password);
            } else {
                $passwordIsValid = false;
            }

            if ($passwordIsValid) {
                $keepLoggedIn =
                    $request->get('rememberMe') === 'Y';

                $this->reg->getLoginManager()->login(
                    (int) $memberId,
                    $keepLoggedIn
                );

                return self::CMD_OK;
            }
        }

        $request->set('csrf_token', $this->getCsrfToken());
        $request->set('passwordIsValid', $passwordIsValid);

        return self::CMD_DEFAULT;
    }

    protected function getLevelOfLoginRequired(): void
    {
        $this->setLoginLevel(new NoLoginRequired());
    }
}</code></pre>

<div class="note">
<strong>Notice the separation:</strong> <code>execute()</code> belongs to the framework and
checks the configured login level. Your application implements only
<code>doExecute()</code> and <code>getLevelOfLoginRequired()</code>.
</div>

<h3>9.2 WelcomeCommand</h3>
<p>This is the first genuinely protected application screen.</p>
<pre><code>&lt;?php

namespace commands;

use controllerframework\controllers\Command;
use controllerframework\registry\Request;
use controllerframework\sessions\User;
use controllerframework\sessions\UserLogin;

final class WelcomeCommand extends Command
{
    public function doExecute(Request $request): int
    {
        $user = User::getInstance();

        if ($user === null) {
            return self::CMD_ERROR;
        }

        $request->set('name', (string) $user->name);

        return self::CMD_OK;
    }

    protected function getLevelOfLoginRequired(): void
    {
        $this->setLoginLevel(new UserLogin());
    }
}</code></pre>

<p>The important line is:</p>
<pre><code>$this->setLoginLevel(new UserLogin());</code></pre>
<p>From that moment on, the framework itself protects the command. The command does not
need to implement its own session checks.</p>

<p><code>UserLogin</code> requires an active member and uses a 60-minute inactivity period
for a normal login. If remember-me authentication is used, Release 30 deliberately lets
the persistent remember token determine the session duration rather than applying that
inactivity timeout.</p>

<h3>9.3 Logout</h3>
<p>No client Command is required. Release 30 already provides:</p>
<pre><code>\controllerframework\commands\login\LogoutCommand</code></pre>
<p>It calls <code>LoginManager::logout()</code>, removes the session and remember-me
tokens, and can then forward to the login path.</p>

<h2 id="controls">10. Connect everything in controls.xml</h2>
<p>Now map the paths to Commands and renderers:</p>
<pre><code>&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;control&gt;

    &lt;command path="/" class="\commands\LoginCommand"&gt;
        &lt;view name="/loginView" /&gt;

        &lt;status value="CMD_OK"&gt;
            &lt;forward path="/welcome" /&gt;
        &lt;/status&gt;

        &lt;status value="CMD_ERROR"&gt;
            &lt;view name="/loginView" /&gt;
        &lt;/status&gt;
    &lt;/command&gt;

    &lt;command path="/welcome" class="\commands\WelcomeCommand"&gt;
        &lt;view name="/welcomeView" /&gt;

        &lt;status value="CMD_ERROR"&gt;
            &lt;view name="/loginView" /&gt;
        &lt;/status&gt;
    &lt;/command&gt;

    &lt;command path="/logout"
        class="\controllerframework\commands\login\LogoutCommand"&gt;
        &lt;status value="CMD_OK"&gt;
            &lt;forward path="/" /&gt;
        &lt;/status&gt;
    &lt;/command&gt;

&lt;/control&gt;</code></pre>

<p>There are now exactly three application paths:</p>
<table>
<tr><th>Path</th><th>Command</th><th>Login</th><th>Result</th></tr>
<tr><td><code>/</code></td><td><code>LoginCommand</code></td><td>None</td><td>Login view or forward to <code>/welcome</code></td></tr>
<tr><td><code>/welcome</code></td><td><code>WelcomeCommand</code></td><td><code>UserLogin</code></td><td>Welcome view</td></tr>
<tr><td><code>/logout</code></td><td>Framework <code>LogoutCommand</code></td><td>None</td><td>Logout and forward to <code>/</code></td></tr>
</table>

<h2 id="views">11. Create the login and welcome views</h2>

<h3>11.1 Login view</h3>
<p>The login form contains the CSRF token supplied by the Command:</p>
<pre><code>&lt;form method="post" action="/"&gt;
    &lt;input
        type="hidden"
        name="csrf_token"
        value="&lt;?= htmlspecialchars(
            (string) $request-&gt;get('csrf_token'),
            ENT_QUOTES,
            'UTF-8'
        ) ?&gt;"
    &gt;

    &lt;label for="username"&gt;Email address&lt;/label&gt;
    &lt;input type="email" name="username" id="username" required&gt;

    &lt;label for="password"&gt;Password&lt;/label&gt;
    &lt;input type="password" name="password" id="password" required&gt;

    &lt;label&gt;
        &lt;input type="checkbox" name="rememberMe" value="Y"&gt;
        Remember me
    &lt;/label&gt;

    &lt;button type="submit"&gt;Log in&lt;/button&gt;
&lt;/form&gt;</code></pre>

<p>The complete example also displays a generic error when the credentials are invalid.</p>

<h3>11.2 Welcome view</h3>
<p>The Command puts the authenticated user's name into the Request:</p>
<pre><code>$request-&gt;set('name', (string) $user-&gt;name);</code></pre>

<p>The view then displays it safely:</p>
<pre><code>&lt;h1&gt;
    Hi &lt;?= htmlspecialchars(
        (string) $request-&gt;get('name'),
        ENT_QUOTES,
        'UTF-8'
    ) ?&gt;,
    welcome to our login protected environment.
&lt;/h1&gt;

&lt;form method="get" action="/logout"&gt;
    &lt;button type="submit"&gt;Logout&lt;/button&gt;
&lt;/form&gt;</code></pre>

<p>The result for the demo user is:</p>
<div class="success"><strong>Hi Demo User, welcome to our login protected environment.</strong><br><br><button>Logout</button></div>

<h2 id="run">12. Run and test the application</h2>
<ol>
<li>Create the database and execute <code>DatabaseSetup.sql</code>.</li>
<li>Enter the database credentials in <code>config/app_options.ini</code>.</li>
<li>Set <code>_APPDIR</code> to the real HTTPS application URL.</li>
<li>Run <code>composer install</code>.</li>
<li>Make sure <code>assets/logging/</code> is writable by the web server.</li>
<li>Open the application root in a browser.</li>
</ol>

<h3>Test 1 — Login page</h3>
<p>You should see the login form at <code>/</code>.</p>

<h3>Test 2 — Invalid password</h3>
<p>Enter the correct email and an incorrect password. The same login page should be
displayed with a generic authentication error.</p>

<h3>Test 3 — Successful login</h3>
<p>Use:</p>
<pre><code>Email:    demo@example.com
Password: Demo123!</code></pre>
<p>The framework creates the authenticated session and forwards to <code>/welcome</code>.</p>

<h3>Test 4 — Direct access to /welcome</h3>
<p>Log out and directly open <code>/welcome</code>. <code>UserLogin</code> rejects the
request and the Command infrastructure records the original path. The login view is
rendered. After a successful login, the framework's forwarding mechanism can return the
user to the original protected path.</p>

<h3>Test 5 — Logout</h3>
<p>Click <strong>Logout</strong>. The framework's <code>LogoutCommand</code> destroys the
session and removes remember-me tokens, then forwards to <code>/</code>.</p>

<h2 id="flow">13. Understand what happens internally</h2>
<h3>13.1 First request: GET /</h3>
<div class="flow">
<div class="box"><code>GET /</code></div><div class="arrow">→</div>
<div class="box"><code>Controller::run()</code></div><div class="arrow">→</div>
<div class="box"><code>InitApplicationController</code></div><div class="arrow">→</div>
<div class="box"><code>RenderCompiler</code></div><div class="arrow">→</div>
<div class="box"><code>LoginCommand</code></div><div class="arrow">→</div>
<div class="box"><code>loginView.php</code></div>
</div>

<h3>13.2 POST / with valid credentials</h3>
<div class="flow">
<div class="box">Login form</div><div class="arrow">→</div>
<div class="box">CSRF validation</div><div class="arrow">→</div>
<div class="box">validateUsername()</div><div class="arrow">→</div>
<div class="box">validatePassword()</div><div class="arrow">→</div>
<div class="box">LoginManager::login()</div><div class="arrow">→</div>
<div class="box">CMD_OK</div><div class="arrow">→</div>
<div class="box">/welcome</div>
</div>

<p><code>LoginManager::login()</code> regenerates the session ID, stores the member ID and
session state, and optionally creates a 30-day remember-me token.</p>

<h3>13.3 GET /welcome</h3>
<p>Before <code>WelcomeCommand::doExecute()</code> is allowed to run, the framework invokes
the Command's configured <code>UserLogin</code> strategy. That strategy checks the current
user and membership state. Only after validation succeeds does the framework execute the
Command.</p>

<h3>13.4 Rendering</h3>
<p>After the Command returns its status, <code>HandleRequestApplicationController</code>
asks the corresponding <code>RenderComponentDescriptor</code> for the renderer. For
<code>CMD_OK</code>, <code>ViewRenderComponent</code> includes <code>welcomeView.php</code>.</p>

<h2 id="extend">14. Where to go from here</h2>
<p>Once this minimal application works, the natural next steps are:</p>
<ol>
<li>Add more protected Commands using <code>UserLogin</code>.</li>
<li>Add administrator screens using <code>AdminLogin</code>.</li>
<li>Move reusable UI elements into view includes.</li>
<li>Add domain objects and Mappers for your own tables.</li>
<li>Use <code>CommandDecorator</code> when integrating reusable application framework Commands.</li>
<li>Add password initiation/change flows using the framework's existing login Commands.</li>
<li>Use <code>DatarequestRenderComponent</code>, downloads, AJAX or forwards where required.</li>
<li>Add CSRF validation to every state-changing POST Command.</li>
<li>Switch to <code>environment=production</code> and harden server configuration before deployment.</li>
</ol>

<div class="note">
<strong>Key development rule:</strong> do not put authentication checks in every view.
Declare the required login strategy in the Command and let the framework's
<code>Command::execute()</code> and <code>LoginRequired</code> hierarchy enforce it.
</div>

<footer>
Controller Framework 1.0.31 — Basic Implementation User Guide<br>
Prepared as a practical client-application tutorial.
</footer>

</main>
</body>
</html>
