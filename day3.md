# KubeCon Day 3

## Keynote

### Zero Trust

This isnt about security, it's about trust.

establish enough trust in a system to perform a task. like testing the teperature of a bath.

what do we mean by trust? it's not a property of a system. it's based on other properties.

trust is an assessment, trust is a decision. trust is assymetrical. 

information overload degrades trust in tooling.

Trust framework

- contextual
- time sesitive
- assymetrical

Trust in computer systems in the cloud

What can we do?

- Patch management
- user account management
- awareness programs
- ask what happens if trust is violated

### Realtime media in k8s

- camera feed sent to studio
- mixing
- encoding
- distribution to cdn
- delivery over hls/dash

how does internet streaming work

http requests media playlist from server.

client gets media segments from server

repeat.

how does live video over ip work?

rtsp

client gets media streams for url from server

client and server negotiate udp ports for stream

server streams media to client ovr udp

#### K8s

kube-proxy & nodeport, no realtime
service mesg & load balances, no udp, no real time
host networking, everyhting, one pod per node.
mediastreaming mesh, no http, realtime media well.

#### Media Streaming Mesh

MSM Gateway pods with RTP Proxy and MSM stub.

Client checks msm stub, checks control plane, connects to msm stub endpoint in video stream pod. traffic will flow to proxy closest, sent across to rtp proxy on the node user connected too.

Benefits

- Observability, measure jitter and packet loss
- Security
- Distribution, replicate streams optimally
- Deployability, optimise cluster footprint.

github.com/media-streaming-mesh

### is k8s delivering on its promise

Shopify platform engineer experience

#### Key stakeholders

app devs, dont want to deal with complexity of infra, want building blocks, want golden path, full integration from dev flow.

Pms, fast time to market, avoid costly prod issues, security and compliance

platform engineers, scalabilioty, reliability, extnsibility

#### using a diy orchestrator

bash scripts, ssh into host, restart containers, no autoscaling.

app
service catalog
logging, ci/cd/security
orchestration
cmdb
networking
compute.

needed an army of experts. iaas + tools + best practice.

#### promise of k8s.

scalable container management platform, infra agnostic.

ready to assemble building blkocks, get started in only a few weeks. improved usbaility for app devs.

#### evaluation

portability - 5 stars, skills are portable regardless of underlying infra
reliability - 5 stars, architecturally designed for reliability
extensibility - 5 stars, custom resources, plugins etc.
scalability - 3 stars, good at scale, but limits/other constraints apply.
simplicity - 1 star, powerful but complex system.

##### complexities

yaml config complexity. managing at global scale.
painful k8s upgrades
multi-cluster management.

###### options to deal with complexity

simplest

- cloud + managed service (AKS/EKS)
- platform on cloud (build on top of vms)
- vendor platform
- build your own k8s platform.

most flexibility

Platform teams love kubernetes

## 1M lines of YAML at shopify

1500 app config sets, 1m lines of yaml, 350 prod clusters

Intent Management.

declarative abstractions are primary.

### Starting a platform

App init, pick a stack.

ERB. embed ruby in config files. It went bad. people did crazy stuff with it. e.g http calls. no interface. high learning curve.

rolling back was not deterministic. yaml re-rendered each deploy.

### New system

- safety, repeatable, revievable
- maintainable, programatical make updates reliably, versioned.
- universality, simple to start, modify, fast. arbitrary customization.

Config CLI -> helps author -> Config API -> generates -> Review Artifact -> generates -> Release Artifact <- pulls Release tool.

#### Config CLI

runtime add, edit, upgrade

#### Config API

yaml custom resource type. manifest.yaml.

#### Review artifact

manifest.yaml.lock, autogenerated from above, easier to compare changes.

#### rlrease artifact

autogenerated from above. replaces artifact refs to the real commit hash. cant do this earlier, because its part of the same repo and ci pipeline, it doesnt exist above. persisted in seperate repo.

-----

Kustomize does something like this.

### Principles

- give users a way to express their intent
- automate any changes your distributing widely
- make consequential changes visible.
- create release artifacts as early as possible.
- make it modular, allow apps to enter at any stage of the workflow. e.g dont need to use the config cli.

### MVP

Config API -> generates -> Release Artifact.

### KRM Functions frameowkr, config api toolkit

kubernetes resource model

```
apiVersion
kind
metadata
   name
```

krm function, a piece of code that performs crud on local resource
krm function spec. a standard
krm function framework. a toolkit in go.

a toolkit for writing go code that generates local k8s config.

validates and defaults the yaml.

## The next Log4shell, preparing for CVEs with eBPF

Log4Shell, allows remote code execution. 

send malicious string to vuln web app jndi:ldap://a

connects to attackers ldap server

executes java class.

how can we detect unpactched software, how do we know our clusters are safe, how can we detect if we've been compomised.

### eBPF

extend linux kernel with security and observability context/.

extend linux kernel to be kubenerntes aware.

can be hooked in at most kernel calls.

what can we answer?

- what binaries are running
- what versions are these
- what network connections are open
- is the network healthy
- what files are being accessed
- are connections encrypted
- is tls compliant?

constraints

- realtime
- minimal cpu
- offline and online

Tetragon, security observability and runtime enforcement

a framework to hook into the kernel, check stuff, and a mechanism to pull the results out of the kernel.

export the json events to SIEM (splunk)

check for late process execution (5 mins after start), detects remote shell exec as malicious.

prevention.

apply leasy priveledged principle for network connections.

DNS based policy, only allow FQDNs, not static IPs, use cilium.

## Building an Open Source Observability stack

greatest challenge in observability, 

- using multiple tools.
- shortage of skills

Observability

- Logs, a timestamp event 
- Metrics, numerical representation of a measure over intervals f time
- Traces, series of related evens as  a request flow through a distributed system.
   - Span, a single event within a trace.

Logging - fluentbit
Metrics - Prometheus 
Metrics, traces, logs - OTel
Metrics, traces, app profiles - PIXIE

### FluentBit

Why is logging challenging in k8s?
- pods are ephemeral
- diverse log formats
- lack of centralization
- perf

very plug and play logging

- source (jornald, tail, syslog)
- parser (json, regex, logfmt)
- filter (grep, k8s, nest/lift)
- buffer (memory, file system)
- router (match on input, tag/regexp)
- output (otel, big query, http)

vs fluentd, come from same source, fluentbit is a more scalable version of fluentd, however fluentd is more configurable.

### Prometheus

Why are metrics hard?

- pods are ephemeral
- millions of metrics
- different metric formats
- dimensional metrics
- centralized access

2 models of capturing metrics, pull and push.

#### Pull

a service to check all pods and get metrics. prometheus can control how much data is ingested. rather than being overwhelmed by being pushed too much.

#### Push

if you have a closed source app, pull endpoint might not be available.

apps push to a push gateway, prometheus pulls from it.

### OTel

a collection of standarized vendor agnostic teletry tools.

a universal format (metrics, traces, logs)

client libraries to instrument app

an api for sending and receiving data in OTel format

a data collector, can receive, transform and send data in OTel format.

OTel standard is a superset to prometheus.

OTel does not have backend

### PIXIE

Adding instrumentation is tedious. what if we could automatically collect this data.

eBPF allows you to dynamically program the kernel for o11y.

Pixie uses eBPF to capture fully body requests, network and cpu.

Pixie exports in OTel format.

### Project trinidad

anomoly detection for sec mon. learn what is normal.

Pixie -> OTel collector -> Kafka -> Jaeger and elasticsearch, hubeflow & mlflow

### Tutorial

https://github.com/pixie-labs/opentelemetry-helm-charts

Demo app -> OTel collector -> Prometheus, OTel/Jaeger, Fluentbit/Loki <- Grafana -> Pixie

Clear text with eBPF, can see sensitive date. doesnt leave pixie, until it's been filtered and agregageted and sent to the client dashboard. Pixie also has PII sanitizers.

Dont collect eveyrhintg, work out whats important, filter, aggregate. then on demand you can request more via tracing.

## CloudNative Edge

resources on the edge, expensive compared to datacentre. lots of arm based, lack of gpu.

KubeEdge brings k8s to the edge.

started in 2018. 

2020 became cncf incubation.

1st kubeedge vehicle 2021

1st kubeedge satelite 2021 launched. only online 6-8 minutes, 6-10 times a day/

kube cluster is about 1gb memory. kubeedge is 60mb.

CloudCore to EdgeCore communicates with WebSocket.

Future is Device Management Interface (DMI) makes the device appear as a kubernetes service, so you can naturally interface with it.

one of the first l4 slsa cncf projects.

## Reflecting on Large Scale Cloud Native modernization

Cloud architect - RedHat
Managing architect - RedHat
VP - ?

Konveyor - CNCF proj konveyor.io

Moving containers on prem -> cloud.
Mainframe -> cloud ready

What are the biggest enterprise challenges?

- Thinking about it as a one time thing. it's not, it's continuous.
- understanding why we're doing this. measure the why. are we seeing what we thought we would.

What should enterprise consider when theyre just starting.

- Create working groups and get allignment.
- PilotBrush
- Select one app, learn and feedback into the process for the next app.

Who;s in the room at the begining, are these the right people, normally not.

Governing body can be a constraint, especially in healthcare, finance etc... get these people involved early.

- Business Leaders
- App Owners
- Architect
- Ops
- Cloud Native team
- Infra
- Network
- Data
- Security

Lessons learnt

- Lots of software not in approved list
   - work with stkaeholder to get this approved early, help them understand what secure software is now.
- Can deploy much faster, but if the governance process isnt there, this will become a blocker. educate early.
- dev team, have to transition there working practice to agile.
- security, bring them in as early as possible. they arent the expert on devsecops and k8s. educate early.
- tech debt, dont tackle it all when migrating. need functional parity. MVP to get to cloud.

Migrations are more of a people problem rather than a tech problem.

Need org buyin.

try and do as much analysis up front, go in with eyes open.

Managed services (AKS/EKS) helps you get going faster.

Konveyor can help you understand your apps, and work out if AKS/EKS is suitable.

Theres a lot of gates before you can end up in prod, like security. migrations normally fail because of these gates and not getting buy in.

dont try to reinvent the wheel, use industry best practice. ensure you can scale and not get bogged down in custom stuff.

## Smarter Golden Signals

intuit - fintec, all run on k8s.

- 275+ clusters
- 20500+ namespaces
- 2500 production services
- 900 teams
- 6000 developers

Metrics

- telegraph metrics for cpu/mem et.c
- prometheus for k8s components
- kube-state-metric for pod states
- active monitor metrics - synthetic monitoring

On call person looking at all of these source from numerous dashboards.

100+ alerts per cluster

how manys services are down, is it service vs cluster issue, blast radius.

What platform engineer wants

- reduce mttd and mttr
- less false positive and negatives from, alerts
- a few good quality signals

### Cluster Golden Signals

#### Service Owner Concerns

Availability -> cluster control plane

Scale -> cluster autoscaling, hpa

Correctness -> cluster authentication, networking, critical addons.

----

Golden pillars for a service

- error rates
- latency
- saturation/capacity
- requests/traffic

States: Healthy, Degraded, Critical

Error Golden Signal

static threshold not appropriate. tax services spike seasonally, this is expected. every cluster is unique.

Anomoly detection, zScores

- +-1 healthy
- +-2 degraded
- +-3 critical

Assumes natural distribution, if things spike on a downward spike, doesnt detect.

Numaproj

realtime analytics and obs on k8s.

Numaflow -> massively parallel real time metric anomoly scoring.

train a model, let is analyze prometheus metrics, if over threshold, raise alert, push back anomoly score into prometheus.

No ML knowledge required to consume.

auto-encoder ml model. tries to learn normal behaviour.

retain about 5 models, during an incident, the model starts to learn the behaviour is normal. after incident is cleared, can delete the bad models.