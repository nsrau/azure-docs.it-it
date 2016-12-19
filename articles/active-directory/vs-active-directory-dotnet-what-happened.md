---
title: "Cosa è successo a un progetto MVC (servizio connesso a Visual Studio Azure Active Directory)? | Documentazione Microsoft"
description: Viene descritto cosa succede al progetto MVC quando ci si connette ad Azure AD mediante i servizi relazionati di Visual Studio.
services: active-directory
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 8b24adde-547e-4ffe-824a-2029ba210216
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 11/18/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: abe30bca0e1d305b5bd0300c63c037ced30be1f5


---
# <a name="what-happened-to-my-mvc-project-visual-studio-azure-active-directory-connected-service"></a>Cosa è successo a un progetto MVC (servizio connesso a Visual Studio Azure Active Directory)?
> [!div class="op_single_selector"]
> * [Per iniziare](vs-active-directory-dotnet-getting-started.md)
> * [Risultati](vs-active-directory-dotnet-what-happened.md)
> 
> 

## <a name="references-have-been-added"></a>Sono stati aggiunti riferimenti
### <a name="nuget-package-references"></a>Riferimenti al pacchetto NuGet
* **Microsoft.IdentityModel.Protocol.Extensions**
* **Microsoft.Owin**
* **Microsoft.Owin.Host.SystemWeb**
* **Microsoft.Owin.Security**
* **Microsoft.Owin.Security.Cookies**
* **Microsoft.Owin.Security.OpenIdConnect**
* **Owin**
* **System.IdentityModel.Tokens.Jwt**

### <a name="net-references"></a>Riferimenti a .NET
* **Microsoft.IdentityModel.Protocol.Extensions**
* **Microsoft.Owin**
* **Microsoft.Owin.Host.SystemWeb**
* **Microsoft.Owin.Security**
* **Microsoft.Owin.Security.Cookies**
* **Microsoft.Owin.Security.OpenIdConnect**
* **Owin**
* **System.IdentityModel**
* **System.IdentityModel.Tokens.Jwt**
* **System.Runtime.Serialization**

## <a name="code-has-been-added"></a>È stato aggiunto codice
### <a name="code-files-were-added-to-your-project"></a>Sono stati aggiunti file di codice al progetto
Al progetto è stata aggiunta una classe di avvio di autenticazione **App_Start/Startup.Auth.cs** contenente la logica di avvio per l'autenticazione di Azure AD. È stata anche aggiunta una classe controller, Controllers/AccountController.cs che include i metodi **SignIn()** e **SignOut()**. È stata infine aggiunta una visualizzazione parziale, **Views/Shared/_LoginPartial.cshtml** che include un collegamento azione per SignIn/SignOut.

### <a name="startup-code-was-added-to-your-project"></a>È stato aggiunto un codice di avvio al progetto
Se nel progetto è già presente una classe Startup, il metodo **Configuration** è stato aggiornato includendo una chiamata a **ConfigureAuth(app)**. In caso contrario, una classe Startup è stata aggiunta al progetto.

### <a name="your-appconfig-or-webconfig-has-new-configuration-values"></a>Il file app.config o web.config include nuovi valori di configurazione
Sono state aggiunte le voci di configurazione seguenti.

    <appSettings>
        <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
        <add key="ida:AADInstance" value="https://login.microsoftonline.com/" />
        <add key="ida:Domain" value="The selected Azure AD Domain" />
        <add key="ida:TenantId" value="The Id of your selected Azure AD Tenant" />
        <add key="ida:PostLogoutRedirectUri" value="Your project start page" />
    </appSettings>

### <a name="an-azure-active-directory-ad-app-was-created"></a>È stata creata un'app Azure Active Directory (AD)
Un'app Azure AD è stata creata nella directory selezionata nella procedura guidata.

## <a name="if-i-checked-disable-individual-user-accounts-authentication-what-additional-changes-were-made-to-my-project"></a>Se è stata selezionata l'opzione *Disabilitare l'autenticazione dell'account utente*, quali altre modifiche sono state apportate al progetto?
Sono stati rimossi i riferimenti del pacchetto NuGet, i file sono stati rimossi e viene eseguito il backup. A seconda dello stato del progetto, è necessario rimuovere riferimenti aggiuntivi o i file manualmente o modificare codice in modo appropriato.

### <a name="nuget-package-references-removed-for-those-present"></a>Riferimenti del pacchetto NuGet rimossi (per coloro che sono presenti)
* **Microsoft.AspNet.Identity.Core**
* **Microsoft.AspNet.Identity.EntityFramework**
* **Microsoft.AspNet.Identity.Owin**

### <a name="code-files-backed-up-and-removed-for-those-present"></a>È stato eseguito il backup dei file di codice e sono stati rimossi (per quelli presenti)
Per ognuno dei seguenti file è stato eseguito il backup e rimosso dal progetto. I File di backup si trovano in una cartella 'Backup' alla radice della directory del progetto.

* **App_Start\IdentityConfig.cs**
* **Controllers\ManageController.cs**
* **Models\IdentityModels.cs**
* **Models\ManageViewModels.cs**

### <a name="code-files-backed-up-for-those-present"></a>Backup dei file di codice (per coloro che presenti)
Per ognuno dei seguenti file è stato eseguito il backup prima della sostituzione. I File di backup si trovano in una cartella 'Backup' alla radice della directory del progetto.

* **Startup.cs**
* **App_Start\Startup.Auth.cs**
* **Controllers\AccountController.cs**
* **Views\Shared\_LoginPartial.cshtml**

## <a name="if-i-checked-read-directory-data-what-additional-changes-were-made-to-my-project"></a>Quali modifiche aggiuntive sono state apportate al progetto dopo aver selezionato *Leggi i dati della directory*?
Sono stati aggiunti riferimenti aggiuntivi.

### <a name="additional-nuget-package-references"></a>Riferimenti al pacchetto NuGet aggiuntivi
* **EntityFramework**
* **Microsoft.Azure.ActiveDirectory.GraphClient**
* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.IdentityModel.Clients.ActiveDirectory**
* **System.Spatial**

### <a name="additional-net-references"></a>Riferimenti .NET aggiuntivi
* **EntityFramework**
* **EntityFramework.SqlServer**
* **Microsoft.Azure.ActiveDirectory.GraphClient**
* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.IdentityModel.Clients.ActiveDirectory**
* **Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms**
* **System.Spatial**

### <a name="additional-code-files-were-added-to-your-project"></a>Sono stati aggiunti altri file di codice al progetto
Per supportare la memorizzazione nella cache token sono stati aggiunti due file: **Models\ADALTokenCache.cs** e **Models\ApplicationDbContext.cs**.  Sono stati aggiunti un controller e una visualizzazione per illustrare l'accesso alle informazioni relative al profilo utente tramite le API Graph di Azure.  Questi file sono **Controllers\UserProfileController.cs** e **Views\UserProfile\Index.cshtml**.

### <a name="additional-startup-code-was-added-to-your-project"></a>È stato aggiunto un altro codice di avvio al progetto
Nel file **startup.auth.cs** è stato aggiunto un nuovo oggetto **OpenIdConnectAuthenticationNotifications** al membro **Notifiche** di **OpenIdConnectAuthenticationOptions**.  Serve per abilitare la ricezione del codice OAuth e scambiarlo con un token di accesso.

### <a name="additional-changes-were-made-to-your-appconfig-or-webconfig"></a>Sono state apportate altre modifiche al file app.config o web.config
Sono state aggiunte le voci di configurazione aggiuntive seguenti.

    <appSettings>
        <add key="ida:ClientSecret" value="Your Azure AD App's new client secret" />
    </appSettings>

Sono state aggiunte le sezioni di configurazione e la stringa di connessione seguenti.

    <configSections>
        <!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
        <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
    </configSections>
    <connectionStrings>
        <add name="DefaultConnection" connectionString="Data Source=(localdb)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\aspnet-[AppName + Generated Id].mdf;Initial Catalog=aspnet-[AppName + Generated Id];Integrated Security=True" providerName="System.Data.SqlClient" />
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


### <a name="your-azure-active-directory-app-was-updated"></a>È stata aggiornata l'app Azure Active Directory
L'app Azure Active Directory è stata aggiornata per includere l'autorizzazione *Leggi i dati della directory* ed è stata creata una chiave aggiuntiva che è stata quindi usata come *ida:ClientSecret* nel file **web.config**.

[Altre informazioni su Azure Active Directory](https://azure.microsoft.com/services/active-directory/)




<!--HONumber=Nov16_HO3-->


