GCPE News WebApp
======================

OpenShift Configuration and Deployment
----------------

The gcpe-news-tools (Tools) project contains the Build Configurations (bc) and Image Streams (is) that are referenced by the Deployment Configurations.

The following projects contain the Deployment Configurations (dc) for the various types of deployments:
- gcpe-news-dev (Development)
- gcpe-news-test (Test)
- gcpe-news-prod (Production)
 

Steps to configure the deployment:
----------------------------------

Ensure that you have access to the build and deployment templates.

These can be found in the OpenShift/Templates folder of the project repository.

If you are setting up a local OpenShift cluster for development, fork the main project.  You'll be using your own fork.

Connect to the OpenShift server using the CLI; either your local instance or the production instance. 
If you have not connected to the production instance before, use the web interface as you will need to login though your GitHub account.  Once you login you'll be able to get the token you'll need to login to you project(s) through the CLI.

The same basic procedure, minus the GitHub part, can be used to connect to your local instance.

Login to the server using the oc command from the page.
Switch to the Tools project by running:
`oc project gcpe-news-tools`

`oc process -f https://raw.githubusercontent.com/bcgov/gcpe-news-webapp/master/OpenShift/templates/build-template.json | oc create -f -`

This will create the objects necessary to build the product.

 You can now login to the OpenShift web interface and observe the progress of the initial build.

**Deployment**

Once you have a valid image built, you can proceed with Deployment.

In the command prompt, type
`oc project gcpe-news-dev`
By default projects do not have permission to access images from other projects.  You will need to grant that.
Run the following:
`oc policy add-role-to-user system:image-puller system:serviceaccount:<project_identifier>:default -n <project namespace where project_identifier needs access>`

EXAMPLE - to allow the production project access to the images, run:

`oc policy add-role-to-user system:image-puller system:serviceaccount:gcpe-news-dev:default -n gcpe-news-tools`


In the command prompt, type:
`oc process -f deployment-template.json | oc create -f -`

This should produce a deployment configurations and service.  
Verify that the Overview looks correct in the OpenShift web UI for the project you provisioned


You can now trigger deployments.

-Go to the Deployments page
-Select the component to deploy by clicking the name
-Click Deploy

If any builds or deployments fail, you can use the events view to see detailed errors.


OpenShift Origin Notes
----------------------
When deploying to OpenShift Origin on a laptop or personal computer, there are some considerations to keep in mind.

Depending on your computer's hardware performance, you may need to increase the OpenShift timeout for communication.

Execute the following commands from a command prompt:

```oc login -u system:admin
   oc project default
   oc export dc/router -o json > router.json
```

   Edit the file router.json, and add the following environment variable:
```json
	{
		"name": "ROUTER_DEFAULT_SERVER_TIMEOUT",
		"value": "500s"
	},

```

