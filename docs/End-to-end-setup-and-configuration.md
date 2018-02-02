# LogPoints for Node.js on Azure

- [Getting started](#getting-started)
  * [Install the VS Code Extension](#install-the-vscode-extension)
    + [Enable logpoints](#enable-logpoints)
    + [Azure Setup and config](#azure-setup-and-config)
  * [Create new App Service environment on Azure](#create-new-app-service-environment-on-azure)
    + [Creating WebApp For containers environment](#creating-webapp-for-containers-environment)
    + [Setup new App Service environment](#setup-new-app-service-environment)
    + [Configure diagnostics logging](#configure-diagnostics-logging)
  * [Deploying your Node Application to Azure](#deploying-your-node-application-to-azure)
    + [Git deployments](#git-deployments)
    + [Zip deployments](#zip-deployments)
- [Using Logpoints from VS Code](#using-logpoints-from-vs-code)
  * [Enable streaming logs](#enable-streaming-logs)
  * [Starting a logpoints session](#starting-a-logpoints-session)
  * [Attaching to a node process](#attaching-to-a-node-process)
  * [Browsing your application source code](#browsing-your-application-source-code)
  * [Setting logpoints](#setting-logpoints)
  * [Downloading agent logs](#downloading-agent-logs)
  * [Looking at logpoints output](#looking-at-logpoints-output)
  * [Disconnecting your session](#disconnecting-your-session)
- [Reporting feedback](#reporting-feedback)
- [Logpoint Expressions](#logpoint-expressions)
  * [Good Examples](#good-examples)
  * [Bad examples](#bad-examples)


# Getting started
To get started, you need to install a few extension for VS Code, and deploy your Node app to Azure using App Services.

## Install the VS Code Extension
1. Install VS Code from http://code.visualstudio.com
1. Install [`Azure App Service tools`](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) extension from the Marketplace
1. Restart VS Code to enable the extensions

### Enable logpoints preview
1. After installation, enable Logpoints by adding the following line in **User Settings** section under ``` File > Preferences > Settings```
```
"appService.logpoints": true
```
![Updating settings](/docs/assets/vscode-settings.PNG)

2. Reload VS Code by opening the command palette (`F1`) and selecting `Reload Window`.

### Azure Setup and config
1. After installing the extensions, you will need to complete the login/setup into Azure by clicking on the "Sign in to Azure..." node in the `Azure App Service` explorer or by opening the Command Palette (`F1`) and choosing `Azure: Sign In`.

## Create new App Service environment on Azure
The next step is to create a App Service envrionment on Azure, where you'll be using our LogPoints-enabled container to host your Node.js application.

### Creating WebApp For containers environment
1. Click here to get started [Web App for Containers]( https://azuremarketplace.microsoft.com/en/marketplace/apps/Microsoft.AppSvcLinux/?utm_source=mkt-AppSvcLinux&utm_content=43048d7e6854426a81f10470cdba990a&utm_campaign=social&utm_medium=E)

![Web App for Containers](/docs/assets/webapp-for-containers-launch.PNG)

1. Login into the Azure console.

### Setup new App Service environment
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

![Docker repo setup](/docs/assets/appsvc-docker-repo-setup.PNG)

### Configure diagnostics logging
After your environment is created, you will need to enable diagnostic logging to be able to stream logs when using logpoints.
1. Browse to your environment's **Diagnostic logs** section and click on **FIle System** as shown below.
1. Hit `Save`. This will restart your container .

![AppService container diagnostics logs setup](/docs/assets/appsvc-config-diag-logs.PNG)


Once your environment is ready, your environment should show the default Azure starting page.

![Default start page for app](/docs/assets/default-app-start-page.PNG)

## Deploying your Node Application to Azure

### Git deployments
* To enable Git based deployments, you will need to update **Properties** for your environment to set the following to true

![Setting up git base deployments](/docs/assets/appsvc-config-git-setup.PNG)

* Setup your environment to enable Git based deployments by following the instructions [here](https://docs.microsoft.com/en-us/azure/app-service/app-service-deploy-local-git).

If the "Deployment Options" option is greyed out in the portal, make sure that you have refreshed the page after setting `WEBSITES_ENABLE_APP_SERVICE_STORAGE` to `true` in App Settings.

### Zip deployments
To use zip deployments, you can use the scripts linked [here](scripts-for-zip-based-app-deployment.md)

# Using Logpoints from VS Code

## Enable streaming logs

Before you can start a Logpoints session, you need to enable streaming logs.

* To enable streaming logs,

  * Click on the ![Folder](/docs/assets/vscode-folder-icon.PNG) to interact with the Azure AppService extension,
  * Right click on your AppService environment and click on ```View Streaming logs``` to start streaming logs.

![Start to stream logs from your app](/docs/assets/vscode-lp-log-streaming.PNG)

  * When prompted, choose "Yes".

![Enable streaming log](/docs/assets/vscode-enable-streaming-log.png)

  This operation only needs to be done once as long as the streaming logs for this App Service remains enabled.

* As an alternative, you can also enable streaming log by following the instructions [here](https://docs.microsoft.com/en-us/azure/app-service/web-sites-enable-diagnostic-log).
## Starting a logpoints session

<table>
  <tr>
    <td>
    <img src="assets/warning.png"/>
    </td>
    <td>
        To avoid running into a VS Code bug, before you start a session please open any folder and continue to the next step.
    </td>
  </tr>
</table>

1. Using VS Code AppService Extension, browse to your AppService environment.
1. Right click on the environment and click on `Start Logpoints debug session` in the context menu as shown below.

![Launching Logpoints session](/docs/assets/vscode-launch-lp-session.PNG)

3. Before the Logpoints debug session is started, Streaming Logs output pane will be opened automatically for you.

## Attaching to a node process
* Once VS Code is able to start a session with the agent, you will see a picker at the top of the screen listing the currently running node processes. Pick the process you want to debug.

![Picking a node process to attach](/docs/assets/vscode-lp-picking-process.PNG)

## Browsing your application source code
* After selecting the Node.js process to debug, VS Code will change to the Debug View with a new debug session connected to the selected Node.js process. On the lower left hand corner of VS Code a "LOADED SCRIPTS" widget will appear, which when expanded will show all the user scripts currently loaded on the Node.js application.
* Depending on the number of scripts in your application, it can take some time.  Please wait for the scripts to load.

![Scripts loaded after attaching to process](/docs/assets/vscode-lp-loaded-scripts.PNG)

* Click on any script to view the script source code inside VS Code.
* Once the script is loaded, you can double-click on the script tab header to keep the script pane around. This is useful if you want to keep the scripts where you set logpoints open

## Setting logpoints
* Once your script has loaded, set logpoints by right clicking on the line where you want to set a logpoint at and selecting `Set or remove logpoint` from the context menu.

![Setting a logpoint](/docs/assets/vscode-lp-setting-logpoint.PNG)

* This will open a message box at the top, asking you to enter an expression which will get executed each time the associated code is executed, and whose results will get logged automatically. Enter your expression and hit enter. It will take 1-2 secs for the lopoint to be persisted.
* If logpoint was set successfully, you should see a blue diamond in the gutter.

![Gutter glyph after successfully setting logpoint](/docs/assets/vscode-lp-logpoint-success.PNG)

* If logpoint was not set successfully, there was an error in your expression. Make sure to have proper JSON string as an expression.
* More details on how to set correct expressions, refer [here](#logpoint-expressions)

## Downloading agent logs
By default, logpoints agent will be logging to the ```/home/logpoints/logs/agent``` directory within appservice.  If you are running into issues (or want to report issues) with logpoints, please download the logs following the steps below:

1. Browse to the SCM url for your application: ``https://<your-app-name>.scm.azurewebsites.net/api/zip/logpoints``
1. Login to Azure portal if you are not logged in
1. A zip file containing the logs should automatically be downloaded.
1. Refer to the [Reporting feedback](#reporting-feedback) section to send us these logs.

## Looking at logpoints output
<table>
  <tr>
    <td>
    <img src="assets/warning.png"/>
    </td>
    <td>
      <ul>
        <li>All Logpoints output would be prefixed by <b>"[logpoints-agent] :"</b></li>
      </ul>
    </td>
  </tr>
</table>

1. After logpoint was set successfully, browse to your application to trigger the code where you set the logpoint(s).
1. Your logpoint should appear in the `Output` pane of VS Code.

![Logpoint showing up in log stream](/docs/assets/vscode-lp-sample.PNG)

## Disconnecting your session
* After your investigations are completed, you can disconnect from the agent by clicking on the `Stop` ![Logpoint showing up in log stream](/docs/assets/vscode-stop-debugging.PNG) icon in the debug adapter or closing VScode.

![Logpoint showing up in log stream](/docs/assets/vscode-lp-disconnected.PNG)

# Reporting feedback
Please send all feedback/questions to the [logpoints@microsoft.com](mailto:logpoints@microsoft.com?Subject=Nodejs%20Logpoints%20Question&Body=Issue%20type%3A%20%3CFeedback%20or%20Bug%3E%0D%0A%0D%0ADescription%3A%20%3Cdescribe%20your%20issue%20here%3E%0D%0A%0D%0ARepro%20steps%3A%20%0D%0A%3CEnter%20the%20steps%20you%20followed%20to%20run%20into%20the%20issue%20you%20are%20describing.%20%20Be%20as%20clear%20as%20possible.%20%3E%0D%0A%0D%0ALogs%3A%20%20%3CPlease%20attach%20the%20agent%20logs%20from%20your%20application%20by%20following%20the%20instructions%20here%20-%20https%3A%2F%2Fgithub.com%2FMicrosoft%2Fvscode-nodejs-logpoints-docs%2Fblob%2Fdhanvik%2Fsetup-instructions%2Fdocs%2FEnd-to-end-setup-and-configuration.md%23downloading-agent-logs%20%3E%0D%0A%0D%0A%0D%0AThank%20you.%0D%0ALogpoints%20team%29). *The template after you click on the email link sometimes might not be properly formatted. Sorry for the inconvenience.*

Use the below template if the link above does not work:

* Description: brief description of the issue
* Repro steps: Steps you did that resulted in the error you saw
* Logs: Attach the logs from your agent. You can download the logs by following the instructions [here](#downloading-agent-logs)
* Any other attachments that you think could help us with investigations.

# Logpoint Expressions
Logpoints are dynamic log statements that you can insert into your running application.

1. **We do not recommended** changing the state of your application within a logpoint expression.This can result in your application breaking or behaving unexpectedly.
1. **We do not recommended** doing expensive calculations within your logpoint expression. If your logpoint expressions are computing values, this can result in your application slowing down. Logpoints are meant to be lightweight logging of application state only.

## Good Examples
Below are some examples of valid logpoints expressions for reference
1. `"this is a logpoint"` : this will print `"this is a logpoint"` whenever the code at the line executed.
1. `` `value is ${prop}` `` : this will print `value is whatever value in prop` at the time the code is executed.
1. `"value is ", object`: this expression will print the object when the logpoint is evaluated.

## Bad examples
Below are some bad examples of logpoint expressions
1. ```while(true) { console.log(`${prop}`)}```: this will put your application in an infinite loop.
1. ```prop = prop + 1``` : This is updating a variable `prop` that is defined in your application. This is not recommended.
1. ```debugger```: This will cause the debugger to pause your program until the debugger is disconnected. This is strongly not recommended.
1. **Runtime errors**: ```foo=bar``` : If `bar` is not defined, you will see an error similar to below in the console output for your AppService. ![Logpoint showing up in log stream](/docs/assets/vscode-logpoint-bad-expression-1.PNG)
1. **Invalid expressions**: If logpoints are invalid, you can see an error in VS Code as shown here.
![Logpoint showing up in log stream](/docs/assets/vscode-bad-lp-alert.PNG)
