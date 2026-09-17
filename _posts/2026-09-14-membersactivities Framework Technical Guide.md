---

layout: post
author: dirkvm

---

<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>MembersActivities Framework 1.0.31 — Technical Guide</title>
<style>
:root{--ink:#202124;--muted:#5f6368;--accent:#1a73e8;--soft:#f6f8fa;--border:#dadce0;--note:#fff8e1;--code:#f6f8fa}
*{box-sizing:border-box}html{scroll-behavior:smooth}
body{margin:0;color:var(--ink);font-family:-apple-system,BlinkMacSystemFont,"Segoe UI",Roboto,Arial,sans-serif;line-height:1.62}
.container{max-width:1120px;margin:auto;padding:40px 30px 80px}
header{border-bottom:1px solid var(--border);padding-bottom:30px;margin-bottom:35px}
h1{font-size:2.4rem;line-height:1.15;margin:0 0 8px}h2{font-size:1.65rem;margin-top:50px;border-bottom:1px solid var(--border);padding-bottom:8px}h3{font-size:1.2rem;margin-top:30px}
.subtitle{font-size:1.15rem;color:var(--muted)}
.meta{display:grid;grid-template-columns:repeat(auto-fit,minmax(210px,1fr));gap:10px;margin-top:22px}.meta div{background:#e8f0fe;padding:11px 14px;border-radius:6px}.meta strong{display:block}
.toc{background:#fafafa;border:1px solid var(--border);border-radius:6px;padding:20px 25px}.toc ol{margin-bottom:0}
pre{background:var(--code);border:1px solid var(--border);border-radius:6px;padding:16px;overflow:auto;line-height:1.45}
code{font-family:"SFMono-Regular",Consolas,"Liberation Mono",monospace;font-size:.9em}:not(pre)>code{background:var(--code);padding:2px 5px;border-radius:4px}
table{width:100%;border-collapse:collapse;margin:18px 0 28px}th,td{border:1px solid var(--border);padding:8px 10px;text-align:left;vertical-align:top}th{background:var(--soft)}
.note{background:var(--note);border-left:4px solid #f9ab00;padding:13px 17px;margin:20px 0}
.diagram{background:#fafafa;border:1px solid var(--border);border-radius:6px;padding:18px;overflow:auto;white-space:pre;font-family:monospace;line-height:1.4}
.small{font-size:.92rem;color:var(--muted)}
ul.check{list-style:none;padding-left:0}ul.check li{margin:4px 0}ul.check li:before{content:"☐ "}
footer{margin-top:60px;border-top:1px solid var(--border);padding-top:20px;color:var(--muted);font-size:.9rem}
a{color:var(--accent)}
@media print{.container{max-width:none;padding:15px}h2{break-before:page}pre,table,.diagram{break-inside:avoid}}
</style>
</head>
<body>
<div class="container">

<header>
<h1>MembersActivities Framework 1.0.31</h1>
<div class="subtitle">Technical Guide &amp; Object Model Reference</div>
<div class="meta">
<div><strong>Framework</strong>MembersActivities 1.0.31</div>
<div><strong>Controller Framework</strong>1.0.31</div>
<div><strong>PHP</strong>8.3+</div>
<div><strong>Database</strong>MySQL / MariaDB via PDO</div>
<div><strong>Author</strong>Dirk Van Meirvenne</div>
</div>
</header>

<section class="toc">
<h2 style="margin-top:0;border:0">Contents</h2>
<ol>
<li><a href="#scope">Scope and Architecture</a></li>
<li><a href="#packages">Package Structure</a></li>
<li><a href="#patterns">Architectural Patterns</a></li>
<li><a href="#object-model">Object Model Overview</a></li>
<li><a href="#activity">Activity Model</a></li>
<li><a href="#costitem">Costitem Model</a></li>
<li><a href="#member">Member Model</a></li>
<li><a href="#subscription">Subscription Model</a></li>
<li><a href="#payment">Payment Model</a></li>
<li><a href="#wallet">Google Wallet Model</a></li>
<li><a href="#mappers">Mapper Layer</a></li>
<li><a href="#commands">Command Layer</a></li>
<li><a href="#decorators">Command Decorators</a></li>
<li><a href="#strategies">Strategy and Type Implementations</a></li>
<li><a href="#validation">Subscription Validation</a></li>
<li><a href="#request">Request / Response Flow</a></li>
<li><a href="#security">Security Architecture</a></li>
<li><a href="#payments">Payment and Mollie Flow</a></li>
<li><a href="#database">Database Model</a></li>
<li><a href="#transactions">Transactions and Consistency</a></li>
<li><a href="#mail">Mail Queue</a></li>
<li><a href="#extension">Extension Guide</a></li>
<li><a href="#api">API / Class Reference</a></li>
<li><a href="#checklist">Technical Review Checklist</a></li>
</ol>
</section>

<h2 id="scope">1. Scope and Architecture</h2>
<p>MembersActivities Framework 1.0.31 is a reusable domain framework for applications managing members, activities, cost items, subscriptions and payments. It is implemented as a Composer package and extends the Controller Framework.</p>
<div class="diagram">┌─────────────────────────────────────────────────────┐
│                  Client Application                 │
│                                                     │
│ Commands · Decorators · Views · Client Models       │
│ Type Implementations · Validation Strategies        │
└──────────────────────────┬──────────────────────────┘
                           │
┌──────────────────────────▼──────────────────────────┐
│          MembersActivities Framework 1.0.31         │
│                                                     │
│ Activities · Costitems · Subscriptions · Payments  │
│ Mappers · Commands · Mollie · Google Wallet         │
└──────────────────────────┬──────────────────────────┘
                           │
┌──────────────────────────▼──────────────────────────┐
│              Controller Framework 1.0.31            │
│                                                     │
│ Command · Request · DomainObject · Mapper           │
│ Registry · Sessions · CSRF · AccessToken            │
│ ErrorHandler · Rendering · Audit                    │
└─────────────────────────────────────────────────────┘</div>
<p>The Composer package declares PHP <code>^8.3</code>, <code>samoscon/controller-framework ^1.0.31</code> and <code>mollie/mollie-api-php ^2.0</code>. Mollie is therefore a package dependency, although the payment integration is optional at application level.</p>

<h2 id="packages">2. Package Structure</h2>
<pre><code>src/
├── commands/
│   ├── DefaultCommand.php
│   ├── admin/
│   │   ├── AddActivityToCompositeCommand.php
│   │   ├── AddMemberToCompositeCommand.php
│   │   ├── AdminHomeCommand.php
│   │   ├── CreateActivityCommand.php
│   │   ├── CreateCostitemCommand.php
│   │   ├── CreateMemberCommand.php
│   │   ├── DeleteActivityCommand.php
│   │   ├── DeleteCostitemCommand.php
│   │   ├── DeleteMemberCommand.php
│   │   ├── DeletePaymentCommand.php
│   │   ├── EditActivityCommand.php
│   │   ├── EditCostitemCommand.php
│   │   ├── EditMemberCommand.php
│   │   ├── EditPaymentCommand.php
│   │   ├── RemoveActivityFromCompositeCommand.php
│   │   ├── RemoveMemberFromCompositeCommand.php
│   │   └── SearchMembersCommand.php
│   ├── downloads/
│   │   ├── DownloadXlsMembersCommand.php
│   │   └── DownloadXlsParticipantsCommand.php
│   ├── mollie/
│   │   ├── OrderToMollieCommand.php
│   │   ├── PaymentToMollieCommand.php
│   │   └── WebhookFromMollieCommand.php
│   └── user/
│       ├── ActivityCommand.php
│       ├── CreatePaymentCommand.php
│       ├── PaymentConfirmationCommand.php
│       ├── PublicActivityCommand.php
│       └── UserActivityCommand.php
│
├── model/
│   ├── activities/
│   │   ├── Activity.php
│   │   ├── ActivityComposite.php
│   │   ├── ActivityMapper.php
│   │   ├── ActivityTypeImplementation.php
│   │   ├── Costitem.php
│   │   ├── CostitemMapper.php
│   │   └── CostitemTypeImplementation.php
│   ├── subscriptions/
│   │   ├── Payment.php
│   │   ├── PaymentMapper.php
│   │   ├── PaymentTypeImplementation.php
│   │   ├── Subscription.php
│   │   ├── SubscriptionMapper.php
│   │   ├── SubscriptionTypeImplementation.php
│   │   └── SubscriptionValidationStrategy.php
│   └── wallet/
│       └── GoogleWalletTicket.php</code></pre>

<h2 id="patterns">3. Architectural Patterns</h2>
<table>
<tr><th>Pattern</th><th>Where used</th><th>Purpose</th></tr>
<tr><td>Active Record / Domain Object</td><td>Activity, Costitem, Payment, Subscription; Member comes from Controller Framework</td><td>Domain objects represent persisted application entities.</td></tr>
<tr><td>Data Mapper</td><td>ActivityMapper, CostitemMapper, PaymentMapper, SubscriptionMapper</td><td>Separates persistence operations from domain objects.</td></tr>
<tr><td>Abstract Factory</td><td><code>getInstance()</code> implementations</td><td>Creates concrete client model objects from database rows.</td></tr>
<tr><td>Builder / Type implementation</td><td>ActivityTypeImplementation, CostitemTypeImplementation, PaymentTypeImplementation, SubscriptionTypeImplementation</td><td>Associates a domain object with classification-specific behaviour.</td></tr>
<tr><td>Strategy</td><td>SubscriptionValidationStrategy</td><td>Allows client applications to define subscription rules independently of the generic command.</td></tr>
<tr><td>Decorator</td><td>CommandDecorator in Controller Framework</td><td>Adds client-specific command behaviour without modifying framework commands.</td></tr>
<tr><td>Composite</td><td>ActivityComposite</td><td>Represents activities arranged in a tree.</td></tr>
</table>

<h2 id="object-model">4. Object Model Overview</h2>
<div class="diagram">                         ┌───────────────────┐
                         │       Member      │
                         │ Controller FW     │
                         └─────────┬─────────┘
                                   │
                         ┌─────────┴─────────┐
                         │                   │
                         ▼                   ▼
                    ┌─────────┐        ┌───────────┐
                    │ Payment │        │Subscription│
                    └────┬────┘        └─────┬─────┘
                         │ 1                │ *
                         │                  │
                         │            ┌─────┴─────┐
                         │            │ Costitem  │
                         │            └─────┬─────┘
                         │                  │ *
                         │                  │
                         │            ┌─────▼─────┐
                         └────────────│ Activity  │
                                      └─────┬─────┘
                                            │
                                     composite parent
                                            │
                                      ┌─────▼─────┐
                                      │  Activity │
                                      │  children │
                                      └───────────┘

Subscription:
  member_id   ───────► Member
  costitem_id ───────► Costitem
  payment_id  ───────► Payment

Costitem:
  activity_id ───────► Activity

Payment:
  member_id   ───────► Member

Member:
  parent_id   ───────► Member (self-reference)</div>

<h2 id="activity">5. Activity Model</h2>
<p><code>membersactivities\model\activities\Activity</code> is an abstract domain object extending the Controller Framework's <code>DomainObject</code>.</p>
<h3>Responsibilities</h3>
<ul>
<li>Instantiate the concrete client activity class based on the database row.</li>
<li>Attach an activity type implementation based on <code>classification</code>.</li>
<li>Resolve a parent activity when <code>parent_id</code> is present.</li>
<li>Return participants.</li>
<li>Determine whether the subscription period is over.</li>
<li>Calculate the total amount received for the activity.</li>
</ul>
<h3>Public extension point</h3>
<pre><code>public ?ActivityTypeImplementation $activitytypeimplementation = null;</code></pre>
<h3>Important methods</h3>
<table><tr><th>Method</th><th>Return</th><th>Purpose</th></tr>
<tr><td><code>getInstance(array $row)</code></td><td>Activity</td><td>Creates a concrete activity or ActivityComposite and attaches its type implementation.</td></tr>
<tr><td><code>getParticipants()</code></td><td>ObjectMap</td><td>Returns paid participants, including inherited participants for child activities.</td></tr>
<tr><td><code>subscriptionPeriodOver()</code></td><td>bool</td><td>Checks the activity due date against the current date.</td></tr>
<tr><td><code>getTotalAmountReceived()</code></td><td>float</td><td>Sums distinct paid payments associated with cost items of the activity.</td></tr>
</table>
<h3>ActivityComposite</h3>
<p><code>ActivityComposite</code> extends <code>Activity</code> and implements the Composite pattern. Children are loaded lazily through <code>ActivityMapper::getChildren()</code>.</p>
<p>For a composite, <code>subscriptionPeriodOver()</code> returns <code>true</code> when any child activity has an expired subscription period.</p>

<h2 id="costitem">6. Costitem Model</h2>
<p><code>Costitem</code> represents a subscribable item belonging to an activity.</p>
<pre><code>public ?CostitemTypeImplementation $costitemtypeimplementation = null;</code></pre>
<p>During <code>getInstance()</code>, the framework:</p>
<ol><li>creates the concrete client Costitem class;</li><li>initializes database properties;</li><li>creates the classification-specific type implementation;</li><li>loads the related Activity when <code>activity_id</code> is present.</li></ol>
<table><tr><th>Database property</th><th>Meaning</th></tr>
<tr><td>id</td><td>Identifier</td></tr><tr><td>description</td><td>Human-readable item description</td></tr>
<tr><td>classification</td><td>Type implementation selector</td></tr><tr><td>price</td><td>Unit price</td></tr>
<tr><td>type</td><td>Application-defined cost item type</td></tr><tr><td>activity_id</td><td>Owning activity</td></tr>
</table>

<h2 id="member">7. Member Model</h2>
<p>Member is supplied by the Controller Framework and specialized by the client application, normally as:</p>
<pre><code>class Member extends \controllerframework\members\Member
{
    // client-specific behaviour
}</code></pre>
<p>The reference schema supports member groups through <code>parent_id</code>. A member can therefore act as a parent/group while child members point to it.</p>
<table><tr><th>Property</th><th>Meaning</th></tr>
<tr><td>name / lastname</td><td>Member identity</td></tr><td></td></tr>
<tr><td>email</td><td>Contact/login email</td></tr>
<tr><td>role</td><td>User or administrator role</td></tr>
<tr><td>password</td><td>Stored password hash managed by authentication infrastructure</td></tr>
<tr><td>active</td><td>Application membership state</td></tr>
<tr><td>subscriptionuntil</td><td>Membership validity date</td></tr>
<tr><td>parent_id</td><td>Optional member/group relationship</td></tr>
</table>

<h2 id="subscription">8. Subscription Model</h2>
<p><code>Subscription</code> represents a registration by a member for a cost item.</p>
<pre><code>public ?SubscriptionTypeImplementation $subscriptiontypeimplementation = null;</code></pre>
<p>When instantiated, the framework resolves:</p>
<pre><code>subscription.member
subscription.costitem
subscription.payment
subscriptiontypeimplementation</code></pre>
<p>The payment relation is optional because a subscription can exist before payment has been associated with it.</p>

<h2 id="payment">9. Payment Model</h2>
<p><code>Payment</code> represents a financial transaction and extends the Controller Framework's <code>DomainObject</code>.</p>
<pre><code>public ?PaymentTypeImplementation $paymenttypeimplementation = null;</code></pre>
<table><tr><th>Method</th><th>Purpose</th></tr>
<tr><td><code>getInstance()</code></td><td>Creates the concrete client payment type and loads the member.</td></tr>
<tr><td><code>delete()</code></td><td>Deletes associated subscriptions and then the payment inside a database transaction.</td></tr>
<tr><td><code>isPaid()</code></td><td>Returns <code>true</code> when status is exactly <code>paid</code>.</td></tr>
<tr><td><code>statusReceived()</code></td><td>Creates an AccessToken and delegates status-specific behaviour to the payment type implementation.</td></tr>
</table>
<p>The transaction in <code>Payment::delete()</code> ensures that deletion of the payment and its associated subscriptions is committed atomically; an exception causes a rollback.</p>

<h2 id="wallet">10. Google Wallet Model</h2>
<p><code>GoogleWalletTicket</code> encapsulates Google Wallet integration. It contains Google API client/service state and methods for creating or updating Wallet classes and objects.</p>
<table><tr><th>Method</th><th>Purpose</th></tr>
<tr><td><code>auth()</code></td><td>Initializes Google API authentication.</td></tr>
<tr><td><code>createClass()</code></td><td>Creates a Wallet class.</td></tr>
<tr><td><code>updateClass()</code></td><td>Updates a Wallet class.</td></tr>
<tr><td><code>createObject()</code></td><td>Creates a Wallet ticket object.</td></tr>
<tr><td><code>updateObject()</code></td><td>Updates a Wallet ticket object.</td></tr>
<tr><td><code>createJwt(int $id)</code></td><td>Creates the JWT used to add the ticket to Google Wallet.</td></tr>
</table>
<p>Event-specific information such as event name, date/time, venue, ticket type and barcode is prepared by the protected setter methods.</p>

<h2 id="mappers">11. Mapper Layer</h2>
<p>The framework contains four domain mappers:</p>
<pre><code>ActivityMapper
CostitemMapper
SubscriptionMapper
PaymentMapper</code></pre>
<table><tr><th>Mapper</th><th>Table</th><th>Framework-specific fields</th></tr>
<tr><td>ActivityMapper</td><td>activity</td><td>date, duedate, longdescription, start, end, location</td></tr>
<tr><td>CostitemMapper</td><td>costitem</td><td>price, type, activity_id</td></tr>
<tr><td>SubscriptionMapper</td><td>subscription</td><td>member_id, costitem_id, payment_id, quantity, remark</td></tr>
<tr><td>PaymentMapper</td><td>payment</td><td>member_id, date, amount, status, type, source</td></tr>
</table>
<p>Each mapper extends the Controller Framework <code>Mapper</code> and defines an allowed-field whitelist. This is important when performing update/insert operations through the persistence layer.</p>
<div class="note"><strong>Important:</strong> <code>Mapper::findAll()</code> accepts a free SQL select clause. Client code must never concatenate untrusted request data into such a clause.</div>

<h2 id="commands">12. Command Layer</h2>
<p>The framework commands are grouped by responsibility.</p>
<table><tr><th>Package</th><th>Commands</th></tr>
<tr><td>admin</td><td>Create, edit, delete and composite-management operations for activities, cost items, members and payments; administration and member search.</td></tr>
<tr><td>downloads</td><td>Member and participant XLS exports.</td></tr>
<tr><td>mollie</td><td>Generic Mollie payment creation and webhook processing.</td></tr>
<tr><td>user</td><td>Activity registration, payment creation, payment confirmation and public/user activity flows.</td></tr>
</table>
<p>Commands are intended to be connected to routes through the Controller Framework configuration, normally <code>controls.xml</code>.</p>

<h2 id="decorators">13. Command Decorators</h2>
<p>Client applications should use Controller Framework <code>CommandDecorator</code> whenever framework command logic can be reused.</p>
<div class="diagram">Client route
    │
    ▼
Client CommandDecorator
    │
    ├── client-specific validation/configuration
    ├── authentication level
    └── Request parameters/objects
    │
    ▼
MembersActivities framework command
    │
    ▼
Domain model / mapper</div>
<p>This is especially important for validation strategies and payment descriptions. It keeps generic framework commands independent of a particular client application's business rules.</p>

<h2 id="strategies">14. Strategy and Type Implementations</h2>
<h3>Type implementations</h3>
<pre><code>ActivityTypeImplementation
CostitemTypeImplementation
PaymentTypeImplementation
SubscriptionTypeImplementation</code></pre>
<p>The classification field determines the concrete implementation. For example, an activity with classification <code>RGLR</code> results in a client class such as <code>\model\Activity_RGLR</code>.</p>
<h3>Client example</h3>
<pre><code>class Activity_RGLR
    extends \membersactivities\model\activities\ActivityTypeImplementation
{
    // default activity behaviour
}

class Activity_STMP
    extends \membersactivities\model\activities\ActivityTypeImplementation
{
    public function seatmap(): bool {
        return true;
    }
}</code></pre>

<h2 id="validation">15. Subscription Validation</h2>
<p><code>SubscriptionValidationStrategy</code> is the extension point for business rules determining whether a member may subscribe to a cost item.</p>
<pre><code>public function subscribe(
    \controllerframework\members\Member $member,
    \membersactivities\model\activities\Costitem $subscribableitem,
    array $properties
): array</code></pre>
<p>Client applications can implement different policies, for example:</p>
<pre><code>SubscriptionValidationPublic
SubscriptionValidationUser
SubscriptionValidationAdmin</code></pre>
<p>The concrete strategy should be supplied through controlled application code, normally a CommandDecorator. A request parameter must never be treated as a class name and instantiated dynamically.</p>

<h2 id="request">16. Request / Response Flow</h2>
<div class="diagram">HTTP Request
     │
     ▼
CommandResolver
     │
     ▼
Command / Decorator
     │
     ├── Request input validation
     ├── CSRF validation where required
     ├── business operation
     └── response preparation
     │
     ▼
Command status
     │
     ├── CMD_OK / CMD_DEFAULT
     ├── CMD_ERROR
     └── other configured statuses
     │
     ▼
View or Forward</div>
<p>The Request object is also used as a controlled communication channel between decorators and wrapped commands. This mechanism is used, among other things, for application-specific validation strategies and Mollie order descriptions.</p>

<h2 id="security">17. Security Architecture</h2>
<h3>17.1 Authentication</h3>
<p>Authentication and login levels are supplied by the Controller Framework. Commands should explicitly define whether they require no login, a user login or administrator login.</p>
<h3>17.2 CSRF</h3>
<p>State-changing operations should use POST and validate the CSRF token before modifying data.</p>
<h3>17.3 AccessToken</h3>
<p>Controller Framework 1.0.31 supplies <code>AccessToken</code>. MembersActivities uses it for protected payment operations.</p>
<pre><code>AccessToken::generate('mollie-order', (string)$paymentId)</code></pre>
<p>The <code>_SALTRAND</code> secret used by the AccessToken mechanism must remain confidential and must not be stored in public source code.</p>
<h3>17.4 Input validation</h3>
<p>Request parameters are untrusted. Validate IDs, email addresses, file names, redirects, query parameters and all other externally supplied values according to their intended type and context.</p>

<h2 id="payments">18. Payment and Mollie Flow</h2>
<div class="diagram">CreatePaymentCommand
        │
        ▼
Payment
        │
        │ protected order/payment identifier
        ▼
PaymentToMollieCommand
        │
        ▼
OrderToMollieCommand
        │
        ├── validate protected identifier/token
        ├── load Payment from server-side database
        ├── obtain Payment::amount
        ├── obtain application-specific orderDescription
        └── create Mollie payment
        │
        ▼
Mollie
   │              │
   │ payment      │ webhook
   ▼              ▼
customer      WebhookFromMollieCommand
                  │
                  ▼
              Payment::statusReceived()
                  │
                  ▼
           PaymentTypeImplementation</div>
<p>The critical trust boundary is the payment amount: the amount must be read from the server-side <code>Payment</code> object and not accepted from a browser-submitted amount.</p>
<p>The order description is intentionally different: the client-specific CommandDecorator can set <code>orderDescription</code> on the Request so that different order types can supply different descriptions without modifying the generic payment command.</p>

<h2 id="database">19. Database Model</h2>
<table><tr><th>Table</th><th>Primary purpose</th><th>Important foreign keys</th></tr>
<tr><td>activity</td><td>Activities and activity hierarchy</td><td>parent_id → activity.id</td></tr>
<tr><td>member</td><td>Members and member groups</td><td>parent_id → member.id</td></tr>
<tr><td>costitem</td><td>Subscribable items/prices</td><td>activity_id → activity.id</td></tr>
<tr><td>payment</td><td>Financial transactions</td><td>member_id → member.id</td></tr>
<tr><td>subscription</td><td>Member registration for a cost item</td><td>member_id, costitem_id, payment_id</td></tr>
<tr><td>remember_tokens</td><td>Remember-me authentication tokens</td><td>member-related authentication data</td></tr>
<tr><td>mail_queue</td><td>Asynchronous email processing</td><td>application process data</td></tr>
</table>
<h3>Relationship detail</h3>
<div class="diagram">activity 1 ─────────── * costitem
activity 1 ─────────── * child activity

member 1 ───────────── * subscription
member 1 ───────────── * payment
member 1 ───────────── * child member

costitem 1 ──────────── * subscription

payment 1 ───────────── * subscription</div>
<p>The reference schema uses InnoDB, utf8mb4 and foreign key constraints. It is intended as a starting schema for a new client application rather than a production migration script.</p>

<h2 id="transactions">20. Transactions and Consistency</h2>
<p>Payment deletion is implemented transactionally: related subscriptions are deleted first, then the payment itself is deleted; an exception rolls the operation back.</p>
<p>For client-specific multi-step business operations, the same principle should be considered whenever several related database changes must succeed or fail as one logical operation.</p>

<h2 id="mail">21. Mail Queue</h2>
<p>The <code>mail_queue</code> table supports asynchronous mail processing. A typical lifecycle is:</p>
<div class="diagram">pending
  │
  ▼
sending
  │
  ├────────► sent
  │
  └────────► failed</div>
<p>The queue records processing information such as attempts, creation/start/send timestamps and an error field. A cron command can process pending messages and cleanup old sent records.</p>

<h2 id="extension">22. Extension Guide</h2>
<h3>22.1 Add a new activity type</h3>
<ol>
<li>Create a client <code>Activity</code> subclass if not already present.</li>
<li>Create <code>Activity_XXXX</code> extending <code>ActivityTypeImplementation</code>.</li>
<li>Store <code>XXXX</code> in the activity classification field.</li>
<li>Add client logic only where the type needs special behaviour.</li>
</ol>
<h3>22.2 Add a new payment type</h3>
<ol>
<li>Create a client <code>Payment</code> subclass.</li>
<li>Create <code>Payment_XXXX</code> extending <code>PaymentTypeImplementation</code>.</li>
<li>Implement <code>statusReceived()</code> when payment status changes require client-specific processing.</li>
</ol>
<h3>22.3 Add client-specific subscription validation</h3>
<ol>
<li>Create a class extending <code>SubscriptionValidationStrategy</code>.</li>
<li>Implement the application-specific validation rules.</li>
<li>Inject the strategy through a controlled CommandDecorator.</li>
</ol>
<h3>22.4 Add a new command around an existing command</h3>
<ol>
<li>Create a client CommandDecorator.</li>
<li>Set the wrapped framework command in <code>initCommand()</code>.</li>
<li>Use <code>doExecuteDecorator()</code> for client-specific preparation.</li>
<li>Define the required authentication level.</li>
<li>Register the route in <code>controls.xml</code>.</li>
</ol>

<h2 id="api">23. API / Class Reference</h2>
<table><tr><th>Class</th><th>Type</th><th>Key API</th></tr>
<tr><td><code>membersactivities\model\activities\Activity</code></td><td>abstract model</td><td>getInstance, getParticipants, subscriptionPeriodOver, getTotalAmountReceived</td></tr>
<tr><td><code>membersactivities\model\activities\ActivityComposite</code></td><td>composite model</td><td>getChildren, isComposite, subscriptionPeriodOver</td></tr>
<tr><td><code>membersactivities\model\activities\Costitem</code></td><td>abstract model</td><td>getInstance</td></tr>
<tr><td><code>membersactivities\model\subscriptions\Subscription</code></td><td>abstract model</td><td>getInstance</td></tr>
<tr><td><code>membersactivities\model\subscriptions\Payment</code></td><td>abstract model</td><td>getInstance, delete, isPaid, statusReceived</td></tr>
<tr><td><code>membersactivities\model\subscriptions\SubscriptionValidationStrategy</code></td><td>strategy</td><td>subscribe, errorcode</td></tr>
<tr><td><code>membersactivities\model\activities\ActivityMapper</code></td><td>mapper</td><td>getChildren, getParticipants</td></tr>
<tr><td><code>membersactivities\model\activities\CostitemMapper</code></td><td>mapper</td><td>persistence / allowed fields</td></tr>
<tr><td><code>membersactivities\model\subscriptions\SubscriptionMapper</code></td><td>mapper</td><td>persistence / allowed fields</td></tr>
<tr><td><code>membersactivities\model\subscriptions\PaymentMapper</code></td><td>mapper</td><td>persistence / allowed fields</td></tr>
<tr><td><code>membersactivities\model\activities\ActivityTypeImplementation</code></td><td>type implementation</td><td>seatmap</td></tr>
<tr><td><code>membersactivities\model\activities\CostitemTypeImplementation</code></td><td>type implementation</td><td>extension point</td></tr>
<tr><td><code>membersactivities\model\subscriptions\SubscriptionTypeImplementation</code></td><td>type implementation</td><td>extension point</td></tr>
<tr><td><code>membersactivities\model\subscriptions\PaymentTypeImplementation</code></td><td>type implementation</td><td>statusReceived, getSubscription, Wallet support</td></tr>
<tr><td><code>membersactivities\model\wallet\GoogleWalletTicket</code></td><td>integration service</td><td>auth, create/update class/object, createJwt</td></tr>
</table>

<h2 id="checklist">24. Technical Review Checklist</h2>
<ul class="check">
<li>MembersActivities Framework version is 1.0.31.</li>
<li>Controller Framework version is 1.0.31.</li>
<li>PHP version satisfies the package requirement.</li>
<li>PDO MySQL/MariaDB is configured correctly.</li>
<li>Client model classes extend the appropriate framework classes.</li>
<li>Classification values map to existing client type implementations.</li>
<li>Client-specific rules are implemented through strategies/decorators where appropriate.</li>
<li>No validator or other class name is instantiated from untrusted request input.</li>
<li>State-changing operations use POST and CSRF validation.</li>
<li>Administrative commands have the correct authentication level.</li>
<li>AccessToken secrets are protected.</li>
<li>Payment amounts are obtained from server-side Payment objects.</li>
<li>Mollie webhooks retrieve authoritative payment state from Mollie.</li>
<li>Free SQL clauses never contain untrusted input.</li>
<li>Redirects and HTTP header values are validated.</li>
<li>Exceptions are centrally handled and not exposed as stack traces.</li>
<li>Multi-step database operations use transactions where atomicity is required.</li>
<li>Mail queue processing is configured and monitored.</li>
<li>Google Wallet credentials are protected if the integration is enabled.</li>
</ul>

<footer>
<p><strong>Reference:</strong> MembersActivities Framework 1.0.31 · Controller Framework 1.0.31</p>
<p>This technical guide describes the framework source and reference database supplied with Release 1.0.31. Client-specific implementations may add domain behaviour beyond the framework API described here.</p>
</footer>

</div>
</body>
</html>
