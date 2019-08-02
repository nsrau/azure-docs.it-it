---
title: App Web che esegue l'accesso degli utenti (configurazione del codice)-piattaforma di identità Microsoft
description: Informazioni su come creare un'app Web per l'accesso degli utenti (configurazione del codice)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a96d17ae7fbe94877032e7b4b2aacb63f6e070ca
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562259"
---
# <a name="web-app-that-signs-in-users---code-configuration"></a>App Web per l'accesso degli utenti-configurazione del codice

Informazioni su come configurare il codice per l'app Web per l'accesso degli utenti.

## <a name="libraries-used-to-protect-web-apps"></a>Librerie usate per proteggere le app Web

<!-- This section can be in an include for Web App and Web APIs -->
Le librerie usate per proteggere un'app Web (e un'API Web) sono:

| Piattaforma | Libreria | DESCRIZIONE |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [Estensioni del modello di identità per .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | Usato direttamente da ASP.NET e ASP.NET Core, Microsoft Identity Extensions per .NET propone un set di dll in esecuzione sia su .NET Framework che su .NET Core. Da un'app Web ASP.NET/ASP.NET Core è possibile controllare la convalida dei token usando la classe **TokenValidationParameters** (in particolare in alcuni scenari ISV) |

## <a name="aspnet-core-configuration"></a>Configurazione di ASP.NET Core

I frammenti di codice in questo articolo e gli elementi seguenti sono estratti dall' [esercitazione incrementale ASP.NET Core app Web, capitolo 1](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg). È possibile fare riferimento a questa esercitazione per i dettagli di implementazione completi.

### <a name="application-configuration-files"></a>File di configurazione dell'applicazione

In ASP.NET Core, gli utenti che accedono a un'applicazione Web con la piattaforma di identità Microsoft vengono `appsettings.json` configurati tramite il file. Le impostazioni che è necessario compilare sono:

- il cloud `Instance` se si vuole che l'app venga eseguita nei cloud nazionali
- destinatari in`tenantId`
- oggetto `clientId` per l'applicazione, copiato dal portale di Azure.

```JSon
{
  "AzureAd": {
    // Azure Cloud instance among:
    // "https://login.microsoftonline.com/" for Azure Public cloud.
    // "https://login.microsoftonline.us/" for Azure US government.
    // "https://login.microsoftonline.de/" for Azure AD Germany
    // "https://login.chinacloudapi.cn/" for Azure AD China operated by 21Vianet
    "Instance": "https://login.microsoftonline.com/",

    // Azure AD Audience among:
    // - the tenant Id as a GUID obtained from the azure portal to sign-in users in your organization
    // - "organizations" to sign-in users in any work or school accounts
    // - "common" to sign-in users with any work and school account or Microsoft personal account
    // - "consumers" to sign-in users with Microsoft personal account only
    "TenantId": "[Enter the tenantId here]]",

    // Client Id (Application ID) obtained from the Azure portal
    "ClientId": "[Enter the Client Id]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc"
  }
}
```

In ASP.NET Core, è presente un altro file che contiene l'URL`applicationUrl`() e la porta SSL`sslPort`() per l'applicazione, oltre a diversi profili.

```JSon
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:3110/",
      "sslPort": 44321
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    },
    "webApp": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      },
      "applicationUrl": "http://localhost:3110/"
    }
  }
}
```

Nel portale di Azure, gli URI di risposta necessari per la registrazione nella pagina di **autenticazione** per l'applicazione devono corrispondere a questi URL. ovvero, per i due file di configurazione `https://localhost:44321/signin-oidc` precedenti, il ApplicationUrl è `http://localhost:3110` , `sslPort` ma `CallbackPath` è specificato (44321) e è `/signin-oidc` come definito in `appsettings.json`.
  
Allo stesso modo, l'URI di disconnessione verrebbe impostato su `https://localhost:44321/signout-callback-oidc`.

### <a name="initialization-code"></a>Codice di inizializzazione

In ASP.NET Core app Web (e le API Web), il codice che esegue l'inizializzazione dell'applicazione `Startup.cs` si trova nel file e, per aggiungere l'autenticazione con la piattaforma di identità Microsoft (in precedenza Azure ad v 2.0), è necessario aggiungere il codice seguente. I commenti nel codice devono essere autoesplicativi.

  > [!NOTE]
  > Se si avvia il progetto con il progetto Web ASP.NET Core predefinito in Visual Studio o `dotnet new mvc` se si `AddAzureAD` usa il metodo è disponibile per impostazione predefinita perché i pacchetti correlati vengono caricati automaticamente. Tuttavia, se si compila un progetto da zero e si tenta di usare il codice riportato di seguito, si consiglia di aggiungere il pacchetto NuGet **"Microsoft. AspNetCore. Authentication. AzureAD. UI"** al progetto per `AddAzureAD` rendere disponibile il metodo.
  
```CSharp
 services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
         .AddAzureAD(options => configuration.Bind("AzureAd", options));

 services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
 {
  // The ASP.NET core templates are currently using Azure AD v1.0, and compute
  // the authority (as {Instance}/{TenantID}). We want to use the Microsoft identity platform endpoint
  options.Authority = options.Authority + "/v2.0/";

  // If you want to restrict the users that can sign-in to specific organizations
  // Set the tenant value in the appsettings.json file to 'organizations', and add the
  // issuers you want to accept to options.TokenValidationParameters.ValidIssuers collection.
  // Otherwise validate the issuer
  options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.ForAadInstance(options.Authority).ValidateAadIssuer;

  // Set the nameClaimType to be preferred_username.
  // This change is needed because certain token claims from Azure AD v1.0 endpoint
  // (on which the original .NET core template is based) are different in Microsoft identity platform endpoint.
  // For more details see [ID Tokens](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
  // and [Access Tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)
  options.TokenValidationParameters.NameClaimType = "preferred_username";
  ...
```

## <a name="aspnet-configuration"></a>Configurazione di ASP.NET

In ASP.NET, l'applicazione viene configurata `Web.Config` tramite il file

```XML
<?xml version="1.0" encoding="utf-8"?>
<!--
  For more information on how to configure your ASP.NET application, please visit
  https://go.microsoft.com/fwlink/?LinkId=301880
  -->
<configuration>
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:ClientId" value="[Enter your client ID, as obtained from the app registration portal]" />
    <add key="ida:ClientSecret" value="[Enter your client secret, as obtained from the app registration portal]" />
    <add key="ida:AADInstance" value="https://login.microsoftonline.com/{0}{1}" />
    <add key="ida:RedirectUri" value="https://localhost:44326/" />
    <add key="vs:EnableBrowserLink" value="false" />
  </appSettings>
```

Il codice correlato all'autenticazione in ASP.NET Web App/API Web si trova nel `App_Start/Startup.Auth.cs` file.

```CSharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // The `Authority` represents the identity platform endpoint - https://login.microsoftonline.com/common/v2.0
     // The `Scope` describes the initial permissions that your app will need.
     //  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
     ClientId = clientId,
     Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
     RedirectUri = redirectUri,
     Scope = "openid profile",
     PostLogoutRedirectUri = redirectUri,
    });
 }
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Accedere e disconnettersi](scenario-web-app-sign-user-sign-in.md)
