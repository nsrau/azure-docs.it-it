---
title: Modifiche apportate a un progetto WebAPI quando ci si connette ad Azure AD | Microsoft Docs
description: L'articolo descrive l'impatto della connessione ad Azure AD con Visual Studio su un progetto WebAPI
services: active-directory
documentationcenter: ''
author: kraigb
manager: ghogen
editor: ''
ms.assetid: 57630aee-26a2-4326-9dbb-ea2a66daa8b0
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: kraigb
ms.custom: aaddev
ms.openlocfilehash: 140f555d28c4d5a923b9c255d8e61d7aea9bb23f
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>Cosa è successo a un progetto WebAPI (servizio connesso di Visual Studio Azure Active Directory)?

> [!div class="op_single_selector"]
> - [Per iniziare](vs-active-directory-webapi-getting-started.md)
> - [Risultati](vs-active-directory-webapi-what-happened.md)

Questo articolo identifica le modifiche esatte apportate ai progetti WebAPI ASP.NET, applicazione a pagina singola ASP.NET e API di Azure ASP.NET quando si aggiunge il [servizio connesso di Active Directory tramite Visual Studio](vs-active-directory-add-connected-service.md). Vale anche per i progetti di servizio mobile di Azure ASP.NET in Visual Studio 2015.

Per informazioni sull'uso del servizio connesso, vedere l'[introduzione](vs-active-directory-webapi-getting-started.md).

## <a name="added-references"></a>Riferimenti aggiunti

Interessa il file di progetto (riferimenti *.NET) e `packages.config` (riferimenti NuGet).

| type | riferimento |
| --- | --- |
| .NET; NuGet | Microsoft.Owin |
| .NET; NuGet | Microsoft.Owin.Host.SystemWeb |
| .NET; NuGet | Microsoft.Owin.Security |
| .NET; NuGet | Microsoft.Owin.Security.ActiveDirectory |
| .NET; NuGet | Microsoft.Owin.Security.Jwt |
| .NET; NuGet | Microsoft.Owin.Security.OAuth |
| .NET; NuGet | Owin |
| .NET; NuGet | System.IdentityModel.Tokens.Jwt |

Riferimenti aggiuntivi se è stata selezionata l'opzione **Lettura dati directory**:

| type | riferimento |
| --- | --- |
| .NET; NuGet | EntityFramework |
| .NET        | EntityFramework.SqlServer (solo Visual Studio 2015) |
| .NET; NuGet | Microsoft.Azure.ActiveDirectory.GraphClient |
| .NET; NuGet | Microsoft.Data.Edm |
| .NET; NuGet | Microsoft.Data.OData |
| .NET; NuGet | Microsoft.Data.Services.Client |
| .NET; NuGet | Microsoft.IdentityModel.Clients.ActiveDirectory |
| .NET        | Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms<br>(solo Visual Studio 2015) |
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
        <add key="ida:Tenant" value="<your selected Azure domain>" />
        <add key="ida:Audience" value="<your selected domain + / + project name>" />
    </appSettings>
    ```

- Solo Visual Studio 2017: è stata aggiunta anche la voce seguente in `<appSettings>`"

    ```xml
    <add key="ida:MetadataAddress" value="<domain URL + /federationmetadata/2007-06/federationmetadata.xml>" />
    ```

- Sono stati aggiunti elementi `<dependentAssembly>` nel nodo `<runtime><assemblyBinding>` per `System.IdentityModel.Tokens.Jwt`.

- Se è stata selezionata l'opzione **Lettura dati directory**, aggiungere la seguente voce di configurazione in `<appSettings>`:

    ```xml
    <add key="ida:Password" value="<Your Azure AD app's new password>" />
    ```

## <a name="code-changes-and-additions"></a>Aggiunte e modifiche al codice

- È stato aggiunto l'attributo `[Authorize]` a `Controllers/ValueController.cs` e ad altri controller esistenti.

- È stata aggiunta o modificata in base alle esigenze una classe di avvio dell'autenticazione denominata `App_Start/Startup.Auth.cs`, contenente la logica di avvio per l'autenticazione di Azure AD. Se è stata selezionata l'opzione **Lettura dati directory**, questo file contiene anche il codice per ricevere un codice OAuth scambiarlo con un token di accesso.

- (Solo Visual Studio 2015 con l'app ASP.NET 4) è stato rimosso `App_Start/IdentityConfig.cs` ed sono stati aggiunti `Controllers/AccountController.cs`, `Models/IdentityModel.cs` e `Providers/ApplicationAuthProvider.cs`.

- È stato aggiunto `Connected Services/AzureAD/ConnectedService.json` (Visual Studio 2017) o `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015), contenente le informazioni usate da Visual Studio per tenere traccia dell'aggiunta del servizio connesso.

### <a name="file-backup-visual-studio-2015"></a>Backup di file (Visual Studio 2015)

Quando si aggiunge il servizio connesso, Visual Studio 2015 esegue il backup dei file modificati e rimossi. Tutti i file interessati vengono salvati nella cartella `Backup/AzureAD`. Visual Studio 2017 non crea backup.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ApplicationOAuthProvider.cs`

## <a name="changes-on-azure"></a>Modifiche in Azure

- È stata creata un'applicazione Azure AD nel dominio selezionato durante l'aggiunta del servizio connesso.
- È stata aggiornata l'app per includere l'autorizzazione **Lettura dati directory** se è stata selezionata l'opzione corrispondente.

[Altre informazioni su Azure Active Directory](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>Passaggi successivi

- [Scenari di autenticazione per Azure Active Directory](active-directory-authentication-scenarios.md)
- [Aggiungere l'accesso con Microsoft a un'app Web ASP.NET](guidedsetups/active-directory-aspnetwebapp-v1.md)