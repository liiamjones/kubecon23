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

## Takeaways

compositions and claims with crossplane

CI/CD is vibrant, best practices still emerging.

Monitoring and observability is key

multi-cluster, mult-tenancy is quite hard to figure out.
