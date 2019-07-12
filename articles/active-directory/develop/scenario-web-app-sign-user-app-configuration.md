---
title: App Web di accesso degli utenti (configurazione del codice) - piattaforma delle identità Microsoft
description: Informazioni su come compilare un'app web di accesso degli utenti (configurazione del codice)
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
ms.openlocfilehash: b7484b627d3bc3f26fa01d4c38ee96047c70d007
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67785473"
---
# <a name="web-app-that-signs-in-users---code-configuration"></a>App Web che esegue l'accesso gli utenti - configurazione del codice

Informazioni su come configurare il codice per l'app Web che gli utenti esegue l'accesso.

## <a name="libraries-used-to-protect-web-apps"></a>Librerie usate per proteggere le app Web

<!-- This section can be in an include for Web App and Web APIs -->
Le librerie usate per proteggere un'App Web (e un'API Web) sono:

| Piattaforma | Libreria | Descrizione |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [Estensioni del modello di identità per .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | Microsoft Identity Extensions for .NET utilizzato direttamente da ASP.NET e ASP.NET Core, propone un set di DLL in esecuzione sia su .NET Framework e .NET Core. In un'app Web di ASP.NET/ASP.NET Core, è possibile controllare la convalida del token usando il **TokenValidationParameters** classe (in particolare in alcuni scenari ISV) |

## <a name="aspnet-core-configuration"></a>Configurazione di ASP.NET Core

Frammenti di codice in questo articolo e i seguenti estratti dal [ASP.NET Core Web app incrementale dell'esercitazione, il capitolo 1](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg). È possibile fare riferimento a tale esercitazione per i dettagli di implementazione completa.

### <a name="application-configuration-files"></a>File di configurazione dell'applicazione

In ASP.NET Core, un'applicazione accesso gli utenti Web con la piattaforma delle identità Microsoft viene configurata tramite il `appsettings.json` file. Le impostazioni che è necessario immettere sono:

- cloud `Instance` se si desidera che l'app da eseguire nei cloud nazionali
- i destinatari in `tenantId`
- il `clientId` per l'applicazione, come copiato dal portale di Azure.

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
    // - the tenant Id as a a GUID obtained from the azure portal to sign-in users in your organization
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

In ASP.NET Core, è presente un altro file che contiene l'URL (`applicationUrl`) e la porta SSL (`sslPort`) per l'applicazione, nonché diversi profili.

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

Nel portale di Azure, l'URI a cui è necessario registrare risposta il **Authentication** pagina per l'applicazione deve corrispondere a questi URL; vale a dire, per i due file di configurazione precedenti, sarebbero `https://localhost:44321/signin-oidc` come il applicationUrl viene `http://localhost:3110` ma la `sslPort` è specificato (44321) e il `CallbackPath` viene `/signin-oidc` come definito nel `appsettings.json`.
  
Nello stesso modo, l'URI di disconnessione verrebbe impostato su `https://localhost:44321/signout-callback-oidc`.

### <a name="initialization-code"></a>Codice di inizializzazione

Nell'App Web ASP.NET Core (e le API Web), il codice esegue l'inizializzazione dell'applicazione è disponibile nel `Startup.cs` file, e, per aggiungere l'autenticazione con Microsoft Identity platform (in precedenza Azure AD) v2.0, è necessario aggiungere il codice seguente. I commenti nel codice devono essere facilmente comprensibili.

  > [!NOTE]
  > Se si avvia il tuo progetto con progetto predefinita ASP.NET core web all'interno di Visual studio o mediante `dotnet new mvc` il metodo `AddAzureAD` è disponibile per impostazione predefinita perché i pacchetti correlati vengono caricati automaticamente. Tuttavia se si compila un progetto da zero e si sta provando a usare il codice riportato di seguito, è consigliabile per aggiungere il pacchetto NuGet **"Microsoft.AspNetCore.Authentication.AzureAD.UI"** al progetto per rendere il `AddAzureAD` metodo disponibile.
  
```CSharp
 services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
         .AddAzureAD(options => configuration.Bind("AzureAd", options));

 services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
 {
  // The ASP.NET core templates are currently using Azure AD v1.0, and compute
  // the authority (as {Instance}/{TenantID}). We want to use the Microsoft Identity Platform v2.0 endpoint
  options.Authority = options.Authority + "/v2.0/";

  // If you want to restrict the users that can sign-in to specific organizations
  // Set the tenant value in the appsettings.json file to 'organizations', and add the
  // issuers you want to accept to options.TokenValidationParameters.ValidIssuers collection.
  // Otherwise validate the issuer
  options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.ForAadInstance(options.Authority).ValidateAadIssuer;

  // Set the nameClaimType to be preferred_username.
  // This change is needed because certain token claims from Azure AD v1.0 endpoint
  // (on which the original .NET core template is based) are different in Azure AD v2.0 endpoint.
  // For more details see [ID Tokens](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
  // and [Access Tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)
  options.TokenValidationParameters.NameClaimType = "preferred_username";
  ...
```

## <a name="aspnet-configuration"></a>Configurazione di ASP.NET

In ASP.NET, l'applicazione è configurata tramite il `Web.Config` file

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

Il codice correlato all'autenticazione in app Web ASP.NET o API Web si trova nel `App_Start/Startup.Auth.cs` file.

```CSharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0
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
