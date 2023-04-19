# KubeCon Day 1

## Keynote

10,000 attendees, largest OSS conference in europe
58% first time attendees
2,000 on wait list to attend

next year in paris march 19-22

in the beginning focussed on kube and containers, now much more than that
159 projects
1,300 maintainers
200k contributors

new CNCF project progress reports

### 4 new company members
EY, Hitachi, Infosys

#### Infosys 

4 key client problem areas
Cloud security
Cost
Paas
Automation

Their approach
- AI adoption across lifecycle
- Reign in costs
- Platform based approach

Infosys live enterprise application development platform

#### HCL

number 1 enabler of kubernetes. 
1,000 enterprise clients enabled with kubernetes
200,000 engineers working for HCL

----

### Cloud native mentoring

Mentee -> Mentor -> Tech Lead SIG, Memver Steering Group, Memeber Oversight Committee.

Please participate: mentoring.cncf.io, about to start next tranche of mentoring.

glossary.cncf.io available in 8 languages.

### Inside Envoy

The proxy for the future

## Tulips, Terabytes and Transformations

The end user community

need to save time.

Wisdom of the cloud newsletter

cut through the noise and let you know whats going on.

CTO summit, exploring the foundations of cloud native maturity.

KubeCon EU Survey

### Cafe

Sabine Wolz - Senior PM at Mercedes
Kasper Nissen - Lead Playtform Architect @ Lunar
Segiu Petean - Head of DevOps @ Allianz
Yuichi Nakamura - Director @ Hitachi

#### How has the CNCF ecosystem helped your org stay up to date with latest cloud native tech

community helps break barriers in large companies and between dev and ops.

#### In what ways has cloud native helped better work with customers

unlock velocity by adopting cloud native and kuberenets, 360 ownership of the services, focus on features.

Digital sovereignty -> be part of a free, open source community, Create vs consume.

#### Cloud native challenges

Auth n and z. FIDO.

### Sustainable computing

Project Kepler -> captures power usage metrics from kubernetes clusters.
Kubernetes based eficient power level exporter
eBPF and ML to calculate power consumption

### Granduated projects

Envoy
Flux
Fluentd and fluentbit (observaibility)
Harbor
Linkerd
Open Policy Agent
Prometheus
TUF
Vitess

### Building a sustainable carbon aware cloud - Azure

Jorge Palama - Principal PM MSFT Azure

Green software principles - green software foundation
- Energy efficiency -> make sure code is efficient
- Hardware efficiency -> 
- Carbon awareness -> clean vs dirty energy -> when to do more or less.

Carbon density, the ammount of carbon generated for the amount of energy we use. gCo2/kWh
Carbon aware KEDA scaler.
Demand shaping
- Get current carbon intensity
- Get KEFA targetRef saling inputs
- Updates targetRef max replica count based on carbon intensity

CNCF sustainability TAG

### Building a CNCF project community.

Be proactive and ask people to contribute

No time to onboard -> fewer contributors -> maintainer burnout -> viscous circle.

Be specific and ask specifc people for help with specific tasks. e.g, review this PR.

use cncf templates to clearly document governance.

onboarding docs, start with something, then the new people can improve where lacking.

contributor ladder to move people up to leadership roles.

mentoring.

Setup project for success after you move on. emeritus roles.

Be strategic about where you can best spend your time to grow your contributor base.

CNCF TAG contributor strategy

1 hour a week to improve contributor docs or help a maintainer learn to do something new.

## It is more than just correlation, a debug journey

Type - Used 
Alerts - All
Metrics - Some
Logs - All
Traces - Not
Network Events - Not
k8s events - All
k8s objects - All
k8s links - All

Alert raised (pagerduty/slack) -> SOP/runbook -> look at app dashboard -> pod logs -> older logs -> pod events -> restart

Hands on troubleshooting requires greatest amount of time
query metrics
reviewing config
high cognitive load

### Chalenges
it takes time to get up to speed with k8s system

### How can we provide SREs with the signals they need

Solution: Korrel8r.

https://github.com/korrel8r/korrel8r

Correlating overvability signals - following relationship to find related data in multiple sources.

Broader connotation

Bring together signals, allow SREs to focus on only data that matters.
Reduce time spent gathering data, jumping around.

Open source project founded in 2022 at redhat.
Correlation engin using a set of tules descriving relationsgip between observability signals.
Standalone go binary.

k8s api
preometheus api, thanos,
loki.

Redhat open shift - alert page

Ask korrle8r about the alert (paste url), can see deployment, events, pods, logs.

Redhat open shift is redhats kubernetes interface.

## Using OTel for AppSec

Ron Vider CTO Oxeye

Apps and vulns are now distributed

### Examples from the wild

dist vuln in Harbor CVE 2022-2880

### Intro to OTel

instrument, generate, collect, export telemtry (metrics, logs, traces) to analyze software perf and behaviour

Helps us to understand service to service communication

- Vulnerable Flow Analysis
- Vulnerabilities Validation
- Public Exposure Verification

OTel wasnt designed for AppSec

Missing lines of code -> Collect call stacks
Missing instrumentation -> Custom sink instrumentations

- Open source, 3rd party and custom code analysis
- Application Flow tracing
- Infrastructure Configuration
- Recalculate Severity

Modern problems require modern solutions
Risks assocaited with security of cloud native apps are evolving, requiring updated security testing methods
OpenTelemetry is designed for observability, but also can be used to trace vulns
Observability is crucial for improving security in modern software dev

oxeye.io/blog

## Protecting your Crown Jewels

### What is secrets management

credentials, api keys

deals with eveyrhting regarding the life cycle of these secrets, created, accessed, stored, transfered, rotated, retired.

Expiry - eternal vs ephemeral
Creation - manual vs auto
dependency - external vs internal vs none
consumer - user vs app

#### Where do we need secrets?
- dev laptops
- CLIs, UI logins
- CI/CD
- terraform, deploy keys, container reg
- Operation Envs
- infra, apps, cloud/on-prem

#### Centralize and win
- secure storage
- auditing
- auth n and z
- rotation apis

LastPass for user secrets
Azure KeyVault

#### Challenges

- Secret Sprawl
- Lifecycle management
- Access control
- Integration
- Bad practices

### External Secrets Operator

successor of godaddy external secrets
inital commit nov 20
CNCF in 2022

Give me a secret, pass to kubernetes

zero config auth - Azure workload identity
lifecycle management
secret distribution
cross-cluster sync
secret templating
fetch and aggregate multiple secrets
extract scretes from structured data
designed for multi tenancy purposes

Can deploy ESO in workload cluster and pull from tooling cluster
or deploy in Tooling cluster and push to workload clusters.

