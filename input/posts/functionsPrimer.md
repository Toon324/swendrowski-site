Published: 10/25/2017
Title: A Primer to Serverless Computing
PromoImageUrl: https://msdnshared.blob.core.windows.net/media/2016/09/fsharp-functions2.png
Category: csharp
Intro: Serverless Computing - The next generation of Cloud
---

Let's clear up one thing right away: "Serverless" computing still runs on some server, somewhere. Where that server is, how it is hosted, how often it is running, when it is updated - all of these things don't matter to you, the Developer. You write code, your platform of choice deals with the rest.

To understand why this is so exciting, let's take a brief walk through history and look at where we came from.

## Hardware Hosting

![](https://stackify.com/wp-content/uploads/2017/05/what-is-function-as-a-service-serverless-architectures-are-here-11196.png)

If you look to your left at the "History of Hosting" exhibit, you may fondly remember the days before the Cloud when everything was hosted in a server somewhere in your building, preferably in a cold isolated room. Companies bought physical devices, set them up with licenses, kept them updated - all so appliations, APIs, and websites could be hosted and made available to the end-user. Plenty of companies still do this for good reasons: once the server is setup, your costs are only for maintenance, updates, and electricity. In-building latency is practically non-existant, and you can keep applications on your in-company network for security.

For plenty of other companies however, this was a lot of work and money just to have a working server. Server rooms are expensive and require you to setup security protocols to ensure no one tampers with your machines and data, and plan for what happens when power goes out or the building floods. If you had users accessing your app from across the world, latency became an issue. Infrastructure as a Service (Iaas) came along to deal with this and was the first big generation of "Cloud". Need a Linux server? Spin up a VM! Windows Server? Just as easy! Want servers across the world? Done! Updates still had to be managed yourself, but all of a sudden Licenses and server rooms and availability concerns all started to go away - just pick your OS and what size you want your VM to be. You can even resize them on-the-fly with minimum downtime! For teams who want full control over everything on their server, VM's are still the go-to-choice for Cloud hosting.

Keeping up with updates and security patches is hard though, and setting up VM's and their access rules requires IT knowledge (and usually an IT team). Developers want to develop, so along came generation 2 of the Cloud - Platform as a Service (PaaS). In Azure, this generation includes offerings such as App Service and Worker Roles. For the common developer, PaaS offered a no-frills no-knowledge-needed way to host code - just write your app and publish, and there it was. OS and Framework updates are handled automatically, configuration of what versions you use are easy to set in a GUI, and you usually get a free DNS to boot. All the developer has to know is how to write code, and what size of server they want their code to run on.

This last question is surprisingly hard - "what size of server DO I need to host my app?" Things like [Burst VM's](https://azure.microsoft.com/en-us/blog/introducing-b-series-our-new-burstable-vm-size/) and the ability to "spin down" an App Service when not in use help to cut down on costs when our App isn't in use, but don't negate it completely. Generation 3 of the Cloud offers a solution to this with "Serverless" hosting, where you only pay for the resources your app consumes. To understand why this is so important, let's look at how Software has been evolving as well.

## Software Archetecture

![](http://usblogs.pwc.com/emerging-technology/wp-content/uploads/2017/02/feature02-figure01.jpg)

Over to your right is the "Hall of Software" exhibit, with our first dispaly - the monolithic app. Some of you may not-so-fondly recall these days where the term "spaghetti code" was born. Everything in the system was joined at weird angles with too-strong glue. Changes in the User registration system could cause errors in the product display pages. Deployments deployed everything at once, and if you were fortunate enough to have a Version Control System (VCS), merge conflicts were abundant. The hosting needs of these apps were simple however - one server, large enough to process everything at once at peak hours. Although the full resources of the server may not have been always utilized, with all of the various pieces running on one system you could usually gurantee a healthly usage of your server resources.

Most modern software projects have fortunately moved past this inital era and adopted a principle of "separation of concerns". Although the application is still hosted in the same fashion, separating the User registration system from the Product display page except for small edge interfaces allow multiple teams to work on and own the pieces of the application. Deployments still deploy the entire application at once, but the separation allows a VCS to cleanly keep unfinished work from being deployed as well. Server resource needs for average-use are able to go down due to better optimized code, but "peak hours" usage still requires servers to have large amounts of resources that remain otherwise unused.

In a persuit of ever-smaller pieces, Microservices came along with the promise of "separate by default". Rather than having your User registration and Product display page exist in the same application, these pieces of functionality now live in distinct applications that talk to the same databases. If your pieces need to talk to each other, various "edges" can be added to enable communication such as HTTP APIs, Message Queues, and Service Busses. Deployment of each piece is now responsible only for deploying itself, allowing for easy updates, A/B testing, and even having multiple instances of your application to deal with high-demand. Hosting becomes more complicated with this model, however - each application needs its own Server (or Docker container, but that's a topic for another post). Your User registration service might need half of the processing power of your Product display page service, but tracking and altering the size of your many servers quickly becomes a time-consuming task - back to needing an IT team!

## How Serverless solves the problem

Serverless Computing can be thought of as "easy microservices". There are [several](https://www.martinfowler.com/articles/serverless.html) [good](https://msdn.microsoft.com/en-us/magazine/mt793269.aspx) [articles](https://docs.microsoft.com/en-us/azure/azure-functions/functions-overview) on how they work, but the short of it is that providers such as Azure have large VM's that are always-on and spin up an instance of your code to execute everytime it is called. Your Batch Delete app is called 100 thousand times but only every Friday? You only pay for those 100k calls, and the app will auto-scale up to meet the demand on the fly.

Pricing is a big factor - in [Azure](https://azure.microsoft.com/en-us/pricing/details/functions/), pricing is based on 2 metrics: Total # of executions per month, and resources consumed as measured in "GB-s".

GB-s is a measure of "how much RAM did your program run for how many seconds". 1 GB-s is equal to 1 GB of RAM use for 1 second (or .5 GB of RAM use for 2 seconds, and so on). Memory is allocated in 128 MB chunks and execution time is measured in the milliseconds. Even excluding the free grant companies like Azure are currently given, the pricing is extremely cheap - 20 cents per million executions, and 0.0016 cents per GB-S.

To compare, the cheapest basic App Service (PaaS) instance has 1.75 GB of RAM and charges $0.075/hour.

If the same Batch Delete app ran on an Always-On Instance, you would have a flat weekly usage of 168 hours.
If you allowed your app to sleep between uses (slower start-up time), assuming your 100k delete calls took 5 seconds to execute, you would have a usage of 1 hour.
For serverless, we will assume .5 GB of memory is actually used.

The breakdown of prices is as follows:

| Hosting | Price per Week |
|--------|--------|
| Always-On       |   $12.60     |
| Sometimes-On | $0.075 |
| Serverless | 0.004 cents |

## What Serverless is good for - and what it isn't

#### Good
- Scheduled Batch Jobs
- Code that has low memory usage / call times / is infrequently called
- Inversely, is called a lot, but only in burst usage
- Code that reacts to events, such as Timers, HTTP Calls, and Queues
- Microservices
- Auto-Scaled APIs
- [Integrating with services such as Microsoft Flow ](https://docs.microsoft.com/en-us/azure/azure-functions/functions-flow-scenario)

#### Not so Good
- Games
- Stateful Applications
- Resource Demanding Applications
- CLI tools / Desktop / Mobile Apps

## In Closing

Serverless is not the end-all be-all. Rather than replace your entire app, there are plenty of opportunities to write small reusable microservices hosted serverless that offload some of the work from your standard applications. Users upload a new profile image? Resize it in a function. New a unique ID across all your systems? Function with an HTTP endpoint. Auth0 has perhaps my favorite usage of Serveless - [Auth0 Extend](https://auth0.com/extend/) lets users write custom rules that run on every login to help customize the login experience and scopes granted. Serverless is the latest iteration in microservices and hosting, and like all options, has a place where it belongs best.

Future blog posts here will explore how to write Azure Functions and where I am using them myself. Till next time!