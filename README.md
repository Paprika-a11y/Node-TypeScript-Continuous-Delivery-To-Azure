# Node.js and TypeScript - Continuous Delivery to Azure
This is a small example showing how to make continuous delivery of a Node.js web application with TypeScript to Azure web app/site, including a client-side build pipeline, work smoothly. This is pretty similar to the [.NET Core and TypeScript - Continuous Delivery to Azure](https://github.com/Fjeddo/NET-Core-TypeScript-Continuous-Delivery-To-Azure) example/repo.

The example is built starting off with a blank Node.js Web Application, created using the template from [Node.js Tools for Visual Studio](https://www.visualstudio.com/en-us/features/node-js-vs.aspx). The solution will be continuously delivered/deployed to an **Azure Web App** using **Kudu** and **Gulp**.

**Please note that this Readme is committed after all other commits.** Open this file in a separate browser tab or window before browsing the code referenced in the text below.

##Prerequisites
To make the most use out of this text and code it is assumed that the reader has some knowledgde about
- Windows Azure
- Windows Azure Portal
- Azure Web App
- Continuous delivery/deployment
- Git version control
- Connecting an Azure Web App and a version contol system (VCS) for triggering deployment
- Kudu deployment engine
- Visual Studio
- NodeJs and npm
- Gulp task runner

To fully implement this solution the following tech things are needed

- An **Azure subscription** for the Azure Web App (free version is sufficient)
- A **GitHub account/repo** to be used for deployment to Azure
- **Visual Studio 2015, Community edition**
- Node.js Tools for Visual Studio (used only for the project template)
- **NodeJs version 6.5.0** or later and **npm version 3.10.3** or later
- **Azure-CLI** installed on the dev machine

**_Note that the npm tool should be invoked in the Visual Studio project root folder while Azure-CLI should be invoked in the root of the repo._** This is because of the folder structure in this example, it might be different in other solutions. The important thing is that the node_modules folder, created by npm, should be placed in the same folder as the template generated package.json, gulpfile.js and so on.

##Initial setup

Start off by creating an Azure Web App and change the **'WEBSITE_NODE_DEFAULT_VERSION'** application setting to be at least **6.5.0**. This will have effect on the Node.js and npm versions used by the server and kudu in the deployment script.

Create a GitHub repo and connect it for deployment to the Azure Web App in the Azure Portal - Deployment Options. The default setting here is that pushing changes to the master branch will trigger a deployment, at least when it comes to git.

Make sure to have a **.gitignore** file suitable for the solution. The one in this repo works for this demo.

##1. Create and tweak the Node.js web application
The Node.js web application is based on the Blank Node.js Web Application, from the Node.js Tools for Visual Studio. Hit **File - New - Project** and select the **Blank Node.js Web Application** template. Hit [OK], the project is created.

Hit F5 to see the web application in action.

Now it is time to modify the **server.js** to serve a **static index.html** instead of a raw text response. To make this work the **Express** node package is used. Install this package using
 > npm install express --save

and **modify the server.js**. Add an **index.html** file and hit F5 to verify that the html file is served. The code at this point looks like [this](https://github.com/Fjeddo/Node-TypeScript-Continuous-Delivery-To-Azure/tree/5aa85bcadfbe12e2edc15e0f635b643baf6be2a0).
 
##2. Continuously deliver the web application to Azure
At this point the web application is ready to be deployed to an Azure web app (the previous deployments probably did not work as expected). The deployment script for kudu is downloaded using the Azure-CLI
>azure site deploymentscript --node --sitePath NodejsWebAppApplication

**Note the --sitePath parameter, making kudu aware of the root of the web application.**

The web application should now deploy succesfully ([code so far](https://github.com/Fjeddo/Node-TypeScript-Continuous-Delivery-To-Azure/tree/0d587cf452f90122e1098e5404a8c03a16852df3)).

**Before adding the deployment script the kudu engine did not know what type of application to deploy and therefore the Node packages were not restored properly.**

##3. Add TypeScript and make it transpile using gulp
In the web application project root folder, add a new **scripts** folder. This folder will contain the TypeScript files for the application. In the scripts folder, add a new TypeScript file with the name **hello.ts**. This will be transpiled into a .js file to be referenced from the index.html file. The transpilation is done using gulp.

Add the **gulp** and **gulp-typescript** package:

>npm install gulp --save

>npm install gulp-typescript --save

In the project root folder add a Gulp confiugration file, **gulpfile.js**. The gulp config file contains a default task for transpiling the TypeScript file and place it in an **app** folder in the project root, at the same level as the index.html ([the code so far](https://github.com/Fjeddo/Node-TypeScript-Continuous-Delivery-To-Azure/tree/d4b67167889baed76acf5bb55c3ae8fc3b64d482)).

The default gulp task should now be visible in the **Task Runner Explorer** in Visual Studio. Right-click the task and run it. The app folder should be created and contain hello.js.

##4. Make the deployment script run the gulp tasks
By default the deployment script does not contain any client-side stuff and therefore the TypeScript transpilation gulp task is not run. The gulp tasks have to be run in a separate step in the deploy.cmd script. Committing and pushing to the GitHub remote repo will trigger a new deployment to the Azure Web App ([the code at this point](https://github.com/Fjeddo/Node-TypeScript-Continuous-Delivery-To-Azure/tree/49722456cef15b99314c7c5f278964a1369480ce)).

Browsing to the Azure Web App, http://node-typescript.azurewebsites.net in this example, with a developer console open should now display the log "**Hello from TypeScript!**".

##Done!
I really hope that this little example will be useful when setting up Continuous Delivery/Deployment to Azure using a git repo. The web application is deployed here http://node-typescript.azurewebsites.net.
