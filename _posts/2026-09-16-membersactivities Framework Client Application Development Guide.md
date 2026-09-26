---

layout: post
author: dirkvm

---

<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>MembersActivities Framework 1.1.0 — Client Application Developer Guide</title>
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
  <h1>MembersActivities Framework 1.1.0</h1>
  <div class="subtitle">Client Application Developer Guide</div>

  <div class="meta">
    <div><strong>Version</strong>1.1.0</div>
    <div><strong>Previous Version</strong>1.0.31</div>
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
<li><a href="#release">What's New in Version 1.1.0</a></li>
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
<li><a href="#models">Models and the Composite Pattern</a></li>
<li><a href="#tickets">Tickets</a></li>
<li><a href="#ticketscan">Ticket Scanning</a></li>
<li><a href="#seats">Seat Reservations</a></li>
<li><a href="#mapper">Mapper Usage and SQL Security</a></li>
<li><a href="#views">Views and controls.xml</a></li>
<li><a href="#errors">Error Handling</a></li>
<li><a href="#headers">Header and Redirect Security</a></li>
<li><a href="#mail">Mail Queue and Cron</a></li>
<li><a href="#wallet">Google Wallet</a></li>
<li><a href="#types">Application-Specific Types</a></li>
<li><a href="#new-screen">Designing a New Client Screen</a></li>
<li><a href="#migration">Migration from 1.0.31</a></li>
<li><a href="#testing">Testing</a></li>
<li><a href="#deployment">Deployment Checklist</a></li>
<li><a href="#summary">Architectural Summary</a></li>
</ol>
</section>

<h2 id="introduction">1. Introduction</h2>

<p>The <strong>MembersActivities Framework</strong> is an application framework for building web applications that manage members, activities, subscriptions, payments, tickets and related functionality.</p>

<p>It is built on top of the <strong>Controller Framework</strong> and provides reusable domain functionality while allowing each client application to define its own user interface, business rules, subscription validation, activity types, payment descriptions, authentication configuration, mail configuration and optional integrations.</p>

<p>Version <strong>1.1.0</strong> introduces persistent ticket entities and ticket scanning as first-class framework concepts.</p>

<div class="diagram">Client Application
       │
       ├── Client Commands
       ├── Client CommandDecorators
       ├── Client Views
       ├── Client Strategies
       ├── Client Ticket Types
       └── Client configuration
       │
       ▼
MembersActivities Framework 1.1.0
       │
       ▼
Controller Framework 1.0.31</div>

<p>The client application should normally <strong>extend and configure the framework rather than modify framework source code</strong>.</p>

<h2 id="release">2. What's New in Version 1.1.0</h2>

<p>MembersActivities Framework 1.1.0 introduces several changes compared with version 1.0.31.</p>

<h3>2.1 Persistent tickets</h3>

<p>Tickets are now represented by individual <code>Ticket</code> domain objects and stored in the <code>ticket</code> database table.</p>

<p>Previously, a subscription's <code>quantity</code> represented the number of tickets. In version 1.1.0, each ticket receives its own persistent identity and token.</p>

<div class="diagram">Subscription
   │
   ├── Ticket #152
   ├── Ticket #153
   └── Ticket #154</div>

<p>This makes it possible to individually identify, validate, cancel and scan tickets.</p>

<h3>2.2 Ticket scanning</h3>

<p>The new <code>TicketScan</code> model records ticket scan information and supports scan results such as:</p>

<pre><code>valid
already_used
cancelled
invalid
wrong_activity</code></pre>

<p>The framework also provides an atomic ticket-claim operation so that two scanners cannot successfully use the same ticket at the same time.</p>

<h3>2.3 Ticket scanning AccessToken</h3>

<p>Version 1.1.0 adds a dedicated <code>ticket_scan</code> AccessToken for an activity.</p>

<pre><code>$token = \controllerframework\security\AccessToken::generate(
    'ticket_scan',
    (string) $activityId
);</code></pre>

<p>This token can be used by a client ticket scanner to protect ticket-scanning operations for a specific activity.</p>

<h3>2.4 Seat reservation conflict detection</h3>

<p>When an activity uses a seat map, the framework now checks whether requested seats have been reserved between the time the seat map was displayed and the time the subscription is submitted.</p>

<p>This reduces the possibility of two users purchasing the same seat concurrently.</p>

<h3>2.5 Google Wallet uses real ticket IDs</h3>

<p>Google Wallet integration now uses persistent ticket records rather than generating ticket numbers from the subscription quantity.</p>

<p>Each Google Wallet object therefore corresponds to an actual ticket entity.</p>

<h3>2.6 Payment status handling</h3>

<p>The administrator payment edit command no longer updates the payment status through the generic update operation. Payment status transitions should be handled through the dedicated payment status mechanism, such as <code>statusReceived</code>.</p>

<h3>2.7 Database changes</h3>

<p>Version 1.1.0 adds:</p>

<ul>
<li><code>ticket</code> table;</li>
<li><code>ticket_scan</code> table;</li>
<li>a nullable <code>expires_at</code> in <code>remember_tokens</code>;</li>
<li>nullable <code>subject</code>, <code>body</code> and <code>recipient</code> fields in <code>mail_queue</code>.</li>
</ul>

<h2 id="requirements">3. Technology Requirements</h2>

<table>
<tr><th>Component</th><th>Requirement</th></tr>
<tr><td>PHP</td><td>8.3 or compatible PHP 8.x</td></tr>
<tr><td>Controller Framework</td><td>1.0.31</td></tr>
<tr><td>MembersActivities Framework</td><td>1.1.0</td></tr>
<tr><td>Database</td><td>MySQL / MariaDB</td></tr>
<tr><td>Database access</td><td>PDO</td></tr>
<tr><td>Web server</td><td>Apache or compatible PHP web server</td></tr>
<tr><td>Composer</td><td>Required</td></tr>
</table>

<p>The framework uses a MySQL/MariaDB PDO connection. Oracle is not a supported database platform for this release.</p>

<h2 id="installation">4. Installing the Framework</h2>

<p>A client application installs the framework through Composer:</p>

<pre><code>{
    "require": {
        "samoscon/membersactivities-framework": "^1.1.0"
    }
}</code></pre>

<p>MembersActivities Framework 1.1.0 requires:</p>

<pre><code>samoscon/controller-framework ^1.0.31</code></pre>

<p>Composer installs that dependency automatically.</p>

<pre><code>composer install</code></pre>

<p>Use <code>composer install</code> for deployments based on a committed <code>composer.lock</code>.</p>

<div class="note">
<strong>Upgrade note:</strong> version 1.1.0 introduces database structures for tickets and ticket scanning. Existing production applications should therefore perform a controlled database migration before enabling ticket functionality.
</div>

<h2 id="structure">5. Recommended Client Application Structure</h2>

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
│   │   ├── ajax/
│   │   ├── downloads/
│   │   └── mollie/
│   ├── model/
│   └── views/
│
└── assets/</code></pre>

<p>For ticket-enabled applications, client-specific ticket implementations may be added to the model directory:</p>

<pre><code>model/
├── Ticket.php
├── Ticket_RGLR.php
├── TicketMapper.php
├── TicketScan.php
├── TicketScan_RGLR.php
└── TicketScanMapper.php</code></pre>

<p>The exact directory structure may be adapted, but framework classes should remain in the Composer package and client-specific classes should remain in the client application.</p>

<h2 id="configuration">6. Application Configuration</h2>

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

<div class="note">
<strong>Security:</strong> values such as database passwords, Mollie credentials, Google credentials and especially <code>_SALTRAND</code> must remain secret. The configuration file must not be publicly downloadable.
</div>

<p><code>_SALTRAND</code> is used by the Controller Framework's <code>AccessToken</code> implementation. It should be a long, cryptographically random secret.</p>

<h2 id="database">7. Database</h2>

<p>The reference schema contains the following principal tables:</p>

<pre><code>member
activity
costitem
subscription
payment
ticket
ticket_scan
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
├── Payment
└── Tickets
├── Ticket
├── Ticket
└── Ticket

Ticket
└── Ticket Scans</div>

<h3>7.1 Ticket table</h3>

<p>The <code>ticket</code> table contains the persistent identity and state of each ticket.</p>

<table>
<tr><th>Field</th><th>Purpose</th></tr>
<tr><td><code>id</code></td><td>Unique ticket identifier.</td></tr>
<tr><td><code>description</code></td><td>Ticket description.</td></tr>
<tr><td><code>classification</code></td><td>Ticket type, defaulting to <code>RGLR</code>.</td></tr>
<tr><td><code>subscription_id</code></td><td>Subscription to which the ticket belongs.</td></tr>
<tr><td><code>token</code></td><td>Unique ticket token used for ticket identification.</td></tr>
<tr><td><code>status</code></td><td>Ticket state: <code>valid</code> or <code>cancelled</code>.</td></tr>
<tr><td><code>used</code></td><td>Indicates whether the ticket has already been claimed.</td></tr>
<tr><td><code>used_at</code></td><td>Date and time at which the ticket was claimed.</td></tr>
<tr><td><code>created_at</code></td><td>Ticket creation timestamp.</td></tr>
</table>

<h3>7.2 Ticket scan table</h3>

<p>The <code>ticket_scan</code> table records scans associated with a ticket.</p>

<table>
<tr><th>Field</th><th>Purpose</th></tr>
<tr><td><code>id</code></td><td>Unique scan identifier.</td></tr>
<tr><td><code>description</code></td><td>Scan description.</td></tr>
<tr><td><code>classification</code></td><td>Scanner type, defaulting to <code>RGLR</code>.</td></tr>
<tr><td><code>ticket_id</code></td><td>Ticket that was scanned.</td></tr>
<tr><td><code>scanner</code></td><td>Identifier of the scanner or scanning application.</td></tr>
<tr><td><code>scanned_at</code></td><td>Scan timestamp.</td></tr>
<tr><td><code>result</code></td><td>Result of the scan.</td></tr>
</table>

<h3>7.3 Remember tokens</h3>

<p><code>remember_tokens.expires_at</code> is now nullable:</p>

<pre><code>expires_at DATETIME DEFAULT NULL</code></pre>

<p>A client application should document how a null expiration value is interpreted by its authentication policy.</p>

<h3>7.4 Mail queue</h3>

<p>The following fields are nullable:</p>

<pre><code>subject
body
recipient</code></pre>

<p>This allows mail queue records to represent processing states where these values are not yet available or are supplied by another part of the mail-processing workflow.</p>

<div class="note">
<strong>Existing production database:</strong> do not replace it with the reference schema. Back it up and apply controlled schema changes or migrations.
</div>

<h2 id="mvc">8. MVC Application Flow</h2>

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

<h2 id="commands">9. Commands</h2>

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

<h2 id="decorators">10. CommandDecorator</h2>

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

<h2 id="validation">11. Subscription Validation Strategy</h2>

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

<div class="note">
<strong>Security rule:</strong> never instantiate a validator class directly from a request parameter. Use a controlled <code>CommandDecorator</code> to select the concrete strategy.
</div>

<h2 id="request">12. Request Object</h2>

<p>The Controller Framework's <code>Request</code> object transports information through the command chain.</p>

<pre><code>$request->get('id');

$request->set('validator', $validator);

$request->addFeedback('Something went wrong.');

$request->set(
    'forwardqueryparams',
    ['id' => $id]
);</code></pre>

<p>The Request object is a transport mechanism, <strong>not a trust boundary</strong>. Data originating from a browser must still be validated.</p>

<h2 id="csrf">13. CSRF Protection</h2>

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

<h2 id="sessions">14. Sessions</h2>

<p>CSRF protection requires an active session. Production applications should use secure session cookies with appropriate <code>Secure</code>, <code>HttpOnly</code> and <code>SameSite</code> settings and should use HTTPS.</p>

<h2 id="authentication">15. Authentication and Login Levels</h2>

<p>Commands define the required authentication level. Typical levels are:</p>

<pre><code>new \controllerframework\sessions\NoLoginRequired()
new \controllerframework\sessions\UserLogin()
new \controllerframework\sessions\AdminLogin()</code></pre>

<p>Administrative commands that expose or modify sensitive data should normally require <code>AdminLogin</code>.</p>

<h2 id="accesstoken">16. AccessToken</h2>

<p>Controller Framework 1.0.31 provides <code>\controllerframework\security\AccessToken</code> for protected public operations.</p>

<h3>16.1 Payment AccessToken</h3>

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

<h3>16.2 Ticket scanning AccessToken</h3>

<p>MembersActivities Framework 1.1.0 introduces a dedicated purpose for ticket scanning:</p>

<pre><code>$ticketScanToken =
    \controllerframework\security\AccessToken::generate(
        'ticket_scan',
        (string) $activityId
    );</code></pre>

<p>The generated token is returned by the activity administration command as:</p>

<pre><code>ticket_scantoken</code></pre>

<p>The token identifies the activity for which ticket scanning is authorized.</p>

<div class="note">
<strong>Security:</strong> ticket scanning tokens should only be transmitted over HTTPS. Do not place them in logs, publicly accessible documents or URLs where they can unnecessarily be disclosed.
</div>

<h2 id="payments">17. Payment Security</h2>

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

<p>In version 1.1.0, administrator payment editing should not use the generic update operation to change payment status. Payment status changes should go through the dedicated payment status mechanism.</p>

<h2 id="mollie">18. Mollie Integration</h2>

<p>Mollie integration is optional. Relevant commands include:</p>

<pre><code>PaymentToMollieCommand
OrderToMollieCommand
WebhookFromMollieCommand
PaymentConfirmationCommand</code></pre>

<p>The generic payment command can be decorated by the client application.</p>

<h3>18.1 Application-specific order descriptions</h3>

<p>The payment amount is obtained from the server-side <code>Payment</code> object. The order description can be supplied by the client-specific <code>CommandDecorator</code> through the <code>Request</code>:</p>

<pre><code>$request->set(
    'orderDescription',
    'Concert tickets - May 2027'
);</code></pre>

<p>The underlying <code>OrderToMollieCommand</code> reads this value. This allows different order types to have different descriptions without modifying the generic framework command.</p>

<h2 id="webhook">19. Mollie Webhook and Payment Confirmation</h2>

<p>The webhook is a machine-to-machine endpoint rather than a normal user interface. It should retrieve the current payment status from Mollie and update the corresponding server-side payment.</p>

<p>The customer redirect is not the authoritative source of payment status.</p>

<p>Payment confirmation should validate the protected order identifier and its <code>AccessToken</code> before displaying information associated with a payment.</p>

<p>Applications using tickets should create or update ticket state based on the confirmed server-side payment state rather than trusting client-side payment information.</p>

<h2 id="models">20. Models and the Composite Pattern</h2>

<p>Core domain models include:</p>

<pre><code>Activity
ActivityComposite
Costitem
Member
MemberComposite
Subscription
Payment
Ticket
TicketScan
GoogleWalletTicket</code></pre>

<p>The client application should use the model API where possible instead of directly manipulating database records.</p>

<h3>20.1 Composite Domain Objects</h3>

<p>The MembersActivities Framework uses the <strong>Composite design pattern</strong> for two important domain concepts: <strong>Activities</strong> and <strong>Members</strong>.</p>

<p>This allows a domain object to represent either an individual object or a parent containing child objects, while exposing a consistent object-oriented model to the client application.</p>

<div class="diagram">                 Composite
                    │
          ┌─────────┴─────────┐
          │                   │
       Activity             Member
          │                   │
      children             children
          │                   │
      Activity             Member
      Activity             Member
      Activity             Member</div>

<h3>20.2 Activity Composite</h3>

<p>An <code>Activity</code> can have child activities. This is represented by the <code>ActivityComposite</code> concept.</p>

<p>A parent activity can therefore act as a container for one or more child activities without requiring the client application to handle parent and child activities as fundamentally different objects.</p>

<div class="diagram">Activity
   │
   ├── Activity
   ├── Activity
   └── Activity</div>

<p>This is useful when an application wants to group related activities under a common parent.</p>

<p>For example, an application could model a concert series as a parent activity with individual concerts as child activities:</p>

<div class="diagram">2026 Concert Season
   │
   ├── Concert 1
   ├── Concert 2
   ├── Concert 3
   └── Concert 4</div>

<p>The exact interpretation of the parent-child relationship is application-specific. The framework provides the object model and persistence mechanism; the client application defines how the hierarchy is presented and used.</p>

<h3>20.3 Member Composite</h3>

<p>The same Composite principle is also used for <code>Member</code>.</p>

<p>A member can have child members. This allows a client application to represent relationships where one member acts as a parent of one or more other members.</p>

<div class="diagram">Member
   │
   ├── Member
   ├── Member
   └── Member</div>

<p>This can, for example, be used to model a household or another application-specific member hierarchy.</p>

<p>The child members remain normal <code>Member</code> objects. The parent-child relationship does not require the client application to introduce a separate domain concept for every level of the hierarchy.</p>

<h3>20.4 Common Composite principle</h3>

<p>The Activity and Member hierarchies follow the same object-oriented principle:</p>

<table>
<tr>
<th>Concept</th>
<th>Parent</th>
<th>Children</th>
</tr>
<tr>
<td>Activity</td>
<td>Parent Activity</td>
<td>Child Activities</td>
</tr>
<tr>
<td>Member</td>
<td>Parent Member</td>
<td>Child Members</td>
</tr>
</table>

<p>Conceptually, both can be represented as:</p>

<div class="diagram">                 Domain Object
                       │
             ┌─────────┴─────────┐
             │                   │
           Parent              Child
             │                   │
             └───────┬───────────┘
                     │
               same domain type</div>

<p>This is an application of the <strong>Composite Object-Oriented Design Pattern</strong>: a parent object and its child objects belong to the same domain type and can therefore be treated consistently by application code.</p>

<div class="note">
<strong>Client application principle:</strong> do not assume that every Activity or Member is a leaf object. When implementing business logic, views or navigation, always consider that an Activity or Member may participate in a parent-child hierarchy.
</div>

<h3>20.5 Working with hierarchies</h3>

<p>Client applications should use the framework's model relationships rather than reproducing parent-child logic in controllers or views.</p>

<p>For Activities, this means that application code should take the <code>ActivityComposite</code> relationship into account when displaying or processing activities.</p>

<p>For Members, the same principle applies to the Member hierarchy.</p>

<p>The hierarchy can be visualized as:</p>

<div class="diagram">Activity hierarchy

Parent Activity
│
├── Child Activity
│      ├── Child Activity
│      └── Child Activity
│
└── Child Activity

Member hierarchy

Parent Member
│
├── Child Member
│      ├── Child Member
│      └── Child Member
│
└── Child Member</div>

<p>Because the relationship is recursive, more than one level of nesting can be represented where the application's business rules require it.</p>

<h3>20.6 Why the Composite pattern is useful</h3>

<ul>
<li>Parent and child objects share the same domain model.</li>
<li>Client code can work with individual objects and groups using a consistent abstraction.</li>
<li>Hierarchical structures can be represented without introducing a separate object type for every hierarchy level.</li>
<li>Business rules can be applied consistently to both parent and child objects.</li>
<li>The framework can support recursive structures without requiring client applications to duplicate the hierarchy implementation.</li>
</ul>

<h2 id="tickets">21. Tickets</h2>

<p>Version 1.1.0 introduces <code>Ticket</code> as a first-class domain object.</p>

<p>The framework provides:</p>

<pre><code>\membersactivities\model\subscriptions\Ticket
\membersactivities\model\subscriptions\TicketMapper
\membersactivities\model\subscriptions\TicketTypeImplementation</code></pre>

<p>These classes are framework components. A client application can provide concrete implementations where application-specific ticket behaviour is required.</p>

<h3>21.1 Ticket lifecycle</h3>

<div class="diagram">Ticket created
      │
      ▼
   VALID
      │
      ├──────────────┐
      │              │
      ▼              ▼
  scanned         cancelled
      │
      ▼
   USED</div>

<p>The database represents the active state using <code>status</code> and <code>used</code>.</p>

<table>
<tr><th>State</th><th>Meaning</th></tr>
<tr><td><code>status = valid</code>, <code>used = 0</code></td><td>Ticket can be used.</td></tr>
<tr><td><code>status = valid</code>, <code>used = 1</code></td><td>Ticket has already been used.</td></tr>
<tr><td><code>status = cancelled</code></td><td>Ticket has been cancelled and must not be accepted.</td></tr>
</table>

<h3>21.2 Ticket token</h3>

<p>Every ticket has a unique token stored in the <code>token</code> field.</p>

<p>The framework provides:</p>

<pre><code>$ticket = \model\Ticket::findByToken($token);</code></pre>

<p>This allows a scanner to identify a ticket without exposing or relying on internal database IDs.</p>

<h3>21.3 Atomic ticket claiming</h3>

<p>The framework provides:</p>

<pre><code>$success = $ticket->claim();</code></pre>

<p>The operation is atomic. Internally it only changes the ticket when:</p>

<pre><code>status = 'valid'
AND
used = 0</code></pre>

<p>This is important for ticket scanning because two scanners may attempt to validate the same ticket almost simultaneously.</p>

<p>Only the scanner that successfully performs the atomic claim should treat the ticket as newly accepted.</p>

<h3>21.4 Ticket type implementation</h3>

<p><code>TicketTypeImplementation</code> provides an extension point for application-specific ticket types.</p>

<p>The framework uses the ticket <code>classification</code> field to select the corresponding implementation.</p>

<p>The default classification is:</p>

<pre><code>RGLR</code></pre>

<p>A client application may therefore provide a corresponding regular ticket implementation where required.</p>

<h2 id="ticketscan">22. Ticket Scanning</h2>

<p>Version 1.1.0 introduces the <code>TicketScan</code> domain model.</p>

<p>The framework provides:</p>

<pre><code>\membersactivities\model\subscriptions\TicketScan
\membersactivities\model\subscriptions\TicketScanMapper
\membersactivities\model\subscriptions\TicketScanTypeImplementation</code></pre>

<p>As with <code>Ticket</code>, these are framework components that can be specialized by the client application.</p>

<h3>22.1 Scan results</h3>

<p>The database supports the following scan results:</p>

<pre><code>valid
already_used
cancelled
invalid
wrong_activity</code></pre>

<p>The client scanner should distinguish between these results so that the operator can understand why a ticket was accepted or rejected.</p>

<h3>22.2 Recommended scanning flow</h3>

<div class="diagram">Scanner
   │
   │ ticket token
   ▼
Server
   │
   ├── validate ticket_scan AccessToken
   ├── find Ticket by token
   ├── verify activity
   ├── verify ticket status
   ├── atomically claim ticket
   │
   ├── success ───────► valid
   │
   └── failure ───────► already_used / cancelled / invalid
                              │
                              ▼
                         TicketScan record</div>

<p>The exact user interface is application-specific, but the server should remain authoritative for ticket validation.</p>

<h3>22.3 Recording scans</h3>

<p>A client application can create a <code>TicketScan</code> record containing information such as:</p>

<pre><code>ticket_id
scanner
result
scanned_at</code></pre>

<p>This provides an audit trail of ticket scanning activity.</p>

<h3>22.4 TicketScan type implementation</h3>

<p><code>TicketScanTypeImplementation</code> provides an extension point for different scanner implementations.</p>

<p>The default classification is:</p>

<pre><code>RGLR</code></pre>

<p>A client application may therefore provide a corresponding regular scan implementation where required.</p>

<h2 id="seats">23. Seat Reservations</h2>

<p>Version 1.1.0 adds server-side conflict detection for seat reservations.</p>

<p>When an activity uses a seat map, the framework checks requested seats against reservations that already exist for the relevant cost item.</p>

<div class="diagram">User opens activity
       │
       ▼
Seat map displayed
       │
       ▼
User selects seats
       │
       ▼
User submits subscription
       │
       ▼
Server checks current reservations
       │
       ├── seats available
       │       │
       │       ▼
       │   create subscription
       │
       └── seat already reserved
               │
               ▼
          return error
          ask user to retry</div>

<p>This check is important because the seat map shown to the user may become outdated while another user is completing a purchase.</p>

<div class="note">
<strong>Important:</strong> a client application must not assume that a seat remains available merely because it was available when the seat map was initially displayed. The server-side validation is authoritative.
</div>

<h2 id="mapper">24. Mapper Usage and SQL Security</h2>

<p>The framework uses mapper classes such as <code>ActivityMapper</code>, <code>CostitemMapper</code>, <code>PaymentMapper</code>, <code>SubscriptionMapper</code>, <code>TicketMapper</code> and <code>TicketScanMapper</code>.</p>

<p>For example:</p>

<pre><code>$payment = \model\Payment::find($id);

$ticket = \model\Ticket::findByToken($token);</code></pre>

<h3>24.1 TicketMapper</h3>

<p><code>TicketMapper</code> provides operations including:</p>

<pre><code>claimTicket(int $ticketId): bool

findByToken(string $token): ?\model\Ticket</code></pre>

<p><code>claimTicket()</code> uses a database update with conditions on both the ticket status and used flag. This provides atomic protection against simultaneous scans.</p>

<h3>24.2 Free SQL clauses</h3>

<p>Special care is required with <code>Mapper::findAll()</code>, because it accepts a free SQL select clause.</p>

<pre><code>$mapper->findAll($selectClause);</code></pre>

<div class="note">
<strong>SQL security:</strong> never concatenate untrusted user input into a free SQL clause. Validate and whitelist values before they are incorporated into SQL, and prefer the framework's model/database mechanisms where possible.
</div>

<h2 id="views">25. Views and controls.xml</h2>

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

<h2 id="errors">26. Error Handling</h2>

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

<h2 id="headers">27. Header and Redirect Security</h2>

<p>Take special care with values used in:</p>

<ul>
<li><code>Location</code> headers;</li>
<li><code>Content-Disposition</code>;</li>
<li>file names;</li>
<li>redirect URLs;</li>
<li>calendar/ICS file names.</li>
</ul>

<p>Validate or whitelist such values before placing them in HTTP headers. When constructing query strings, prefer <code>http_build_query()</code> to manual concatenation.</p>

<h2 id="mail">28. Mail Queue and Cron</h2>

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

<h2 id="wallet">29. Google Wallet</h2>

<p>Google Wallet integration is optional. Applications using it require appropriate Google credentials, an issuer ID and application configuration.</p>

<p>Version 1.1.0 changes the relationship between subscriptions and Google Wallet tickets.</p>

<h3>29.1 Version 1.0.31 behaviour</h3>

<p>Previously, the framework generated Google Wallet ticket objects based on the subscription quantity.</p>

<h3>29.2 Version 1.1.0 behaviour</h3>

<p>The framework now obtains the actual ticket records and creates a Wallet object for each persistent ticket.</p>

<div class="diagram">Subscription
     │
     ├── Ticket ID 152 ──► Google Wallet Object 152
     │
     ├── Ticket ID 153 ──► Google Wallet Object 153
     │
     └── Ticket ID 154 ──► Google Wallet Object 154</div>

<p>The ticket ID is used as the ticket number/object identifier.</p>

<div class="note">
<strong>Migration consideration:</strong> applications upgrading from 1.0.31 should determine how existing subscriptions are converted into individual Ticket records before relying on the new Google Wallet behaviour.
</div>

<h2 id="types">30. Application-Specific Types</h2>

<p>The framework provides extension points such as:</p>

<pre><code>ActivityTypeImplementation
CostitemTypeImplementation
PaymentTypeImplementation
SubscriptionTypeImplementation
TicketTypeImplementation
TicketScanTypeImplementation</code></pre>

<p>Client-specific business rules should be implemented in these extension points where appropriate rather than accumulating all business logic in controllers.</p>

<h2 id="new-screen">31. Designing a New Client Screen</h2>

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

<h2 id="migration">32. Migration from 1.0.31</h2>

<p>Version 1.1.0 introduces database and application-level changes. Existing applications should therefore be upgraded in a controlled sequence.</p>

<h3>32.1 Update Composer</h3>

<pre><code>{
    "require": {
        "samoscon/membersactivities-framework": "^1.1.0"
    }
}</code></pre>

<p>The Controller Framework remains at version 1.0.31.</p>

<h3>32.2 Update the database</h3>

<p>Apply the new definitions from <code>example/DatabaseSetup.sql</code> for:</p>

<ul>
<li><code>ticket</code>;</li>
<li><code>ticket_scan</code>;</li>
<li><code>remember_tokens.expires_at</code>;</li>
<li>nullable mail queue fields.</li>
</ul>

<h3>32.3 Create tickets for existing subscriptions</h3>

<p>Existing subscriptions previously represented multiple tickets through the <code>quantity</code> field.</p>

<p>For applications that use tickets, each relevant subscription should now have corresponding <code>ticket</code> records.</p>

<div class="diagram">Before 1.1.0

Subscription
│
└── quantity = 3

After 1.1.0

Subscription
│
├── Ticket
├── Ticket
└── Ticket</div>

<p>The migration process must generate unique ticket tokens and preserve any application-specific information required by the client.</p>

<h3>32.4 Update client models</h3>

<p>Applications using the ticket functionality should provide concrete implementations of the new framework models and their mappers where required.</p>

<h3>32.5 Update Google Wallet</h3>

<p>Applications using Google Wallet should verify that existing subscriptions have corresponding ticket records before generating new Wallet objects.</p>

<h3>32.6 Update ticket scanning</h3>

<p>Applications introducing ticket scanning should:</p>

<ul>
<li>generate the activity-specific <code>ticket_scan</code> token;</li>
<li>protect the scanner endpoint;</li>
<li>validate ticket tokens server-side;</li>
<li>use the atomic ticket claim operation;</li>
<li>record scan results where an audit trail is required.</li>
</ul>

<div class="note">
<strong>Important:</strong> the framework does not automatically convert every historical subscription into individual tickets. The migration strategy is application-specific because existing applications may have different ticket, seat and payment requirements.
</div>

<h2 id="testing">33. Testing a New Release</h2>

<h3>Public functionality</h3>

<ul>
<li>Home page</li>
<li>Activity display</li>
<li>Activity details</li>
<li>Public registration</li>
<li>Seat selection</li>
<li>Payment initiation</li>
<li>Payment confirmation</li>
</ul>

<h3>Member functionality</h3>

<ul>
<li>Login</li>
<li>Logout</li>
<li>Member activity</li>
<li>Subscription</li>
<li>Payment</li>
<li>Remember-me login</li>
</ul>

<h3>Composite functionality</h3>

<ul>
<li>Parent Activity</li>
<li>Child Activities</li>
<li>Nested Activity hierarchies where applicable</li>
<li>Parent Member</li>
<li>Child Members</li>
<li>Nested Member hierarchies where applicable</li>
<li>Correct handling of parent and child objects in views and business logic</li>
</ul>

<h3>Ticket functionality</h3>

<ul>
<li>Ticket creation</li>
<li>Unique ticket token generation</li>
<li>Ticket lookup by token</li>
<li>Valid ticket scanning</li>
<li>Already-used ticket</li>
<li>Cancelled ticket</li>
<li>Invalid ticket</li>
<li>Ticket belonging to another activity</li>
<li>Concurrent scans of the same ticket</li>
<li>Ticket scan logging</li>
</ul>

<h3>Seat reservation functionality</h3>

<ul>
<li>Reserve available seat</li>
<li>Reserve multiple seats</li>
<li>Attempt to reserve an already reserved seat</li>
<li>Concurrent reservation attempts</li>
<li>Retry after seat conflict</li>
</ul>

<h3>Administrator functionality</h3>

<ul>
<li>Activity creation/editing</li>
<li>Cost item management</li>
<li>Member management</li>
<li>Payment management</li>
<li>Reports and Excel exports</li>
<li>Ticket management where implemented</li>
</ul>

<h3>Integration functionality</h3>

<ul>
<li>Mollie payment</li>
<li>Mollie webhook</li>
<li>Google Wallet</li>
<li>Mail queue</li>
<li>Cron jobs</li>
<li>Ticket scanner</li>
</ul>

<h3>Security</h3>

<ul>
<li>Invalid IDs</li>
<li>Invalid CSRF tokens</li>
<li>Missing authentication</li>
<li>Unauthorized administrative access</li>
<li>Invalid payment AccessTokens</li>
<li>Invalid ticket scan AccessTokens</li>
<li>Modified payment identifiers</li>
<li>Modified payment amounts</li>
<li>Invalid ticket tokens</li>
<li>Attempt to reuse a ticket</li>
<li>Attempt to scan a ticket for another activity</li>
<li>Unexpected input</li>
</ul>

<h2 id="deployment">34. Deployment Checklist</h2>

<ul class="checklist">
<li>PHP version verified</li>
<li>Composer dependencies installed</li>
<li>Controller Framework 1.0.31 installed</li>
<li>MembersActivities Framework 1.1.0 installed</li>
<li>Database configured</li>
<li>Database backup available</li>
<li><code>ticket</code> table created</li>
<li><code>ticket_scan</code> table created</li>
<li>Existing subscriptions migrated where required</li>
<li>Application ticket implementations deployed</li>
<li><code>app_options.ini</code> protected</li>
<li><code>_SALTRAND</code> generated and secret</li>
<li>Production Mollie credentials configured</li>
<li>Google credentials protected</li>
<li>HTTPS enabled</li>
<li>Session security configured</li>
<li>CSRF protection tested</li>
<li>Administrative commands protected</li>
<li>Payment AccessToken flows tested</li>
<li>Ticket scan AccessToken tested</li>
<li>Payment amount verified as server-side data</li>
<li>Mollie webhook tested</li>
<li>Seat reservation conflict detection tested</li>
<li>Ticket lookup by token tested</li>
<li>Atomic ticket claiming tested</li>
<li>Ticket scan logging tested</li>
<li>Google Wallet ticket generation tested</li>
<li>Mail queue tested</li>
<li>Cron jobs configured with absolute paths</li>
<li>Error logging configured</li>
<li>Debug output disabled</li>
<li>Public, user and administrator routes tested</li>
<li>Downloads tested</li>
</ul>

<h2 id="summary">35. Architectural Summary</h2>

<p>MembersActivities Framework 1.1.0 is intended to be used as a reusable domain framework rather than as a complete application.</p>

<div class="diagram">┌────────────────────────────────────────────┐
│             Client Application             │
│                                            │
│ Commands / Decorators / Views              │
│ Strategies / Business Rules               │
│ Ticket Types / Scanner Types               │
│ Configuration / Integrations               │
└──────────────────────┬─────────────────────┘
                       │
┌──────────────────────▼─────────────────────┐
│      MembersActivities Framework 1.1.0     │
│                                            │
│ Members / Activities / Subscriptions       │
│ Payments / Tickets / Ticket Scans          │
│ Seat Reservations / Mail / Mollie / Wallet │
└──────────────────────┬─────────────────────┘
                       │
┌──────────────────────▼─────────────────────┐
│        Controller Framework 1.0.31         │
│                                            │
│ MVC / Commands / Request / Sessions        │
│ Security / CSRF / AccessToken / PDO       │
│ Error handling / Audit / Rendering         │
└────────────────────────────────────────────┘</div>

<h3>35.1 Domain relationships</h3>

<div class="diagram">Activity
   │
   ├── Child Activity
   │      └── Child Activity
   │
   └── Child Activity

Member
│
├── Child Member
│      └── Child Member
│
└── Child Member

Activity
│
└── CostItem
│
└── Subscription
│
├── Payment
│
└── Ticket
│
└── TicketScan</div>

<p>Both <code>Activity</code> and <code>Member</code> can participate in recursive parent-child relationships following the Composite design pattern.</p>

<p>The ticket model introduces another important domain relationship: a subscription can contain multiple persistent tickets, and each ticket can have multiple scan records.</p>

<p>The key development principle remains:</p>

<div class="note">
<strong>Keep generic functionality in the framework and implement client-specific behaviour through decorators, strategies, models, commands, configuration and views.</strong>
</div>

<p>Version 1.1.0 extends this principle to tickets and ticket scanning while retaining the existing Composite-based domain model for Activities and Members.</p>

<p>This makes client applications easier to maintain and allows framework upgrades without unnecessarily modifying application code.</p>

<footer>
  <p><strong>Reference version:</strong> MembersActivities Framework 1.1.0 · Controller Framework 1.0.31 · PHP 8.3 · MySQL/MariaDB</p>
  <p><strong>Previous framework version:</strong> MembersActivities Framework 1.0.31</p>
  <p>Client applications should verify the exact framework versions in <code>composer.json</code> and <code>composer.lock</code> before applying this guide to an existing installation.</p>
</footer>

</div>
</body>
</html>
