<properties pageTitle="Introduzione ai dati (Windows Store) | Mobile Dev Center" description="Informazioni su come iniziare a usare Servizi mobili per sfruttare i dati nell'app per Windows Store." services="mobile-services" documentationCenter="windows" authors="wesmc7777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/27/2014" ms.author="wesmc"/>

# Aggiungere Servizi mobili a un'app esistente

[AZURE.INCLUDE [mobile-services-selector-get-started-data-legacy](../includes/mobile-services-selector-get-started-data-legacy.md)]

Questo argomento descrive come usare Servizi mobili di Azure come origine dati di back-end per un'app di Windows Store. In questa esercitazione si scaricherà un progetto di Visual Studio 2013 per un'app che archivia dati in memoria, si creerà un nuovo servizio mobile e lo si integrerà con l'app e quindi si visualizzeranno le modifiche apportate ai dati durante l'esecuzione dell'app.

Il servizio mobile che verrà creato in questa esercitazione è un servizio mobile back-end di .NET. Il Back-end .NET consente di usare i linguaggi .NET e Visual Studio per la logica di business sul lato server nel servizio mobile; inoltre è possibile eseguire il servizio mobile ed effettuarne il debugging sul computer locale in uso. Per creare un servizio mobile che consenta di scrivere la logica di business sul lato server in JavaScript, vedere la versione per back-end JavaScript di questo argomento.

>[AZURE.NOTE] Questo argomento descrive come aggiungere Servizi mobili di Azure a un progetto di Windows Store. È possibile usare gli strumenti di Visual Studio 2013 per aggiungere lo stesso servizio mobile back-end .NET a un progetto di app di Windows universale. Per altre informazioni, vedere la [versione per l'app di Windows universale](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-data) di questa esercitazione.

Questa esercitazione spiega come eseguire le operazioni di base seguenti:

1. [Scaricare il progetto dell'app di Windows Store]
2. [Creare un nuovo servizio mobile]
3. [Scaricare il servizio mobile in locale]
4. [Aggiornare l'app di Windows Store per l'uso del servizio mobile]
5. [Testare l'app di Windows Store nel servizio ospitato localmente]
6. [Pubblicare il servizio mobile in Azure]
7. [Testare l'app di Windows Store nel servizio ospitato in Azure]

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un account Azure attivo. Se non si ha un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/it-it/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fit-it%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-javascript-get-started-data%2F).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>. È disponibile una versione di valutazione gratuita.

##<a name="download-app"></a>Scaricare il progetto GetStartedWithData

Questa esercitazione è basata sull'[app GetStartedWithMobileServices][sito di esempi di codice per sviluppatori], ovvero un progetto di app di Windows Store disponibile in Visual Studio 2013. L'interfaccia utente di questa app è simile a quella dell'app generata dalla guida introduttiva di Servizi mobili, ma in questo caso gli elementi aggiunti vengono archiviati nella memoria locale. 

1. Scaricare la versione JavaScript dell'app di esempio GetStartedWithMobileServices dal [sito di esempi di codice per sviluppatori]. 

2. Eseguire Visual Studio 2013 con privilegi amministrativi facendo clic con il pulsante destro del mouse su Visual Studio e scegliendo **Esegui come amministratore**.

3. In Visual Studio 2013 aprire il progetto scaricato. Espandere la cartella js in Esplora soluzioni ed esaminare il file default.js.

   	Si noti che gli oggetti **TodoItem** aggiunti sono archiviati in un oggetto  `WinJS.Binding.List` in memoria.

4. Premere **F5** per ricompilare il progetto e avviare l'app.

5. Nell'app digitare un testo nella casella sotto **Insert a TodoItem**, quindi fare clic su **Save**.

   	![][0]  

   	Si noti che il testo salvato viene visualizzato nella seconda colonna sotto **Query and update data**.

##<a name="create-service"></a>Creare un nuovo servizio mobile

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

##<a name="download-the-service-locally"></a>Scaricare il progetto di servizio mobile e aggiungerlo alla soluzione

1. Nel [portale di gestione di Azure] fare clic sul nuovo servizio mobile o sulla scheda della relativa icona per passare alla pagina di panoramica.

    ![][2]

2. Fare clic sulla piattaforma **Windows Store**. Nella sezione **Informazioni di base** espandere **Connetti un'app di Windows Store esistente** e fare clic sul pulsante **Download** per scaricare un progetto iniziale personalizzato per il servizio mobile. 

    ![][3]

3. Scorrere verso il basso nella sezione **Informazioni di base** fino al passaggio **Pubblica il servizio nel cloud**. Fare clic sul collegamento visualizzato nella schermata in basso per scaricare un file del profilo di pubblicazione per il servizio mobile appena scaricato.

    > [AZURE.NOTE] Salvare il file in una posizione sicura, in quanto contiene informazioni sensibili relative all'account Azure. Il file verrà eliminato dopo la pubblicazione del servizio mobile, più avanti in questo argomento. 

    ![][5]


4. Decomprimere il progetto iniziale personalizzato del servizio scaricato in precedenza. Copiare le cartelle estratte dal file ZIP nella stessa directory **JavaScript** in cui si trova il file della soluzione GetStartedWithData (con estensione sln). In questo modo, per il componente Gestione pacchetti NuGet sarà più semplice mantenere sincronizzati tutti i pacchetti. 

    ![][26]

5. In Esplora soluzioni di Visual Studio fare clic con il pulsante destro del mouse sulla soluzione per l'applicazione di Windows Store GetStartedWithData. Scegliere **Aggiungi**, quindi **Progetto esistente**.

    ![][4]

6. Nella finestra di dialogo Aggiungi progetto esistente passare alla cartella del progetto di servizio mobile che è stata precedentemente spostata nella directory **JavaScript**. Selezionare il file di progetto di C# (con estensione csproj) nella sottodirectory del servizio. Fare clic su **Apri** per aggiungere il progetto alla soluzione.

    ![][6]

7. In Visual Studio, in Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto di servizio appena aggiunto e quindi scegliere **Compila** per verificare che venga compilato senza errori. Durante la compilazione, è possibile che Gestione pacchetti NuGet debba ripristinare alcuni pacchetti NuGet a cui viene fatto riferimento nel progetto.

    ![][20]

8. Fare di nuovo clic con il pulsante destro del mouse sul progetto di servizio. Questa volta, scegliere **Avvia nuova istanza** nel menu di scelta rapida **Debug**.

    ![][21]

    Visual Studio aprirà la pagina Web predefinita per il servizio. È possibile fare clic su **Provare adesso** per testare i metodi del servizio mobile dalla pagina Web predefinita.

    ![][22]

    Per impostazione predefinita, Visual Studio ha ospitato il servizio mobile localmente in IIS Express. Questo è facilmente verificabile facendo clic con il pulsante destro del mouse sull'icona di IIS Express sulla barra delle applicazioni.

    ![][23]


#<a name="update-app"></a>Aggiornare l'app di Windows Store per l'uso del servizio mobile

In questa sezione l'app di Windows Store verrà aggiornata per l'uso del servizio mobile come servizio back-end.


1. In Visual Studio, in Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto di app per Windows Store e quindi scegliere **Gestisci pacchetti NuGet**.

    ![][7]

2. Nella finestra di dialogo Gestisci pacchetti NuGet cercare **WindowsAzure.MobileServices.WinJS** nella raccolta di pacchetti online e fare clic per installare il pacchetto NuGet per Servizi mobili di Azure. Chiudere la finestra di dialogo.

    ![][8]

3. Tornare alla pagina di panoramica del servizio mobile nel portale di gestione di Azure e individuare il passaggio **Connettere l'app e archiviare dati nel servizio**. Selezionare il linguaggio **JavaScript** e copiare il frammento di codice per creare il client  `MobileServiceClient`.

    ![][9]

4. In Visual Studio, in Esplora soluzioni espandere la cartella **js** e aprire il file default.js. Incollare il frammento di codice copiato nel gestore eventi  `app.onactivated` immediatamente prima della definizione della variabile  `todoItems`. Si noti che il frammento di codice include un costruttore impostato come commento per la connessione del servizio mobile in Azure mediante la chiave applicazione. I simboli di commento verranno rimossi in un passaggio successivo.

    ![][10]


5. Nel file default.js sostituire il resto del codice per il gestore eventi  `app.onactiviated` con il codice seguente, che definisce gli oggetti  `todoItems` e le operazioni che verranno testate con il servizio mobile. Salvare il file.

    Questo codice usa Mobile Services SDK per JavaScript per abilitare l'archiviazione dei dati dell'app in una tabella fornita dal servizio anziché nella memoria locale. I tre metodi principali sono  `insertTodoItem`, `refreshTodoItems` e  `updateCheckedTodoItem`. Questi tre metodi consentono di eseguire operazioni asincrone di inserimento, aggiornamento o query sulla raccolta dati con una tabella in Azure.

            var todoTable = client.getTable('TodoItem');
            var todoItems = new WinJS.Binding.List();
            var insertTodoItem = function (todoItem) {
                // This code inserts a new TodoItem into the database. 
                // Mobile Services assigns each item an id and the 
                // item is added to the Binding List
                todoTable.insert(todoItem).done(function (item) {
                    todoItems.push(item);
                });
            };
            var refreshTodoItems = function () {
                // This code refreshes the entries in the list view 
                // by querying the TodoItems table.
                todoTable.where()
                    .read()
                    .done(function (results) {
                        todoItems = new WinJS.Binding.List(results);
                        listItems.winControl.itemDataSource = todoItems.dataSource;
                    });
            };
            var updateCheckedTodoItem = function (todoItem) {
                // This code updates the database. 
                // When the MobileService responds, 
                // the item is removed from the list 
                todoTable.update(todoItem).done(function (item) {
                    todoItems.splice(todoItems.indexOf(item), 1);
                });
            };
            buttonSave.addEventListener("click", function () {
                insertTodoItem({
                    text: textInput.value,
                    complete: false
                });
            });
            buttonRefresh.addEventListener("click", function () {
                refreshTodoItems();
            });
            listItems.addEventListener("change", function (eventArgs) {
                var todoItem = eventArgs.target.dataContext.backingData;
                todoItem.complete = eventArgs.target.checked;
                updateCheckedTodoItem(todoItem);
            });
            refreshTodoItems();

6. In Visual Studio, in Esplora soluzioni aprire il file default.html. Nella parte superiore del file aggiungere un nuovo riferimento allo script WinJS per MobileServices.js. Salvare il file.

        <script type="text/javascript" src="/js/MobileServices.js"></script>

    ![][19]

##<a name="test-locally-hosted"></a>Testare l'app di Windows Store nel servizio ospitato localmente

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service-data](../includes/mobile-services-dotnet-backend-test-local-service-data.md)]

##<a name="publish-mobile-service"></a>Pubblicare il servizio mobile in Azure

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]


##<a name="test-azure-hosted"></a>Testare il servizio mobile pubblicato in Azure

1. In Visual Studio, in Esplora soluzioni espandere la cartella **js** e aprire il file default.js.  Impostare come commento il codice che crea il client  `MobileServiceClient` che esegue la connessione al servizio mobile ospitato localmente. Rimuovere i simboli di commento dal codice per la creazione dell'oggetto  `MobileServiceClient` che esegue la connessione al servizio in Azure. Salvare le modifiche al file.

        //var client = new WindowsAzure.MobileServiceClient(
        //          "http://localhost:59226"
        //);	
        // Use this constructor instead after publishing to the cloud
        var client = new WindowsAzure.MobileServiceClient(
            "https://todolist.preview.azure-mobile-preview.net/",
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");


2. In Visual Studio premere F5 o scegliere **Avvia debug** dal menu Debug. In questo modo, prima dell'esecuzione, l'app di Windows Store verrà ricompilata con la modifica precedente, per effettuare la connessione al servizio mobile ospitato in remoto in Azure. 

    ![][12]


3. Immettere nuovi elementi todoitem e fare clic su **Salva** per ognuno di essi. Fare clic sulla casella di controllo per completare alcuni dei nuovi elementi. Ogni nuovo elemento TodoItem verrà archiviato e aggiornato nel database SQL precedentemente configurato per il servizio mobile nel portale di gestione di Azure. 

    ![][16]

    È possibile riavviare l'app per verificare che le modifiche siano state salvate in modo permanente nel database in Azure. È inoltre possibile esaminare il database usando il portale di gestione di Azure oppure il comando Esplora oggetti di SQL Server in Visual Studio. I due passaggi successivi illustrano come usare il portale di gestione di Azure per visualizzare le modifiche nel database.

4. Nel portale di gestione di Azure fare clic su Gestisci per il database associato al servizio mobile.

    ![][17]

5. Nel portale di gestione eseguire una query per visualizzare le modifiche apportate dall'app di Windows Store. La query da usare sarà simile alla seguente, ma anziché `todolist` conterrà il nome del database.

        SELECT * FROM [todolist].[todoitems]	

    ![][18]

L'esercitazione **Introduzione ai dati** è terminata.

## <a name="next-steps"> </a>Passaggi successivi

In questa esercitazione vengono illustrate le nozioni di base per consentire a un'app di Windows Store di usare dati in Servizi mobili. In seguito, è consigliabile eseguire una delle esercitazioni seguenti, basate sull'app GetStartedWithData creata in questa esercitazione:

* [Usare script per la convalida e la modifica di dati]
  <br/>Altre informazioni sull'uso di script del server in Servizi mobili per convalidare e modificare i dati inviati dall'app.

* [Usare il paging per ridefinire le query]
  <br/>Informazioni su come usare il paging nelle query per controllare la quantità di dati gestiti in un'unica richiesta.

Una volta completata la serie relativa ai dati, provare a eseguire una delle esercitazioni seguenti:

* [Introduzione all'autenticazione]
  <br/>Informazioni sull'autenticazione degli utenti dell'app.

* [Introduzione alle notifiche push] 
  <br/>Informazioni sull'invio di una notifica push di base all'app.

* [Riferimento per i concetti e le procedure di .NET per Servizi mobili]
  <br/>Altre informazioni su come usare Servizi mobili con HTML e JavaScript.
  
<!-- Anchors. -->

[Scaricare il progetto dell'app di Windows Store]: #download-app
[Creare un nuovo servizio mobile]: #create-service
[Scaricare il servizio mobile in locale]: #download-the-service-locally
[Aggiornare l'app di Windows Store per l'uso del servizio mobile]: #update-app
[Testare l'app di Windows Store nel servizio ospitato localmente]: #test-locally-hosted
[Pubblicare il servizio mobile in Azure]: #publish-mobile-service
[Testare l'app di Windows Store nel servizio ospitato in Azure]: #test-azure-hosted
[Passaggi successivi]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/app-view.png
[1]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/mobile-data-sample-download-javascript-vs13.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/mobile-service-overview-page.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/download-service-project.png
[4]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/add-service-project-to-solution.png
[5]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/download-publishing-profile.png
[6]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/add-existing-project-dialog.png
[7]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-manage-nuget-packages.png
[8]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/manage-nuget-packages.png
[9]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/copy-mobileserviceclient-snippet.png
[10]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-pasted-mobileserviceclient.png
[11]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-build-solution.png
[12]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-run-solution.png

[16]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/azure-items.png
[17]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/manage-sql-azure-database.png
[18]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/sql-azure-query.png
[19]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-mobileservices-script-reference.png
[20]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-build-service-project.png
[21]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-start-debug-service-project.png
[22]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/service-welcome-page.png
[23]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/iis-express-tray.png

[26]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/copy-service-and-packages-folder.png

<!-- URLs. -->
[Usare script per la convalida e la modifica di dati]: /it-it/develop/mobile/tutorials/validate-modify-and-augment-data-js
[Usare il paging per ridefinire le query]: /it-it/develop/mobile/tutorials/add-paging-to-data-js
[Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started/
[Introduzione ai dati]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/
[Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/
[Introduzione alle notifiche push]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/


[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Portale di gestione]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[sito di esempi di codice per sviluppatori]:  http://go.microsoft.com/fwlink/p/?LinkId=328660
[Riferimento per i concetti e le procedure di .NET per Servizi mobili]: /it-it/documentation/articles/mobile-services-html-how-to-use-client-library/
[Classe MobileServiceClient]: http://go.microsoft.com/fwlink/p/?LinkId=302030
  

<!--HONumber=42-->
