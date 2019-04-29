# F5 AS3 Tech Day Lab


## Lab Environment

UDF Blueprint: AS3 PS Techday Lab Jan 2019

### Networks
VLAN | Subnet
---- | ------
Management | 10.1.1.0/24
Subnet 10  | 10.1.10.0/24

### Component Info & Credentials

| Host              | Management IP | Subnet 10 IP    | Username      | Password    |
| ----------------- | ------------- | --------------- | ------------- | ----------- |
| BIG-IP 1          | 10.1.1.4      | 10.1.10.11      | admin         | admin       |
| BIG-IQ CM         | 10.1.1.9      | 10.1.10.16      | admin         | admin       |
| Linux WebServer   | 10.1.1.6      | 10.1.10.100-105 | ubuntu        | key-only    |
| Windows Jump Host | 10.1.1.7      | 10.1.10.200     | Administrator | xn78LcHCtMc |


## AS3 Reference Links

* [AS3 Docs](https://clouddocs.f5.com/products/extensions/f5-appsvcs-extension/latest/)
* [AS3 Composing a Declaration](https://clouddocs.f5.com/products/extensions/f5-appsvcs-extension/latest/userguide/composing-a-declaration.html)
* [AS3 Example Declarations](https://clouddocs.f5.com/products/extensions/f5-appsvcs-extension/latest/declarations/)
* [AS3 Schema Reference](https://clouddocs.f5.com/products/extensions/f5-appsvcs-extension/latest/refguide/schema-reference.html)
* [AS3 GitHub Page](https://github.com/F5Networks/f5-appsvcs-extension)


## Lab Guide 


### Start UDF Lab Environment

Once the environment has been started, confirm you are able to login to windows and big-ip instances with credentials above.

You may need to manually perform a configuration sync of the BIG-IPs once they come up.

***

#### Clone this git repo

From the windows host:
1. Open cmd or bash git console, which ever your prefer.
2. Clone repo with the following command: ``` git clone https://github.com/mhermsdorfer/f5-as3-tech-day-lab.git ```
3. This will clone this repository into the current working directory, which is likely: ```C:\User\Administrator```.
4. navigate a file explorer to the newly created folder: ```C:\Users\Administrator\f5-as3-tech-day-lab```

***

#### Setup Postman Environment

1. Open up Postman.
2. Ensure SSL Validation is disabled: File Menu => Settings.  Then turn off "SSL certificate verification"
3. Click Import button in top left, to import the postman collection.
4. Drag and drop the ```AS3-Tech-Day-2019-02.postman_collection.json``` file into the import window.
5. Click Import button in top left, to import the postman environment.
6. Drag and drop the ```AS3-Tech-Day-2019-02.postman_environment.json``` file into the import window.

Alternatively, you can import from link using the following:
* Postman Collection: ```https://raw.githubusercontent.com/mhermsdorfer/f5-as3-tech-day-lab/master/AS3-Tech-Day-2019-02.postman_collection.json```
* Postman Environment: ```https://raw.githubusercontent.com/mhermsdorfer/f5-as3-tech-day-lab/master/AS3-Tech-Day-2019-02.postman_environment.json```

Next, set the environment and start exploring the collection:
1. In the dropdown at the top right, where it says "No Environment" select: "AS3 Tedch Day Lab - UDF"
2. On the left hand side, where you see History & Collections, select Collections, and expand out AS3 Tech Day collection.

Your screen should look like the following:
![alt text](https://raw.githubusercontent.com/mhermsdorfer/f5-as3-tech-day-lab/master/postman_setup.png "Postman Screenshot")

***

### Install AS3 Extension

* Expand the "Install AS3" Folder.
* Enable iAppsLX: This first request is not 100% necessary, but it enables GUI management of the iAppLX extensions.
* Execute the "Download the AS3 Package to BIG-IP", this leverages the standard /mgmt/tm/util/bash iControlREST API Endpoint to run a curl command.  It downloads the AS3 RPM from GitHub and saves it to ```/var/config/rest/downloads/f5-appsvcs-3.8.0-3.noarch.rpm```
* Install AS3 RPM: This uses a fairly new iControlREST endpoint: /mgmt/shared/iapp/package-management-tasks, to install the AS3 RPM.  The F5 will respond with a status id that we will populate into an environment variable.  You can see this happening under the "Tests" tab of the request.
* Check the status of the RPM Install.  Here we send a GET request to the previous endpoint with the status id, to see if the RPM was installed.  You should see status: FINISHED
* Perform the AS3 Self-Test: You should get a 200 OK response with the message "all tests passed"

Additionally, There's also an example of how to get a list of iAppLX extensions via a GET request. As well as an example of how to delete the currently deployed instance of the AS3 iAppLX RPM.  If you do run the un-install request, you'll need to re-instal the RPM before proceeding.  Additionally, notice that there are several installed iAppLX RPMs these are for elements such as APM's new AGC or Access Guided Configuration GUI wizards.

***

### Deploy first application

* Expand the "Deploy Example Applications" folder.
* Review the "Basic TCP Load Balancing" declaration.
  * Tenant is: "TCP_LB_stack_1"
  * Application is: "ssh_app" using the tcp template.
  * It contains a 1 service class: Service_TCP, as well as a Pool class and Monitor class.  Note hose these tie into each other, the serviceMain class referencing the pool, etc.
* Send this request.  You should get a 200 OK response with message success.
* Login to the BIG-IP and review the configuration it created.
  * A new Partition: "TCP_LB_stack_1" was created.
  * Inside that new partition, a folder was created for the application "ssh_app"
  * The folder contains the monitor, pool, and serviceMain virtual server.
* Next, review the "2nd Basic TCP Load Balancing" declaration.
* Send this request.  This time you get back an error, 422.
* Why did this declaration fail, what could be done to fix it?
* Now, re-deploy the 1st Basic TCP load Balancing declaration using the Re-Deploy with shareNodes declaration.
  * Note the only difference is in the pool member statement, we have an additional attribute "shareNodes" set to true, this places the LTM node objects in the /Common/ partition.
* Now, you can successfully deploy the 2nd Basic TCP load Balancing declaration.

* Did you notice that the BIG-IP pair is now out of sync because by default AS3 does not perform a config sync operation after deployment?
  * This is fixable using the "syncToGroup" in the AS3 class declaration.

***

### Deploy HTTP/HTTPS applications & Adding applications to a tenant

From now on, we'll use shareNodes true in this lab, as we have a limited number of simulated pool members/nodes that we're sharing amongst multiple tenants.  Additionally, we'll specify "syncToGroup": "/Common/sync-failover-dg" in the AS3 class declaration which will perform a configuration sync.

* Review the "Basic HTTP Load Balancing" application.
  * Note: the different class "Service_HTTP" this tells AS3 to apply http profile to the virtual server.
* Deploy this new HTTP load balancing application and review configuration on BIG-IP.

Next we'll look at HTTPS Offload and HTTPS Bridging:

* Review & Deploy the declaration for "HTTPS Application with SSL Offload"
* Review the Declaration for "HTTPS Application with SSL Bridging"
  * Note that his is in the same tenant as the previous application.  What do you think will happen when we deploy it?
  * Deploy this application, review the result, was this expected?
* Review & Deploy the declaration for "HTTPS Tenant with SSL Offload and SSL Bridging Applications"
  * See that we have two applications in the tenant now.
  * Note that both applications have virtual servers named "serviceMain"
* Review the PATCH to add a single application to an existing tenant.
  * Note the different syntax, action: patch, and we include a patchBody to describe what we're patching.
  * Send this request.  Note, the response includes the full tenant declaration.

***

### Review General AS3 Operations

* Expand the "General Operations" folder.
* Review how to get a list of declarations with and without defaults
* Review how to delete declarations.


***

### Review AS3ExampleDeclarations from official tutorial

* As time permits explore the official AS3 example Declarations, included in this postman collection.
* Documentation here: [AS3 Example Declarations](https://clouddocs.f5.com/products/extensions/f5-appsvcs-extension/latest/declarations/)


