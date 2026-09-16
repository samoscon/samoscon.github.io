---

layout: post
author: dirkvm

---

<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>MembersActivities Framework 1.0.30 — Client Application Developer Guide</title>
<style>
:root {
  --ink: #202124;
  --muted: #5f6368;
  --accent: #1a73e8;
  --accent-soft: #e8f0fe;
  --border: #dadce0;
  --code: #f6f8fa;
  --note: #fff8e1;
  --bg: #ffffff;
}
* { box-sizing: border-box; }
html { scroll-behavior: smooth; }
body {
  margin: 0;
  background: var(--bg);
  color: var(--ink);
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Arial, sans-serif;
  line-height: 1.65;
}
.container {
  max-width: 1050px;
  margin: 0 auto;
  padding: 40px 28px 80px;
}
header {
  border-bottom: 1px solid var(--border);
  padding-bottom: 30px;
  margin-bottom: 38px;
}
h1 { font-size: 2.35rem; line-height: 1.2; margin: 0 0 10px; }
h2 {
  margin-top: 48px;
  padding-bottom: 8px;
  border-bottom: 1px solid var(--border);
  font-size: 1.65rem;
}
h3 { margin-top: 30px; font-size: 1.2rem; }
p, li { font-size: 1rem; }
.subtitle { color: var(--muted); font-size: 1.15rem; }
.meta {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(210px, 1fr));
  gap: 10px;
  margin-top: 24px;
}
.meta div {
  background: var(--accent-soft);
  padding: 12px 15px;
  border-radius: 6px;
}
.meta strong { display: block; }
pre {
  background: var(--code);
  border: 1px solid var(--border);
  border-radius: 6px;
  padding: 16px;
  overflow-x: auto;
  line-height: 1.45;
}
code {
  font-family: "SFMono-Regular", Consolas, "Liberation Mono", monospace;
  font-size: .9em;
}
:not(pre) > code {
  background: var(--code);
  padding: 2px 5px;
  border-radius: 4px;
}
.note {
  background: var(--note);
  border-left: 4px solid #f9ab00;
  padding: 14px 18px;
  margin: 22px 0;
}
table {
  width: 100%;
  border-collapse: collapse;
  margin: 18px 0 28px;
}
th, td {
  border: 1px solid var(--border);
  padding: 9px 11px;
  text-align: left;
  vertical-align: top;
}
th { background: var(--code); }
.diagram {
  background: #fafafa;
  border: 1px solid var(--border);
  border-radius: 6px;
  padding: 18px;
  white-space: pre;
  overflow-x: auto;
  font-family: monospace;
}
.toc {
  background: #fafafa;
  border: 1px solid var(--border);
  padding: 20px 24px;
  border-radius: 6px;
}
.toc ol { margin-bottom: 0; }
.checklist { list-style: none; padding-left: 0; }
.checklist li { margin: 5px 0; }
.checklist li::before { content: "☐ "; }
footer {
  margin-top: 60px;
  padding-top: 20px;
  border-top: 1px solid var(--border);
  color: var(--muted);
  font-size: .9rem;
}
a { color: var(--accent); }
@media print {
  .container { max-width: none; padding: 20px; }
  h2 { break-before: page; }
  pre, table { break-inside: avoid; }
}
</style>
</head>
<body>
<div class="container">

<header>
  <h1>MembersActivities Framework 1.0.30</h1>
  <div class="subtitle">Client Application Developer Guide</div>
  <div class="meta">
    <div><strong>Version</strong>1.0.30</div>
    <div><strong>Controller Framework</strong>1.0.31</div>
    <div><strong>PHP</strong>8.3 or compatible PHP 8.x</div>
    <div><strong>Database</strong>MySQL / MariaDB</div>
    <div><strong>Author</strong>Dirk Van Meirvenne</div>
  </div>
</header>

<section class="toc">
<h2 style="margin-top:0;border:0">Contents</h2>
<ol>
<li><a href="#introduction">Introduction</a></li>
<li><a href="#requirements">Technology Requirements</a></li>
<li><a href="#installation">Installing the Framework</a></li>
<li><a href="#structure">Recommended Client Application Structure</a></li>
<li><a href="#configuration">Application Configuration</a></li>
<li><a href="#database">Database</a></li>
<li><a href="#mvc">MVC Application Flow</a></li>
<li><a href="#commands">Commands</a></li>
<li><a href="#decorators">CommandDecorator</a></li>
<li><a href="#validation">Subscription Validation Strategy</a></li>
<li><a href="#request">Request Object</a></li>
<li><a href="#csrf">CSRF Protection</a></li>
<li><a href="#sessions">Sessions</a></li>
<li><a href="#authentication">Authentication and Login Levels</a></li>
<li><a href="#accesstoken">AccessToken</a></li>
<li><a href="#payments">Payment Security</a></li>
<li><a href="#mollie">Mollie Integration</a></li>
<li><a href="#webhook">Mollie Webhook</a></li>
<li><a href="#models">Models</a></li>
<li><a href="#mapper">Mapper Usage and SQL Security</a></li>
<li><a href="#views">Views and controls.xml</a></li>
<li><a href="#errors">Error Handling</a></li>
<li><a href="#headers">Header and Redirect Security</a></li>
<li><a href="#mail">Mail Queue and Cron</a></li>
<li><a href="#wallet">Google Wallet</a></li>
<li><a href="#types">Application-Specific Types</a></li>
<li><a href="#new-screen">Designing a New Client Screen</a></li>
<li><a href="#testing">Testing</a></li>
<li><a href="#deployment">Deployment Checklist</a></li>
<li><a href="#summary">Architectural Summary</a></li>
</ol>
</section>

<h2 id="introduction">1. Introduction</h2>
<p>The <strong>MembersActivities Framework</strong> is an application framework for building web applications that manage members, activities, subscriptions, payments and related functionality.</p>
<p>It is built on top of the <strong>Controller Framework</strong> and provides reusable domain functionality while allowing each client application to define its own user interface, business rules, subscription validation, activity types, payment descriptions, authentication configuration, mail configuration and optional integrations.</p>
<div class="diagram">Client Application
       │
       ├── Client Commands
       ├── Client CommandDecorators
       ├── Client Views
       ├── Client Strategies
       └── Client configuration
       │
       ▼
MembersActivities Framework 1.0.30
       │
       ▼
Controller Framework 1.0.31</div>
<p>The client application should normally <strong>extend and configure the framework rather than modify framework source code</strong>.</p>

<h2 id="requirements">2. Technology Requirements</h2>
<table>
<tr><th>Component</th><th>Requirement</th></tr>
<tr><td>PHP</td><td>8.3 or compatible PHP 8.x</td></tr>
<tr><td>Controller Framework</td><td>1.0.31</td></tr>
<tr><td>MembersActivities Framework</td><td>1.0.30</td></tr>
<tr><td>Database</td><td>MySQL / MariaDB</td></tr>
<tr><td>Database access</td><td>PDO</td></tr>
<tr><td>Web server</td><td>Apache or compatible PHP web server</td></tr>
<tr><td>Composer</td><td>Required</td></tr>
</table>
<p>The framework uses a MySQL/MariaDB PDO connection. Oracle is not a supported database platform for this release.</p>

<h2 id="installation">3. Installing the Framework</h2>
<p>A client application installs the framework through Composer:</p>
<pre><code>{
    "require": {
        "samoscon/membersactivities-framework": "1.0.30"
    }
}</code></pre>
<p>The MembersActivities Framework requires <code>samoscon/controller-framework ^1.0.31</code>. Composer installs that dependency automatically.</p>
<pre><code>composer install</code></pre>
<p>Use <code>composer install</code> for deployments based on a committed <code>composer.lock</code>.</p>

<h2 id="structure">4. Recommended Client Application Structure</h2>
<pre><code>application/
│
├── index.php
├── composer.json
├── composer.lock
│
├── config/
│   └── app_options.ini
│
├── MVCFramework/
│   ├── controls.xml
│   ├── commands/
│   │   ├── DefaultCommand.php
│   │   ├── admin/
│   │   ├── user/
│   │   └── mollie/
│   ├── model/
│   └── views/
│
└── assets/</code></pre>
<p>The exact directory structure may be adapted, but framework classes should remain in the Composer package and client-specific classes should remain in the client application.</p>

<h2 id="configuration">5. Application Configuration</h2>
<p>Configuration contains application-specific values such as environment, paths, database credentials, mail settings and security secrets.</p>
<pre><code>[config]
environment=development
templatepath=/MVCFramework/views
controlsfile=/MVCFramework/controls.xml
loggingpath=/assets/logging/

[globals]
APP=MyApplication
_APPDIR=https://www.example.com/
_HOMEPAGE=https://www.example.com/
_ASSETDIR=https://www.example.com/assets/

_SALTRAND=...
_RAND=...

_DBUSER=...
_DBPASSWORD="..."
_DBNAME=...
_DBHOST=localhost</code></pre>
<div class="note"><strong>Security:</strong> values such as database passwords, Mollie credentials, Google credentials and especially <code>_SALTRAND</code> must remain secret. The configuration file must not be publicly downloadable.</div>
<p><code>_SALTRAND</code> is used by the Controller Framework's <code>AccessToken</code> implementation. It should be a long, cryptographically random secret.</p>

<h2 id="database">6. Database</h2>
<p>The reference schema is intended for a new client application. Principal tables include:</p>
<pre><code>member
activity
costitem
subscription
payment
remember_tokens
mail_queue</code></pre>
<div class="diagram">Member
 ├── Subscriptions
 ├── Payments
 └── Remember Tokens

Activity
 ├── Cost Items
 └── child Activities

Subscription
 ├── Member
 ├── Cost Item
 └── optional Payment

Payment
 └── Member</div>
<div class="note"><strong>Existing production database:</strong> do not replace it with the reference schema. Back it up and apply controlled schema changes or migrations.</div>

<h2 id="mvc">7. MVC Application Flow</h2>
<div class="diagram">Browser
   │
   ▼
index.php
   │
   ▼
Controller Framework
   │
   ▼
Command Resolver
   │
   ▼
Client Command / CommandDecorator
   │
   ▼
MembersActivities Command
   │
   ▼
Model
   │
   ▼
Database</div>
<p>Commands return statuses such as <code>CMD_DEFAULT</code>, <code>CMD_OK</code>, <code>CMD_CONTINUE</code> and <code>CMD_ERROR</code>. Routing and rendering are defined through the application's controller configuration.</p>

<h2 id="commands">8. Commands</h2>
<p>A client command normally extends:</p>
<pre><code>\controllerframework\controllers\Command</code></pre>
<p>Example:</p>
<pre><code>namespace commands\user;

class MyCommand extends \controllerframework\controllers\Command
{
    public function doExecute(
        \controllerframework\registry\Request $request
    ): int {
        // Application logic
        return self::CMD_DEFAULT;
    }

    protected function getLevelOfLoginRequired(): void
    {
        $this->setLoginLevel(
            new \controllerframework\sessions\NoLoginRequired()
        );
    }
}</code></pre>
<p>A command should obtain and validate request data, perform the required operation, prepare response data and return an appropriate command status.</p>

<h2 id="decorators">9. CommandDecorator</h2>
<p>The <strong>CommandDecorator</strong> is one of the most important extension mechanisms. It allows client-specific behaviour to be added around a reusable MembersActivities command.</p>
<pre><code>class PublicActivityCommand
    extends \controllerframework\controllers\CommandDecorator
{
    public function doExecuteDecorator(
        \controllerframework\registry\Request $request
    ): ?int {
        $request->set(
            'validator',
            new \model\SubscriptionValidationPublic()
        );
        return null;
    }

    public function initCommand(): void
    {
        $this->setCommand(
            new \membersactivities\commands\user\ActivityCommand()
        );
    }

    protected function getLevelOfLoginRequired(): void
    {
        $this->setLoginLevel(
            new \controllerframework\sessions\NoLoginRequired()
        );
    }
}</code></pre>
<p>This pattern avoids modifying generic framework commands for every possible client application's business rule.</p>

<h2 id="validation">10. Subscription Validation Strategy</h2>
<p>Subscription validation uses <code>SubscriptionValidationStrategy</code>. A client application provides a concrete strategy containing its own business rules.</p>
<pre><code>class SubscriptionValidationUser
    extends \membersactivities\model\subscriptions\
             SubscriptionValidationStrategy
{
    protected function doCheckSubscription(
        \controllerframework\members\Member $member,
        \membersactivities\model\activities\Costitem $costitem
    ): array {
        if (!$member->active) {
            return $this->errorcode(
                200,
                'Your membership is inactive.'
            );
        }

        return $this->errorcode(0);
    }
}</code></pre>
<p>The framework can then call the strategy before creating a subscription.</p>
<div class="note"><strong>Security rule:</strong> never instantiate a validator class directly from a request parameter. Use a controlled <code>CommandDecorator</code> to select the concrete strategy.</div>

<h2 id="request">11. Request Object</h2>
<p>The Controller Framework's <code>Request</code> object transports information through the command chain.</p>
<pre><code>$request->get('id');

$request->set('validator', $validator);

$request->addFeedback('Something went wrong.');

$request->set(
    'forwardqueryparams',
    ['id' => $id]
);</code></pre>
<p>The Request object is a transport mechanism, <strong>not a trust boundary</strong>. Data originating from a browser must still be validated.</p>

<h2 id="csrf">12. CSRF Protection</h2>
<p>State-changing requests should use POST and CSRF protection.</p>
<pre><code>// Generate token
$responses['csrf_token'] = $this->getCsrfToken();

// Validate token
if (!$this->validateCsrfToken($request)) {
    $request->set('errorcode', 'InvalidCsrfToken');
    return self::CMD_ERROR;
}</code></pre>
<div class="diagram">GET
 │
 ├── display form
 └── generate CSRF token
       │
       ▼
POST
 │
 ├── validate CSRF token
 ├── validate input
 ├── perform state change
 └── return status</div>
<p>Do not implement state-changing operations through GET requests.</p>

<h2 id="sessions">13. Sessions</h2>
<p>CSRF protection requires an active session. Production applications should use secure session cookies with appropriate <code>Secure</code>, <code>HttpOnly</code> and <code>SameSite</code> settings and should use HTTPS.</p>

<h2 id="authentication">14. Authentication and Login Levels</h2>
<p>Commands define the required authentication level. Typical levels are:</p>
<pre><code>new \controllerframework\sessions\NoLoginRequired()
new \controllerframework\sessions\UserLogin()
new \controllerframework\sessions\AdminLogin()</code></pre>
<p>Administrative commands that expose or modify sensitive data should normally require <code>AdminLogin</code>.</p>

<h2 id="accesstoken">15. AccessToken</h2>
<p>Controller Framework 1.0.31 provides <code>\controllerframework\security\AccessToken</code> for protected public operations.</p>
<pre><code>$accessToken =
    \controllerframework\security\AccessToken::generate(
        'mollie-order',
        (string) $orderid
    );

\controllerframework\security\AccessToken::validate(
    'mollie-order',
    (string) $orderid,
    $accessToken
);</code></pre>
<p>The purpose and identifier form part of the security boundary. Tokens should only be transmitted over HTTPS and should not be unnecessarily logged.</p>

<h2 id="payments">16. Payment Security</h2>
<p>Payment amounts must come from trusted server-side data. A browser must never be allowed to determine the amount sent to Mollie.</p>
<div class="diagram">Browser
   │
   │ order ID
   ▼
Server
   │
   ├── retrieve Payment
   ├── read trusted amount
   └── create Mollie payment</div>
<p>The payment object should be loaded from the database and its server-side <code>amount</code> used when creating the payment.</p>

<h2 id="mollie">17. Mollie Integration</h2>
<p>Mollie integration is optional. Relevant commands include:</p>
<pre><code>PaymentToMollieCommand
OrderToMollieCommand
WebhookFromMollieCommand
PaymentConfirmationCommand</code></pre>
<p>The generic payment command can be decorated by the client application.</p>
<h3>17.1 Application-specific order descriptions</h3>
<p>The payment amount is obtained from the server-side <code>Payment</code> object. The order description can be supplied by the client-specific <code>CommandDecorator</code> through the <code>Request</code>:</p>
<pre><code>$request->set(
    'orderDescription',
    'Concert tickets - May 2027'
);</code></pre>
<p>The underlying <code>OrderToMollieCommand</code> reads this value. This allows different order types to have different descriptions without modifying the generic framework command.</p>

<h2 id="webhook">18. Mollie Webhook and Payment Confirmation</h2>
<p>The webhook is a machine-to-machine endpoint rather than a normal user interface. It should retrieve the current payment status from Mollie and update the corresponding server-side payment.</p>
<p>The customer redirect is not the authoritative source of payment status.</p>
<p>Payment confirmation should validate the protected order identifier and its <code>AccessToken</code> before displaying information associated with a payment.</p>

<h2 id="models">19. Models</h2>
<p>Core domain models include:</p>
<pre><code>Activity
ActivityComposite
Costitem
Member
Subscription
Payment
GoogleWalletTicket</code></pre>
<p>The client application should use the model API where possible instead of directly manipulating database records.</p>

<h2 id="mapper">20. Mapper Usage and SQL Security</h2>
<p>The framework uses mapper classes such as <code>ActivityMapper</code>, <code>CostitemMapper</code>, <code>PaymentMapper</code> and <code>SubscriptionMapper</code>.</p>
<p>For example:</p>
<pre><code>$payment = \model\Payment::find($id);</code></pre>
<p>Special care is required with <code>Mapper::findAll()</code>, because it accepts a free SQL select clause.</p>
<pre><code>$mapper->findAll($selectClause);</code></pre>
<div class="note"><strong>SQL security:</strong> never concatenate untrusted user input into a free SQL clause. Validate and whitelist values before they are incorporated into SQL, and prefer the framework's model/database mechanisms where possible.</div>

<h2 id="views">21. Views and controls.xml</h2>
<p>Views are responsible for presentation. Commands should prepare data and pass it to the view.</p>
<p>Command routing is defined in <code>controls.xml</code>. A simplified example:</p>
<pre><code>&lt;command
    path="/activity"
    class="\commands\user\PublicActivityCommand"&gt;

    &lt;view name="/user/activity" /&gt;

    &lt;status value="CMD_ERROR"&gt;
        &lt;view name="/errorView" /&gt;
    &lt;/status&gt;

&lt;/command&gt;</code></pre>

<h2 id="errors">22. Error Handling</h2>
<p>Use the Controller Framework's centralized error handling for unexpected exceptions.</p>
<pre><code>try {
    $activity = \model\Activity::find($id);
}
catch (\Throwable $ex) {
    \controllerframework\error\ErrorHandler
        ::handleException($ex);

    $request->addFeedback(
        'Unable to retrieve the requested activity.'
    );

    return self::CMD_ERROR;
}</code></pre>
<p>Do not expose stack traces or exception dumps to users. Most importantly, do not catch an exception and then continue as if the failed operation had succeeded.</p>

<h2 id="headers">23. Header and Redirect Security</h2>
<p>Take special care with values used in:</p>
<ul>
<li><code>Location</code> headers;</li>
<li><code>Content-Disposition</code>;</li>
<li>file names;</li>
<li>redirect URLs;</li>
<li>calendar/ICS file names.</li>
</ul>
<p>Validate or whitelist such values before placing them in HTTP headers. When constructing query strings, prefer <code>http_build_query()</code> to manual concatenation.</p>

<h2 id="mail">24. Mail Queue and Cron</h2>
<p>The framework supports asynchronous mail processing through the <code>mail_queue</code> table. Typical statuses include:</p>
<pre><code>pending
sending
sent
failed</code></pre>
<p>A typical workflow is:</p>
<div class="diagram">Create mail queue records
        │
        ▼
Return response to user
        │
        ▼
Cron job
        │
        ▼
Process mail queue</div>
<p>Cron jobs should use absolute paths:</p>
<pre><code>php /absolute/path/to/application/index.php cleanupMailQueue</code></pre>
<p>Do not rely on the current working directory in cron jobs.</p>

<h2 id="wallet">25. Google Wallet</h2>
<p>Google Wallet integration is optional. Applications using it require appropriate Google credentials, an issuer ID and application configuration. Credential files should be kept outside publicly accessible locations whenever possible.</p>

<h2 id="types">26. Application-Specific Types</h2>
<p>The framework provides extension points such as:</p>
<pre><code>ActivityTypeImplementation
CostitemTypeImplementation
PaymentTypeImplementation
SubscriptionTypeImplementation</code></pre>
<p>Client-specific business rules should be implemented in these extension points where appropriate rather than accumulating all business logic in controllers.</p>

<h2 id="new-screen">27. Designing a New Client Screen</h2>
<h3>Step 1 — Define the route</h3>
<p>Add the route to <code>controls.xml</code>.</p>
<h3>Step 2 — Create a client CommandDecorator</h3>
<pre><code>namespace commands\user;

class MyActivityCommand
    extends \controllerframework\controllers\CommandDecorator
{
    public function doExecuteDecorator(
        \controllerframework\registry\Request $request
    ): ?int {
        // Application-specific preparation
        return null;
    }

    public function initCommand(): void
    {
        $this->setCommand(
            new \membersactivities\commands\user\ActivityCommand()
        );
    }

    protected function getLevelOfLoginRequired(): void
    {
        $this->setLoginLevel(
            new \controllerframework\sessions\UserLogin()
        );
    }
}</code></pre>
<h3>Step 3 — Add application-specific strategies</h3>
<pre><code>$request->set(
    'validator',
    new \model\SubscriptionValidationUser()
);</code></pre>
<h3>Step 4 — Add the view</h3>
<p>Create the appropriate template.</p>
<h3>Step 5 — Configure status handling</h3>
<pre><code>&lt;status value="CMD_ERROR"&gt;
    &lt;view name="/errorView" /&gt;
&lt;/status&gt;</code></pre>
<h3>Step 6 — Test</h3>
<p>Test authenticated and unauthenticated users, invalid identifiers, invalid POST data, invalid CSRF tokens and expected failure paths.</p>

<h2 id="testing">28. Testing a New Release</h2>
<h3>Public functionality</h3>
<ul><li>Home page</li><li>Activity display</li><li>Activity details</li><li>Public registration</li><li>Payment initiation</li><li>Payment confirmation</li></ul>
<h3>Member functionality</h3>
<ul><li>Login</li><li>Logout</li><li>Member activity</li><li>Subscription</li><li>Payment</li><li>Remember-me login</li></ul>
<h3>Administrator functionality</h3>
<ul><li>Activity creation/editing</li><li>Cost item management</li><li>Member management</li><li>Payment management</li><li>Reports and Excel exports</li></ul>
<h3>Integration functionality</h3>
<ul><li>Mollie payment</li><li>Mollie webhook</li><li>Google Wallet</li><li>Mail queue</li><li>Cron jobs</li></ul>
<h3>Security</h3>
<ul><li>Invalid IDs</li><li>Invalid CSRF tokens</li><li>Missing authentication</li><li>Unauthorized administrative access</li><li>Invalid access tokens</li><li>Modified payment identifiers</li><li>Modified payment amounts</li><li>Unexpected input</li></ul>

<h2 id="deployment">29. Deployment Checklist</h2>
<ul class="checklist">
<li>PHP version verified</li>
<li>Composer dependencies installed</li>
<li>Controller Framework 1.0.31 installed</li>
<li>MembersActivities Framework 1.0.30 installed</li>
<li>Database configured</li>
<li>Database backup available</li>
<li><code>app_options.ini</code> protected</li>
<li><code>_SALTRAND</code> generated and secret</li>
<li>Production Mollie credentials configured</li>
<li>Google credentials protected</li>
<li>HTTPS enabled</li>
<li>Session security configured</li>
<li>CSRF protection tested</li>
<li>Administrative commands protected</li>
<li>AccessToken flows tested</li>
<li>Payment amount verified as server-side data</li>
<li>Mollie webhook tested</li>
<li>Mail queue tested</li>
<li>Cron jobs configured with absolute paths</li>
<li>Error logging configured</li>
<li>Debug output disabled</li>
<li>Public, user and administrator routes tested</li>
<li>Downloads tested</li>
</ul>

<h2 id="summary">30. Architectural Summary</h2>
<p>MembersActivities Framework 1.0.30 is intended to be used as a reusable domain framework rather than as a complete application.</p>
<div class="diagram">┌─────────────────────────────────────────┐
│          Client Application             │
│                                         │
│ Commands / Decorators / Views           │
│ Strategies / Business Rules             │
│ Configuration / Integrations            │
└────────────────────┬────────────────────┘
                     │
┌────────────────────▼────────────────────┐
│     MembersActivities Framework         │
│                                         │
│ Members / Activities / Subscriptions    │
│ Payments / Mail / Mollie / Wallet       │
└────────────────────┬────────────────────┘
                     │
┌────────────────────▼────────────────────┐
│       Controller Framework 1.0.31       │
│                                         │
│ MVC / Commands / Request / Sessions     │
│ Security / CSRF / AccessToken / PDO    │
│ Error handling / Audit / Rendering      │
└─────────────────────────────────────────┘</div>
<p>The key development principle is:</p>
<div class="note"><strong>Keep generic functionality in the framework and implement client-specific behaviour through decorators, strategies, models, commands, configuration and views.</strong></div>
<p>This makes client applications easier to maintain and allows framework upgrades without unnecessarily modifying application code.</p>

<footer>
  <p><strong>Reference version:</strong> MembersActivities Framework 1.0.30 · Controller Framework 1.0.31 · PHP 8.3 · MySQL/MariaDB</p>
  <p>Client applications should verify the exact framework versions in <code>composer.json</code> and <code>composer.lock</code> before applying this guide to an existing installation.</p>
</footer>

</div>
</body>
</html>
