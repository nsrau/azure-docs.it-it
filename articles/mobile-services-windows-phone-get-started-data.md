<properties linkid="develop-mobile-tutorials-get-started-with-data-wp8" urlDisplayName="Get Started with Data" pageTitle="Get started with data (WP8) - Azure Mobile Services" metaKeywords="" description="Learn how to get started using data from your Azure Mobile Services Windows Phone 8 app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/19/2014" ms.author="glenga" />

# Introduzione ai dati in Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-get-started-data-legacy](../includes/mobile-services-selector-get-started-data-legacy.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>In questo argomento viene illustrato come utilizzare Servizi mobili di Azure per sfruttare i dati in un'app per Windows Phone 8. In questa esercitazione si scaricher&agrave; un'app che archivia dati in memoria, si creer&agrave; un nuovo servizio mobile e lo si integrer&agrave; con l'app e quindi si effettuer&agrave; l'accesso al portale di gestione di Azure per visualizzare le modifiche apportate ai dati durante l'esecuzione dell'app.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkID=298628" target="_blank" class="label">video di esercitazione</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-get-started-data-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkID=298628" target="_blank" class="dev-onpage-video"><span class="icon">Riproduci video</span></a> <span class="time">12:54:00</span></div>

</div>

<div class="dev-callout"><b>Nota</b>
<p>In questa esercitazione viene descritto come &egrave; possibile utilizzare Servizi mobili di Azure per archiviare e recuperare i dati da un'app per Windows Phone 8 e vengono pertanto riproposte molte delle procedure gi&agrave; completate nella guida introduttiva a Servizi mobili. Se si tratta della prima esperienza con Servizi mobili, &egrave; consigliabile iniziare dall'esercitazione <a href="/it-it/develop/mobile/tutorials/get-started-wp8">Introduzione a Servizi mobili</a>.</p>
</div>

In questa esercitazione vengono descritte le operazioni di base seguenti:

1.  [Download del progetto dell'app per Windows Phone 8][Download del progetto dell'app per Windows Phone 8]
2.  [Creazione del servizio mobile][Creazione del servizio mobile]
3.  [Aggiunta di una tabella dati per l'archiviazione][Aggiunta di una tabella dati per l'archiviazione]
4.  [Aggiornamento dell'app per l'utilizzo di Servizi mobili][Aggiornamento dell'app per l'utilizzo di Servizi mobili]
5.  [Test dell'app in Servizi mobili][Test dell'app in Servizi mobili]

Questa esercitazione richiede Visual Studio 2012 Express per Windows Phone 8 e [Windows Phone 8 SDK][Windows Phone 8 SDK] in esecuzione su Windows 8. Per completare questa esercitazione per la creazione di un'app per Windows Phone 8.1, è necessario usare Visual Studio 2013 Update 2 o una versione successiva.

> [WACOM.NOTE] Per completare l'esercitazione, è necessario un account Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][versione di valutazione gratuita di Azure].

## <a name="download-app"></a>Scaricare il progetto GetStartedWithData

Questa esercitazione è basata sull'[app GetStartedWithData][app GetStartedWithData] che è un progetto di app per Windows Phone Silverlight 8. L'interfaccia utente di questa app è identica a quella dell'app generata dalla guida introduttiva di Servizi mobili, ma in questo caso gli elementi aggiunti vengono archiviati nella memoria locale.

1.  Scaricare il progetto di app di esempio GetStartedWithData dal [sito degli esempi di codice di Developer Network][app GetStartedWithData].

    > [WACOM.NOTE]Per creare un'app per Windows Phone Silverlight 8.1, nel progetto di app per Windows Phone Silverlight 8 scaricato è sufficiente impostare il sistema operativo di destinazione su Windows Phone 8.1. Per creare un'app di Windows Phone Store, scaricare la [versione dell'app di Windows Phone Store][versione dell'app di Windows Phone Store] del progetto di app di esempio GetStartedWithData.

2.  In Visual Studio aprire il progetto scaricato ed esaminare il file MainPage.xaml.cs.

    Si noti che gli oggetti **TodoItem** aggiunti sono archiviati in un oggetto **ObservableCollection\<TodoItem\>** in memoria.

3.  Premere **F5** per ricompilare il progetto e avviare l'app.

4.  Nell'app digitare un testo nella casella di testo e quindi fare clic sul pulsante **Save**.

    ![][0]

    Si noti che il testo salvato è visualizzato nell'elenco riportato di seguito.

## <a name="create-service"></a><span class="short-header">Creazione del servizio mobile</span>Creazione di un nuovo servizio mobile nel portale di gestione

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

## <a name="add-table"></a><span class="short-header">Aggiunta di una nuova tabella</span>Aggiunta di una nuova tabella al servizio mobile

[WACOM.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

## <a name="update-app"></a><span class="short-header">Aggiornamento dell'app</span>Aggiornamento dell'app per l'utilizzo del servizio mobile per l'accesso ai dati

Ora che il servizio mobile è pronto, è possibile aggiornare l'app in modo che gli elementi vengano archiviati in Servizi mobili anziché nella raccolta locale.

1.  In **Esplora soluzioni** in Visual Studio fare clic con il pulsante destro del mouse sul nome del progetto e quindi scegliere **Manage NuGet Packages**.

2.  Nel riquadro sinistro selezionare la categoria **Online**, cercare `WindowsAzure.MobileServices`, fare clic su **Installa** nel pacchetto di **Servizi mobili di Azure**, quindi accettare il contratto di licenza.

    ![][1]

    La libreria client per i servizi di Servizi mobili verrà aggiunta al progetto.

3.  Nel portale di gestione fare clic su **Mobile Services** e quindi sul servizio mobile appena creato.

4.  Fare clic sulla scheda **Dashboard** e prendere nota del valore di **Site URL**, quindi fare clic su **Manage keys** e prendere nota del valore di **Application key**.

    ![][2]

    Questi valori sono necessari per accedere al servizio mobile dal codice dell'app.

5.  In Visual Studio aprire il file App.xaml.cs e aggiungere l'istruzione `using` seguente o rimuovere i relativi simboli di commento:

        using Microsoft.WindowsAzure.MobileServices;

6.  Nello stesso file, rimuovere i simboli di commento dal codice che definisce la variabile **MobileService** e specificare l'URL e la chiave dell'applicazione indicati nel servizio mobile nel costruttore **MobileServiceClient**, senza modificare l'ordine.

        //public static MobileServiceClient MobileService = new MobileServiceClient( 
        //    "AppUrl", 
        //    "AppKey" 
        //); 

    Verrà creata una nuova istanza di **MobileServiceClient** utilizzata per accedere al servizio mobile.

7.  Nel file MainPage.xaml.cs aggiungere le istruzioni `using` seguenti o rimuovere i relativi simboli di commento:

        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json;

8.  Nello stesso file sostituire la definizione della classe **TodoItem** con il codice seguente:

        public class TodoItem
        {
            public string Id { get; set; }

            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }

            [JsonProperty(PropertyName = "complete")]
            public bool Complete { get; set; }
        }

9.  Aggiungere i simboli di commento alla riga che definisce la raccolta **items** esistente, quindi rimuovere i simboli di commento dalle righe seguenti:

        private MobileServiceCollection<TodoItem, TodoItem> items;
        private IMobileServiceTable<TodoItem> todoTable = 
            App.MobileService.GetTable<TodoItem>();

    Questo codice consente di creare una raccolta di binding compatibile con Servizi mobili (**items**) e una classe proxy per la tabella di database SQL **TodoItem** (**todoTable**).

10. Nel metodo **InsertTodoItem** rimuovere la riga di codice per l'impostazione della proprietà **TodoItem**.**Id**, aggiungere il modificatore **async** al metodo e rimuovere i simboli di commento dalla riga di codice seguente:

        await todoTable.InsertAsync(todoItem);

    Questo codice consente di inserire un nuovo elemento nella tabella.

11. Nel metodo **RefreshTodoItems** aggiungere il modificatore **async** al metodo, quindi rimuovere i simboli di commento dalla riga di codice seguente:

        items = await todoTable.ToCollectionAsync();

    In questo modo verrà impostata l'associazione alla raccolta di elementi in todoTable, che contiene tutti gli oggetti TodoItem restituiti dal servizio mobile.

12. Nel metodo **UpdateCheckedTodoItem** aggiungere il modificatore **async** al metodo, quindi rimuovere i simboli di commento dalla riga di codice seguente:

         await todoTable.UpdateAsync(item);

    In questo modo un aggiornamento dell'elemento verrà inviato al servizio mobile.

Una volta aggiornata l'app per consentire l'utilizzo di Servizi mobili per l'archiviazione back-end, è possibile verificarne il funzionamento in Servizi mobili.

## <a name="test-app"></a><span class="short-header">Test dell'app</span>Test dell'app nel nuovo servizio mobile

1.  In Visual Studio premere F5 per eseguire l'app.

2.  Come in precedenza, digitare un testo nella casella di testo e quindi fare clic sul pulsante **Save**.

    Un nuovo elemento verrà inviato come inserimento al servizio mobile.

3.  Nel [portale di gestione][portale di gestione] fare clic su **Mobile Services** e quindi sul servizio mobile.

4.  Fare clic sulla scheda **Data** e quindi su **Browse**.

    ![][3]

    Si noti che la tabella **TodoItem** ora contiene dati, con valori ID generati da Servizi mobili, e che alla tabella sono state aggiunte automaticamente colonne per garantire la corrispondenza con la classe TodoItem nell'app.

L'esercitazione **Introduzione ai dati** per Windows Phone 8 è terminata.

## <a name="next-steps"> </a>Passaggi successivi

In questa esercitazione vengono illustrate le nozioni di base per consentire a un'app per Windows Phone 8 di utilizzare dati in Servizi mobili. In seguito, è consigliabile eseguire l'esercitazione seguente, basata sull'app GetStartedWithData creata in questa esercitazione:

-   [Utilizzo di script per la convalida e la modifica di dati][Utilizzo di script per la convalida e la modifica di dati]
    Ulteriori informazioni sull'utilizzo di script del server in Servizi mobili per convalidare e modificare i dati inviati dall'app.

-   [Utilizzo del paging per ridefinire le query][Utilizzo del paging per ridefinire le query]
    Ulteriori informazioni su come utilizzare il paging nelle query per controllare la quantità di dati gestiti in un'unica richiesta.

Una volta completata la serie relativa ai dati, è possibile provare a eseguire una delle esercitazioni per Windows Phone 8 seguenti:

-   [Introduzione all'autenticazione][Introduzione all'autenticazione]
    Informazioni sull'autenticazione degli utenti dell'app.

-   [Introduzione alle notifiche push][Introduzione alle notifiche push]
    Informazioni sull'invio di una notifica push di base all'app con Servizi mobili.



  [mobile-services-selector-get-started-data-legacy]: ../includes/mobile-services-selector-get-started-data-legacy.md
  [Download del progetto dell'app per Windows Phone 8]: #download-app
  [Creazione del servizio mobile]: #create-service
  [Aggiunta di una tabella dati per l'archiviazione]: #add-table
  [Aggiornamento dell'app per l'utilizzo di Servizi mobili]: #update-app
  [Test dell'app in Servizi mobili]: #test-app
  [Windows Phone 8 SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268374
  [versione di valutazione gratuita di Azure]: http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fit-it%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-wp8%2F
  [app GetStartedWithData]: http://go.microsoft.com/fwlink/p/?LinkId=271146
  [versione dell'app di Windows Phone Store]: http://go.microsoft.com/fwlink/p/?LinkId=397372
  [0]: ./media/mobile-services-windows-phone-get-started-data/mobile-quickstart-startup-wp8.png
  [mobile-services-create-new-service-data]: ../includes/mobile-services-create-new-service-data.md
  [mobile-services-create-new-service-data-2]: ../includes/mobile-services-create-new-service-data-2.md
  [1]: ./media/mobile-services-windows-phone-get-started-data/mobile-add-nuget-package-wp.png
  [2]: ./media/mobile-services-windows-phone-get-started-data/mobile-dashboard-tab.png
  [portale di gestione]: https://manage.windowsazure.com/
  [3]: ./media/mobile-services-windows-phone-get-started-data/mobile-todoitem-data-browse.png
  [Utilizzo di script per la convalida e la modifica di dati]: /it-it/develop/mobile/tutorials/validate-modify-and-augment-data-wp8
  [Utilizzo del paging per ridefinire le query]: /it-it/develop/mobile/tutorials/add-paging-to-data-wp8
  [Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-wp8
  [Introduzione alle notifiche push]: /it-it/develop/mobile/tutorials/get-started-with-push-wp8
