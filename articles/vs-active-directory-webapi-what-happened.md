<properties 
	pageTitle="" 
	description="Descrive le modifiche apportate al progetto di Visual Studio dopo l’esecuzione della procedura guidata di Azure Active Directory." 
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

> [AZURE.SELECTOR]
> - [Per iniziare](/documentation/articles/vs-active-directory-webapi-getting-started/)
> - [Risultati](/documentation/articles/vs-active-directory-webapi-what-happened/)

###<span id="whathappened">Che cosa è successo al progetto?</span>

Sono stati aggiunti dei riferimenti. 

#####Riferimenti al pacchetto NuGet 

- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.ActiveDirectory`
- `Microsoft.Owin.Security.Jwt`
- `Microsoft.Owin.Security.OAuth`
- `Newtonsoft.Json`
- `Owin`
- `System.IdentityModel.Tokens.Jwt`

#####Riferimenti a .NET 

- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.ActiveDirectory`
- `Microsoft.Owin.Security.Jwt`
- `Microsoft.Owin.Security.OAuth`
- `Newtonsoft.Json`
- `Owin`
- `System.IdentityModel.Tokens.Jwt` 

#####Sono stati aggiunti file di codice al progetto 

Al progetto è stata aggiunta una classe di avvio di autenticazione **App_Start/Startup.Auth.cs** contenente la logica di avvio per l'autenticazione di Azure AD. 

#####Al progetto è stato aggiunto codice di avvio 

Se nel progetto era già presente una classe Startup, il metodo **Configuration** è stato aggiornato per includere una chiamata a `ConfigureAuth(app)`. In caso contrario, una classe Startup è stata aggiunta al progetto. 


#####Il file app.config o web.config include nuovi valori di configurazione.

Sono state aggiunte le voci di configurazione seguenti. 
	<pre>
	`<appSettings> 
    		<add key="ida:ClientId" value="ClientId from the new Azure AD App" /> 
    		<add key="ida:Tenant" value="Your selected Azure AD Tenant" /> 
    		<add key="ida:Audience" value="The App ID Uri from the wizard" /> 
	</appSettings>` </pre>

###È stata creata un'app Azure AD 

Nella directory selezionata nella procedura guidata è stata creata un'app Azure AD.


[Altre informazioni su Azure Active Directory](http://azure.microsoft.com/services/active-directory/)

<!--HONumber=46--> 
