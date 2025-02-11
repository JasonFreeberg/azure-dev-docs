---
title: "3: Create the local authenticated Static web app"
titleSuffix: Azure Developer Center
description: In this article, learn to configure a Static web app and API to use the MSAL SDK to authenticate users both on the client app and on the Azure Function API. 
ms.topic: how-to
ms.date: 06/17/2021
ms.custom: devx-track-js
---

# Create the local authenticated Static web app

In this article, learn to configure a Static web app and API to use the MSAL SDK to authenticate users both on the client app and on the Azure Function API.

Start by cloning the full client/API sample. Then set the MSAL configuration values you obtained when you registered the identity application. Then run the application locally.

The React app passes an authenticated user's access token to the Function API so that the API can act on behalf of the user. This specific API calls the Microsoft Graph, _as an example_ of calling an Azure service.

## Clone the GitHub repository for the sample project

The sample code is _part of_ a repository with several samples. While you are cloning the entire repo, make sure that in the rest of the article, you are focused on just the single sample.

1. In a bash terminal on your local machine, clone the sample repository.

    ```bash
    git clone https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial
    ```

1. Navigate to the sample for this article in the `\4-Deployment\2-deploy-static\App` directory, then install the dependencies. 

    ```bash
    cd "ms-identity-javascript-react-tutorial\4-Deployment\2-deploy-static\App" && \
    npm install && \
    cd api && \
    npm install && \
    cd .. 
    ```

1. Open the project in VS Code

    ```bash
    code .
    ```

## Configure settings and secrets

The React client and the Azure Function both need to have configuration settings to use the MSAL SDK. 

You should have collected the following information from the [previous article in this series](register-application-with-identity.md):

* Application (client) ID
* Directory (tenant) ID
* Client secret
* App ID URI

1. Open the React file, `./src/authConfig.js`. and set the following values:

    |Property|Value|Description|
    |--|--|--|
    |msalConfig.auth.clientId|Application (client) ID|Enter value as string.|
    |msalconfig.auth.redirectUri|"http://localhost:3000"|
    |msalconfig.auth.postLogoutRedirectUri|"http://localhost:3000"|    
    |functionApi.scopes|`https://<App ID URI>/access_as_user`|Enter value as part of the URI, `<App ID URI>`.|

1. Open the Function API file, `./api/local.settings.json`, and set the following values:

    |Property|Value|Description|
    |--|--|--|
    |CLIENT_ID|Application (client) ID|Enter value as string.|
    |CLIENT_SECRET|Client secret|Enter value as string.|
    |TENANT_INFO|Directory (tenant) ID|Enter value as string.|

## Configure local proxy

When deployed to Azure Static web apps, the React client's calls to `/api/hello` are proxied to the Azure Function app without having to set up the proxy. For local development, you need to set up the proxy.  

Open the `./src/package.json` file and add the following property object to the root.

```json
"proxy": "http://localhost:7071",
```

## Run the app locally

1. In a VSCode interactive bash terminal, build the React client app. 

    ```bash
    npm start
    ```

1. In a VSCode interactive bash terminal, build the Function API

    ```bash
    cd ./api && npm start
    ```

## Sign in to use the app

1. Open a browser with the local client URL, `http://localhost:3000/`.

1. The first time you sign in to the app, you (as the user) need to give the Authentication app (created in the Azure portal) permission to access your data. This is the same API permissions created in the authentication app:

    * Microsoft Graph - User.Read
    * Your own API - access_as_user

1. Once you are logged in, select the **Profile** Menu item to access the Graph API from the React client. 

    :::image type="content" source="../../../media/how-to-with-authentication-static-web-app-msal/msal-react-profile-microsoft-graph.png" alt-text="A browser screenshot show the Microsoft Graph profile information for the signed in user from the React client.":::

1. Select the **Function API** Menu item to access the Graph API from the Function API. 

    :::image type="content" source="../../../media/how-to-with-authentication-static-web-app-msal/msal-react-function-api-microsoft-graph.png" alt-text="A browser screenshot show the Microsoft Graph profile information for the signed in user from the Function API.":::
 
1. When you are done using the app, close both VS Code interactive terminals to stop the application.

## View user sign-ins from Azure portal

As an administrator of your authentication app, you may want to see login history and errors. 

1. Sign in to the Azure portal, then search for **Enterprise applications**.
1. On the Enterprise applications page, search for the name of your app you registered [in this step](register-application-with-identity.md#create-microsoft-identity-provider-app-registration), such as `msal-react-spa-YOUR-NAME`. Or you can search with the client ID from the `./src/authConfig.js` file. 
1. Select your app from the list. 
1. Select **Users and groups** to see the people that are in your app. Because you just created the app and you are the only person that logged in, you should just see your own Identity record.
1. If you need to see who has signed in, select **Sign-ins**.  

## Next steps

* [Learn how to deploy a Static web app]([5. Create Azure Static Web app resource](../../../tutorial/static-web-app/create-static-web-app-visual-studio-code-extension.md))
