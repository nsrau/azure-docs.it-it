<properties linkid="develop-mobile-tutorials-get-started-with-push-js-vs2013" urlDisplayName="Get Started with Push (JS)" pageTitle="Get started with push notifications (Windows Store JavaScript) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Windows Store JavaScript app." metaCanonical="http://www.windowsazure.com/it-it/develop/mobile/tutorials/get-started-with-push-dotnet/" services="" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

Introduzione alle notifiche push in Servizi mobili
==================================================

[Windows Store C\#](/it-it/documentation/articles/mobile-services-windows-store-dotnet-get-started-push "Windows Store C#")[Windows Store JavaScript](/it-it/documentation/articles/mobile-services-windows-store-javascript-get-started-push "Windows Store JavaScript")[Windows Phone](/it-it/documentation/articles/mobile-services-windows-phone-get-started-push "Windows Phone")[iOS](/it-it/documentation/articles/mobile-services-ios-get-started-push "iOS")[Android](/it-it/documentation/articles/mobile-services-android-get-started-push "Android")[Xamarin.iOS](/it-it/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push "Xamarin.iOS")[Xamarin.Android](/it-it/documentation/articles/partner-xamarin-mobile-services-android-get-started-push "Xamarin.Android")
[Back-end .NET](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/ "Back-end .NET") | [Back-end JavaScript](/it-it/documentation/articles/mobile-services-windows-store-javascript-get-started-push/ "Back-end JavaScript")

In questo argomento viene illustrato come utilizzare Servizi mobili di Azure in Visual Studio 2013 per inviare notifiche push all'app di Windows Store. In questa esercitazione si utilizzerà Servizi notifica Push Windows per aggiungere notifiche push al progetto di guida introduttiva, direttamente da Visual Studio. Al termine dell'esercitazione, il servizio mobile invierà una notifica push ogni volta che viene inserito un record.

> [WACOM.NOTE]Servizi mobili è ora integrato con Hub di notifica di Azure e supporta ulteriori funzionalità di notifica push, ad esempio modelli, più piattaforme e scalabilità. Questa funzionalità integrata è attualmente disponibile in anteprima. Per ulteriori informazioni, vedere questa versione di [Introduzione alle notifiche push](/it-it/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push/).

In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

1.  [Registrazione dell'app per le notifiche push e configurazione di Servizi mobili](#register)
2.  [Aggiornamento del codice delle notifiche push generato](#update-scripts)
3.  [Inserimento di dati per la ricezione di notifiche](#test)

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare l'esercitazione, è necessario completare [Introduzione a Servizi mobili](/en-us/develop/mobile/tutorials/get-started/) o [Introduzione ai dati](/en-us/develop/mobile/tutorials/get-started-with-data-js/) per collegare il progetto al servizio mobile. Se non è stato collegato alcun servizio mobile, la procedura guidata Aggiungi notifica push crea automaticamente la connessione.

Registrazione dell'appAggiunta e configurazione delle notifiche push nell'app
-----------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

1.  Aprire il file di codice push.register.js generato e quindi controllare il codice che ottiene l'ID dell'installazione e il canale del dispositivo e inserisce questi dati nella nuova tabella **channels**.

    Questa tabella è stata creata nel servizio mobile tramite la procedura guidata Aggiungi notifica push. In questo modo è possibile garantire che la registrazione del dispositivo venga tentata ogni volta che l'app viene attivata.

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

2.  Nell'app digitare un testo in **Insert a TodoItem**, quindi fare clic su **Save**.

   	![][13]

   	Si noti che al termine dell'inserimento l'app riceve una notifica push da WNS.

   	![][14]

Passaggi successivi
-------------------

In questa esercitazione viene illustrata la funzionalità di notifica push di base fornita da Servizi mobili. Se per l'app sono richieste funzionalità più avanzate, ad esempio l'invio di notifiche tra piattaforme diverse, il routing basato sulla sottoscrizione o la gestione di volumi molto elevati, con il servizio mobile è consigliabile utilizzare Hub di notifica di Azure. Per ulteriori informazioni, vedere uno degli argomenti seguenti relativi a Hub di notifica:

-   [Introduzione ad Hub di notifica](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet/)

    Informazioni su come sfruttare Hub di notifica nell'app di Windows Store.

-   [Invio di notifiche ai sottoscrittori](/en-us/manage/services/notification-hubs/breaking-news-dotnet/)

    Informazioni su come gli utenti possono registrarsi e ricevere notifiche push per le categorie cui sono interessati.

-   [Invio di notifiche agli utenti](/en-us/manage/services/notification-hubs/notify-users/)

    Informazioni su come inviare notifiche push da un servizio mobile a utenti specifici su qualsiasi dispositivo.

-   [Invio di notifiche su piattaforme diverse agli utenti](/en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/)

    Informazioni su come utilizzare i modelli per inviare notifiche push da un servizio mobile, senza che sia necessario creare payload specifici della piattaforma nel back-end.

Per ulteriori informazioni, vedere anche i seguenti argomenti su Servizi mobili:

-   [Introduzione ai dati](/en-us/develop/mobile/tutorials/get-started-with-data-js/)

    Ulteriori informazioni sull'archiviazione e sulle query dei dati mediante Servizi mobili.

-   [Introduzione all'autenticazione](/en-us/develop/mobile/tutorials/get-started-with-users-js)

    Informazioni sull'autenticazione degli utenti dell'app con l'account Windows.

-   [Riferimento per gli script del server di Servizi mobili](http://go.microsoft.com/fwlink/?LinkId=262293)

    Ulteriori informazioni sulla registrazione e l'utilizzo di script del server.

-   [Riferimento per i concetti e le procedure di Servizi mobili con HTML/JavaScript](/en-us/develop/mobile/how-to-guides/work-with-html-js-client/)

    Ulteriori informazioni su come utilizzare Servizi mobili con HTML e JavaScript.

<!-- Images. -->



[13]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push2.png

