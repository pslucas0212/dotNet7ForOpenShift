# Run .Net 7 code on Red Hat OpenShift Container Platform on Mac OS

Create a simple Hello World .Net 7 application and run it on a local instance of Red Hat OpenShift AKA OpenShift Local. See how easy it is to get started with development on OpenShift Container Platform (OCP). OCP supports many languages and you can easily bring your .Net code to the world of containers and Kubernetes with OCP.

Updated 19 January 2023

### Pre-req .Net 7 SDK
- Note: I'm using a Mac for this example.
- Download and install [.Net 7 SDK](https://dotnet.microsoft.com/en-us/download/dotnet/7.0) for your OS.
- Note: For this tutorial I used Microsoft's installation package for SDK 7.0.102

![Download .Net 7](images/dot01.jpg)

- Install the .Net SDK per the instructions for your OS
- Test your .Net SDK installation

```
% dotnet --version
7.0.102
```

## Install Red Hat OpenShift Local
- For this next step you will need a Red Hat account.
- Download the Red Hat OpenShift Local (RHOL) for you OS from the Red Hat Hybrid Cloud Console [Create an OpenShift cluster](https://console.redhat.com/openshift/create/local) 
- Chose the "local" tab and select your OS.
- After you download CRC, click the Download pull secret

![Create an OpenShift cluster](/images/dot02.jpg)

- After you download the code run the installer.  The latest release of RHOL takes care of installing RHOL for you.

## Prep your CodeReady Containers environment
- After installing RHOL, we will do the rest of the work on the command line.  On the Mac open a Terminal window.  I placed the pull secret in my home Documents folder in a folder I labeled crc.  On the Mac or Linux it might look like this: ~/Documents/rhol

```
% mv pull-secret ~/Documents/rhol/
```

- Check the RHOL version.  Note that RHOL still uses the previous Code Ready Containers acronym - crc.  As of 01/19/2023 the latest release was 2.12 with OpenShift 4.11.18

```
% crc version
CRC version: 2.12.0+ea98bb41
OpenShift version: 4.11.18
Podman version: 4.2.0
```      
- Run the setup command to download the CRC bundle and prep your environment. This typically takes a few mintues.
```      
% crc setup
CRC is constantly improving and we would like to know more about usage (more details at https://developers.redhat.com/article/tool-data-collection)
...
Your system is correctly setup for using CRC. Use 'crc start' to start the instance
 ```
        
## Start up Red Hat OpenShift Local
- Start up RHOL an include the pull secret you previously downloaded

```
% crc start -p ~/Documents/rhol/pull-secret
INFO Checking if running as non-root              
INFO Checking if crc-admin-helper executable is cached 
...

````

- Depending on your hardware, it make several minutes for CRC to startup.  Be sure to copy the login information that is displayed on the console after starting CRC.  See example output below.

```
...
INFO Adding crc-admin and crc-developer contexts to kubeconfig... 
Started the OpenShift cluster.

The server is accessible via web console at:
  https://console-openshift-console.apps-crc.testing

Log in as administrator:
  Username: kubeadmin
  Password: zXje7-...-6aUho

Log in as user:
  Username: developer
  Password: developer

Use the 'oc' command line interface:
  $ eval $(crc oc-env)
  $ oc login -u developer https://api.crc.testing:6443
```
- We can test our login to RHOL with the information from the installation.  We use the oc login command provide the userid and passward along with the URL to the RHOL Server.
```
% eval $(crc oc-env)
% oc login -u kubeadmin -p zXje7-...-6aUho https://api.crc.testing:6443
Login successful.

You have access to 67 projects, the list has been suppressed. You can list all projects with 'oc projects'

Using project "default".
```
## Create a sample Hello World .Net web app
Now let's create our sample .Net application.  Choose a directory where you would like to store your sample appication.  I created my sample Hello World .Net web app in a directory called projects.  We will use the .Net Model-View-Controller (mvc) template to build our sample .Net application.

```
% dotnet new mvc -o myWebApp --no-https
```

- When the sample app code has finished generating change into the applicaiton's directory and start up the applicaiton.

```
% cd myWebApp
% dotnet run
Building...
...
info: Microsoft.Hosting.Lifetime[0]
      Content root path: /Users/palucas/projects/myWebApp
```
       
- The app starts up quickly and is ready to test when **Content root path:...** printed to the terminal screen.  Follow the instructions to access the application via a browser.
- The URL for my sample app was found on this line in the terminal output
```
Now listening on: http://localhost:5206
```

![.Net App Welcome Page](/images/dot03.png)

- When you are finished return to the terminal window and type Ctrl-c to stop the .Net test server.


## Prep OCP for .Net
- Login to OCP as developerl

      % eval $(crc oc-env)
      % oc login -u developer https://api.crc.testing:6443

- To see who you are logged in as type the following

      % oc whoami
      developer
      
- Create a new OCP project (K8s namespace) for our .Net Welcome application.

      % oc new-project my-first-app
      
- You can check the project you are currently in with the following oc command:

      % oc get projects
      NAME           DISPLAY NAME   STATUS
      my-first-app                  Active

- If this is your first .Net project in CRC, then you'll need to add a .Net imagestreams.  Imagestreams makes it easy to build and deploy our .Net app in a container and can be used to trigger new deployments when a new image becomes available   See links below for more informaton on Imagestreams

      % oc create -f https://raw.githubusercontent.com/redhat-developer/s2i-dotnetcore/master/dotnet_imagestreams.json
      % oc replace -f https://raw.githubusercontent.com/redhat-developer/s2i-dotnetcore/master/dotnet_imagestreams.json
      
## Build, Deploy and access your new .Net app
- Create a new build configuration.  We will use a Red Hat Universal Base Image (ubi8) that includes the .Net 5 SDK and runtimes.  UBI containers are OCI-compliant.  We are building the app from binary contents.  

      % oc new-build --name=my-web-app dotnet:5.0-ubi8 --binary=true
```      
--> Found image b264294 (13 days old) in image stream "my-first-app/dotnet" under tag "5.0-ubi8" for "dotnet:5.0-ubi8"

    .NET 5 
    ------ 
    Platform for building and running .NET 5 applications

    Tags: builder, .net, dotnet, dotnetcore, dotnet-50

    * A source build using binary input will be created
      * The resulting image will be pushed to image stream tag "my-web-app:latest"
      * A binary build was created, use 'oc start-build --from-dir' to trigger a new build

--> Creating resources with label build=my-web-app ...
    imagestream.image.openshift.io "my-web-app" created
    buildconfig.build.openshift.io "my-web-app" created
--> Success
```
      
- Start the build and specify the path to the binary artifacts in .Net project (I'm at the "root" of my project folder myWebApp).

      % oc start-build my-web-app --from-dir=bin/Release/net5.0/publish
      
```
Uploading directory "bin/Release/net5.0/publish" as binary input for the build ...
...........
Uploading finished
build.build.openshift.io/my-web-app-1 started
```
      
- You can also check the logs to see if the build is completed.

      % oc logs -f bc/my-web-app
      
- Create our new application on OCP.

      % oc new-app my-web-app
   
```
--> Found image 4dff9a4 (10 minutes old) in image stream "my-first-app/my-web-app" under tag "latest" for "my-web-app"

    .NET 5 
    ------ 
    Platform for building and running .NET 5 applications

    Tags: builder, .net, dotnet, dotnetcore, dotnet-50


--> Creating resources ...
    deployment.apps "my-web-app" created
    service "my-web-app" created
--> Success
    Application is not exposed. You can expose services to the outside world by executing one or more of the commands below:
     'oc expose service/my-web-app' 
    Run 'oc status' to view your app.
```

- You can check the status of your app.

      % oc status
   
```
In project my-first-app on server https://api.crc.testing:6443

svc/my-web-app - 10.217.4.30:8080
  deployment/my-web-app deploys istag/my-web-app:latest <-
    bc/my-web-app source builds uploaded code on istag/dotnet:5.0-ubi8 
    deployment #2 running for 46 seconds - 1 pod
    deployment #1 deployed 46 seconds ago


1 info identified, use 'oc status --suggest' to see details.
```

- At this point we can log into the OCP console and see our application.

![OCP Console login](/images/dot05.png)

- Click on the Topology tab on the left side and the click the my-web-app project link.

![OCP Console Topology](/images/dot06.png)

- Hover over the app icon and click on the blue arrow to see more detail about our application.  Notice there is no route defined to get to our application.  A service is created when we created a new application, but we need to expose that service via a route to access our application from outside the OCP cluster

![application detail](/images/dot07.png)


- Back at the command line let's make our app available to the outside world.

      % oc expose service/my-web-app
      route.route.openshift.io/my-web-app exposed
      
- Get the the URL to your app.

      # oc status

```
In project my-first-app on server https://api.crc.testing:6443

http://my-web-app-my-first-app.apps-crc.testing to pod port 8080-tcp (svc/my-web-app)
  deployment/my-web-app deploys istag/my-web-app:latest <-
    bc/my-web-app source builds uploaded code on istag/dotnet:5.0-ubi8 
    deployment #2 running for 8 minutes - 1 pod
    deployment #1 deployed 8 minutes ago


1 info identified, use 'oc status --suggest' to see details.
```

- Back at the OCP console we now see our app is available to the outside world.  You can click on the link in the Routes section to access your application.

![App route available](/images/dot08.png)

## Access the OCP console to see your project and app
- No we can see our application running on OCP.  My URL looked like this: https://console-openshift-console.apps-crc.testing

![.Net App Running on OCP](/images/dot09.png)


## References
- [Product Documentation for Red Hat OpenShift Local 2.12](https://access.redhat.com/documentation/en-us/red_hat_openshift_local/2.12)
- [Product Documentation for .NET 7.0]([https://access.redhat.com/documentation/en-us/net/5.0/html-single/getting_started_with_.net_on_rhel_8/index#publishing-apps-using-dotnet_using-dotnet-on-rhel](https://access.redhat.com/documentation/en-us/net/7.0))
- [Managing Imagestreams OCP 4.11]([https://docs.openshift.com/container-platform/4.7/openshift_images/image-streams-manage.html](https://docs.openshift.com/container-platform/4.11/openshift_images/image-streams-manage.html))

