---
title: "Advancing Threat Detection & Response with Honeypots"
date: 2022-02-11T17:35:45-08:00
draft: false
---
_Why honeypots should be a fundamental component of a D&R program._
<!--more-->
# Traditional Detection Eng.
There are plenty of resources which describe common attack behaviors. Any Detection and Response (D&R) engineer could describe in great detail multiple ways an attacker could move across their environment. They could even tell you what the attacker would likely target! 

So why is detecting malicious activity such a difficult task? The problem is that there are *so* many ways to move laterally, escalate privileges, and exfiltrate data. On top of that, attackers leverage the same tools most engineers use to do their job such as `curl`, `ssh`, `nmap`, and `powershell`.  **The only difference between attacker behavior and benign behavior is intent  --  and intent is incredibly hard to detect.**

```
$ curl http://169.254.169.254/latest/meta-data/iam/security-credentials
```
{{< center >}}
    <i>is this an attacker stealing AWS creds or an engineer debugging?</i>
{{< /center >}}

# Policy-based Detections
Something I've noticed recently though -- the best detections do not detect intent, but detect **policy violations**. I define a policy-based detection to be a detection which is triggered when an event that should never happens, happens. 

For example:
* If your org forbids the use of the `root` AWS account, then write a detection that triggers when the `root` account is used. You've just built a fantastic alert that will generate high signal for *compromised credentials!*
* If your org forbids the use of `powershell` on CX agent machines, then write a detection that triggers when `powershell` is used on an CX agent machine. It is a great detection for *execution* attempts! 

Policy-based detections are great because they are super easy to write and deploy. More importantly though, **policy-based detections leverage one of the greatest advantages most D&R teams don't even realize they have  --  knowledge and control over the environment they are hired to protect.** 

Attackers entering the environment for the first time have no idea where they are or what the environment looks like. Policy-based detections leverage that fact to cause the attacker to reveal themselves.

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
I've made my case as to why Intrusion Detection Honeypots can aid immensely in detecting attacks, but there is one more benefit I'd like to bring up and that is the addition of **deception**. A well designed IDH system can not only detect an attacker in your environment, but can actively lure the attackers *away* from the resources you are trying to protect (ex. customer data, production servers, etc.) and into a safe, controlled environment.

For example: 

Let's say an attacker lands onto a server in your environment. There are 3 paths they realize they could take: IAM credentials, SSH keys, and an RDP windows server. Little does the attacker know, only the SSH keys are legitimate and lead to their target; the IAM creds and windows servers are actually honeypots!

![IDH Example](/idh_example.png#c)
{{< center >}}
    <i>Sample honeypot system</i>
{{< /center >}}

If the attacker finds either the honey IAM creds or honey Windows server to be attractive enough to pursue, the IDH effectively lures them away from the actual attack path and into a rabbit hole full of frustration and valueless data. In addition, the Incident Response (IR) team is getting notified on every move the attacker makes on the honeypots. **Even if it takes the attacker only 10 minutes to realize they were deceived, that is an extra 10 minutes the IR gets back to contain the threat!**

# Detection & Response  --  & Deception
Intrusion Detection Honeypots are extremely high signal and easy to deploy. In addition, IDH's go beyond a typical D&R's call of duty by not only detecting attackers but actively deceiving the attacker. This additional layer of deception makes it more difficult for the attacker to navigate the environment, lures the attacker away from their target, and gives Incident Response team members extra time in a scenario where every minute counts. 

I envision a future where honeypots are included at the core of a D&R program and deception becomes an additional feature that the team uses to increase their odds in stopping attacks and providing value to the org and security team.

**We can be called D&R&D** *(ok, I'm half kidding.)*

## Additional Reading
*[Intrusion Detection Honeypots: Detection Through Deception](https://www.amazon.com/Intrusion-Detection-Honeypots-through-Deception/dp/1735188301)* by [Chris Sanders](https://twitter.com/chrissanders88)