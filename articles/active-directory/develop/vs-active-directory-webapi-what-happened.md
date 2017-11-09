---
title: Modifiche apportate a un progetto WebApi quando ci si connette ad Azure AD | Documentazione Microsoft
description: L'articolo descrive l'impatto della connessione ad Azure AD con Visual Studio su un progetto Web Api
services: active-directory
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: 57630aee-26a2-4326-9dbb-ea2a66daa8b0
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 03/01/2017
ms.author: kraigb
ms.custom: aaddev
ms.openlocfilehash: 086e5a9622cad681cd282345d97e0c28ee7de2fa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>Cosa è successo a un progetto WebApi (servizio connesso a Visual Studio Azure Active Directory)?
> [!div class="op_single_selector"]
> * [Per iniziare](vs-active-directory-webapi-getting-started.md)
> * [Risultati](vs-active-directory-webapi-what-happened.md)
> 
> 

## <a name="references-have-been-added"></a>Sono stati aggiunti riferimenti
### <a name="nuget-package-references"></a>Riferimenti al pacchetto NuGet
* `Microsoft.Owin`
* `Microsoft.Owin.Host.SystemWeb`
* `Microsoft.Owin.Security`
* `Microsoft.Owin.Security.ActiveDirectory`
* `Microsoft.Owin.Security.Jwt`
* `Microsoft.Owin.Security.OAuth`
* `Owin`
* `System.IdentityModel.Tokens.Jwt`

### <a name="net-references"></a>Riferimenti a .NET
* `Microsoft.Owin`
* `Microsoft.Owin.Host.SystemWeb`
* `Microsoft.Owin.Security`
* `Microsoft.Owin.Security.ActiveDirectory`
* `Microsoft.Owin.Security.Jwt`
* `Microsoft.Owin.Security.OAuth`
* `Owin`
* `System.IdentityModel.Tokens.Jwt`

## <a name="code-changes"></a>Modifiche al codice
### <a name="code-files-were-added-to-your-project"></a>Sono stati aggiunti file di codice al progetto
Al progetto è stata aggiunta una classe di avvio di autenticazione **App_Start/Startup.Auth.cs** contenente la logica di avvio per l'autenticazione di Azure AD.

### <a name="startup-code-was-added-to-your-project"></a>È stato aggiunto un codice di avvio al progetto
Se nel progetto è già presente una classe Startup, il metodo **Configuration** è stato aggiornato includendo una chiamata a `ConfigureAuth(app)`. In caso contrario, una classe Startup è stata aggiunta al progetto.

### <a name="your-appconfig-or-webconfig-file-has-new-configuration-values"></a>Il file app.config o web.config include nuovi valori di configurazione.
Sono state aggiunte le voci di configurazione seguenti.

```
    <appSettings>
            <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
            <add key="ida:Tenant" value="Your selected Azure AD Tenant" />
            <add key="ida:Audience" value="The App ID Uri from the wizard" />
    </appSettings>`
```

### <a name="an-azure-ad-app-was-created"></a>È stata creata un'app Azure AD
Un'app Azure AD è stata creata nella directory selezionata nella procedura guidata.

[Altre informazioni su Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

## <a name="if-i-checked-disable-individual-user-accounts-authentication-what-additional-changes-were-made-to-my-project"></a>Se è stata selezionata l'opzione *Disabilitare l'autenticazione dell'account utente*, quali altre modifiche sono state apportate al progetto?
Sono stati rimossi i riferimenti del pacchetto NuGet, i file sono stati rimossi e viene eseguito il backup. A seconda dello stato del progetto, è necessario rimuovere riferimenti aggiuntivi o i file manualmente o modificare codice in modo appropriato.

### <a name="nuget-package-references-removed-for-those-present"></a>Riferimenti del pacchetto NuGet rimossi (per coloro che sono presenti)
* `Microsoft.AspNet.Identity.Core`
* `Microsoft.AspNet.Identity.EntityFramework`
* `Microsoft.AspNet.Identity.Owin`

### <a name="code-files-backed-up-and-removed-for-those-present"></a>È stato eseguito il backup dei file di codice e sono stati rimossi (per quelli presenti)
Per ognuno dei seguenti file è stato eseguito il backup e rimosso dal progetto. I File di backup si trovano in una cartella 'Backup' alla radice della directory del progetto.

* `App_Start\IdentityConfig.cs`
* `Controllers\AccountController.cs`
* `Controllers\ManageController.cs`
* `Models\IdentityModels.cs`
* `Providers\ApplicationOAuthProvider.cs`

### <a name="code-files-backed-up-for-those-present"></a>Backup dei file di codice (per coloro che presenti)
Per ognuno dei seguenti file è stato eseguito il backup prima della sostituzione. I File di backup si trovano in una cartella 'Backup' alla radice della directory del progetto.

* `Startup.cs`
* `App_Start\Startup.Auth.cs`

## <a name="if-i-checked-read-directory-data-what-additional-changes-were-made-to-my-project"></a>Quali modifiche aggiuntive sono state apportate al progetto dopo aver selezionato *Leggi i dati della directory*?
### <a name="additional-changes-were-made-to-your-appconfig-or-webconfig"></a>Sono state apportate altre modifiche al file app.config o web.config
Sono state aggiunte le voci di configurazione aggiuntive seguenti.

```
    <appSettings>
        <add key="ida:Password" value="Your Azure AD App's new password" />
    </appSettings>`
```

### <a name="your-azure-active-directory-app-was-updated"></a>È stata aggiornata l'app Azure Active Directory
L'app Azure Active Directory è stata aggiornata per includere l'autorizzazione *Leggi i dati della directory* ed è stata creata una chiave aggiuntiva che è stata quindi usata come *ida:Password* nel file `web.config`.

## <a name="next-steps"></a>Passaggi successivi
- [Altre informazioni su Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

