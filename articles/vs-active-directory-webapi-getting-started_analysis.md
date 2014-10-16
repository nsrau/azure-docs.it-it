<properties title="Getting Started with Active Directory Authentication" pageTitle="" metaKeywords="Azure, Getting Started, Active Directory" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

### Operazione eseguita

Sono stati aggiunti riferimenti al progetto

###### Riferimenti al pacchetto NuGet

Microsoft.Owin, Microsoft.Owin.Host.SystemWeb, Microsoft.Owin.Security, Microsoft.Owin.Security.ActiveDirectory, Microsoft.Owin.Security.Jwt, Microsoft.Owin.Security.OAuth, Newtonsoft.Json, Owin, System.IdentityModel.Tokens.Jwt

###### Riferimenti a .NET

Microsoft.Owin, Microsoft.Owin.Host.SystemWeb, Microsoft.Owin.Security, Microsoft.Owin.Security.ActiveDirectory, Microsoft.Owin.Security.Jwt, Microsoft.Owin.Security.OAuth, Newtonsoft.Json, Owin, System.IdentityModel.Tokens.Jwt

###### Sono stati aggiunti file di codice al progetto

Una classe di avvio di autenticazione, App\_Start/Startup.Auth.cs, è stata aggiunta al progetto contenente logica di avvio per l'autenticazione di Azure AD.

###### È stato aggiunto codice di avvio al progetto

Se nel progetto era già presente una classe Startup, il metodo Configuration() è stato aggiornato per includere una chiamata a ConfigureAuth(app). In caso contrario, una classe Startup è stata aggiunta al progetto.

###### Il file app.config o web.config include nuovi valori di configurazione.

Sono state aggiunte le voci di configurazione seguenti.

     <appSettings> 
    		<add key="ida:ClientId" value="ClientId from the new Azure AD App" /> 
    		<add key="ida:Tenant" value="Your selected Azure AD Tenant" /> 
    		<add key="ida:Audience" value="The App ID Uri from the wizard" /> 
	</appSettings> 

</p>
### È stata creata un'app Azure Active Directory (AD)

Un'app Azure AD è stata creata nella directory selezionata nella procedura guidata.

### Introduzione ad Azure Active Directory (AD)

Di seguito sono illustrate le operazioni che è possibile eseguire con il codice aggiunto.

###### Richiesta di autenticazione ai controller di accesso

A tutti i controller del progetto è stato assegnato l'attributo Authorize. Questo attributo richiede l'autenticazione dell'utente prima dell'accesso alle API definite dai controller. Per permettere l'accesso anonimo al controller, rimuovere l'attributo dal controller. Per configurare le autorizzazioni con un livello di granularità superiore, applicare l'attributo a ogni metodo che necessita di autorizzazione invece di applicarlo alla classe controller.

