<properties urlDisplayName="Get Started with Data" pageTitle="Introduzione ai dati (iOS) | Mobile Developer Center" metaKeywords="Azure iOS data, Azure mobile services data, " description="Informazioni su come iniziare a usare Servizi mobili per sfruttare i dati nell'app per iOS." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="krisragh" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# Aggiungere Servizi mobili a un'app esistente

[WACOM.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

Questo argomento illustra come usare Servizi mobili di Azure per sfruttare i dati in un'app per iOS. In questa esercitazione si scaricherà un'app che archivia dati in memoria, si creerà un nuovo servizio mobile e lo si integrerà con l'app. Si effettuerà quindi l'accesso al portale di gestione di Azure per visualizzare le modifiche apportate ai dati durante l'esecuzione dell'app.

<div class="dev-callout"><b>Nota</b>
<p>In questa esercitazione viene descritto come è possibile usare Servizi mobili di Azure per archiviare e recuperare i dati da un'app per iOS e vengono pertanto riproposte molte delle procedure già completate nella guida introduttiva a Servizi mobili. Se si tratta della prima esperienza con Servizi mobili, è consigliabile iniziare dall'esercitazione <a href="/it-it/develop/mobile/tutorials/get-started-ios">Introduzione a Servizi mobili</a>.</p>
</div>

In questa esercitazione vengono descritte le operazioni di base seguenti:

1. [Download del progetto dell'app per iOS]
2. [Creare il servizio mobile]
3. [Aggiungere una tabella dati per l'archiviazione]
4. [Aggiornare l'app per l'uso di Servizi mobili]
5. [Testare l'app in Servizi mobili]

Per completare questa esercitazione, è necessario disporre di:

+ [Mobile Services SDK per iOS] e [XCode 4.5][Install Xcode] e iOS 5.0 o versioni successive.
+ Account Microsoft Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fit-it%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-ios%2F" target="_blank">versione di valutazione gratuita di Azure</a>.</p></div>

##<a name="download-app"></a>Scaricare il progetto GetStartedWithData

Questa esercitazione è basata sull'[app GetStartedWithData][GitHub], che è un'app per iOS. L'interfaccia utente di questa app è identica a quella dell'app generata dalla guida introduttiva di Servizi mobili per iOS, ma in questo caso gli elementi aggiunti vengono archiviati nella memoria locale.

1. Scaricare l' [app di esempio][GitHub] GetStartedWithData.

2. In Xcode aprire il progetto scaricato ed esaminare il file TodoService.m.

   	Si noti che sono presenti otto commenti **// TODO** in cui vengono specificate le procedure da eseguire per il funzionamento dell'app con il servizio mobile.

3. Premere il pulsante **Run** (o il tasto Comando+R) per ricompilare il progetto e avviare l'app.

4. Nell'app digitare un testo nella casella di testo e quindi fare clic sul pulsante **+**.

   	![][0]  

   	Si noti che il testo salvato è visualizzato nell'elenco riportato di seguito.

##<a name="create-service"></a>Creare un nuovo servizio mobile nel portale di gestione

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

##<a name="add-table"></a>Aggiungere una nuova tabella al servizio mobile

[WACOM.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

##<a name="update-app"></a>Aggiornare l'app per l'uso del servizio mobile per l'accesso ai dati

[WACOM.INCLUDE [mobile-services-ios-enable-mobile-service-access](../includes/mobile-services-ios-enable-mobile-service-access.md)]

##<a name="test-app"></a>Testare l'app nel nuovo servizio mobile

1. In Xcode selezionare un emulatore in cui effettuare la distribuzione (iPhone o iPad), premere il pulsante **Run** (o il tasto Comando+R) per ricompilare il progetto e avviare l'app.

   	Viene eseguito il client di Servizi mobili di Azure, compilato con l'SDK per iOS, e viene eseguita una query sugli elementi dal servizio mobile.

2. Come in precedenza, digitare un testo nella casella di testo e quindi fare clic sul pulsante **+**.

   	Un nuovo elemento verrà inviato come inserimento al servizio mobile.

3. Nel [portale di gestione] fare clic su **Servizi mobili** e quindi sul servizio mobile.

4. Fare clic sulla scheda **Dati** e quindi su **Sfoglia**.

   	![][9]

   	Si noti che la tabella **TodoItem** ora contiene dati, con valori ID generati da Servizi mobili e che alla tabella sono state aggiunte automaticamente colonne per garantire la corrispondenza con la classe TodoItem nell'app.

L'esercitazione **Introduzione ai dati** per iOS è terminata.

##<a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le nozioni di base per consentire a un'app per iOS di usare dati in Servizi mobili.

In seguito, è consigliabile eseguire una delle esercitazioni seguenti, basate sull'app GetStartedWithData creata in questa esercitazione:

* [Convalidare e modificare dati mediante script]
  <br/>Altre informazioni sull'uso di script del server in Servizi mobili per convalidare e modificare i dati inviati dall'app.

* [Usare il paging per ridefinire le query]
  <br/>Altre informazioni su come usare il paging nelle query per controllare la quantità di dati gestiti in un'unica richiesta.

Una volta completata la serie relativa ai dati, provare a eseguire le esercitazioni per iOS seguenti:

* [Introduzione all'autenticazione]
	<br/>Informazioni sull'autenticazione degli utenti dell'app.

* [Introduzione alle notifiche push]
  <br/>Informazioni sull'invio di una notifica push di base all'app con Servizi mobili.

<!-- Anchors. -->
[Download del progetto dell'app per iOS]: #download-app
[Creare il servizio mobile]: #create-service
[Aggiungere una tabella dati per l'archiviazione]: #add-table
[Aggiornare l'app per l'uso di Servizi mobili]: #update-app
[Testare l'app in Servizi mobili]: #test-app
[Passaggi successivi]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-ios-get-started-data/mobile-quickstart-startup-ios.png







[8]: ./media/mobile-services-ios-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-ios-get-started-data/mobile-todoitem-data-browse.png



<!-- URLs. -->
[Convalidare e modificare dati mediante script]: /it-it/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Usare il paging per ridefinire le query]: /it-it/develop/mobile/tutorials/add-paging-to-data-ios
[Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started-ios
[Introduzione ai dati]: /it-it/develop/mobile/tutorials/get-started-with-data-ios
[Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-ios
[Introduzione alle notifiche push]: /it-it/develop/mobile/tutorials/get-started-with-push-ios

[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Portale di gestione]: https://manage.windowsazure.com/
[Installare Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Mobile Services SDK per iOS]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[GitHub]:  http://go.microsoft.com/fwlink/p/?LinkId=268622
[Archivio GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=268784
