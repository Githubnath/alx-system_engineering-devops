# Postmortem

## Nath Microsystems API infrastructure outage incident report

May 12, 2023

Nath Microsystems is a tech firm; which offers a wide range of affordable web design and marketing services. Nath Microsystems design, develop software applications and deploy websites. It has more than 1000 subscribers that always visit its site for support.

The following is the incident report for Nath Microsystems API infrastructure outage that occurred on March 24, 2023.


## Issue Summary

From 7:35 PM to 8:58 PM WAT, requests to most Nath Microsystems APIs resulted in 500 error response messages. Our clients applications that rely on these APIs also returned errors or had reduced functionality. At its peak, the issue affected 100% of traffic to this API infrastructure. Users could continue to access certain APIs that run on separate infrastructures. The root cause of this outage was an invalid configuration change that exposed a bug in a widely used internal library.

## Timeline (all times  West Africa Time (WAT))

7:19 PM: Configuration push begins
7:35 PM: Outage begins
7:35 PM: Pagers alerted teams
7:54 PM: Failed configuration change rollback
8:15 PM: Successful configuration change rollback
8:19 PM: Server restarts begin
8:58 PM: 100% of traffic back online

## Root Cause

At 7:19 PM WAT, a configuration change was inadvertently released to our production environment without first being released to the testing environment. The change specified an invalid address for the authentication servers in production. This exposed a bug in the authentication libraries which caused them to block permanently while attempting to resolve the invalid address to physical services. In addition, the internal monitoring systems permanently blocked this call to the authentication library. The combination of the bug and configuration error quickly caused all of the serving threads to be consumed. Traffic was permanently queued waiting for a serving thread to become available. The servers began repeatedly hanging and restarting as they attempted to recover and at 7:35 PM WAT, the service outage began.

## Resolution and recovery


At 7:35 PM WAT, the monitoring systems alerted our engineers who investigated and quickly escalated the issue. By 7:41 PM, the incident response team identified that the monitoring system was exacerbating the problem caused by this bug. 

At 7:54 PM, we attempted to rollback the problematic configuration change. This rollback failed due to complexity in the configuration system which caused our security checks to reject the rollback. These problems were addressed and we successfully rolled back at 8:15 PM.

Some jobs started to slowly recover, and we determined that the overall recovery would be faster by a restart of all of the API infrastructure servers. To help with the recovery, we turned off some of our monitoring systems which were triggering the bug. As a result, we decided to restart servers gradually (at 8:19 PM), to avoid possible cascading failures from a wide scale restart. By 8:48 PM, 25% of traffic was restored and 100% of traffic was routed to the API infrastructure at 8:58 PM.

## Corrective and Preventative Measures

 It was conducted an internal review and analysis of the outage. The following were the actions we took to address the causes of the issue and to help prevent recurrence and improve response times:

●	Disable the current configuration release mechanism until safer measures are implemented. (Completed.)

●	Change the rollback process to be quicker and more robust.

●	Fix the underlying authentication libraries and monitoring to correctly timeout/interrupt on errors.

●	Programmatically enforce staged rollouts of all configuration changes.

●	Improve process for auditing all high-risk configuration options.

●	Add a faster rollback mechanism and improve the traffic ramp-up process, so any future problems of this type can be corrected quickly.

●	Develop better mechanisms for quickly delivering status notifications during incidents.




## Author: Emenike Nathaniel.
 




