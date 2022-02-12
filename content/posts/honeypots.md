---
title: "Bringing D&R to New Heights with Honeypots"
date: 2022-02-11T17:35:45-08:00
draft: false
---
_Why honeypots should be included at the core of a D&R program_

# Traditional Detection Eng.
There are plenty of resources out there such as the MITRE ATT&CK Matrix which describe common attack behaviors. Any Detection and Response (D&R) engineer could decribe in great detail multiple ways an attacker could move across their environment. They could even tell you what the attacker would likely target! 

So why is detecting malicious activity such a difficult task? The problem is that there are *so* many ways to move laterally, escalate privileges, and exfiltrate data. On top of that, attackers leverage tools that are the same tools most engineers use to do their job such as `curl`, `ssh`, `nmap`, and `powershell`.  **The only difference between attacker behavior and benign behavior is intent  --  and intent is incredibly hard to detect.**

```
$ curl http://169.254.169.254/latest/meta-data/
```
{{< center >}}
    <i>is this an attacker stealing AWS creds or an engineer debugging?</i>
{{< /center >}}

# Policy-based Detections
Something I've noticed recently though -- the best detections do not detect intent, but detect **policy violations**. I define a policy-based detection to be a detection which is triggered when an event that should never happens, happens. 

For example:
* If your org forbids the use of the `root` AWS account, then write a detection that triggers when the `root` account is used. You've just built a fantastic alert that will generate high signal for *compromised credentials!*
* If your org forbids the use of `powershell` on CX agent machines, then write a detection that triggers when `powershell` is used on an CX agent machine. It is a great detection for *execution* attempts! 

Policy-based detections are great because they are super easy to write and deploy. More importantly though, **policy-based detections leverage one of the greatest advantages most D&R teams don't even realize they have  --  knowledge and control over the environment they are hired to protect.** 

Attackers entering the environment for the first time have no idea where they are or what the environment looks like. Policy-based detections use that advantage to cause the attacker to reveal themselves.

# Intrusion Detection Honeypots
The issue with policy-based detections is that there needs to be a policy that can be violated. This limitation restricts D&R teams from really taking advantage of policy-based detections across the org, especially in very open environments such as a typical development env. 

An Intrusion Detection Honeypot (IDH) is a resource created for the purpose of triggering an alert when they are probed, used, or compromised. **IDH's has the same benefits as policy-based detections, but it allows D&R teams to ***create*** policies which can be scattered across your environment.**

For example:
* *Honey servers* are servers that can be placed in your environment that should never be probed. 
  * Honey servers could detect *discovery*.
* *Honey tokens* are tokens such as API keys and AWS credentials scattered across your environment which should never be used. 
  * Honey tokens could detect *lateral movement*  
* *Honey Resources* are resources such as database tables, S3 buckets, Word documents, and PDFs which should never be read or downloaded. 
  * Honey resources could detect *collection*

**Instead of squeezing signal out of a lot of noise, Intrusion Detection Honeypots enable D&R teams to create their own signal!**

# Deception
I've made my case as to why Intrusion Detection Honeypots can aid immensly in detecting attacks, but there is one more benefit I'd like to bring up and that is the addition of **deception**. A well designed IDH system can not only detect an attacker in your environment, but can actively lure the attackers *away* from the resources you are trying to protect (ex. customer data, production servers, etc.) and into a safe, controlled environment.

For example: 

Let's say an attacker lands onto a server in your environment. There are 3 paths they realize they could take: IAM credentials, SSH keys, and an RDP windows server. Little does the attacker know, only the SSH keys are legitimate and lead to their target; the IAM creds and windows servers are actually honeypots!

![IDH Example](/blog/idh_example.png#c)
{{< center >}}
    <i>Sample honeypot system</i>
{{< /center >}}

If the attacker finds either the honey IAM creds or honey Windows server to be attractive enough to presue, the IDH effectively lures them away from the actual attack path and into a rabbit hole that leads them to an S3 bucket with a whole lot of valueless data. In addition, the Incident Response (IR) team is getting notified on every move the attacker makes on the honeypots. **Even if it only takes the attacker 10 minutes to realize they were deceived, that is an extra 10 minutes the IR gets back to contain the threat!**

# Detection & Response  --  & Deception
Intrusion Detection Honeypots are extremely high signal, easy to deploy, and go above the D&R call of duty by not only detecting attacks but actively deceiving the attacker away from their target. I envision a future where honeypots are included at the core of a D&R program and deception becomes an additional feature that the team uses to increase their odds in stopping attacks and providing value to the org and security team.

We can be called D&R&D *(ok, I'm half kidding.)*

