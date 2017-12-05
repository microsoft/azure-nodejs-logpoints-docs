# Index
- [What are Logpoints](#what-are-logpoints)
- [VSCode Extension (Client machine)](#vscode-extension--client-machine-)
  * [Enable logpoints](#enable-logpoints)
  * [Azure Setup and config](#azure-setup-and-config)
- [WebApp for containers environment setup and configuration](#webapp-for-containers-environment-setup-and-configuration)
  * [Creating WebApp For containers environment](#creating-webapp-for-containers-environment)
  * [Configuring new AppService environment](#configuring-new-appservice-environment)
  * [Configure diagnostics logging](#configure-diagnostics-logging)
- [Deploying your Node Application](#deploying-your-node-application)
  * [GIT deployments](#git-deployments)
  * [Zip deployments](#zip-deployments)
- [Setting up Logpoints](#setting-up-logpoints)
  * [Starting a logpoints session](#starting-a-logpoints-session)
  * [Attaching to a node process](#attaching-to-a-node-process)
  * [Browsing your application source code](#browsing-your-application-source-code)
  * [Setting logpoints](#setting-logpoints)
  * [Looking at logpoints output](#looking-at-logpoints-output)
  * [Disconnecting your session](#disconnecting-your-session)
- [Logpoint Expressions](#logpoint-expressions)

# What are Logpoints
Logpoints are dynamic log statements that you can insert into your application in Azure (as a web app for container environment). Logpoints help you to at realtime print values of objects and variables you are interested in to standard out of the container. With Logpoints, we provide a mechanism for you to debug your application with minimal overhead allowing you to quickly understand and resolve issues with your application.

# VSCode Extension (Client machine)
1. Install VSCode
1. Install `Azure Account` extension following [these instructions](https://code.visualstudio.com/docs/editor/extension-gallery#_browse-and-install-extensions)
1. Install `Azure App Service tools` extension.
1. Update `Azure App Service tools` extension to the preview version with Logpoints support following [these instructions](https://code.visualstudio.com/docs/editor/extension-gallery#_install-from-a-vsix) using the .vsix obtained from [here](https://logpointsassets.blob.core.windows.net/vscode/vscode-azureappservice-0.4.0.vsix)

![VScode Extension installation](/docs/assets/vscode-install-vsix.png)
1. Restart VS Code to enable the extensions

## Enable logpoints
1. After installation, enable Logpoints by adding the following line in **User Settings** section under ``` File > Preferences > Settings```
```
"appService.logpoints": true
```
![Updating settings](/docs/assets/vscode-settings.png)

## Azure Setup and config
1. After installing the extensions, you will need to complete the login/setup into Azure by clicking on the "Sign in to Azure..." node in the AZURE APP SERVICE widget.

# WebApp for containers environment setup and configuration

## Creating WebApp For containers environment
1. Click here to get started [Web App for Containers]( https://azuremarketplace.microsoft.com/en/marketplace/apps/Microsoft.AppSvcLinux/?utm_source=mkt-AppSvcLinux&utm_content=43048d7e6854426a81f10470cdba990a&utm_campaign=social&utm_medium=E)

![Web App for Containers](/docs/assets/webapp-for-containers-launch.png)

1. Login into the Azure console.

## Configuring new AppService environment
After clicking "Create" to create a new WebApp for container, follow the instructions below to create a new environment using the Logpoints container.
1. Enter an app name 
1. Select a subscription
1. Create a new resource group **OR** create a new one..
1. Click Configure container
    1. Select `Private Registry`
    1. Image and optional tag: `azurenodelogpoints.azurecr.io/azure-nodejs-logpoints:latest`
    1. Server URL: `https://azurenodelogpoints.azurecr.io` 
    1. Login username : `b19ab41b-f194-4269-b551-040a6446ca3a`
    1. Password: `%96c8064e-d3d0-11e7-b604-239b97631f61~`
1. Hit the `OK` button and the blue `Create` button

![Docker repo setup](/docs/assets/appsvc-docker-repo-setup.png)


## Configure diagnostics logging
After your environment is created, you will need to enable diagnostic logging to be able to stream logs when using logpoints. 
1.  Browse to your environment's **Diagnostic logs** section and click on **FIle System** as shown below.
1. Hit `Save`. This will restart your container . 

![AppService container diagnostics logs setup](/docs/assets/appsvc-config-diag-logs.png)


Once your environment is ready, your environment should show the default Azure starting page.

![Default start page for app](/docs/assets/default-app-start-page.png)


#  Deploying your Node Application
## GIT deployments
* To enable Git based deployments, you will need to update **Properties** for your environment to set the following to true

![Setting up git base deployments](/docs/assets/appsvc-config-git-setup.png)

* Setup your environment to enable Git based deployments by following the instructions [here](https://docs.microsoft.com/en-us/azure/app-service/app-service-deploy-local-git). 

## Zip deployments
To use zip deployments, you can use the scripts linked [here](scripts-for-zip-based-app-deployment.md)

# Setting up Logpoints
## Starting a logpoints session
1. After VSCode is opened, open any arbitrary folder. (This is a workaround for a VSCode bug, it's fixed but not released)
1. Using VSCode AppService Extension connect to the environment you created above.
1. Select your environment under your subscription
1. Right click on the environment you create earlier and click on `Start Logpoints debug session` in the context menu as shown below.

![Launching Logpoints session](/docs/assets/vscode-launch-lp-session.png)

## Attaching to a node process
* Once VSCode is able to start a session with the agent, you will see a picker at the top of the screen listing the currently running node processes. Pick the process you want to debug. 

![Picking a node process to attach](/docs/assets/vscode-lp-picking-process.png)

## Browsing your application source code
* After process selection, VSCode will open a new debug session window where it would load application scripts in the lower left hand corner of VSCode.
* Depending on the number of scripts in your application, it can take some time.  Please wait for the scripts to load.

![Scripts loaded after attaching to process](/docs/assets/vscode-lp-loaded-scripts.png)

* Click on any script to download the script and load it in VSCode.
* Once the script is loaded, you can double-click on the script tab header to keep the script pane around. This is useful if you want to keep the scripts where you set logpoints open 

## Setting logpoints
* Once your script has loaded, set logpoints by right clicking on the line where you want to set a logpoint at and selecting `Set or remove logpoint` from the context menu.

![Setting a logpoint](/docs/assets/vscode-lp-setting-logpoint.png)

* This will open a message box at the top, asking you to enter an expression you want to log. Enter your expression and hit enter. It will take 1-2 secs for the lopoint to be persisted. 
* If logpoint was set successfully, you should see a blue diamond in the gutter.

![Gutter glyph after successfully setting logpoint](/docs/assets/vscode-lp-logpoint-success.png)

    * If logpoint was not set successfully, there was an error in your expression. Make sure to have proper JSON string as an expression.
* More details on how to set correct expressions, refer [here](#logpoint-expressions) 

## Looking at logpoints output
1. After logpoint was set successfully, 
    * Go back to *Folder* view in VSCode, 
    * Right click on your AppService application and click on "View Streaming logs" to start streaming logs. 

![Start to stream logs from your app](/docs/assets/vscode-lp-log-streaming.png)
    
    * If this is the first time streaming logs, it will ask you for an application restart. 

1. Now browse to your  application to trigger the code where you set the logpoint(s).
1. Your logpoint should appear in the `Output` pane of VSCode.

![Logpoint showing up in log stream](/docs/assets/vscode-lp-sample.png)

## Disconnecting your session
* After your investigations are completed, you can disconnect from the agent by clicking on the `Stop` icon in the debug adapter or closing VScode.

# Logpoint Expressions
Logpoints are dynamic log statements that you can insert into your running application. 

Below are some examples of valid logpoints expressions for reference
1. `"this is a logpoint"` : this will print `"this is a logpoint"` whenever the code at the line executed.
1. `value is ${prop}` : this will print `value is whatever value in prop` at the time the code is executed.
1. `"value is ", object`: this expression will print the object when the logpoint is evaluated.