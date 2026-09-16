


<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>MembersActivities Framework 1.0.30 — Basic Example Guide</title>
<style>
:root{--ink:#202124;--muted:#5f6368;--accent:#1a73e8;--soft:#f6f8fa;--border:#dadce0;--note:#fff8e1}
*{box-sizing:border-box}html{scroll-behavior:smooth}
body{margin:0;color:var(--ink);font-family:-apple-system,BlinkMacSystemFont,"Segoe UI",Roboto,Arial,sans-serif;line-height:1.65}
.container{max-width:1080px;margin:auto;padding:40px 30px 80px}
header{border-bottom:1px solid var(--border);padding-bottom:30px;margin-bottom:35px}
h1{font-size:2.4rem;line-height:1.15;margin:0 0 8px}h2{font-size:1.65rem;margin-top:48px;border-bottom:1px solid var(--border);padding-bottom:8px}h3{font-size:1.2rem;margin-top:30px}
.subtitle{font-size:1.15rem;color:var(--muted)}
.meta{display:grid;grid-template-columns:repeat(auto-fit,minmax(210px,1fr));gap:10px;margin-top:22px}.meta div{background:#e8f0fe;padding:11px 14px;border-radius:6px}.meta strong{display:block}
.toc{background:#fafafa;border:1px solid var(--border);border-radius:6px;padding:20px 25px}.toc ol{margin-bottom:0}
pre{background:var(--soft);border:1px solid var(--border);border-radius:6px;padding:16px;overflow:auto;line-height:1.45}
code{font-family:"SFMono-Regular",Consolas,"Liberation Mono",monospace;font-size:.9em}:not(pre)>code{background:var(--soft);padding:2px 5px;border-radius:4px}
.note{background:var(--note);border-left:4px solid #f9ab00;padding:13px 17px;margin:20px 0}
.diagram{background:#fafafa;border:1px solid var(--border);border-radius:6px;padding:18px;white-space:pre;overflow:auto;font-family:monospace;line-height:1.45}
table{width:100%;border-collapse:collapse;margin:18px 0 28px}th,td{border:1px solid var(--border);padding:8px 10px;text-align:left;vertical-align:top}th{background:var(--soft)}
.step{border-left:3px solid var(--accent);padding-left:18px;margin:24px 0}
footer{margin-top:60px;border-top:1px solid var(--border);padding-top:20px;color:var(--muted);font-size:.9rem}
@media print{.container{max-width:none;padding:15px}h2{break-before:page}pre,.diagram,table{break-inside:avoid}}

.downloads {
  margin: 1.5rem 0;
  padding: 1rem 1.25rem;
  border-left: 4px solid #666;
  background: #f5f5f5;
}
.downloads h3 {
  margin-top: 0;
}
.downloads p:last-child {
  margin-bottom: 0;
}
</style>
</head>
<body>
<div class="container">

<header>
<h1>MembersActivities Framework 1.0.30</h1>
<div class="subtitle">Basic Example Application Guide</div>
<div class="meta">
<div><strong>Framework</strong>MembersActivities 1.0.30</div>
<div><strong>Controller Framework</strong>1.0.31</div>
<div><strong>Purpose</strong>Learning and starting-point application</div>
<div><strong>Database</strong>MySQL / MariaDB</div>
</div>
</header>

<section class="toc">
<h2 style="margin-top:0;border:0">Contents</h2>
<ol>
<li><a href="#purpose">Purpose of the Example</a></li>
<li><a href="#structure">Example Folder Structure</a></li>
<li><a href="#requirements">Prerequisites</a></li>
<li><a href="#copy">Create the Application</a></li>
<li><a href="#composer">Install Dependencies</a></li>
<li><a href="#database">Create the Database</a></li>
<li><a href="#config">Configure the Application</a></li>
<li><a href="#index">Understand index.php</a></li>
<li><a href="#autoload">Understand Client Autoloading</a></li>
<li><a href="#controls">Understand controls.xml</a></li>
<li><a href="#default">The Default Command</a></li>
<li><a href="#models">Create Client Model Classes</a></li>
<li><a href="#types">Activity and Costitem Types</a></li>
<li><a href="#validation">Subscription Validation Strategies</a></li>
<li><a href="#decorator">CommandDecorator Example</a></li>
<li><a href="#activity">Public Activity Example</a></li>
<li><a href="#payment">Payment Example</a></li>
<li><a href="#mollie">Mollie Example</a></li>
<li><a href="#admin">Administration Example</a></li>
<li><a href="#run">Run and Test the Example</a></li>
<li><a href="#customize">How to Turn the Example into Your Application</a></li>
<li><a href="#checklist">Basic Implementation Checklist</a></li>
</ol>
</section>

<h2 id="purpose">1. Purpose of the Example</h2>
<p>The <code>example</code> directory included in MembersActivities Framework 1.0.30 is a complete starting-point client application. It demonstrates how the framework is intended to be integrated rather than merely showing isolated API calls.</p>
<p>The example contains:</p>
<ul>
<li>Composer configuration;</li>
<li>application configuration;</li>
<li>database setup;</li>
<li>client model specializations;</li>
<li>client type implementations;</li>
<li>subscription validation strategies;</li>
<li>CommandDecorators;</li>
<li>public and user activity flows;</li>
<li>payment and Mollie integration;</li>
<li>administration commands;</li>
<li>views;</li>
<li>mail queue cleanup;</li>
<li>PDF ticket generation;</li>
<li>optional Google Wallet integration;</li>
<li>seat-map support.</li>
</ul>
<div class="note"><strong>Important:</strong> the example is intentionally application-specific. It contains sample values, sample texts and example business rules. Replace those with the rules and configuration of your own application.</div>

<div class="downloads">
  <h3>Example Files</h3>
  <p>
    The complete example application is available as a ZIP archive, including the
    folder structure and all underlying example files:
    <a href="#">Download the Example Folder Structure and Files (ZIP)</a>.
  </p>
  <p>
    The database setup is provided separately and is <strong>not included in the ZIP archive</strong>:
    <a href="#">Download DatabaseSetup.sql</a>.
  </p>
</div>

<h2 id="structure">2. Example Folder Structure</h2>
<pre><code>example/
│
├── index.php
├── composer.json
├── DatabaseSetup.sql
├── .htaccess
│
├── config/
│   ├── app_options.ini
│   └── [Your google wallet keyfile].json
│
├── MVCFramework/
│   ├── controls.xml
│   │
│   ├── commands/
│   │   ├── DefaultCommand.php
│   │   ├── admin/
│   │   ├── ajax/
│   │   ├── cron/
│   │   ├── downloads/
│   │   ├── mollie/
│   │   └── user/
│   │
│   ├── model/
│   │   ├── Activity.php
│   │   ├── ActivityMapper.php
│   │   ├── Activity_RGLR.php
│   │   ├── Activity_STMP.php
│   │   ├── Costitem.php
│   │   ├── CostitemMapper.php
│   │   ├── Member.php
│   │   ├── MemberMapper.php
│   │   ├── Member_RGLR.php
│   │   ├── Member_PRTN.php
│   │   ├── Payment.php
│   │   ├── PaymentMapper.php
│   │   ├── Payment_RGLR.php
│   │   ├── Payment_YRLY.php
│   │   ├── Subscription.php
│   │   ├── SubscriptionMapper.php
│   │   ├── Subscription_RGLR.php
│   │   └── SubscriptionValidation*.php
│   │
│   └── views/
│       ├── defaultView.php
│       ├── errorView.php
│       ├── admin/
│       ├── login/
│       └── user/
│
└── assets/
    ├── logging/
    ├── qrcodes/
    └── ...</code></pre>

<h2 id="requirements">3. Prerequisites</h2>
<p>Before running the example, install:</p>
<ul>
<li>PHP 8.3 or compatible PHP 8.x;</li>
<li>Composer;</li>
<li>MySQL or MariaDB;</li>
<li>a PHP-compatible web server;</li>
<li>HTTPS for production use.</li>
</ul>
<p>The example's Composer configuration requires MembersActivities Framework 1.0.30. The framework in turn uses Controller Framework 1.0.31.</p>

<h2 id="copy">4. Create the Application</h2>
<div class="step"><strong>Step 1.</strong> Copy the complete <code>example</code> directory to a working application directory.</div>
<div class="step"><strong>Step 2.</strong> Rename the directory if desired.</div>
<div class="step"><strong>Step 3.</strong> Do not publish the configuration directory or credential files directly as downloadable web resources.</div>
<div class="step"><strong>Step 4.</strong> Keep the application's framework-specific classes below <code>MVCFramework/</code>.</div>
<p>A suitable starting point is to keep the example structure intact until the application is understood, then progressively replace the example's client-specific code.</p>

<h2 id="composer">5. Install Dependencies</h2>
<p>The example's <code>composer.json</code> contains:</p>
<pre><code>{
    "require": {
        "setasign/fpdf": "^1.8",
        "blueimp/jquery-file-upload": "9.22.*",
        "tinymce/tinymce": "^8.0",
        "chillerlan/php-qrcode": "*",
        "samoscon/membersactivities-framework": "1.0.30",
        "google/auth": "^1.53",
        "guzzlehttp/guzzle": "^7.10",
        "google/apiclient": "^2.19",
        "google/apiclient-services": "~0.350"
    }
}</code></pre>
<p>Run:</p>
<pre><code>composer install</code></pre>
<p>The result is a <code>vendor/</code> directory containing the framework and third-party dependencies.</p>

<h2 id="database">6. Create the Database</h2>
<p>The example provides <code>DatabaseSetup.sql</code>. Import it into a new MySQL/MariaDB database.</p>
<pre><code>CREATE DATABASE your_database
    CHARACTER SET utf8mb4
    COLLATE utf8mb4_unicode_ci;</code></pre>
<p>Then import the supplied SQL script using your preferred database administration tool.</p>
<div class="note"><strong>For an existing application:</strong> do not simply import the reference schema over a production database. Back up the database and apply controlled schema changes.</div>

<h2 id="config">7. Configure the Application</h2>
<p>Edit <code>config/app_options.ini</code>. The example contains placeholders such as:</p>
<pre><code>_APPDIR=https://[(sub)domeinnaam]/
_HOMEPAGE=https://[your client homepage]
_ASSETDIR=https://[(sub)domeinnaam]/assets/

_SALTRAND=[long random secret]
_RAND=[random integer]

_DBUSER=[database username]
_DBPASSWORD="[database password]"
_DBNAME=[database name]
_DBHOST=localhost</code></pre>
<h3>7.1 Application identity</h3>
<pre><code>APP=BM_test
_LOGO=assets/[Your logo].jpg</code></pre>
<p>Replace these with the identity of your own application.</p>
<h3>7.2 Login policy</h3>
<pre><code>_MINLEVELTOLOGIN='A'</code></pre>
<p>The example uses <code>A</code> to indicate an administrator-only login environment. The alternative <code>U</code> allows administrators and members to log in as users.</p>
<h3>7.3 Payment policy</h3>
<pre><code>_WTALLOWED='Y'</code></pre>
<p>This controls whether wire transfer is available as an alternative to online payment.</p>
<h3>7.4 Mollie</h3>
<pre><code>_MOLLIECONFIG="test_[your mollie key]"</code></pre>
<p>Use a test key during development and a live key only in a properly secured production configuration.</p>
<h3>7.5 Google Wallet</h3>
<pre><code>_WALLETORIGIN=...
_WALLETCREDENTIALS=config/[your keyfile].json
_WALLETISSUERID=...</code></pre>
<p>Google Wallet is optional. If it is not used, the corresponding configuration can remain disabled/empty according to the application's conventions.</p>

<h2 id="index">8. Understand index.php</h2>
<p>The example's entry point is deliberately small:</p>
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

try {
    controllerframework\controllers\Controller::run();
} catch (\Throwable $e) {
    http_response_code(500);

    echo '&lt;h1&gt;Application initialization failed&lt;/h1&gt;';
    echo '&lt;p&gt;' .
         htmlspecialchars(
             $e-&gt;getMessage(),
             ENT_QUOTES,
             'UTF-8'
         ) .
         '&lt;/p&gt;';
    exit;
}</code></pre>
<p>The sequence is:</p>
<div class="diagram">index.php
   │
   ├── Composer autoloader
   │
   ├── client application autoloader
   │
   └── Controller::run()
              │
              ▼
        framework request processing</div>

<h2 id="autoload">9. Understand Client Autoloading</h2>
<p>The example automatically maps namespaced client classes below <code>MVCFramework/</code>.</p>
<p>For example:</p>
<pre><code>\model\Activity_RGLR</code></pre>
<p>maps to:</p>
<pre><code>MVCFramework/model/Activity_RGLR.php</code></pre>
<p>and:</p>
<pre><code>\commands\user\PublicActivityCommand</code></pre>
<p>maps to:</p>
<pre><code>MVCFramework/commands/user/PublicActivityCommand.php</code></pre>
<p>This makes it possible for the client application to provide concrete classes expected by the abstract framework classes.</p>

<h2 id="controls">10. Understand controls.xml</h2>
<p><code>MVCFramework/controls.xml</code> connects URL paths to commands and views.</p>
<p>The root route is:</p>
<pre><code>&lt;command path="/" class="\commands\DefaultCommand"&gt;
    &lt;view name="/defaultView" /&gt;
    ...
&lt;/command&gt;</code></pre>
<p>The activity route is:</p>
<pre><code>&lt;command path="/activity"
         class="\commands\user\PublicActivityCommand"&gt;
    &lt;view name="/user/activity" /&gt;
    ...
&lt;/command&gt;</code></pre>
<p>The payment flow contains:</p>
<pre><code>/createPayment
        │
        ▼
/paymentToMollie
        │
        ▼
Mollie

Mollie webhook
        │
        ▼
/webhookFromMollie

Payment confirmation
        │
        ▼
/paymentConfirmation</code></pre>

<h2 id="default">11. The Default Command</h2>
<p>The example's <code>DefaultCommand</code> is a client CommandDecorator around the framework's default command.</p>
<pre><code>class DefaultCommand
    extends \controllerframework\controllers\CommandDecorator
{
    public function doExecuteDecorator(
        \controllerframework\registry\Request $request
    ): ?int {
        $this-&gt;addResponses($request, [
            'title' =&gt; 'Inloggen'
        ]);
        return null;
    }

    public function initCommand(): void {
        $this-&gt;setCommand(
            new \membersactivities\commands\DefaultCommand()
        );
    }

    protected function getLevelOfLoginRequired(): void {
        $this-&gt;setLoginLevel(
            new \controllerframework\sessions\NoLoginRequired()
        );
    }
}</code></pre>
<p>The client changes only what it needs: here, the page title.</p>

<h2 id="models">12. Create Client Model Classes</h2>
<p>The example supplies concrete client classes for the abstract MembersActivities models.</p>
<h3>Activity</h3>
<pre><code>namespace model;

class Activity
    extends \membersactivities\model\activities\Activity
{
    // client-specific code
}</code></pre>
<h3>Costitem</h3>
<pre><code>namespace model;

class Costitem
    extends \membersactivities\model\activities\Costitem
{
    // client-specific code
}</code></pre>
<h3>Subscription</h3>
<pre><code>namespace model;

class Subscription
    extends \membersactivities\model\subscriptions\Subscription
{
    // client-specific code
}</code></pre>
<h3>Payment</h3>
<pre><code>namespace model;

class Payment
    extends \membersactivities\model\subscriptions\Payment
{
    // client-specific code
}</code></pre>
<p>Most of these classes are intentionally almost empty. Their existence allows the framework's abstract model architecture to be specialized by the client.</p>

<h2 id="types">13. Activity, Member and Costitem Types</h2>
<p>The example demonstrates classification-specific type implementations.</p>
<h3>Activity_RGLR</h3>
<pre><code>class Activity_RGLR
    extends \membersactivities\model\activities\ActivityTypeImplementation
{
}</code></pre>
<p>This represents a normal activity without additional rules.</p>
<h3>Activity_STMP</h3>
<pre><code>class Activity_STMP
    extends \membersactivities\model\activities\ActivityTypeImplementation
{
    public function seatmap(): bool {
        return true;
    }
}</code></pre>
<p>This type tells the activity command/view that a seat map should be used.</p>
<h3>Costitem_RGLR</h3>
<pre><code>class Costitem_RGLR
    extends \membersactivities\model\activities\CostitemTypeImplementation
{
}</code></pre>
<h3>Member types</h3>
<p>The example contains:</p>
<pre><code>Member_RGLR
Member_PRTN</code></pre>
<p>These extend <code>MemberTypeImplementation</code> and demonstrate how client-specific member rules can be added.</p>
<p>For example, <code>Member_RGLR</code> implements a yearly participation fee, while <code>Member_PRTN</code> contains partner-specific logic.</p>

<h2 id="validation">14. Subscription Validation Strategies</h2>
<p>The example contains three strategies:</p>
<pre><code>SubscriptionValidationPublic
SubscriptionValidationUser
SubscriptionValidationAdmin</code></pre>
<p>The user strategy checks:</p>
<ul>
<li>whether the activity subscription period is over;</li>
<li>whether the member is active;</li>
<li>whether the member's subscription validity covers the activity date.</li>
</ul>
<pre><code>if($subscribableitem-&gt;activity-&gt;subscriptionPeriodOver()) {
    return $this-&gt;errorcode(
        100,
        'Inschrijving of annuleren is jammer genoeg niet langer mogelijk.'
    );
}

if(!$member-&gt;active) {
    return $this-&gt;errorcode(
        200,
        'Je lidmaatschap is gedeactiveerd.'
    );
}

if($member-&gt;subscriptionuntil &lt;
   $subscribableitem-&gt;activity-&gt;date) {
    return $this-&gt;errorcode(
        201,
        'Gelieve eerst je lidgeld voor volgend jaar te betalen'
    );
}

return $this-&gt;errorcode(0);</code></pre>
<p>The admin strategy extends the user strategy and deliberately applies different rules.</p>

<h2 id="decorator">15. CommandDecorator Example</h2>
<p>The example's <code>PublicActivityCommand</code> illustrates the recommended extension pattern.</p>
<pre><code>class PublicActivityCommand
    extends \controllerframework\controllers\CommandDecorator
{
    public function doExecuteDecorator(
        \controllerframework\registry\Request $request
    ): ?int {

        $id = filter_var(
            $request-&gt;get('id'),
            FILTER_VALIDATE_INT
        );

        if(!$id) {
            $request-&gt;set('errorcode', 'wrongID');
            $request-&gt;addFeedback("Wrong ID");
            return self::CMD_ERROR;
        }

        $activity = \model\Activity::find($id);

        $seatmap =
            $activity-&gt;activitytypeimplementation-&gt;seatmap();

        $this-&gt;addResponses($request, [
            'seatmap' =&gt; $seatmap
        ]);

        return null;
    }

    public function initCommand(): void {
        $this-&gt;setCommand(
            new \membersactivities\commands\user\PublicActivityCommand
        );
    }
}</code></pre>
<p>The decorator performs client-specific work and then delegates the generic activity operation to the MembersActivities command.</p>

<h2 id="activity">16. Public Activity Example</h2>
<p>The route is:</p>
<pre><code>/activity?id=123</code></pre>
<p>The client decorator validates the ID, retrieves the activity and determines whether the activity's type requires a seat map.</p>
<p>The generic framework command then prepares the activity, member and cost-item information for the view.</p>
<div class="diagram">/activity?id=123
       │
       ▼
commands\user\PublicActivityCommand
       │
       ├── validate ID
       ├── load Activity
       └── determine seatmap
       │
       ▼
membersactivities\commands\user\PublicActivityCommand
       │
       ▼
views/user/activity.php</div>
<p>The view renders activity information, cost items and, when enabled, the seat-map interface.</p>

<h2 id="payment">17. Payment Example</h2>
<p>The example uses a separate <code>CreatePaymentCommand</code> decorator. It currently delegates without adding extra logic.</p>
<pre><code>public function doExecuteDecorator(
    \controllerframework\registry\Request $request
): ?int {
    return null;
}</code></pre>
<p>The wrapped framework command performs the generic payment creation.</p>
<p>This is a useful template: client code can remain empty until application-specific behaviour is actually required.</p>

<h2 id="mollie">18. Mollie Example</h2>
<p>The example's <code>PaymentToMollieCommand</code> extends the framework Mollie command.</p>
<pre><code>class PaymentToMollieCommand
    extends \membersactivities\commands\mollie\PaymentToMollieCommand
{
    public function doExecuteDecorator(
        \controllerframework\registry\Request $request
    ): ?int {

        $id = filter_var(
            $request-&gt;get('id'),
            FILTER_VALIDATE_INT
        );

        $request-&gt;set(
            'paymentConfirmation',
            'paymentConfirmation'
        );

        $request-&gt;set(
            'orderDescription',
            APP . " orderid=" . $id
        );

        return null;
    }
}</code></pre>
<p>This demonstrates an important separation:</p>
<div class="diagram">Client PaymentToMollieCommand
        │
        ├── determines paymentConfirmation route
        └── determines orderDescription
        │
        ▼
Framework OrderToMollieCommand
        │
        ├── retrieves server-side Payment
        ├── uses server-side amount
        └── creates Mollie payment</div>
<p>The order description is therefore client-specific and travels through the <code>Request</code>. The payment amount must remain server-side.</p>

<h3>18.1 Payment status processing</h3>
<p>The example's <code>Payment_RGLR</code> implements <code>statusReceived()</code>. When the status becomes <code>paid</code>, it can:</p>
<ul>
<li>retrieve the subscription;</li>
<li>create Google Wallet tickets when configured;</li>
<li>prepare ticket/download information;</li>
<li>send a confirmation email;</li>
<li>update the payment source/status.</li>
</ul>
<p>This is an example of a client-specific payment type implementation.</p>

<h2 id="admin">19. Administration Example</h2>
<p>The example contains administrative commands for:</p>
<ul>
<li>creating and editing activities;</li>
<li>creating and editing cost items;</li>
<li>creating and editing members;</li>
<li>editing and deleting payments;</li>
<li>managing activity composites;</li>
<li>managing member composites;</li>
<li>searching members;</li>
<li>exporting data.</li>
</ul>
<p>A typical client decorator explicitly requires administrator login:</p>
<pre><code>protected function getLevelOfLoginRequired(): void {
    $this-&gt;setLoginLevel(
        new \controllerframework\sessions\AdminLogin()
    );
}</code></pre>
<p>This should be the normal pattern for commands exposing administrative data or state-changing administrative operations.</p>

<h2 id="run">20. Run and Test the Example</h2>
<h3>20.1 Development server</h3>
<p>For a local PHP development environment, the application's public entry point should be <code>index.php</code>. Depending on the Controller Framework's routing requirements and your web-server setup, configure the document root and rewrite rules according to the supplied <code>.htaccess</code>.</p>
<h3>20.2 First test</h3>
<p>Open the configured application homepage:</p>
<pre><code>https://your-domain.example/</code></pre>
<p>The root command forwards to the configured login/activity/admin flow depending on the returned command status.</p>
<h3>20.3 Test an activity</h3>
<p>Use an activity ID from the database:</p>
<pre><code>https://your-domain.example/activity?id=1</code></pre>
<p>Verify that the activity, cost items and appropriate seat-map behaviour are displayed.</p>
<h3>20.4 Test payment</h3>
<p>Use the test Mollie configuration. Verify the complete sequence:</p>
<div class="diagram">Activity
  │
  ▼
Create Payment
  │
  ▼
Mollie
  │
  ├── customer return
  │
  └── webhook
        │
        ▼
Payment status update
        │
        ▼
Payment_RGLR::statusReceived()</div>
<h3>20.5 Test administration</h3>
<p>Log in as an administrator and verify activity, cost item, member and payment management.</p>

<h2 id="customize">21. How to Turn the Example into Your Application</h2>
<p>The recommended approach is incremental.</p>
<div class="step"><strong>1. Keep the framework dependency unchanged.</strong><br>Start with MembersActivities Framework 1.0.30.</div>
<div class="step"><strong>2. Replace configuration.</strong><br>Set application name, domain, database, mail and integration credentials.</div>
<div class="step"><strong>3. Replace the client models.</strong><br>Adapt <code>Member</code>, <code>Activity</code>, <code>Costitem</code>, <code>Subscription</code> and <code>Payment</code> to your domain.</div>
<div class="step"><strong>4. Define classifications.</strong><br>Create type implementations such as <code>Activity_RGLR</code> or your own application-specific types.</div>
<div class="step"><strong>5. Define validation strategies.</strong><br>Implement the rules for public users, members and administrators.</div>
<div class="step"><strong>6. Adapt views.</strong><br>Replace example branding, texts and layout.</div>
<div class="step"><strong>7. Add decorators.</strong><br>Use CommandDecorators for client-specific behaviour around framework commands.</div>
<div class="step"><strong>8. Configure payments.</strong><br>Keep amounts server-side and use the Request mechanism for client-specific payment descriptions.</div>
<div class="step"><strong>9. Remove unused integrations.</strong><br>If you do not use Google Wallet, seat maps or another optional feature, do not retain unnecessary configuration or code.</div>
<div class="step"><strong>10. Test before production.</strong><br>Test authentication, CSRF, subscriptions, payments, webhooks, exports and error paths.</div>

<h2 id="checklist">22. Basic Implementation Checklist</h2>
<table>
<tr><th>Area</th><th>Check</th></tr>
<tr><td>Composer</td><td><code>samoscon/membersactivities-framework</code> is set to 1.0.30.</td></tr>
<tr><td>Controller Framework</td><td>1.0.31 is installed through Composer.</td></tr>
<tr><td>Configuration</td><td>Application, database and integration settings are replaced with client values.</td></tr>
<tr><td>Secrets</td><td><code>_SALTRAND</code>, database passwords and API credentials are protected.</td></tr>
<tr><td>Database</td><td>Reference schema is imported only for a new application or migrated deliberately.</td></tr>
<tr><td>Models</td><td>Required client model classes exist.</td></tr>
<tr><td>Type implementations</td><td>Every classification used by the database has a corresponding client type class.</td></tr>
<tr><td>Validation</td><td>Public/user/admin subscription rules are explicitly defined.</td></tr>
<tr><td>Routing</td><td>All required commands and views are registered in <code>controls.xml</code>.</td></tr>
<tr><td>Security</td><td>POST/CSRF, login levels and AccessToken flows are tested.</td></tr>
<tr><td>Payments</td><td>Payment amounts come from server-side Payment objects.</td></tr>
<tr><td>Mollie</td><td>Test webhook and payment confirmation flow works before live deployment.</td></tr>
<tr><td>Mail</td><td>SMTP settings and mail queue processing are tested.</td></tr>
<tr><td>Cron</td><td>Absolute paths are used.</td></tr>
<tr><td>Production</td><td>HTTPS, protected configuration and production credentials are configured.</td></tr>
</table>

<footer>
<p><strong>Reference:</strong> MembersActivities Framework 1.0.30 · Controller Framework 1.0.31</p>
<p>This guide is based on the example application included in the MembersActivities Framework 1.0.30 release. The example contains application-specific sample code and should be adapted before production use.</p>
</footer>

</div>
</body>
</html>
