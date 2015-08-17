<properties
	pageTitle="Introduzione all'autenticazione di Active Directory - Risultati"
	description="Illustra cosa è successo al progetto Azure Active Directory in Visual Studio."
	services="active-directory"
	documentationCenter=""
	authors="patshea123"
	manager="douge"
	editor="tglee"/>

<tags
	ms.service="active-directory"
	ms.workload="web"
	ms.tgt_pltfrm="vs-what-happened"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/22/2015"
	ms.author="patshea123"/>

# Che cosa è successo al progetto?

> [AZURE.SELECTOR]
> - [Getting Started](vs-active-directory-dotnet-getting-started.md)
> - [What Happened](vs-active-directory-dotnet-what-happened.md)



##Sono stati aggiunti riferimenti

###Riferimenti al pacchetto NuGet

- `Microsoft.IdentityModel.Protocol.Extensions`
- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.Cookies`
- `Microsoft.Owin.Security.OpenIdConnect`
- `Owin`
- `System.IdentityModel.Tokens.Jwt`

###Riferimenti a .NET

- `Microsoft.IdentityModel.Protocol.Extensions`
- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.Cookies`
- `Microsoft.Owin.Security.OpenIdConnect`
- `Owin`
- `System.IdentityModel`
- `System.IdentityModel.Tokens.Jwt`
- `System.Runtime.Serialization`

##È stato aggiunto codice

###Sono stati aggiunti file di codice al progetto

Al progetto è stata aggiunta una classe Startup di autenticazione, `App_Start/Startup.Auth.cs` contenente la logica di avvio per l'autenticazione di Azure AD. È stata aggiunta anche una classe Controller, Controllers/AccountController.cs che contiene i metodi `SignIn()` e `SignOut()`. È stata infine aggiunta una visualizzazione parziale, `Views/Shared/_LoginPartial.cshtml` contenente un collegamento azione per SignIn/SignOut.

###È stato aggiunto un codice di avvio al progetto

Se nel progetto è già presente una classe Startup, il metodo **Configuration** è stato aggiornato includendo una chiamata a `ConfigureAuth(app)`. In caso contrario, una classe Startup è stata aggiunta al progetto.

###Il file app.config o web.config include nuovi valori di configurazione

Sono state aggiunte le voci di configurazione seguenti. <pre> `<appSettings>
	    <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
	    <add key="ida:AADInstance" value="https://login.microsoftonline.com/" />
	    <add key="ida:Domain" value="The selected Azure AD Domain" />
	    <add key="ida:TenantId" value="The Id of your selected Azure AD Tenant" />
	    <add key="ida:PostLogoutRedirectUri" value="Your project start page" />
	</appSettings>` </pre>

###È stata creata un'app Azure Active Directory (AD)
Un'app Azure AD è stata creata nella directory selezionata nella procedura guidata.

##Se è stata selezionata l'opzione *Disabilitare l'autenticazione dell'account utente*, quali altre modifiche sono state apportate al progetto?
Sono stati rimossi i riferimenti del pacchetto NuGet, i file sono stati rimossi e viene eseguito il backup. A seconda dello stato del progetto, è necessario rimuovere riferimenti aggiuntivi o i file manualmente o modificare codice in modo appropriato.

###Riferimenti del pacchetto NuGet rimossi (per coloro che sono presenti)

- `Microsoft.AspNet.Identity.Core`
- `Microsoft.AspNet.Identity.EntityFramework`
- `Microsoft.AspNet.Identity.Owin`

###È stato eseguito il backup dei file di codice e sono stati rimossi (per quelli presenti)

Per ognuno dei seguenti file è stato eseguito il backup e rimosso dal progetto. I File di backup si trovano in una cartella 'Backup' alla radice della directory del progetto.

- `App_Start\IdentityConfig.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ManageViewModels.cs`

###Backup dei file di codice (per coloro che presenti)

Per ognuno dei seguenti file è stato eseguito il backup prima della sostituzione. I File di backup si trovano in una cartella 'Backup' alla radice della directory del progetto.

- `Startup.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Views\Shared\_LoginPartial.cshtml`

##Quali modifiche aggiuntive sono state apportate al progetto dopo aver selezionato *Leggi i dati della directory*?

Sono stati aggiunti riferimenti aggiuntivi.

###Riferimenti al pacchetto NuGet aggiuntivi

- `EntityFramework`
- `Microsoft.Azure.ActiveDirectory.GraphClient`
- `Microsoft.Data.Edm`
- `Microsoft.Data.OData`
- `Microsoft.Data.Services.Client`
- `Microsoft.IdentityModel.Clients.ActiveDirectory`
- `System.Spatial`

###Riferimenti .NET aggiuntivi

- `EntityFramework`
- `EntityFramework.SqlServer`
- `Microsoft.Azure.ActiveDirectory.GraphClient`
- `Microsoft.Data.Edm`
- `Microsoft.Data.OData`
- `Microsoft.Data.Services.Client`
- `Microsoft.IdentityModel.Clients.ActiveDirectory`
- `Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms`
- `System.Spatial`

###Sono stati aggiunti altri file di codice al progetto

Sono stati aggiunti due file per supportare la memorizzazione dei token nella cache: `Models\ADALTokenCache.cs` e `Models\ApplicationDbContext.cs`. Sono stati aggiunti un controller e una visualizzazione per illustrare l'accesso alle informazioni relative al profilo utente tramite le API Graph di Azure. Questi file sono `Controllers\UserProfileController.cs` e `Views\UserProfile\Index.cshtml`.

###È stato aggiunto un altro codice di avvio al progetto

Nel file `startup.auth.cs` è stato aggiunto un nuovo oggetto `OpenIdConnectAuthenticationNotifications` al membro `Notifications` di `OpenIdConnectAuthenticationOptions`. Serve per abilitare la ricezione del codice OAuth e scambiarlo con un token di accesso.

###Sono state apportate altre modifiche al file app.config o web.config

Sono state aggiunte le voci di configurazione aggiuntive seguenti. <pre> `<appSettings>
	    <add key="ida:ClientSecret" value="Your Azure AD App's new client secret" />
	</appSettings>` </pre>

Sono state aggiunte le sezioni di configurazione e la stringa di connessione seguenti. <pre> `<configSections>
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
	</entityFramework>`</pre>


###È stata aggiornata l'app Azure Active Directory
L'app Azure Active Directory è stata aggiornata per includere l'autorizzazione *Leggi i dati della directory* ed è stata creata una chiave aggiuntiva che è stata quindi usata come *ida:ClientSecret* nel file `web.config`.

[Altre informazioni su Azure Active Directory](http://azure.microsoft.com/services/active-directory/)

<!---HONumber=August15_HO6-->