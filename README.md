<!--
Copyright © Microsoft Corporation
All rights reserved.
Creative Commons Attribution 4.0 License (International): https://creativecommons.org/licenses/by/4.0/legalcode
-->

# LogPoints for Node.js on Azure

## What are Logpoints?
Logpoints are dynamic log statements that you can insert into your Node.js application when running on Azure (as a [web app for container](https://azure.microsoft.com/en-us/services/app-service/containers/) environment).

Logpoints help you to at realtime print values of objects and variables you are interested in to standard out of the container. With Logpoints, we provide a mechanism for you to debug your application with minimal overhead allowing you to quickly understand and resolve issues with your application.

## How does Logpoints work?
The core part of the logpoints experience is an agent that runs alongside your application within the AppServices environment. This agent is listening to commands from a logpoints client (e.g. VS Code). The client will request actions on the agent like attaching to a process, fetch scripts from application server, setting/removing logpoints on scripts running on your application server.

![Logpoints Architecture](/docs/assets/logpoints-arch.PNG)

When a client requests a debug session, the agent will put your application process into debug mode for the duration of the session. After that you can download scripts, set logpoints and have them be evaluated whenever that code is executed by your application. Once your session is closed/disconnected, the agent will put your application back into non-debug mode.

## Limitations
1. LogPoints can only be set from VS Code.
1. Currently logpoints will only work within Linux based Web Apps for Containers experience within Azure AppServices.
1. Applications must be running on Node 8.8.1
1. Custom Docker images for Logpoints are built on top of the existing [Azure AppServices Dockerfile](https://github.com/Azure-App-Service/node/tree/master/8.2.1). Additions include changes required to configure and start the logpoints agent.
1. The Docker images are available only on the Azure Container registry noted in the sections below.
1. Actions done on Azure web portal (e.g. restarting app, deleting app, changing configuration, etc.) while also using the VS Code extension can put your logpoints session in a bad state. This could mean logpoints are not longer set, invalid or crash your application.
1. Using invalid LogPoints expression can cause your app to crash. Take a look at the [Logpoint expression section](#logpoint-expressions) below for examples and recommendations when setting logpoints.
1. Do not use logpoints with an application that contains ```debugger``` statments in it's source code. The logpoints debugger might pause your application indefinetly.

## Documentation

- [Getting started with LogPoints on Azure App Services](docs/End-to-end-setup-and-configuration.md)

## Contributing & Feedback

Have a question or feedback?

Please send all feedback/questions to the [logpoints@microsoft.com](mailto:logpoints@microsoft.com?Subject=Nodejs%20Logpoints%20Question&Body=Issue%20type%3A%20%3CFeedback%20or%20Bug%3E%0D%0A%0D%0ADescription%3A%20%3Cdescribe%20your%20issue%20here%3E%0D%0A%0D%0ARepro%20steps%3A%20%0D%0A%3CEnter%20the%20steps%20you%20followed%20to%20run%20into%20the%20issue%20you%20are%20describing.%20%20Be%20as%20clear%20as%20possible.%20%3E%0D%0A%0D%0ALogs%3A%20%20%3CPlease%20attach%20the%20agent%20logs%20from%20your%20application%20by%20following%20the%20instructions%20here%20-%20https%3A%2F%2Fgithub.com%2FMicrosoft%2Fvscode-nodejs-logpoints-docs%2Fblob%2Fdhanvik%2Fsetup-instructions%2Fdocs%2FEnd-to-end-setup-and-configuration.md%23downloading-agent-logs%20%3E%0D%0A%0D%0A%0D%0AThank%20you.%0D%0ALogpoints%20team%29). *The template after you click on the email link sometimes might not be properly formatted. Sorry for the inconvenience.*


## License

By using this software you agree to the [license terms](https://microsoft.github.io/vscode-nodejs-logpoints-docs/license/preview-eula.html) and [privacy statement](https://www.microsoft.com/en-us/privacystatement/EnterpriseDev/default.aspx).

License for documentation:

    Copyright © Microsoft Corporation
    All rights reserved.
    Creative Commons Attribution 4.0 License (International): https://creativecommons.org/licenses/by/4.0/legalcode

