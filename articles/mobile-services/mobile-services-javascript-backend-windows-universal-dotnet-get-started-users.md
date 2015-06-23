<properties 
	pageTitle="Introduzione all'autenticazione (Windows Store) | Mobile Dev Center" 
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
	ms.date="02/26/2015" 
	ms.author="glenga"/>

# Aggiungere l'autenticazione all'app di Servizi mobili

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)] 

Questo argomento illustra come autenticare gli utenti in Servizi mobili di Azure dalla propria app di Windows universale. Nell'esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite un provider di identità supportato da Servizi mobili. In seguito all'autenticazione e all'autorizzazione di Servizi mobili, viene visualizzato il valore dell'ID utente.

Questa esercitazione descrive le operazioni di base per abilitare l'autenticazione in un'app:

1. [Registrare l'app per l'autenticazione e configurare Servizi mobili]
2. [Limitare le autorizzazioni per la tabella agli utenti autenticati]
3. [Aggiungere l'autenticazione all'app]
5. [Archiviare i token di autenticazione sul client]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. È inoltre necessario completare prima l'esercitazione [Introduzione a Servizi mobili].

>[AZURE.NOTE]Questa esercitazione descrive come autenticare gli utenti nelle app di Windows Store e Windows Phone Store 8.1. Per le app di Windows Phone 8.0 o Windows Phone Silverlight 8.1, consultare la sezione [Introduzione all'autenticazione in Servizi mobili](mobile-services-windows-phone-get-started-users.md).

>Questa esercitazione illustra il flusso di autenticazione gestito da Servizi mobili mediante vari provider di identità. Questo metodo è semplice da configurare e supporta più provider. Per usare invece Live Connect con l'autenticazione gestita da client e offrire un ambiente di accesso Single Sign-On nella propria app per Windows Phone, vedere [Autenticare l'app di Windows Store mediante l'autenticazione gestita da client con account Microsoft](mobile-services-windows-store-dotnet-single-sign-on.md). L'uso dell'autenticazione gestita dal client consente all'app di accedere a dati utente aggiuntivi mantenuti dal provider di identità. È possibile ottenere gli stessi dati utente nel servizio mobile chiamando la funzione **user.getIdentities()** negli script del server. Per altre informazioni, vedere [questo post](http://go.microsoft.com/fwlink/p/?LinkId=506605).

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

Nella prossima esercitazione, [Autorizzazione sul lato servizio degli utenti di Servizi mobili](mobile-services-javascript-backend-service-side-authorization.md), il valore dell'ID utente fornito da Servizi mobili e basato su un utente autenticato verrà utilizzato per filtrare i dati restituiti da Servizi mobili. Per ulteriori informazioni su come utilizzare Servizi mobili con .NET, vedere [Riferimento per i concetti e le procedure di .NET per Servizi mobili]

<!-- Anchors. -->
[Registrare l'app per l'autenticazione e configurare Servizi mobili]: #register
[Limitare le autorizzazioni per la tabella agli utenti autenticati]: #permissions
[Aggiungere l'autenticazione all'app]: #add-authentication
[Archiviare i token di autenticazione sul client]: #tokens
[Next Steps]: #next-steps


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Introduzione a Servizi mobili]: mobile-services-javascript-backend-windows-store-dotnet-get-started.md
[Get started with data]: ../mobile-services-javascript-backend-windows-store-dotnet-get-started-data.md
[Get started with authentication]: ../mobile-services-javascript-backend-windows-store-dotnet-get-started-users.md
[Get started with push notifications]: ../mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md
[Authorize users with scripts]: ../mobile-services-windows-store-dotnet-authorize-users-in-scripts.md
[JavaScript and HTML]: mobile-services-windows-store-javascript-get-started-users.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Riferimento per i concetti e le procedure di .NET per Servizi mobili]: mobile-services-windows-dotnet-how-to-use-client-library.md
[Register your Windows Store app package for Microsoft authentication]: ../mobile-services-how-to-register-store-app-package-microsoft-authentication.md

<!--HONumber=54--> 