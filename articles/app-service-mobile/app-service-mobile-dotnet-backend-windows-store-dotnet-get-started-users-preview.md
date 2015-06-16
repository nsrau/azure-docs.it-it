<properties 
	pageTitle="Introduzione all'autenticazione per app per dispositivi mobili in Windows" 
	description="Informazioni su come usare le app per dispositivi mobili per autenticare gli utenti dell'app per iOS tramite vari provider di identità, tra cui AAD, Google, Facebook, Twitter e Microsoft." 
	services="app-service\mobile" 
	documentationCenter="windows" 
	authors="mattchenderson,ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/23/2014" 
	ms.author="mahender"/>

# Aggiungere l'autenticazione all'app Windows

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Questo argomento descrive come autenticare gli utenti di un'app mobile del servizio app dall'applicazione client. In questa esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite un provider di identità supportato dal servizio app. In seguito all'autenticazione e all'autorizzazione da parte dell'app mobile, viene visualizzato il valore dell'ID utente.

Questa esercitazione è basata sulla guida introduttiva dell'app mobile. È anche necessario completare prima l'esercitazione [Introduzione al servizio per app per dispositivi mobili].

##<a name="register"></a>Registrare l'app per l'autenticazione e configurare il servizio app

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)] 

##<a name="permissions"></a>Limitare le autorizzazioni agli utenti autenticati

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)] 

<ol start="7">
<li>In Visual Studio aprire il progetto di app client e assicurarsi che l'istanza di <b>MobileServiceClient</b> in App.xaml.cs sia configurata per l'uso dell'URL della risorsa dell'app per dispositivi mobili nel cloud.</li> 
<li><p>Premere F5 per eseguire questa app basata sul progetto di guida introduttiva. Verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (non autorizzato).</p>
   
   	<p>L'eccezione non gestita viene generata perché l'app prova ad accedere al codice dell'app per dispositivi mobili come utente non autenticato, mentre la tabella <em>TodoItem</em> richiede ora l'autenticazione.</p></li>
</ol>

A questo punto, si aggiornerà l'app in modo che autentichi gli utenti prima di richiedere risorse al servizio mobile.

##<a name="add-authentication"></a>Aggiungere l'autenticazione all'app

[AZURE.INCLUDE [app-service-mobile-windows-universal-dotnet-authenticate-app](../../includes/app-service-mobile-windows-universal-dotnet-authenticate-app.md)] 


[AZURE.NOTE]Se le informazioni del pacchetto dell'app di Windows Store sono state registrate con i servizi app, è necessario chiamare il metodo <a href="http://go.microsoft.com/fwlink/p/?LinkId=311594" target="_blank">LoginAsync</a> specificando il valore <strong>true</strong> per il parametro <em>useSingleSignOn</em>. In caso contrario, gli utenti dovranno comunque specificare le credenziali di accesso ogni volta che viene chiamato il metodo di accesso.


##<a name="tokens"></a>Archiviare il token di autenticazione sul client

[AZURE.INCLUDE [app-service-mobile-windows-store-dotnet-authenticate-app-with-token](../../includes/app-service-mobile-windows-store-dotnet-authenticate-app-with-token.md)] 


<!-- Anchors. -->
[Register your app for authentication and configure the App Service]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #tokens
[Next Steps]: #next-steps


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introduzione al servizio per app per dispositivi mobili]: app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview.md


[Azure Management Portal]: https://manage.windowsazure.com/
[Mobile Services .NET How-to Conceptual Reference]: /it-it/develop/mobile/how-to-guides/work-with-net-client-library
[Register your Windows Store app package for Microsoft authentication]: ../mobile-services-how-to-register-store-app-package-microsoft-authentication.md
[Single sign-on for Windows Store apps by using Live Connect]: ../mobile-services-windows-store-dotnet-single-sign-on.md
<!--HONumber=54--> 