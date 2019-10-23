# Antidote v1.0 Mini-Projects
## MP1: Syringe Redesign

The Antidote platform requires a concerted effort in order to get it to what
could be considered a "v1.0 architecture". As a key component to the platform, Syringe
requires a large portion of this focus. This document outlines the first mini-project
involved in this effort, titled "MP1".

The first portion of the document section can be read much like a design document. Its job
is to describe the intended "end state". The latter portion of the document will cover
the concrete steps and milestones for getting there.

> **NOTE** - this is NOT intended to describe the final state of Syringe. v1.0 plans are just that - v1.0 plans.
> The point of this effort, in large part, is to make it easier to make future changes to Syringe.

## Existing Architecture and Historical Design Requirements

A year ago, the most important thing to do was to put **something** out there that showed the promise of
curriculum-as-code and full automated on-demand lessons, so that the community could start rallying around (and start
contributing to) a reference curriculum (NRE Labs). Since we knew NRE Labs was never going to be a revenue-generator,
it was okay for us to make short-term decisions that would otherwise seem crazy - things like keeping all state in
memory in a single binary that would occasionally crash and wipe its own slate.

The existing monolithic architecture of Syringe was intentional - the name of the game initially was getting
a PoC out quickly, so that the community could get their hands on the project ASAP. The important parts of Syringe
weren't how well it scaled, or how easy it was to understand or extend - it was the abstraction offered
to lesson authors, and how well it automated the setup of new lesson resources on behalf of the user. This is what
conveyed the value of the platform, and scalability and stability frankly didn't really matter that much.

The current Syringe architecture is quite simple. It all compiles to a single binary: `syringed`, but is logically
built of two components - the scheduler, and the API server:

![](images/syringe_old.png)

At start, this binary loads the entire curriculum into memory. It also maintains an internal map of
which lessons are active and what stage they're running, etc. In short, everything is contained within
this single program - both from a code perspective, as well as runtime state.

This simple approach has several advantages:

- Single binary, easy to deploy
- No external database to worry about
- Allowed us to get NRE Labs to a public PoC quickly

However, it also has some key disadvantages:

- `syringed` is a single point of failure
- Everything is tightly coupled, so extending Syringe's capabilities is more difficult.
- State is kept in-memory, so if Syringe is restarted, state is lost. This is why we currently kill
  all running lessons every time Syringe starts.
- Fairly opaque - all monitoring is custom, and relies primarily on good logs.

## High-Level Overview of v1.0 Design

There are a few things that we have learned since we started the project, and together, they're driving the
need for a new architecture:

- Usage of NRE Labs is growing rapidly, and we're encountering use cases that will rely more heavily on its
  availability. An architecture that tolerates failures and scales seamlessly is needed.
- Increasingly, our community wants to work with Antidote directly, and it's important that we invest more time in
  making it not only easy to understand, but also easy to operate, and easy to extend.

This new Syringe architecture - which will be outlined in detail in this document - can be summarized in
the following points:

- Breaking up the logical services currently offered within `syringed` into stateless, horizontally
  scalable microservices, connected via standard communication platforms like pub/sub messaging, gRPC, and REST.
- Manage all state via an external database
- Add better logging and observability instrumentation to aid in better production debugging

![](images/syringe_new.png)

The "message bus + database" design is a popular and well-understood distributed systems pattern. Projects
like [StackStorm](https://github.com/StackStorm/st2) use this architecture to get the best of both worlds.
In short:

- The database is used for managing state, so that all services can be stateless, and can operate without worrying
  about stepping on each other (database will handle locking and transactions).
- The message bus is used to notify various components that they have work to do. Syringe services will listen
  for certain message types so they know when they need to perform certain operations (pub/sub).

This design has some key advantages over the current approach:

- **Better Resilience** - There's now no “one syringe”. All services are stateless, so the important services
  like the scheduler or the API can be horizontally scaled for HA.
- **More Extensible** - The events sent on the bus are well-known, and any software can subscribe to
  those events without Syringe even knowing about it.
- **Easier to Understand** - It will be easier to reason about, maintain, and contribute to individual services.

## Design Detail

Now that the high-level design is out of the way, there are a number of design areas that should be
explored in further detail.

> Note that these are not ordered in any way - the sections to follow merely explore a specific part of
> the new design for clarity. Read to the end of this document for an execution plan.

### Syringe API

#### Questions

- Stick with gRPC + REST? Or go with either pure REST or pure gRPC?
- Regardless of if gRPC or REST, a javascript client lib should be generated that antidote-web can use.
- Make sure the [context](https://golang.org/pkg/context/#Context) passed in gets forwarded throughout the system - you're currently duplicating that functionality.

### External State

- https://jbrandhorst.com/post/postgres/
- https://medium.com/@vptech/complexity-is-the-bane-of-every-software-engineer-e2878d0ad45a

One of the most fragile parts of the existing model is that all state is held in memory. As part of this project, we'll be moving all tracked state to a proper database. This will result in 



Having state in an external component means we can allow everything else to be stateless.

The database will primarily be used for state management, so we can use a common source of state for all components, complete with all the necessary features like locking

The API can easily be stateless because it's just designed to translate API calls into message queue messages essentially, or perform reads on teh database. It won't make direct writes to the DB. As a result, this can scale out easily.



This section outlines
the new model, which is that Syringe will be back-ended by a Postgres database. This database will still be fairly
simple, as there's not that much state to deal with, but we should still be clear about what state there is,
and how it will be managed.


#### Current Tracked State

All state is currently tracked in memory, and can be summarized in the list below:

- [KubeLabs](https://github.com/nre-learning/syringe/blob/e903877229b48d600b06e397454be3225d657956/scheduler/scheduler.go#L63)
- [LiveLessons](https://github.com/nre-learning/syringe/blob/e903877229b48d600b06e397454be3225d657956/api/exp/server.go#L38)
- [VerificationTasks](https://github.com/nre-learning/syringe/blob/e903877229b48d600b06e397454be3225d657956/api/exp/server.go#L43)
- [GcWhiteList](https://github.com/nre-learning/syringe/blob/e903877229b48d600b06e397454be3225d657956/scheduler/scheduler.go#L61)

There are a few things that should be said about the above:

- We will not need the VerificationTasks to be tracked in the new model. All lessons that have an optional objectives
  will be constantly watched for objective verification.
- The relationship between KubeLabs and LiveLessons should be re-evaluated. Mostly, LiveLesson is just an API-friendly
  version of KubeLab, which is where we are currently placing all created kubernetes objects. The very first
  consideration to make is if this storage is even necessary - it's likely we don't even need kubelab at all, and
  livelesson can be made to be much simpler by only containing fields that are relevant to running state.

#### Model Definition

Currently, all data models in Syringe are defined in protobuf files. This was done to automatically generate
Go structs to be used throughout the codebase, as well as provide a basis for easily providing access to that
data programmatically via gRPC. We also made use of the [`protoc-gen-validate`](https://github.com/envoyproxy/protoc-gen-validate) tool to provide basic validation functionality (ensuring strings are a certain length, etc).

This works fairly well, and the go-pg translations from native Go types to Postgres data types seems straightforward.
These native types are almost always way generous (i.e. text vs varchar) so we should do two things to compensate for this:

- We can always override via [tags that we can inject right in the protobuf definition](https://github.com/favadi/protoc-go-inject-tag).
- The Lyft validation tags can also add restrictions to the 

Using the above two methods, we can keep not only the database types, but also their constraints, right in the
protobuf definition files, rather than maintain two separate model locations.

This intersects with a common design consideration when building database applications that also have an API.
Sometimes it's necessary to build one model for the database - often much more verbose - and a second for the
API that's been trimmed down or transformed in some way to help the API stay lean. In the case of Syringe, I am
leaning towards the position that this is not necessary, and that we can get away with a single model for both
purposes. There are two main reasons for this:

- There's not much that we will want to store in the database but not make available directly through the API
- For corner cases where there's a type that has some fields that we want to store in the database but we don't want to be shown in the API, this is easy enough to handle on a case-by-case basis for now.

In short, I don't currently see a need to maintain two separate models, which would add unneceessary complexity, as well as the need to maintain a translation boundary between the two.

New database schema for Syringe:

<iframe width="560" height="315" src='https://dbdiagram.io/embed/5dae815e02e6e93440f27a53'> </iframe>

A few notes about the above data model:

- The `lock` field in the livelesson table allows us to ensure that only one change to a livelesson is being processed
  at one time - the scheduler processes should use this to avoid contention.

In addition, since the scheduler will be writing to the database (as will the GC process), we need to make sure we can perform transactions on a per-UUID basis. Need to put more detailed thought into this part.

The [`go-pg`](https://github.com/go-pg/pg) is a very popular ORM for Go, and we'll be using it for database operations.




The only thing we'll have to figure out is if we need to import lesson definitions into the database. On one hand, I like the idea of pure curriculum-as-code - meaning the files on the filesystem, cloned from the Git repo, are the source of truth. However, this would mean we'd have to be make sure that we have the same version of the curriculum always in the right spot for all the microservices, which may be on different machines.

On the other hand, we could do what StackStorm does with packs, which is to provide tooling to import a curriculum's "code" into database constructs. The microservices then access that data, rather than all of them accessing their own localized filesystems. The curriculum-as-code is still the original source of truth, but it's been placed into a centralized location for all to access. Maybe a "syrctl import" command is needed to facilitate this.





#### Questions

- The UUID for users is generated on clients, but we probably don't want to use that for the primary key.
  How to prevent collisions here? Should we try?
- How will database initialization be handled?
- Need to figure out a way to represent a user ID that isn't bound to a specific platform. Like, we **think** we will
  use Discourse but we may use any of the platforms in MP6. The data model should account for this.
- Don't forget about [migrations]. When will we need them? How can we limit the need for them? (Maybe we just
  wipe out and re-initialize the database between versions - we don't need to save anything). If we go this route,
  we should write the Syringe version during initialization, and each service should check this version on startup
  to ensure it's the same as their version, and if not, quit.
- TODO - add security controls here. We should be storing state to do rate-limiting of some kind.

### Microservices and Messaging

In the new Syringe architecture, there are a number of services

Services:
- `syringe-api`
- `syringe-scheduler`
- `syringe-gc`
- `syringe-stats`
- `syringe-checker`

These will all be discussed in detail below.

![](images/messaging.jpg)

These services need to communicate with each other to be effective - the chief use case is for the API service to
inform the scheduler service that there's work to do (e.g. spin up kubernetes resources for a new lesson). To
facilitate this, we'll use the [NATS messaging platform](https://nats.io/), as the nature of Syringe's inter-service
communication aligns well with the publish-subscribe messaging model that NATS fits nicely into. NATS is also written
in Go, and built to be extremely fast, and simple.

> I believe we can get away with plain NATS, and avoid using [NATS Streaming](https://nats-io.github.io/docs/nats_streaming/intro.html).
> The only service that absolutely must receive messages is the scheduler, and we'll
> not only be running multiple parallel processes for this service, each scheduler service will handle incoming
> requests in a goroutine, which means there will always be something to process incoming messages.

When the API sends a message to the scheduler to spin up a lesson, we should leverage
[request reply](https://nats-io.github.io/docs/developer/concepts/reqreply.html) to ensure a scheduler
handled it. If this fails, that means no scheduler is available and the API should mark the livelesson as failed.


Message queue design. As long as there's a component to translate events to achievements
then we will pick them up. If not then we will configure the messages to time out and they just go nowhere. Allows us to just plug things into the bus to enable the functionality

The design allows us to enable or disable features simply by starting the relevant processes to listen on the message queue or the Syringe API. If we don't want to export to influx, we simply don't enable that process. If we don't want to enable gamification, don't start the translator process. All messages like this will be sent with a TTL, so if nothing is there to pick messages up off the queue, they'll disappear after a while.


These are the core services, but we might want to develop a service that listens for known Syringe events to do some external integration, so having a messaging system that we can just send events into without caring about subscribers is nice. It's then up to the subscribers on what they do with that information.

If centralized messaging is chosen, [NATS](https://nat.io/documentation/) is the likely candidate for this.
It's simple, fast, and built in Go (which Syringe is as well).

> [This blog post](https://storageos.com/nats-good-gotchas-awesome-features/) does a good job of covering the pros and cons of using NATS.

Since we're using NATS, we need to first understand the types of messages or "events" that will be sent to NATS, and subsequently received by other services:

Events:
1. `NewLiveLesson`
2. `StageChangeLiveLesson`
3. `DeleteLiveLesson`
4. `LiveLessonReady`
5. `LiveLessonFailed`
6. `ObjectiveComplete`

Next, we'll describe each microservice, and discuss how they behave. They will all need to read from the database,
but we'll also mention below which ones need to also write to the database, as well as which services subscribe to
which events, and in which manner. We'll also mention which services publish which messages.

> these will be given subject names with a hierarchy that groups similar messages together. This will allow subscribers to receive the messages they want more easily.

#### `syringe-api`

Writes to DB: Yes
Subject Subscriptions: None
Queue Subcriptions: None
Publishes: 1,2

#### `syringe-scheduler`

Writes to DB: Yes
Subject Subscriptions: None
Queue Subcriptions: 1,2
Publishes: 4,5

#### `syringe-gc`

Writes to DB: Yes
Subject Subscriptions: None
Queue Subcriptions: None
Publishes: 3

#### `syringe-stats`

Writes to DB: No
Subject Subscriptions: None
Queue Subcriptions: 1,3,4
Publishes: None

#### `syringe-checker`

Writes to DB: No
Subject Subscriptions: None
Queue Subcriptions: 1,2,3
Publishes: 6

We'll need a [centralized messaging package](https://keepingitclassless.net/2019/10/keeping-nats-connections-dry-in-go/) to give the various Syringe services a single place for:
- Event Definitions
- Constructor functions for setting up [communication channels](https://github.com/nats-io/nats.go#using-go-channels-netchan)
- Observability instrumentation (will still need API to be instrumented)

The blog post above was written precisely as a prototype for how this will be laid out in Syringe, so please
use that blog post as guidance for how this package should be built.

We **should** be able to implement this comms package before even breaking Syringe up - as the scheduler
and api portion of Syringe today communicate directly and those events are maintained in code. We could replace
those with a comms package before breaking the services apart.

Further Reading:
- https://solace.com/blog/experience-awesomeness-event-driven-microservices/

### Objective Verification

Maybe we could use NATS for this - when a stage is activated, a message will be sent to subscribers, which will include a set of `syringe-verify` services or something like that. The job of this service is to listen for lesson GC or stage change (or lesson start) events and add verification tasks as needed. It will maintain state for which <lesson>-<session>-<stage>-<objective> is being checked in which pod, and updating the back-end state accordingly. The API simply periodically checks the state.


### Observability and Logging

There are two questions we currently don't have very good answers to:

- **Are users having problems?** - Monitoring components is easy, monitoring the end-to-end user experience is hard.
  We don't currently have a good way to know how many of today's launched lessons were launched without issues. This
  is obviously not ideal.

- **If they are, what can we even do about it?** - In the 0.01% of cases where users find a way to get feedback to
  us, all of the context is lost. Someone might find a way to tell us that such and such a lesson isn't working, but
  reproducing the problem is almost always difficult - many issues, especially with the content, are intermittent.



https://opentelemetry.io/
https://opentracing.io/specification/

https://github.com/nats-io/not.go



When to record spans? Receipt? Send? Both?

This is MOSTLY a syringe thing, but will also include changes in antidote-web
Threads:
- Component observability - Better structured logging (I haven’t been the most sanitary here) with option to send to remote collector
- System observability - Tracing from web front-end all the way through every syringe microservice. Allows us to leverage inherent cardinality based from initial session and request ID.

https://peter.bourgon.org/blog/2017/02/21/metrics-tracing-and-logging.html

Not easy to debug stuff
- It is really, really hard to see a user’s path through the system right now with flat logs. Debugging problems is nearly impossible right now, because of two main insufficiencies
-Not enough context is given for each log message.
-Flat logging won’t give us any insight whatsoever into how well the solution is scaling. This will be especially true if we fix Problem #1, as we’ll invest time into breaking Syringe apart into microservices but with flat logs we’ll be operating blind.

Solution:
-Logging is still good, but it needs to be structured, and it needs to be done in such a way that all the context is preserved, so we can filter on a UUID, for instance
-Instrument the code for distributed tracing so that we can export traces to standardized distributed tracing tools like Jaeger and Honeycomb. This will allow us to take a piece of cardinal data (i.e. UUID) and see the end-to-end flow throughout the system.
-OpenTracing is overwhelmingly the goto standard for distributed tracing, and is supported by numerous backends, including Jaeger, Honeycomb, Zipkin and more. https://opentracing.io/specification/

Traces will begin on receipt of requests, either in GRPC (below) or rest-gateway if possible
-https://medium.com/@masroor.hasan/tracing-infrastructure-with-jaeger-on-kubernetes-6800132a677
-https://github.com/grpc-ecosystem/go-grpc-middleware/
-NATS can also be instrumented: https://github.com/nats-io/not.go


Spans
-Api-to-scheduler
-Scheduler-to-st2
-Etc - need to build a trace diagram on the whiteboard and copy it here.
-Links


**ALSO** - will definitely need to do better about reporting errors from Syringe. And as a side note, in production we should ensure these are surfacing to someone.

We didn't know about this issue https://github.com/nre-learning/nrelabs-curriculum/issues/274 because we don't have error reporting. Who knows how many folks this affects.

Also, Antidote-web should not have any timeouts. It should simply report what the API says. If Syringe's timeout triggers, it should throw an exception to our error handling solution while also marking the lesson as failed, so the web UI can show that to the user.

## Mini-Project Milestones

This mini-project is too large to take place within a single platform release. To facilitate ongoing stability,
and create logical steps to the end-goal, a set of milestones is listed below. These milestones should be done
in the order shown, and can be added to any platform release that is deemed suitable.

This document will be updated as these milestones are achieved, with links to the Pull Request(s) and/or
that satisfy them.

### MP1.-2 - Collapse syringed-mock

Need to:
- provide more control within Syringe - i.e. presentation paths should be provided as a "LivePresentation" or something like this
- Since the API is its own service at this point, we can get rid of syringed-mock and simply deploy only the API microservice with prepopulated DB data that doesn't change since there's no scheduler.

### MP1.-1 - Error Reporting?

Is there not an open standard?
If not we'll have to build a basic abstraction within Syringe and keep the vendor bits limited.

https://sentry.io/

### MP1.0 - Move to go modules

### MP1.1 - Move state to external database

### MP1.2 - Break out garbage collection

### MP1.3 - Break out TSDB

### MP1.5 - Separate API and Scheduler and implement message queue

### MP1.6 - Structured and centralized logging with Fluentd

### MP1.7 - Observability instrumentation

### MP1.8 - Syringe Security

No need to go overboard here because we are running behind cloudflare in prod, but some common sense
measures should be taken, especially since not everyone will be running behind cloudflare.

Specifically things like rate limiting, source IP stuff, etc etc.

## Misc Things

These need to be done eventually, but could probably be addressed in separate projects.
This may be removed from this document in the future.

- **Auth and RBAC** You want to be able to do stuff like allow contributions
from randos that are in an "unsupported realm"
- **Lesson Networking** - The existing model works well but has shortcomings.
In this project we should evaluate NetworkServiceMesh and figure out if it suits our needs.
