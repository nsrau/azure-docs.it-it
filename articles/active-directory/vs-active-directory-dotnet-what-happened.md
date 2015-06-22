<properties 
	pageTitle="Introduzione all'autenticazione di Active Directory - Risultati" 
	description="Illustra cosa è successo al progetto Azure Active Directory in Visual Studio." 
	services="active-directory" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor="tglee"/>
  
<tags 
	ms.service="active-directory" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-what-happened" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/02/2015" 
	ms.author="kempb"/>

# Che cosa è successo al progetto?

> [AZURE.SELECTOR]
> - [Guida introduttiva](vs-active-directory-dotnet-getting-started.md)
> - [Risultati](vs-active-directory-dotnet-what-happened.md)

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

Al progetto è stata aggiunta una classe Startup  `App_Start/Startup.Auth.cs` contenente la logica di avvio per l'autenticazione di Azure AD. È stata aggiunta anche una classe Controller, Controllers/AccountController.cs, contenente i metodi  `SignIn()` e  `SignOut()`. È stata infine aggiunta una visualizzazione parziale  `Views/Shared/_LoginPartial.cshtml` contenente un collegamento azione per SignIn/SignOut. 

#####È stato aggiunto codice di avvio al progetto
 
Se nel progetto è già presente una classe Startup, il metodo **Configuration** è stato aggiornato per includere una chiamata a  `ConfigureAuth(app)`. In caso contrario, una classe Startup è stata aggiunta al progetto. 

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

[Ulteriori informazioni su Azure Active Directory](http://azure.microsoft.com/services/active-directory/)

<!--HONumber=49--> 