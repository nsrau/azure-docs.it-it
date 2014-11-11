<properties pageTitle="Get started with push notifications (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Windows Store app (legacy push)." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services (legacy push)" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/11/2014" ms.author="glenga" />

# Introduzione alle notifiche push in Servizi mobili (push legacy)

<div class="dev-center-tutorial-selector sublanding">

[Windows Store C#][Windows Store C#] [Windows Store JavaScript][Windows Store JavaScript] [Windows Phone][Windows Phone] [iOS][iOS] [Android][Android] <!--    <a href="/it-it/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a>     <a href="/it-it/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a> --> [Appcelerator][Appcelerator]

</div>

<div class="dev-center-tutorial-subselector">

[Back-end .NET][Back-end .NET] | [Back-end JavaScript][Back-end JavaScript]

</div>

In questo argomento viene illustrato come utilizzare Servizi mobili di Azure in Visual Studio 2013 per inviare notifiche push all'app di Windows Store. In questa esercitazione si utilizzerà Servizi notifica Push Windows per aggiungere notifiche push al progetto di guida introduttiva, direttamente da Visual Studio. Al termine dell'esercitazione, il servizio mobile invierà una notifica push ogni volta che viene inserito un record.

> [WACOM.NOTE]Questo argomento riguarda i servizi mobili *esistenti* che *non sono stati ancora aggiornati* per l'integrazione di Hub di notifica. Quando si crea un *nuovo* servizio mobile, questa funzionalità integrata viene abilitata automaticamente. Per i nuovi servizi mobili, vedere [Introduzione alle notifiche push][Introduzione alle notifiche push].
>
> Servizi mobili è integrato con Hub di notifica di Azure per supportare altre funzionalità di notifica push, ad esempio modelli, più piattaforme e maggiore scalabilità. *È consigliabile aggiornare i servizi mobili esistenti per l'uso di Hub di notifica, se possibile*. Una volta eseguito l'aggiornamento, vedere questa versione di [Introduzione alle notifiche push][Introduzione alle notifiche push].

In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

1.  [Registrazione dell'app per le notifiche push e configurazione di Servizi mobili][Registrazione dell'app per le notifiche push e configurazione di Servizi mobili]
2.  [Aggiornamento del codice delle notifiche push generato][Aggiornamento del codice delle notifiche push generato]
3.  [Inserimento di dati per la ricezione di notifiche][Inserimento di dati per la ricezione di notifiche]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare l'esercitazione, è necessario completare [Introduzione a Servizi mobili][Introduzione a Servizi mobili] o [Introduzione ai dati][Introduzione ai dati] per collegare il progetto al servizio mobile. Se non è stato collegato alcun servizio mobile, la procedura guidata Aggiungi notifica push crea automaticamente la connessione.

## <a name="register"></a><span class="short-header">Registrazione dell'app</span>Aggiunta e configurazione delle notifiche push nell'app

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

1.  Espandere **services**, **mobile services**, il nome del proprio servizio, aprire il file di codice generato e quindi controllare il metodo **UploadChannel** che ottiene l'ID dell'installazione e il canale del dispositivo e inserisce questi dati nella nuova tabella channels.

    Anche la procedura guidata aggiunge una chiamata a questo metodo al gestore dell'evento **OnLaunched** nel file di codice App.xaml.cs. In questo modo verrà effettuato un tentativo di registrazione del dispositivo a ogni avvio dell'app.

2.  In Esplora server espandere**Azure**, **Mobile Services**, il nome del servizio, **channels**, quindi aprire il file insert.js.

    Questo file, archiviato nel servizio mobile, contiene codice JavaScript che viene eseguito quando un client invia una richiesta per la registrazione di un dispositivo inserendo dati nella tabella channels.

    <div class="dev-callout">

    **Nota**
    La versione iniziale di questo file contiene codice che verifica la presenza di una registrazione esistente per il dispositivo. Contiene inoltre codice che invia una notifica push quando viene aggiunta una nuova registrazione alla tabella channels. Il codice che invia una notifica push può essere incluso in qualsiasi file di script registrato. Il percorso di questo script dipende da come viene attivata la notifica. Gli script possono essere registrati con un'operazione insert, update, delete o read su una tabella, come processo pianificato o come API personalizzata. Per ulteriori informazioni, vedere [Utilizzo degli script del server in Servizi mobili][Utilizzo degli script del server in Servizi mobili].

    </div>

3.  Premere F5 per eseguire l'app e verificare che venga immediatamente ricevuta una notifica dal servizio mobile.

    Questa notifica è stata generata inserendo una riga nella nuova tabella channels, corrispondente alla registrazione del dispositivo.

Il codice generato semplifica la dimostrazione dell'invio di una notifica durante l'esecuzione dell'app, ma in genere non si tratta di uno scenario significativo. Si rimuoverà quindi il codice delle notifiche dalla tabella channels e lo si sostituirà, con qualche modifica, nella tabella TodoItem.

## <a name="update-scripts"></a><span class="short-header">Aggiornamento del codice</span>Aggiornamento del codice delle notifiche push generato

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013-2](../includes/mobile-services-create-new-push-vs2013-2.md)]

## <a name="test"></a><span class="short-header">Test dell'app</span>Esecuzione del test delle notifiche push nell'app

1.  In Visual Studio premere F5 per eseguire l'app.

2.  Nell'app digitare un testo in **Insert a TodoItem**, quindi fare clic su **Save**.

    ![][0]

    Si noti che al termine dell'inserimento l'app riceve una notifica push da WNS.

    ![][1]

## <a name="next-steps"> </a>Passaggi successivi

In questa esercitazione vengono illustrate le nozioni di base per consentire a un'app di Windows Store di utilizzare dati in Servizi mobili. In seguito, è consigliabile eseguire una delle esercitazioni seguenti, basate sull'app GetStartedWithData creata in questa esercitazione:

-   [Introduzione ad Hub di notifica][Introduzione ad Hub di notifica]
    Informazioni su come sfruttare Hub di notifica nell'app di Windows Store.

-   [Invio di notifiche ai sottoscrittori][Invio di notifiche ai sottoscrittori]
    Informazioni su come gli utenti possono registrarsi e ricevere notifiche push per le categorie cui sono interessati.

-   [Invio di notifiche agli utenti][Invio di notifiche agli utenti]
    Informazioni su come inviare notifiche push da un servizio mobile a utenti specifici su qualsiasi dispositivo.

-   [Invio di notifiche su piattaforme diverse agli utenti][Invio di notifiche su piattaforme diverse agli utenti]
    Informazioni su come usare i modelli per inviare notifiche push da un servizio mobile senza che sia necessario creare payload specifici della piattaforma nel back-end.

Per ulteriori informazioni, vedere anche i seguenti argomenti su Servizi mobili:

-   [Introduzione ai dati][Introduzione ai dati]
    Ulteriori informazioni sull'archiviazione e sulle query dei dati mediante Servizi mobili.

-   [Introduzione all'autenticazione][Introduzione all'autenticazione]
    Informazioni sull'autenticazione degli utenti dell'app con l'account Windows.

-   [Riferimento per gli script del server di Servizi mobili][Riferimento per gli script del server di Servizi mobili]
    Ulteriori informazioni sulla registrazione e l'utilizzo di script del server.

-   [Riferimento per i concetti e le procedure di Servizi mobili con .NET][Riferimento per i concetti e le procedure di Servizi mobili con .NET]
    Ulteriori informazioni su come utilizzare Servizi mobili con .NET.



  [Windows Store C#]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-get-started-push "Windows Store C#"
  [Windows Store JavaScript]: /it-it/documentation/articles/mobile-services-windows-store-javascript-get-started-push "Windows Store JavaScript"
  [Windows Phone]: /it-it/documentation/articles/mobile-services-windows-phone-get-started-push "Windows Phone"
  [iOS]: /it-it/documentation/articles/mobile-services-ios-get-started-push "iOS"
  [Android]: /it-it/documentation/articles/mobile-services-android-get-started-push "Android"
  [Appcelerator]: /it-it/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push "Appcelerator"
  [Back-end .NET]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/ ".NET backend"
  [Back-end JavaScript]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-get-started-push/ "JavaScript backend"
  [Introduzione alle notifiche push]: /it-it/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/
  [Registrazione dell'app per le notifiche push e configurazione di Servizi mobili]: #register
  [Aggiornamento del codice delle notifiche push generato]: #update-scripts
  [Inserimento di dati per la ricezione di notifiche]: #test
  [Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started/
  [Introduzione ai dati]: /it-it/develop/mobile/tutorials/get-started-with-data-dotnet/
  [mobile-services-create-new-push-vs2013]: ../includes/mobile-services-create-new-push-vs2013.md
  [Utilizzo degli script del server in Servizi mobili]: http://go.microsoft.com/fwlink/p/?LinkID=287178
  [mobile-services-create-new-push-vs2013-2]: ../includes/mobile-services-create-new-push-vs2013-2.md
  [0]: ./media/mobile-services-windows-store-dotnet-get-started-push/mobile-quickstart-push1.png
  [1]: ./media/mobile-services-windows-store-dotnet-get-started-push/mobile-quickstart-push2.png
  [Introduzione ad Hub di notifica]: /it-it/manage/services/notification-hubs/getting-started-windows-dotnet/
  [Invio di notifiche ai sottoscrittori]: /it-it/manage/services/notification-hubs/breaking-news-dotnet/
  [Invio di notifiche agli utenti]: /it-it/manage/services/notification-hubs/notify-users/
  [Invio di notifiche su piattaforme diverse agli utenti]: /it-it/manage/services/notification-hubs/notify-users-xplat-mobile-services/
  [Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-dotnet
  [Riferimento per gli script del server di Servizi mobili]: http://go.microsoft.com/fwlink/?LinkId=262293
  [Riferimento per i concetti e le procedure di Servizi mobili con .NET]: /it-it/develop/mobile/how-to-guides/work-with-net-client-library/
