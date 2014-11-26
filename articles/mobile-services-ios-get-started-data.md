<properties linkid="develop-mobile-tutorials-get-started-with-data-ios" urlDisplayName="Get Started with Data" pageTitle="Get started with data (iOS) | Mobile Dev Center" metaKeywords="Azure iOS data, Azure mobile services data, " description="Learn how to get started using Mobile Services to leverage data in your iOS app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="09/19/2014" ms.author="glenga" />

# Introduzione ai dati in Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

In questo argomento viene illustrato come utilizzare Servizi mobili di Azure per sfruttare i dati in un'app per iOS. In questa esercitazione si scaricherà un'app che archivia dati in memoria, si creerà un nuovo servizio mobile e lo si integrerà con l'app e quindi si effettuerà l'accesso al portale di gestione di Azure per visualizzare le modifiche apportate ai dati durante l'esecuzione dell'app.

<div class="dev-callout"><b>Nota</b>
<p>In questa esercitazione viene descritto come &egrave; possibile utilizzare Servizi mobili di Azure per archiviare e recuperare i dati da un'app per iOS e vengono pertanto riproposte molte delle procedure gi&agrave; completate nella guida introduttiva a Servizi mobili. Se si tratta della prima esperienza con Servizi mobili, &egrave; consigliabile iniziare dall'esercitazione <a href="/it-it/develop/mobile/tutorials/get-started-ios">Introduzione a Servizi mobili</a>.</p>
</div>

In questa esercitazione vengono descritte le operazioni di base seguenti:

1.  [Download del progetto dell'app per iOS][Download del progetto dell'app per iOS]
2.  [Creazione del servizio mobile][Creazione del servizio mobile]
3.  [Aggiunta di una tabella dati per l'archiviazione][Aggiunta di una tabella dati per l'archiviazione]
4.  [Aggiornamento dell'app per l'utilizzo di Servizi mobili][Aggiornamento dell'app per l'utilizzo di Servizi mobili]
5.  [Test dell'app in Servizi mobili][Test dell'app in Servizi mobili]

Per completare questa esercitazione, è necessario disporre di:

-   [Mobile Services SDK per iOS][Mobile Services SDK per iOS], di [XCode 4.5][XCode 4.5] e di iOS 5.0 o versioni successive.
-   Un account Microsoft Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][versione di valutazione gratuita di Azure].
    </p>
    </div>

## <a name="download-app"></a>Scaricare il progetto GetStartedWithData

Questa esercitazione è basata sull'[app GetStartedWithData][app GetStartedWithData] che è un'app per iOS. L'interfaccia utente di questa app è identica a quella dell'app generata dalla guida introduttiva di Servizi mobili per iOS, ma in questo caso gli elementi aggiunti vengono archiviati nella memoria locale.

1.  Scaricare l'[app di esempio][app GetStartedWithData] GetStartedWithData.

2.  In Xcode aprire il progetto scaricato ed esaminare il file TodoService.m.

    Si noti che sono presenti otto commenti **// TODO** in cui vengono specificate le procedure da eseguire per il funzionamento dell'app con il servizio mobile.

3.  Premere il pulsante **Run** (o il tasto Comando+R) per ricompilare il progetto e avviare l'app.

4.  Nell'app digitare un testo nella casella di testo e quindi fare clic sul pulsante **+**.

    ![][0]

    Si noti che il testo salvato è visualizzato nell'elenco riportato di seguito.

## <a name="create-service"></a>Creazione di un nuovo servizio mobile nel portale di gestione

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

## <a name="add-table"></a>Aggiunta di una nuova tabella al servizio mobile

[WACOM.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

## <a name="update-app"></a>Aggiornamento dell'app per l'uso del servizio mobile per l'accesso ai dati

[WACOM.INCLUDE [mobile-services-ios-enable-mobile-service-access](../includes/mobile-services-ios-enable-mobile-service-access.md)]

## <a name="test-app"></a>Test dell'app sul nuovo servizio mobile

1.  In Xcode selezionare un emulatore in cui effettuare la distribuzione (iPhone o iPad), premere il pulsante **Run** (o il tasto Comando+R) per ricompilare il progetto e avviare l'app.

    Viene eseguito il client Servizi mobili di Azure, compilato con l'SDK per iOS, e viene eseguita una query sugli elementi dal servizio mobile.

2.  Come in precedenza, digitare un testo nella casella di testo e quindi fare clic sul pulsante **+**.

    Un nuovo elemento verrà inviato come inserimento al servizio mobile.

3.  Nel [portale di gestione][portale di gestione] fare clic su **Mobile Services** e quindi sul servizio mobile.

4.  Fare clic sulla scheda **Data** e quindi su **Browse**.

    ![][1]

    Si noti che la tabella **TodoItem** ora contiene dati, con valori ID generati da Servizi mobili, e che alla tabella sono state aggiunte automaticamente colonne per garantire la corrispondenza con la classe TodoItem nell'app.

L'esercitazione **Introduzione ai dati** per iOS è terminata.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le nozioni di base per consentire a un'app per iOS di utilizzare dati in Servizi mobili.

In seguito, è consigliabile eseguire una delle esercitazioni seguenti, basate sull'app GetStartedWithData creata in questa esercitazione:

-   [Utilizzo di script per la convalida e la modifica di dati][Utilizzo di script per la convalida e la modifica di dati]
    Ulteriori informazioni sull'utilizzo di script del server in Servizi mobili per convalidare e modificare i dati inviati dall'app.

-   [Utilizzo del paging per ridefinire le query][Utilizzo del paging per ridefinire le query]
    Ulteriori informazioni su come utilizzare il paging nelle query per controllare la quantità di dati gestiti in un'unica richiesta.

Una volta completata la serie relativa ai dati, provare a eseguire le esercitazioni per iOS seguenti:

-   [Introduzione all'autenticazione][Introduzione all'autenticazione]
    Informazioni sull'autenticazione degli utenti dell'app.

-   [Introduzione alle notifiche push][Introduzione alle notifiche push]
    Informazioni sull'invio di una notifica push di base all'app con Servizi mobili.



  [Download del progetto dell'app per iOS]: #download-app
  [Creazione del servizio mobile]: #create-service
  [Aggiunta di una tabella dati per l'archiviazione]: #add-table
  [Aggiornamento dell'app per l'utilizzo di Servizi mobili]: #update-app
  [Test dell'app in Servizi mobili]: #test-app
  [Mobile Services SDK per iOS]: https://go.microsoft.com/fwLink/p/?LinkID=266533
  [XCode 4.5]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [versione di valutazione gratuita di Azure]: http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fit-it%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-ios%2F
  [app GetStartedWithData]: http://go.microsoft.com/fwlink/p/?LinkId=268622
  [0]: ./media/mobile-services-ios-get-started-data/mobile-quickstart-startup-ios.png
  [portale di gestione]: https://manage.windowsazure.com/
  [1]: ./media/mobile-services-ios-get-started-data/mobile-todoitem-data-browse.png
  [Utilizzo di script per la convalida e la modifica di dati]: /it-it/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
  [Utilizzo del paging per ridefinire le query]: /it-it/develop/mobile/tutorials/add-paging-to-data-ios
  [Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-ios
  [Introduzione alle notifiche push]: /it-it/develop/mobile/tutorials/get-started-with-push-ios
