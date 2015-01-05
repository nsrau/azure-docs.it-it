<properties urlDisplayName="Get Started with Push (JS)" pageTitle="Introduzione alle notifiche push (push legacy) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Windows Store JavaScript app (legacy push)." metaCanonical="http://www.windowsazure.com/it-it/develop/mobile/tutorials/get-started-with-push-dotnet/" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="11/22/2014" ms.author="glenga" />

# Aggiungere notifiche push all'app di Servizi mobili (push legacy)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/it-it/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Windows Store C#">Windows Store C#</a>
    <a href="/it-it/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Windows Store JavaScript" class="current">Windows Store JavaScript</a>
    <a href="/it-it/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a>
    <a href="/it-it/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a>
    <a href="/it-it/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
<!--    <a href="/it-it/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a>
    <a href="/it-it/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a> -->
	<a href="/it-it/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/" title=".NET backend">Back-end .NET</a> |  <a href="/it-it/documentation/articles/mobile-services-windows-store-javascript-get-started-push/"  title="JavaScript backend" class="current">Back-end JavaScript</a></div>		

Questo argomento descrive come usare Servizi mobili di Azure in Visual Studio 2013 per inviare notifiche push all'app di Windows Store. In questa esercitazione si utilizzerà Servizi notifica Push Windows per aggiungere notifiche push al progetto di guida introduttiva, direttamente da Visual Studio. Al termine dell'esercitazione, il servizio mobile invierà una notifica push ogni volta che viene inserito un record.

>[WACOM.NOTE]In questo argomento sono supportati servizi mobili <em>esistenti</em> che <em>non sono stati ancora aggiornati</em> per l'uso dell'integrazione di Hub di notifica. Quando si crea un <em>nuovo</em> servizio mobile, questa funzionalità integrata viene abilitata automaticamente. Per i nuovi servizi mobili, vedere [Introduzione alle notifiche push].(/it-it/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push/).
>
>Servizi mobili è integrato con Hub di notifica di Azure per supportare altre funzionalità di notifica push, ad esempio modelli, più piattaforme e maggiore scalabilità. <em>È consigliabile aggiornare i servizi mobili esistenti per l'uso di Hub di notifica, se possibile</em>. Dopo aver eseguito l'aggiornamento, vedere questa versione di [Introduzione alle notifiche push](/it-it/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push/).

In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

1. [Registrare l'app per le notifiche push e configurare Servizi mobili]
2. [Aggiornare il codice delle notifiche push generato]
3. [Inserire dati per la ricezione di notifiche]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare l'esercitazione, è necessario completare [Introduzione a Servizi mobili] o [Introduzione ai dati] per collegare il progetto al servizio mobile. Se non è stato collegato alcun servizio mobile, la procedura guidata Aggiungi notifica push crea automaticamente la connessione. 

<h2><a name="register"></a>Aggiungere e configurare le notifiche push nell'app</h2>

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

<ol start="6">
<li><p>Aprire il file di codice push.register.js generato e quindi controllare il codice che ottiene l'ID dell'installazione e il canale del dispositivo e inserisce questi dati nella nuova tabella <strong>channels</strong>.</p> 

	<p>Questa tabella è stata creata nel servizio mobile tramite la procedura guidata Aggiungi notifica push. In questo modo è possibile garantire che la registrazione del dispositivo venga tentata ogni volta che l'app viene attivata.</p></li>
<li><p>In Esplora server espandere <strong>Azure</strong>, <strong>Servizi mobili</strong>, il nome del servizio e <strong>channels</strong>, quindi aprire il file insert.js.</p> 

<p>Questo file, archiviato nel servizio mobile, contiene codice JavaScript che viene eseguito quando un client invia una richiesta per la registrazione di un dispositivo inserendo dati nella tabella channels.</p> 

<div class="dev-callout"><b>Nota</b>
	<p>La versione iniziale di questo file contiene codice che verifica la presenza di una registrazione esistente per il dispositivo. Contiene inoltre codice che invia una notifica push quando viene aggiunta una nuova registrazione alla tabella channels. Il codice che invia una notifica push può essere incluso in qualsiasi file di script registrato. Il percorso di questo script dipende da come viene attivata la notifica. Gli script possono essere registrati con un'operazione insert, update, delete o read su una tabella, come processo pianificato o come API personalizzata. Per altre informazioni, vedere <a href="http://go.microsoft.com/fwlink/p/?LinkID=287178">Uso degli script del server in Servizi mobili</a>.</p>
</div>
</li> 
<li><p>Premere F5 per eseguire l'app e verificare che venga immediatamente ricevuta una notifica dal servizio mobile.</p>
<p>Questa notifica è stata generata inserendo una riga nella nuova tabella channels, corrispondente alla registrazione del dispositivo.</p>
</li>
</ol>

Il codice generato semplifica la dimostrazione dell'invio di una notifica durante l'esecuzione dell'app, ma in genere non si tratta di uno scenario significativo. Si rimuoverà quindi il codice delle notifiche dalla tabella channels e lo si sostituirà, con qualche modifica, nella tabella TodoItem. 

<h2><a name="update-scripts"></a>Aggiornamento del codice delle notifiche push generato</h2>

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013-2](../includes/mobile-services-create-new-push-vs2013-2.md)]

<h2><a name="test"></a>Testare le notifiche push nell'app</h2>

1. In Visual Studio premere F5 per eseguire l'app.

2. Nell'app digitare un testo in **Insert a TodoItem**, quindi fare clic su **Save**.

   	![][13]

   	Si noti che al termine dell'inserimento l'app riceve una notifica push da WNS.

   	![][14]

## <a name="next-steps"> </a>Passaggi successivi

In questa esercitazione viene illustrata la funzionalità di notifica push di base fornita da Servizi mobili. Se per l'app sono richieste funzionalità più avanzate, ad esempio l'invio di notifiche tra piattaforme diverse, il routing basato sulla sottoscrizione o la gestione di volumi molto elevati, provare a usare Hub di notifica di Azure con il servizio mobile. Per altre informazioni, vedere uno degli argomenti seguenti relativi a Hub di notifica:

+ [Introduzione ad Hub di notifica]
  <br/>Informazioni su come sfruttare Hub di notifica nell'app di Windows Store.

+ [Inviare notifiche ai sottoscrittori]
	<br/>Informazioni su come gli utenti possono registrarsi e ricevere notifiche push per le categorie cui sono interessati.

+ [Inviare notifiche agli utenti]
	<br/>Informazioni su come inviare notifiche push da un servizio mobile a utenti specifici su qualsiasi dispositivo.

+ [Inviare notifiche agli utenti tra piattaforme diverse]
	<br/>Informazioni su come usare i modelli per inviare notifiche push da un servizio mobile senza che sia necessario creare payload specifici della piattaforma nel back-end.

Per altre informazioni, vedere anche i seguenti argomenti su Servizi mobili:

* [Introduzione ai dati]
  <br/>Altre informazioni sull'archiviazione e l'esecuzione di query sui dati tramite Servizi mobili.

* [Introduzione all'autenticazione]
  <br/>Informazioni sull'autenticazione degli utenti dell'app con un account Windows.

* [Informazioni di riferimento sugli script del server di Servizi mobili]
  <br/>Altre informazioni sulla registrazione e l'uso di script del server.

* [Riferimento per i concetti e le procedure di Servizi mobili con HTML/JavaScript]
  <br/>Altre informazioni su come usare Servizi mobili con HTML e JavaScript.  

<!-- Anchors. -->
[Registrare l'app per le notifiche push e configurare Servizi mobili]: #register
[Aggiornare il codice delle notifiche push generato]: #update-scripts
[Inserire dati per la ricezione di notifiche]: #test
[Passaggi successivi]:#next-steps

<!-- Images. -->







[13]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push2.png




<!-- URLs. -->
[Pagina Invia un'app]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Applicazioni personali]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK per Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started/
[Introduzione ai dati]: /it-it/develop/mobile/tutorials/get-started-with-data-js/
[Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-js
[Introduzione alle notifiche push]: /it-it/develop/mobile/tutorials/get-started-with-push-js
[Inviare notifiche push agli utenti di app]: /it-it/develop/mobile/tutorials/push-notifications-to-users-js
[Autorizzare gli utenti con gli script]: /it-it/develop/mobile/tutorials/authorize-users-in-scripts-js
[JavaScript e HTML]: /it-it/develop/mobile/tutorials/get-started-with-push-js

[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Riferimento per i concetti e le procedure di Servizi mobili con HTML/JavaScript]: /it-it/develop/mobile/how-to-guides/work-with-html-js-client/
[Informazioni di riferimento sugli script del server di Servizi mobili]: http://go.microsoft.com/fwlink/?LinkId=262293
[Introduzione ad Hub di notifica]: /it-it/manage/services/notification-hubs/getting-started-windows-dotnet/
[Informazioni su Hub di notifica]: /it-it/develop/net/how-to-guides/service-bus-notification-hubs/
[Inviare notifiche ai sottoscrittori]: /it-it/manage/services/notification-hubs/breaking-news-dotnet/
[Inviare notifiche agli utenti]: /it-it/manage/services/notification-hubs/notify-users/
[Inviare notifiche agli utenti tra piattaforme diverse]: /it-it/manage/services/notification-hubs/notify-users-xplat-mobile-services/

<!--HONumber=35.1-->
