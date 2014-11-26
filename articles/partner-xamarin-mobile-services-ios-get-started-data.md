<properties linkid="develop-mobile-tutorials-get-started-with-data-xamarin-ios" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Xamarin.iOS) - Azure Mobile Services" metaKeywords="Azure Xamarin.iOS data, Azure mobile services data, " description="Learn how to store and access data from your Azure Mobile Services Xamarin.iOS app." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="donnam" manager="dwrede" services="mobile-services"/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="donnam" />

# Introduzione ai dati in Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

In questo argomento viene illustrato come utilizzare Servizi mobili di Azure per sfruttare i dati in un'app di Xamarin.iOS. In questa esercitazione si scaricherà un'app che archivia dati in memoria, si creerà un nuovo servizio mobile e lo si integrerà con l'app e quindi si effettuerà l'accesso al portale di gestione di Azure per visualizzare le modifiche apportate ai dati durante l'esecuzione dell'app.

<div class="dev-callout"><b>Nota</b>
<p>In questa esercitazione viene descritto come &egrave; possibile utilizzare Servizi mobili di Azure per archiviare e recuperare i dati da un'app di Xamarin.iOS e vengono pertanto riproposte molte delle procedure gi&agrave; completate nella guida introduttiva a Servizi mobili. Se si tratta della prima esperienza con Servizi mobili, &egrave; consigliabile iniziare dall'esercitazione <a href="/it-it/develop/mobile/tutorials/get-started-xamarin-ios">Introduzione a Servizi mobili</a>.</p>
</div>

In questa esercitazione vengono descritte le operazioni di base seguenti:

1.  [Download del progetto dell'app di Xamarin.iOS][Download del progetto dell'app di Xamarin.iOS]
2.  [Creazione del servizio mobile][Creazione del servizio mobile]
3.  [Aggiunta di una tabella dati per l'archiviazione][Aggiunta di una tabella dati per l'archiviazione]
4.  [Aggiornamento dell'app per l'utilizzo di Servizi mobili][Aggiornamento dell'app per l'utilizzo di Servizi mobili]
5.  [Test dell'app in Servizi mobili][Test dell'app in Servizi mobili]

Per completare questa esercitazione, è necessario disporre del [componente Servizi mobili di Azure][componente Servizi mobili di Azure], [XCode 5.0][XCode 5.0], [Xamarin.iOS] e di iOS 5.0 o versione successiva.

<div class="dev-callout"><strong>Nota</strong> <p>Per completare l'esercitazione, &egrave; necessario un account Azure. Se non si dispone di un account, &egrave; possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fit-it%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-xamarin-ios%2F" target="_blank">versione di valutazione gratuita di Azure</a>.</p></div>

## <a name="download-app"></a><span class="short-header">Download del progetto</span>Download del progetto GetStartedWithData

Questa esercitazione è basata sull'[app GetStartedWithData][Download del progetto dell'app di Xamarin.iOS] che è un'app di Xamarin.iOS. L'interfaccia utente di questa app è identica a quella dell'app generata dalla guida introduttiva di Servizi mobili per Xamarin.iOS, ma in questo caso gli elementi aggiunti vengono archiviati nella memoria locale.

1.  Scaricare l'[app GetStartedWithData][Download del progetto dell'app di Xamarin.iOS].

2.  In Xamarin Studio aprire il progetto scaricato ed esaminare la classe **TodoService**.

    Si noti che sono presenti più commenti **// TODO** che specificano le procedure da eseguire per il funzionamento dell'app con il servizio mobile.

3.  Nel menu **Run** fare clic su **Start Without Debugging** per avviare l'app.

4.  Nell'app digitare un testo nella casella di testo e quindi fare clic sul pulsante **+**.

    ![][0]

    Si noti che il testo salvato è visualizzato nell'elenco riportato di seguito.

## <a name="create-service"></a><span class="short-header">Creazione del servizio mobile</span>Creazione di un nuovo servizio mobile nel portale di gestione

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

## <a name="add-table"></a><span class="short-header">Aggiunta di una nuova tabella</span>Aggiunta di una nuova tabella al servizio mobile

Prima di poter archiviare i dati dell'app nel nuovo servizio mobile, è necessario creare una nuova tabella nell'istanza di database SQL associata.

1.  Nel portale di gestione fare clic su **Mobile Services** e quindi sul servizio mobile appena creato.

2.  Fare clic sulla scheda **Data** e quindi su **+Create**.

    ![][1]

    Verrà visualizzata la finestra di dialogo **Create new table**.

3.  In **Table name** digitare *TodoItem*, quindi fare clic sul segno di spunta.

    ![][2]

    Verrà creata una nuova tabella di archiviazione **TodoItem** con le autorizzazioni predefinite impostate. Questo significa che qualsiasi utente dell'app potrà accedere alla tabella e modificare i dati.

    <div class="dev-callout"> 
<b>Nota</b> 
<p>Nella Guida introduttiva di Servizi mobili viene usato lo stesso nome di tabella. Tuttavia, ogni tabella viene creata in uno schema specifico per un determinato servizio mobile. In questo modo &egrave; possibile evitare conflitti di dati quando pi&ugrave; servizi mobili utilizzano lo stesso database.</p> 
</div>

4.  Fare clic sulla nuova tabella **TodoItem** e verificare che non siano presenti righe di dati.

5.  Fare clic sulla scheda **Columns** e verificare che sia presente una sola colonna **id**, che viene creata automaticamente.

    Questo è il requisito minimo per le tabelle in Servizi mobili.

    <div class="dev-callout"><b>Nota</b>
<p>Se nel servizio mobile &egrave; abilitato lo schema dinamico, vengono automaticamente create nuove colonne quando al servizio mobile vengono inviati oggetti JSON mediante un'operazione di inserimento o aggiornamento.</p>
</div>

È ora possibile utilizzare il nuovo servizio mobile come archivio dati per l'app.

## <a name="update-app"></a><span class="short-header">Aggiornamento dell'app</span>Aggiornamento dell'app per l'utilizzo del servizio mobile per l'accesso ai dati

Ora che il servizio mobile è pronto, è possibile aggiornare l'app in modo che gli elementi vengano archiviati in Servizi mobili anziché nella raccolta locale.

1.  Se **Servizi mobili di Azure** non è già elencato nella cartella dei componenti, per accedervi fare clic con il pulsante destro del mouse su **Components**, scegliere **Get More Components** e quindi cercare **Servizi mobili di Azure**.

2.  Nella visualizzazione Solution di Xamarin Studio aprire la classe **TodoService** e rimuovere i simboli di commento dall'istruzione `using` seguente:

        using Microsoft.WindowsAzure.MobileServices;

3.  Nel portale di gestione fare clic su **Mobile Services** e quindi sul servizio mobile appena creato.

4.  Fare clic sulla scheda **Dashboard** e prendere nota del valore di **Site URL**, quindi fare clic su **Manage keys** e prendere nota del valore di **Application key**.

    ![][3]

5.  Nella classe **Constants** rimuovere i simboli di commento dalle costanti seguenti:

        public const string ApplicationURL = @"AppUrl";
        public const string ApplicationKey = @"AppKey";

6.  Sostituire **AppUrl** e **AppKey** nelle costanti precedenti con i valori recuperati dal portale di gestione.

7.  Nella classe **TodoService** rimuovere i simboli di commento dalla riga di codice seguente:

        private MobileServiceClient client;

    Verrà creata una proprietà che rappresenta il client di Servizi mobili che si connette al servizio

8.  Nella classe **TodoService** rimuovere i simboli di commento dalla riga di codice seguente:

        private IMobileServiceTable<TodoItem> todoTable;  

    Verrà creata una rappresentazione della proprietà per la tabella di Servizi mobili.

9.  Rimuovere i simboli di commento dalle righe seguenti nel costruttore **TodoService**:

        // TODO:: Uncomment these lines to use Mobile Services
        client = new MobileServiceClient(Constants.ApplicationURL, Constants.ApplicationKey).WithFilter(this); 
        todoTable = client.GetTable<TodoItem>(); 

    Verranno create un'istanza del client di Servizi mobili e un'istanza della tabella TodoItem.

10. Rimuovere i simboli di commento dalle righe seguenti nel metodo **RefreshDataAsync** in **TodoService**

        // TODO:: Uncomment these lines to use Mobile Services

    try
     {
     // This code refreshes the entries in the list view by querying the TodoItems table.
     // The query excludes completed TodoItems
     Items = await todoTable
     .Where (todoItem =\> todoItem.Complete == false).ToListAsync();
     }
     catch (MobileServiceInvalidOperationException e)
     {
     Console.Error.WriteLine (@"ERROR {0}", e.Message);
     return null;
     }

    Verrà creata una query che restituisce tutte le attività non ancora completate.

11. Individuare il metodo **InsertTodoItemAsync** e rimuovere i simboli di commento dalla riga seguente:

        await todoTable.InsertAsync(todoItem);

    Il codice invia una richiesta insert al servizio mobile.

12. Individuare il metodo **CompleteItemAsync** e rimuovere i simboli di commento dalla riga seguente:

        await todoTable.UpdateAsync(item);

    Il codice rimuove gli elementi TodoItems dopo che sono stati contrassegnati come completati.

Una volta aggiornata l'app per consentire l'utilizzo di Servizi mobili per l'archiviazione back-end, è possibile verificarne il funzionamento in Servizi mobili.

## <a name="test-app"></a><span class="short-header">Test dell'app</span>Test dell'app nel nuovo servizio mobile

1.  In Xamarin Studio selezionare un emulatore o un dispositivo in cui eseguire la distribuzione da una delle caselle combinate principali, quindi passare al menu **Run** e selezionare **Start WithoutDebugging** per avviare l'app.

    Verrà eseguito il client di Servizi mobili di Azure, compilato con Xamarin.iOS, e verrà eseguita una query sugli elementi dal servizio mobile.

2.  Come in precedenza, digitare un testo nella casella di testo e quindi fare clic sul pulsante **+**.

    Un nuovo elemento verrà inviato come inserimento al servizio mobile.

3.  Nel [portale di gestione][portale di gestione] fare clic su **Mobile Services** e quindi sul servizio mobile.

4.  Fare clic sulla scheda **Data** e quindi su **Browse**.

    ![][4]

    Si noti che la tabella **TodoItem** ora contiene dati, con valori ID generati da Servizi mobili, e che alla tabella sono state aggiunte automaticamente colonne per garantire la corrispondenza con la classe TodoItem nell'app.

L'esercitazione **Introduzione ai dati** per Xamarin.iOS è terminata.

## Download dell'esempio completato

Scaricare il [progetto di esempio completato][Download del progetto dell'app di Xamarin.iOS]. Assicurarsi di aggiornare le variabili **applicationURL** e **applicationKey** con le proprie impostazioni di Azure.

## <a name="next-steps"> </a>Passaggi successivi

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

 <!-- URLs. TODO:: update download link, github link, and completed example project with new Xamarin.iOs projects -->

  [Download del progetto dell'app di Xamarin.iOS]: http://go.microsoft.com/fwlink/p/?LinkId=331302
  [Creazione del servizio mobile]: #create-service
  [Aggiunta di una tabella dati per l'archiviazione]: #add-table
  [Aggiornamento dell'app per l'utilizzo di Servizi mobili]: #update-app
  [Test dell'app in Servizi mobili]: #test-app
  [componente Servizi mobili di Azure]: http://components.xamarin.com/view/azure-mobile-services/
  [XCode 5.0]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [0]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-quickstart-startup-ios.png
  [1]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-data-tab-empty.png
  [2]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-create-todoitem-table.png
  [3]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-dashboard-tab.png
  [portale di gestione]: https://manage.windowsazure.com/
  [4]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-todoitem-data-browse.png
  [Utilizzo di script per la convalida e la modifica di dati]: /it-it/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios
  [Utilizzo del paging per ridefinire le query]: /it-it/develop/mobile/tutorials/add-paging-to-data-xamarin-ios
  [Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-xamarin-ios
  [Introduzione alle notifiche push]: /it-it/develop/mobile/tutorials/get-started-with-push-xamarin-ios
