---
layout: post
author: dirkvm
---

<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Controller Framework 1.0.31 — Technical Reference</title>
<style>
:root {
  --ink:#17202a; --muted:#5d6875; --line:#d9dee5; --panel:#f6f8fa;
  --accent:#2457a6; --codebg:#111827; --codetext:#f3f4f6;
  --warn:#fff7df; --good:#edf8f0;
}
* { box-sizing:border-box; }
body { margin:0; background:#fff; color:var(--ink); font-family:Arial,Helvetica,sans-serif; line-height:1.55; }
main { max-width:1200px; margin:auto; padding:34px 42px 80px; }
header { border-bottom:1px solid var(--line); padding-bottom:24px; margin-bottom:28px; }
h1 { font-size:2.25rem; margin:.1em 0 .3em; }
h2 { margin-top:2.2em; border-bottom:1px solid var(--line); padding-bottom:.3em; }
h3 { margin-top:1.6em; }
h4 { margin-top:1.3em; }
p, li { max-width:100ch; }
.lead { font-size:1.12rem; color:var(--muted); }
.badge { display:inline-block; padding:4px 10px; border-radius:999px; background:#edf2fb; color:var(--accent); font-weight:bold; }
nav { background:var(--panel); border:1px solid var(--line); border-radius:10px; padding:18px 24px; }
nav ol { columns:2; }
a { color:var(--accent); }
code { font-family:Consolas,Monaco,monospace; background:#f1f3f5; padding:1px 4px; border-radius:4px; }
pre {
  background:var(--codebg); color:var(--codetext); padding:17px;
  border-radius:8px; overflow:auto; line-height:1.45; white-space:pre;
}
pre code { background:transparent; color:var(--codetext); padding:0; border-radius:0; }
.diagram { border:1px solid var(--line); border-radius:10px; padding:14px; margin:20px 0; overflow:auto; background:#fff; }
.note,.warning,.success { padding:14px 18px; border-left:5px solid; border-radius:5px; margin:18px 0; }
.note { background:#f3f7ff; border-color:var(--accent); }
.warning { background:var(--warn); border-color:#c48a00; }
.success { background:var(--good); border-color:#39834d; }
table { width:100%; border-collapse:collapse; margin:18px 0 26px; }
th,td { border:1px solid var(--line); padding:8px 10px; vertical-align:top; text-align:left; }
th { background:var(--panel); }
.api { margin:22px 0 30px; border:1px solid var(--line); border-radius:8px; overflow:hidden; }
.api h4 { margin:0; padding:10px 14px; background:var(--panel); font-family:Consolas,Monaco,monospace; }
.api .body { padding:12px 14px; }
.small { color:var(--muted); font-size:.92rem; }
footer { border-top:1px solid var(--line); margin-top:50px; padding-top:20px; color:var(--muted); }
@media print {
  main { max-width:none; padding:0; }
  nav,.diagram,.api,table,.warning,.note,.success { break-inside:avoid; }
  pre { color:#111; background:#f3f3f3; border:1px solid #ccc; white-space:pre-wrap; }
  pre code { color:#111; background:transparent; }
}
@media (max-width:760px) {
  main { padding:22px 18px 50px; }
  nav ol { columns:1; }
}

.uml-overview {
    margin: 2rem 0 2.5rem;
    text-align: center;
}
.uml-overview img {
    display: block;
    width: 100%;
    max-width: 1150px;
    height: auto;
    margin: 0 auto;
    border: 1px solid #d1d5db;
    border-radius: 8px;
}
.uml-overview figcaption {
    margin-top: .75rem;
    font-size: .9rem;
    color: #4b5563;
}

</style>
</head>
<body>
<main>

<header>
<span class="badge">Controller Framework 1.0.31</span>
<h1>Technical Reference</h1>
<p class="lead">PHPDoc-style technical documentation for framework developers and maintainers building client applications on top of Controller Framework 1.0.31.</p>
<p><strong>Source basis:</strong> Release 31 source code, Composer definition and supplied example application. This document describes the actual class hierarchy and object relationships in Release 31 rather than a generic MVC framework.</p>
<p><strong>Related documentation:</strong>
<a href="https://samoscon.github.io/2026/09/11/controller-Framework-Client-Application-Developer-Guide.html">Controller Framework 1.0.31 — Client Application Developer Guide</a>
contains the practical, application-oriented usage guide.</p>
</header>

<nav>
<strong>Contents</strong><ul><li><a href="#uml-overview">UML class diagram overview</a></li></ul>
<ol>
<li><a href="#architecture">Architecture</a></li>
<li><a href="#objectmodels">Object models</a></li>
<li><a href="#controllers">Controllers package</a></li>
<li><a href="#registry">Registry and Request</a></li>
<li><a href="#database">Database object model</a></li>
<li><a href="#members">Member object model</a></li>
<li><a href="#sessions">Session object model</a></li>
<li><a href="#security">Security</a></li>
<li><a href="#mail">Mail</a></li>
<li><a href="#audit">Audit</a></li>
<li><a href="#errors">Error handling</a></li>
<li><a href="#client">Client application contracts</a></li>
<li><a href="#reference">API reference</a></li>
</ol>
</nav>


<section id="uml-overview">
<h2>UML class diagram overview</h2>
<p>
The following diagram gives a high-level UML view of the Controller Framework 1.0.31.
It shows the principal class hierarchies and relationships between the controller,
request, rendering, authentication, security, domain/database, and infrastructure
parts of the framework. The diagram is intended as an orientation map; the detailed
API and class relationships are documented in the sections that follow.
</p>
<figure class="uml-overview"><img src="./assets/Class Diagram Controller Framework.png" alt="Controller Framework 1.0.31 UML class diagram overview"><figcaption> Figure 1 — Controller Framework 1.0.31 UML class diagram overview</figcaption></figure>
<p class="note">
<strong>Reading the diagram:</strong> inheritance is shown through the class
hierarchies, while the main framework collaborations are grouped by functional area.
For detailed attributes, operations, signatures, and contracts, use the corresponding
package sections below.
</p>
</section>

<h2 id="architecture">1. Architecture</h2>
<p>The Application Controller variant of Release 31 is the primary framework configuration used by the supplied example application. The application entry point calls <code>Controller::run()</code>. The framework initializes configuration and the request, compiles <code>controls.xml</code> into descriptors, resolves the Command for the requested path, executes it and finally renders the result.</p>

<div class="diagram">
<svg viewBox="0 0 1120 430" width="100%" role="img" aria-label="Release 31 application controller object model">
<defs><marker id="a" markerWidth="10" markerHeight="10" refX="9" refY="3" orient="auto"><path d="M0,0 L10,3 L0,6 Z" fill="#2457a6"/></marker></defs>
<g font-family="Arial">
<rect x="20" y="175" width="145" height="70" rx="9" fill="#f6f8fa" stroke="#8b98a7"/>
<text x="92" y="204" text-anchor="middle">Browser / CLI</text><text x="92" y="226" text-anchor="middle" font-size="13">Request</text>
<rect x="205" y="145" width="165" height="130" rx="9" fill="#edf2fb" stroke="#2457a6"/>
<text x="287" y="176" text-anchor="middle" font-weight="bold">Controller</text><text x="287" y="199" text-anchor="middle" font-size="13">ErrorHandler</text><text x="287" y="220" text-anchor="middle" font-size="13">InitController</text><text x="287" y="241" text-anchor="middle" font-size="13">HandleRequestController</text>
<rect x="410" y="55" width="190" height="90" rx="9" fill="#f6f8fa" stroke="#8b98a7"/>
<text x="505" y="88" text-anchor="middle" font-weight="bold">Request</text><text x="505" y="111" text-anchor="middle" font-size="13">HttpRequest / CliRequest</text><text x="505" y="130" text-anchor="middle" font-size="13">path + properties + status</text>
<rect x="410" y="180" width="190" height="90" rx="9" fill="#f6f8fa" stroke="#8b98a7"/>
<text x="505" y="213" text-anchor="middle" font-weight="bold">RenderCompiler</text><text x="505" y="236" text-anchor="middle" font-size="13">controls.xml</text><text x="505" y="255" text-anchor="middle" font-size="13">→ Conf of descriptors</text>
<rect x="410" y="305" width="190" height="90" rx="9" fill="#f6f8fa" stroke="#8b98a7"/>
<text x="505" y="338" text-anchor="middle" font-weight="bold">Registry</text><text x="505" y="361" text-anchor="middle" font-size="13">application-wide services</text><text x="505" y="380" text-anchor="middle" font-size="13">DB / Login / config / commands</text>
<rect x="650" y="70" width="195" height="90" rx="9" fill="#edf2fb" stroke="#2457a6"/>
<text x="747" y="103" text-anchor="middle" font-weight="bold">Command</text><text x="747" y="126" text-anchor="middle" font-size="13">login strategy</text><text x="747" y="145" text-anchor="middle" font-size="13">doExecute()</text>
<rect x="650" y="205" width="195" height="90" rx="9" fill="#edf2fb" stroke="#2457a6"/>
<text x="747" y="238" text-anchor="middle" font-weight="bold">RenderComponent</text><text x="747" y="261" text-anchor="middle" font-size="13">View / Forward / Datarequest</text><text x="747" y="280" text-anchor="middle" font-size="13">selected by command status</text>
<rect x="895" y="95" width="190" height="90" rx="9" fill="#f6f8fa" stroke="#8b98a7"/>
<text x="990" y="128" text-anchor="middle" font-weight="bold">Domain Model</text><text x="990" y="151" text-anchor="middle" font-size="13">DomainObject / Member</text><text x="990" y="170" text-anchor="middle" font-size="13">Mapper / ObjectMap</text>
<rect x="895" y="230" width="190" height="90" rx="9" fill="#f6f8fa" stroke="#8b98a7"/>
<text x="990" y="263" text-anchor="middle" font-weight="bold">Services</text><text x="990" y="286" text-anchor="middle" font-size="13">LoginManager / Mailer</text><text x="990" y="305" text-anchor="middle" font-size="13">CSRF / AuditTrace</text>
<line x1="165" y1="210" x2="205" y2="210" stroke="#2457a6" stroke-width="3" marker-end="url(#a)"/>
<line x1="370" y1="190" x2="410" y2="100" stroke="#2457a6" stroke-width="3" marker-end="url(#a)"/>
<line x1="370" y1="210" x2="410" y2="225" stroke="#2457a6" stroke-width="3" marker-end="url(#a)"/>
<line x1="370" y1="230" x2="410" y2="350" stroke="#2457a6" stroke-width="3" marker-end="url(#a)"/>
<line x1="600" y1="225" x2="650" y2="115" stroke="#2457a6" stroke-width="3" marker-end="url(#a)"/>
<line x1="600" y1="225" x2="650" y2="250" stroke="#2457a6" stroke-width="3" marker-end="url(#a)"/>
<line x1="845" y1="115" x2="895" y2="140" stroke="#2457a6" stroke-width="3" marker-end="url(#a)"/>
<line x1="845" y1="250" x2="895" y2="275" stroke="#2457a6" stroke-width="3" marker-end="url(#a)"/>
</g>
</svg>
</div>

<h2 id="objectmodels">2. Correct object models</h2>

<h3>2.1 Controller object model</h3>
<div class="diagram">
<svg viewBox="0 0 1100 650" width="100%" role="img" aria-label="Controller class hierarchy">
<defs><marker id="b" markerWidth="10" markerHeight="10" refX="9" refY="3" orient="auto"><path d="M0,0 L10,3 L0,6 Z" fill="#2457a6"/></marker></defs>
<g font-family="Arial" font-size="14">
<rect x="450" y="20" width="200" height="55" rx="8" fill="#edf2fb" stroke="#2457a6"/><text x="550" y="53" text-anchor="middle" font-weight="bold">Controller</text>
<rect x="105" y="125" width="230" height="65" rx="8" fill="#f6f8fa" stroke="#8b98a7"/><text x="220" y="152" text-anchor="middle" font-weight="bold">InitController &lt;&lt;abstract&gt;&gt;</text><text x="220" y="173" text-anchor="middle" font-size="12">init() / setupCommands()</text>
<rect x="380" y="125" width="250" height="65" rx="8" fill="#f6f8fa" stroke="#8b98a7"/><text x="505" y="152" text-anchor="middle" font-weight="bold">HandleRequestController</text><text x="505" y="173" text-anchor="middle" font-size="12">&lt;&lt;abstract&gt;&gt;</text>
<rect x="690" y="125" width="250" height="65" rx="8" fill="#f6f8fa" stroke="#8b98a7"/><text x="815" y="152" text-anchor="middle" font-weight="bold">Command</text><text x="815" y="173" text-anchor="middle" font-size="12">&lt;&lt;abstract&gt;&gt;</text>
<rect x="20" y="245" width="200" height="60" rx="8" fill="#fff" stroke="#8b98a7"/><text x="120" y="271" text-anchor="middle">InitApplicationController</text>
<rect x="240" y="245" width="200" height="60" rx="8" fill="#fff" stroke="#8b98a7"/><text x="340" y="271" text-anchor="middle">InitFrontController</text>
<rect x="430" y="245" width="220" height="60" rx="8" fill="#fff" stroke="#8b98a7"/><text x="540" y="271" text-anchor="middle">HandleRequestApplicationController</text>
<rect x="670" y="245" width="220" height="60" rx="8" fill="#fff" stroke="#8b98a7"/><text x="780" y="271" text-anchor="middle">HandleRequestFrontController</text>
<rect x="920" y="245" width="160" height="60" rx="8" fill="#fff" stroke="#8b98a7"/><text x="1000" y="271" text-anchor="middle">DefaultCommand</text>
<rect x="665" y="365" width="200" height="60" rx="8" fill="#edf2fb" stroke="#2457a6"/><text x="765" y="392" text-anchor="middle" font-weight="bold">CommandDecorator</text><text x="765" y="412" text-anchor="middle" font-size="12">&lt;&lt;abstract&gt;&gt;</text>
<rect x="900" y="365" width="175" height="60" rx="8" fill="#fff" stroke="#8b98a7"/><text x="987" y="399" text-anchor="middle">Client Command</text>
<rect x="25" y="365" width="190" height="60" rx="8" fill="#f6f8fa" stroke="#8b98a7"/><text x="120" y="399" text-anchor="middle">RenderComponent</text>
<rect x="250" y="365" width="175" height="60" rx="8" fill="#fff" stroke="#8b98a7"/><text x="337" y="399" text-anchor="middle">ViewRenderComponent</text>
<rect x="450" y="365" width="175" height="60" rx="8" fill="#fff" stroke="#8b98a7"/><text x="537" y="399" text-anchor="middle">ForwardRenderComponent</text>
<rect x="25" y="475" width="160" height="60" rx="8" fill="#f6f8fa" stroke="#8b98a7"/><text x="105" y="509" text-anchor="middle">DatarequestRenderComponent</text>
<rect x="205" y="475" width="150" height="60" rx="8" fill="#fff" stroke="#8b98a7"/><text x="280" y="509" text-anchor="middle">Ajax</text>
<rect x="375" y="475" width="150" height="60" rx="8" fill="#fff" stroke="#8b98a7"/><text x="450" y="509" text-anchor="middle">Download</text>
<rect x="545" y="475" width="150" height="60" rx="8" fill="#fff" stroke="#8b98a7"/><text x="620" y="509" text-anchor="middle">Agenda</text>
<rect x="715" y="475" width="150" height="60" rx="8" fill="#fff" stroke="#8b98a7"/><text x="790" y="509" text-anchor="middle">Mollie</text>
<rect x="885" y="475" width="150" height="60" rx="8" fill="#fff" stroke="#8b98a7"/><text x="960" y="509" text-anchor="middle">NoRender</text>
<g stroke="#2457a6" stroke-width="2" fill="none" marker-end="url(#b)">
<line x1="510" y1="75" x2="220" y2="125"/><line x1="550" y1="75" x2="505" y2="125"/><line x1="600" y1="75" x2="815" y2="125"/>
<line x1="220" y1="190" x2="120" y2="245"/><line x1="250" y1="190" x2="340" y2="245"/>
<line x1="505" y1="190" x2="540" y2="245"/><line x1="560" y1="190" x2="780" y2="245"/>
<line x1="815" y1="190" x2="1000" y2="245"/>
<line x1="815" y1="190" x2="765" y2="365"/><line x1="765" y1="425" x2="987" y2="365"/>
<line x1="420" y1="190" x2="120" y2="365"/><line x1="120" y1="425" x2="105" y2="475"/>
<line x1="120" y1="425" x2="337" y2="365"/><line x1="120" y1="425" x2="537" y2="365"/>
<line x1="105" y1="535" x2="280" y2="475"/><line x1="105" y1="535" x2="450" y2="475"/><line x1="105" y1="535" x2="620" y2="475"/><line x1="105" y1="535" x2="790" y2="475"/><line x1="105" y1="535" x2="960" y2="475"/>
</g>
</g>
</svg>
</div>

<p><strong>Important:</strong> <code>CommandDecorator</code> is itself a Command. It does not subclass a client command; instead it contains another <code>Command</code> in <code>$command</code> and delegates to it unless <code>doExecuteDecorator()</code> returns a non-null status.</p>

<h3>2.2 Database object model</h3>
<div class="diagram">
<svg viewBox="0 0 1050 500" width="100%" role="img" aria-label="Database object model">
<defs><marker id="c" markerWidth="10" markerHeight="10" refX="9" refY="3" orient="auto"><path d="M0,0 L10,3 L0,6 Z" fill="#2457a6"/></marker></defs>
<g font-family="Arial" font-size="14">
<rect x="40" y="60" width="245" height="90" rx="8" fill="#edf2fb" stroke="#2457a6"/><text x="162" y="95" text-anchor="middle" font-weight="bold">DomainObject</text><text x="162" y="118" text-anchor="middle">&lt;&lt;abstract&gt;&gt;</text><text x="162" y="138" text-anchor="middle" font-size="12">id + dynamic properties</text>
<rect x="40" y="255" width="245" height="90" rx="8" fill="#fff" stroke="#8b98a7"/><text x="162" y="290" text-anchor="middle" font-weight="bold">Client DomainObject</text><text x="162" y="313" text-anchor="middle">e.g. \model\Activity</text><text x="162" y="333" text-anchor="middle" font-size="12">one class per domain entity</text>
<rect x="400" y="60" width="245" height="90" rx="8" fill="#edf2fb" stroke="#2457a6"/><text x="522" y="95" text-anchor="middle" font-weight="bold">Mapper</text><text x="522" y="118" text-anchor="middle">&lt;&lt;abstract&gt;&gt;</text><text x="522" y="138" text-anchor="middle" font-size="12">PDO + ObjectMap cache</text>
<rect x="400" y="255" width="245" height="90" rx="8" fill="#fff" stroke="#8b98a7"/><text x="522" y="290" text-anchor="middle" font-weight="bold">Client Mapper</text><text x="522" y="313" text-anchor="middle">e.g. \model\ActivityMapper</text><text x="522" y="333" text-anchor="middle" font-size="12">table + object creation + fields</text>
<rect x="760" y="60" width="245" height="90" rx="8" fill="#f6f8fa" stroke="#8b98a7"/><text x="882" y="95" text-anchor="middle" font-weight="bold">ObjectMap</text><text x="882" y="118" text-anchor="middle">extends SplObjectStorage</text><text x="882" y="138" text-anchor="middle" font-size="12">object → database id</text>
<rect x="760" y="255" width="245" height="90" rx="8" fill="#f6f8fa" stroke="#8b98a7"/><text x="882" y="290" text-anchor="middle" font-weight="bold">PDO</text><text x="882" y="313" text-anchor="middle">MySQL / MariaDB</text><text x="882" y="333" text-anchor="middle" font-size="12">application database</text>
<g stroke="#2457a6" stroke-width="2" fill="none" marker-end="url(#c)">
<line x1="162" y1="150" x2="162" y2="255"/><line x1="285" y1="105" x2="400" y2="105"/>
<line x1="522" y1="150" x2="522" y2="255"/><line x1="645" y1="105" x2="760" y2="105"/>
<line x1="645" y1="130" x2="760" y2="290"/>
</g>
</g>
</svg>
</div>

<h3>2.3 Member object model</h3>
<div class="diagram">
<svg viewBox="0 0 1100 560" width="100%" role="img" aria-label="Member object model">
<defs><marker id="d" markerWidth="10" markerHeight="10" refX="9" refY="3" orient="auto"><path d="M0,0 L10,3 L0,6 Z" fill="#2457a6"/></marker></defs>
<g font-family="Arial" font-size="14">
<rect x="415" y="20" width="270" height="75" rx="8" fill="#edf2fb" stroke="#2457a6"/><text x="550" y="49" text-anchor="middle" font-weight="bold">DomainObject</text><text x="550" y="72" text-anchor="middle">&lt;&lt;abstract&gt;&gt;</text>
<rect x="415" y="145" width="270" height="75" rx="8" fill="#edf2fb" stroke="#2457a6"/><text x="550" y="175" text-anchor="middle" font-weight="bold">controllerframework\members\Member</text><text x="550" y="198" text-anchor="middle">&lt;&lt;abstract&gt;&gt;</text>
<rect x="120" y="285" width="300" height="80" rx="8" fill="#fff" stroke="#8b98a7"/><text x="270" y="316" text-anchor="middle" font-weight="bold">\model\Member</text><text x="270" y="340" text-anchor="middle" font-size="12">client concrete Member</text>
<rect x="610" y="285" width="300" height="80" rx="8" fill="#fff" stroke="#8b98a7"/><text x="760" y="316" text-anchor="middle" font-weight="bold">MemberComposite</text><text x="760" y="340" text-anchor="middle" font-size="12">contains ObjectMap of children</text>
<rect x="20" y="435" width="300" height="75" rx="8" fill="#f6f8fa" stroke="#8b98a7"/><text x="170" y="465" text-anchor="middle" font-weight="bold">MemberTypeImplementation</text><text x="170" y="487" text-anchor="middle" font-size="12">&lt;&lt;abstract&gt;&gt; Bridge</text>
<rect x="390" y="435" width="300" height="75" rx="8" fill="#fff" stroke="#8b98a7"/><text x="540" y="465" text-anchor="middle" font-weight="bold">\model\Member_RGLR</text><text x="540" y="487" text-anchor="middle" font-size="12">type = RGLR example</text>
<rect x="760" y="435" width="300" height="75" rx="8" fill="#fff" stroke="#8b98a7"/><text x="910" y="465" text-anchor="middle" font-weight="bold">\model\Member_PRTN</text><text x="910" y="487" text-anchor="middle" font-size="12">type = PRTN example</text>
<g stroke="#2457a6" stroke-width="2" fill="none" marker-end="url(#d)">
<line x1="550" y1="95" x2="550" y2="145"/>
<line x1="500" y1="220" x2="270" y2="285"/><line x1="600" y1="220" x2="760" y2="285"/>
<line x1="270" y1="365" x2="170" y2="435"/><line x1="270" y1="365" x2="540" y2="435"/><line x1="760" y1="365" x2="170" y2="435"/>
<line x1="170" y1="510" x2="540" y2="510"/><line x1="170" y1="510" x2="910" y2="510"/>
</g>
<text x="175" y="415" text-anchor="middle" font-size="12">membertypeimplementation</text>
</g>
</svg>
</div>

<div class="note"><strong>Client model contract:</strong> the framework's <code>DomainObject::mapper()</code> derives the Mapper class from the short class name and the <code>\model\</code> namespace. For example, <code>\model\Activity</code> maps to <code>\model\ActivityMapper</code>. For Members the framework explicitly uses <code>\model\Member</code>. A client application should therefore keep this namespace convention unless the framework itself is changed.</div>

<h3>2.4 Session object model</h3>
<div class="diagram">
<svg viewBox="0 0 950 420" width="100%" role="img" aria-label="Session object model">
<defs><marker id="e" markerWidth="10" markerHeight="10" refX="9" refY="3" orient="auto"><path d="M0,0 L10,3 L0,6 Z" fill="#2457a6"/></marker></defs>
<g font-family="Arial" font-size="14">
<rect x="365" y="20" width="220" height="65" rx="8" fill="#edf2fb" stroke="#2457a6"/><text x="475" y="48" text-anchor="middle" font-weight="bold">Login</text><text x="475" y="69" text-anchor="middle">&lt;&lt;abstract Strategy&gt;&gt;</text>
<rect x="80" y="140" width="220" height="70" rx="8" fill="#fff" stroke="#8b98a7"/><text x="190" y="170" text-anchor="middle" font-weight="bold">NoLoginRequired</text><text x="190" y="192" text-anchor="middle" font-size="12">validate() = true</text>
<rect x="365" y="140" width="220" height="70" rx="8" fill="#edf2fb" stroke="#2457a6"/><text x="475" y="170" text-anchor="middle" font-weight="bold">LoginRequired</text><text x="475" y="192" text-anchor="middle" font-size="12">&lt;&lt;abstract Template&gt;&gt;</text>
<rect x="650" y="140" width="220" height="70" rx="8" fill="#fff" stroke="#8b98a7"/><text x="760" y="170" text-anchor="middle" font-weight="bold">LoginManager</text><text x="760" y="192" text-anchor="middle" font-size="12">session + remember-me</text>
<rect x="365" y="270" width="220" height="65" rx="8" fill="#fff" stroke="#8b98a7"/><text x="475" y="298" text-anchor="middle" font-weight="bold">UserLogin</text><text x="475" y="319" text-anchor="middle" font-size="12">60 minute inactivity</text>
<rect x="650" y="270" width="220" height="65" rx="8" fill="#fff" stroke="#8b98a7"/><text x="760" y="298" text-anchor="middle" font-weight="bold">AdminLogin</text><text x="760" y="319" text-anchor="middle" font-size="12">15 minute inactivity</text>
<g stroke="#2457a6" stroke-width="2" fill="none" marker-end="url(#e)">
<line x1="475" y1="85" x2="190" y2="140"/><line x1="475" y1="85" x2="475" y2="140"/><line x1="475" y1="85" x2="760" y2="140"/>
<line x1="475" y1="210" x2="475" y2="270"/><line x1="585" y1="175" x2="650" y2="300"/>
<line x1="760" y1="210" x2="760" y2="270"/>
</g>
</g>
</svg>
</div>

<h2 id="controllers">3. Controllers package</h2>

<h3><code>controllerframework\controllers\Controller</code></h3>
<p>The public application entry point. The constructor is private; applications use the static <code>run()</code> method.</p>
<pre><code>final public static function run(): void</code></pre>
<p>Runtime sequence: register <code>ErrorHandler</code> → initialize the Registry through the configured <code>InitController</code> → set the configured environment → delegate the current Request to the configured <code>HandleRequestController</code>.</p>

<h3><code>Command</code></h3>
<div class="api"><h4>class Command</h4><div class="body">
<p><strong>Namespace:</strong> <code>controllerframework\controllers</code><br>
<strong>Type:</strong> abstract class<br><strong>Pattern:</strong> Command + Template Method + Strategy</p>
<pre><code>abstract class Command
{
    protected Registry $reg;
    protected Login $loginLevel;

    public const CMD_DEFAULT = 0;
    public const CMD_OK = 1;
    public const CMD_ERROR = 2;
    public const CMD_INSUFFICIENT_DATA = 3;
    public const CMD_ADMIN = 4;
    public const CMD_CHANGE_PASSWORD = 5;
    public const CMD_CONTINUE = 6;

    final public function __construct(): void;
    public function execute(Request $request): int;

    protected function setLoginLevel(Login $loginLevel): void;
    protected function loginChecks(Member $user): int;
    protected function addResponses(Request $request, array $responses): void;
    protected function getCsrfToken(): string;
    protected function validateCsrfToken(Request $request): bool;

    abstract public function doExecute(Request $request): int;
    abstract protected function getLevelOfLoginRequired(): void;
}</code></pre>
</div></div>

<div class="warning"><strong>Client implementation rule:</strong> implement <code>doExecute()</code> and <code>getLevelOfLoginRequired()</code>. Do not override <code>execute()</code>; it is the framework Template Method that performs login validation, invokes the application logic and stores the command status in the Request.</div>

<h3><code>CommandDecorator</code></h3>
<pre><code>abstract class CommandDecorator extends Command
{
    protected ?Command $command = null;

    protected function setCommand(Command $command): void;

    final public function doExecute(Request $request): int;

    abstract public function initCommand(): void;
    abstract public function doExecuteDecorator(Request $request): ?int;
}</code></pre>
<p>Execution first calls <code>initCommand()</code>. If <code>doExecuteDecorator()</code> returns a non-null/non-zero status, that status becomes the result. Otherwise the decorated Command is executed.</p>

<h3><code>Conf</code></h3>
<pre><code>class Conf
{
    public function __construct(array $conf = []): void;
    public function set(string $key, mixed $value): void;
    public function get(string $key): mixed;
}</code></pre>
<p><code>Conf</code> is a small object wrapper around a named PHP array. It is used for application configuration and the compiled command map.</p>

<h3><code>RenderComponent</code> and renderers</h3>
<pre><code>interface RenderComponent
{
    public function render(Request $request): void;
}

abstract class DatarequestRenderComponent implements RenderComponent
{
    public static function init(string $type): DatarequestRenderComponent;
}

class ViewRenderComponent implements RenderComponent
{
    public function __construct(string $name);
    public function render(Request $request): void;
}

class ForwardRenderComponent implements RenderComponent
{
    public function __construct(string $path);
    public function render(Request $request): void;
}

class AjaxRenderComponent extends DatarequestRenderComponent
class DownloadRenderComponent extends DatarequestRenderComponent
class AgendaRenderComponent extends DatarequestRenderComponent
class MollieRenderComponent extends DatarequestRenderComponent
class NoRenderRenderComponent extends DatarequestRenderComponent</code></pre>

<table>
<tr><th>Renderer</th><th>Expected Request state</th><th>Response</th></tr>
<tr><td>ViewRenderComponent</td><td>view configured in <code>controls.xml</code></td><td>Includes PHP view</td></tr>
<tr><td>ForwardRenderComponent</td><td>optional <code>forwardqueryparams</code> array</td><td>HTTP redirect</td></tr>
<tr><td>AjaxRenderComponent</td><td><code>results</code> array</td><td>JSON</td></tr>
<tr><td>DownloadRenderComponent</td><td><code>filename</code>, <code>columnNames</code>, <code>results</code></td><td>CSV</td></tr>
<tr><td>AgendaRenderComponent</td><td><code>filename</code>, <code>results</code></td><td>iCalendar</td></tr>
<tr><td>MollieRenderComponent</td><td><code>results</code></td><td>HTTP Location redirect</td></tr>
<tr><td>NoRenderRenderComponent</td><td>none</td><td>Ends response without rendering</td></tr>
</table>

<h3><code>RenderComponentDescriptor</code></h3>
<pre><code>class RenderComponentDescriptor
{
    public function __construct(string $path, string $cmdstr);
    public function getCommand(): Command;
    public function setRenderer(int $status, RenderComponent $renderer): void;
    public function getRenderer(Request $request): RenderComponent;
}</code></pre>
<p>A descriptor represents one path from <code>controls.xml</code>. It contains the Command class name and a renderer map indexed by Command status. A renderer matching the current status is preferred; status <code>CMD_DEFAULT</code> (numeric 0) is used as fallback.</p>

<h3><code>RenderCompiler</code></h3>
<pre><code>class RenderCompiler
{
    public function parseFile(string $file): Conf;
}</code></pre>
<p>The compiler parses XML using <code>simplexml_load_file()</code>, converts each command into a <code>RenderComponentDescriptor</code>, resolves status constants from <code>Command</code> and creates the configured renderer objects.</p>

<h3><code>CommandResolver</code></h3>
<pre><code>class CommandResolver
{
    public function __construct();
    public function getCommand(Request $request): Command;
}</code></pre>
<p>It resolves a command from the request/command configuration and validates that the configured class exists and is a subclass of <code>Command</code>.</p>

<h3><code>HandleRequestController</code></h3>
<pre><code>abstract class HandleRequestController
{
    protected function getCommand(Request $request): Command;
    protected function getRenderer(Request $request): RenderComponent;
    abstract public function handleRequest(Request $request): void;
}</code></pre>

<pre><code>class HandleRequestApplicationController
    extends HandleRequestController
{
    public function handleRequest(Request $request): void
    {
        $this->getCommand($request)->execute($request);
        $this->getRenderer($request)->render($request);
    }
}</code></pre>

<h2 id="registry">4. Registry and Request</h2>

<h3><code>Registry</code></h3>
<pre><code>class Registry
{
    public static function instance(): self;
    public static function reset(): void;

    public function getRequest(): Request;
    public function setRequest(Request $request): void;

    public function getInitController(): InitController;
    public function getHandleRequestController(): HandleRequestController;

    public function setAppConfig(Conf $conf): void;
    public function getAppConfig(): Conf;

    public function setCommands(Conf $commands): void;
    public function getCommands(): Conf;

    public function getLoginManager(): LoginManager;
    public function getDb(): \PDO;
}</code></pre>
<p><code>Registry</code> is a Singleton. It lazily creates the application database PDO connection and LoginManager and stores the Request, application configuration and compiled command map.</p>

<h3><code>Request</code></h3>
<pre><code>abstract class Request
{
    protected array $properties;
    protected int $status = 0;
    protected array $feedback = [];
    protected string $path = "/";

    public function __construct();

    abstract public function init(): void;
    abstract public function forward(string $path): void;

    public function setPath(string $path): void;
    public function getPath(): string;

    public function get(string $key): mixed;
    public function set(string $key, mixed $val): void;

    public function addFeedback(string $msg): void;
    public function getFeedback(): array;
    public function getFeedbackString(string $seperator = "\n"): string;
    public function clearFeedback(): void;

    public function setCmdStatus(int $status): void;
    public function getCmdStatus(): int;
}</code></pre>

<h3>Concrete Requests</h3>
<pre><code>class HttpRequest extends Request
{
    public function init(): void;
    public function forward(string $path): void;
}

class CliRequest extends Request
{
    public function init(): void;
    public function forward(string $path): void;
}</code></pre>
<p><code>InitController</code> selects <code>HttpRequest</code> when <code>$_SERVER['REQUEST_METHOD']</code> exists and <code>CliRequest</code> otherwise.</p>

<h3><code>InitController</code></h3>
<pre><code>abstract class InitController
{
    public function __construct();
    public function init(): void;

    abstract protected function setupCommands(
        array $options,
        string $controlsfile
    ): Conf;
}</code></pre>
<p>The constructor determines the application root from the framework installation location and derives <code>/config/app_options.ini</code>. <code>init()</code> reads configuration, defines global constants, determines the controls file and stores the Request.</p>

<h3><code>InitApplicationController</code></h3>
<pre><code>class InitApplicationController extends InitController
{
    protected function setupCommands(
        array $options,
        string $controlsfile
    ): Conf;
}</code></pre>
<p>It uses <code>RenderCompiler</code> to create the application command map.</p>

<h2 id="database">5. Database object model</h2>

<h3><code>DomainObject</code></h3>
<pre><code>abstract class DomainObject
{
    public ?Conf $properties = null;

    public function __construct(int $id);

    abstract public static function getInstance(array $row): DomainObject;

    public static function find(int $id): DomainObject;
    public static function findAll(string $selectclause = ''): ObjectMap;
    public static function insert(array $properties): DomainObject;

    public function update(array $properties): DomainObject;
    public function delete(): void;

    protected static function mapper(): Mapper;

    public function isComposite(): bool;
    public function getId(): int;

    protected function initProperties(array $row): void;

    public function __get(string $key): mixed;
    public function __set(string $key, mixed $value): void;
}</code></pre>

<p>Every database table represented by a DomainObject is expected to have an <code>id</code> column that uniquely identifies a row. Additional database columns are represented dynamically through the public <code>properties</code> configuration object and <code>__get()</code>/<code>__set()</code>.</p>

<h3><code>Mapper</code></h3>
<pre><code>abstract class Mapper implements AuditableItem
{
    public function __construct();

    public function find(string $classname, int $id): DomainObject;
    public function findAll(
        string $classname,
        string $selectclause = ''
    ): ObjectMap;

    public function createObject(
        string $classname,
        array $row
    ): DomainObject;

    public function insert(
        string $classname,
        array $properties
    ): DomainObject;

    protected function getAllowedFields(): array;
    protected function validateFields(array $updates): void;

    public function update(
        DomainObject $obj,
        array $updates
    ): DomainObject;

    public function delete(DomainObject $obj): void;
    public function checkForChildren(int $id): array;

    abstract protected function tablename(): string;
    abstract protected function doCreateObject(
        string $classname,
        array $row
    ): DomainObject;
}</code></pre>

<p>The Mapper owns the PDO connection and an <code>ObjectMap</code> cache. <code>find()</code> first checks the cache, then loads the row and creates the object. Updates validate every field against <code>getAllowedFields()</code> before executing the update.</p>

<div class="warning"><strong><code>findAll()</code> security contract:</strong> <code>$selectclause</code> is intentionally a free SQL fragment. It is not a bound parameter. Never put untrusted GET, POST, cookie or other external input directly into this argument. Use application-controlled/whitelisted SQL fragments or redesign the query when dynamic user input is required.</div>

<h3><code>ObjectMap</code></h3>
<pre><code>class ObjectMap extends \SplObjectStorage
{
    public function getObjectBy(int $info): ?DomainObject;
}</code></pre>
<p>The mapper uses the database ID as the <code>SplObjectStorage</code> info value. It therefore provides an object collection that can also retrieve a DomainObject by database ID.</p>

<h2 id="members">6. Member object model</h2>

<h3><code>controllerframework\members\Member</code></h3>
<pre><code>abstract class Member extends \controllerframework\db\DomainObject
{
    public ?MemberTypeImplementation $membertypeimplementation = null;

    public function __construct(int $id);
    public static function getInstance(array $row): Member;

    public function getYearlyfee(): float;
    public function isRejected(): bool;
    public function isAdministrator(): bool;
    public function shouldExtendMembership(): bool;
    public function extendMembership(): void;

    protected function getMembershipFee(): float;

    public function subscriptionPeriodOver(): bool;
    public function getTotalAmountReceived(): float;

    abstract public function initiatePassword(
        string $pwd = ''
    ): string;
}</code></pre>

<p><code>Member::getInstance()</code> determines whether the database row has children. If so, it returns a <code>MemberComposite</code>; otherwise it creates the concrete Member class and attaches a <code>MemberTypeImplementation</code> based on the row's <code>classification</code> value.</p>

<h3><code>MemberComposite</code></h3>
<pre><code>class MemberComposite extends Member
{
    protected ObjectMap $children;

    public function __construct(int $id);
    public static function getInstance(array $row): Member;

    public function getChildren(): ObjectMap;
    public function isComposite(): bool;

    public function initiatePassword(string $pwd = ''): string;
}</code></pre>
<p>A composite Member cannot be deleted through <code>DomainObject::delete()</code>, because <code>isComposite()</code> returns true.</p>

<h3><code>MemberMapper</code></h3>
<pre><code>abstract class MemberMapper extends \controllerframework\db\Mapper
{
    public function tablename(): string;
    protected function doCreateObject(
        string $classname,
        array $row
    ): Member;

    protected function getAllowedFields(): array;

    public function getChildren(
        MemberComposite $membercomposite
    ): ObjectMap;
}</code></pre>
<p>The framework MemberMapper is tied to the database table <code>member</code>. A client application normally creates a concrete <code>\model\MemberMapper</code> subclass.</p>

<h3><code>MemberTypeImplementation</code></h3>
<pre><code>abstract class MemberTypeImplementation
{
    public function getMembershipFee(
        \model\Member $member
    ): float;

    abstract public function getYearlyParticipationFee(
        \model\Member $member
    ): int;

    public function calculateProRataFee(float $fee): float;
}</code></pre>

<h3>Correct client model structure</h3>
<pre><code>namespace model;

class Member
    extends \controllerframework\members\Member
{
    public function initiatePassword(
        string $pwd = ''
    ): string {
        // application-specific mail body
    }
}

final class MemberMapper
    extends \controllerframework\members\MemberMapper
{
}

class Member_RGLR
    extends \controllerframework\members\MemberTypeImplementation
{
    public function getYearlyParticipationFee(
        \model\Member $member
    ): int {
        return 350;
    }
}

class Member_PRTN
    extends \controllerframework\members\MemberTypeImplementation
{
    public function getYearlyParticipationFee(
        \model\Member $member
    ): int {
        // application-specific calculation
    }
}</code></pre>

<p>The framework constructs the type implementation dynamically as <code>\model\Member_{classification}</code>. Consequently a classification such as <code>RGLR</code> requires <code>\model\Member_RGLR</code>.</p>

<h2 id="sessions">7. Session object model</h2>

<h3><code>Login</code></h3>
<pre><code>abstract class Login
{
    abstract public function validate(): bool;
}</code></pre>

<h3><code>LoginRequired</code></h3>
<pre><code>abstract class LoginRequired extends Login
{
    public function __construct();
    public function validate(): bool;

    protected function setLastActive(
        int $numberOfMinutes
    ): void;

    abstract protected function initLastActive(): void;
    abstract protected function checkMemberRights(): bool;
}</code></pre>

<p>The Template Method <code>validate()</code> obtains the current Member from LoginManager, checks inactivity and then delegates rights validation to the concrete strategy.</p>

<table>
<tr><th>Strategy</th><th>Rights</th><th>Normal inactivity</th></tr>
<tr><td><code>NoLoginRequired</code></td><td>No authentication required</td><td>Not applicable</td></tr>
<tr><td><code>UserLogin</code></td><td>Member must be active</td><td>60 minutes</td></tr>
<tr><td><code>AdminLogin</code></td><td>Member must be active and role <code>A</code></td><td>15 minutes</td></tr>
</table>

<p>If <code>$_SESSION['rememberMe']</code> is true, the inactivity test is bypassed. Persistent authentication is then controlled by the 30-day remember-token mechanism.</p>

<h3><code>LoginManager</code></h3>
<pre><code>class LoginManager implements AuditableItem
{
    public function __construct();

    public function getUser(): ?Member;

    public function validateUsername(
        string $username
    ): int|false;

    public function validatePassword(
        int $memberid,
        string $password
    ): bool;

    public function login(
        int $memberid,
        bool $keepLoggedin = true
    ): Member;

    public function logout(): void;

    public function initiatePassword(
        int $memberid,
        int $pwdlength = 8,
        bool $requestedByAdmin = false
    ): void;

    public function changePassword(
        Member $user,
        string $password
    ): void;
}</code></pre>

<h4>Session security implemented by Release 31</h4>
<ul>
<li>Session cookie: Secure, HttpOnly and SameSite=Lax.</li>
<li><code>session.use_strict_mode</code> is enabled.</li>
<li>Session ID is regenerated after successful login and remember-token authentication.</li>
<li>Remember-me cookie uses selector + validator.</li>
<li>Only the SHA-256 hash of the validator is stored in the database.</li>
<li>Remember tokens expire after 30 days and are rotated after successful reuse.</li>
<li>Logout removes the session, session cookie, remember token records and remember cookie.</li>
</ul>

<h3><code>User</code></h3>
<pre><code>class User
{
    public static function getInstance(
        int $memberid = 0
    ): ?\model\Member;
}</code></pre>
<p>This singleton-like helper resolves the Member represented by the current session member ID.</p>

<h2 id="security">8. Security API</h2>

<h3><code>controllerframework\security\Csrf</code></h3>
<pre><code>class Csrf
{
    public const TOKEN_PARAMETER = 'csrf_token';

    public static function getToken(): string;
    public static function validate(?string $token): bool;
    public static function regenerateToken(): string;
}</code></pre>
<p>The token is generated with <code>random_bytes(32)</code>, stored in the session and compared using <code>hash_equals()</code>.</p>

<h3>Command CSRF helpers</h3>
<pre><code>protected function getCsrfToken(): string;

protected function validateCsrfToken(
    Request $request
): bool;</code></pre>

<p>The helper reads <code>Csrf::TOKEN_PARAMETER</code> from the Request. Client Commands performing state-changing POST operations should validate it before processing the operation.</p>

<h3><code>controllerframework\\security\\AccessToken</code></h3>
<pre><code>class AccessToken
{
    public static function generate(
        string $purpose,
        string $identifier
    ): string;

    public static function validate(
        string $purpose,
        string $identifier,
        ?string $token
    ): bool;
}</code></pre>

<p>
The <code>AccessToken</code> class provides an additional token-based security
mechanism for protecting specific URLs or application operations. A token is
bound to a purpose and an application-specific identifier.
</p>

<p>
Access tokens are generated and validated using a server-side secret and
HMAC-SHA-256. Validation uses a timing-safe comparison. The token does not
require a database table or other server-side token storage.
</p>

<p>
The same purpose and identifier produce the same token. Consequently,
<code>AccessToken</code> is not a session-token mechanism and does not by itself
provide expiration, one-time use or revocation.
</p>

<div class="warning">
<strong>Security rule:</strong> the server-side secret used by
<code>AccessToken</code> must remain confidential. Access tokens should only be
transmitted over HTTPS and should not be written to application or audit logs.
The token must be validated before the protected operation is performed.
</div>

<h4>Example</h4>
<pre><code>use controllerframework\\security\\AccessToken;

$token = AccessToken::generate(
    'mollie-order',
    (string) $orderid
);

if (!AccessToken::validate(
    'mollie-order',
    (string) $orderid,
    $token
)) {
    throw new \\RuntimeException(
        'Invalid access token.'
    );
}</code></pre>

<h2 id="mail">9. Mail API</h2>

<h3><code>Mailer</code></h3>
<pre><code>class Mailer
{
    public static function sendMail(
        string $subject,
        string $body,
        string $toBcc,
        ?string $to = null
    ): void;
}</code></pre>
<p>The SMTP DSN is constructed from <code>_MAILHOST</code>, <code>_MAILHOSTPORT</code>, <code>_MAILUSERNAME</code> and <code>_MAILPASSWORD</code>. Sender and reply-to values come from the configured mail constants.</p>

<h3><code>MailQueue</code></h3>
<pre><code>class MailQueue
{
    public static function add(
        string $subject,
        string $body,
        string $recipient,
        string $bcc = ''
    ): int;
}</code></pre>
<p><code>MailQueue::add()</code> inserts a message into the <code>mail_queue</code> database table and returns its inserted ID.</p>

<h3><code>MailerQueue</code></h3>
<pre><code>class MailerQueue
{
    public static function sendMail(
        $subject,
        $body,
        $toBcc,
        $to = null
    ): void;
}</code></pre>
<p>This helper sends a message through Symfony Mailer. A scheduled queue processor can retrieve queued records and pass their values to <code>MailerQueue::sendMail()</code>.</p>

<h2 id="audit">10. Audit API</h2>

<h3><code>AuditableItem</code></h3>
<pre><code>interface AuditableItem
{
    public function notifyAuditTrace(
        string $functionname,
        array $arglist = []
    ): void;
}</code></pre>

<h3><code>AuditableItemTrait</code></h3>
<pre><code>trait AuditableItemTrait
{
    public function notifyAuditTrace(
        string $functionname,
        array $arglist = []
    ): void;
}</code></pre>

<h3><code>AuditTrace</code></h3>
<pre><code>class AuditTrace
{
    public function notify(
        string $classname,
        string $functionname,
        array $arglist = []
    ): void;
}</code></pre>

<p>The trait is used by framework classes such as <code>Mapper</code> and <code>LoginManager</code>. The logging path is supplied through the application's <code>loggingpath</code> configuration.</p>

<h2 id="errors">11. Error handling</h2>

<h3><code>ErrorHandler</code></h3>
<pre><code>class ErrorHandler
{
    public static function register(): void;
    public static function setEnvironment(
        string $environment
    ): void;
    public static function handleException(
        \Throwable $exception
    ): void;
}</code></pre>

<table>
<tr><th>Environment</th><th>Purpose</th></tr>
<tr><td><code>development</code></td><td>Detailed exception diagnostics for development.</td></tr>
<tr><td><code>production</code></td><td>Generic HTTP 500 behavior without exposing internal details.</td></tr>
</table>

<h2 id="client">12. Client application contracts</h2>

<h3>12.1 Application entry point</h3>
<pre><code>&lt;?php

require __DIR__ . '/vendor/autoload.php';

controllerframework\controllers\Controller::run();</code></pre>

<h3>12.2 Client command</h3>
<pre><code>namespace commands;

class ActivityCommand
    extends \controllerframework\controllers\Command
{
    public function doExecute(
        \controllerframework\registry\Request $request
    ): int {
        $activity = \model\Activity::find(
            (int) $request->get('id')
        );

        $this->addResponses($request, [
            'activity' => $activity
        ]);

        return self::CMD_OK;
    }

    protected function getLevelOfLoginRequired(): void
    {
        $this->setLoginLevel(
            new \controllerframework\sessions\UserLogin()
        );
    }
}</code></pre>

<h3>12.3 Client DomainObject</h3>
<pre><code>namespace model;

class Activity
    extends \controllerframework\db\DomainObject
{
    public static function getInstance(
        array $row
    ): \controllerframework\db\DomainObject {
        $activity = new self($row['id']);
        $activity->initProperties($row);
        return $activity;
    }
}</code></pre>

<h3>12.4 Client Mapper</h3>
<pre><code>namespace model;

final class ActivityMapper
    extends \controllerframework\db\Mapper
{
    protected function tablename(): string
    {
        return 'activity';
    }

    protected function doCreateObject(
        string $classname,
        array $row
    ): \controllerframework\db\DomainObject {
        return $classname::getInstance($row);
    }

    protected function getAllowedFields(): array
    {
        return array_merge(
            parent::getAllowedFields(),
            [
                'name',
                'date',
                'active'
            ]
        );
    }
}</code></pre>

<div class="note"><strong>Mapper naming:</strong> <code>DomainObject::mapper()</code> derives <code>\model\ActivityMapper</code> from <code>\model\Activity</code>. The same convention is used for other client DomainObjects.</div>

<h3>12.5 Controls XML contract</h3>
<pre><code>&lt;command
    path="/activity"
    class="\commands\ActivityCommand"&gt;

    &lt;view name="/activity/activityView" /&gt;

    &lt;status value="CMD_OK"&gt;
        &lt;forward path="/activity/list" /&gt;
    &lt;/status&gt;

    &lt;status value="CMD_ERROR"&gt;
        &lt;view name="/errorView" /&gt;
    &lt;/status&gt;

&lt;/command&gt;</code></pre>

<h3>12.6 Response flow</h3>
<pre><code>HTTP request
    ↓
Request::getPath()
    ↓
RenderComponentDescriptor
    ↓
Command::execute()
    ├── Login::validate()
    └── Command::doExecute()
            ↓
      Request::setCmdStatus()
            ↓
RenderComponentDescriptor::getRenderer()
            ↓
RenderComponent::render()</code></pre>

<h2 id="reference">13. API reference by package</h2>

<h3><code>controllerframework\controllers</code></h3>
<table>
<tr><th>Class / interface</th><th>Role</th></tr>
<tr><td><code>Controller</code></td><td>Application entry point.</td></tr>
<tr><td><code>Command</code></td><td>Base class for application commands.</td></tr>
<tr><td><code>CommandDecorator</code></td><td>Decorates another Command.</td></tr>
<tr><td><code>DefaultCommand</code></td><td>Framework default command.</td></tr>
<tr><td><code>Conf</code></td><td>Named-array configuration object.</td></tr>
<tr><td><code>CommandResolver</code></td><td>Command resolution and class validation.</td></tr>
<tr><td><code>RenderCompiler</code></td><td>Compiles <code>controls.xml</code>.</td></tr>
<tr><td><code>RenderComponentDescriptor</code></td><td>Path → Command + renderer mapping.</td></tr>
<tr><td><code>RenderComponent</code></td><td>Rendering interface.</td></tr>
<tr><td><code>ViewRenderComponent</code></td><td>PHP view renderer.</td></tr>
<tr><td><code>ForwardRenderComponent</code></td><td>HTTP forward renderer.</td></tr>
<tr><td><code>DatarequestRenderComponent</code></td><td>Base for data response renderers.</td></tr>
<tr><td><code>AjaxRenderComponent</code></td><td>JSON renderer.</td></tr>
<tr><td><code>DownloadRenderComponent</code></td><td>CSV download renderer.</td></tr>
<tr><td><code>AgendaRenderComponent</code></td><td>iCalendar renderer.</td></tr>
<tr><td><code>MollieRenderComponent</code></td><td>Payment redirect renderer.</td></tr>
<tr><td><code>NoRenderRenderComponent</code></td><td>Response terminator without rendering.</td></tr>
<tr><td><code>InitController</code></td><td>Base initialization controller.</td></tr>
<tr><td><code>InitApplicationController</code></td><td>Application Controller initialization.</td></tr>
<tr><td><code>InitFrontController</code></td><td>Front-controller initialization variant.</td></tr>
<tr><td><code>HandleRequestController</code></td><td>Base request handler.</td></tr>
<tr><td><code>HandleRequestApplicationController</code></td><td>Application request handler.</td></tr>
<tr><td><code>HandleRequestFrontController</code></td><td>Front-controller request handler.</td></tr>
</table>

<h3><code>controllerframework\db</code></h3>
<table>
<tr><th>Class</th><th>Role</th></tr>
<tr><td><code>DomainObject</code></td><td>Base persistent domain object.</td></tr>
<tr><td><code>Mapper</code></td><td>Base database mapper.</td></tr>
<tr><td><code>ObjectMap</code></td><td>Object collection keyed by database ID.</td></tr>
</table>

<h3><code>controllerframework\members</code></h3>
<table>
<tr><th>Class</th><th>Role</th></tr>
<tr><td><code>Member</code></td><td>Base Member domain object.</td></tr>
<tr><td><code>MemberComposite</code></td><td>Member containing child Members.</td></tr>
<tr><td><code>MemberMapper</code></td><td>Base Mapper for the <code>member</code> table.</td></tr>
<tr><td><code>MemberTypeImplementation</code></td><td>Member-type-specific behavior.</td></tr>
</table>

<h3><code>controllerframework\registry</code></h3>
<table>
<tr><th>Class</th><th>Role</th></tr>
<tr><td><code>Registry</code></td><td>Application-wide Singleton registry.</td></tr>
<tr><td><code>Request</code></td><td>Base request abstraction.</td></tr>
<tr><td><code>HttpRequest</code></td><td>HTTP request implementation.</td></tr>
<tr><td><code>CliRequest</code></td><td>CLI request implementation.</td></tr>
</table>

<h3><code>controllerframework\sessions</code></h3>
<table>
<tr><th>Class</th><th>Role</th></tr>
<tr><td><code>Login</code></td><td>Login validation Strategy base.</td></tr>
<tr><td><code>LoginRequired</code></td><td>Template for authenticated strategies.</td></tr>
<tr><td><code>UserLogin</code></td><td>Active-user strategy.</td></tr>
<tr><td><code>AdminLogin</code></td><td>Active-administrator strategy.</td></tr>
<tr><td><code>NoLoginRequired</code></td><td>Public command strategy.</td></tr>
<tr><td><code>LoginManager</code></td><td>Session, authentication and password management.</td></tr>
<tr><td><code>User</code></td><td>Session-member resolver.</td></tr>
</table>

<h3><code>controllerframework\security</code></h3>
<table>
<tr><th>Class</th><th>Role</th></tr>
<tr><td><code>Csrf</code></td><td>Session-based CSRF token generation and validation.</td></tr>
</table>

<h3><code>controllerframework\mail</code></h3>
<table>
<tr><th>Class</th><th>Role</th></tr>
<tr><td><code>Mailer</code></td><td>Immediate SMTP mail sending.</td></tr>
<tr><td><code>MailQueue</code></td><td>Persist mail for asynchronous sending.</td></tr>
<tr><td><code>MailerQueue</code></td><td>Send a queued message through SMTP.</td></tr>
</table>

<h3><code>controllerframework\audit</code></h3>
<table>
<tr><th>Type</th><th>Role</th></tr>
<tr><td><code>AuditableItem</code></td><td>Audit notification contract.</td></tr>
<tr><td><code>AuditableItemTrait</code></td><td>Standard audit notification implementation.</td></tr>
<tr><td><code>AuditTrace</code></td><td>Audit log writer.</td></tr>
</table>

<h2>14. Technical invariants for client developers</h2>
<ul>
<li>Every persistent DomainObject has an integer database ID.</li>
<li>Client DomainObjects are expected in the <code>\model</code> namespace because the framework derives Mapper class names from it.</li>
<li>A client DomainObject must implement <code>getInstance(array $row)</code>.</li>
<li>Each client DomainObject has a corresponding Mapper named <code>{ShortClassName}Mapper</code>.</li>
<li>Member classification is resolved as <code>\model\Member_{classification}</code>.</li>
<li>Every client Command must supply a login Strategy.</li>
<li><code>Command::execute()</code> must remain the framework entry point for command execution.</li>
<li>Command status is stored in the Request and determines rendering.</li>
<li><code>controls.xml</code> is compiled into <code>RenderComponentDescriptor</code> objects.</li>
<li>State-changing POST requests should validate the CSRF token.</li>
<li>Access tokens must be validated before the protected operation is performed.</li>
<li><code>AccessToken</code> does not provide expiration, one-time use or revocation by itself.</li>
<li>Database values must use bound parameters; free SQL fragments must never contain untrusted input.</li>
<li>Redirect and download-header values must originate from trusted application data.</li>
<li>Production applications must use <code>environment=production</code> and disable PHP error display.</li>
</ul>

<div class="success"><strong>Release 31 technical baseline:</strong> the diagrams and API signatures in this document reflect the actual Release 31 class hierarchy and the supplied Application Controller example. Application frameworks layered on top of Controller Framework may introduce additional classes, but they should preserve these framework contracts.</div>

<footer>
<p>Controller Framework 1.0.31 — Technical Reference</p>
<p>Prepared as PHPDoc-style developer documentation for client application programmers and framework maintainers.</p>
</footer>

</main>
</body>
</html>
