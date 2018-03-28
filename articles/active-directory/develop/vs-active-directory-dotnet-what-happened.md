---
title: Modifiche apportate a un progetto MVC quando ci si connette ad Azure AD | Microsoft Docs
description: Viene descritto cosa succede al progetto MVC quando ci si connette ad Azure AD mediante i servizi relazionati di Visual Studio.
services: active-directory
documentationcenter: na
author: kraigb
manager: ghogen
editor: ''
ms.assetid: 8b24adde-547e-4ffe-824a-2029ba210216
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: kraigb
ms.custom: aaddev
ms.openlocfilehash: b17c5fe500f3e2a8370ec5c4a09b62737d9afb84
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="what-happened-to-my-mvc-project-visual-studio-azure-active-directory-connected-service"></a>Cosa è successo a un progetto MVC (servizio connesso a Visual Studio Azure Active Directory)?

> [!div class="op_single_selector"]
> - [Per iniziare](vs-active-directory-dotnet-getting-started.md)
> - [Risultati](vs-active-directory-dotnet-what-happened.md)

Questo articolo identifica le modifiche esatte apportate a un progetto MVC ASP.NET quando si aggiunge il servizio connesso [Azure Active Directory tramite Visual Studio](vs-active-directory-add-connected-service.md).

Per informazioni sull'uso del servizio connesso, vedere l'[introduzione](vs-active-directory-dotnet-getting-started.md).

## <a name="added-references"></a>Riferimenti aggiunti

Interessa il file di progetto (riferimenti *.NET) e `packages.config` (riferimenti NuGet).

| type | Riferimenti |
| --- | --- |
| .NET; NuGet | Microsoft.IdentityModel.Protocol.Extensions |
| .NET; NuGet | Microsoft.Owin |
| .NET; NuGet | Microsoft.Owin.Host.SystemWeb |
| .NET; NuGet | Microsoft.Owin.Security |
| .NET; NuGet | Microsoft.Owin.Security.Cookies |
| .NET; NuGet | Microsoft.Owin.Security.OpenIdConnect |
| .NET; NuGet | Owin |
| .NET        | System.IdentityModel |
| .NET; NuGet | System.IdentityModel.Tokens.Jwt |
| .NET        | System.Runtime.Serialization |

Riferimenti aggiuntivi se è stata selezionata l'opzione **Lettura dati directory**:

| type | Riferimenti |
| --- | --- |
| .NET; NuGet | EntityFramework |
| .NET        | EntityFramework.SqlServer (solo Visual Studio 2015) |
| .NET; NuGet | Microsoft.Azure.ActiveDirectory.GraphClient |
| .NET; NuGet | Microsoft.Data.Edm |
| .NET; NuGet | Microsoft.Data.OData |
| .NET; NuGet | Microsoft.Data.Services.Client |
| .NET; NuGet | Microsoft.IdentityModel.Clients.ActiveDirectory |
| .NET        | Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms (solo Visual Studio 2015) |
| .NET; NuGet | System.Spatial |

I seguenti riferimenti vengono rimossi (solo 4 progetti ASP.NET, come in Visual Studio 2015):

| type | Riferimenti |
| --- | --- |
| .NET; NuGet | Microsoft.AspNet.Identity.Core |
| .NET; NuGet | Microsoft.AspNet.Identity.EntityFramework |
| .NET; NuGet | Microsoft.AspNet.Identity.Owin |

## <a name="project-file-changes"></a>Modifiche al file di progetto

- Impostare la proprietà `IISExpressSSLPort` su un numero distinto.
- Impostare la proprietà `WebProject_DirectoryAccessLevelKey` su 0 o 1 se è stata selezionata l'opzione **Lettura dati directory**.
- Impostare la proprietà `IISUrl` su `https://localhost:<port>/` dove `<port>` corrisponde al valore `IISExpressSSLPort`.

## <a name="webconfig-or-appconfig-changes"></a>modifiche apportate al file web.config o app.config

- Sono state aggiunte le seguenti voci di configurazione:

    ```xml
    <appSettings>
        <add key="ida:ClientId" value="<ClientId from the new Azure AD app>" />
        <add key="ida:AADInstance" value="https://login.microsoftonline.com/" />
        <add key="ida:Domain" value="<your selected Azure domain>" />
        <add key="ida:TenantId" value="<the Id of your selected Azure AD tenant>" />
        <add key="ida:PostLogoutRedirectUri" value="<project start page, such as https://localhost:44335>" />
    </appSettings>
    ```

- Sono stati aggiunti elementi `<dependentAssembly>` nel nodo `<runtime><assemblyBinding>` per `System.IdentityModel.Tokens.Jwt` e `Microsoft.IdentityModel.Protocol.Extensions`.

Modifiche aggiuntive se è stata selezionata l'opzione **Lettura dati directory**:

- È stata aggiunta la seguente voce di configurazione in `<appSettings>`:

    ```xml
    <add key="ida:ClientSecret" value="<Azure AD app's new client secret>" />
    ```

- Sono stati aggiunti gli elementi seguenti in `<configuration>`; i valori per project-mdf-file e project-catalog-id variano:

    ```xml
    <configSections>
      <!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
      <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
    </configSections>

    <connectionStrings>
      <add name="DefaultConnection" connectionString="Data Source=(localdb)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\<project-mdf-file>.mdf;Initial Catalog=<project-catalog-id>;Integrated Security=True" providerName="System.Data.SqlClient" />
    </connectionStrings>

    <entityFramework>
      <defaultConnectionFactory type="System.Data.Entity.Infrastructure.LocalDbConnectionFactory, EntityFramework">
        <parameters>
          <parameter value="mssqllocaldb" />
        </parameters>
      </defaultConnectionFactory>
      <providers>
        <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
      </providers>
    </entityFramework>
    ```

- Sono stati aggiunti elementi `<dependentAssembly>` nel nodo `<runtime><assemblyBinding>` per `Microsoft.Data.Services.Client`, `Microsoft.Data.Edm` e `Microsoft.Data.OData`.

## <a name="code-changes-and-additions"></a>Aggiunte e modifiche al codice

- È stato aggiunto l'attributo `[Authorize]` a `Controllers/HomeController.cs` e ad altri controller esistenti.

- È stata aggiunta una classe di avvio dell'autenticazione denominata `App_Start/Startup.Auth.cs`, contenente la logica di avvio per l'autenticazione di Azure AD. Se è stata selezionata l'opzione **Lettura dati directory**, questo file contiene anche il codice per ricevere un codice OAuth scambiarlo con un token di accesso.

- È stata aggiunta una classe controller denominata `Controllers/AccountController.cs`, contenente i metodi `SignIn` e `SignOut`.

- È stata aggiunta una visualizzazione parziale denominata `Views/Shared/_LoginPartial.cshtml`, contenente un collegamento all'azione per `SignIn` e `SignOut`.

- È stata aggiunta una visualizzazione parziale denominata `Views/Account/SignoutCallback.cshtml`, contenente codice HTML per l'interfaccia utente di disconnessione.

- È stato aggiornato il metodo `Startup.Configuration` per includere una chiamata a `ConfigureAuth(app)` se la classe esiste già. In caso contrario, è stata aggiunta una classe `Startup` che chiama il metodo.

- È stato aggiunto `Connected Services/AzureAD/ConnectedService.json` (Visual Studio 2017) o `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015), contenente le informazioni usate da Visual Studio per tenere traccia dell'aggiunta del servizio connesso.

- Se è stata selezionata l'opzione **Lettura dati directory**, sono stati aggiunti `Models/ADALTokenCache.cs` e `Models/ApplicationDbContext.cs` per supportare la memorizzazione nella cache dei token. Sono stati aggiunti anche un altro controller e una visualizzazione per illustrare l'accesso alle informazioni relative al profilo utente tramite le API Graph di Azure: `Controllers/UserProfileController.cs`, `Views/UserProfile/Index.cshtml` e `Views/UserProfile/Relogin.cshtml`

### <a name="file-backup-visual-studio-2015"></a>Backup di file (Visual Studio 2015)

Quando si aggiunge il servizio connesso, Visual Studio 2015 esegue il backup dei file modificati e rimossi. Tutti i file interessati vengono salvati nella cartella `Backup/AzureAD`. Visual Studio 2017 non crea backup.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ManageViewModels.cs`
- `Views\Shared\_LoginPartial.cshtml`

## <a name="changes-on-azure"></a>Modifiche in Azure

- È stata creata un'applicazione Azure AD nel dominio selezionato durante l'aggiunta del servizio connesso.
- È stata aggiornata l'app per includere l'autorizzazione **Lettura dati directory** se è stata selezionata l'opzione corrispondente.

[Altre informazioni su Azure Active Directory](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>Passaggi successivi

- [Scenari di autenticazione per Azure Active Directory](active-directory-authentication-scenarios.md)
- [Aggiungere l'accesso con Microsoft a un'app Web ASP.NET](guidedsetups/active-directory-aspnetwebapp-v1.md)
