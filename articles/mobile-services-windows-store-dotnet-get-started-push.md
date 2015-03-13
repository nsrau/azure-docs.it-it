<properties 
	pageTitle="Introduzione alle notifiche push (Windows Store) | Mobile Dev Center" 
	description="Informazioni su come usare Servizi mobili di Azure per inviare notifiche push all'app per Windows Store (push legacy)." 
	services="mobile-services, notification-hubs" 
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
	ms.date="09/11/2014" 
	ms.author="glenga"/>

# Aggiungere notifiche push all'app di Servizi mobili (push legacy)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/it-it/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Windows Store C#" class="current">Windows Store C#</a>
    <a href="/it-it/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Windows Store JavaScript">Windows Store JavaScript</a>
    <a href="/it-it/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a>
    <a href="/it-it/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a>
    <a href="/it-it/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
<!--    <a href="/it-it/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a>
    <a href="/it-it/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a> -->
	<a href="/it-it/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/" title=".NET backend">Back-end .NET</a> | <a href="/it-it/documentation/articles/mobile-services-windows-store-dotnet-get-started-push/"  title="JavaScript backend" class="current">Back-end JavaScript</a></div>	

Questo argomento descrive come usare Servizi mobili di Azure in Visual Studio 2013 per inviare notifiche push all'app di Windows Store. In questa esercitazione si userà Servizi notifica Push Windows per aggiungere notifiche push al progetto di guida introduttiva, direttamente da Visual Studio. Al termine dell'esercitazione, il servizio mobile invierà una notifica push ogni volta che viene inserito un record.

>[AZURE.NOTE]L'argomento supporta <em>servizi mobili</em> esistenti <em>che non sono stati ancora aggiornati</em> per l'uso dell'integrazione di Hub di notifica. Quando si crea un <em>nuovo</em> servizio mobile, questa funzionalità integrata è abilitata automaticamente. Per i nuovi servizi mobili, vedere [Introduzione alle notifiche push](/it-it/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/).
>
>Servizi mobili è integrato con Hub di notifica di Azure per supportare altre funzionalità di notifica push, ad esempio modelli, più piattaforme e maggiore scalabilità. <em>È consigliabile aggiornare i servizi mobili esistenti per l'uso di Hub di notifica, se possibile</em>. Dopo aver eseguito l'aggiornamento, vedere questa versione di [Introduzione alle notifiche push](/it-it/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/).

In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

1. [Registrare l'app per le notifiche push e configurare Servizi mobili]
2. [Aggiornare il codice delle notifiche push generato]
3. [Inserire dati per la ricezione di notifiche]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare l'esercitazione, è necessario completare [Introduzione a Servizi mobili] o [Introduzione ai dati] per collegare il progetto al servizio mobile. Se non è stato collegato alcun servizio mobile, la procedura guidata Aggiungi notifica push crea automaticamente la connessione. 

<h2><a name="register"></a>Aggiungere e configurare le notifiche push nell'app</h2>

[AZURE.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

<ol start="6">
<li><p>Espandere <strong>services</strong>, <strong>mobile services</strong>, il nome del proprio servizio, aprire il file di codice generato e quindi controllare il metodo <strong>UploadChannel</strong> che ottiene l'ID dell'installazione e il canale del dispositivo e inserisce questi dati nella nuova tabella channels.</p> 

<p>Anche la procedura guidata aggiunge una chiamata a questo metodo al gestore dell'evento <strong>OnLaunched</strong> nel file di codice App.xaml.cs. In questo modo verrà effettuato un tentativo di registrazione del dispositivo a ogni avvio dell'app.</p></li> 
<li><p>In Esplora server espandere <strong>Azure</strong>, <strong>Servizi mobili</strong>, il nome del servizio e <strong>channels</strong>, quindi aprire il file insert.js.</p> 

<p>Questo file, archiviato nel servizio mobile, contiene codice JavaScript che viene eseguito quando un client invia una richiesta per la registrazione di un dispositivo inserendo dati nella tabella channels.</p> 

> [AZURE.NOTE] La versione iniziale di questo file contiene codice che verifica la presenza di una registrazione esistente per il dispositivo. Contiene inoltre codice che invia una notifica push quando viene aggiunta una nuova registrazione alla tabella channels. Il codice che invia una notifica push può essere incluso in qualsiasi file di script registrato. Il percorso di questo script dipende da come viene attivata la notifica. Gli script possono essere registrati con un'operazione insert, update, delete o read su una tabella, come processo pianificato o come API personalizzata. Per altre informazioni, vedere [Uso degli script del server in Servizi mobili](http://go.microsoft.com/fwlink/p/?LinkID=287178).

</li> 
<li><p>Premere F5 per eseguire l'app e verificare che venga immediatamente ricevuta una notifica dal servizio mobile.</p>
<p>Questa notifica è stata generata inserendo una riga nella nuova tabella channels, corrispondente alla registrazione del dispositivo.</p>
</li>
</ol>
Il codice generato semplifica la dimostrazione dell'invio di una notifica durante l'esecuzione dell'app, ma in genere non si tratta di uno scenario significativo. Si rimuoverà quindi il codice delle notifiche dalla tabella channels e lo si sostituirà, con qualche modifica, nella tabella TodoItem. 

<h2><a name="update-scripts"></a>Aggiornare il codice delle notifiche push generato</h2>

[AZURE.INCLUDE [mobile-services-create-new-push-vs2013-2](../includes/mobile-services-create-new-push-vs2013-2.md)]

<h2><a name="test"></a>Testare le notifiche push nell'app</h2>

1. In Visual Studio premere F5 per eseguire l'app.

2. Nell'app digitare un testo in **Insert a TodoItem**, quindi fare clic su **Save**.

   	![][13]

   	Si noti che al termine dell'inserimento l'app riceve una notifica push da WNS.

   	![][14]

## <a name="next-steps"> </a>Passaggi successivi

In questa esercitazione vengono illustrate le nozioni di base per consentire a un'app di Windows Store di usare dati in Servizi mobili. In seguito, è consigliabile eseguire una delle esercitazioni seguenti, basate sull'app GetStartedWithData creata in questa esercitazione:

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

* [Riferimento per i concetti e le procedure di .NET per Servizi mobili]
  <br/>Altre informazioni su come usare Servizi mobili con .NET.

<!-- Anchors. -->
[Registrare l'app per le notifiche push e configurare Servizi mobili]: #register
[Aggiornare il codice delle notifiche push generato]: #update-scripts
[Inserire dati per la ricezione di notifiche]: #test
[Passaggi successivi]:#next-steps

<!-- Images. -->











[13]: ./media/mobile-services-windows-store-dotnet-get-started-push/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-dotnet-get-started-push/mobile-quickstart-push2.png




<!-- URLs. -->
[Pagina Invia un'app]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Applicazioni personali]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK per Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started/
[Introduzione ai dati]: /it-it/develop/mobile/tutorials/get-started-with-data-dotnet/
[Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-dotnet
[Introduzione alle notifiche push]: /it-it/develop/mobile/tutorials/get-started-with-push-dotnet
[Inviare notifiche push agli utenti di app]: /it-it/develop/mobile/tutorials/push-notifications-to-users-dotnet
[Autorizzare gli utenti con gli script]: /it-it/develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript e HTML]: /it-it/develop/mobile/tutorials/get-started-with-push-js

[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Oggetto wns]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Riferimento per i concetti e le procedure di .NET per Servizi mobili]: /it-it/develop/mobile/how-to-guides/work-with-net-client-library/
[Usare script per la convalida e la modifica di dati]: /it-it/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Usare il paging per ridefinire le query]: /it-it/develop/mobile/tutorials/add-paging-to-data-dotnet
[Introduzione ad Hub di notifica]: /it-it/manage/services/notification-hubs/getting-started-windows-dotnet/
[Informazioni su Hub di notifica]: /it-it/develop/net/how-to-guides/service-bus-notification-hubs/
[Inviare notifiche ai sottoscrittori]: /it-it/manage/services/notification-hubs/breaking-news-dotnet/
[Inviare notifiche agli utenti]: /it-it/manage/services/notification-hubs/notify-users/
[Inviare notifiche agli utenti tra piattaforme diverse]: /it-it/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Informazioni di riferimento sugli script del server di Servizi mobili]: http://go.microsoft.com/fwlink/?LinkId=262293


<!--HONumber=42-->
