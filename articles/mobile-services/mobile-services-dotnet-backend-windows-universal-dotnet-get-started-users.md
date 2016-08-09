<properties
	pageTitle="Aggiungere l'autenticazione all'app della piattaforma UWP 8.1 | Microsoft Azure"
	description="Informazioni su come usare i Servizi mobili per autenticare utenti dell'app della piattaforma UWP 8.1, usando diversi provider di identità, inclusi Google, Facebook, Twitter e Microsoft."
	services="mobile-services"
	documentationCenter="windows"
	authors="ggailey777"
	manager="erikre"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="glenga"/>

# Aggiungere l'autenticazione all'app di Servizi mobili
[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Per la versione equivalente di questo argomento per le app per dispositivi mobili, vedere [Aggiungere l'autenticazione all'app Windows](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-users.md).

## Panoramica

Questo argomento illustra come autenticare gli utenti in Servizi mobili di Azure dalla propria app universale di Windows. Nell'esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite un provider di identità supportato da Servizi mobili. In seguito all'autenticazione e all'autorizzazione di Servizi mobili, viene visualizzato il valore dell'ID utente.

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. È inoltre necessario completare prima l'esercitazione [Introduzione a Servizi mobili] o [Aggiungere Servizi mobili a un'app esistente](mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data.md).

>[AZURE.NOTE]Questa esercitazione illustra come usare l'autenticazione verso il server per gli utenti in app per Windows Store e Windows Phone Store 8.1. Per altre informazioni sull'autenticazione verso il client, vedere [Accesso a Servizi mobili di Azure mediante Google, Microsoft e Facebook SDK](https://azure.microsoft.com/blog/2014/10/27/logging-in-with-google-microsoft-and-facebook-sdks-to-azure-mobile-services/).

##<a name="register"></a>Registrare l'app per l'autenticazione e configurare Servizi mobili

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../../includes/mobile-services-dotnet-backend-aad-server-extension.md)]

##<a name="permissions"></a>Limitare le autorizzazioni agli utenti autenticati

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

&nbsp;&nbsp;6. In Visual Studio fare clic con il pulsante destro del mouse sul progetto Windows Store per l'app TodoList, quindi selezionare **Imposta come progetto di avvio**.

&nbsp;&nbsp;7. Nel progetto condiviso, aprire il file di progetto App.xaml.cs, individuare la definizione di [MobileServiceClient](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx) e assicurarsi che sia configurata in modo tale da effettuare la connessione al servizio mobile in esecuzione su Azure.

>[AZURE.NOTE]In caso di utilizzo di uno strumento di Visual Studio per connettere la propria app a un servizio mobile, lo strumento genera due set di definizioni **MobileServiceClient**, uno per ciascuna piattaforma client. Si consiglia di semplificare il codice generato unendo le definizioni **MobileServiceClient** `#if...#endif` con wrapping in un'unica definizione senza wrapping usata da entrambe le versioni dell'app. Non è necessario eseguire questa operazione quando l'app di guida introduttiva è stata scaricata dal [portale di Azure classico].

&nbsp;&nbsp;8. Premere F5 per eseguire l'app di Windows Store. Verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (non autorizzato).

&nbsp;&nbsp;L'eccezione non gestita viene generata perché l'app tenta di accedere a Servizi mobili come utente non autenticato, mentre la tabella *TodoItem* richiede ora l'autenticazione.

A questo punto, si aggiornerà l'app in modo che autentichi gli utenti prima di richiedere risorse al servizio mobile.

##<a name="add-authentication"></a>Aggiungere l'autenticazione all'app

[AZURE.INCLUDE [mobile-windows-universal-dotnet-authenticate-app](../../includes/mobile-windows-universal-dotnet-authenticate-app.md)]

>[AZURE.NOTE]Se le informazioni del pacchetto dell'app di Windows Store sono state registrate con Servizi mobili, è necessario chiamare il metodo <a href="http://go.microsoft.com/fwlink/p/?LinkId=311594" target="_blank">LoginAsync</a> specificando il valore **true** per il parametro *useSingleSignOn*. In caso contrario, gli utenti dovranno comunque specificare le credenziali di accesso ogni volta che viene chiamato il metodo di accesso.

##<a name="tokens"></a>Archiviare i token di autorizzazione sul client

Nell'esempio precedente è stato illustrato un accesso standard, che richiede al client di contattare sia il provider di identità sia il servizio mobile ogni volta che l'app viene avviata. Oltre a essere inefficiente, questo metodo potrebbe creare problemi di utilizzo qualora molti clienti provassero ad avviare l'app contemporaneamente. Un miglior approccio consiste nel memorizzare nella cache il token di autorizzazione restituito dai Servizi mobili e provare a usare questo prima di usare un accesso basato su provider.

>[AZURE.NOTE]È possibile memorizzare nella cache il token rilasciato dai Servizi mobili indipendentemente dal fatto che si usi l'autenticazione gestita dal client o gestita dal servizio. In questa esercitazione viene usata l'autenticazione gestita dal servizio.

[AZURE.INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]


## <a name="next-steps"> </a>Passaggi successivi

Nella prossima esercitazione, [Autorizzazione sul lato servizio degli utenti di Servizi mobili][Authorize users with scripts], il valore dell'ID utente fornito da Servizi mobili e basato su un utente autenticato verrà utilizzato per filtrare i dati restituiti da Servizi mobili.

##Vedere anche

+ [Funzionalità utenti avanzate](https://azure.microsoft.com/blog/2014/10/02/custom-login-scopes-single-sign-on-new-asp-net-web-api-updates-to-the-azure-mobile-services-net-backend/)<br/> È possibile ottenere dati utente aggiuntivi gestiti dal provider di identità nel servizio mobile chiamando il metodo **ServiceUser.GetIdentitiesAsync()** in un back-end .NET.

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
[Introduzione a Servizi mobili]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Get started with data]: mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md
[Get started with authentication]: mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md
[Get started with push notifications]: mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md
[Authorize users with scripts]: mobile-services-dotnet-backend-service-side-authorization.md
[JavaScript and HTML]: mobile-services-dotnet-backend-windows-store-javascript-get-started-users.md

[portale di Azure classico]: https://manage.windowsazure.com/
[Riferimento per i concetti e le procedure di Servizi mobili con .NET]: mobile-services-windows-dotnet-how-to-use-client-library.md
[Register your Windows Store app package for Microsoft authentication]: mobile-services-how-to-register-store-app-package-microsoft-authentication.md

<!---HONumber=AcomDC_0727_2016-->