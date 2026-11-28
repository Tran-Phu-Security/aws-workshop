---
title : "Event 1"
date : 2026-11-28
weight : 1
chapter : false
pre : " <b> 4.1. </b> "
---

# Summary Report: “Navigating the Future of Cloud & AI in Vietnam”

#### Event information

| Item | Details |
|---|---|
| Event name | Navigating the Future of Cloud & AI in Vietnam |
| Speaker | Dr. Werner Vogels, Chief Technology Officer of Amazon |
| Date | October 2, 2026 |
| Location | [ Bitexco Tower - Ho Chi Minh City] |
| Role | Attendee |

I attended this session to hear Dr. Werner Vogels talk about the future of software engineering. As a student who studies in class and also builds projects on my own, I often ask myself whether AI will replace programmers, so the topic felt very close to home. The whole hall kept taking notes, and each time the speaker mentioned AI the room grew noticeably quieter.

#### Key takeaways

- **The outage on a peak shopping day.** Mr. Vogels described Amazon's early years: during the holiday shopping season the relational database was overwhelmed, the system went down for a full day and the company suffered heavy losses. What impressed me most was how the team responded. The software vendor argued that Amazon had used the product far beyond its design limits, and he openly agreed they were right. The engineers did not patch it up quickly. They stepped back and measured how the system was really used. The result showed that most queries were simple key-value lookups that did not need a heavy relational database. That finding gave rise to Dynamo and, later, DynamoDB. I use DynamoDB for the job and result-cache tables in the Trust Check project, so this story helped me understand why the service exists.
- **Operational discipline.** According to Mr. Vogels, looking at averages is an easy way to fool yourself, because an average hides the poor experience of users at both extremes. He stressed the mindset that "everything fails at some point". Amazon runs drills on purpose, shutting down parts of its live infrastructure to see how well the system tolerates failure.
- **The “Renaissance Developer”.** This was my favorite part. Programmers will not be pushed out if they develop around five traits: curiosity, systems thinking, ownership, broad knowledge together with deep expertise, and communication skills. He compared AI to a natural-language compiler: very fast but able to make mistakes, so its output always needs a human to check it.

#### Photos from the event

![Photo from the event 1](/aws-workshop/images/4-EventParticipated/4.1-Event1/photo-1.jpg)

![Photo from the event 2](/aws-workshop/images/4-EventParticipated/4.1-Event1/photo-2.jpg)

#### Lessons learned and personal contribution

1. **AI writes the code, but the responsibility is mine.** I used to take AI-generated code, run a quick test and put it straight into my project once it worked. After this session I set myself a rule: never commit a line of code I cannot explain. Before accepting code from AI, I will look at edge cases, security vulnerabilities and performance bottlenecks. Anything I do not understand, I cannot claim as my own work.
2. **Look at the whole system instead of isolated features.** School assignments usually revolve around single modules. I will redraw the data flow of Trust Check from the interface, through API Gateway, SQS and Lambda, down to DynamoDB, and ask what happens if one component stops working. Major outages almost always start from assumptions that were never verified.
3. **Understand the real workload before choosing technology.** I tend to pick tools simply because they are popular or talked about. Amazon's story reminded me to start from how the system is actually used. From now on, before choosing a database or framework, I will list the kinds of queries and data processing the application really needs. The Trust Check cache table is looked up by a hash of the content, exactly the kind of key-value query Mr. Vogels described.
4. **Take communication skills seriously.** I used to treat communication as a side matter. Now I see how it helps engineers find the real problem behind vague requests and explain technical trade-offs to non-technical people. I will practice writing concise documentation for my part of the work and presenting more often in group projects.
5. **Be wary of “we have always done it this way”.** Mr. Vogels closed with Grace Hopper's well-known warning about the most dangerous phrase in the language. For me it is a reminder to review and refresh, from time to time, how I learn and write code.

I left the hall feeling more relieved than worried. What I took home was not an empty reassurance to "stop worrying about AI", but a clearer view of what AI still cannot replace: judgment, systems thinking, accountability and professional pride. Those are the skills I need to build starting now. My first task this week is to redraw the architecture diagram of my current project and review all the code I previously let AI write for me.