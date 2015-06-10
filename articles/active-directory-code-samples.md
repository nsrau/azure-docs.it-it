<properties 
   pageTitle="Esempi di codice di Azure Active Directory" 
   description="Indice di esempi di codice di Azure Active Directory, organizzati in base allo scenario." 
   services="active-directory" 
   documentationCenter="dev-center-name" 
   authors="msmbaldwin" 
   manager="mbaldwin" 
   editor=""/>

<tags
   ms.service="azure"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity" 
   ms.date="04/06/2015"
   ms.author="mbaldwin"/>

# Esempi di codice di Azure Active Directory 

[AZURE.INCLUDE [active-directory-devguide](../includes/active-directory-devguide.md)]

Microsoft Azure Active Directory (Azure AD) consente di aggiungere funzionalità di autenticazione e autorizzazione alle applicazioni e alle API Web. Questa sezione fornisce collegamenti a esempi di codice che illustrano la procedura da seguire e a frammenti di codice usabili nelle applicazioni. Nella pagina relativa agli esempi di codice sono disponibili argomenti readme dettagliati contenenti informazioni sui requisiti e sulle procedure di installazione e configurazione. Il codice è inoltre accompagnato da commenti per facilitare la comprensione delle sezioni critiche.

Per comprendere lo scenario di base di ogni tipo di esempio, vedere Scenari di autenticazione per Azure AD.

È possibile contribuire agli esempi su GitHub: [Esempi e documentazione su Microsoft Azure Active Directory](https://github.com/AzureADSamples).

## Da Web browser ad applicazione Web 

Questi esempi illustrano come scrivere un'applicazione Web che indirizza il browser dell'utente per l'accesso a Azure AD.



| Linguaggio/Piattaforma | Esempio | Descrizione
| ----------------- | ------ | -----------
| C#/.NET | [WebApp-OpenIDConnect-DotNet](http://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | Viene usato OpenID Connect (middleware OpenID Connect OWIN ASP.NET) per eseguire l'autenticazione degli utenti da un tenant di Azure AD.
| C#/.NET | [WebApp-MultiTenant-OpenIdConnect-DotNet](https://github.com/AzureADSamples/WebApp-MultiTenant-OpenIdConnect-DotNet) | Applicazione Web MVC .NET multi-tenant che usa OpenID Connect (middleware OpenID Connect OWIN ASP.NET) per eseguire l'autenticazione degli utenti da più tenant di Azure AD.
| C#/.NET | [WebApp-WSFederation-DotNet](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | Viene usato WS-Federation (middleware WS-Federation OWIN ASP.NET) per eseguire l'autenticazione degli utenti da un tenant di Azure AD.






## Applicazione a singola pagina (SPA)

Questo esempio illustra come scrivere un'applicazione a singola pagina protetta con Azure AD.

| Linguaggio/Piattaforma | Esempio | Descrizione
| ----------------- | ------ | -----------
| JavaScript, C#/.NET | [SinglePageApp-DotNet](https://github.com/AzureADSamples/SinglePageApp-DotNet) | Vengono usati ADAL per JavaScript e Azure AD per proteggere un'app a singola pagina basata su AngularJS implementata con il back-end di un'API Web ASP.NET.


## Da applicazione nativa ad API Web
 
Questi esempi di codice illustrano come creare applicazioni client native che chiamano API Web protette da Azure AD. Vengono usati [Azure AD Authentication Library (ADAL)](http://go.microsoft.com/fwlink/?LinkID=258232) e [OAuth 2.0 in Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).
 
| Linguaggio/Piattaforma | Esempio | Descrizione
| ----------------- | ------ | -----------
| JavaScript | [NativeClient-MultiTarget-Cordova](https://github.com/AzureADSamples/NativeClient-MultiTarget-Cordova) | Viene usato il plug-in ADAL per Apache Cordova per creare un'app Apache Cordova che chiama un'API Web e usa Azure AD per l'autenticazione.
| C#/.NET | [NativeClient-DotNet](http://github.com/AzureADSamples/NativeClient-DotNet) | Applicazione WPF .NET che chiama un'API Web protetta mediante Azure AD.
| C#/.NET | [NativeClient-WindowsStore](http://github.com/AzureADSamples/NativeClient-WindowsStore) | Applicazione di Windows Store che chiama un'API Web protetta mediante Azure AD.
| C#/.NET | [NativeClient-WebAPI-MultiTenant-WindowsStore](https://github.com/AzureADSamples/NativeClient-WebAPI-MultiTenant-WindowsStore) | Applicazione di Windows Store che chiama un'API Web multi-tenant protetta mediante Azure AD.
| C#/.NET | [WebAPI-OnBehalfOf-DotNet](http://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet) | Applicazione client nativa che chiama un'API Web, la quale ottiene un token per agire per conto dell'utente originale e quindi usa il token per chiamare un'altra API Web.
| C#/.NET | [NativeClient-WindowsPhone8.1](https://github.com/AzureADSamples/NativeClient-WindowsPhone8.1) | Applicazione di Windows Store per Windows Phone 8.1 che chiama un'API Web protetta mediante Azure AD.
| ObjC | [NativeClient-iOS](http://github.com/AzureADSamples/NativeClient-iOS) | Applicazione iOS che chiama un'API Web che richiede Azure AD per l'autenticazione.
| C#/.NET | [WebAPI-ManuallyValidateJwt-DotNet](https://github.com/AzureADSamples/WebAPI-ManuallyValidateJwt-DotNet) | Applicazione client nativa che include la logica per l'elaborazione di un token JWT in un'API Web anziché usare il middleware OWIN.
| C#/Xamarin | [NativeClient-Xamarin-Android](https://github.com/AzureADSamples/NativeClient-Xamarin-Android) | Binding Xamarin all'istanza nativa di Azure AD Authentication Library (ADAL) per la libreria Android.
| C#/Xamarin | [NativeClient-Xamarin-iOS](http://github.com/AzureADSamples/NativeClient-Xamarin-iOS) | Binding Xamarin all'istanza nativa di Azure AD Authentication Library (ADAL) per iOS.
| C#/Xamarin | [NativeClient-MultiTarget-DotNet](http://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet) | Progetto Xamarin destinato a cinque piattaforme che chiama un'API Web protetta mediante Azure AD.
| C#/.NET | [NativeClient-Headless-DotNet](http://github.com/AzureADSamples/NativeClient-Headless-DotNet) | Applicazione nativa che esegue l'autenticazione non interattiva e chiama un'API Web protetta mediante Azure AD.

   

## Da applicazione Web ad API Web

Questi esempi di codice illustrano come usare [OAuth 2.0 in Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx) per creare applicazioni Web che chiamano API Web protette mediante Azure AD.

| Linguaggio/Piattaforma | Esempio | Descrizione
| ----------------- | ------ | -----------
| C#/.NET | [WebApp-WebAPI-OpenIDConnect-DotNet](http://github.com/AzureADSamples/WebApp-WebAPI-OpenIDConnect-DotNet) | Viene chiamata un'API Web con le autorizzazioni dell'utente che ha eseguito l'accesso.
| C#/.NET | [WebApp-WebAPI-OAuth2-AppIdentity-DotNet](http://github.com/AzureADSamples/WebApp-WebAPI-OAuth2-AppIdentity-DotNet) | Viene chiamata un'API Web con le autorizzazioni dell'applicazione.
| C#/.NET | [WebApp-WebAPI-OAuth2-UserIdentity-Dotnet](http://github.com/AzureADSamples/WebApp-WebAPI-OAuth2-UserIdentity-Dotnet) | Vengono aggiunte autorizzazioni con [OAuth 2.0 in Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx) a un'applicazione Web esistente, in modo che questa possa chiamare un'API Web.
| JavaScript | [WebAPI-Nodejs](http://github.com/AzureADSamples/WebAPI-Nodejs) | Viene configurato un servizio API REST integrato con Azure AD per la protezione delle API. Viene incluso un server Node.js con un'API Web.
| C#/.NET | [WebApp-WebAPI-MultiTenant-OpenIdConnect-DotNet](https://github.com/AzureADSamples/WebApp-WebAPI-MultiTenant-OpenIdConnect-DotNet) | Applicazione Web MVC multi-tenant che usa OpenID Connect (middleware OpenID Connect OWIN ASP.NET) per eseguire l'autenticazione degli utenti da un tenant di Azure AD. Viene usato un codice di autorizzazione per richiamare l'API Graph.

## Applicazione server o daemon ad API Web

Questi esempi di codice illustrano come creare un'applicazione server o daemon che ottiene risorse da un'API Web mediante [Azure AD Authentication Library (ADAL)](http://go.microsoft.com/fwlink/?LinkID=258232) e [OAuth 2.0 in Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).

| Linguaggio/Piattaforma | Esempio | Descrizione
| ----------------- | ------ | -----------
| C#/.NET | [Daemon-DotNet](http://github.com/AzureADSamples/Daemon-DotNet) | Applicazione console che chiama un'API Web. Le credenziali client sono rappresentate da una password.
| C#/.NET | [Daemon-CertificateCredential-DotNet](http://github.com/AzureADSamples/Daemon-CertificateCredential-DotNet) | Applicazione console che chiama un'API Web. Le credenziali client sono rappresentate da un certificato.


## Chiamata dell'API Graph di Azure AD

Questi esempi di codice illustrano come creare applicazioni che chiamano l'API Graph di Azure AD per la lettura e la scrittura dei dati della directory.

| Linguaggio/Piattaforma | Esempio | Descrizione
| ----------------- | ------ | -----------
| Java | [WebApp-GraphAPI-Java](http://github.com/AzureADSamples/WebApp-GraphAPI-Java) | Applicazione Web che usa l'API Graph per accedere ai dati della directory di Azure AD.
| PHP | [WebApp-GraphAPI-PHP](http://github.com/AzureADSamples/WebApp-GraphAPI-PHP) | Applicazione Web che usa l'API Graph per accedere ai dati della directory di Azure AD.
| C#/.NET | [WebApp-GraphAPI-DotNet](http://github.com/AzureADSamples/WebApp-GraphAPI-DotNet) | Applicazione Web che usa l'API Graph per accedere ai dati della directory di Azure AD.
| C#/.NET | [ConsoleApp-GraphAPI-DotNet](https://github.com/AzureADSamples/ConsoleApp-GraphAPI-DotNet) | Questa app console mostra le normali chiamate in lettura e scrittura all'API Graph e illustra come eseguire l'assegnazione delle licenze utente e aggiornare la foto di anteprima e i collegamenti di un utente.
| C#/.NET | [ConsoleApp-GraphAPI-DiffQuery-DotNet](https://github.com/AzureADSamples/ConsoleApp-GraphAPI-DiffQuery-DotNet) | Applicazione console che usa la query differenziale nell'API Graph per ottenere modifiche periodiche agli oggetti utente in un tenant di Azure AD.
| C#/.NET | [WebApp-GraphAPI-DirectoryExtensions-DotNet](https://github.com/AzureADSamples/WebApp-GraphAPI-DirectoryExtensions-DotNet) | Applicazione MVC che usa query sull'API Graph per generare un semplice organigramma della società.
| PHP | [WebApp-GraphAPI-DirectoryExtensions-PHP](https://github.com/AzureADSamples/WebApp-GraphAPI-DirectoryExtensions-PHP) | Applicazione PHP che chiama l'API Graph per registrare un'estensione e quindi leggere, aggiornare ed eliminare i valori definiti nell'attributo dell'estensione.


## Authorization

Questo esempio di codice illustra come usare Azure AD per l'autorizzazione.

| Linguaggio/Piattaforma | Esempio | Descrizione
| ----------------- | ------ | -----------
| C#/.NET | [WebApp-GroupClaims-DotNet](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet) | Viene eseguito il controllo degli accessi in base al ruolo usando le attestazioni basate su gruppo di Azure Active Directory in un'applicazione integrata con Azure AD.
| C#/.NET | [WebApp-RoleClaims-DotNet](https://github.com/AzureADSamples/WebApp-RoleClaims-DotNet) | Viene eseguito il controllo degli accessi in base al ruolo usando i ruoli applicazione di Azure Active Directory in un'applicazione integrata con Azure AD.


## Procedure dettagliate legacy

Queste procedure possono risultare interessanti per l'utente pur essendo basate su tecnologie meno recenti.

| Linguaggio/Piattaforma | Esempio | Descrizione
| ----------------- | ------ | -----------
| C#/.NET | [Autorizzazione basata sui ruoli e basata su ACL in un'applicazione di Microsoft Azure AD](http://go.microsoft.com/fwlink/?LinkId=331694) | Viene eseguita l'autorizzazione basata sui ruoli (Controllo degli accessi in base al ruolo) e sull'elenco di controllo di accesso (ACL) in un'applicazione integrata con Azure AD.
| C#/.NET | [AAL - Da app di Windows Store a servizio REST - Autenticazione](http://go.microsoft.com/fwlink/?LinkId=330605) | Viene usata la libreria [Azure AD Authentication Library (ADAL)](http://go.microsoft.com/fwlink/?LinkID=258232) (in precedenza AAL) per Windows Store Beta per aggiungere funzionalità di autenticazione utente a un'app di Windows Store.
| C#/.NET | [ADAL - Da app nativa a servizio REST - Autenticazione con AAD mediante finestra di dialogo del browser](http://go.microsoft.com/fwlink/?LinkId=259814) | Viene usata la libreria [Azure AD Authentication Library (ADAL)](http://go.microsoft.com/fwlink/?LinkID=258232) per aggiungere funzionalità di autenticazione utente a un client WPF.
| C#/.NET | [ADAL - Da app nativa a servizio REST - Autenticazione con ACS mediante finestra di dialogo del browser](http://code.msdn.microsoft.com/AAL-Native-App-to-REST-de57f2cc) | Vengono usati la libreria [Azure AD Authentication Library (ADAL)](http://go.microsoft.com/fwlink/?LinkID=258232) e il [Servizio di controllo di accesso (ACS, Access Control Service) 2.0](http://msdn.microsoft.com/library/azure/hh147631.aspx) per aggiungere funzionalità di autenticazione utente a un client WPF.
| C#/.NET | [ADAL - Autenticazione da server a server](http://go.microsoft.com/fwlink/?LinkId=259816) | Viene usata la libreria [Azure AD Authentication Library (ADAL)](http://go.microsoft.com/fwlink/?LinkID=258232) per proteggere le chiamate effettuate da un processo lato server a un servizio REST dell'API Web MVC4.
| C#/.NET | [Aggiunta del processo di accesso nell'applicazione Web tramite Azure AD](https://msdn.microsoft.com/library/azure/dn151790.aspx) | Viene configurata un'applicazione .NET per eseguire l'accesso Single Sign-On Web nella directory dell'organizzazione di Azure AD.
| C#/.NET | [Sviluppo di applicazioni Web multi-tenant con Azure AD](https://msdn.microsoft.com/library/azure/dn151789.aspx) | Viene usato Azure AD per potenziare le funzionalità Single Sign-On e di accesso alla directory di un'applicazione .NET per consentirne il funzionamento in più organizzazioni.
JAVA | [App di esempio Java per l'API Graph di Azure AD](http://go.microsoft.com/fwlink/?LinkId=263969) | Viene usata l'API Graph per accedere ai dati della directory da Azure AD.
PHP | [App di esempio PHP per l'API Graph di Azure AD](http://code.msdn.microsoft.com/PHP-Sample-App-For-Windows-228c6ddb) | Viene usata l'API Graph per accedere ai dati della directory da Azure AD.
| C#/.NET | [App di esempio per l'API Graph di Azure AD](http://go.microsoft.com/fwlink/?LinkID=262648) | Viene usata l'API Graph per accedere ai dati della directory da Azure AD.
| C#/.NET | [App di esempio per la query differenziale di Azure AD Graph](http://go.microsoft.com/fwlink/?LinkId=275398) | Viene usata la query differenziale nell'API Graph per ottenere modifiche periodiche agli oggetti utente in un tenant di Azure AD.
| C#/.NET | [App di esempio per integrare un'applicazione cloud multi-tenant per Azure AD](http://go.microsoft.com/fwlink/?LinkId=275397) | Viene integrata un'applicazione multi-tenant in Azure AD.
| C#/.NET | [Protezione di un'applicazione di Windows Store e di un servizio Web REST mediante Azure AD (Anteprima)](https://msdn.microsoft.com/library/azure/dn169448.aspx) | Vengono create una risorsa API Web semplice e un'applicazione client di Windows Store mediante Azure AD e [Azure AD Authentication Library (ADAL)](http://go.microsoft.com/fwlink/?LinkID=258232).
| C#/.NET| [Uso dell'API Graph per eseguire query su Azure AD](https://msdn.microsoft.com/library/azure/dn151791.aspx) | Viene configurata un'applicazione Microsoft .NET per l'uso dell'API Graph di Azure AD per accedere ai dati da una directory di un tenant di Azure AD.

## Vedere anche

##### Altre risorse


[Libreria helper dell'API Graph di Azure AD](http://code.msdn.microsoft.com/Windows-Azure-AD-Graph-API-a8c72e18)

[Sviluppo di applicazioni moderne con OAuth e Active Directory Federation Services](http://msdn.microsoft.com/library/dn633593.aspx)

<!---HONumber=58-->