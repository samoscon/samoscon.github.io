---

layout: post
author: dirkvm

---

<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>MembersActivities Framework 1.1.0 — Technical Guide</title>

<style>
:root{
  --ink:#202124;
  --muted:#5f6368;
  --accent:#1a73e8;
  --soft:#f6f8fa;
  --border:#dadce0;
  --note:#fff8e1;
  --code:#f6f8fa
}
*{box-sizing:border-box}
html{scroll-behavior:smooth}
body{
  margin:0;
  color:var(--ink);
  font-family:-apple-system,BlinkMacSystemFont,"Segoe UI",Roboto,Arial,sans-serif;
  line-height:1.62
}
.container{
  max-width:1120px;
  margin:auto;
  padding:40px 30px 80px
}
header{
  border-bottom:1px solid var(--border);
  padding-bottom:30px;
  margin-bottom:35px
}
h1{
  font-size:2.4rem;
  line-height:1.15;
  margin:0 0 8px
}
h2{
  font-size:1.65rem;
  margin-top:50px;
  border-bottom:1px solid var(--border);
  padding-bottom:8px
}
h3{
  font-size:1.2rem;
  margin-top:30px
}
.subtitle{
  font-size:1.15rem;
  color:var(--muted)
}
.meta{
  display:grid;
  grid-template-columns:repeat(auto-fit,minmax(210px,1fr));
  gap:10px;
  margin-top:22px
}
.meta div{
  background:#e8f0fe;
  padding:11px 14px;
  border-radius:6px
}
.meta strong{
  display:block
}
.toc{
  background:#fafafa;
  border:1px solid var(--border);
  border-radius:6px;
  padding:20px 25px
}
.toc ol{
  margin-bottom:0
}
pre{
  background:var(--code);
  border:1px solid var(--border);
  border-radius:6px;
  padding:16px;
  overflow:auto;
  line-height:1.45
}
code{
  font-family:"SFMono-Regular",Consolas,"Liberation Mono",monospace;
  font-size:.9em
}
:not(pre)>code{
  background:var(--code);
  padding:2px 5px;
  border-radius:4px
}
table{
  width:100%;
  border-collapse:collapse;
  margin:18px 0 28px
}
th,td{
  border:1px solid var(--border);
  padding:8px 10px;
  text-align:left;
  vertical-align:top
}
th{
  background:var(--soft)
}
.note{
  background:var(--note);
  border-left:4px solid #f9ab00;
  padding:13px 17px;
  margin:20px 0
}
.diagram{
  background:#fafafa;
  border:1px solid var(--border);
  border-radius:6px;
  padding:18px;
  overflow:auto;
  white-space:pre;
  font-family:monospace;
  line-height:1.4
}
.small{
  font-size:.92rem;
  color:var(--muted)
}
ul.check{
  list-style:none;
  padding-left:0
}
ul.check li{
  margin:4px 0
}
ul.check li:before{
  content:"☐ "
}
footer{
  margin-top:60px;
  border-top:1px solid var(--border);
  padding-top:20px;
  color:var(--muted);
  font-size:.9rem
}
a{
  color:var(--accent)
}
@media print{
  .container{
    max-width:none;
    padding:15px
  }
  h2{
    break-before:page
  }
  pre,table,.diagram{
    break-inside:avoid
  }
}
</style>

</head>

<body>
<div class="container">

<header>
<h1>MembersActivities Framework 1.1.0</h1>
<div class="subtitle">Technical Guide &amp; Object Model Reference</div>

<div class="meta">
<div><strong>Framework</strong>MembersActivities 1.1.0</div>
<div><strong>Previous Version</strong>1.0.31</div>
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
<li><a href="#release">Release 1.1.0 Technical Changes</a></li>
<li><a href="#packages">Package Structure</a></li>
<li><a href="#patterns">Architectural Patterns</a></li>
<li><a href="#object-model">Object Model Overview</a></li>
<li><a href="#activity">Activity Model</a></li>
<li><a href="#member">Member Model</a></li>
<li><a href="#costitem">Costitem Model</a></li>
<li><a href="#subscription">Subscription Model</a></li>
<li><a href="#payment">Payment Model</a></li>
<li><a href="#ticket">Ticket Model</a></li>
<li><a href="#ticketscan">TicketScan Model</a></li>
<li><a href="#wallet">Google Wallet Model</a></li>
<li><a href="#mappers">Mapper Layer</a></li>
<li><a href="#commands">Command Layer</a></li>
<li><a href="#decorators">Command Decorators</a></li>
<li><a href="#strategies">Strategy and Type Implementations</a></li>
<li><a href="#validation">Subscription Validation</a></li>
<li><a href="#request">Request / Response Flow</a></li>
<li><a href="#security">Security Architecture</a></li>
<li><a href="#payments">Payment and Mollie Flow</a></li>
<li><a href="#ticketscan-flow">Ticket Scanning Flow</a></li>
<li><a href="#seats">Seat Reservation Consistency</a></li>
<li><a href="#database">Database Model</a></li>
<li><a href="#transactions">Transactions and Consistency</a></li>
<li><a href="#mail">Mail Queue</a></li>
<li><a href="#extension">Extension Guide</a></li>
<li><a href="#migration">Migration from 1.0.31</a></li>
<li><a href="#api">API / Class Reference</a></li>
<li><a href="#checklist">Technical Review Checklist</a></li>
</ol>
</section>

<h2 id="scope">1. Scope and Architecture</h2>

<p>MembersActivities Framework 1.1.0 is a reusable domain framework for applications managing members, activities, cost items, subscriptions, payments, tickets and ticket scanning. It is implemented as a Composer package and extends the Controller Framework.</p>

<div class="diagram">┌──────────────────────────────────────────────────────────┐
│                    Client Application                     │
│                                                          │
│ Commands · Decorators · Views · Client Models            │
│ Type Implementations · Validation Strategies             │
│ Ticket Types · TicketScan Types · Integrations            │
└──────────────────────────┬───────────────────────────────┘
                           │
┌──────────────────────────▼───────────────────────────────┐
│          MembersActivities Framework 1.1.0               │
│                                                          │
│ Activities · Members · Costitems · Subscriptions         │
│ Payments · Tickets · TicketScans                         │
│ Mappers · Commands · Mollie · Google Wallet              │
└──────────────────────────┬───────────────────────────────┘
                           │
┌──────────────────────────▼───────────────────────────────┐
│              Controller Framework 1.0.31                  │
│                                                          │
│ Command · Request · DomainObject · Mapper                 │
│ Registry · Sessions · CSRF · AccessToken                 │
│ ErrorHandler · Rendering · Audit                         │
└──────────────────────────────────────────────────────────┘</div>

<p>The Composer package declares PHP <code>^8.3</code>, <code>samoscon/controller-framework ^1.0.31</code> and the Mollie API package. Mollie remains an optional application-level integration even though its package is available to the framework.</p>

<p>Version 1.1.0 extends the domain model with persistent tickets and ticket scans while retaining the existing Activity and Member hierarchical model.</p>

<h2 id="release">2. Release 1.1.0 Technical Changes</h2>

<p>Version 1.1.0 introduces the following principal technical changes compared with 1.0.31.</p>

<table>
<tr>
<th>Area</th>
<th>Version 1.1.0 change</th>
</tr>
<tr>
<td>Ticket domain</td>
<td>Introduces persistent <code>Ticket</code> objects and ticket persistence.</td>
</tr>
<tr>
<td>Ticket scanning</td>
<td>Introduces <code>TicketScan</code> objects and scan persistence.</td>
</tr>
<tr>
<td>Ticket security</td>
<td>Introduces an activity-specific <code>ticket_scan</code> AccessToken purpose.</td>
</tr>
<tr>
<td>Seat reservations</td>
<td>Adds server-side conflict detection before a subscription is created.</td>
</tr>
<tr>
<td>Google Wallet</td>
<td>Wallet ticket generation is based on actual Ticket objects rather than subscription quantity.</td>
</tr>
<tr>
<td>Payment status</td>
<td>Payment status changes use the dedicated status mechanism rather than generic payment editing.</td>
</tr>
<tr>
<td>Remember tokens</td>
<td><code>remember_tokens.expires_at</code> is nullable.</td>
</tr>
<tr>
<td>Mail queue</td>
<td><code>subject</code>, <code>body</code> and <code>recipient</code> are nullable.</td>
</tr>
<tr>
<td>Composite model</td>
<td>Both Activity and Member explicitly participate in recursive parent-child relationships.</td>
</tr>
</table>

<h3>2.1 Ticket domain model</h3>

<p>In version 1.0.31, the quantity of tickets was represented by the <code>quantity</code> property of a subscription. Version 1.1.0 introduces an individual persistent Ticket entity.</p>

<div class="diagram">Version 1.0.31

Subscription
│
└── quantity = 3

Version 1.1.0

Subscription
│
├── Ticket
├── Ticket
└── Ticket</div>

<p>This allows individual tickets to have their own token, state, scan history and identity.</p>

<h3>2.2 Ticket scanning</h3>

<p>The new <code>TicketScan</code> model records an attempt to scan a ticket. The framework can distinguish valid, already-used, cancelled, invalid and wrong-activity results.</p>

<h3>2.3 Seat reservation conflict detection</h3>

<p>Seat availability is no longer only a property of the seat map displayed to the user. The server performs a final check against existing reservations when the subscription is submitted.</p>

<h3>2.4 Google Wallet</h3>

<p>Google Wallet integration now works from persistent ticket entities. Each ticket can therefore be represented independently in Google Wallet.</p>

<h2 id="packages">3. Package Structure</h2>

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
│   │   ├── SubscriptionValidationStrategy.php
│   │   ├── Ticket.php
│   │   ├── TicketMapper.php
│   │   ├── TicketScan.php
│   │   ├── TicketScanMapper.php
│   │   ├── TicketScanTypeImplementation.php
│   │   └── TicketTypeImplementation.php
│   └── wallet/
│       └── GoogleWalletTicket.php</code></pre>

<p>The <code>Member</code> model remains supplied by the Controller Framework and is normally specialized by the client application.</p>

<h2 id="patterns">4. Architectural Patterns</h2>

<table>
<tr><th>Pattern</th><th>Where used</th><th>Purpose</th></tr>
<tr><td>Active Record / Domain Object</td><td>Activity, Costitem, Payment, Subscription, Ticket, TicketScan; Member comes from Controller Framework</td><td>Domain objects represent persisted application entities.</td></tr>
<tr><td>Data Mapper</td><td>ActivityMapper, CostitemMapper, PaymentMapper, SubscriptionMapper, TicketMapper, TicketScanMapper</td><td>Separates persistence operations from domain objects.</td></tr>
<tr><td>Abstract Factory</td><td><code>getInstance()</code> implementations</td><td>Creates concrete client model objects from database rows.</td></tr>
<tr><td>Builder / Type implementation</td><td>ActivityTypeImplementation, CostitemTypeImplementation, PaymentTypeImplementation, SubscriptionTypeImplementation, TicketTypeImplementation, TicketScanTypeImplementation</td><td>Associates a domain object with classification-specific behaviour.</td></tr>
<tr><td>Strategy</td><td>SubscriptionValidationStrategy</td><td>Allows client applications to define subscription rules independently of the generic command.</td></tr>
<tr><td>Decorator</td><td>CommandDecorator in Controller Framework</td><td>Adds client-specific command behaviour without modifying framework commands.</td></tr>
<tr><td>Composite</td><td>ActivityComposite and Member parent-child hierarchy</td><td>Represents recursive domain hierarchies using objects of the same domain type.</td></tr>
<tr><td>Atomic state transition</td><td>Ticket claiming</td><td>Prevents two concurrent scanners from successfully claiming the same ticket.</td></tr>
</table>

<h2 id="object-model">5. Object Model Overview</h2>

<div class="diagram">                         ┌───────────────────┐
                         │       Member      │
                         │ Controller FW     │
                         └─────────┬─────────┘
                                   │
                     parent-child │
                                   │
                         ┌─────────▼─────────┐
                         │   Child Member    │
                         └───────────────────┘

Member
│
├── Payment
│
└── Subscription
│
├── Costitem
│      │
│      └── Activity
│             │
│             ├── Child Activity
│             └── Child Activity
│
└── Ticket
│
└── TicketScan</div>

<h3>5.1 Domain relationships</h3>

<div class="diagram">Member
  │
  ├── parent_id ─────────► Member
  │
  ├── 1 ──────────────── * Payment
  │
  └── 1 ──────────────── * Subscription
                              │
                              ├── Costitem
                              │      │
                              │      └── Activity
                              │
                              ├── Payment
                              │
                              └── 1 ──────────────── * Ticket
                                                        │
                                                        └── 1 ─── * TicketScan</div>

<h3>5.2 Composite relationships</h3>

<p>Both Activity and Member use a recursive parent-child relationship.</p>

<div class="diagram">Activity Composite

Activity
│
├── Activity
│     ├── Activity
│     └── Activity
│
└── Activity

Member Composite

Member
│
├── Member
│     ├── Member
│     └── Member
│
└── Member</div>

<p>The important design principle is that the parent and child are instances of the same domain type. This allows client code to work with individual objects and composite objects through a consistent abstraction.</p>

<h2 id="activity">6. Activity Model</h2>

<p><code>membersactivities\model\activities\Activity</code> is an abstract domain object extending the Controller Framework's <code>DomainObject</code>.</p>

<h3>Responsibilities</h3>

<ul>
<li>Instantiate the concrete client activity class based on the database row.</li>
<li>Attach an activity type implementation based on <code>classification</code>.</li>
<li>Resolve a parent activity when <code>parent_id</code> is present.</li>
<li>Return participants.</li>
<li>Determine whether the subscription period is over.</li>
<li>Calculate the total amount received for the activity.</li>
<li>Support the Activity Composite hierarchy.</li>
</ul>

<h3>Public extension point</h3>

<pre><code>public ?ActivityTypeImplementation
    $activitytypeimplementation = null;</code></pre>

<h3>Important methods</h3>

<table>
<tr><th>Method</th><th>Return</th><th>Purpose</th></tr>
<tr><td><code>getInstance(array $row)</code></td><td>Activity</td><td>Creates a concrete activity or ActivityComposite and attaches its type implementation.</td></tr>
<tr><td><code>getParticipants()</code></td><td>ObjectMap</td><td>Returns paid participants, including participants inherited through the activity hierarchy.</td></tr>
<tr><td><code>subscriptionPeriodOver()</code></td><td>bool</td><td>Checks the activity due date and, for composites, relevant child activities.</td></tr>
<tr><td><code>getTotalAmountReceived()</code></td><td>float</td><td>Sums distinct paid payments associated with cost items of the activity.</td></tr>
</table>

<h3>6.1 ActivityComposite</h3>

<p><code>ActivityComposite</code> extends <code>Activity</code> and implements the Composite pattern.</p>

<p>Children are loaded through the Activity mapper. The composite therefore represents a parent Activity while retaining the same base domain type.</p>

<div class="diagram">ActivityComposite
      │
      ├── Activity
      ├── Activity
      └── Activity</div>

<p>This permits an application to treat a single activity and a hierarchy of activities consistently where appropriate.</p>

<p>For a composite, <code>subscriptionPeriodOver()</code> evaluates the relevant child activities.</p>

<h2 id="member">7. Member Model</h2>

<p>Member is supplied by the Controller Framework and specialized by the client application, normally as:</p>

<pre><code>class Member extends \controllerframework\members\Member
{
    // client-specific behaviour
}</code></pre>

<h3>7.1 Member Composite</h3>

<p>MembersActivities Framework 1.1.0 explicitly uses the same Composite principle for members.</p>

<p>The reference schema supports member groups through <code>parent_id</code>. A Member can therefore act as a parent while one or more other Member objects refer to it as their parent.</p>

<div class="diagram">Member
   │
   ├── Member
   ├── Member
   └── Member</div>

<p>The relationship is recursive. A child Member is itself a Member and can therefore have its own children where the application permits this.</p>

<p>This design allows client applications to represent structures such as households or other application-specific member hierarchies without introducing a separate object type for each hierarchy level.</p>

<table>
<tr><th>Property</th><th>Meaning</th></tr>
<tr><td>name / lastname</td><td>Member identity</td></tr>
<tr><td>email</td><td>Contact/login email</td></tr>
<tr><td>role</td><td>User or administrator role</td></tr>
<tr><td>password</td><td>Stored password hash managed by authentication infrastructure</td></tr>
<tr><td>active</td><td>Application membership state</td></tr>
<tr><td>subscriptionuntil</td><td>Membership validity date</td></tr>
<tr><td>parent_id</td><td>Optional parent Member relationship</td></tr>
</table>

<h3>7.2 Composite design principle</h3>

<p>The Activity and Member models intentionally follow the same object-oriented principle:</p>

<table>
<tr>
<th>Domain</th>
<th>Parent</th>
<th>Child</th>
<th>Relationship</th>
</tr>
<tr>
<td>Activity</td>
<td>Activity</td>
<td>Activity</td>
<td>Parent Activity → Child Activities</td>
</tr>
<tr>
<td>Member</td>
<td>Member</td>
<td>Member</td>
<td>Parent Member → Child Members</td>
</tr>
</table>

<p>In both cases the parent is not a different domain type. It is a domain object containing references to objects of the same type.</p>

<div class="note">
<strong>Implementation principle:</strong> client code should not assume that an Activity or Member is always a leaf object. Business logic that processes these objects should take possible parent-child relationships into account.
</div>

<h2 id="costitem">8. Costitem Model</h2>

<p><code>Costitem</code> represents a subscribable item belonging to an activity.</p>

<pre><code>public ?CostitemTypeImplementation
    $costitemtypeimplementation = null;</code></pre>

<p>During <code>getInstance()</code>, the framework:</p>

<ol>
<li>creates the concrete client Costitem class;</li>
<li>initializes database properties;</li>
<li>creates the classification-specific type implementation;</li>
<li>loads the related Activity when <code>activity_id</code> is present.</li>
</ol>

<table>
<tr><th>Database property</th><th>Meaning</th></tr>
<tr><td>id</td><td>Identifier</td></tr>
<tr><td>description</td><td>Human-readable item description</td></tr>
<tr><td>classification</td><td>Type implementation selector</td></tr>
<tr><td>price</td><td>Unit price</td></tr>
<tr><td>type</td><td>Application-defined cost item type</td></tr>
<tr><td>activity_id</td><td>Owning activity</td></tr>
</table>

<h2 id="subscription">9. Subscription Model</h2>

<p><code>Subscription</code> represents a registration by a member for a cost item.</p>

<pre><code>public ?SubscriptionTypeImplementation
    $subscriptiontypeimplementation = null;</code></pre>

<p>When instantiated, the framework resolves:</p>

<pre><code>subscription.member
subscription.costitem
subscription.payment
subscriptiontypeimplementation</code></pre>

<p>The payment relation is optional because a subscription can exist before payment has been associated with it.</p>

<h3>9.1 Ticket relationship</h3>

<p>In version 1.1.0 a subscription can have multiple persistent Ticket objects.</p>

<div class="diagram">Subscription
   │
   ├── member
   ├── costitem
   ├── payment
   │
   └── tickets
         ├── Ticket
         ├── Ticket
         └── Ticket</div>

<p>The historical <code>quantity</code> concept should therefore no longer be interpreted as the complete ticket identity model. Individual Ticket records provide the persistent identity of each ticket.</p>

<h2 id="payment">10. Payment Model</h2>

<p><code>Payment</code> represents a financial transaction and extends the Controller Framework's <code>DomainObject</code>.</p>

<pre><code>public ?PaymentTypeImplementation
    $paymenttypeimplementation = null;</code></pre>

<table>
<tr><th>Method</th><th>Purpose</th></tr>
<tr><td><code>getInstance()</code></td><td>Creates the concrete client payment type and loads the member.</td></tr>
<tr><td><code>delete()</code></td><td>Deletes associated subscriptions and then the payment inside a database transaction.</td></tr>
<tr><td><code>isPaid()</code></td><td>Returns <code>true</code> when status is exactly <code>paid</code>.</td></tr>
<tr><td><code>statusReceived()</code></td><td>Creates the payment status processing flow and delegates status-specific behaviour to the payment type implementation.</td></tr>
</table>

<p>The transaction in <code>Payment::delete()</code> ensures that deletion of the payment and its associated subscriptions is committed atomically; an exception causes a rollback.</p>

<div class="note">
<strong>Payment status:</strong> client applications should use the dedicated payment status mechanism rather than treating payment status as an ordinary editable field in a generic update operation.
</div>

<h2 id="ticket">11. Ticket Model</h2>

<p><code>membersactivities\model\subscriptions\Ticket</code> is a persistent domain object representing an individual ticket.</p>

<h3>11.1 Responsibilities</h3>

<ul>
<li>Represent one individual ticket.</li>
<li>Maintain a unique ticket token.</li>
<li>Maintain ticket status.</li>
<li>Track whether the ticket has been used.</li>
<li>Record the time at which the ticket was used.</li>
<li>Provide atomic claiming of a ticket.</li>
<li>Participate in Google Wallet integration.</li>
</ul>

<h3>11.2 Ticket properties</h3>

<table>
<tr><th>Property</th><th>Purpose</th></tr>
<tr><td><code>id</code></td><td>Persistent ticket identifier.</td></tr>
<tr><td><code>description</code></td><td>Ticket description.</td></tr>
<tr><td><code>classification</code></td><td>Ticket type implementation selector.</td></tr>
<tr><td><code>subscription_id</code></td><td>Owning subscription.</td></tr>
<tr><td><code>token</code></td><td>Unique externally usable ticket token.</td></tr>
<tr><td><code>status</code></td><td>Ticket state, such as <code>valid</code> or <code>cancelled</code>.</td></tr>
<tr><td><code>used</code></td><td>Indicates whether the ticket has already been claimed.</td></tr>
<tr><td><code>used_at</code></td><td>Timestamp of successful ticket claim.</td></tr>
<tr><td><code>created_at</code></td><td>Creation timestamp.</td></tr>
</table>

<h3>11.3 Ticket lookup</h3>

<p>Tickets are identified by a unique token when used by external clients such as a scanner.</p>

<pre><code>$ticket = \model\Ticket::findByToken($token);</code></pre>

<p>The token should be treated as untrusted input and must always be validated on the server.</p>

<h3>11.4 Atomic ticket claim</h3>

<p>The Ticket model provides an atomic claim operation:</p>

<pre><code>$success = $ticket->claim();</code></pre>

<p>The operation is successful only when the ticket is still valid and unused.</p>

<div class="diagram">Scanner A ───────┐
                 │
                 ▼
             claimTicket()
                 │
                 ├── used = 0 ──► SUCCESS
                 │
                 └── used = 1 ──► FAILURE
                 ▲
                 │
Scanner B ───────┘</div>

<p>This protects the ticketing system against simultaneous scans from different devices.</p>

<h3>11.5 Ticket type implementation</h3>

<pre><code>TicketTypeImplementation</code></pre>

<p>The ticket <code>classification</code> determines the concrete ticket behaviour. The default classification is <code>RGLR</code>.</p>

<h2 id="ticketscan">12. TicketScan Model</h2>

<p><code>TicketScan</code> represents an individual attempt to scan or validate a Ticket.</p>

<h3>12.1 Responsibilities</h3>

<ul>
<li>Record a ticket scan.</li>
<li>Associate the scan with a Ticket.</li>
<li>Identify the scanner.</li>
<li>Record the scan timestamp.</li>
<li>Record the result of the scan.</li>
</ul>

<table>
<tr><th>Property</th><th>Purpose</th></tr>
<tr><td><code>id</code></td><td>Persistent scan identifier.</td></tr>
<tr><td><code>description</code></td><td>Scan description.</td></tr>
<tr><td><code>classification</code></td><td>Scan type implementation selector.</td></tr>
<tr><td><code>ticket_id</code></td><td>Ticket being scanned.</td></tr>
<tr><td><code>scanner</code></td><td>Identifier of the scanning device/application.</td></tr>
<tr><td><code>scanned_at</code></td><td>Timestamp of the scan.</td></tr>
<tr><td><code>result</code></td><td>Validation result.</td></tr>
</table>

<h3>12.2 Scan result values</h3>

<pre><code>valid
already_used
cancelled
invalid
wrong_activity</code></pre>

<p>The exact presentation of these values is a client application concern, but the server remains authoritative for determining the result.</p>

<h3>12.3 TicketScan type implementation</h3>

<pre><code>TicketScanTypeImplementation</code></pre>

<p>The default classification is <code>RGLR</code>. Client applications may provide specialized scan behaviour through their own type implementation.</p>

<h2 id="wallet">13. Google Wallet Model</h2>

<p><code>GoogleWalletTicket</code> encapsulates Google Wallet integration.</p>

<table>
<tr><th>Method</th><th>Purpose</th></tr>
<tr><td><code>auth()</code></td><td>Initializes Google API authentication.</td></tr>
<tr><td><code>createClass()</code></td><td>Creates a Wallet class.</td></tr>
<tr><td><code>updateClass()</code></td><td>Updates a Wallet class.</td></tr>
<tr><td><code>createObject()</code></td><td>Creates a Wallet ticket object.</td></tr>
<tr><td><code>updateObject()</code></td><td>Updates a Wallet ticket object.</td></tr>
<tr><td><code>createJwt(int $id)</code></td><td>Creates the JWT used to add the ticket to Google Wallet.</td></tr>
</table>

<h3>13.1 Ticket-based Wallet objects</h3>

<p>Version 1.1.0 creates Wallet objects from persistent Ticket records.</p>

<div class="diagram">Subscription
     │
     ├── Ticket 152 ─────► Wallet Object 152
     ├── Ticket 153 ─────► Wallet Object 153
     └── Ticket 154 ─────► Wallet Object 154</div>

<p>This replaces the previous model where ticket instances were derived directly from subscription quantity.</p>

<h2 id="mappers">14. Mapper Layer</h2>

<p>Version 1.1.0 contains the following domain mappers:</p>

<pre><code>ActivityMapper
CostitemMapper
SubscriptionMapper
PaymentMapper
TicketMapper
TicketScanMapper</code></pre>

<table>
<tr>
<th>Mapper</th>
<th>Table</th>
<th>Framework-specific fields</th>
</tr>
<tr>
<td>ActivityMapper</td>
<td>activity</td>
<td>date, duedate, longdescription, start, end, location, parent_id</td>
</tr>
<tr>
<td>CostitemMapper</td>
<td>costitem</td>
<td>price, type, activity_id</td>
</tr>
<tr>
<td>SubscriptionMapper</td>
<td>subscription</td>
<td>member_id, costitem_id, payment_id, quantity, remark</td>
</tr>
<tr>
<td>PaymentMapper</td>
<td>payment</td>
<td>member_id, date, amount, status, type, source</td>
</tr>
<tr>
<td>TicketMapper</td>
<td>ticket</td>
<td>subscription_id, token, status, used, used_at</td>
</tr>
<tr>
<td>TicketScanMapper</td>
<td>ticket_scan</td>
<td>ticket_id, scanner, scanned_at, result</td>
</tr>
</table>

<p>Each mapper extends the Controller Framework <code>Mapper</code> and defines an allowed-field whitelist. This is important when performing update/insert operations through the persistence layer.</p>

<h3>14.1 TicketMapper</h3>

<p>The TicketMapper provides ticket-specific persistence operations including token lookup and atomic ticket claiming.</p>

<pre><code>findByToken(string $token): ?Ticket

claimTicket(int $ticketId): bool</code></pre>

<p>The atomic claim operation must be implemented at database level so that concurrent scanners cannot both successfully change the same ticket from unused to used.</p>

<h3>14.2 TicketScanMapper</h3>

<p>The TicketScanMapper persists scan records associated with tickets.</p>

<pre><code>TicketScan
    │
    └── ticket_id ───► Ticket</code></pre>

<div class="note">
<strong>Important:</strong> <code>Mapper::findAll()</code> accepts a free SQL select clause. Client code must never concatenate untrusted request data into such a clause.
</div>

<h2 id="commands">15. Command Layer</h2>

<p>The framework commands are grouped by responsibility.</p>

<table>
<tr><th>Package</th><th>Commands</th></tr>
<tr><td>admin</td><td>Create, edit, delete and composite-management operations for activities, cost items, members and payments; administration and member search.</td></tr>
<tr><td>downloads</td><td>Member and participant XLS exports.</td></tr>
<tr><td>mollie</td><td>Generic Mollie payment creation and webhook processing.</td></tr>
<tr><td>user</td><td>Activity registration, payment creation, payment confirmation and public/user activity flows.</td></tr>
</table>

<p>Ticket scanning may be exposed through client-specific commands or through framework commands depending on the client application's routing and scanner architecture.</p>

<p>Commands are intended to be connected to routes through the Controller Framework configuration, normally <code>controls.xml</code>.</p>

<h2 id="decorators">16. Command Decorators</h2>

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

<p>This is especially important for validation strategies, payment descriptions and client-specific ticket or scanner behaviour.</p>

<h2 id="strategies">17. Strategy and Type Implementations</h2>

<h3>17.1 Type implementations</h3>

<pre><code>ActivityTypeImplementation
CostitemTypeImplementation
PaymentTypeImplementation
SubscriptionTypeImplementation
TicketTypeImplementation
TicketScanTypeImplementation</code></pre>

<p>The classification field determines the concrete implementation.</p>

<p>For example:</p>

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

<h3>17.2 Ticket type</h3>

<pre><code>class Ticket_RGLR
    extends \membersactivities\model\subscriptions\TicketTypeImplementation
{
    // regular ticket behaviour
}</code></pre>

<h3>17.3 TicketScan type</h3>

<pre><code>class TicketScan_RGLR
    extends \membersactivities\model\subscriptions\TicketScanTypeImplementation
{
    // regular scanner behaviour
}</code></pre>

<p>The client application should use type implementations when behaviour genuinely depends on classification rather than adding classification-specific branching throughout the generic framework.</p>

<h2 id="validation">18. Subscription Validation</h2>

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

<h2 id="request">19. Request / Response Flow</h2>

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

<h2 id="security">20. Security Architecture</h2>

<h3>20.1 Authentication</h3>

<p>Authentication and login levels are supplied by the Controller Framework. Commands should explicitly define whether they require no login, a user login or administrator login.</p>

<h3>20.2 CSRF</h3>

<p>State-changing operations should use POST and validate the CSRF token before modifying data.</p>

<h3>20.3 AccessToken</h3>

<p>Controller Framework 1.0.31 supplies <code>AccessToken</code>. MembersActivities uses it for protected payment operations and version 1.1.0 also uses a dedicated token purpose for ticket scanning.</p>

<pre><code>AccessToken::generate(
    'mollie-order',
    (string) $paymentId
);

AccessToken::generate(
    'ticket_scan',
    (string) $activityId
);</code></pre>

<p>The <code>_SALTRAND</code> secret used by the AccessToken mechanism must remain confidential and must not be stored in public source code.</p>

<h3>20.4 Ticket scanner security</h3>

<p>The ticket scanning endpoint should validate the activity-specific <code>ticket_scan</code> token before processing ticket tokens.</p>

<div class="diagram">Scanner
   │
   ├── activity ID
   ├── ticket_scan AccessToken
   └── ticket token
          │
          ▼
       Server
          │
          ├── validate AccessToken
          ├── find Ticket
          ├── verify activity
          ├── verify status
          └── claim atomically</div>

<h3>20.5 Input validation</h3>

<p>Request parameters are untrusted. Validate IDs, email addresses, file names, redirects, query parameters, ticket tokens and all other externally supplied values according to their intended type and context.</p>

<h2 id="payments">21. Payment and Mollie Flow</h2>

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

<h2 id="ticketscan-flow">22. Ticket Scanning Flow</h2>

<div class="diagram">Ticket scanner
      │
      │ ticket token
      │ activity token
      ▼
Ticket scan endpoint
      │
      ├── validate ticket_scan AccessToken
      │
      ├── find Ticket by token
      │
      ├── verify Ticket exists
      │
      ├── verify Ticket belongs to requested activity
      │
      ├── verify Ticket status
      │
      └── atomic claim
              │
       ┌──────┴─────────┐
       │                │
       ▼                ▼
   accepted           rejected
       │                │
       └───────┬────────┘
               ▼
          TicketScan
               │
               ▼
        scan result</div>

<h3>22.1 Correctness requirements</h3>

<p>The scanner must not consider a ticket valid merely because the token exists.</p>

<p>The server should verify:</p>

<ol>
<li>the scanning AccessToken is valid;</li>
<li>the Ticket exists;</li>
<li>the Ticket belongs to the relevant activity;</li>
<li>the Ticket has a valid status;</li>
<li>the Ticket has not already been used;</li>
<li>the atomic claim operation succeeds.</li>
</ol>

<p>The final atomic claim is the authoritative concurrency protection.</p>

<h2 id="seats">23. Seat Reservation Consistency</h2>

<p>Applications using seat maps must consider the difference between displayed availability and actual database state.</p>

<div class="diagram">Seat map displayed
       │
       ▼
User selects seat
       │
       ▼
Other user may reserve same seat
       │
       ▼
Current subscription submitted
       │
       ▼
Server checks current reservations
       │
       ├── available ───► continue
       │
       └── conflict ────► reject / retry</div>

<p>The seat conflict check therefore belongs at the server-side subscription creation boundary rather than only in the JavaScript seat map.</p>

<p>Client-side seat maps improve usability, but they are not authoritative for reservation consistency.</p>

<h2 id="database">24. Database Model</h2>

<table>
<tr>
<th>Table</th>
<th>Primary purpose</th>
<th>Important foreign keys</th>
</tr>
<tr>
<td>activity</td>
<td>Activities and activity hierarchy</td>
<td>parent_id → activity.id</td>
</tr>
<tr>
<td>member</td>
<td>Members and member hierarchy</td>
<td>parent_id → member.id</td>
</tr>
<tr>
<td>costitem</td>
<td>Subscribable items/prices</td>
<td>activity_id → activity.id</td>
</tr>
<tr>
<td>payment</td>
<td>Financial transactions</td>
<td>member_id → member.id</td>
</tr>
<tr>
<td>subscription</td>
<td>Member registration for a cost item</td>
<td>member_id, costitem_id, payment_id</td>
</tr>
<tr>
<td>ticket</td>
<td>Individual persistent tickets</td>
<td>subscription_id → subscription.id</td>
</tr>
<tr>
<td>ticket_scan</td>
<td>Ticket scanning history</td>
<td>ticket_id → ticket.id</td>
</tr>
<tr>
<td>remember_tokens</td>
<td>Remember-me authentication tokens</td>
<td>member-related authentication data</td>
</tr>
<tr>
<td>mail_queue</td>
<td>Asynchronous email processing</td>
<td>application process data</td>
</tr>
</table>

<h3>24.1 Relationship detail</h3>

<div class="diagram">activity 1 ─────────── * costitem
activity 1 ─────────── * child activity

member 1 ───────────── * subscription
member 1 ───────────── * payment
member 1 ───────────── * child member

costitem 1 ─────────── * subscription

payment 1 ──────────── * subscription

subscription 1 ─────── * ticket

ticket 1 ───────────── * ticket_scan</div>

<h3>24.2 Ticket schema</h3>

<div class="diagram">ticket
 ├── id
 ├── description
 ├── classification
 ├── subscription_id
 ├── token
 ├── status
 ├── used
 ├── used_at
 └── created_at</div>

<h3>24.3 Ticket scan schema</h3>

<div class="diagram">ticket_scan
 ├── id
 ├── description
 ├── classification
 ├── ticket_id
 ├── scanner
 ├── scanned_at
 └── result</div>

<h3>24.4 Other 1.1.0 database changes</h3>

<p><code>remember_tokens.expires_at</code> is nullable.</p>

<p>The following <code>mail_queue</code> fields are nullable:</p>

<pre><code>subject
body
recipient</code></pre>

<p>The reference schema uses InnoDB, utf8mb4 and foreign key constraints. It is intended as a starting schema for a new client application rather than a production migration script.</p>

<h2 id="transactions">25. Transactions and Consistency</h2>

<p>Payment deletion is implemented transactionally: related subscriptions are deleted first, then the payment itself is deleted; an exception rolls the operation back.</p>

<h3>25.1 Ticket claiming</h3>

<p>Ticket claiming is a different form of consistency requirement. The important property is not a multi-statement business transaction but an atomic conditional state transition.</p>

<p>Conceptually:</p>

<pre><code>UPDATE ticket
SET
    used = 1,
    used_at = CURRENT_TIMESTAMP
WHERE
    id = :ticket_id
    AND status = 'valid'
    AND used = 0;</code></pre>

<p>The caller can determine whether the claim succeeded from the affected-row result.</p>

<p>This prevents two concurrent scanners from both receiving a successful claim for the same ticket.</p>

<h3>25.2 Seat reservation consistency</h3>

<p>Seat reservation validation must similarly be performed against current server-side database state immediately before the subscription is persisted.</p>

<p>For client-specific multi-step business operations, transactions should be considered whenever several related database changes must succeed or fail as one logical operation.</p>

<h2 id="mail">26. Mail Queue</h2>

<p>The <code>mail_queue</code> table supports asynchronous mail processing.</p>

<p>A typical lifecycle is:</p>

<div class="diagram">pending
  │
  ▼
sending
  │
  ├────────► sent
  │
  └────────► failed</div>

<p>The queue records processing information such as attempts, creation/start/send timestamps and an error field.</p>

<p>Version 1.1.0 allows <code>subject</code>, <code>body</code> and <code>recipient</code> to be nullable, which permits the queue to represent records whose mail content is completed at a later processing stage.</p>

<p>A cron command can process pending messages and clean up old sent records.</p>

<h2 id="extension">27. Extension Guide</h2>

<h3>27.1 Add a new activity type</h3>

<ol>
<li>Create a client <code>Activity</code> subclass if not already present.</li>
<li>Create <code>Activity_XXXX</code> extending <code>ActivityTypeImplementation</code>.</li>
<li>Store <code>XXXX</code> in the activity classification field.</li>
<li>Add client logic only where the type needs special behaviour.</li>
</ol>

<h3>27.2 Add a new payment type</h3>

<ol>
<li>Create a client <code>Payment</code> subclass.</li>
<li>Create <code>Payment_XXXX</code> extending <code>PaymentTypeImplementation</code>.</li>
<li>Implement <code>statusReceived()</code> when payment status changes require client-specific processing.</li>
</ol>

<h3>27.3 Add a new ticket type</h3>

<ol>
<li>Create a client <code>Ticket</code> subclass if required.</li>
<li>Create <code>Ticket_XXXX</code> extending <code>TicketTypeImplementation</code>.</li>
<li>Store <code>XXXX</code> in the ticket classification field.</li>
<li>Implement only ticket-specific behaviour.</li>
</ol>

<h3>27.4 Add a new TicketScan type</h3>

<ol>
<li>Create a client <code>TicketScan</code> subclass if required.</li>
<li>Create <code>TicketScan_XXXX</code> extending <code>TicketScanTypeImplementation</code>.</li>
<li>Store <code>XXXX</code> in the scan classification field.</li>
<li>Implement scanner-specific behaviour.</li>
</ol>

<h3>27.5 Implement a Member hierarchy</h3>

<p>Member hierarchy support is based on the existing <code>parent_id</code> self-reference.</p>

<pre><code>Parent Member
     │
     ├── Child Member
     └── Child Member</code></pre>

<p>Client-specific code should treat the relationship as a domain hierarchy rather than introducing a separate "MemberGroup" object unless the application's business requirements genuinely require one.</p>

<h3>27.6 Add client-specific subscription validation</h3>

<ol>
<li>Create a class extending <code>SubscriptionValidationStrategy</code>.</li>
<li>Implement the application-specific validation rules.</li>
<li>Inject the strategy through a controlled CommandDecorator.</li>
</ol>

<h3>27.7 Add a new command around an existing command</h3>

<ol>
<li>Create a client CommandDecorator.</li>
<li>Set the wrapped framework command in <code>initCommand()</code>.</li>
<li>Use <code>doExecuteDecorator()</code> for client-specific preparation.</li>
<li>Define the required authentication level.</li>
<li>Register the route in <code>controls.xml</code>.</li>
</ol>

<h2 id="migration">28. Migration from 1.0.31</h2>

<p>Version 1.1.0 introduces new persistent domain entities and therefore requires more than a Composer version change for applications using the new functionality.</p>

<h3>28.1 Composer</h3>

<pre><code>{
    "require": {
        "samoscon/membersactivities-framework": "^1.1.0"
    }
}</code></pre>

<p>The Controller Framework remains at version 1.0.31.</p>

<h3>28.2 Database migration</h3>

<p>Applications should apply controlled database changes for:</p>

<ul>
<li><code>ticket</code>;</li>
<li><code>ticket_scan</code>;</li>
<li>nullable <code>remember_tokens.expires_at</code>;</li>
<li>nullable mail queue fields.</li>
</ul>

<h3>28.3 Existing subscriptions</h3>

<p>In version 1.0.31, multiple tickets could be represented by a subscription's <code>quantity</code>. Version 1.1.0 introduces individual Ticket records.</p>

<div class="diagram">Before:

Subscription
│
└── quantity = 3

After:

Subscription
│
├── Ticket
├── Ticket
└── Ticket</div>

<p>Applications using the ticket functionality should migrate existing relevant subscriptions to individual Ticket records.</p>

<p>The migration must generate unique ticket tokens and preserve any client-specific ticket information.</p>

<h3>28.4 Google Wallet migration</h3>

<p>Applications using Google Wallet should ensure that the required Ticket records exist before using the 1.1.0 Wallet implementation.</p>

<h3>28.5 Ticket scanner migration</h3>

<p>Applications introducing ticket scanning should implement:</p>

<ul>
<li>activity-specific <code>ticket_scan</code> AccessToken generation;</li>
<li>server-side ticket-token lookup;</li>
<li>activity verification;</li>
<li>atomic ticket claiming;</li>
<li>scan-result persistence where required.</li>
</ul>

<div class="note">
<strong>Migration principle:</strong> framework upgrades and client data migrations should be treated as separate controlled steps. Do not replace an existing production database with the reference database schema.
</div>

<h2 id="api">29. API / Class Reference</h2>

<table>
<tr>
<th>Class</th>
<th>Type</th>
<th>Key API</th>
</tr>

<tr>
<td><code>membersactivities\model\activities\Activity</code></td>
<td>abstract model</td>
<td>getInstance, getParticipants, subscriptionPeriodOver, getTotalAmountReceived</td>
</tr>

<tr>
<td><code>membersactivities\model\activities\ActivityComposite</code></td>
<td>composite model</td>
<td>getChildren, isComposite, subscriptionPeriodOver</td>
</tr>

<tr>
<td><code>controllerframework\members\Member</code></td>
<td>domain model / composite root</td>
<td>parent-child member relationship</td>
</tr>

<tr>
<td><code>membersactivities\model\activities\Costitem</code></td>
<td>abstract model</td>
<td>getInstance</td>
</tr>

<tr>
<td><code>membersactivities\model\subscriptions\Subscription</code></td>
<td>abstract model</td>
<td>getInstance</td>
</tr>

<tr>
<td><code>membersactivities\model\subscriptions\Payment</code></td>
<td>abstract model</td>
<td>getInstance, delete, isPaid, statusReceived</td>
</tr>

<tr>
<td><code>membersactivities\model\subscriptions\Ticket</code></td>
<td>domain model</td>
<td>getInstance, findByToken, claim</td>
</tr>

<tr>
<td><code>membersactivities\model\subscriptions\TicketScan</code></td>
<td>domain model</td>
<td>getInstance, scan persistence</td>
</tr>

<tr>
<td><code>membersactivities\model\subscriptions\SubscriptionValidationStrategy</code></td>
<td>strategy</td>
<td>subscribe, errorcode</td>
</tr>

<tr>
<td><code>membersactivities\model\activities\ActivityMapper</code></td>
<td>mapper</td>
<td>getChildren, getParticipants</td>
</tr>

<tr>
<td><code>membersactivities\model\activities\CostitemMapper</code></td>
<td>mapper</td>
<td>persistence / allowed fields</td>
</tr>

<tr>
<td><code>membersactivities\model\subscriptions\SubscriptionMapper</code></td>
<td>mapper</td>
<td>persistence / allowed fields</td>
</tr>

<tr>
<td><code>membersactivities\model\subscriptions\PaymentMapper</code></td>
<td>mapper</td>
<td>persistence / allowed fields</td>
</tr>

<tr>
<td><code>membersactivities\model\subscriptions\TicketMapper</code></td>
<td>mapper</td>
<td>findByToken, claimTicket, persistence / allowed fields</td>
</tr>

<tr>
<td><code>membersactivities\model\subscriptions\TicketScanMapper</code></td>
<td>mapper</td>
<td>scan persistence / allowed fields</td>
</tr>

<tr>
<td><code>membersactivities\model\activities\ActivityTypeImplementation</code></td>
<td>type implementation</td>
<td>seatmap and activity-specific extension points</td>
</tr>

<tr>
<td><code>membersactivities\model\activities\CostitemTypeImplementation</code></td>
<td>type implementation</td>
<td>extension point</td>
</tr>

<tr>
<td><code>membersactivities\model\subscriptions\SubscriptionTypeImplementation</code></td>
<td>type implementation</td>
<td>extension point</td>
</tr>

<tr>
<td><code>membersactivities\model\subscriptions\PaymentTypeImplementation</code></td>
<td>type implementation</td>
<td>statusReceived, getSubscription, Wallet support</td>
</tr>

<tr>
<td><code>membersactivities\model\subscriptions\TicketTypeImplementation</code></td>
<td>type implementation</td>
<td>ticket-specific extension point</td>
</tr>

<tr>
<td><code>membersactivities\model\subscriptions\TicketScanTypeImplementation</code></td>
<td>type implementation</td>
<td>ticket-scan-specific extension point</td>
</tr>

<tr>
<td><code>membersactivities\model\wallet\GoogleWalletTicket</code></td>
<td>integration service</td>
<td>auth, create/update class/object, createJwt</td>
</tr>
</table>

<h2 id="checklist">30. Technical Review Checklist</h2>

<ul class="check">

<li>MembersActivities Framework version is 1.1.0.</li>

<li>Controller Framework version is 1.0.31.</li>

<li>PHP version satisfies the package requirement.</li>

<li>PDO MySQL/MariaDB is configured correctly.</li>

<li>Client model classes extend the appropriate framework classes.</li>

<li>Classification values map to existing client type implementations.</li>

<li>Activity parent-child relationships are handled correctly.</li>

<li>Member parent-child relationships are handled correctly.</li>

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

<li>Ticket records are created for subscriptions where ticket functionality is required.</li>

<li>Ticket tokens are unique and treated as untrusted external input.</li>

<li>Ticket scanning validates the activity-specific AccessToken.</li>

<li>Ticket scanning verifies that the Ticket belongs to the relevant Activity.</li>

<li>Ticket claiming is atomic.</li>

<li>Already-used tickets are rejected.</li>

<li>Cancelled tickets are rejected.</li>

<li>Ticket scan results are recorded where an audit trail is required.</li>

<li>Seat reservations are checked against current server-side state.</li>

<li>Google Wallet objects are generated from actual Ticket records.</li>

<li>Mail queue processing is configured and monitored.</li>

<li>Google Wallet credentials are protected if the integration is enabled.</li>

<li>Existing production data is migrated through controlled database changes rather than replacing the database with the reference schema.</li>

</ul>

<footer>
<p><strong>Reference:</strong> MembersActivities Framework 1.1.0 · Controller Framework 1.0.31</p>

<p><strong>Previous version:</strong> MembersActivities Framework 1.0.31</p>

<p>This technical guide describes the framework source and reference database for Release 1.1.0. Client-specific implementations may add domain behaviour beyond the framework API described here.</p>

<p>The Activity and Member domain models both support recursive parent-child relationships following the Composite design principle.</p>
</footer>

</div>
</body>
</html>
