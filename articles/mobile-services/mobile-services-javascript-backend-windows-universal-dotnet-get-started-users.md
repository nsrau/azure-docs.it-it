<properties 
	pageTitle="Aggiungere l'autenticazione all'app universale di Windows 8.1 | Servizi mobili di Azure"
	description="Informazioni su come usare Servizi mobili per autenticare gli utenti dell'app per Windows Store tramite vari provider di identità, tra cui Google, Facebook, Twitter e Microsoft." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="10/06/2015" 
	ms.author="glenga"/>

# Aggiungere l'autenticazione all'app universale della piattaforma UWP 8.1

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

Questo argomento illustra come autenticare gli utenti in Servizi mobili di Azure dalla propria app di Windows universale 8.1. Nell'esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite un provider di identità supportato da Servizi mobili. In seguito all'autenticazione e all'autorizzazione di Servizi mobili, viene visualizzato il valore dell'ID utente.

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. È inoltre necessario completare prima l'esercitazione [Introduzione a Servizi mobili].

>[AZURE.NOTE]Questa esercitazione descrive come autenticare gli utenti nelle app di Windows Store e Windows Phone Store 8.1. Per le app di Windows Phone 8.0 o Windows Phone Silverlight 8.1, consultare la sezione [Introduzione all'autenticazione in Servizi mobili](mobile-services-windows-phone-get-started-users.md).

##<a name="register"></a> Registrare l'app per l'autenticazione e configurare Servizi mobili

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

##<a name="permissions"></a> Limitare le autorizzazioni agli utenti autenticati

[AZURE.INCLUDE [mobile-services-restrict-permissions-windows](../../includes/mobile-services-restrict-permissions-windows.md)]
 
>[AZURE.NOTE]In caso di utilizzo di uno strumento di Visual Studio per connettere la propria app a un servizio mobile, lo strumento genera due set di definizioni **MobileServiceClient**, uno per ciascuna piattaforma client. Si consiglia di semplificare il codice generato unendo le definizioni **MobileServiceClient** `#if...#endif` con wrapping in un'unica definizione senza wrapping usata da entrambe le versioni dell'app. Non è necessario eseguire questa operazione se l'app di guida introduttiva è stata scaricata dal portale di gestione di Azure.

##<a name="add-authentication"></a> Aggiungere l'autenticazione all'app

[AZURE.INCLUDE [mobile-services-windows-universal-dotnet-authenticate-app](../../includes/mobile-services-windows-universal-dotnet-authenticate-app.md)]

A questo punto, qualsiasi utente autenticato dal provider di identità attendibile può accedere alla tabella *TodoItem*. Per proteggere meglio i dati specifici dell'utente, è necessario implementare anche l'autorizzazione. A tale scopo si ottiene l'ID di un determinato utente, che può quindi essere usato per definire il livello di accesso che l'utente deve avere per una specifica risorsa.

##<a name="tokens"></a>Archiviare i token di autorizzazione sul client

[AZURE.INCLUDE [mobile-services-windows-store-dotnet-authenticate-app-with-token](../../includes/mobile-services-windows-store-dotnet-authenticate-app-with-token.md)]

## <a name="next-steps"> </a>Passaggi successivi

Nella prossima esercitazione, [Autorizzazione sul lato servizio degli utenti di Servizi mobili](mobile-services-javascript-backend-service-side-authorization.md), il valore dell'ID utente fornito da Servizi mobili e basato su un utente autenticato verrà utilizzato per filtrare i dati restituiti da Servizi mobili.

##Vedere anche

+ [Funzionalità utenti avanzate](http://go.microsoft.com/fwlink/p/?LinkId=506605)<br/> È possibile ottenere dati utente aggiuntivi gestiti dal provider di identità nel servizio mobile chiamando la funzione **user.getIdentities()** negli script del server. 

+ [Riferimento per i concetti e le procedure di Servizi mobili con .NET] <br/>Ulteriori informazioni su come utilizzare Servizi mobili con un client .NET.


<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #tokens
[Next Steps]: #next-steps


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Introduzione a Servizi mobili]: mobile-services-javascript-backend-windows-store-dotnet-get-started.md
[Get started with authentication]: ../mobile-services-javascript-backend-windows-store-dotnet-get-started-users.md
[Get started with push notifications]: ../mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md
[Authorize users with scripts]: ../mobile-services-windows-store-dotnet-authorize-users-in-scripts.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Riferimento per i concetti e le procedure di Servizi mobili con .NET]: mobile-services-windows-dotnet-how-to-use-client-library.md
[Register your Windows Store app package for Microsoft authentication]: ../mobile-services-how-to-register-store-app-package-microsoft-authentication.md
 

<!---HONumber=Oct15_HO3-->