<properties
   pageTitle="Esempi di codice di Azure Active Directory | Microsoft Azure"
   description="Indice di esempi di codice di Azure Active Directory, organizzati in base allo scenario."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="05/16/2016"
   ms.author="mbaldwin"/>

# Esempi di codice di Azure Active Directory

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Microsoft Azure Active Directory (Azure AD) consente di aggiungere funzionalità di autenticazione e autorizzazione alle applicazioni e alle API Web. Questa sezione fornisce collegamenti a esempi di codice che illustrano la procedura da seguire e a frammenti di codice usabili nelle applicazioni. Nella pagina relativa agli esempi di codice sono disponibili argomenti readme dettagliati contenenti informazioni sui requisiti e sulle procedure di installazione e configurazione. Il codice è inoltre accompagnato da commenti per facilitare la comprensione delle sezioni critiche.

Per comprendere lo scenario di base di ogni tipo di esempio, vedere Scenari di autenticazione per Azure AD.

È possibile contribuire agli esempi su GitHub: [Esempi e documentazione su Microsoft Azure Active Directory](https://github.com/Azure-Samples?page=3&query=active-directory).

## Da Web browser ad applicazione Web

Questi esempi illustrano come scrivere un'applicazione Web che indirizza il browser dell'utente per l'accesso a Azure AD.



| Linguaggio/Piattaforma | Esempio | Descrizione
| ----------------- | ------ | -----------
| C#/.NET | [WebApp-OpenIDConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) | Viene usato OpenID Connect (middleware OpenID Connect OWIN ASP.NET) per eseguire l'autenticazione degli utenti da un tenant di Azure AD.
| C#/.NET | [WebApp-MultiTenant-OpenIdConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect) | Applicazione Web MVC .NET multi-tenant che usa OpenID Connect (middleware OpenID Connect OWIN ASP.NET) per eseguire l'autenticazione degli utenti da più tenant di Azure AD.
| C#/.NET | [WebApp-WSFederation-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) | Viene usato WS-Federation (middleware WS-Federation OWIN ASP.NET) per eseguire l'autenticazione degli utenti da un tenant di Azure AD.






## Applicazione a singola pagina (SPA)

Questo esempio illustra come scrivere un'applicazione a singola pagina protetta con Azure AD.

| Linguaggio/Piattaforma | Esempio | Descrizione
| ----------------- | ------ | -----------
| JavaScript, C#/.NET | [SinglePageApp-DotNet](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) | Vengono usati ADAL per JavaScript e Azure AD per proteggere un'app a singola pagina basata su AngularJS implementata con il back-end di un'API Web ASP.NET.


## Da applicazione nativa ad API Web

Questi esempi di codice illustrano come creare applicazioni client native che chiamano API Web protette da Azure AD. Vengono usati [Azure AD Authentication Library (ADAL)](active-directory-authentication-libraries.md) e [OAuth 2.0 in Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).

| Linguaggio/Piattaforma | Esempio | Descrizione
| ----------------- | ------ | -----------
| JavaScript | [NativeClient-MultiTarget-Cordova](https://github.com/Azure-Samples/active-directory-cordova-multitarget) | Viene usato il plug-in ADAL per Apache Cordova per creare un'app Apache Cordova che chiama un'API Web e usa Azure AD per l'autenticazione.
| C#/.NET | [NativeClient-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) | Applicazione WPF .NET che chiama un'API Web protetta mediante Azure AD.
| C#/.NET | [NativeClient-WindowsStore](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) | Applicazione di Windows Store che chiama un'API Web protetta mediante Azure AD.
| C#/.NET | [NativeClient-WebAPI-MultiTenant-WindowsStore](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) | Applicazione di Windows Store che chiama un'API Web multi-tenant protetta mediante Azure AD.
| C#/.NET | [WebAPI-OnBehalfOf-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof) | Applicazione client nativa che chiama un'API Web, la quale ottiene un token per agire per conto dell'utente originale e quindi usa il token per chiamare un'altra API Web.
| C#/.NET | [NativeClient-WindowsPhone8.1](https://github.com/Azure-Samples/active-directory-dotnet-windowsphone-8.1) | Applicazione di Windows Store per Windows Phone 8.1 che chiama un'API Web protetta mediante Azure AD.
| ObjC | [NativeClient-iOS](https://github.com/Azure-Samples/active-directory-ios) | Applicazione iOS che chiama un'API Web che richiede Azure AD per l'autenticazione.
| C#/.NET | [WebAPI-ManuallyValidateJwt-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapi-manual-jwt-validation) | Applicazione client nativa che include la logica per l'elaborazione di un token JWT in un'API Web anziché usare il middleware OWIN.
| C#/Xamarin | [NativeClient-Xamarin-Android](https://github.com/Azure-Samples/active-directory-xamarin-android) | Binding Xamarin all'istanza nativa di Azure AD Authentication Library (ADAL) per la libreria Android.
| C#/Xamarin | [NativeClient-Xamarin-iOS](https://github.com/Azure-Samples/active-directory-xamarin-ios) | Binding Xamarin all'istanza nativa di Azure AD Authentication Library (ADAL) per iOS.
| C#/Xamarin | [NativeClient-MultiTarget-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-native-multitarget) | Progetto Xamarin destinato a cinque piattaforme che chiama un'API Web protetta mediante Azure AD.
| C#/.NET | [NativeClient-Headless-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-native-headless) | Applicazione nativa che esegue l'autenticazione non interattiva e chiama un'API Web protetta mediante Azure AD.



## Da applicazione Web ad API Web

Questi esempi di codice illustrano come usare [OAuth 2.0 in Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx) per creare applicazioni Web che chiamano API Web protette mediante Azure AD.

| Linguaggio/Piattaforma | Esempio | Descrizione
| ----------------- | ------ | -----------
| C#/.NET | [WebApp-WebAPI-OpenIDConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect) | Viene chiamata un'API Web con le autorizzazioni dell'utente che ha eseguito l'accesso.
| C#/.NET | [WebApp-WebAPI-OAuth2-AppIdentity-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity) | Viene chiamata un'API Web con le autorizzazioni dell'applicazione.
| C#/.NET | [WebApp-WebAPI-OAuth2-UserIdentity-Dotnet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | Vengono aggiunte autorizzazioni con [OAuth 2.0 in Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx) a un'applicazione Web esistente, in modo che questa possa chiamare un'API Web.
| JavaScript | [WebAPI-Nodejs](https://github.com/Azure-Samples/active-directory-node-webapi) | Viene configurato un servizio API REST integrato con Azure AD per la protezione delle API. Viene incluso un server Node.js con un'API Web.
| C#/.NET | [WebApp-WebAPI-MultiTenant-OpenIdConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-multitenant-openidconnect) | Applicazione Web MVC multi-tenant che usa OpenID Connect (middleware OpenID Connect OWIN ASP.NET) per eseguire l'autenticazione degli utenti da un tenant di Azure AD. Viene usato un codice di autorizzazione per richiamare l'API Graph.

## Applicazione server o daemon ad API Web

Questi esempi di codice illustrano come creare un'applicazione server o daemon che ottiene risorse da un'API Web mediante [Azure AD Authentication Library (ADAL)](active-directory-authentication-libraries.md) e [OAuth 2.0 in Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).

| Linguaggio/Piattaforma | Esempio | Descrizione
| ----------------- | ------ | -----------
| C#/.NET | [Daemon-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-daemon) | Applicazione console che chiama un'API Web. Le credenziali client sono rappresentate da una password.
| C#/.NET | [Daemon-CertificateCredential-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential) | Applicazione console che chiama un'API Web. Le credenziali client sono rappresentate da un certificato.


## Chiamata dell'API Graph di Azure AD

Questi esempi di codice illustrano come creare applicazioni che chiamano l'API Graph di Azure AD per la lettura e la scrittura dei dati della directory.

| Linguaggio/Piattaforma | Esempio | Descrizione
| ----------------- | ------ | -----------
| Java | [WebApp-GraphAPI-Java](https://github.com/Azure-Samples/active-directory-java-graphapi-web) | Applicazione Web che usa l'API Graph per accedere ai dati della directory di Azure AD.
| PHP | [WebApp-GraphAPI-PHP](https://github.com/Azure-Samples/active-directory-php-graphapi-web) | Applicazione Web che usa l'API Graph per accedere ai dati della directory di Azure AD.
| C#/.NET | [WebApp-GraphAPI-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-web) | Applicazione Web che usa l'API Graph per accedere ai dati della directory di Azure AD.
| C#/.NET | [ConsoleApp-GraphAPI-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-console) | Questa app console mostra le normali chiamate in lettura e scrittura all'API Graph e illustra come eseguire l'assegnazione delle licenze utente e aggiornare la foto di anteprima e i collegamenti di un utente.
| C#/.NET | [ConsoleApp-GraphAPI-DiffQuery-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-diffquery) | Applicazione console che usa la query differenziale nell'API Graph per ottenere modifiche periodiche agli oggetti utente in un tenant di Azure AD.
| C#/.NET | [WebApp-GraphAPI-DirectoryExtensions-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-directoryextensions-web) | Applicazione MVC che usa query sull'API Graph per generare un semplice organigramma della società.
| PHP | [WebApp-GraphAPI-DirectoryExtensions-PHP](https://github.com/Azure-Samples/active-directory-php-graphapi-directoryextensions-web) | Applicazione PHP che chiama l'API Graph per registrare un'estensione e quindi leggere, aggiornare ed eliminare i valori definiti nell'attributo dell'estensione.


## Authorization

Questo esempio di codice illustra come usare Azure AD per l'autorizzazione.

| Linguaggio/Piattaforma | Esempio | Descrizione
| ----------------- | ------ | -----------
| C#/.NET | [WebApp-GroupClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) | Viene eseguito il controllo degli accessi in base al ruolo usando le attestazioni basate su gruppo di Azure Active Directory in un'applicazione integrata con Azure AD.
| C#/.NET | [WebApp-RoleClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | Viene eseguito il controllo degli accessi in base al ruolo usando i ruoli applicazione di Azure Active Directory in un'applicazione integrata con Azure AD.


## Procedure dettagliate legacy

Queste procedure possono risultare interessanti per l'utente pur essendo basate su tecnologie meno recenti.

| Linguaggio/Piattaforma | Esempio | Descrizione
| ----------------- | ------ | -----------
| C#/.NET | [Autorizzazione basata sui ruoli e basata su ACL in un'applicazione di Microsoft Azure AD](http://go.microsoft.com/fwlink/?LinkId=331694) | Viene eseguita l'autorizzazione basata sui ruoli (Controllo degli accessi in base al ruolo) e sull'elenco di controllo di accesso (ACL) in un'applicazione integrata con Azure AD.
| C#/.NET | [AAL - Da app di Windows Store a servizio REST - Autenticazione](http://go.microsoft.com/fwlink/?LinkId=330605) | Viene usata la libreria [Azure AD Authentication Library (ADAL)](active-directory-authentication-libraries.md) (in precedenza AAL) per Windows Store Beta per aggiungere funzionalità di autenticazione utente a un'app di Windows Store.
| C#/.NET | [ADAL - Da app nativa a servizio REST - Autenticazione con AAD mediante finestra di dialogo del browser](http://go.microsoft.com/fwlink/?LinkId=259814) | Viene usata la libreria [Azure AD Authentication Library (ADAL)](active-directory-authentication-libraries.md) per aggiungere funzionalità di autenticazione utente a un client WPF.
| C#/.NET | [ADAL - Da app nativa a servizio REST - Autenticazione con ACS mediante finestra di dialogo del browser](http://code.msdn.microsoft.com/AAL-Native-App-to-REST-de57f2cc) | Vengono usati la libreria [Azure AD Authentication Library (ADAL)](active-directory-authentication-libraries.md) e il [Servizio di controllo di accesso (ACS, Access Control Service) 2.0](http://msdn.microsoft.com/library/azure/hh147631.aspx) per aggiungere funzionalità di autenticazione utente a un client WPF.
| C#/.NET | [ADAL - Autenticazione da server a server](http://go.microsoft.com/fwlink/?LinkId=259816) | Viene usata la libreria [Azure AD Authentication Library (ADAL)](active-directory-authentication-libraries.md) per proteggere le chiamate effettuate da un processo lato server a un servizio REST dell'API Web MVC4.
| C#/.NET | [Aggiunta del processo di accesso nell'applicazione Web tramite Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) | Viene configurata un'applicazione .NET per eseguire l'accesso Single Sign-On Web nella directory dell'organizzazione di Azure AD.
| C#/.NET | [Sviluppo di applicazioni Web multi-tenant con Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect) | Viene usato Azure AD per potenziare le funzionalità Single Sign-On e di accesso alla directory di un'applicazione .NET per consentirne il funzionamento in più organizzazioni.
JAVA | [App di esempio Java per l'API Graph di Azure AD](http://go.microsoft.com/fwlink/?LinkId=263969) | Viene usata l'API Graph per accedere ai dati della directory da Azure AD.
PHP | [App di esempio PHP per l'API Graph di Azure AD](http://code.msdn.microsoft.com/PHP-Sample-App-For-Windows-228c6ddb) | Viene usata l'API Graph per accedere ai dati della directory da Azure AD.
| C#/.NET | [App di esempio per l'API Graph di Azure AD](http://go.microsoft.com/fwlink/?LinkID=262648) | Viene usata l'API Graph per accedere ai dati della directory da Azure AD.
| C#/.NET | [App di esempio per la query differenziale di Azure AD Graph](http://go.microsoft.com/fwlink/?LinkId=275398) | Viene usata la query differenziale nell'API Graph per ottenere modifiche periodiche agli oggetti utente in un tenant di Azure AD.
| C#/.NET | [App di esempio per integrare un'applicazione cloud multi-tenant per Azure AD](http://go.microsoft.com/fwlink/?LinkId=275397) | Viene integrata un'applicazione multi-tenant in Azure AD.
| C#/.NET | [Protezione di un'applicazione di Windows Store e di un servizio Web REST mediante Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) | Vengono create una risorsa API Web semplice e un'applicazione client di Windows Store mediante Azure AD e [Azure AD Authentication Library (ADAL)](active-directory-authentication-libraries.md).
| C#/.NET| [Uso dell'API Graph per eseguire query su Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-web) | Viene configurata un'applicazione Microsoft .NET per l'uso dell'API Graph di Azure AD per accedere ai dati da una directory di un tenant di Azure AD.

## Vedere anche

##### Altre risorse

[Guida per gli sviluppatori di Azure Active Directory](active-directory-developers-guide.md)

[Concetti e riferimenti relativi all'API Graph di Azure AD](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Libreria helper dell'API Graph di Azure AD](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)

<!---HONumber=AcomDC_0518_2016-->