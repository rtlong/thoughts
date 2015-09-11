# Mail Service on AWS

Building a typical email “server” stack relying mostly on AWS services rather than traditional mail server software packages on a linux server or system of servers.

Using AWS Lambda, which could perform the bulk of the pipelining operations, combined with AWS Simple API, SES, SQS, and one of the many data storage services, the majority of the system would exist as these intrinsic services. The only piece I see unable to be produced within the intrinsic services is non-HTTP protocol layers. Some minimal SMTP-to-HTTP and IMAP-to-HTTP translation gateway services would need to be run on a linux server, I think. Also, if doing contacts, calendar, and notes, WebDAV-to-S3 or WebDAV-to-HTTP, depending on the implementation needs of those services — i’m not sure at the moment. Full encryption could be applied at the gateway service layers, so nothing exists on the wire un-encrypted for any longer than necessary. 

### The benefits of this approach would be:

- cost — assuming a relatively low volume of incoming mail (maybe even with a larger volume) — only guaranteed costs are the small linux server (t2.micro would probably suffice, so we’re talking around $7/month), with additional (low) costs based on volume
- speed — if a high volume comes in as a surge, there would still be a bottleneck at the gateway service (good reason to make that as minimal and concurrent as possible [use Go]); after that, where there may be heavier filtering/processing down the pipeline, Lambda and SQS make for infinite horizontal scalability.
- reliability — again, excepting the gateway, which could be made redundant and load balanced to — all components of the system are run by AWS, so there is very little room for it to break due to network or hardware failure. — that said, see the ‘reliability’ point in the downsides below.

### Downsides of this approach:

- reliability — this idea would require writing a custom mail service, so there would be a ton of alpha software in use which is almost certain to cause outages regardless how solid the hardware/network infrastructure is — though, of course, this would improve over time
- lock-in — you’re totally beholden to AWS
