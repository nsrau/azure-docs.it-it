<properties title="Getting Started with Active Directory Authentication" pageTitle="" metaKeywords="Azure, Getting Started, Active Directory" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

### Operazione eseguita

Sono stati aggiunti riferimenti al progetto

###### Riferimenti al pacchetto NuGet

Microsoft.IdentityModel.Protocol.Extensions, Microsoft.Owin, Microsoft.Owin.Host.SystemWeb, Microsoft.Owin.Security, Microsoft.Owin.Security.Cookies, Microsoft.Owin.Security.OpenIdConnect, Owin, System.IdentityModel.Tokens.Jwt

###### Riferimenti a .NET

Microsoft.IdentityModel.Protocol.Extensions, Microsoft.Owin, Microsoft.Owin.Host.SystemWeb, Microsoft.Owin.Security, Microsoft.Owin.Security.Cookies, Microsoft.Owin.Security.OpenIdConnect, Owin, System, System.Data, System.Drawing, System.IdentityModel, System.IdentityModel.Tokens.Jwt, System.Runtime.Serialization

###### Sono stati aggiunti file di codice al progetto

Una classe di avvio di autenticazione, App\_Start/Startup.Auth.cs, è stata aggiunta al progetto contenente logica di avvio per l'autenticazione di Azure AD. È stata anche aggiunta una classe controller, Controllers/AccountController.cs che include metodi SignIn() e SignOut(). È stata infine aggiunta una visualizzazione parziale, Views/Shared/\_LoginPartial.cshtml che include un collegamento azione per SignIn/SignOut.

###### È stato aggiunto codice di avvio al progetto

Se nel progetto era già presente una classe Startup, il metodo Configuration() è stato aggiornato per includere una chiamata a ConfigureAuth(app). In caso contrario, una classe Startup è stata aggiunta al progetto.

###### Il file app.config o web.config include nuovi valori di configurazione

Sono state aggiunte le voci di configurazione seguenti.

   	<appSettings>
	    <add key="ida:ClientId" value="ClientId from the new Azure AD App" /> 
	    <add key="ida:Tenant" value="Your selected Azure AD Tenant" /> 
	    <add key="ida:AADInstance" value="https://login.windows.net/{0}" /> 
	    <add key="Ida:PostLogoutRedirectURI" value="Your project start page" /> 
	</appSettings>

</p>
###### È stata creata un'app Azure Active Directory (AD)

Un'app Azure AD è stata creata nella directory selezionata nella procedura guidata.

## Introduzione ad Azure Active Directory (AD)

Di seguito sono illustrate le operazioni che è possibile eseguire con il codice aggiunto.

###### Richiesta di autenticazione ai controller di accesso

A tutti i controller del progetto è stato assegnato l'attributo [Authorize]. Questo attributo richiede l'autenticazione dell'utente prima dell'accesso ai controller. Per permettere l'accesso anonimo al controller, rimuovere l'attributo dal controller.

###### Aggiunta di controlli SignIn/SignOut

Per aggiungere i controlli SignIn/SignOut alla visualizzazione, è possibile usare la visualizzazione parziale \_LoginPartial.cshtml per aggiungere la funzionalità a una delle visualizzazioni. L'esempio seguente illustra l'aggiunta di funzionalità alla visualizzazione \_Layout.cshtml standard:

     <!DOCTYPE html> 
	<html> 
	<head> 
	    <meta charset="utf-8" /> 
	    <meta name="viewport" content="width=device-width, initial-scale=1.0"> 
	    <title>@ViewBag.Title - My ASP.NET Application</title> 
	    @Styles.Render("~/Content/css") 
	    @Scripts.Render("~/bundles/modernizr") 
	</head> 
	<body> 
	    <div class="navbar navbar-inverse navbar-fixed-top"> 
	        <div class="container"> 
	            <div class="navbar-header"> 
	                <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse"> 
	                    <span class="icon-bar"></span> 
	                    <span class="icon-bar"></span> 
	                    <span class="icon-bar"></span> 
	                </button> 
	                @Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" }) 
	            </div> 
	            <div class="navbar-collapse collapse"> 
	                <ul class="nav navbar-nav"> 
	                    <li>@Html.ActionLink("Home", "Index", "Home")</li> 
	                    <li>@Html.ActionLink("About", "About", "Home")</li> 
	                    <li>@Html.ActionLink("Contact", "Contact", "Home")</li> 
	                </ul> 
	                @Html.Partial("_LoginPartial") 
	            </div> 
	        </div> 
	    </div> 
	    <div class="container body-content"> 
	        @RenderBody() 
	        <hr /> 
	        <footer> 
	            <p>&copy; @DateTime.Now.Year - My ASP.NET Application</p> 
	        </footer> 
	    </div> 
	    @Scripts.Render("~/bundles/jquery") 
	    @Scripts.Render("~/bundles/bootstrap") 
	    @RenderSection("scripts", required: false) 
	</body> 
	</html> 

</p>

