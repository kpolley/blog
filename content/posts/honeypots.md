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

Policy-based detections are great because they are super easy to define and deploy. More importantly though, **policy-based detections leverage one of the greatest advantages most D&R teams don't even realize they have  --  ***knowledge*** over the environment they are protecting.** Attackers entering the environment have no idea what it looks like. 

Policy-based detections are great, but limited. Such detections can only be used if there is a defined policy which can be violated. In addition, the policy needs to be *strong* or else you end up with false positives. This limitation restricts D&R teams from really taking advantage of policy-based detections across the org, especially in very open environments such as a typical development env. 

# Intrusion Detection Honeypots
An Intrusion Detection Honeypot (IDH) is a resource created for the purpose of triggering an alert when they are probed, used, or compromised. IDH's leverage the same advantage as policy-based detections, but do not share the same limitations. **Instead of struggling to extract signal out of a lot of noise, D&R teams could instead deploy IDH's to ***create*** their own signal!**

For example:
* *Honey servers* are servers that can be placed in your environment that should never be probed. 
  * Honey servers could detect *discovery*.
* *Honey tokens* are tokens such as API keys and AWS credentials scattered across your environment which should never be used. 
  * Honey tokens could detect *lateral movement*  
* *Honey resources* are resources such as database tables, S3 buckets, Word documents, and PDFs which should never be read or downloaded. 
  * Honey resources could detect *collection*

IDHs can be super simple and provide impact on it's own or they can interact with each other to create a networked system of digital trip-wires and rabbit holes. Ex.  *honey tokens* authenticate into a *honey user* which has access to *honey data* etc.

# Deception
This brings me to a typically forgotten concept to D&R: the addition of deception. **A well designed IDH system can not only detect an attacker in your environment, but can actively lure the attackers ***away*** from the resources you are trying to protect** (ex. customer data, company secrets, etc.) and into a safe, controlled environment. 

For example: 

Let's say an attacker lands onto a server in your environment. There are 3 paths they realize they could take: IAM credentials, SSH keys, and an RDP windows server. Little does the attacker know, only the SSH keys are legitimate and lead to their target; the IAM creds and windows servers are actually honeypots!

![IDH Example](/idh_example.png#c)
{{< center >}}
    <i>Sample honeypot system</i>
{{< /center >}}

If the attacker finds either the honey IAM creds or honey Windows server to be attractive enough to pursue, the IDH effectively lures them away from the actual attack path and into a rabbit hole full of frustration and valueless data. In addition, the Incident Response (IR) team gets high-signal notifications describing every move the attacker makes on the honeypots. **Even if it takes the attacker only 10 minutes to realize they were deceived, that is an extra 10 minutes the IR gets back to contain the threat!** 

# Detection & Response  --  & Deception
Intrusion Detection Honeypots are extremely high signal and low noise. They are easy to deploy and can be added to any environment. IDH's go beyond a typical D&R's call of duty by not only detecting attacks but also functions as a deceptive system which lures attackers away from their target. The Incident Response team will get a whole lot of extra time in a scenario where every minute is precious. 

I can't help but envision a strategy where Intrusion Detection Honeypots are the central focus and output of a successful D&R program. Instead of constantly wrangling and tuning detections in a SIEM, D&R engineers instead build a network of IDH's which detect, alert, and deceive malicious actors. 

**We can be called D&R&D** *(ok, I'm half kidding.)*

## Additional Reading
*[Intrusion Detection Honeypots: Detection Through Deception](https://www.amazon.com/Intrusion-Detection-Honeypots-through-Deception/dp/1735188301)* by [Chris Sanders](https://twitter.com/chrissanders88)