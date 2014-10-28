<properties linkid="develop-mobile-tutorials-get-started-with-data-html" urlDisplayName="Get Started with Data (HTML5)" pageTitle="Get started with data (HTML 5) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your HTML app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="09/24/2014" ms.author="glenga"></tags>

# Introduzione ai dati in Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-get-started-data][]]

In questo argomento viene illustrato come utilizzare Servizi mobili di Azure per sfruttare i dati in un'app HTML. In questa esercitazione si scaricherà un'app che archivia dati in memoria, si creerà un nuovo servizio mobile e lo si integrerà con l'app e quindi si effettuerà l'accesso al portale di gestione di Azure per visualizzare le modifiche apportate ai dati durante l'esecuzione dell'app.

> [WACOM.NOTE]Questa esercitazione descrive come è possibile usare Servizi mobili di Azure per archiviare e recuperare i dati da un'app HTML e ripropone quindi molte delle procedure già completate nella guida introduttiva a Servizi mobili. Se si tratta della prima esperienza con Servizi mobili, è consigliabile iniziare dall'esercitazione [Introduzione a Servizi mobili][].

In questa esercitazione vengono descritte le operazioni di base seguenti:

1.  [Download del progetto dell'app HTML][]
2.  [Creazione del servizio mobile][]
3.  [Aggiunta di una tabella dati per l'archiviazione][]
4.  [Aggiornamento dell'app per l'utilizzo di Servizi mobili][]
5.  [Test dell'app in Servizi mobili][]

<div class="dev-callout"><strong>Nota</strong> <p>Per completare l'esercitazione, &egrave; necessario un account Azure. Se non si dispone di un account, &egrave; possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fit-it%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-html%2F" target="_blank">versione di valutazione gratuita di Azure</a>.</p></div>

### Requisiti aggiuntivi

È possibile ospitare l'app GetStartedWithData in qualsiasi server Web. Per semplicità, tuttavia, sono stati forniti script che consentono di eseguire l'app in `http://localhost:8000`.

-   Per completare questa esercitazione e utilizzare localhost, è necessario che nel computer locale sia in esecuzione uno dei server Web seguenti:

    -   **In Windows**: IIS Express. IIS Express viene installato tramite l'[Installazione guidata piattaforma Web Microsoft].
    -   **In MacOS X**: Python, che dovrebbe essere già installato.
    -   **In Linux**: Python. È necessario installare la [versione più recente di Python].

    È possibile utilizzare qualsiasi server Web per ospitare l'app, tuttavia quelli indicati sono i server Web supportati dagli script scaricati.

-   Un Web browser che supporta HTML5.

## <a name="download-app"></a><span class="short-header">Download del progetto</span>Download del progetto GetStartedWithData

Questa esercitazione è basata sull'[app GetStartedWithData][] che è un'app HTML5. L'interfaccia utente di questa app è identica a quella dell'app generata dalla guida introduttiva di Servizi mobili, ma in questo caso gli elementi aggiunti vengono archiviati nella memoria locale.

1.  [Download dei file di progetto dell'app HTML][app GetStartedWithData].

2.  In un editor HTML aprire il progetto scaricato ed esaminare il file app.js.

    Si noti che gli elementi aggiunti vengono archiviati in un oggetto **Array** in memoria (**staticItems**). Aggiornare la pagina. I dati scompariranno dalla visualizzazione perché non sono persistenti.

3.  Avviare uno dei file di comando seguenti dalla sottocartella **server**.

    -   **launch-windows** (computer Windows)
    -   **launch-mac.command** (computer Mac OS X)
    -   **launch-linux.sh** (computer Linux)

    <div class="dev-callout"><b>Nota</b>
    <p>In un computer Windows, digitare 'R' quando PowerShell chiede di confermare che si desidera eseguire lo script. Il Web browser potrebbe visualizzare un avviso in cui si consiglia di non eseguire lo script in quanto scaricato da Internet. In questo caso, &egrave; necessario richiedere che il browser proceda nel caricamento dello script.</p>
</div>

    Verrà quindi avviato un server Web nel computer locale per ospitare la nuova app.

4.  Aprire l'URL <http://localhost:8000/> in un Web browser per avviare l'app.

5.  Nell'app digitare un testo significativo, ad esempio *Complete the tutorial*, in **Enter new task** e quindi fare clic su **Add**.

    ![][]

    Si noti che il testo salvato viene aggiunto alla matrice **staticItems** la pagina viene aggiornata per visualizzare il nuovo elemento.

## <a name="create-service"></a><span class="short-header">Creazione del servizio mobile</span>Creazione di un nuovo servizio mobile nel portale di gestione

[WACOM.INCLUDE [mobile-services-create-new-service-data][]]

## <a name="add-table"></a><span class="short-header">Aggiunta di una nuova tabella</span>Aggiunta di una nuova tabella al servizio mobile

Prima di poter archiviare i dati dell'app nel nuovo servizio mobile, è necessario creare una nuova tabella nell'istanza di database SQL associata.

1.  Nel portale di gestione fare clic su **Mobile Services** e quindi sul servizio mobile appena creato.

2.  Fare clic sulla scheda **Data** e quindi su **+Create**.

    ![][1]

    Verrà visualizzata la finestra di dialogo **Create new table**.

3.  In **Table name** digitare *TodoItem*, quindi fare clic sul segno di spunta.

    ![][2]

    Verrà creata una nuova tabella di archiviazione, **TodoItem**, con le autorizzazioni predefinite impostate. Questo significa che chiunque disponga della chiave dell'applicazione, che viene distribuita con l'app, potrà accedere alla tabella e modificare i dati.

    <div class="dev-callout"> 
<b>Nota</b> 
<p>Nella Guida introduttiva di Servizi mobili viene usato lo stesso nome di tabella. Tuttavia, ogni tabella viene creata in uno schema specifico per un determinato servizio mobile. In questo modo &egrave; possibile evitare conflitti di dati quando pi&ugrave; servizi mobili utilizzano lo stesso database.</p> 
</div>

4.  Fare clic sulla nuova tabella **TodoItem** e verificare che non siano presenti righe di dati.

5.  Fare clic sulla scheda **Columns**. Verificare che siano state create automaticamente le colonne predefinite seguenti:

    | Nome colonna  | Tipo              | Indice                             |
    |---------------|-------------------|------------------------------------|
    | id            | stringa           | Indicizzata                        |
    | \_\_createdAt | data              | Indicizzata                        |
    | \_\_updatedAt | data              | <font color="transparent">-</font> |
    | \_\_version   | timestamp (MSSQL) | <font color="transparent">-</font> |

    Questo è il requisito minimo per le tabelle in Servizi mobili.

    <div class="dev-callout"><b>Nota</b>
<p>Se nel servizio mobile &egrave; abilitato lo schema dinamico, vengono automaticamente create nuove colonne quando al servizio mobile vengono inviati oggetti JSON mediante un'operazione di inserimento o aggiornamento.</p>
</div>

6.  Nella scheda **Configura**, verificare che `localhost` sia giù indicato nell'elenco **Consentire le richieste da nomi host** in **Condivisione risorse tra le origini (CORS)**. In caso contrario, digitare `localhost` nel campo **Nome host** e quindi fare clic su **Salva**.

    ![][3]

    <div class="dev-callout"><b>Nota</b>
    <p>Se si distribuisce l'app di guida introduttiva in un server Web diverso da localhost, &egrave; necessario aggiungere il nome host del server Web all'elenco <strong>Consentire le richieste da nomi host</strong>. Per altre informazioni, vedere <a href="http://msdn.microsoft.com/it-it/library/windowsazure/dn155871.aspx" target="_blank">Utilizzare la condivisione di risorse tra origini</a>.</p>
</div>

È ora possibile utilizzare il nuovo servizio mobile come archivio dati per l'app.

## <a name="update-app"></a><span class="short-header">Aggiornamento dell'app</span>Aggiornamento dell'app per l'utilizzo del servizio mobile per l'accesso ai dati

Ora che il servizio mobile è pronto, è possibile aggiornare l'app in modo che gli elementi vengano archiviati in Servizi mobili anziché nella raccolta locale.

1.  Nel portale di gestione fare clic su **Mobile Services** e quindi sul servizio mobile appena creato.

2.  Fare clic sulla scheda **Dashboard** e prendere nota del valore di **Site URL**, quindi fare clic su **Manage keys** e prendere nota del valore di **Application key**.

    ![][4]

    Questi valori sono necessari per accedere al servizio mobile dal codice dell'app.

3.  Nell'editor Web aprire il file di progetto index.html e aggiungere il codice seguente ai riferimenti allo script per la pagina:

        <script src='http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.1.2.min.js'></script>

4.  Nell'editor aprire il file app.js, rimuovere i simboli di commento dal codice seguente che definisce la variabile **MobileServiceClient**, infine specificare l'URL e la chiave dell'applicazione indicati nel servizio mobile nel costruttore **MobileServiceClient**, senza modificare l'ordine.

        var MobileServiceClient = WindowsAzure.MobileServiceClient,
            client = new MobileServiceClient('AppUrl', 'AppKey'),               

    Verrà creata una nuova istanza di MobileServiceClient utilizzata per accedere al servizio mobile.

5.  Rimuovere quindi i simboli di commento dalle righe di codice seguenti:

        var itemCount = 0;
        var staticItems = [];

    I dati verranno archiviati nel servizio mobile e non in una matrice in memoria.

6.  Rimuovere quindi i simboli di commento dalla riga di codice seguente:

        todoItemTable = client.getTable('todoitem');

    Questo codice crea un oggetto proxy (**todoItemTable**) per l'elemento **TodoItem** del database SQL.

7.  Sostituire il gestore di eventi **$('\#add-item').submit** con il codice seguente:

        $('#add-item').submit(function(evt) {
            var textbox = $('#new-item-text'),
                itemText = textbox.val();
            if (itemText !== '') {
                todoItemTable.insert({ text: itemText, complete: false })
                    .then(refreshTodoItems);
            }
            textbox.val('').focus();
            evt.preventDefault();
        });

    Questo codice consente di inserire un nuovo elemento nella tabella.

8.  Sostituire il metodo **refreshTodoItems** con il codice seguente:

        function refreshTodoItems() {

            var query = todoItemTable;

            query.read().then(function(todoItems) {
                listItems = $.map(todoItems, function(item) {
                    return $('<li>')
                        .attr('data-todoitem-id', item.id)
                        .append($('<button class="item-delete">Delete</button>'))
                        .append($('<input type="checkbox" class="item-complete">').prop('checked', item.complete))
                        .append($('<div class="dev-callout"><b>Nota</b>
<p>Se &egrave; necessario riavviare il server Web, ripetere la procedura illustrata nella prima sezione.</p>
</div>

2.  Come in precedenza, digitare un testo in **Enter new task** e quindi fare clic su **Add**.

    Un nuovo elemento verrà inviato come inserimento al servizio mobile.

3.  Nel [portale di gestione][] fare clic su **Mobile Services** e quindi sul servizio mobile.

4.  Fare clic sulla scheda **Data** e quindi su **Browse**.

    ![][5]

    Si noti che la tabella **TodoItem** ora contiene dati, con valori ID generati da Servizi mobili, e che alla tabella sono state aggiunte automaticamente colonne per garantire la corrispondenza con la classe TodoItem nell'app.

5.  Nell'app selezionare uno degli elementi nell'elenco, quindi tornare alla scheda Browse nel portale e fare clic su **Refresh**.

    Si noti che il valore di complete è cambiato da **false** a **true**.

6.  Nel file di progetto app.js individuare il metodo **RefreshTodoItems** e sostituire la riga di codice che definisce la `query` con il codice seguente:

        var query = todoItemTable.where({ complete: false });

7.  Caricare di nuovo la pagina e selezionare un altro elemento nell'elenco.

    Si noti che l'elemento selezionato non è più presente nell'elenco. Ogni aggiornamento comporta un round trip al servizio mobile, che ora restituisce i dati filtrati.

L'esercitazione **Introduzione ai dati** è terminata.

## <a name="next-steps"> </a>Passaggi successivi

In questa esercitazione sono state illustrate le nozioni di base per consentire a un'app HTML di utilizzare dati in Servizi mobili. In seguito, è consigliabile eseguire una delle esercitazioni seguenti, basate sull'app GetStartedWithData creata in questa esercitazione:

-   [Utilizzo di script per la convalida e la modifica di dati][]
    Ulteriori informazioni sull'utilizzo di script del server in Servizi mobili per convalidare e modificare i dati inviati dall'app.

-   [Utilizzo del paging per ridefinire le query][]
    Ulteriori informazioni su come utilizzare il paging nelle query per controllare la quantità di dati gestiti in un'unica richiesta.

Dopo avere completato la serie di esercitazioni relative ai dati, è possibile passare alle esercitazioni che illustrano come autenticare gli utenti dell'app, iniziando da [Introduzione all'autenticazione][].

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [mobile-services-selector-get-started-data]: ../includes/mobile-services-selector-get-started-data.md
  [Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started-html
  [Download del progetto dell'app HTML]: #download-app
  [Creazione del servizio mobile]: #create-service
  [Aggiunta di una tabella dati per l'archiviazione]: #add-table
  [Aggiornamento dell'app per l'utilizzo di Servizi mobili]: #update-app
  [Test dell'app in Servizi mobili]: #test-app
  [versione di valutazione gratuita di Azure]: http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fit-it%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-html%2F
  [app GetStartedWithData]: http://go.microsoft.com/fwlink/?LinkID=286345
  []: ./media/mobile-services-html-get-started-data/mobile-quickstart-startup-html.png
  [mobile-services-create-new-service-data]: ../includes/mobile-services-create-new-service-data.md
  [1]: ./media/mobile-services-html-get-started-data/mobile-data-tab-empty.png
  [2]: ./media/mobile-services-html-get-started-data/mobile-create-todoitem-table.png
  [3]: ./media/mobile-services-html-get-started-data/mobile-services-set-cors-localhost.png
  [Utilizzare la condivisione di risorse tra origini]: http://msdn.microsoft.com/it-it/library/windowsazure/dn155871.aspx
  [4]: ./media/mobile-services-html-get-started-data/mobile-dashboard-tab.png
  [portale di gestione]: https://manage.windowsazure.com/
  [5]: ./media/mobile-services-html-get-started-data/mobile-todoitem-data-browse.png
  [Utilizzo di script per la convalida e la modifica di dati]: /it-it/develop/mobile/tutorials/validate-modify-and-augment-data-html
  [Utilizzo del paging per ridefinire le query]: /it-it/develop/mobile/tutorials/add-paging-to-data-html
  [Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-html
