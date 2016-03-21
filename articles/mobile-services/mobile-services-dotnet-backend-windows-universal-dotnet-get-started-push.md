<properties
	pageTitle="Aggiungere notifiche push all'app per piattaforma UWP 8.1 | Microsoft Azure"
	description="Informazioni su come inviare notifiche push all'app per piattaforma UWP 8.1 dal servizio mobile back-end .NET usando Hub di notifica di Azure."
	services="mobile-services,notification-hubs"
	documentationCenter="windows"
	authors="ggailey777"
	manager="erikre"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-store"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/05/2016"
	ms.author="glenga"/>

# Aggiungere notifiche push all'app di Servizi mobili

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Per la versione equivalente di questo argomento per le app per dispositivi mobili, vedere [Aggiungere notifiche push all'app Windows](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md).

##Panoramica
In questo argomento viene illustrato come usare Servizi mobili di Azure con un back-end .NET per inviare notifiche push a un'app universale di Windows. In questa esercitazione si usa Hub di notifica di Azure per abilitare le notifiche push nel progetto di app universale di Windows. Al termine dell'esercitazione, il servizio mobile in uso invierà una notifica push dal back-end .NET a tutte le app di Windows Store e Windows Phone Store registrate ogni volta che viene inserito un record nella tabella TodoList. L'hub di notifica creato può essere utilizzato gratuitamente con il servizio mobile, può essere gestito indipendentemente da quest'ultimo e può essere utilizzato da altri servizi e applicazioni.

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un [account di Microsoft Store](http://go.microsoft.com/fwlink/p/?LinkId=280045) attivo.
* [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934).

##<a id="register"></a>Registrare l'app per le notifiche push

[AZURE.INCLUDE [mobile-services-create-new-push-vs2013](../../includes/mobile-services-create-new-push-vs2013.md)]

&nbsp;&nbsp;6. Passare alla cartella del progetto `\Services\MobileServices\your_service_name`, aprire il file di codice push.register.cs generato ed esaminare il metodo **UploadChannel** che registra l'URL del canale del dispositivo con l'hub di notifica.

&nbsp;&nbsp;7. Aprire il file di codice App.xaml.cs condiviso. Si noti come una chiamata al nuovo metodo **UploadChannel** è stata aggiunta al gestore dell'evento **OnLaunched**. In questo modo verrà effettuato un tentativo di registrazione del dispositivo a ogni avvio dell'app.

&nbsp;&nbsp;8. Ripetere i passaggi precedenti per aggiungere notifiche push al progetto di app di Windows Phone Store, quindi nel file App.xaml.cs condiviso rimuovere la chiamata supplementare a **UploadChannel** e il wrapper condizionale `#if...#endif` restante. Ora entrambi i progetti possono condividere un'unica chiamata a **UploadChannel**.

> [AZURE.NOTE] È anche possibile semplificare il codice generato unendo le definizioni con wrapping `#if...#endif` [MobileServiceClient](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx) in un'unica definizione senza wrapping usata da entrambe le versioni dell'app.

Ora che le notifiche push sono abilitate nell'app, è necessario aggiornare il servizio mobile per l'invio delle notifiche push.

##<a id="update-service"></a>Aggiornare il servizio per l'invio di notifiche push

Seguire i seguenti passaggi per aggiornare la classe TodoItemController esistente per inviare una notifica push a tutti i dispositivi registrati quando viene inserito un nuovo elemento. È possibile implementare un codice simile in qualsiasi [ApiController](https://msdn.microsoft.com/library/system.web.http.apicontroller.aspx) e [TableController](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.tables.tablecontroller.aspx) personalizzato o in qualsiasi altro punto dei servizi back-end.

[AZURE.INCLUDE [mobile-services-dotnet-backend-update-server-push](../../includes/mobile-services-dotnet-backend-update-server-push.md)]

##<a id="local-testing"></a> Abilitare le notifiche push per i test locali

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push-vs2013](../../includes/mobile-services-dotnet-backend-configure-local-push-vs2013.md)]

I seguenti passaggi di questa sezione sono facoltativi e consentono di testare l'app con il proprio servizio mobile in esecuzione su un computer locale. Se si desidera testare le notifiche push usando il servizio mobile in esecuzione su Azure, è possibile passare direttamente all'ultima sezione, in quanto la procedura guidata Aggiungi notifica Push ha già configurato l'app per la connessione al proprio servizio in esecuzione su Azure.

>[AZURE.NOTE]Non usare mai un servizio mobile di produzione per operazioni di test e sviluppo. Pubblicare sempre il proprio progetto di servizio mobile in un ambiente di gestione temporanea per i test.

&nbsp;&nbsp;5. Aprire il file di progetto App.xaml.cs condiviso e individuare qualsiasi riga di codice che crei una nuova istanza della classe [MobileServiceClient] per accedere al servizio mobile in esecuzione su Azure.

&nbsp;&nbsp;6. Impostare questo codice come commento e aggiungere codice per creare una nuova classe [MobileServiceClient] con lo stesso nome, ma che usa l'URL dell'host locale nel costruttore, analogamente a quanto segue:

	// This MobileServiceClient has been configured to communicate with your local
	// test project for debugging purposes.
	public static MobileServiceClient todolistClient = new MobileServiceClient(
		"http://localhost:4584"
	);

&nbsp;&nbsp;Usando questo [MobileServiceClient], l'app si connetterà al servizio locale anziché alla versione ospitata in Azure. Quando si desidera tornare indietro ed eseguire l'app sul servizio mobile ospitato in Azure, tornare alle definizioni di [MobileServiceClient] originali.

##<a id="test"></a> Testare le notifiche push nell'app

[AZURE.INCLUDE [mobile-services-dotnet-backend-windows-universal-test-push](../../includes/mobile-services-dotnet-backend-windows-universal-test-push.md)]

## <a name="next-steps"> </a>Passaggi successivi

In questa esercitazione vengono illustrate le nozioni di base per consentire a un'app di Windows Store di utilizzare Servizi mobili e Hub di notifica per l'invio delle notifiche push. In seguito si consiglia di passare all'esercitazione seguente, [Invio di notifiche push agli utenti autenticati], che spiega come utilizzare i tag per inviare notifiche push da un servizio mobile unicamente agli utenti autenticati.

Per altre informazioni su Servizi mobili e su Hub di notifica, fare riferimento ai seguenti argomenti:

* [Aggiungere Servizi mobili a un'app esistente][Get started with data] <br/>Altre informazioni sull'archiviazione e l'esecuzione di query sui dati tramite Servizi mobili.

* [Aggiungere l'autenticazione all'app][Get started with authentication] <br/>Informazioni sull'autenticazione degli utenti dell'app con tipi di account diversi mediante i servizi mobili.

* [Informazioni su Hub di notifica] <br/>Altre informazioni sull'uso di Hub di notifica per recapitare le notifiche alle app in tutte le principali piattaforme client.

* [Eseguire il debug delle applicazioni di Hub di notifica](http://go.microsoft.com/fwlink/p/?linkid=386630) </br>Informazioni aggiuntive sulla risoluzione dei problemi e sul debug di soluzioni di Hub di notifica.

* [Come usare un client .NET per Servizi mobili di Azure] <br/>Altre informazioni su come usare Servizi mobili dalle app C# per Windows.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Get started with data]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data.md
[Get started with authentication]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users.md

[Invio di notifiche push agli utenti autenticati]: mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users.md

[Informazioni su Hub di notifica]: ../notification-hubs-overview.md

[Come usare un client .NET per Servizi mobili di Azure]: mobile-services-windows-dotnet-how-to-use-client-library.md
[MobileServiceClient]: http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx

<!---HONumber=AcomDC_0309_2016-->