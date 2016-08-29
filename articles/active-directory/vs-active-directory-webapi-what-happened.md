<properties
	pageTitle="Cosa è successo a un progetto WebApi (servizio connesso a Visual Studio Azure Active Directory)? | Microsoft Azure "
	description="Viene descritto cosa succede al progetto WebApi quando ci si connette ad Azure AD mediante Visual Studio"
  services="active-directory"
	documentationCenter=""
	authors="TomArcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="web"
	ms.tgt_pltfrm="vs-what-happened"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/15/2016"
	ms.author="tarcher"/>

# Cosa è successo a un progetto WebApi (servizio connesso a Visual Studio Azure Active Directory)?

> [AZURE.SELECTOR]
> - [Per iniziare](vs-active-directory-webapi-getting-started.md)
> - [Risultati](vs-active-directory-webapi-what-happened.md)

##Sono stati aggiunti riferimenti

###Riferimenti al pacchetto NuGet

- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.ActiveDirectory`
- `Microsoft.Owin.Security.Jwt`
- `Microsoft.Owin.Security.OAuth`
- `Owin`
- `System.IdentityModel.Tokens.Jwt`

###Riferimenti a .NET

- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.ActiveDirectory`
- `Microsoft.Owin.Security.Jwt`
- `Microsoft.Owin.Security.OAuth`
- `Owin`
- `System.IdentityModel.Tokens.Jwt`

##Modifiche al codice

###Sono stati aggiunti file di codice al progetto

Al progetto è stata aggiunta una classe Startup, **App\_Start/Startup.Auth.cs**, contenente la logica di avvio per l'autenticazione di Azure AD.

###È stato aggiunto un codice di avvio al progetto

Se nel progetto è già presente una classe Startup, il metodo **Configuration** è stato aggiornato includendo una chiamata a `ConfigureAuth(app)`. In caso contrario, una classe Startup è stata aggiunta al progetto.


###Il file app.config o web.config include nuovi valori di configurazione.

Sono state aggiunte le voci di configurazione seguenti.
```
	`<appSettings>
    		<add key="ida:ClientId" value="ClientId from the new Azure AD App" />
    		<add key="ida:Tenant" value="Your selected Azure AD Tenant" />
    		<add key="ida:Audience" value="The App ID Uri from the wizard" />
	</appSettings>`
```

###È stata creata un'app Azure AD

Un'app Azure AD è stata creata nella directory selezionata nella procedura guidata.

[Altre informazioni su Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

##Se è stata selezionata l'opzione *Disabilitare l'autenticazione dell'account utente*, quali altre modifiche sono state apportate al progetto?
Sono stati rimossi i riferimenti del pacchetto NuGet, i file sono stati rimossi e viene eseguito il backup. A seconda dello stato del progetto, è necessario rimuovere riferimenti aggiuntivi o i file manualmente o modificare codice in modo appropriato.

###Riferimenti del pacchetto NuGet rimossi (per coloro che sono presenti)

- `Microsoft.AspNet.Identity.Core`
- `Microsoft.AspNet.Identity.EntityFramework`
- `Microsoft.AspNet.Identity.Owin`

###È stato eseguito il backup dei file di codice e sono stati rimossi (per quelli presenti)

Per ognuno dei seguenti file è stato eseguito il backup e rimosso dal progetto. I File di backup si trovano in una cartella 'Backup' alla radice della directory del progetto.

- `App_Start\IdentityConfig.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Providers\ApplicationOAuthProvider.cs`

###Backup dei file di codice (per coloro che presenti)

Per ognuno dei seguenti file è stato eseguito il backup prima della sostituzione. I File di backup si trovano in una cartella 'Backup' alla radice della directory del progetto.

- `Startup.cs`
- `App_Start\Startup.Auth.cs`

##Quali modifiche aggiuntive sono state apportate al progetto dopo aver selezionato *Leggi i dati della directory*?

###Sono state apportate altre modifiche al file app.config o web.config

Sono state aggiunte le voci di configurazione aggiuntive seguenti.

```
	`<appSettings>
	    <add key="ida:Password" value="Your Azure AD App's new password" />
	</appSettings>`
```

###È stata aggiornata l'app Azure Active Directory
L'app Azure Active Directory è stata aggiornata per includere l'autorizzazione *Leggi i dati della directory* ed è stata creata una chiave aggiuntiva che è stata quindi usata come *ida:Password* nel file `web.config`.

[Altre informazioni su Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

<!---HONumber=AcomDC_0817_2016-->