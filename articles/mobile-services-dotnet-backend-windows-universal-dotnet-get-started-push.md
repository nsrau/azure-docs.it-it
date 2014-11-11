<properties pageTitle="Get started with push notification using a .NET backend mobile service" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your universal Windows app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="mobile" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/11/2014" ms.author="glenga"></tags>

# Introduzione alle notifiche push in Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

In questo argomento viene illustrato come utilizzare Servizi mobili di Azure con un back-end .NET per inviare notifiche push a un'app universale di Windows. In questa esercitazione si utilizzerà Hub di notifica di Azure per abilitare le notifiche push nel progetto di app universale di Windows. Al termine dell'esercitazione, il servizio mobile in uso invierà una notifica push dal back-end .NET a tutte le app di Windows Store e Windows Phone Store registrate ogni volta che viene inserito un record nella tabella TodoList. L'hub di notifica creato può essere utilizzato gratuitamente con il servizio mobile, può essere gestito indipendentemente da quest'ultimo e può essere utilizzato da altri servizi e applicazioni.

> [WACOM.NOTE]In questo argomento viene illustrato come utilizzare gli strumenti in Visual Studio Professional 2013 con aggiornamento 3 per aggiungere il supporto per le notifiche push di Servizi mobili a un'app universale di Windows. La stessa procedura può essere eseguita per aggiungere le notifiche push da Servizi mobili a un'app Windows Store o Windows Phone Store 8.1. Per aggiungere le notifiche push a un'app di Windows Phone 8 o Windows Phone Silverlight 8.1, vedere questa versione della guida [Introduzione alle notifiche push in Servizi mobili][Introduzione alle notifiche push in Servizi mobili].

> Se non è possibile eseguire l'aggiornamento a Visual Studio Professional 2013 aggiornamento 3 o se si preferisce aggiungere manualmente il proprio progetto mobile a una soluzione di app Windows Store, consultare [questa versione][questa versione] dell'argomento.

In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

1.  [Registrazione dell'app per le notifiche push][Registrazione dell'app per le notifiche push]
2.  [Aggiornamento del servizio per l'invio di notifiche push][Aggiornamento del servizio per l'invio di notifiche push]
3.  [Abilitare le notifiche push per test locali][Abilitare le notifiche push per test locali]
4.  [Esecuzione del test delle notifiche push nell'app][Esecuzione del test delle notifiche push nell'app]

Per completare l'esercitazione, sono necessari gli elementi seguenti:

-   Un [account di Microsoft Store][account di Microsoft Store] attivo.
-   [Visual Studio Professional 2013][Visual Studio Professional 2013] (aggiornamento 3 o versione successiva).
    È disponibile una versione di valutazione gratuita.

## <span id="register"></span></a>Registrazione dell'app per le notifiche push

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

1.  Passare alla cartella di progetto `\Services\MobileServices\your_service_name`, aprire il file di codice push.register.cs generato ed esaminare il metodo **UploadChannel** che registra l'URL del canale del dispositivo con l'hub di notifica.

2.  Aprire il file di codice App.xaml.cs condiviso. Si noti come una chiamata al nuovo metodo **UploadChannel** è stata aggiunta al gestore dell'evento **OnLaunched**.

    In questo modo verrà effettuato un tentativo di registrazione del dispositivo a ogni avvio dell'app.

3.  Ripetere i passaggi precedenti per aggiungere notifiche push al progetto di app di Windows Phone Store. Quindi, nel file App.xaml.cs condiviso, rimuovere la chiamata supplementare a **UploadChannel** e il wrapper condizionale `#if...#endif` restante.

    Ora entrambi i progetti possono condividere un'unica chiamata a **UploadChannel**.

    <div class="dev-callout"><strong>Nota</strong> <p>&Egrave; inoltre possibile semplificare il codice generato unendo le definizioni <a href="http://msdn.microsoft.com/it-it/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a> <code data-inline="1">#if...#endif</code> con wrapping in un'unica definizione senza wrapping utilizzata da entrambe le versioni dell'app.</p></div>

Ora che le notifiche push sono abilitate nell'app, è necessario aggiornare il servizio mobile per l'invio delle notifiche push.

## <span id="update-service"></span></a>Aggiornamento del servizio per l'invio di notifiche push

Seguire i seguenti passaggi per aggiornare la classe TodoItemController esistente per inviare una notifica push a tutti i dispositivi registrati quando viene inserito un nuovo elemento. È possibile implementare un codice simile in qualsiasi [ApiController] e [TableController] personalizzato o in qualsiasi altro punto dei propri servizi di back-end.

[WACOM.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

## <span id="local-testing"></span></a> Abilitare le notifiche push per test locali

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push-vs2013](../includes/mobile-services-dotnet-backend-configure-local-push-vs2013.md)]

I seguenti passaggi di questa sezione sono facoltativi e consentono di testare l'app con il proprio servizio mobile in esecuzione su un computer locale. Se si desidera testare le notifiche push utilizzando il servizio mobile in esecuzione su Azure, è possibile passare direttamente all'ultima sezione, in quanto la procedura guidata Aggiungi notifica Push ha già configurato l'app per la connessione al proprio servizio in esecuzione su Azure.

> [WACOM.NOTE]Non utilizzare mai un servizio mobile di produzione per operazioni di test e sviluppo. Pubblicare sempre il proprio progetto di servizio mobile in un ambiente di gestione temporanea per i test.

1.  Aprire il file di progetto App.xaml.cs condiviso e individuare qualsiasi riga di codice che crei una nuova istanza della classe [MobileServiceClient][MobileServiceClient] per accedere al servizio mobile in esecuzione su Azure.

2.  Rimuovere quindi i simboli di commento dal codice e aggiungere codice per creare una nuova classe [MobileServiceClient][MobileServiceClient] con lo stesso nome, ma che utilizza l'URL dell'host locale nel costruttore, analogamente a quanto segue:

        // This MobileServiceClient has been configured to communicate with your local
        // test project for debugging purposes.
        public static MobileServiceClient todolistClient = new MobileServiceClient(
            "http://localhost:4584"
        );

    Utilizzando questo [MobileServiceClient][MobileServiceClient], l'app si connetterà al servizio locale anziché alla versione ospitata in Azure. Quando si desidera tornare indietro ed eseguire l'app sul servizio mobile ospitato in Azure, tornare alle definizioni di [MobileServiceClient][MobileServiceClient] originali.

## <span id="test"></span></a>Esecuzione del test delle notifiche push nell'app

[WACOM.INCLUDE [mobile-services-dotnet-backend-windows-universal-test-push](../includes/mobile-services-dotnet-backend-windows-universal-test-push.md)]

## <a name="next-steps"> </a>Passaggi successivi

In questa esercitazione vengono illustrate le nozioni di base per consentire a un'app di Windows Store di utilizzare Servizi mobili e Hub di notifica per l'invio delle notifiche push. In seguito si consiglia di passare all'esercitazione seguente, [Invio di notifiche push agli utenti autenticati][Invio di notifiche push agli utenti autenticati], che spiega come utilizzare i tag per inviare notifiche push da un servizio mobile unicamente agli utenti autenticati.

Per altre informazioni su Servizi mobili e su Hub di notifica, fare riferimento ai seguenti argomenti:

-   [Introduzione ai dati][Introduzione ai dati]
    Altre informazioni sull'archiviazione e sulle query dei dati mediante i servizi mobili.

-   [Introduzione all'autenticazione][Introduzione all'autenticazione]
    Informazioni sull'autenticazione degli utenti dell'app con tipi di account diversi mediante i servizi mobili.

-   [Informazioni su Hub di notifica][Informazioni su Hub di notifica]
    Altre informazioni sull'uso di Hub di notifica per recapitare le notifiche alle app in tutte le principali piattaforme client.

-   [Come utilizzare un client .NET per Servizi mobili di Azure][Come utilizzare un client .NET per Servizi mobili di Azure]
    Informazioni su come utilizzare Servizi mobili dalle app C# per Windows.


  [mobile-services-selector-get-started-push]: ../includes/mobile-services-selector-get-started-push.md
  [Introduzione alle notifiche push in Servizi mobili]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push
  [questa versione]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push
  [Registrazione dell'app per le notifiche push]: #register
  [Aggiornamento del servizio per l'invio di notifiche push]: #update-service
  [Abilitare le notifiche push per test locali]: #local-testing
  [Esecuzione del test delle notifiche push nell'app]: #test
  [account di Microsoft Store]: http://go.microsoft.com/fwlink/p/?LinkId=280045
  [Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=391934
  [mobile-services-create-new-push-vs2013]: ../includes/mobile-services-create-new-push-vs2013.md
  [MobileServiceClient]: http://msdn.microsoft.com/it-it/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx
  [mobile-services-dotnet-backend-update-server-push]: ../includes/mobile-services-dotnet-backend-update-server-push.md
  [mobile-services-dotnet-backend-configure-local-push-vs2013]: ../includes/mobile-services-dotnet-backend-configure-local-push-vs2013.md
  [mobile-services-dotnet-backend-windows-universal-test-push]: ../includes/mobile-services-dotnet-backend-windows-universal-test-push.md
  [Invio di notifiche push agli utenti autenticati]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users/
  [Introduzione ai dati]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data
  [Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users
  [Informazioni su Hub di notifica]: /it-it/documentation/articles/notification-hubs-overview/
  [Come utilizzare un client .NET per Servizi mobili di Azure]: /it-it/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
