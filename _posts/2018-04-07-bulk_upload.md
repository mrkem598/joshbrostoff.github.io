---
title: "Bulk Uploading Catalog Items"

---

Often times when new platform is rolled out or a new business unit is spun up, hundreds or even thousands of users within an organizaiton may require access to systems or hardware. Out of the box, ServiceNow offers a “Bulk Requests” feature, which allows a REQ to be copied for up <20 users.

However, this could take an individual several hours or more to manually submit all these requests.

As a solution to this, I have created a transform map script that uses the ServiceNow cart API to automate sumbmitting these requests for all users listed in a CSV, XLS, or XSLX file.  This allows for thousands of REQ’s to be submitted within a matter of seconds, saving valuable time and money for organizations.

Another advantage of this script are the safe checks in place to ensure that RITM’s will not be created for inactive users.

The steps to create a bulk upload can be seen below.

1.	Create a new data source and attach the CSV or XLSX file with the required columns to match the variables in the catalog item
2.	Create a new transform map for the data source
3.	Create an onBefore script where you will paste your code
4.	Identify the variables on the catalog item which you need to be imported from the CSV file
5.	Identify the sys_id of the catalog item
6.	Replace the variables with the correct names on the catalog item and input the sys_id of the catalog item where appropriate below
7.	Run the transform and within seconds your requests are created!

