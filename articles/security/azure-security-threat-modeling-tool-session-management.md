---
title: 'Gestione della sessione: Microsoft Threat Modeling Tool - Azure | Microsoft Docs'
description: Procedure di mitigazione delle minacce esposte in Threat Modeling Tool
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 56471d8ef68eacacb3ecebad5056d7e7a9f3ca40
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2017
---
# <a name="security-frame-session-management--articles"></a>Infrastruttura di sicurezza: gestione della sessione - Articoli 
| Prodotto o servizio | Articolo |
| --------------- | ------- |
| **Azure AD**    | <ul><li>[Implementare la disconnessione appropriata con i metodi ADAL quando si usa Azure AD](#logout-adal)</li></ul> |
| Dispositivo IoT | <ul><li>[Usare durate limitate per i token di firma di accesso condiviso generati](#finite-tokens)</li></ul> |
| **Azure Document DB** | <ul><li>[Usare durate minime per i token delle risorse generati](#resource-tokens)</li></ul> |
| **AD FS** | <ul><li>[Implementare la disconnessione appropriata con i metodi WsFederation quando si usa AD FS](#wsfederation-logout)</li></ul> |
| **Identity Server** | <ul><li>[Implementare la disconnessione appropriata quando si usa Identity Server](#proper-logout)</li></ul> |
| **Applicazione Web** | <ul><li>[Le applicazioni disponibili tramite HTTPS devono usare cookie sicuri](#https-secure-cookies)</li><li>[Tutte le applicazioni basate su http devono specificare solo http per la definizione dei cookie](#cookie-definition)</li><li>[Mitigare il rischio di attacchi basati su richieste intersito false nelle pagine Web ASP.NET](#csrf-asp)</li><li>[Configurare una sessione per la durata dell'inattività](#inactivity-lifetime)</li><li>[Implementare la disconnessione appropriata dall'applicazione](#proper-app-logout)</li></ul> |
| **API Web** | <ul><li>[Mitigare il rischio di attacchi basati su richieste intersito false nelle API Web ASP.NET](#csrf-api)</li></ul> |

## <a id="logout-adal"></a>Implementare la disconnessione appropriata con i metodi ADAL quando si usa Azure AD

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Azure AD | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| **Passaggi** | Se l'applicazione si basa su un token di accesso rilasciato da Azure AD, il gestore eventi di disconnessione eseguirà la chiamata. |

### <a name="example"></a>Esempio
```C#
HttpContext.GetOwinContext().Authentication.SignOut(OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType)
```

### <a name="example"></a>Esempio
Eliminerà anche definitivamente la sessione dell'utente chiamando il metodo Session.Abandon(). Il metodo seguente illustra l'implementazione sicura della disconnessione utente:
```C#
    [HttpPost]
        [ValidateAntiForgeryToken]
        public void LogOff()
        {
            string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
            AuthenticationContext authContext = new AuthenticationContext(Authority + TenantId, new NaiveSessionCache(userObjectID));
            authContext.TokenCache.Clear();
            Session.Clear();
            Session.Abandon();
            Response.SetCookie(new HttpCookie("ASP.NET_SessionId", string.Empty));
            HttpContext.GetOwinContext().Authentication.SignOut(
                OpenIdConnectAuthenticationDefaults.AuthenticationType,
                CookieAuthenticationDefaults.AuthenticationType);
        } 
```

## <a id="finite-tokens"></a>Usare durate limitate per i token di firma di accesso condiviso generati

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Dispositivo IoT | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| **Passaggi** | I token di firma di accesso condiviso generati per l'autenticazione all'hub IoT di Azure devono avere un periodo di validità limitato. Impostare durate minime per i token di firma di accesso condiviso per limitare il periodo di tempo in cui i token possono essere riprodotti nel caso in cui vengano compromessi.|

## <a id="resource-tokens"></a>Usare durate minime per i token delle risorse generati

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Azure DocumentDB | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| **Passaggi** | Ridurre l'intervallo di tempo del token della risorsa a un valore minimo necessario. I token delle risorse hanno un intervallo di tempo valido predefinito di 1 ora.|

## <a id="wsfederation-logout"></a>Implementare la disconnessione appropriata con i metodi WsFederation quando si usa AD FS

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | AD FS | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| **Passaggi** | Se l'applicazione si basa sul token del servizio token di sicurezza rilasciato da AD FS, il gestore eventi di disconnessione chiamerà il metodo WSFederationAuthenticationModule.FederatedSignOut() per disconnettere l'utente. Verrà anche eliminata definitivamente la sessione corrente e il valore del token della sessione verrà reimpostato e reso null.|

### <a name="example"></a>Esempio
```C#
        [HttpPost, ValidateAntiForgeryToken]
        [Authorization]
        public ActionResult SignOut(string redirectUrl)
        {
            if (!this.User.Identity.IsAuthenticated)
            {
                return this.View("LogOff", null);
            }

            // Removes the user profile.
            this.Session.Clear();
            this.Session.Abandon();
            HttpContext.Current.Response.Cookies.Add(new System.Web.HttpCookie("ASP.NET_SessionId", string.Empty)
                {
                    Expires = DateTime.Now.AddDays(-1D),
                    Secure = true,
                    HttpOnly = true
                });

            // Signs out at the specified security token service (STS) by using the WS-Federation protocol.
            Uri signOutUrl = new Uri(FederatedAuthentication.WSFederationAuthenticationModule.Issuer);
            Uri replyUrl = new Uri(FederatedAuthentication.WSFederationAuthenticationModule.Realm);
            if (!string.IsNullOrEmpty(redirectUrl))
            {
                replyUrl = new Uri(FederatedAuthentication.WSFederationAuthenticationModule.Realm + redirectUrl);
            }
           //     Signs out of the current session and raises the appropriate events.
            var authModule = FederatedAuthentication.WSFederationAuthenticationModule;
            authModule.SignOut(false);
        //     Signs out at the specified security token service (STS) by using the WS-Federation
        //     protocol.            
            WSFederationAuthenticationModule.FederatedSignOut(signOutUrl, replyUrl);
            return new RedirectResult(redirectUrl);
        }
```

## <a id="proper-logout"></a>Implementare la disconnessione appropriata quando si usa Identity Server

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Identity Server | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | [IdentityServer3: disconnessione federata](https://identityserver.github.io/Documentation/docsv2/advanced/federated-signout.html) |
| **Passaggi** | Identity Server supporta la federazione con i provider di identità eterni. Quando un utente si disconnette da un provider di identità upstream, a seconda del protocollo usato, si potrebbe ricevere una notifica quando l'utente si disconnette. Ciò consente quindi a Identity Server di inviare una notifica ai client che potranno disconnettere l'utente a loro volta. Controllare la documentazione nella sezione Riferimenti per i dettagli sull'implementazione.|

## <a id="https-secure-cookies"></a>Le applicazioni disponibili tramite HTTPS devono usare cookie sicuri

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web. | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | Tipo di ambiente: locale |
| **Riferimenti**              | [Elemento httpCookies (schema delle impostazioni ASP.NET)](http://msdn.microsoft.com/library/ms228262(v=vs.100).aspx), [Proprietà HttpCookie.Secure](http://msdn.microsoft.com/library/system.web.httpcookie.secure.aspx) |
| **Passaggi** | I cookie in genere sono accessibili solo per il dominio per il cui ambito sono stati definiti. La definizione di "dominio" purtroppo non include il protocollo, quindi i cookie creati tramite HTTPS sono accessibile tramite HTTP. L'attributo "secure" indica al browser che il cookie deve essere disponibile solo tramite HTTPS. Assicurarsi che tutti i cookie impostati tramite HTTPS usino l'attributo **secure**. Il requisito può essere applicato nel file web.config impostando l'attributo requireSSL su true. Si tratta dell'approccio da preferire perché applicherà l'attributo **secure** per tutti i cookie correnti e futuri senza dover apportare altre modifiche al codice.|

### <a name="example"></a>Esempio
```C#
<configuration>
  <system.web>
    <httpCookies requireSSL="true"/>
  </system.web>
</configuration>
```
L'impostazione viene applicata anche se viene usato HTTP per accedere all'applicazione. Se viene usato HTTP per accedere all'applicazione, l'impostazione interrompe l'applicazione perché i cookie vengono impostati con l'attributo secure e il browser non li invierà di nuovo all'applicazione.

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web. | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Web Form, MVC 5 |
| **Attributes (Attributi) (Attributi)**              | Tipo di ambiente: locale |
| **Riferimenti**              | N/D  |
| **Passaggi** | Quando l'applicazione Web è la relying party e l'IdP è il server AD FS, l'attributo secure del token FedAuth può essere configurato impostando requireSSL su True nella sezione `system.identityModel.services` di web.config:|

### <a name="example"></a>Esempio
```C#
  <system.identityModel.services>
    <federationConfiguration>
      <!-- Set requireSsl=true; domain=application domain name used by FedAuth cookies (Ex: .gdinfra.com); -->
      <cookieHandler requireSsl="true" persistentSessionLifetime="0.0:20:0" />
    ....  
    </federationConfiguration>
  </system.identityModel.services>
```

## <a id="cookie-definition"></a>Tutte le applicazioni basate su http devono specificare solo http per la definizione dei cookie

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web. | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | [Secure Cookie Attribute](https://en.wikipedia.org/wiki/HTTP_cookie#Secure_cookie) (Attributo dei cookie Secure) |
| **Passaggi** | Per mitigare il rischio di diffusione di informazioni con un attacco di tipo cross-site scripting (XSS), un nuovo attributo (httpOnly) è stato introdotto nei cookie ed è supportato da tutti i principali browser. L'attributo specifica che un cookie non è accessibile tramite script. Usando i cookie HttpOnly, un'applicazione Web riduce la possibilità che le informazioni sensibili contenute nel cookie possano essere sottratte tramite script e inviate al sito Web di un utente malintenzionato. |

### <a name="example"></a>Esempio
Tutte le applicazioni basate su HTTP che usano cookie devono specificare HttpOnly nella definizione del cookie, implementando la configurazione seguente in web.config:
```XML
<system.web>
.
.
   <httpCookies requireSSL="false" httpOnlyCookies="true"/>
.
.
</system.web>
```

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web. | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Web Form |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | [Proprietà FormsAuthentication.RequireSSL](https://msdn.microsoft.com/library/system.web.security.formsauthentication.requiressl.aspx) |
| **Passaggi** | Il valore della proprietà RequireSSL viene impostato nel file di configurazione per un'applicazione ASP.NET usando l'attributo requireSSL dell'elemento di configurazione. È possibile specificare nel file Web.config per l'applicazione ASP.NET se SSL (Secure Sockets Layer) è necessario per restituire il cookie di autenticazione basata su form al server impostando l'attributo requireSSL.|

### <a name="example"></a>Esempio 
L'esempio di codice seguente imposta l'attributo requireSSL nel file Web.config.
```XML
<authentication mode="Forms">
  <forms loginUrl="member_login.aspx" cookieless="UseCookies" requireSSL="true"/>
</authentication>
```

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web. | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | MVC 5 |
| **Attributes (Attributi) (Attributi)**              | Tipo di ambiente: locale |
| **Riferimenti**              | [Windows Identity Foundation (WIF) Configuration – Part II](https://blogs.msdn.microsoft.com/alikl/2011/02/01/windows-identity-foundation-wif-configuration-part-ii-cookiehandler-chunkedcookiehandler-customcookiehandler/) (Configurazione di Windows Identity Foundation (WIF): parte II) |
| **Passaggi** | Per impostare l'attributo httpOnly per i cookie FedAuth, il valore dell'attributo hideFromCsript deve essere impostato su True. |

### <a name="example"></a>Esempio
La configurazione seguente illustra la configurazione corretta:
```XML
<federatedAuthentication>
<cookieHandler mode="Custom"
                       hideFromScript="true"
                       name="FedAuth"
                       path="/"
                       requireSsl="true"
                       persistentSessionLifetime="25">
</cookieHandler>
</federatedAuthentication>
```

## <a id="csrf-asp"></a>Mitigare il rischio di attacchi basati su richieste intersito false nelle pagine Web ASP.NET

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web. | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| **Passaggi** | Richiesta intersito falsa (CSRF o XSRF) è un tipo di attacco in cui un utente malintenzionato può eseguire azioni nel contesto di protezione della sessione stabilita di un utente diverso in un sito web. L'obiettivo è quello di modificare o eliminare il contenuto, se il sito web di destinazione si affida esclusivamente ai cookie di sessione per autenticare la richiesta ricevuta. Un utente malintenzionato potrebbe sfruttare questa vulnerabilità acquisendo il browser di un altro utente per caricare un URL con un comando da un sito vulnerabile a cui l'utente è già connesso. Un utente malintenzionato può raggiungere questo scopo in diversi modi, ad esempio ospitando un sito Web diverso che carica una risorsa dal server vulnerabile o spingendo l'utente a fare clic su un collegamento. L'attacco può essere evitato se il server invia un token aggiuntivo al client, richiede al client di includere tale token in tutte le richieste future e verifica che tutte le richieste future includano un token relativo alla sessione corrente, ad esempio usando AntiForgeryToken o ViewState di ASP.NET. |

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web. | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | MVC 5, MVC 6 |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | [XSRF/CSRF Prevention in ASP.NET MVC and Web Pages](http://www.asp.net/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) (Prevenzione delle richieste intersito false in ASP.NET MVC e nelle pagine Web ASP.NET) |
| **Passaggi** | Form ASP.NET MVC e contro le richieste intersito: usare il metodo helper `AntiForgeryToken` in Views. Inserire `Html.AntiForgeryToken()` nel form, ad esempio,|

### <a name="example"></a>Esempio
```C#
@using (Html.BeginForm("UserProfile", "SubmitUpdate")) { 
    @Html.ValidationSummary(true) 
    @Html.AntiForgeryToken()
    <fieldset> 
```

### <a name="example"></a>Esempio
```C#
<form action="/UserProfile/SubmitUpdate" method="post">
    <input name="__RequestVerificationToken" type="hidden" value="saTFWpkKN0BYazFtN6c4YbZAmsEwG0srqlUqqloi/fVgeV2ciIFVmelvzwRZpArs" />
    <!-- rest of form goes here -->
</form>
```

### <a name="example"></a>Esempio
Html.AntiForgeryToken() assegna contemporaneamente al visitatore un cookie denominato __RequestVerificationToken, con lo stesso valore del valore nascosto casuale illustrato sopra. Per convalidare un post del form in ingresso, aggiungere quindi il filtro [ValidateAntiForgeryToken] al metodo azione di destinazione. ad esempio:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Filtro di autorizzazione che controlla che:
* La richiesta in ingresso abbia un cookie denominato __RequestVerificationToken.
* La richiesta in ingresso ha un cookie `Request.Form` denominato __RequestVerificationToken
* Questi valori del cookie e di `Request.Form` corrispondono. Presumendo che sia tutto a posto, la richiesta viene completata normalmente. In caso contrario, si verifica un errore di autorizzazione e viene visualizzato il messaggio "Token antifalsificazione non specificato o non valido". 

### <a name="example"></a>Esempio
Prevenzione di richieste intersito false e AJAX: il token del form può rappresentare un problema per le richieste AJAX, perché una richiesta AJAX potrebbe inviare dati JSON, non dati in formato HTML. Una soluzione consiste nell'inviare i token in un'intestazione HTTP personalizzata. Il codice seguente usa la sintassi Razor per generare i token e quindi li aggiunge a una richiesta AJAX. 
```C#
<script>
    @functions{
        public string TokenHeaderValue()
        {
            string cookieToken, formToken;
            AntiForgery.GetTokens(null, out cookieToken, out formToken);
            return cookieToken + ":" + formToken;                
        }
    }

    $.ajax("api/values", {
        type: "post",
        contentType: "application/json",
        data: {  }, // JSON data goes here
        dataType: "json",
        headers: {
            'RequestVerificationToken': '@TokenHeaderValue()'
        }
    });
</script>
```

### <a name="example"></a>Esempio
Quando si elabora la richiesta, estrarre i token dall'intestazione della richiesta. Chiamare quindi il metodo AntiForgery.Validate per convalidare i token. Il metodo Validate genera un'eccezione se i token non sono validi.
```C#
void ValidateRequestHeader(HttpRequestMessage request)
{
    string cookieToken = "";
    string formToken = "";

    IEnumerable<string> tokenHeaders;
    if (request.Headers.TryGetValues("RequestVerificationToken", out tokenHeaders))
    {
        string[] tokens = tokenHeaders.First().Split(':');
        if (tokens.Length == 2)
        {
            cookieToken = tokens[0].Trim();
            formToken = tokens[1].Trim();
        }
    }
    AntiForgery.Validate(cookieToken, formToken);
}
```

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web. | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Web Form |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | [Take Advantage of ASP.NET Built-in Features to Fend Off Web Attacks](https://msdn.microsoft.com/library/ms972969.aspx#securitybarriers_topic2) (Sfruttare le funzionalità predefinite di ASP.NET per respingere gli attacchi Web) |
| **Passaggi** | Gli attacchi basati su richieste intersito false nelle applicazioni basate su Web Form possono essere mitigati impostando ViewStateUserKey su una stringa casuale diversa per ogni utente (ID utente o, meglio ancora, ID sessione). Per diversi motivi tecnici e legati ai social network, l'ID sessione è molto più appropriato perché non è prevedibile, scade ed è diverso per ogni utente.|

### <a name="example"></a>Esempio
Ecco il codice che è necessario avere in tutte le pagine:
```C#
void Page_Init (object sender, EventArgs e) {
   ViewStateUserKey = Session.SessionID;
   :
}
```

## <a id="inactivity-lifetime"></a>Configurare una sessione per la durata dell'inattività

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web. | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | [Proprietà HttpSessionState.Timeout](https://msdn.microsoft.com/library/system.web.sessionstate.httpsessionstate.timeout(v=vs.110).aspx) |
| **Passaggi** | Il timeout della sessione rappresenta l'evento che si verifica quando un utente non esegue alcuna azione in un sito Web durante un intervallo (definito dal server Web). L'evento sul lato server imposta lo stato della sessione utente come "non valida" (ad esempio, "non più usata") e indica al server Web di eliminarla definitivamente (eliminando tutti i dati contenuti). L'esempio di codice seguente imposta l'attributo del timeout della sessione su 15 minuti nel file Web.config.|

### <a name="example"></a>Esempio
```Codice XML <configuration> <system.web> <sessionState mode="InProc" cookieless="true" timeout="15" /> </system.web> </configuration>
```

## <a id="threat-detection"></a>Enable Threat detection on Azure SQL
```

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web. | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Web Form |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | [Elemento forms per authentication (schema delle impostazioni ASP.NET)](https://msdn.microsoft.com/library/1d3t3c61(v=vs.100).aspx) |
| **Passaggi** | Impostare il timeout del cookie del ticket di autenticazione basata su form su 15 minuti.|

### <a name="example"></a>Esempio
```Codice XML <forms  name=".ASPXAUTH" loginUrl="login.aspx"  defaultUrl="default.aspx" protection="All" timeout="15" path="/" requireSSL="true" slidingExpiration="true"/>
</forms>
```

| Title                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Web Application | 
| **SDL Phase**               | Build |  
| **Applicable Technologies** | Web Forms, MVC5 |
| **Attributes**              | EnvironmentType - OnPrem |
| **References**              | [asdeqa](https://skf.azurewebsites.net/Mitigations/Details/wefr) |
| **Steps** | When the web application is Relying Party and ADFS is the STS, the lifetime of the authentication cookies - FedAuth tokens - can be set by the following configuration in web.config:|

### Example
```XML
  <system.identityModel.services>
    <federationConfiguration>
      <!-- Set requireSsl=true; domain=application domain name used by FedAuth cookies (Ex: .gdinfra.com); -->
      <cookieHandler requireSsl="true" persistentSessionLifetime="0.0:15:0" />
      <!-- Set requireHttps=true; -->
      <wsFederation passiveRedirectEnabled="true" issuer="http://localhost:39529/" realm="https://localhost:44302/" reply="https://localhost:44302/" requireHttps="true"/>
      <!--
      Use the code below to enable encryption-decryption of claims received from ADFS. Thumbprint value varies based on the certificate being used.
      <serviceCertificate>
        <certificateReference findValue="4FBBBA33A1D11A9022A5BF3492FF83320007686A" storeLocation="LocalMachine" storeName="My" x509FindType="FindByThumbprint" />
      </serviceCertificate>
      -->
    </federationConfiguration>
  </system.identityModel.services>
```

### <a name="example"></a>Esempio
Anche la durata del token dell'attestazione SAML rilasciato da AD FS deve essere impostato su 15 minuti, eseguendo il comando di PowerShell seguente nel server AD FS:
```C#
Set-ADFSRelyingPartyTrust -TargetName “<RelyingPartyWebApp>” -ClaimsProviderName @(“Active Directory”) -TokenLifetime 15 -AlwaysRequireAuthentication $true
```

## <a id="proper-app-logout"></a>Implementare la disconnessione appropriata dall'applicazione

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web. | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| **Passaggi** | Eseguire la disconnessione appropriata dall'applicazione, quando l'utente fa clic sul pulsante di disconnessione. Durante la disconnessione, l'applicazione deve eliminare definitivamente la sessione dell'utente e anche reimpostare e rendere null il valore del cookie della sessione, oltre a reimpostare e rendere null il valore del cookie di autenticazione. Quando più sessioni sono collegate a una singola identità utente, è anche necessario terminarle collettivamente sul lato server al momento del timeout o della disconnessione. Assicurarsi infine che la funzionalità di disconnessione sia disponibile in ogni pagina. |

## <a id="csrf-api"></a>Mitigare il rischio di attacchi basati su richieste intersito false nelle API Web ASP.NET

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| **Passaggi** | Richiesta intersito falsa (CSRF o XSRF) è un tipo di attacco in cui un utente malintenzionato può eseguire azioni nel contesto di protezione della sessione stabilita di un utente diverso in un sito web. L'obiettivo è quello di modificare o eliminare il contenuto, se il sito web di destinazione si affida esclusivamente ai cookie di sessione per autenticare la richiesta ricevuta. Un utente malintenzionato potrebbe sfruttare questa vulnerabilità acquisendo il browser di un altro utente per caricare un URL con un comando da un sito vulnerabile a cui l'utente è già connesso. Un utente malintenzionato può raggiungere questo scopo in diversi modi, ad esempio ospitando un sito Web diverso che carica una risorsa dal server vulnerabile o spingendo l'utente a fare clic su un collegamento. L'attacco può essere evitato se il server invia un token aggiuntivo al client, richiede al client di includere tale token in tutte le richieste future e verifica che tutte le richieste future includano un token relativo alla sessione corrente, ad esempio usando AntiForgeryToken o ViewState di ASP.NET. |

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | MVC 5, MVC 6 |
| **Attributes (Attributi) (Attributi)**              | N/D  |
| **Riferimenti**              | [Preventing Cross-Site Request Forgery (CSRF) Attacks in ASP.NET Web API](http://www.asp.net/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks) (Prevenzione di attacchi basati su richieste intersito false nelle API Web ASP.NET) |
| **Passaggi** | Prevenzione di richieste intersito false e AJAX: il token del form può rappresentare un problema per le richieste AJAX, perché una richiesta AJAX potrebbe inviare dati JSON, non dati in formato HTML. Una soluzione consiste nell'inviare i token in un'intestazione HTTP personalizzata. Il codice seguente usa la sintassi Razor per generare i token e quindi li aggiunge a una richiesta AJAX. |

### <a name="example"></a>Esempio
```Javascript
<script>
    @functions{
        public string TokenHeaderValue()
        {
            string cookieToken, formToken;
            AntiForgery.GetTokens(null, out cookieToken, out formToken);
            return cookieToken + ":" + formToken;                
        }
    }
    $.ajax("api/values", {
        type: "post",
        contentType: "application/json",
        data: {  }, // JSON data goes here
        dataType: "json",
        headers: {
            'RequestVerificationToken': '@TokenHeaderValue()'
        }
    });
</script>
```

### <a name="example"></a>Esempio
Quando si elabora la richiesta, estrarre i token dall'intestazione della richiesta. Chiamare quindi il metodo AntiForgery.Validate per convalidare i token. Il metodo Validate genera un'eccezione se i token non sono validi.
```C#
void ValidateRequestHeader(HttpRequestMessage request)
{
    string cookieToken = "";
    string formToken = "";

    IEnumerable<string> tokenHeaders;
    if (request.Headers.TryGetValues("RequestVerificationToken", out tokenHeaders))
    {
        string[] tokens = tokenHeaders.First().Split(':');
        if (tokens.Length == 2)
        {
            cookieToken = tokens[0].Trim();
            formToken = tokens[1].Trim();
        }
    }
    AntiForgery.Validate(cookieToken, formToken);
}
```

### <a name="example"></a>Esempio
Form ASP.NET MVC e contro le richieste intersito: usare il metodo helper AntiForgeryToken in Views. Inserire Html.AntiForgeryToken() nel form, ad esempio,
```C#
@using (Html.BeginForm("UserProfile", "SubmitUpdate")) { 
    @Html.ValidationSummary(true) 
    @Html.AntiForgeryToken()
    <fieldset> 
}
```

### <a name="example"></a>Esempio
Nell'esempio precedente verrà generato un output simile al seguente:
```C#
<form action="/UserProfile/SubmitUpdate" method="post">
    <input name="__RequestVerificationToken" type="hidden" value="saTFWpkKN0BYazFtN6c4YbZAmsEwG0srqlUqqloi/fVgeV2ciIFVmelvzwRZpArs" />
    <!-- rest of form goes here -->
</form>
```

### <a name="example"></a>Esempio
Html.AntiForgeryToken() assegna contemporaneamente al visitatore un cookie denominato __RequestVerificationToken, con lo stesso valore del valore nascosto casuale illustrato sopra. Per convalidare un post del form in ingresso, aggiungere quindi il filtro [ValidateAntiForgeryToken] al metodo azione di destinazione. ad esempio:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Filtro di autorizzazione che controlla che:
* La richiesta in ingresso abbia un cookie denominato __RequestVerificationToken.
* La richiesta in ingresso ha un cookie `Request.Form` denominato __RequestVerificationToken
* Questi valori del cookie e di `Request.Form` corrispondono. Presumendo che sia tutto a posto, la richiesta viene completata normalmente. In caso contrario, si verifica un errore di autorizzazione e viene visualizzato il messaggio "Token antifalsificazione non specificato o non valido".

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | MVC 5, MVC 6 |
| **Attributes (Attributi) (Attributi)**              | Provider di identità: AD FS, provider di identità: Azure AD |
| **Riferimenti**              | [Secure a Web API with Individual Accounts and Local Login in ASP.NET Web API 2.2](http://www.asp.net/web-api/overview/security/individual-accounts-in-web-api) (Proteggere un'API Web con account singoli e account di accesso locale in API Web ASP.NET 2.2) |
| **Passaggi** | Se l'API Web viene protetta con OAuth 2.0, è previsto un token di connessione nell'intestazione della richiesta di autorizzazione e l'accesso alla richiesta viene concesso solo se il token è valido. Diversamente dall'autenticazione basata su cookie, i browser non allegano i token di connessione alle richieste. Il client richiedente deve allegare esplicitamente il token di connessione nell'intestazione della richiesta. Per le API Web ASP.NET protette con OAuth 2.0, i token di connessione vengono quindi considerati una difesa contro gli attacchi basati su richieste intersito false. Tenere presente che, se la parte MVC dell'applicazione usa l'autenticazione basata su form (ad esempio, usa i cookie), dall'app Web MVC devono essere usati token antifalsificazione. |

### <a name="example"></a>Esempio
All'API Web deve essere comunicato di basarsi SOLO sui token di connessione e non sui cookie. È possibile seguire la configurazione nel metodo `WebApiConfig.Register`: ```C-Sharp code config.SuppressDefaultHostAuthentication(); config.Filters.Add(new HostAuthenticationFilter(OAuthDefaults.AuthenticationType));
```
The SuppressDefaultHostAuthentication method tells Web API to ignore any authentication that happens before the request reaches the Web API pipeline, either by IIS or by OWIN middleware. That way, we can restrict Web API to authenticate only using bearer tokens.