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





## References
- [Product Documentation for Red Hat OpenShift Local 2.12](https://access.redhat.com/documentation/en-us/red_hat_openshift_local/2.12)
- [Product Documentation for .NET 7.0]([https://access.redhat.com/documentation/en-us/net/5.0/html-single/getting_started_with_.net_on_rhel_8/index#publishing-apps-using-dotnet_using-dotnet-on-rhel](https://access.redhat.com/documentation/en-us/net/7.0))
- [Managing Imagestreams OCP 4.11]([https://docs.openshift.com/container-platform/4.7/openshift_images/image-streams-manage.html](https://docs.openshift.com/container-platform/4.11/openshift_images/image-streams-manage.html))

