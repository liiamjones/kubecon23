# KubeCon Day 2

## Keynote

WASM working group - newest shinest thing

Platforms whitepaper tag-app-delivery.cncf.io

Proposal for observability query standards working group

knowledge glaciers -> depp knowledge, compacted over time.

onboarding takes about a year, relies on existing guides and docs.

how to onbaord in opensource?

- Handoffs between maintainers
- succession planning.
- look for where informations exists
- repos have some but not all 

- Start small, build trust and rapport
- find gaps and lack of clarity.
- contribute this back for the next person who follows.

monocultutres are morelikely to fail.
empower individuals to be succesfull and give them space to grow.
let them lead, become a resource for them.

### MLOps on highly sensitive data

explainable AI, understand why a decision was made.

sampling station, data tokenization process.strict micro k8s

confidential computing at the cloud layer

## The ins and outs of the cloud provider in kubernetes

KubeControllerManager
CloudProviderInterface
CloudControllerManager
removing cloud cojntrollers from kcm so they can be deployed via ccm

### CCMs

four controller loops, 

node controller, responsible for updating status and labels on nodes, syncing zonal and region info, net addr, host names.

node lifecycle, responsible for upd and delteing nodes that have been removed from cloud

route controller, responsible for configuring routes so containers on dif nodes can communicate with each other.

service controller, responsible for updating load balancers

in-tree provider has been deprecated, out-of-tree is the future.

run ccms as early as possible

AKS/EKS you dont need to worry.

run ccms on control plane

### HA

use leader election to avoid conflicting control loops

pod distribution budget can help avoid disruptiom by preserving min number of pods during sclaing

### Problems

is node still tainted as uninit, is ccm running?

is node labelled with topology info

has cni initialised the node

github.com/kubernetes-sigs/cloud-providers-azure/issues/3500

go read the issues.

CloudProviders are expressed in very different ways.

### Cloud provider framework

can build your own cloud provider for your own hosted k8s.

cloud provider interface, defined in cloud.go

optional interfaces, not all required, not all cloud providers implement all of them.

CCM will log all interfaces not implemented on boot.

- Instances for in tree KCMs, deprecated.
- InstancesV2 for out of tree CCMs, better perf.

If your building CCM from scratch, you dont need zones anymore.

github.com/kubernetes/cloud-provider (sample)

kubernetes-sigs GH org is where to put new cloud providers, kubernetes org containers older providers.

## Tips from the trenches - GitOps at Adobe

making it easier for developers to experiment

Adobe company wide migration to adobe 2019

deployment platform on top of the argo proj 2022

streamling devops practices with argo 2023

### Foundational practices

trunk based dev

CICD and automated rollback

Wave deployments

### Challenges

i want to deploy apps in particular order. use sync-wave from argo-cd

hooks are not desired state - post sync job. tranisent.

running post deploy tests - make tests desired state

env config - seperate source code from config code. 2 seperate PRs are dependant. If it's done in a single repo, can have atomicity. 

use helm templates for k8s manifest and seperate envs. use an umbrella chart. 

### Managing apps and infrastructure

every commit gets to prod as fast as possible.

terraform. most teams use (600 engineers)

seperate infra repo.

terraform enterprise -> workspaces.

Ops provide private module registry. Devs consume.

too many tools, cant scale teams if they all need to know everything.

### A Unified approach

changes to apps and infra are managed and deployed through a single CI/CD tool.

Cross-plane. open source k8s extension. use managed resources and connect to public cloud using cloud providers.

single app  and infra  repo.

custom resource health checks in argocd.

### Takeaways

compositions and claims with crossplane

CI/CD is vibrant, best practices still emerging.

Monitoring and observability is key

multi-cluster, mult-tenancy is quite hard to figure out.

## KEDA deep dive

the problem -> app consuming from rabbitMq. want to autoscale the app.

Can use HPA to autoscale with kubernetes (based on cpu or memmory), might not correlate with message number.

Introducing KEDA, scrapes metrics from rabbitmq and scales k8s app.

kubernetes event driven autoscaling, dead simple.

scale any deployment based on events.

60+ event sources.

Built ontop of k8s.

ScaledObject/Scaled Job. defined scaling metadata.

Manages workloads to scale to 0

publishes metrics HPA which makes most scaling decisions.

HPA -> horizontal pod autoscaler.

ScaledJobs usefuk for long running process.

### new features

architecture changes. open connection to external source from both metrics adapter and controller. Now only from controller. controller now talks to metrics adapter.

certificate management. automatically providing local certs, users can bring thier own certs. TLS 1.3 between components.

by default, KEDA will be safe.

Validation webhooks -> scaledObject validations. will reject if autoscaling already setup on the workload.

Prometheus metrics -> added /metrics at port 8080.

Kubernetes does not support float. e.g 4800m/5 is 4.8/5

Average, Value, Utilization.

### Best practice

polling interval is only relevant to 0-1 sclaing. 1-N is controlled by HPA. by default 15s

consider using metric caching, metrics are only scraped each poll, then cached in the operator.

HPA scaling behavior -> Stabilization window, prevents replica count flapping (makes scaling smoother). Scalining policies control the rate of change of replicas while scaling.

## Processing of Amsterdam city data with vendor agnostic serverless functions.

What is serverless? Lots of people think AWS lambda. CNCF define it as apps do not rquire server management, simple deployment model, auto scalled and billed.

- Autoscalling to zero
- Event driven
- Exceptional UX

Serverless is a deployment model.

Functions/FaaS is a programming model. deploye as a serverless app.

ideal serverless workload is stateless, short running, http based or event driven.

ther serverless pattern. event -> trigger > app -> produce -> results

### Knative

k8s based platform to deploy and manage modern serverless workloads. Incubating project since march 22

serving, scale from zero

eventing, common infra for consuming and producing events.

functions

vendor agnostic, deploy anywhere.

IDE extensions. knative for vscode or intellik. The desired state of functions, eventing and serving auto refreshed.

### Demo

OPen shift running on AWS. react app + node.js baackend. running in kube deploy, coms via socket.io. backend rest api. backend emit CloudEvents

cloudevents.io a spec for describing an event in a common way.

knative eventing. uses standard post requests to send and receive cloudevents. components that route events from prooducers to consumers. Broker, Trigger, Sink Binding.

hit add knative function. supports multiple runtimes, templates.

a func to add code to.

can run func deploy from vscode. will build and deploy.

build packs automated the process of building container images. buildpacks.io

knative serving -> simplified deployment model for staeless apps. demand based autoscaling, seperaton of code and config. traffic splitting, custom rollout strats.

### On cluster build

build without a local container platform (docker)

tekton pipelines

pipelines as code

triggers a build of the func on a cluster.

tekton.dev
pipelinesascode.com

func config git set

## Deliver applications efficiently with Cloud Native Platforms

GitOps principle, Operator white paper

CNCF TAG app delivery

enable cncf projects and guide cncf end users

platforms enable efficient app delivery.

building on the shoulder of giants.

a platform consolidates a collection of capacbilities that an app needs in a coherant way.

in the cloud native world, bringing together o11y, eventing, databases etc..

platform whitepaper.

reduce cognitive load of developers.

A load of yaml files to bring in dependencies.

app manifest picks up all that stuff and puts it in the cluster.

### Platform attrbiyes

- product mindset
- devex
- docs
- self service
- reduce cognitive load
- optional composable
- secure by default

### Capabilities

- portals, apis
- templates and docs
- automation
- dev envs
- o11y
- infra
- data and messaging

### Operators

aid lifecucle management beyond k8s primitives.

abstract a lot of platform toil.

operator watch declarative requested state, reports crrent status and applys changes to managed resources.

manage your platform as a product.

abstract away the complexity

crossplane abstracts away resource details, providing modules to be consumed.

Operator of operators, manage other operators.

Operation lifecycle manager

operator sdk.

Popular frameworks, kubebuilder, cncf operator frameworks, 

Emerging frameworks, metacontroller, juju (enterprise multi-cloud)

### Applications

not a clear definition of what an app is.

what is a cloud app? an app, could have one service, or multiple services. services can scale indepentantly. infra and config can be seen as part of a service.

What is the problem?

- services are not always dev'd and tested individually.
- knowing when a app deploy is finished
- workload health check vs app health.
- standardization - how to switch between tools?

Application definitions

- whole app is defined in resource (kubevela)
   - all the components are in the manifest
- reference to source is stored in resource (argo)
   - only have a ref to a git repo in manifest, dont know what the workload is
- reference to objects is stored in resource (keptn)
   - list out references to workloads

TAG wants to come up with a standard app def

tag-app-delivery.cncf.io

## Choas Engineering

Simple -> linear, predicatable, comprehensible
Complex -> nonlinear, unpredictable, impossible to build a complete model

Choas Engineering Book - chapter 1.

traditional systems, slowly moved into cloud native -> kubernetes -> kubernetes components

cloud native -> more microservices, third party tooling.

Testing is hard. Enter choas engineering

chaos monkey -> principles of chaos engineering -> cloud native chaos engineering.

the discipline of experimenting on a susyem in order to build confidence in the systems capability to withstand turbulent conditions in productions.

injecting failures into the system, to see what would happen, before it happens in prod.

testing makes an assertion on a property

experimentation proposes a hypothesis, proven or disproven.

### principles

1. define steady state
2. hypothesize steady state will continue
3. introduce real world variables (e.g latency)
4. try to disprove hypothesis by looking for difference in steady state between control and experiment group.

run experiments in production, reduce blast radius (e.g only select nodes with apps that are okay to fail), continuos.

### Chaos Mesh

a tool for doing chaos engineering on k8s. has physical experiments. e.g pod kill, latency, kernel chaos. cloud integration. dashboard for analytics.

chaos workflows, linear or parralel.

install choas mesh on any cluster via helm. choascontrollermanager and daemon gets installed.

multiple CRDs, one per experiment. experiments defined as resources.

v2.5 introduces multi cluster chaos experiments, http chaos tls support.

cert manager and lets encrypt to add tls support to your app.

choas mesh, connect to cluster using tokens create in kubectl.

### Demo (network latency)

create networkchoas resource, adds 10ms latency to label selector.

kubectl apply networkchoas.yaml

### Demo (multi cluster)

remotecluster choas resource, namespace, create ckuster 1 secrect, pass to cluster 2 choas yaml.

apply in cluster 1, run in cluster 2.

Cluster2 has burn cpu applied.

### Demo (workflow)

cretae new workflow from ui

### Recommendations

Chaos Engineering is mainstream
Learn the theory
Continuous chaos, hook into CI/CD