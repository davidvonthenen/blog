---
post_title: 'Why Owning Your AI Model Infrastructure Is Important'
layout: post
published: true
author: david
tags:
    - AI
    - Artificial Intelligence
    - Conferences
    - Machine Learning
    - ML
categories:
    - AI/ML
    - Conferences
---
I attended a session at [RenderATL](https://www.renderatl.com) this week titled "From Demo to Production: The Infrastructure Gap (and Why You Must Own the Stack)" by [Nolan Code](https://www.linkedin.com/in/nolan-s-code-mba/). Nolan is from the [Atlanta AI & Robotics Initiative](https://www.linkedin.com/company/atlanta-ai-robotics-initiative/)... I honestly didn't know anything about the organization since I am not from the area, but they seem to be doing a lot of amazing things! The session was interesting and focused primarily on Physical AI... Think robotics, like self-driving cars and automation (as in industrial or factory). He said Physical AI will be among one of the fastest-growing segments of AI along with Agentic AI; I completely agree with his assessment and have been saying this for some time.

!["From Demo to Production: The Infrastructure Gap (and Why You Must Own the Stack)" by Nolan Code](https://davidvonthenen.com/wp-content/uploads/2026/08/infra-gap-session-scaled.png)

Along with Physical AI comes a need to own your own infrastructure. In the self-driving car sense... You obviously need cars to build autonomous self-driving technology, but the session's deeper message is that you need to own the entire AI infrastructure stack. (hardware and software). His organization uses GPU acceleration, such as the NVIDIA Jetson, for inference on these models, but notes that they still use the big 3 hyperscalers for training. 

Nolan stressed the importance of owning the inference stack for many reasons, but privacy and inference costs were the primary motivators, with cost being the heavier of the two. The cost of renting GPUs at significantly higher prices than traditional compute is definitely a buzzkill. He backed up his hypothesis by pointing to an article about 20 regional AI leaders, all of whom own their infrastructure stack in their own data centers, as supporting evidence. 

![Factory Automation](https://davidvonthenen.com/wp-content/uploads/2026/08/factory-automation-robots.png)

This got me thinking. While I agree with this idea, in this particular situation... the bigger problem might be privacy, not cost. Cost, you say? From me? The guy who has been yelling about the inevitable price hike looming in AI. Heck, I am even in the process of writing a book on Small Language Models, with the intention of getting AI practitioners to think about building AI solutions with privacy and cost in mind. Why is this situation different? Let me explain...

All 20 of those companies cited are inherently successful. While the cost of renting GPUs will continue, the money at this point isn't a big deal to these successful companies. For those in the multi-hundreds of millions or even in the big "B" category for revenue, this is especially true. Others in their financial situation (by definition) have a successful and, more importantly, financially sustaining business where they don't need to worry about things like GPUs depreciating. But I wholeheartedly agree that, at their level, you need to own that inference stack, but for privacy reasons.

![Man-in-the-middle Attack](https://davidvonthenen.com/wp-content/uploads/2026/08/mitm-logo.png)

The systems that run these AI workloads, LLMs or traditional ML models, require GPUs or some AI acceleration. Typically, if those models are core to their business, they have dedicated infrastructure to run these models. Their solutions access those models sitting on those GPUs via some API... REST, MCP, whatever. That boundary is the source of your access and your problem. What stops those providers from recording the input and output over time and training their own version of your model? If you are using a cloud provider, they can record large amounts of input data and output, aka the answer... and then use that data to train a model to do the same thing. The simplest example of this is a classifier. If you aren't of consequence (aka a company that doesn't make a lot of money), who cares. HOWEVER, if you are, what's to stop them from monitoring the network for that data and taking your IP?

That's some tinfoil hat stuff, but with all the "urgency" from these AI providers to be better than their peers and turn a profit on their efforts... Why not do this? Why not do it? I mean, these companies have been scraping websites, violating content creators' terms of service, and on and on. If you have proprietary data and have built a machine learning model that is core to your business and IP, bringing in billions, those providers might want a piece of it. Don't think that would happen?

![Amazon Basics and 3rd Party Sellers](https://davidvonthenen.com/wp-content/uploads/2026/08/amazon-third-party-data.png)

We know Amazon already uses its retail business to monitor which products do well, and for those that do, it goes to a Chinese dropship company to source those products. This is EXACTLY their operating model for Amazon Basics. This has been happening for some time now. Check out [this article](https://www.reuters.com/article/amp/idUSKBN23J2Z9/), or [this one](https://www.businessinsider.com/amazon-copied-third-party-sellers-competitors-india-reuters-report-2021-10), or watch [this video](https://www.youtube.com/watch?v=l2p3fZyV254). Amazon is basically doing this with cables and backpacks... the small shit. Why not for the big shit?

![Google Lawsuit with Incognito Mode on Chrome](https://davidvonthenen.com/wp-content/uploads/2026/08/google-incognito-lawsuit.png)

Google doesn't let you opt out of using your personal information and data (think Gmail, Docs, etc.) unless you turn off all history and memory features. Even then, I would bet that they still do it, but use the legal loophole of anonymizing YOU while still using all the intelligence from the behaviors and patterns. Google effectively did this already with Chrome and incognito mode. Check out [this article](https://www.theguardian.com/technology/2024/apr/01/google-destroying-browsing-data-privacy-lawsuit) describing the payout from the lawsuit or [this video](https://www.youtube.com/watch?v=i9db1l9BcUk) if you aren't familiar with what happened.

Why not do this in AI/ML in the middle of this AI arms race?

If you own the hardware infrastructure and stack in a datacenter, you don't have this problem. For companies bringing in billions, this is the real threat. For you and me, who are using AI to solve coding problems... maybe they get to see your billion-dollar idea take off from the greenfield and help them create ideas. Until it becomes the money maker, your biggest concern is cost... A completely different problem based on a completely different audience.

While my reasoning might be very different from Nolan, he is absolutely freaking right.

What do you think?
