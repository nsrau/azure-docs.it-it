<properties linkid="develop-mobile-tutorials-dotnet-backend-get-started-with-data-wp8" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Phone app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="wesmc" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/25/2014" ms.author="wesmc" />

# Introduzione ai dati in Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-get-started-data-legacy](../includes/mobile-services-selector-get-started-data-legacy.md)]

Questo argomento illustra come usare Servizi mobili di Azure come origine dati di back-end per un'app per Windows Phone (Silverlight). Questa esercitazione consente di scaricare un progetto di Visual Studio per un'app che archivia dati in memoria, di creare un nuovo servizio mobile e integrarlo con l'app e quindi di visualizzare le modifiche apportate ai dati durante l'esecuzione dell'app.

Il servizio mobile creato in questa esercitazione supporterà il runtime .NET. Questo consentirà di utilizzare linguaggi .NET e Visual Studio per la logica di business sul lato server nel servizio mobile. Per creare un servizio mobile che consenta di scrivere la logica di business sul lato server in JavaScript, vedere la [versione per back-end JavaScript][versione per back-end JavaScript] di questo argomento.

In questa esercitazione vengono descritte le operazioni di base seguenti:

1.  [Download del progetto dell'app per Windows Phone 8][Download del progetto dell'app per Windows Phone 8]
2.  [Creazione di un nuovo servizio mobile][Creazione di un nuovo servizio mobile]
3.  [Download del servizio mobile in locale][Download del servizio mobile in locale]
4.  [Aggiornamento dell'app per Windows Phone per l'uso del servizio mobile][Aggiornamento dell'app per Windows Phone per l'uso del servizio mobile]
5.  [Test dell'app per Windows Phone con il servizio ospitato in locale][Test dell'app per Windows Phone con il servizio ospitato in locale]
6.  [Pubblicazione del servizio mobile in Azure][Pubblicazione del servizio mobile in Azure]
7.  [Test dell'app per Windows Phone con il servizio ospitato in Azure][Test dell'app per Windows Phone con il servizio ospitato in Azure]

Per completare questa esercitazione, è necessario disporre di:

-   Visual Studio 2013 e [Windows Phone 8 SDK][Windows Phone 8 SDK] in esecuzione in Windows 8. Per completare questa esercitazione per la creazione di un'app per Windows Phone 8.1, è necessario usare Visual Studio 2013 Update 2 o una versione successiva.
-   Un account Microsoft Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][versione di valutazione gratuita di Azure].
    </p>
    </div>

## <a name="download-app"></a>Scaricare il progetto GetStartedWithData

Questa esercitazione è basata sull'[app GetStartedWithMobileServices][app GetStartedWithMobileServices] che è un progetto di app per Windows Phone Silverlight 8. L'interfaccia utente di questa app è simile a quella dell'app generata dalla guida introduttiva di Servizi mobili, ma in questo caso gli elementi aggiunti vengono archiviati nella memoria locale.

1.  Scaricare la versione in C# dell'app di esempio GetStartedWithMobileServices dal [sito degli esempi di codice di Developer Network][app GetStartedWithMobileServices].

    ![][0]

    > [WACOM.NOTE]Per creare un'app per Windows Phone Silverlight 8.1, nel progetto di app per Windows Phone Silverlight 8 scaricato è sufficiente impostare il sistema operativo di destinazione su Windows Phone 8.1. Per creare un'app di Windows Phone Store, scaricare la [versione dell'app di Windows Phone Store][versione dell'app di Windows Phone Store] del progetto di app di esempio GetStartedWithData.

2.  Eseguire Visual Studio con privilegi amministrativi facendo clic con il pulsante destro del mouse su Visual Studio e scegliendo **Esegui come amministratore**.

3.  In Visual Studio aprire il progetto scaricato ed esaminare il file MainPage.xaml.cs.

    Si noti che gli oggetti **TodoItem** aggiunti sono archiviati in un oggetto **ObservableCollection\<TodoItem\>** in memoria.

4.  In Visual Studio scegliere una destinazione della distribuzione per l'app. È possibile eseguire la distribuzione in un dispositivo Windows Phone o in uno degli emulatori inclusi in Windows Phone SDK. In questa esercitazione verrà illustrata la distribuzione in un emulatore.

    ![][1]

5.  Premere **F5**. L'app verrà compilata, distribuita e avviata per il debug.

6.  Nell'app digitare un testo nella casella di testo, quindi fare clic su **Save** per salvare alcuni elementi in memoria nell'app.

    ![][2]

    Si noti che il testo per ogni elemento `TodoItem` viene visualizzato sotto il pulsante di aggiornamento insieme a una casella di controllo che consente di contrassegnare l'elemento come completato.

## <a name="create-service"></a><span class="short-header">Creazione di un nuovo servizio mobile</span>Creazione di un nuovo servizio mobile

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## <a name="download-the-service-locally"></a><span class="short-header">Download del servizio in locale</span>Download del progetto di servizio mobile e aggiunta alla soluzione

1.  Nel [portale di gestione di Azure][portale di gestione di Azure] fare clic sul nuovo servizio mobile o sulla scheda della relativa icona per passare alla pagina di panoramica.

    ![][3]

2.  Fare clic sulla piattaforma **Windows Phone 8**. Nella sezione **Get Started** espandere **Connect an existing Windows Phone 8** e fare clic sul pulsante **Download** per scaricare un progetto iniziale personalizzato per il servizio mobile.

    ![][4]

3.  Anche in questa sezione fare clic sul collegamento visualizzato nella schermata in basso per scaricare un file del profilo di pubblicazione per il servizio mobile appena scaricato.

    > [WACOM.NOTE] Salvare il file in una posizione sicura, in quanto contiene informazioni sensibili relative all'account Azure. Il file verrà eliminato dopo la pubblicazione del servizio mobile, più avanti in questo argomento.

    ![][5]

4.  Decomprimere il progetto iniziale personalizzato del servizio scaricato in precedenza. Copiare le cartelle estratte dal file ZIP nella stessa directory **C#** in cui si trova il file della soluzione GetStartedWithData (con estensione sln). In questo modo, per il componente Gestione pacchetti NuGet sarà più semplice mantenere sincronizzati tutti i pacchetti.

    ![][6]

5.  In Esplora soluzioni di Visual Studio fare clic con il pulsante destro del mouse sulla soluzione per l'app di Windows Store GetStartedWithData. Scegliere **Aggiungi**, quindi **Progetto esistente**.

    ![][7]

6.  Nella finestra di dialogo Aggiungi progetto esistente passare alla cartella del progetto di servizio mobile che è stata precedentemente spostata nella directory **C#**. Selezionare il file di progetto di C# (con estensione csproj) nella sottodirectory del servizio. Fare clic su **Apri** per aggiungere il progetto alla soluzione.

    ![][8]

7.  In Visual Studio, in Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto di servizio appena aggiunto e quindi scegliere **Compila** per verificare che venga compilato senza errori. Durante la compilazione, è possibile che Gestione pacchetti NuGet debba ripristinare alcuni pacchetti NuGet a cui viene fatto riferimento nel progetto.

    ![][9]

8.  Fare di nuovo clic con il pulsante destro del mouse sul progetto di servizio. Questa volta, scegliere **Avvia nuova istanza** nel menu di scelta rapida **Debug**.

    ![][10]

    Visual Studio aprirà la pagina Web predefinita per il servizio. È possibile fare clic su **try it now** per testare i metodi del servizio mobile dalla pagina Web predefinita.

    ![][11]

    Per impostazione predefinita, Visual Studio ha ospitato il servizio mobile localmente in IIS Express. Questo è facilmente verificabile facendo clic con il pulsante destro del mouse sull'icona di IIS Express sulla barra delle applicazioni.

    ![][12]

## <a name="update-app"></a>Aggiornamento dell'app per Windows Phone per l'uso del servizio mobile

In questa sezione l'app per Windows Phone verrà aggiornata per l'utilizzo del servizio mobile come servizio back-end.

1.  In Visual Studio, in Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto di app per Windows Phone e quindi scegliere **Manage NuGet Packages**.

    ![][13]

2.  Nella finestra di dialogo Manage NuGet Packages cercare **WindowsAzure.MobileServices** nella raccolta di pacchetti online e fare clic per installare il pacchetto NuGet per Servizi mobili di Azure. Chiudere la finestra di dialogo.

    ![][14]

3.  Tornare al portale di gestione di Azure e individuare il passaggio **Connect your app and store data in your service**. Copiare il frammento di codice che crea la connessione `MobileServiceClient`.

    ![][15]

4.  In Visual Studio aprire il file App.xaml.cs. Incollare il frammento di codice all'inizio della definizione di classe `App`. Aggiungere inoltre l'istruzione `using` seguente all'inizio del file e salvare il file.

        using Microsoft.WindowsAzure.MobileServices;

    ![][16]

5.  In Visual Studio aprire il file MainPage.xaml.cs e aggiungere l'istruzione using all'inizio del file:

        using Microsoft.WindowsAzure.MobileServices;

6.  In Visual Studio, nel file MainPage.xaml.cs sostituire la definizione di classe `MainPage` con la definizione seguente e salvare il file.

    Questo codice utilizza Mobile Services SDK per abilitare l'archiviazione dei dati dell'app in una tabella fornita dal servizio anziché nella memoria locale. I tre metodi principali sono `InsertTodoItem`, `RefreshTodoItems` e `UpdateCheckedTodoItem`. Questi tre metodi consentono di eseguire operazioni asincrone di inserimento, aggiornamento o query sulla raccolta dati in una tabella in Azure.

        public sealed partial class MainPage : PhoneApplicationPage
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
                items.Add(todoItem);
            }
            private async void RefreshTodoItems()
            {
                items = await todoTable 
                    .ToCollectionAsync(); 
                ListItems.ItemsSource = items;
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
                var todoItem = new TodoItem { Text = InputText.Text };
                InsertTodoItem(todoItem);
            }
            private void CheckBoxComplete_Checked(object sender, RoutedEventArgs e)
            {
                CheckBox cb = (CheckBox)sender;
                TodoItem item = cb.DataContext as TodoItem;
                item.Complete = (bool)cb.IsChecked;
                UpdateCheckedTodoItem(item);
            }
            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
                RefreshTodoItems();
            }
        }

## <a name="test-locally-hosted"></a>Test dell'app per Windows Phone con il servizio ospitato in locale

</h2>
In questa sezione si utilizzerà Visual Studio per testare l'app e il servizio mobile in locale nella workstation di sviluppo. Per testare il servizio mobile ospitato in locale in IIS Express da un dispositivo Windows Phone o da uno degli emulatori di Windows Phone, è necessario configurare IIS Express e la workstation in modo da consentire le connessioni all'indirizzo e alla porta IP della workstation. I dispositivi e gli emulatori di Windows Phone si connettono come client di rete non locale.

#### Configurare IIS Express per consentire le connessioni remote

[WACOM.INCLUDE [mobile-services-how-to-configure-iis-express](../includes/mobile-services-how-to-configure-iis-express.md)]

#### Test dell'app nel servizio mobile in IIS Express

1.  In Visual Studio aprire il file App.xaml.cs e impostare come commento la definizione `MobileService` incollata poco prima nel file. Aggiungere una nuova definizione in modo che la connessioni risulti basata sull'indirizzo e sulla porta IP configurati nella workstation. Salvare il file. Il codice dovrebbe essere simile a quello riportato di seguito.

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "http://192.168.111.11:54321");

        //public static MobileServiceClient MobileService = new MobileServiceClient(
        //    "https://todolist.azure-mobile.net/",
        //    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
        //);        

2.  In Visual Studio premere F7 o scegliere **Compila soluzione** dal menu Compila per compilare sia l'app per Windows Phone che il servizio mobile. Verificare che entrambi i progetti vengano compilati senza errori nella finestra di output di Visual Studio

    ![][17]

3.  In Visual Studio premere F5 o scegliere **Avvia debug** dal menu Debug per eseguire l'app e ospitare il servizio localmente in IIS Express.

    > [WACOM.NOTE] Verificare di aver eseguito Visual Studio con l'opzione **Esegui come amministratore**. In caso contrario, IIS Express potrebbe non caricare le modifiche apportate a applicationhost.config.

    ![][18]

4.  Immettere il testo di un nuovo oggetto todoitem. Fare quindi clic su **Salva**. Un nuovo elemento TodoItem verrà inserito nel database creato dal servizio mobile ospitato localmente in IIS Express. Fare clic sulla casella di controllo relativa a uno degli elementi per contrassegnarlo come completato.

    ![][19]

5.  In Visual Studio interrompere il debug dell'app. È possibile visualizzare le modifiche nel database creato per il servizio back-end aprendo Esplora server ed espandendo Connessioni dati. Fare clic con il pulsante destro del mouse sulla tabella TodoItems in **MS\_TableConnectionString** e scegliere **Mostra dati tabella**

    ![][20]

6.  Dopo aver completato il test con il servizio mobile ospitato localmente, eliminare la regola di Windows Firewall creata per aprire la porta sulla workstation.

## <a name="publish-mobile-service"></a>Pubblicazione del servizio mobile in Azure

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## <a name="test-azure-hosted"></a>Test del servizio mobile pubblicato in Azure

1.  In Visual Studio aprire il file App.xaml.cs. Impostare come commento il codice per la creazione dell'oggetto `MobileServiceClient` che esegue la connessione al servizio mobile ospitato localmente. Rimuovere i simboli di commento dal codice per la creazione dell'oggetto `MobileServiceClient` che esegue la connessione al servizio in Azure. Salvare le modifiche al file.

        sealed partial class App : Application
        {
            //public static MobileServiceClient MobileService = new MobileServiceClient(
            //          "http://192.168.111.11:54321");

            // Use this constructor instead after publishing to the cloud
            public static MobileServiceClient MobileService = new MobileServiceClient(
                 "https://todolist.azure-mobile.net/",
                 "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
            );        
            ....

2.  In Visual Studio premere F5 o scegliere **Avvia debug** dal menu Debug. In questo modo, prima dell'esecuzione, l'app verrà ricompilata con la modifica precedente, per effettuare la connessione al servizio mobile ospitato in remoto in Azure.

    ![][18]

3.  Immettere nuovi elementi todoitem e fare clic su **Salva** per ognuno di essi. Fare clic sulla casella di controllo per completare alcuni dei nuovi elementi. Ogni nuovo elemento TodoItem verrà archiviato e aggiornato nel database SQL precedentemente configurato per il servizio mobile nel portale di gestione di Azure.

    ![][21]

    È possibile riavviare l'app per verificare che le modifiche siano state salvate in modo permanente nel database in Azure. È inoltre possibile esaminare il database utilizzando il portale di gestione di Azure oppure il comando Esplora oggetti di SQL Server in Visual Studio. I due passaggi successivi illustrano come utilizzare il portale di gestione di Azure per visualizzare le modifiche nel database.

4.  Nel portale di gestione di Azure fare clic su Manage per il database associato al servizio mobile.

    ![][22]

5.  Nel portale di gestione eseguire una query per visualizzare le modifiche apportate dall'app. La query da utilizzare sarà simile alla seguente, ma anziché `todolist` conterrà il nome del database.

        SELECT * FROM [todolist].[todoitems]

    ![][23]

L'esercitazione **Introduzione ai dati** è terminata.

## <a name="next-steps"> </a>Passaggi successivi

In questa esercitazione vengono illustrate le nozioni di base per consentire a un'app per Windows Phone 8 di utilizzare dati in Servizi mobili compilati mediante il runtime .Net. In seguito, è consigliabile eseguire una delle esercitazioni seguenti, basate sull'app GetStartedWithData creata in questa esercitazione:

-   [Utilizzo di script per la convalida e la modifica di dati][Utilizzo di script per la convalida e la modifica di dati]
    Ulteriori informazioni sull'utilizzo di script del server in Servizi mobili per convalidare e modificare i dati inviati dall'app.

-   [Utilizzo del paging per ridefinire le query][Utilizzo del paging per ridefinire le query]
    Ulteriori informazioni su come utilizzare il paging nelle query per controllare la quantità di dati gestiti in un'unica richiesta.

Una volta completata la serie relativa ai dati, provare a eseguire una delle esercitazioni seguenti:

-   [Introduzione all'autenticazione][Introduzione all'autenticazione]
    Informazioni sull'autenticazione degli utenti dell'app.

<!-- * [Get started with push notifications]    <br/>Learn how to send a very basic push notification to your app. -->

-   [Riferimento per i concetti e le procedure di Servizi mobili con .NET][Riferimento per i concetti e le procedure di Servizi mobili con .NET]
    Ulteriori informazioni su come utilizzare Servizi mobili con .NET.



  [versione per back-end JavaScript]: /it-it/develop/mobile/tutorials/get-started-with-data-wp8
  [Download del progetto dell'app per Windows Phone 8]: #download-app
  [Creazione di un nuovo servizio mobile]: #create-service
  [Download del servizio mobile in locale]: #download-the-service-locally
  [Aggiornamento dell'app per Windows Phone per l'uso del servizio mobile]: #update-app
  [Test dell'app per Windows Phone con il servizio ospitato in locale]: #test-locally-hosted
  [Pubblicazione del servizio mobile in Azure]: #publish-mobile-service
  [Test dell'app per Windows Phone con il servizio ospitato in Azure]: #test-azure-hosted
  [Windows Phone 8 SDK]: http://go.microsoft.com/fwlink/p/?linkid=268374
  [versione di valutazione gratuita di Azure]: http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=AE564AB28&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fit-it%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started-data%2F
  [app GetStartedWithMobileServices]: http://go.microsoft.com/fwlink/p/?linkid=271146
  [0]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/mobile-data-sample-download-wp8-vs12.png
  [versione dell'app di Windows Phone Store]: http://go.microsoft.com/fwlink/p/?LinkId=397372
  [1]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-deployment-target.png
  [2]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/app-view.png
  [portale di gestione di Azure]: https://manage.windowsazure.com/
  [3]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/mobile-service-overview-page.png
  [4]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/download-service-project.png
  [5]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/download-publishing-profile.png
  [6]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/copy-service-and-packages-folder.png
  [7]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/add-service-project-to-solution.png
  [8]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/add-existing-project-dialog.png
  [9]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-build-service-project.png
  [10]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-start-debug-service-project.png
  [11]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/service-welcome-page.png
  [12]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/iis-express-tray.png
  [13]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-manage-nuget-packages.png
  [14]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/manage-nuget-packages.png
  [15]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/copy-mobileserviceclient-snippet.png
  [16]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-pasted-mobileserviceclient.png
  [17]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-build-solution.png
  [18]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-run-solution.png
  [19]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/local-item-checked.png
  [20]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-show-local-table-data.png
  [21]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/azure-items.png
  [22]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/manage-sql-azure-database.png
  [23]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/sql-azure-query.png
  [Utilizzo di script per la convalida e la modifica di dati]: /it-it/develop/mobile/tutorials/validate-modify-and-augment-data-wp8
  [Utilizzo del paging per ridefinire le query]: /it-it/develop/mobile/tutorials/add-paging-to-data-wp8
  [Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/
  [Riferimento per i concetti e le procedure di Servizi mobili con .NET]: /it-it/develop/mobile/how-to-guides/work-with-net-client-library
