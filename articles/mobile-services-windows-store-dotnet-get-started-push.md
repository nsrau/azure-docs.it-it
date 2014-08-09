<properties pageTitle="Get started with push notifications (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Windows Store app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

Introduzione alle notifiche push in Servizi mobili
==================================================

[Windows Store C\#](/it-it/documentation/articles/mobile-services-windows-store-dotnet-get-started-push "Windows Store C#")[Windows Store JavaScript](/it-it/documentation/articles/mobile-services-windows-store-javascript-get-started-push "Windows Store JavaScript")[Windows Phone](/it-it/documentation/articles/mobile-services-windows-phone-get-started-push "Windows Phone")[iOS](/it-it/documentation/articles/mobile-services-ios-get-started-push "iOS")[Android](/it-it/documentation/articles/mobile-services-android-get-started-push "Android")[Xamarin.iOS](/it-it/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push "Xamarin.iOS")[Xamarin.Android](/it-it/documentation/articles/partner-xamarin-mobile-services-android-get-started-push "Xamarin.Android")

[Back-end .NET](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/ "Back-end .NET") | [Back-end JavaScript](/it-it/documentation/articles/mobile-services-windows-store-dotnet-get-started-push/ "Back-end JavaScript")

In questo argomento viene illustrato come utilizzare Servizi mobili di Azure in Visual Studio 2013 per inviare notifiche push all'app di Windows Store. In questa esercitazione si utilizzerà Servizi notifica Push Windows per aggiungere notifiche push al progetto di guida introduttiva, direttamente da Visual Studio. Al termine dell'esercitazione, il servizio mobile invierà una notifica push ogni volta che viene inserito un record.

> [WACOM.NOTE]Servizi mobili è ora integrato con Hub di notifica di Azure e supporta ulteriori funzionalità di notifica push, ad esempio modelli, più piattaforme e scalabilità. Questa funzionalità integrata è attualmente disponibile in anteprima. Per ulteriori informazioni, vedere questa versione di [Introduzione alle notifiche push](/it-it/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/).

In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

1.  [Registrazione dell'app per le notifiche push e configurazione di Servizi mobili](#register)
2.  [Aggiornamento del codice delle notifiche push generato](#update-scripts)
3.  [Inserimento di dati per la ricezione di notifiche](#test)

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare l'esercitazione, è necessario completare [Introduzione a Servizi mobili](/en-us/develop/mobile/tutorials/get-started/) o [Introduzione ai dati](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet/) per collegare il progetto al servizio mobile. Se non è stato collegato alcun servizio mobile, la procedura guidata Aggiungi notifica push crea automaticamente la connessione.

Registrazione dell'appAggiunta e configurazione delle notifiche push nell'app
-----------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

1.  Espandere **services**, **mobile services**, il nome del proprio servizio, aprire il file di codice generato e quindi controllare il metodo **UploadChannel** che ottiene l'ID dell'installazione e il canale del dispositivo e inserisce questi dati nella nuova tabella channels.

    Anche la procedura guidata aggiunge una chiamata a questo metodo al gestore dell'evento **OnLaunched** nel file di codice App.xaml.cs. In questo modo verrà effettuato un tentativo di registrazione del dispositivo a ogni avvio dell'app.

2.  In Esplora server espandere **Azure**, **Mobile Services**, il nome del servizio, **channels**, quindi aprire il file insert.js.

    Questo file, archiviato nel servizio mobile, contiene codice JavaScript che viene eseguito quando un client invia una richiesta per la registrazione di un dispositivo inserendo dati nella tabella channels.

    **Nota**

    La versione iniziale di questo file contiene codice che verifica la presenza di una registrazione esistente per il dispositivo. Contiene inoltre codice che invia una notifica push quando viene aggiunta una nuova registrazione alla tabella channels. Il codice che invia una notifica push può essere incluso in qualsiasi file di script registrato. Il percorso di questo script dipende da come viene attivata la notifica. Gli script possono essere registrati con un'operazione insert, update, delete o read su una tabella, come processo pianificato o come API personalizzata. Per ulteriori informazioni, vedere [Utilizzo degli script del server in Servizi mobili](http://go.microsoft.com/fwlink/p/?LinkID=287178).

3.  Premere F5 per eseguire l'app e verificare che venga immediatamente ricevuta una notifica dal servizio mobile.

    Questa notifica è stata generata inserendo una riga nella nuova tabella channels, corrispondente alla registrazione del dispositivo.

Il codice generato semplifica la dimostrazione dell'invio di una notifica durante l'esecuzione dell'app, ma in genere non si tratta di uno scenario significativo. Si rimuoverà quindi il codice delle notifiche dalla tabella channels e lo si sostituirà, con qualche modifica, nella tabella TodoItem.

Aggiornamento del codiceAggiornamento del codice delle notifiche push generato
------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013-2](../includes/mobile-services-create-new-push-vs2013-2.md)]

Test dell'appEsecuzione del test delle notifiche push nell'app
--------------------------------------------------------------

1.  In Visual Studio premere F5 per eseguire l'app.

2.  Nell'app digitare un testo in **Insert a TodoItem**, quindi fare clic su **Save**

   	![][13]

   	Si noti che al termine dell'inserimento l'app riceve una notifica push da WNS.

   	![][14]

Passaggi successivi
-------------------

In questa esercitazione vengono illustrate le nozioni di base per consentire a un'app di Windows Store di utilizzare dati in Servizi mobili. In seguito, è consigliabile eseguire una delle esercitazioni seguenti, basate sull'app GetStartedWithData creata in questa esercitazione:

-   [Introduzione ad Hub di notifica](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet/)
    <br/>Informazioni su come sfruttare Hub di notifica nell'app di Windows Store.

-   [Invio di notifiche ai sottoscrittori](/en-us/manage/services/notification-hubs/breaking-news-dotnet/)
    <br/>Informazioni su come gli utenti possono registrarsi e ricevere notifiche push per le categorie cui sono interessati.

-   [Invio di notifiche agli utenti](/en-us/manage/services/notification-hubs/notify-users/)
    <br/>Informazioni su come inviare notifiche push da un servizio mobile a utenti specifici su qualsiasi dispositivo.

-   [Invio di notifiche su piattaforme diverse agli utenti](/en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/)
    <br/>Informazioni su come utilizzare i modelli per inviare notifiche push da un servizio mobile, senza che sia necessario creare payload specifici della piattaforma nel back-end.

Per ulteriori informazioni, vedere anche i seguenti argomenti su Servizi mobili:

-   [Introduzione ai dati](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet/)
    <br/>Ulteriori informazioni sull'archiviazione e sulle query dei dati mediante Servizi mobili.

-   [Introduzione all'autenticazione](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet)
    <br/>Informazioni sull'autenticazione degli utenti dell'app con l'account Windows.

-   [Riferimento per gli script del server di Servizi mobili](http://go.microsoft.com/fwlink/?LinkId=262293)
    <br/>Ulteriori informazioni sulla registrazione e l'utilizzo di script del server.

-   [Riferimento per i concetti e le procedure di Servizi mobili con .NET](/en-us/develop/mobile/how-to-guides/work-with-net-client-library/)
    <br/>Ulteriori informazioni su come utilizzare Servizi mobili con .NET.


[13]: ./media/mobile-services-windows-store-dotnet-get-started-push/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-dotnet-get-started-push/mobile-quickstart-push2.png