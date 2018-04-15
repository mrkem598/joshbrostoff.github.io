---
title: "Knowledge Base Content Curation"

---

One of the most difficult challenges with ServiceNow is content curation for the knowledge base.  With thousands of articles to maintain and often only one person reviewing articles for accuracy, articles can become out of date quickly.

It’s great that authors are willing to contribute to help increase incident deflection and drive a culture of self service.  However, when authors don’t update their own articles, it can hurt the organization more than it helps.

To keep articles up to date and hold knowledge authors accountable, I have automated the process of reminding authors to update their own content.  As a result of this, every 6 months, knowledge authors will receive an email with all the articles they are listed as the author of, reminding them to check their articles for accuracy.

Here is how to implement this:

**Scheduled Script Execution**
1. Create a new scheduled script execution called "Knowledge Review Cycle"
2. Set a run interval for which you want these email reminders to send to authors
3. Paste the following code in the "Run this script":

```javascript
var au = new ArrayUtil();
var list = [];
var kb = new GlideRecord('kb_knowledge');
kb.addQuery('active', true);
kb.query();
while (kb.next()) {
         list.push(kb.getValue('author'));
}
var shortList = au.unique(list);
for (var i = 0; i < shortList.length; i++) {
         gs.eventQueue('knowledge.review.cycle', null, shortList[i], ''); // trigger the event
}
```

**Event**
Create a new event called "knowledge.review.cycle" on the kb_knowledge table

**Email Notification**
1. Create a new email notification on the kb_knowledge table
2. Set the "Send when" to "Event is fired", and select the knowledge_review_cycle event that you previously created.
3. On the "Who will receive tab", check the "Event parm 1 contains recipient"
4. Set the "Message HTML" to a mail script called "${mail_script:knowledgereview}".

**Email Script**
1. Create an email script called "knowledgereview" that will loop through the knowledge articles by author.
2. Check the "Newlines to HTML" box

```javascript
(function runMailScript(/* GlideRecord */ current, /* TemplatePrinter */ template,
/* Optional EmailOutbound */ email, /* Optional GlideRecord */ email_action,
/* Optional GlideRecord */ event) {
	
	var vArticle = new GlideRecord('kb_knowledge');
	vArticle.addQuery('author', event.parm1);
	vArticle.addQuery('workflow_state', published);
	vArticle.query();
	while (vArticle.next()) {
		template.print(" - <a href='" + gs.getProperty("glide.servlet.uri") + vArticle.getLink() + "'>" + vArticle.number + "</a> - " + vArticle.short_description + "\n");
		
	}
})(current, template, email, email_action, event);
```
