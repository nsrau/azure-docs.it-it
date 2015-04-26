<properties 
	pageTitle="Uso di dati offline in Servizi mobili (Windows Phone) | Mobile Dev Center" 
	description="Informazioni su come usare Servizi mobili di Azure per sincronizzare i dati offline nell'applicazione per Windows Phone" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="12/10/2014" 
	ms.author="wesmc"/>

# Uso della sincronizzazione dei dati offline in Servizi mobili

[AZURE.INCLUDE [mobile-services-selector-offline](../includes/mobile-services-selector-offline.md)]


Questo argomento descrive come usare le funzionalità offline di Servizi mobili di Azure. Servizi mobili di Azure consente di interagire con un database locale quando si usa un servizio mobile in modalità offline. Le funzionalità offline consentono di sincronizzare le modifiche locali con il servizio mobile usato quando si torna online. 

In questa esercitazione verrà aggiornata l'app creata nell'esercitazione [Introduzione ai dati] per supportare le funzionalità offline di Servizi mobili di Azure. Quindi, verranno aggiunti dati in uno scenario offline, verrà effettuata la sincronizzazione degli elementi con il database online, quindi si effettuerà l'accesso al portale di gestione di Azure per visualizzare le modifiche apportate durate l'esecuzione dell'app.


>[AZURE.NOTE] In questa esercitazione viene descritto come usare Servizi mobili di Azure per archiviare e recuperare i dati da un'app per Windows Phone. Se si tratta della prima esperienza con Servizi mobili, è consigliabile iniziare dalle esercitazioni [Introduzione a Servizi mobili] e [Introduzione ai dati]. 

Questa esercitazione spiega come eseguire le operazioni di base seguenti:

1. [Aggiornare l'app per supportare le funzionalità offline]
2. [Testare l'app in uno scenario offline] 
3. [Aggiornare l'app per la riconnessione al servizio mobile]
4. [Testare l'app connessa al servizio mobile]

Per completare questa esercitazione, è necessario soddisfare i seguenti requisiti:

* Visual Studio 2012
* [Windows Phone 8 SDK]
* Completamento dell'esercitazione [Introduzione ai dati].
* [Azure Mobile Services SDK versione 1.3.0 (o successiva)][Pacchetto NuGet Mobile Services SDK]
* [Azure Mobile Services SQLite Store versione 1.0.0 (o successiva)][Pacchetto NuGet SQLite store]
* [SQLite per Windows Phone 8]

>[AZURE.NOTE] Per completare l'esercitazione, è necessario un account Azure. Se non si ha un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">versione di valutazione gratuita di Azure</a>. 

## <a name="enable-offline-app"></a>Aggiornare l'app per supportare le funzionalità offline

Servizi mobili di Azure consente di interagire con un database locale quando si usa un servizio mobile in modalità offline. Per usare queste funzionalità nell'app, inizializzare `MobileServiceClient.SyncContext` in un archivio locale. Quindi, fare riferimento alla tabella tramite l'interfaccia di `IMobileServiceSyncTable`.

In questa sezione viene usato SQLite come archivio locale per le funzionalità offline.

>[AZURE.NOTE] È possibile ignorare questa sezione e limitarsi a scaricare una versione del progetto introduttivo che dispone già di supporto offline.  Per scaricare un progetto con il supporto offline abilitato, vedere l'[esempio introduttivo offline per Windows Phone].


1. Installare SQLite per i progetti Windows Phone 8. È possibile installarlo direttamente dal collegamento [SQLite for Windows Phone 8].

    >[AZURE.NOTE] Quando si fa clic sul collegamento di installazione di SQLite in Internet Explorer, è possibile che venga chiesto di scaricare il file con estensione vsix come zip. Salvare il file in un percorso del disco rigido usando l'estensione vsix anziché zip. Fare quindi doppio clic sul file con estensione vsix in Esplora risorse per eseguire l'installazione.

2. In Visual Studio aprire il progetto completato nell'esercitazione [Introduzione a Servizi mobili] o [Introduzione ai dati]. In Esplora soluzioni fare clic con il pulsante destro del mouse su **Riferimenti** sotto il progetto e aggiungere un riferimento a **SQLite for Windows Phone** in **Windows Phone**>**Estensioni**. 

    ![][1]

3. Per l'esecuzione del runtime SQLite è necessario che sia impostata l'architettura di processore **x86**, **x64** o **ARM** per il progetto creato. **Qualsiasi CPU** non è supportata. Modificare l'architettura del processore in una delle impostazioni supportate da testare.

    ![][11]

4. In Esplora soluzioni di Visual Studio fare clic con il pulsante destro del mouse sul progetto di app e scegliere **Gestisci pacchetti NuGet** per eseguire Gestione pacchetti NuGet. Cercare **SQLiteStore** per installare il pacchetto **WindowsAzure.MobileServices.SQLiteStore**.

    ![][2]

5. In Esplora soluzioni di Visual Studio aprire il file MainPage.xaml.cs. Aggiungere le istruzioni using seguenti all'inizio del file:

        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Newtonsoft.Json.Linq;

6. In Mainpage.xaml.cs sostituire la dichiarazione di `todoTable` con una dichiarazione di tipo `IMobileServicesSyncTable` inizializzata mediante la chiamata di `MobileServicesClient.GetSyncTable()`.

        //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
        private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>();

7. In MainPage.xaml.cs aggiornare la classe `TodoItem` in modo che includa la proprietà di sistema **Version** come segue.

        public class TodoItem
        {
          public string Id { get; set; }
          [JsonProperty(PropertyName = "text")]
          public string Text { get; set; }
          [JsonProperty(PropertyName = "complete")]
          public bool Complete { get; set; }
          [Version]
          public string Version { get; set; }
        }


8. In MainPage.xaml.cs aggiornare il gestore eventi `OnNavigatedTo` in modo che sia un metodo `async` e inizializzi il contesto di sincronizzazione del client con un archivio SQLite. L'archivio SQLite viene creato con una tabella che corrisponde allo schema della tabella di Servizi mobili, ma deve contenere la proprietà di sistema **Version** aggiunta nel passaggio precedente.

        protected async override void OnNavigatedTo(NavigationEventArgs e)
        {
            if (!App.MobileService.SyncContext.IsInitialized)
            {
                var store = new MobileServiceSQLiteStore("localsync12.db");
                store.DefineTable<TodoItem>();
                await App.MobileService.SyncContext.InitializeAsync(store, new MobileServiceSyncHandler());
            }
            RefreshTodoItems();
        }

9. In Esplora soluzioni di Visual Studio aprire il file MainPage.xaml. Trovare la definizione del pulsante per il pulsante **Aggiorna**. Sostituirla con la definizione di pannello Stack seguente. 

    Questo codice aggiunge due controlli di pulsante con i gestori eventi clic per le operazioni **Push** e **Pull**. I pulsanti si trovano sono allineati in orizzontale al pulsante di aggiornamento. Salvare il file.

        <StackPanel  Orientation="Horizontal" Grid.Row="3" Grid.ColumnSpan="2" HorizontalAlignment="Center">
          <Button Name="ButtonRefresh" Click="ButtonRefresh_Click" Width="160">Refresh</Button>
          <Button Name="ButtonPush" Click="ButtonPush_Click" Width="160">Push</Button>
          <Button Name="ButtonPull" Click="ButtonPull_Click" Width="160">Pull</Button>
        </StackPanel>

    Modificare il testo dei blocchi di testo in base alla schermata seguente.

    ![][12]
        


10. In MainPage.xaml.cs aggiungere i gestori eventi clic per i pulsanti **Push** and **Pull**, quindi salvare il file.

        private async void ButtonPull_Click(object sender, RoutedEventArgs e)
        {
            Exception pullException = null;
            try
            {
                await todoTable.PullAsync("todoItems", todoTable.CreateQuery()); // first param is query ID, used for incremental sync
                RefreshTodoItems();
            }
            catch (Exception ex)
            {
                pullException = ex;
            }
            if (pullException != null) {
                MessageBox.Show("Pull failed: " + pullException.Message +
                  "\n\nIf you are in an offline scenario, " + 
                  "try your Pull again when connected with your Mobile Serice.");
            }
        }
        private async void ButtonPush_Click(object sender, RoutedEventArgs e)
        {
            string errorString = null;
            try
            {
                await App.MobileService.SyncContext.PushAsync();
                RefreshTodoItems();
            }
            catch (MobileServicePushFailedException ex)
            {
                errorString = "Push failed because of sync errors: " + 
                  ex.PushResult.Errors.Count() + ", message: " + ex.Message;
            }
            catch (Exception ex)
            {
                errorString = "Push failed: " + ex.Message;
            }
            if (errorString != null) {
                MessageBox.Show(errorString + 
                  "\n\nIf you are in an offline scenario, " + 
                  "try your Push again when connected with your Mobile Serice.");
            }
        }

11. Non eseguire ancora l'app. Premere **F7** per ricompilare il progetto. Verificare che non si siano verificati errori di compilazione.

## <a name="test-offline-app"></a>Testare l'app in uno scenario offline

In questa sezione verrà interrotta la connessione dell'app al servizio mobile per simulare uno scenario offline. Quindi, verranno aggiunti alcuni elementi di dati che saranno conservati nell'archivio locale.

Si noti che in questa sezione l'app non deve essere connessa ad alcun servizio mobile. Pertanto, se vengono testati, i pulsanti **Push** e **Pull** genereranno eccezioni. Nella sezione successiva si connetterà di nuovo questa app client al servizio mobile per testare le operazioni **push** e **pull** allo scopo di sincronizzare l'archivio con il database del servizio mobile.


1. In Esplora soluzioni di Visual Studio aprire il file App.xaml.cs. Impostare un indirizzo non valido per l'inizializzazione di **MobileServiceClient** sostituendo "**azure-mobile.net**" con "**azure-mobile.xxx**" per l'URL. Salvare il file.

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.xxx/",
            "AppKey"
        );

2. In Visual Studio premere **F5** per compilare ed eseguire l'app. Immettere un nuovo elemento Todo e fare clic su **Salva**. I nuovi elementi todo esistono solo nell'archivio locale fino a quando è possibile effettuarne il push al servizio mobile. L'app client si comporta come se fosse connessa al servizio mobile, supportando tutte le operazioni CRUD (creazione, lettura, aggiornamento ed eliminazione).

    ![][4]

3. Chiudere l'app e riavviarla per verificare che i nuovi elementi creati siano salvati in modo permanente nell'archivio locale.

## <a name="update-online-app"></a>Aggiornare l'app per la riconnessione al servizio mobile

In questa sezione verrà effettuata la riconnessione dell'app al servizio mobile. Viene simulato il passaggio dell'app dallo stato offline allo stato online con il servizio mobile.


1. In Esplora soluzioni di Visual Studio aprire il file App.xaml.cs. Ripristinare l'indirizzo corretto per l'inizializzazione di **MobileServiceClient** sostituendo "**azure-mobile.xxx**" con "**azure-mobile.net**" per l'URL. Salvare il file.

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.net/",
            "Your AppKey"
        );


## <a name="test-online-app"></a>Testare l'app connessa al servizio mobile


In questa sezione verrà effettuato il test delle operazioni push e pull per sincronizzare l'archivio locale con il database del servizio mobile.

1. In Visual Studio premere **F5** per ricompilare ed eseguire l'app. Si noti che i dati appaiono uguali a quelli dello scenario offline nonostante l'app sia ora connessa al servizio mobile. Questo avviene perché questa app funziona sempre con la `IMobileServiceSyncTable` che fa riferimento all'archivio locale.

    ![][4]

2.  Accedere al portale di gestione di Microsoft Azure ed esaminare il database relativo al servizio mobile. Se il servizio usa il back-end JavaScript per i servizi mobili, è possibile visualizzare i dati dalla scheda **Dati** del servizio mobile. 

    Se si usa il back-end .NET per il servizio mobile, in Visual Studio passare a **Esplora server** -> **Azure** -> **Database SQL**. Fare clic con il pulsante destro del mouse sul database e scegliere **Apri in Esplora oggetti di SQL Server**.

    Si noti che i dati non sono stati sincronizzati tra il database e l'archivio locale.

    ![][6]

3. Nell'app scegliere il pulsante **Push**. L'app effettuerà la chiamata a `MobileServiceClient.SyncContext.PushAsync` e quindi a `RefreshTodoItems` in modo da effettuare l'aggiornamento con gli elementi dell'archivio locale. Con questa operazione push il database del servizio mobile riceve i dati dall'archivio. Tuttavia, l'archivio locale non riceve gli elementi dal database del servizio mobile.

    Un'operazione push viene eseguita da `MobileServiceClient.SyncContext` invece che da `IMobileServicesSyncTable` ed esegue il push delle modifiche a tutte le tabelle associate a tale contesto di sincronizzazione. Questa operazione serve per gli scenari in cui sono presenti relazioni tra tabelle.

    ![][7]

4. Nell'app alcuni nuovi elementi sono inviati all'archivio locale.

    ![][8]

5. Questa volta premere il pulsante **Pull** nell'app. L'app chiama solo `IMobileServiceSyncTable.PullAsync()` e `RefreshTodoItems`.  Si noti che tutti i dati provenienti dal database del servizio mobile sono stati estratti nell'archivio locale e vengono mostrati nell'app. Tuttavia, si noti anche che tutti i dati dell'archivio locale sono stati comunque sottoposti a push nel database del servizio mobile. Questo avviene perché **un'operazione pull effettua sempre un'operazione push all'inizio**
 
    In questo esempio vengono recuperati tutti i record presenti nella tabella `todoTable` remota, ma è anche possibile filtrare i record passando una query. Il primo parametro di `PullAsync` è un ID di query usato per la sincronizzazione incrementale, che usa il timestamp `UpdatedAt` per ottenere i soli record modificati dopo l'ultima sincronizzazione. L'ID di query deve essere una stringa descrittiva univoca per ogni query logica presente nell'app. Per rifiutare esplicitamente la sincronizzazione incrementale, passare `null` come ID di query. In ogni operazione pull verranno recuperati tutti i record e questo potrebbe creare inefficienze.

    >[AZURE.NOTE] Per supportare la sincronizzazione dei record eliminati con la sincronizzazione dei dati offline, è consigliabile abilitare l'[eliminazione temporanea]. In caso contrario, è necessario chiamare `IMobileServiceSyncTable.PurgeAsync()` per ripulire l'archivio locale.

 
    ![][9]

    ![][10] 
  

##Riepilogo

##Riepilogo

[AZURE.INCLUDE [mobile-services-offline-summary-csharp](../includes/mobile-services-offline-summary-csharp.md)]

## Passaggi successivi

* [Gestione dei conflitti con il supporto offline per Servizi mobili]

* [Uso dell'eliminazione temporanea in Servizi mobili][Eliminazione temporanea]

<!-- Anchors. -->
[Aggiornare l'app per supportare le funzionalità offline]: #enable-offline-app
[Testare l'app in uno scenario offline]: #test-offline-app
[Aggiornare l'app per la riconnessione al servizio mobile]: #update-online-app
[Testare l'app connessa al servizio mobile]: #test-online-app
[Passaggi successivi]:#next-steps

<!-- Images -->
[0]: ./media/mobile-services-windows-phone-get-started-data-vs2013/mobile-todoitem-data-browse.png
[1]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-services-add-reference-sqlite-dialog.png
[2]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-services-sqlitestore-nuget.png
[3]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-services-sqlitepcl-nuget.png
[4]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-services-offline-app-run1.png
[5]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-services-online-app-run1.png
[6]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-data-browse.png
[7]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-data-browse2.png
[8]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-services-online-app-run2.png
[9]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-services-online-app-run3.png
[10]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-data-browse3.png
[11]: ./media/mobile-services-windows-phone-get-started-offline-data/vs-select-processor-architecture.png
[12]: ./media/mobile-services-windows-phone-get-started-offline-data/ui-screenshot.png

<!-- URLs. -->
[Gestione dei conflitti con il supporto offline per Servizi mobili]: /it-it/documentation/articles/mobile-services-windows-phone-handling-conflicts-offline-data/ 
[Esempio introduttivo offline per Windows Phone]: http://go.microsoft.com/fwlink/?LinkId=397952
[Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-windows-phone-get-started/
[Introduzione ai dati]: /it-it/documentation/articles/mobile-services-windows-phone-get-started-data/
[SQLite per Windows Phone 8]: http://go.microsoft.com/fwlink/?LinkId=397953
[Windows Phone 8 SDK]: http://go.microsoft.com/fwlink/p/?linkid=268374
[Eliminazione temporanea]: /it-it/documentation/articles/mobile-services-using-soft-delete/

[Pacchetto NuGet Mobile Services SDK]: http://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.0
[Pacchetto NuGet SQLite Store]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.0



<!--HONumber=42-->
