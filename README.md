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

![Create an OpenShift cluster](/images/dot2.jpg)

- After you download the code run the installer.  The latest release of CRC takes care of installing CRC for you.

