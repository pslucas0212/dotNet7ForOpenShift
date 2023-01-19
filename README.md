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
Started the OpenShift cluster.

The server is accessible via web console at:
  https://console-openshift-console.apps-crc.testing

Log in as administrator:
  Username: kubeadmin
  Password: TDjTx-BwDqi-vDvqA-VAxJz

Log in as user:
  Username: developer
  Password: developer

Use the 'oc' command line interface:
  $ eval $(crc oc-env)
  $ oc login -u developer https://api.crc.testing:6443
```






## References
- [Product Documentation for Red Hat OpenShift Local 2.12](https://access.redhat.com/documentation/en-us/red_hat_openshift_local/2.12)
- [Product Documentation for .NET 7.0]([https://access.redhat.com/documentation/en-us/net/5.0/html-single/getting_started_with_.net_on_rhel_8/index#publishing-apps-using-dotnet_using-dotnet-on-rhel](https://access.redhat.com/documentation/en-us/net/7.0))
- [Managing Imagestreams OCP 4.11]([https://docs.openshift.com/container-platform/4.7/openshift_images/image-streams-manage.html](https://docs.openshift.com/container-platform/4.11/openshift_images/image-streams-manage.html))

