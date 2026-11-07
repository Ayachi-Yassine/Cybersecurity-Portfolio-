# Unified Kill Chain

The Unified Kill Chain is a framework which establishes the phases of an attack, and a means of identifying and mitigating risk to IT assets.

## Threat Modelling

Series of steps to improve the security of a system , it is about identifying RISK and essentially boids down to:

1. Identifying what systems and applications need to be secured and what function they serve in the environment. For example, is the system critical to normal operations, and is a system holding sensitive information like payment info or addresses?
2. Assessing what vulnerabilities and weaknesses these systems and applications may have and how they could be potentially exploited
3. Creating a plan of action to secure these systems and applications from the vulnerabilities highlighted
4. Putting in policies to prevent these vulnerabilities from occurring again where possible (for example, implementing a software development life cycle () for an application or training employees on  awareness).
    
    SDLC
    
    phishing
    

piece of hardware or software ⇒ asset

![708e85cf63230b21bacee32bfbd6d311.png](708e85cf63230b21bacee32bfbd6d311.png)

## Foothold

The main focus of this series of phases is for an attacker to gain access to a system or networked environment. An attacker will employ numerous tactics to investigate the system for potential vulnerabilities that can be exploited to gain a foothold in the system. For example, a common tactic is the use of reconnaissance against a system to discover potential attack vectors (such as applications and services)

![9f902ef203a9aacb47ee847d7b3051d6.png](9f902ef203a9aacb47ee847d7b3051d6.png)

## Through : Network Propagation

The "**Through**" goal follows a successful foothold being established on the target network. If defensive controls prevent the attacker from achieving their objective, they will seek to gain additional access and privileges to systems. The attacker would set up a base on one of the systems to act as their pivot point and use it to gather information about the internal network. The diagram below represents the individual attack phases of the "**Through**":

![5adc12d43ac2b82857b4c5b78f0c2579.png](5adc12d43ac2b82857b4c5b78f0c2579.png)

## OUT : Action on the Objectives

The "**Out**" goal wraps up the journey of an adversary’s attack on an environment, where they have critical asset access and can fulfil their attack goals. These goals are usually geared toward compromising the confidentiality, integrity and availability (CIA) triad. The tactics to be deployed by an attacker would include:

![696514d6007992ac83e1b46b-1768836661994.jpg](696514d6007992ac83e1b46b-1768836661994.jpg)