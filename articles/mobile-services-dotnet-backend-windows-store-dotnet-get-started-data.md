<properties 
	pageTitle="Introduzione ai dati (Windows Store) | Mobile Dev Center" 
	description="Informazioni su come iniziare a usare Servizi mobili per sfruttare i dati nell'app per Windows Store." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/23/2014" 
	ms.author="wesmc"/>

# Aggiungere Servizi mobili a un'app esistente

[AZURE.INCLUDE [mobile-services-selector-get-started-data-legacy](../includes/mobile-services-selector-get-started-data-legacy.md)]

<div class="dev-center-tutorial-subselector">
	<a href="/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/" title=".NET backend" class="current">Back-end .NET</a> | 
	<a href="/it-it/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/" title="JavaScript backend">Back-end JavaScript</a>
</div>

Questo argomento descrive come usare Servizi mobili di Azure come origine dati di back-end per un'app di Windows Store. In questa esercitazione si scaricherà un progetto di Visual Studio 2013 per un'app che archivia dati in memoria, si creerà un nuovo servizio mobile e lo si integrerà con l'app e quindi si visualizzeranno le modifiche apportate ai dati durante l'esecuzione dell'app.

Il servizio mobile che verrà creato in questa esercitazione è un servizio mobile back-end di .NET. Il Back-end .NET consente di usare i linguaggi .NET e Visual Studio per la logica di business sul lato server nel servizio mobile; inoltre è possibile eseguire il servizio mobile ed effettuarne il debugging sul computer locale in uso. Per creare un servizio mobile che consenta di scrivere la logica di business sul lato server in JavaScript, vedere la versione per back-end JavaScript di questo argomento.

>[AZURE.NOTE] Questo argomento descrive come aggiungere Servizi mobili di Azure a un progetto di Windows Store. È possibile usare gli strumenti di Visual Studio 2013 per aggiungere lo stesso servizio mobile back-end .NET a un progetto di app di Windows universale. Per altre informazioni, vedere la [versione per l'app di Windows universale](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data) di questa esercitazione.

Questa esercitazione descrive le operazioni di base seguenti:

1. [Scaricare il progetto dell'app di Windows Store]
2. [Creare un nuovo servizio mobile]
3. [Scaricare il servizio mobile in locale]
4. [Aggiornare l'app di Windows Store per l'uso del servizio mobile]
5. [Testare l'app di Windows Store nel servizio ospitato localmente]
6. [Pubblicare il servizio mobile in Azure]
7. [Testare l'app di Windows Store nel servizio ospitato in Azure]

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un account Azure attivo. Se non si ha un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fit-it%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started-data%2F).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>. È disponibile una versione di valutazione gratuita.

##<a name="download-app"></a>Scaricare il progetto GetStartedWithData

Questa esercitazione è basata sull'[app GetStartedWithMobileServices][sito degli esempi di codice di Developer Network], ovvero un progetto di app di Windows Store disponibile in Visual Studio 2013. L'interfaccia utente di questa app è simile a quella dell'app generata dalla guida introduttiva di Servizi mobili, ma in questo caso gli elementi aggiunti vengono archiviati nella memoria locale. 

1. Scaricare la versione in C# dell'app di esempio GetStartedWithMobileServices dal [sito degli esempi di codice di Developer Network]. 

2. Eseguire Visual Studio 2013 con privilegi amministrativi facendo clic con il pulsante destro del mouse su Visual Studio e scegliendo **Esegui come amministratore**.

3. In Visual Studio 2013 aprire il progetto scaricato ed esaminare il file MainPage.xaml.cs.

   	Si noti che gli oggetti **TodoItem** aggiunti sono archiviati in un oggetto **ObservableCollection&lt;TodoItem&gt;** in memoria.

4. Premere **F5** per ricompilare il progetto e avviare l'app.

5. Nell'app digitare un testo in **Insert a TodoItem**, quindi fare clic su **Save**.

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


4. Decomprimere il progetto iniziale personalizzato del servizio scaricato in precedenza. Copiare le cartelle estratte dal file ZIP nella stessa directory **C#** in cui si trova il file della soluzione GetStartedWithData (estensione .sln). In questo modo, per il componente Gestione pacchetti NuGet sarà più semplice mantenere sincronizzati tutti i pacchetti.

    ![][26]

5. In Esplora soluzioni di Visual Studio fare clic con il pulsante destro del mouse sulla soluzione per l'app di Windows Store GetStartedWithData. Scegliere **Aggiungi**, quindi **Progetto esistente**.

    ![][4]

6. Nella finestra di dialogo Aggiungi progetto esistente passare alla cartella del progetto di servizio mobile che è stata precedentemente spostata nella directory **C#**. Selezionare il file di progetto di C# (estensione .csproj) nella sottodirectory del servizio. Fare clic su **Apri** per aggiungere il progetto alla soluzione.

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

2. Nella finestra di dialogo Gestisci pacchetti NuGet cercare **WindowsAzure.MobileServices** nella raccolta di pacchetti online e fare clic per installare il pacchetto NuGet per Servizi mobili di Azure. Chiudere la finestra di dialogo.

    ![][8]

3. Tornare al portale di gestione di Azure e individuare il passaggio **Connettere l'app e archiviare dati nel servizio**. Assicurarsi che sia selezionato il linguaggio **C#**. Copiare il frammento di codice che crea la connessione `MobileServiceClient`.

    ![][9]

4. In Visual Studio aprire il file App.xaml.cs. Incollare il frammento di codice all'inizio della definizione di classe `App`. Aggiungere anche l'istruzione `using` seguente all'inizio del file e salvare il file.

		using Microsoft.WindowsAzure.MobileServices;

    ![][10]


5. In Visual Studio aprire il file MainPage.xaml.cs e aggiungere l'istruzione using: 

		using Microsoft.WindowsAzure.MobileServices;

6. In Visual Studio, nel file MainPage.xaml.cs sostituire la definizione di classe `MainPage` con la definizione seguente e salvare il file. 

    Questo codice usa Mobile Services SDK per abilitare l'archiviazione dei dati dell'app in una tabella fornita dal servizio anziché nella memoria locale. I tre metodi principali sono `InsertTodoItem`, `RefreshTodoItems` e `UpdateCheckedTodoItem`. Questi tre metodi consentono di eseguire operazioni asincrone di inserimento, aggiornamento o query sulla raccolta dati con una tabella in Azure.

        public sealed partial class MainPage : Page
        {
            private MobileServiceCollection<TodoItem, TodoItem> items;
            private IMobileServiceTable<TodoItem> todoTable = 
                App.MobileService.GetTable<TodoItem>();            
            public MainPage()
            {
                this.InitializeComponent();
            }
            private async void InsertTodoItem(TodoItem todoItem)
            {
                await todoTable.InsertAsync(todoItem); 
                ite
	ms.Add(todoItem);
            }
            private async void RefreshTodoItems()
            {
                items = await todoTable.ToCollectionAsync(); 
                ListIte
	ms.ItemsSource = items;
            }
            private async void UpdateCheckedTodoItem(TodoItem item)
            {
                await todoTable.UpdateAsync(item);      
            }
            private void ButtonRefresh_Click(object sender, RoutedEventArgs e)
            {
                RefreshTodoItems();
            }
            private void ButtonSave_Click(object sender, RoutedEventArgs e)
            {
                var todoItem = new TodoItem { Text = TextInput.Text };
                InsertTodoItem(todoItem);
            }
            private void CheckBoxComplete_Checked(object sender, RoutedEventArgs e)
            {
                CheckBox cb = (CheckBox)sender;
                TodoItem item = cb.DataContext as TodoItem;
                UpdateCheckedTodoItem(item);
            }
            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
                RefreshTodoItems();
            }
        }


##<a name="test-locally-hosted"></a>Testare l'app di Windows Store nel servizio ospitato localmente

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service-data](../includes/mobile-services-dotnet-backend-test-local-service-data.md)]

##<a name="publish-mobile-service"></a>Pubblicare il servizio mobile in Azure

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

##<a name="test-azure-hosted"></a>Testare il servizio mobile pubblicato in Azure

1. In Visual Studio aprire il file App.xaml.cs.  Impostare come commento il codice che crea il client `MobileServiceClient` che esegue la connessione al servizio mobile ospitato localmente. Rimuovere i simboli di commento dal codice per la creazione dell'oggetto `MobileServiceClient` che esegue la connessione al servizio in Azure. Salvare le modifiche al file.

        sealed partial class App : Application
        {
            //public static MobileServiceClient MobileService = new MobileServiceClient(
            //          "http://localhost:59226"
            //);
            // Use this constructor instead after publishing to the cloud
            public static MobileServiceClient MobileService = new MobileServiceClient(
                 "https://todolist.preview.azure-mobile-preview.net/",
                 "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
            );        
            ....


2. In Visual Studio premere F5 o scegliere **Avvia debug** dal menu Debug. In questo modo, prima dell'esecuzione, l'app di Windows Store verrà ricompilata con la modifica precedente, per effettuare la connessione al servizio mobile ospitato in remoto in Azure. 


3. Immettere nuovi elementi todoitem e fare clic su **Salva** per ognuno di essi. Fare clic sulla casella di controllo per completare alcuni dei nuovi elementi. Ogni nuovo elemento TodoItem verrà archiviato e aggiornato nel database SQL precedentemente configurato per il servizio mobile nel portale di gestione di Azure. 

    ![][16]

    È possibile riavviare l'app per verificare che le modifiche siano state salvate in modo permanente nel database in Azure. 

##<a name="view-stored-data"></a>Visualizzare i dati archiviati nel database SQL

[AZURE.INCLUDE [mobile-services-dotnet-backend-view-sql-data](../includes/mobile-services-dotnet-backend-view-sql-data.md)]

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
  <br/>Altre informazioni su come usare Servizi mobili con .NET.
  
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
[0]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/app-view.png
[1]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/mobile-data-sample-download-dotnet-vs13.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/mobile-service-overview-page.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/download-service-project.png
[4]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/add-service-project-to-solution.png
[5]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/download-publishing-profile.png
[6]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/add-existing-project-dialog.png
[7]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-manage-nuget-packages.png
[8]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/manage-nuget-packages.png
[9]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/copy-mobileserviceclient-snippet.png
[10]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-pasted-mobileserviceclient.png
[11]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-build-solution.png
[12]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-run-solution.png
[16]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/azure-ite
	ms.png
[17]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/manage-sql-azure-database.png
[18]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/sql-azure-query.png

[20]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-build-service-project.png
[21]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-start-debug-service-project.png
[22]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/service-welcome-page.png
[23]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/iis-express-tray.png

[26]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/copy-service-and-packages-folder.png


<!-- URLs. -->
[Usare script per la convalida e la modifica di dati]: /it-it/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Usare il paging per ridefinire le query]: /it-it/develop/mobile/tutorials/add-paging-to-data-dotnet
[Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
[Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/
[Introduzione alle notifiche push]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/

[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Portale di gestione]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[sito degli esempi di codice di Developer Network]:  http://go.microsoft.com/fwlink/p/?LinkId=328660
[Riferimento per i concetti e le procedure di .NET per Servizi mobili]: /it-it/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
[Classe MobileServiceClient]: http://go.microsoft.com/fwlink/p/?LinkId=302030
 

<!--HONumber=42-->
