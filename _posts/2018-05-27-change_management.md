---
title: "Practicing Quality Change Management in ServiceNow "

---

Change management is arguably one of the most important processes in ITSM.  One mistake in the process can cost a company millions of dollars.  It’s straight forward in theory, but can have major organizational consequences if it’s not done correctly.  Over the years, I’ve seen a variety of companies who have made vital mistakes in their process.  As organizations look to implement and sustain their change process, I wanted to share some of my observations to ensure success.

**1. Configure vs. Customize**

It’s important to define what I mean by configure vs. customize.  For example, in your change management process, configuring would be setting system properties for the change_request table to true/false.  Customizing would be scripting a business rule that references a 1,000-line script include.  For the most part, avoid customizing at all costs.  Once you start adding dozens of scripted business rules, parts of your process start to contradict yourself.  This can break existing processes, make it difficult for your developers to figure out what is causing defects, and is the gateway to a disastrous change process.  To say the least, things get out of control quickly with customization. 

**2. Business Rules and Conflicting Workflows**

This one is a recipe for disaster and goes against best practices on the platform in multiple aspects.  For example, let’s say you have a workflow that is setting the state on a change request, but a business rule that is preventing a state change from happening and aborting the database action.

Ex. :

-	Change request moves stage to “Implement” through the workflow

- At the same time, a business rule with a script runs which says, “IF stage moves to implement AND all approvals have not been set to “approved”, THEN abort the database action.  

I cant tell you the amount of times I’ve seen these lines cause issues between workflows and business rules: 

```javascript
setAbortAction(true);
current.state = previous.state;
```

**4. Required Fields**
Developers and engineers don’t like documenting changes, especially when they have to fill out thirty required fields on a form.  Before you go making every field on the change form required, ask yourself:

- Is this data absolutely required for compliance?
- At what state in the change process should this data become required?
- How can we minimize the amount of data required to make it easiest for our ITIL users?

A feature which I like to add to all change processes is a “Save as Draft” UI action on the change request form.  The use case for this being, when requestors start documenting the change, they often don’t always have the initial data required, but still want to save their change for later editing.

**5. Over Complication**

If your change process spans multiple pages with dozens of workflow activities and paths, your change process is likely over complicated.  Over the course of time, it’s easy to let this process grow exponentially.  Don’t let this happen.  It’s important to scrutinize every single enhancement request to modify this workflow.  In fact, I would only recommend modifying change workflows very rarely once you have in a steady place.

The purpose of the “show workflow” UI action is to allow your end users to have a visual and comprehendible view of where their change is in the process.  When this starts to look like an intricate engineering diagram, people immediately get frustrated with ServiceNow.

**6. Data Policies**

Data policies are excellent for maintaining data integrity.  Unlike client-side UI policies which can be bypassed by certain user action, data policies run server side.  Data policies enforce required fields even in list views, which will ensure you have quality data for reporting and compliance purposes.  Always use data policies for change management instead of UI policies

**7. Notifications**

When it comes to change management notifications, less is more.  I personally think the OOB notifications provide more than enough detail to end users.  Each additional notification you add on increases the likelihood that change requestors will set up an email filter for these notifications.

A nice feature I like to add when implementing change management is having these emails send from a unique email address and not using the default system mailbox.  Setting the “From” address to your organizational change process email address ensures that previously created end user filters are not honored.

**8. CMDB**

Quality data in your CMDB is important for all aspects of ITSM.  However, it is an absolutely vital piece of ensuring your organization is practicing effective change management.  Although there is an extensive list of best practices for maintaining quality data in your CMDB, some things to look out for as it relates to change management are:

-	Eliminating duplicate CI’s
-	Ensuring correct CI relationships
-	Putting rules in place to prevent conflicting changes taking place on related CI’s

**9. UI Actions**

The default UI in ServiceNow can be a lot to look at.  A clunky form view in combination with the application navigator is a lot for an end user to visually consume.  When possible, always try and add net new UI actions as related links.  And if you have to add new form buttons, always try and avoid showing more than three at a time on the header and keep the verbiage on the button at one to two words.

**10. Stuck Workflows**

Every developer or change process owner has probably heard “my change request is stuck” at one point or another.  Often times, the change may not actually be stuck, but rather, a confusing process has been put in place.  To avoid this situation, always follow the rules below:

-	When using a “Wait For” workflow activity, let end users know what they need to do to make a change request progress forward.  A few ways to do this are through a form information message or client-side pop up.  If you don’t let users know why their change is stuck in a state, they are likely to put in an incident.  This leads to wasted time by not only end users, but production support 	and development teams as well.
-	Like I mentioned before earlier in this post, never use conflicting business rules with workflows.  This is the biggest culprit for stuck workflows. Always use workflows to drive state progression in change requests.

