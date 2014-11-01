<properties title="Introduzione all'autenticazione di Active Directory" pageTitle="" metaKeywords="Azure, Getting Started, Active Directory" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

> [AZURE.SELECTOR]
>
> -   [Introduzione][Introduzione]
> -   [Risultati][Risultati]

### <span id="whathappened">Che cosa è successo al progetto?</span>

Sono stati aggiunti dei riferimenti.

##### Riferimenti al pacchetto NuGet

-   `Microsoft.Owin`
-   `Microsoft.Owin.Host.SystemWeb`
-   `Microsoft.Owin.Security`
-   `Microsoft.Owin.Security.ActiveDirectory`
-   `Microsoft.Owin.Security.Jwt`
-   `Microsoft.Owin.Security.OAuth`
-   `Newtonsoft.Json`
-   `Owin`
-   `System.IdentityModel.Tokens.Jwt`

##### Riferimenti a .NET

-   `Microsoft.Owin`
-   `Microsoft.Owin.Host.SystemWeb`
-   `Microsoft.Owin.Security`
-   `Microsoft.Owin.Security.ActiveDirectory`
-   `Microsoft.Owin.Security.Jwt`
-   `Microsoft.Owin.Security.OAuth`
-   `Newtonsoft.Json`
-   `Owin`
-   `System.IdentityModel.Tokens.Jwt`

##### Sono stati aggiunti file di codice al progetto

Al progetto è stata aggiunta una classe Startup, **App\_Start/Startup.Auth.cs**, contenente la logica di avvio per l'autenticazione di Azure AD.

##### È stato aggiunto codice di avvio al progetto

Se nel progetto è già presente una classe Startup, il metodo **Configuration** è stato aggiornato per includere una chiamata a `ConfigureAuth(app)`. In caso contrario, una classe Startup è stata aggiunta al progetto.

##### Il file app.config o web.config include nuovi valori di configurazione.

Sono state aggiunte le voci di configurazione seguenti.

     <appSettings>              <add key="ida:ClientId" value="ClientId from the new Azure AD App" />              <add key="ida:Tenant" value="Your selected Azure AD Tenant" />              <add key="ida:Audience" value="The App ID Uri from the wizard" />      </appSettings> 

</p>
### È stata creata un'app Azure AD

Un'app Azure AD è stata creata nella directory selezionata nella procedura guidata.

[Ulteriori informazioni su Azure Active Directory][Ulteriori informazioni su Azure Active Directory]

  [Introduzione]: /documentation/articles/vs-active-directory-webapi-getting-started/
  [Risultati]: /documentation/articles/vs-active-directory-webapi-what-happened/
  [Ulteriori informazioni su Azure Active Directory]: http://azure.microsoft.com/services/active-directory/
