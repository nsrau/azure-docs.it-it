<properties title="Getting Started with Active Directory Authentication - What Happened" pageTitle="Introduzione all'autenticazione di Active Directory - Risultati" metaKeywords="Azure, Getting Started, Active Directory" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />
  
<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="vs-what-happened" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
> - [Per iniziare](/documentation/articles/vs-active-directory-dotnet-getting-started/)
> - [Risultati](/documentation/articles/vs-active-directory-dotnet-what-happened/)

###<span id="whathappened">Che cosa è successo al progetto?</span>
 
Sono stati aggiunti dei riferimenti. 

#####Riferimenti al pacchetto NuGet

- `Microsoft.IdentityModel.Protocol.Extensions`
- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.Cookies`
- `Microsoft.Owin.Security.OpenIdConnect`
- `Owin`
- `System.IdentityModel.Tokens.Jwt`

#####Riferimenti a .NET

- `Microsoft.IdentityModel.Protocol.Extensions`
- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.Cookies`
- `Microsoft.Owin.Security.OpenIdConnect`
- `Owin`
- `System`
- `System.Data`
- `System.Drawing`
- `System.IdentityModel`
- `System.IdentityModel.Tokens.Jwt`
- `System.Runtime.Serialization`

#####Sono stati aggiunti file di codice al progetto 

Al progetto è stata aggiunta una classe di avvio di autenticazione `App_Start/Startup.Auth.cs` contenente la logica di avvio per l'autenticazione di Azure AD. È stata anche aggiunta una classe controller, Controllers/AccountController.cs che include metodi `SignIn()` e `SignOut()`. È stata infine aggiunta una visualizzazione parziale, `Views/Shared/_LoginPartial.cshtml` che include un collegamento azione per SignIn/SignOut. 

#####Al progetto è stato aggiunto codice di avvio
 
Se nel progetto era già presente una classe Startup, il metodo **Configuration** è stato aggiornato per includere una chiamata a `ConfigureAuth(app)`. In caso contrario, una classe Startup è stata aggiunta al progetto. 

#####Il file app.config o web.config include nuovi valori di configurazione 

Sono state aggiunte le voci di configurazione seguenti. 
	<pre>
	`<appSettings>
	    <add key="ida:ClientId" value="ClientId from the new Azure AD App" /> 
	    <add key="ida:Tenant" value="Your selected Azure AD Tenant" /> 
	    <add key="ida:AADInstance" value="https://login.windows.net/{0}" /> 
	    <add key="Ida:PostLogoutRedirectURI" value="Your project start page" /> 
	</appSettings>` </pre>

#####È stata creata un'app Azure Active Directory (AD) 
Nella directory selezionata nella procedura guidata è stata creata un'app Azure AD. 

[Altre informazioni su Azure Active Directory](http://azure.microsoft.com/services/active-directory/)
