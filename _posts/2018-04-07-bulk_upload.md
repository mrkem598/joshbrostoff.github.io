---
title: "Bulk Uploading Requests"

---

Often times when new software is rolled out at an organization, hundreds or even thousands of users are required to submit access requests through ServiceNow for audit and compliance purposes.  This requirement often leads to stakeholders asking "Can I give you all the people that need access in a file and you can upload it into ServiceNow?"

Out of the box, ServiceNow offers a “Bulk Requests” feature, which allows a request to be copied for up to 20 users.  However, this could take an individual several hours or more to manually submit all these requests.

As a solution to this, I have created a transform map script that uses the ServiceNow cart API to automate REQ and RITM sumbmission for all users listed in a CSV, XLS, or XSLX file.  This allows for thousands of requests to be submitted within a matter of seconds, saving valuable time and money for organizations.

Another advantage of this script are the safe checks in place to ensure that RITM’s will not be created for inactive users.

The steps to create a bulk upload can be seen below.

1.	Create a new data source and attach the CSV, XLS or XLSX file with the required columns to match the variables in the catalog item
2.	Create a new transform map for the data source
3.	Create an onBefore script where you will paste your code (seen below)
4.	Identify the variables on the catalog item which you need to be imported from the CSV file and replace them in the code below as necessary.
    ```javascript
    //set the additional comments and date required variables from the data source
		var dataSourceComments = source.u_additional_comments;
		var dataSourceDate = source.u_date_required;
		
		cart.setVariable(item, 'comments', dataSourceComments);
		cart.setVariable(item, 'date_required', dataSourceDate);
    ```
5.	Identify the sys_id of the catalog item and replace it as seen below 
    ```javascript
    //Catalog Item sys_id which you want to bulk upload
		var item = cart.addItem('fa05539c6f9bde80c7fe90264b3ee409');
    ```
7.	Run the transform, and within seconds your Requests and Requested Items are created.


Here is the complete onBefore script:
```javascript
(function runTransformScript(source, map, log, target /*undefined onStart*/ ) {
	
	//set the opened by
	var openedBy = new GlideRecord("sys_user");
	openedBy.addQuery("user_name", source.u_opened_by_network_id);
	openedBy.query();
	if (openedBy.next()) {
		var getCurrentCart = cart.getCart();
		
	}
	
	var setOpenedBy = openedBy.sys_id;
	var cart = new Cart(null, setOpenedBy);
	
	//if the network ID doesnt exist in ServiceNow, dont create a request
	var existNetID = source.u_requested_for_network_id;
	var checkExisting = new GlideRecord('sys_user');
	checkExisting.addQuery("user_name", existNetID);
	checkExisting.query();
	if (!checkExisting.next()){
			ignore = true;
			gs.log('The network ID ' + existNetID + ' does not exist in ServiceNow.  This record was skipped during the import.');
	}
	
	
	//if the user isnt active, dont create a request.
	var networkID = source.u_requested_for_network_id;
	var checkInactive = new GlideRecord('sys_user');
	checkInactive.addQuery("user_name", networkID);
	checkInactive.addQuery("active", false);
	checkInactive.query();
	if(checkInactive.next()){
		if (action == "insert") {
			ignore = true;
			gs.log('The user record for ' + checkInactive.name + ' is inactive.  This record was skipped during the import.');
		}
	}
	
	else {
		
	var netID = source.u_requested_for_network_id;
	var checkActive = new GlideRecord('sys_user');
	checkActive.addQuery("user_name", netID);
	checkActive.addQuery("active", true);
	checkActive.query();
	while(checkActive.next()){
		
		//Catalog Item sys_id which you want to bulk upload
		var item = cart.addItem('fa05539c6f9bde80c7fe90264b3ee409');
		//set the additional comments and date required variables from the data source
		var dataSourceComments = source.u_additional_comments;
		var dataSourceDate = source.u_date_required;
		
		cart.setVariable(item, 'comments', dataSourceComments);
		cart.setVariable(item, 'date_required', dataSourceDate);


		ignore = true; //used so duplicate RITM's are not created
		
		// set the requested for on the RITM
		var gr = new GlideRecord("sys_user");
		gr.addQuery("user_name", source.u_requested_for_network_id);
		gr.addQuery("active", true);
		gr.query();
		if (gr.next()) {
			var cartGR = cart.getCart();
			cartGR.requested_for = gr.sys_id;
			cartGR.update();
		}
		
		//set the requested for and manager of the requested for on the actual catalog item (separate from the RITM)
		cart.setVariable(item, 'requested_for', gr.sys_id);
		cart.setVariable(item, 'manager', gr.manager);
		
		// query the user table for the requested by user record
		var setRequestedBy = new GlideRecord("sys_user");
		setRequestedBy.addQuery("user_name", source.u_requested_by_network_id);
		setRequestedBy.addQuery("active", true);
		setRequestedBy.query();
		if (setRequestedBy.next()) {
			var userCart = cart.getCart();
			userCart.update();
		}
		
		//set the requested by on the catalog item
		cart.setVariable(item, 'requested_by', setRequestedBy.sys_id);
		
		var rc = cart.placeOrder();
```
