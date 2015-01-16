<properties urlDisplayName="Using Offline Data" pageTitle="Uso di dati offline in Servizi mobili (Windows Store) | Mobile Developer Center" metaKeywords="" description="Informazioni su come usare Servizi mobili di Azure per memorizzare nella cache e sincronizzare i dati offline nell'applicazione per Windows Store" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Using offline data sync in Mobile Services" authors="wesmc" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/25/2014" ms.author="wesmc" />

# Uso della sincronizzazione dei dati offline in Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-offline](../includes/mobile-services-selector-offline.md)]


<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Questa esercitazione illustra come aggiungere un supporto offline a un'app di Windows Universal Store mediante Servizi mobili di Azure. Il supporto offline consentirà l'interazione con un database locale quando l'app è in uno scenario offline. Una volta che l'app è online con il database back-end, si sincronizzano le modifiche locali con le funzionalità offline. 
</p>
<p>Se si preferisce guardare un video, nel clip a destra vengono eseguiti gli stessi passaggi dell'esercitazione.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="label">video di esercitazione</a> <a style="background-image: url('http://video.ch9.ms/ch9/ea1c/ffed2371-4db1-4a8e-8869-80013859ea1c/BuildOfflineAppsAzureMobileServices_220.jpg') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Riproduci video</span></a> <span class="time">14:36</span></div>
</div>


In questa esercitazione verrà aggiornato il progetto di app universale creato nell'esercitazione [Introduzione a Servizi mobili] per supportare le funzionalità offline di Servizi mobili di Azure. Quindi, verranno aggiunti dati in uno scenario offline, verrà effettuata la sincronizzazione degli elementi con il database online, quindi si effettuerà l'accesso al portale di gestione di Azure per visualizzare le modifiche apportate durate l'esecuzione dell'app.


>[WACOM.NOTE] In questa esercitazione viene descritto come è possibile usare Servizi mobili di Azure per archiviare e recuperare i dati da un'app di Windows Store. Se si tratta della prima esperienza con Servizi mobili, è consigliabile iniziare dall'esercitazione [Introduzione a Servizi mobili].
>
>Per completare l'esercitazione, è necessario un account Azure. Se non si ha un account, è possibile iscriversi per accedere a una versione di valutazione di Azure e ottenere un massimo di 10 servizi mobili gratuiti che potranno essere usati anche dopo il termine del periodo di valutazione. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">versione di valutazione gratuita di Azure</a>. 
>
>È ancora disponibile la precedente [esercitazione con Windows Phone 8 per Visual Studio 2012].


In questa esercitazione vengono descritte le operazioni di base seguenti:

1. [Aggiornare l'app per supportare le funzionalità offline]
2. [Testare l'app in uno scenario offline] 
3. [Aggiornare l'app per la riconnessione al servizio mobile]
4. [Testare l'app connessa al servizio mobile]

Per completare questa esercitazione, è necessario disporre di:

* Visual Studio 2013 in esecuzione su Windows 8.1.
* Completamento dell'esercitazione [Introduzione a Servizi mobili].
* [Azure Mobile Services SDK versione 1.3.0-beta2 (o successiva)][Mobile Services SDK Nuget]
* [Azure Mobile Services SQLite Store versione 1.0.0-beta2 (o successiva))][SQLite store nuget]
* SQLite for Windows 8.1

>[AZURE.NOTE] Per completare l'esercitazione, è necessario un account Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">versione di valutazione gratuita di Azure</a>. 

## <a name="enable-offline-app"></a>Aggiornare l'app per supportare le funzionalità offline

Servizi mobili di Azure consente di interagire con un database locale quando si usa un servizio mobile in modalità offline. Per usare queste funzionalità nell'app, inizializzare `MobileServiceClient.SyncContext` in un archivio locale. Quindi, fare riferimento alla tabella tramite l'interfaccia di `IMobileServiceSyncTable`. In questa esercitazione verrà usato SQLite per l'archivio locale.

>[AZURE.NOTE] È possibile saltare questa sezione e scaricare direttamente il progetto di esempio già con il supporto offline dall'archivio di esempi Github per Servizi mobili. Il progetto con il supporto offline abilitato si trova nell'[esempio offline TodoList].


1. Installare SQLite Runtime per Windows 8.1 e Windows Phone 8.1. 

    * **Windows 8.1 Runtime:** installare SQLite Runtime per Windows 8.1 da [SQLite per Windows 8.1].
    * **Windows Phone 8.1:** installare SQLite Runtime per Windows Phone 8.1 da [SQLite per Windows Phone 8.1].

    >[AZURE.NOTE] Se si usa Internet Explorer, facendo clic sul collegamento di installazione di SQLite potrebbe essere visualizzato un messaggio in cui viene chiesto di scaricare vsix come file zip. Salvare il file in un percorso del disco rigido usando l'estensione .vsix invece di .zip. Quindi fare doppio clic sul file vsix in Esplora risorse per eseguire l'installazione.

2. In Visual Studio aprire il progetto completato nell'esercitazione [Introduzione a Servizi mobili] In Esplora soluzioni fare clic con il pulsante destro del mouse su **Riferimenti** per i progetti delle piattaforme Windows 8.1 Runtime e Windows Phone 8.1 e aggiungere un riferimento a SQLite, che si trova nella sezione **Estensioni**. 

    ![][1]
    </br>**Windows 8.1 Runtime**

    ![][11]
    </br>**Windows Phone 8.1**

3. SQLite Runtime richiede la modifica dell'architettura del processore per il progetto creato in **x86**, **x64** o **ARM**. **Qualsiasi CPU**  non è supportata. In Esplora soluzioni per Visual Studio fare clic sulla soluzione in alto, quindi nella casella di riepilogo relativa all'architettura del processore selezionare una delle impostazioni supportate da testare.

    ![][13]

4. Installare il pacchetto NuGet  **WindowsAzure.MobileServices.SQLiteStore** per i progetti Windows 8.1 Runtime e Windows Phone 8.1.

    * **Windows 8.1:** in Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto Windows 8.1 e scegliere **Gestisci pacchetti NuGet** per eseguire Gestione pacchetti NuGet. Cercare **SQLiteStore** per installare il pacchetto WindowsAzure.MobileServices.SQLiteStore.
    * **Windows Phone 8.1:** fare clic con il pulsante destro del mouse sul progetto Windows Phone 8.1 e scegliere **Gestisci pacchetti NuGet** per eseguire Gestione pacchetti NuGet. Cercare **SQLiteStore** per installare il pacchetto WindowsAzure.MobileServices.SQLiteStore.     

    >[WACOM.NOTE] Se l'installazione crea un riferimento a una versione precedente di SQLite, è sufficiente eliminare tale riferimento duplicato. 

    ![][2]

5. In Esplora soluzioni di Visual Studio, nel progetto condiviso, aprire il file MainPage.cs. Aggiungere le istruzioni using seguenti all'inizio del file.

        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;
        using Microsoft.WindowsAzure.MobileServices.Sync;

6. Nel file condiviso Mainpage.cs sostituire la dichiarazione di `todoTable` con una dichiarazione di tipo `IMobileServicesSyncTable` inizializzata mediante la chiamata di `MobileServicesClient.GetSyncTable()`.

        //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
        private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>();


7. Nel file condiviso MainPage.cs aggiornare il gestore di eventi `OnNavigatedTo` in modo che inizializzi il contesto di sincronizzazione del client con un archivio SQLite. L'archivio SQLite viene creato con una tabella che corrisponde allo schema della tabella di Servizi mobili e deve includere la proprietà di sistema **Version** che si aggiungerà nel passaggio successivo.

        protected async override void OnNavigatedTo(NavigationEventArgs e)
        {
            if (!App.MobileService.SyncContext.IsInitialized)
            {
                var store = new MobileServiceSQLiteStore("localsync.db");
                store.DefineTable<TodoItem>();
                await App.MobileService.SyncContext.InitializeAsync(store);
            }
            await RefreshTodoItems();
        }


8. In Esplora soluzioni di Visual Studio, nel progetto condiviso espandere **DataModel** e aprire TodoItem.cs. Aggiungere un'istruzione `using` per lo spazio dei nomi MobileServices. È necessaria per risolvere l'attributo version per la proprietà di sistema version.

        using Microsoft.WindowsAzure.MobileServices;

      Aggiornare quindi la classe `TodoItem` in modo che includa la proprietà di sistema **Version** come segue. Lo schema della tabella deve includere la proprietà di sistema **Version** come mostrato qui per supportare le funzionalità offline.

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




9. Aggiornare quindi l'interfaccia utente per i progetti Windows 8.1 e Windows Phone 8.1 per poter includere pulsanti che supporteranno le operazioni di sincronizzazione offline tra il database offline locale e il database di Servizi mobili in Azure. 

    * **Windows 8.1:** in Esplora soluzioni di Visual Studio, nel progetto Windows 8.1, aprire MainPage.xaml. Trovare il pulsante **ButtonRefresh**. Sostituire tale elemento pulsante con il seguente pannello Stack di pulsanti. 

            <StackPanel Orientation="Horizontal">
                <Button Margin="72,0,0,0" Height="44" Name="ButtonRefresh" Click="ButtonRefresh_Click">
                	Refresh
                </Button>
                <Button Margin="10,0,0,0" Name="ButtonPull" Click="ButtonPull_Click">
                    <StackPanel Orientation="Horizontal">
                        <TextBlock Margin="5">Pull</TextBlock>
                        <SymbolIcon Symbol="Sync"/>
                    </StackPanel>
                </Button>
                <Button Margin="10,0,0,0" Name="ButtonPush" Click="ButtonPush_Click">
                    <StackPanel Orientation="Horizontal">
                        <TextBlock Margin="5">Push</TextBlock>
                        <SymbolIcon Symbol="Upload"/>
                    </StackPanel>
                </Button>
            </StackPanel>

    * **Windows Phone 8.1:** in Esplora soluzioni di Visual Studio, nel progetto Windows Phone 8.1, aprire MainPage.xaml. Trovare il pulsante **ButtonRefresh**. Sostituire tale elemento pulsante con il seguente pannello Stack di pulsanti. 

            <StackPanel Orientation="Horizontal" Grid.Row="3" Grid.ColumnSpan="2">
                <Button Name="ButtonRefresh" Click="ButtonRefresh_Click" Margin="10,0,0,0">
                    Refresh
                </Button>
                <Button Name="ButtonPull" Click="ButtonPull_Click" Margin="10,0,0,0">
                    <StackPanel Orientation="Horizontal">
                        <TextBlock Margin="5">Pull</TextBlock>
                        <SymbolIcon Symbol="Sync"/>
                    </StackPanel>
                </Button>
                <Button Name="ButtonPush" Click="ButtonPush_Click" Margin="10,0,0,0">
                    <StackPanel Orientation="Horizontal">
                        <TextBlock Margin="5">Push</TextBlock>
                        <SymbolIcon Symbol="Upload"/>
                    </StackPanel>
                </Button>
        </StackPanel>
        


10. Nel file MainPage.cs condiviso aggiungere i gestori di eventi click per i pulsanti **Push** e **Pull**. Salvare il file. 
        
    Tenere presente che `MobileServicePushFailedException` può verificarsi sia per un'operazione push che pull. Può verificarsi per pull perché l'operazione pull esegue internamente un push per assicurarsi che tutte le tabelle con qualsiasi relazione siano sincronizzate. L'esercitazione successiva, [Gestione dei conflitti con il supporto offline per Servizi mobili], mostra come gestire queste eccezioni relative alla sincronizzazione.

    Per supportare la sincronizzazione dei record eliminati con la sincronizzazione dei dati offline, è consigliabile abilitare l'[eliminazione temporanea](/it-it/documentation/articles/mobile-services-using-soft-delete/); in caso contrario, è necessario rimuovere manualmente i record nell'archivio locale o chiamare `IMobileServiceSyncTable::PurgeAsync()` per ripulire l'archivio locale.

    In questo esempio viene passata una query alla chiamata al metodo `PullAsync` per supportare la sincronizzazione incrementale. Ciò è utile nei casi in cui non si vuole effettivamente eseguire il pull dell'intera tabella durante la sincronizzazione. In questo scenario non sono importanti le modifiche al testo degli elementi todoitem. Il solo obiettivo è il pull degli elementi completati per spuntarli nell'app todolist.

        private async void ButtonPull_Click(object sender, Windows.UI.Xaml.RoutedEventArgs e)
        {
            string errorString = null;

            // Prevent extra clicking while Pull is in progress
            // and visibly show the action is in progress. 
            ButtonPull.Focus(FocusState.Programmatic);
            ButtonPull.IsEnabled = false;

            try
            {
                // In this example scenario we are demonstrating incremental sync. We only want to 
                // sync the uncomplete todoitems. If another client updated the text or any other 
                // field after completing the item, it doesn't concern us. So we pass a query to the 
                // PullAsync() method to pull uncomplete todoitems instead of the entire table.
                await todoTable.PullAsync(todoTable.Where(todoItem => todoItem.Complete == false));

                await RefreshTodoItems();
            }
            catch (MobileServicePushFailedException ex)
            {
                errorString = "Internal Push operation during pull request failed because of sync errors: " +
                  ex.PushResult.Errors.Count + " errors, message: " + ex.Message;
            }
            catch (Exception ex)
            {
                errorString = "Pull failed: " + ex.Message +
                  "\n\nIf you are still in an offline scenario, " +
                  "you can try your Pull again when connected with your Mobile Serice.";
            }

            if (errorString != null)
            {
                MessageDialog d = new MessageDialog(errorString);
                await d.ShowAsync();
            }

            ButtonPull.IsEnabled = true; 
        }


        private async void ButtonPush_Click(object sender, Windows.UI.Xaml.RoutedEventArgs e)
        {
            string errorString = null;

            // Prevent extra clicking while Push is in progress
            // and visibly show the action is in progress. 
            ButtonPush.Focus(FocusState.Programmatic);
            ButtonPush.IsEnabled = false;

            try
            {
                await App.MobileService.SyncContext.PushAsync();
            }
            catch (MobileServicePushFailedException ex)
            {
                errorString = "Push failed because of sync errors: " + 
                  ex.PushResult.Errors.Count + " errors, message: " + ex.Message;
            }
            catch (Exception ex)
            {
                errorString = "Push failed: " + ex.Message +
                  "\n\nIf you are still in an offline scenario, " +
                  "you can try your Push again when connected with your Mobile Serice.";
            }

            if (errorString != null)
            {
                MessageDialog d = new MessageDialog(errorString);
                await d.ShowAsync();
            }

            ButtonPush.IsEnabled = true;
        }

11. Compilare la soluzione e verificare che non ci siano errori di compilazione in nessuno dei progetti.


## <a name="test-offline-app"></a>Testare l'app in uno scenario offline

In questa sezione verrà interrotta la connessione dell'app al servizio mobile per simulare uno scenario offline. Quindi, verranno aggiunti alcuni elementi di dati che saranno conservati nell'archivio locale.

Si noti che in questa sezione l'app non deve essere connessa ad alcun servizio mobile. Pertanto, se vengono testati, i pulsanti **Push** e **Pull** genereranno eccezioni. Nella sezione successiva verrà effettuata di nuovo la connessione di questa app client al servizio mobile per testare le operazioni **Push** e **Pull** allo scopo di sincronizzare l'archivio con il database del servizio mobile.


1. In Esplora soluzioni di Visual Studio aprire App.xaml.cs nel progetto condiviso. Cambiare l'inizializzazione di **MobileServiceClient** impostandola su un indirizzo non valido mediante la sostituzione di "**azure-mobile.net**" con "**azure-mobile.xxx**" per l'URL. Salvare il file.

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.xxx/",
            "AppKey"
        );

2. In Visual Studio premere **F5** per compilare ed eseguire l'app. Immettere nuovi elementi todo e fare clic su **Salva** per ognuno di essi. I nuovi elementi todo esistono solo nell'archivio locale fino a quando è possibile effettuarne il push al servizio mobile. L'app client si comporta come se fosse connessa al servizio mobile, supportando tutte le operazioni CRUD (creazione, lettura, aggiornamento ed eliminazione).

    ![][4]

3. Chiudere l'app e riavviarla per verificare che i nuovi elementi creati siano salvati in modo permanente nell'archivio locale.

## <a name="update-online-app"></a>Aggiornare l'app per la riconnessione al servizio mobile

In questa sezione verrà effettuata la riconnessione dell'app al servizio mobile. Viene simulato il passaggio dell'app dallo stato offline allo stato online con il servizio mobile.


1. In Esplora soluzioni di Visual Studio aprire App.xaml.cs nel progetto condiviso. Impostare nuovamente l'inizializzazione di **MobileServiceClient** sull'indirizzo corretto mediante la sostituzione di "**azure-mobile.xxx**" con "**azure-mobile.net**" per l'URL. Salvare il file.

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.net/",
            "Your AppKey"
        );


## <a name="test-online-app"></a>Testare l'app connessa al servizio mobile


In questa sezione verrà effettuato il test delle operazioni push e pull per sincronizzare l'archivio locale con il database del servizio mobile. È possibile testare il client di Windows Store 8.1 o il client di Windows Phone 8.1. Le catture di schermata seguenti sono relative al client di Windows Store 8.1. 

1. In Visual Studio premere **F5** per ricompilare ed eseguire l'app. Si noti che i dati appaiono uguali a quelli dello scenario offline nonostante l'app sia ora connessa al servizio mobile. Questo avviene perché questa app funziona sempre con `IMobileServiceSyncTable` che fa riferimento all'archivio locale.

    ![][4]

2. Accedere al portale di gestione di Microsoft Azure ed esaminare il database relativo al servizio mobile. Se il servizio utilizza il back-end JavaScript per i servizi mobili, è possibile visualizzare i dati dalla scheda **Dati** del servizio mobile. Se si usa il back-end .NET per il servizio mobile, è possibile fare clic sul pulsante **Manage** per il database nell'estensione SQL Azure per eseguire una query sulla tabella.

    Si noti che i dati non sono stati sincronizzati tra il database e l'archivio locale.

    ![][6]

3. Nell'app premere il pulsante  **Push**. L'app chiamerà `MobileServiceClient.SyncContext.PushAsync`. Con questa operazione push il database del servizio mobile riceve i dati dall'archivio. Tuttavia, l'archivio locale non riceve gli elementi dal database del servizio mobile.

    Un'operazione push viene eseguita da `MobileServiceClient.SyncContext` invece che da `IMobileServicesSyncTable` ed effettua il push delle modifiche a tutte le tabelle associate a tale contesto di sincronizzazione. Questa operazione serve per gli scenari in cui sono presenti relazioni tra tabelle.

    ![][7]

4. Nell'app fare clic sulla casella di controllo accanto ad alcuni elementi per completarli nell'archivio locale. 

    ![][8]

5. Questa volta premere il pulsante **Pull** nell'app. L'app chiama `IMobileServiceSyncTable.PullAsync()` e `RefreshTodoItems`.  Si noti che tutti i dati provenienti dal database del servizio mobile sono stati estratti nell'archivio locale e vengono mostrati nell'app. Tuttavia, si noti anche che tutti i dati dell'archivio locale sono stati comunque sottoposti a push nel database del servizio mobile. Questo perché un'operazione **pull effettua sempre un'operazione push all'inizio**. Lo scopo è assicurare che tutte le tabelle nell'archivio locale con le relazioni siano sincronizzate.
 
    ![][9]

    ![][10] 
  

##Riepilogo

Per supportare le funzionalità offline di Servizi mobili è stata usata l'interfaccia `IMobileServiceSyncTable` e inizializzato `MobileServiceClient.SyncContext` in un archivio locale. In questo caso l'archivio locale era un database SQLite.

Le normali operazioni CRUD per Servizi mobili funzionano come se l'app fosse ancora connessa, ma tutte le operazioni si verificano nell'archivio locale.

Per sincronizzare l'archivio locale con il server sono stati usati i metodi `IMobileServiceSyncTable.PullAsync` e `MobileServiceClient.SyncContext.PushAsync`.

*  Per effettuare il push delle modifiche al server, è stata effettuata la chiamata a `IMobileServiceSyncContext.PushAsync()`. Questo metodo fa parte di `IMobileServicesSyncContext` invece che della tabella di sincronizzazione perché effettuerà il push delle modifiche in tutte le tabelle:

    Solo i record che sono stati in qualche modo modificati localmente (tramite le operazioni CRUD) verranno inviate al server.
   
* Per effettuare il pull dei dati da una tabella del server all'app, è stata effettuata la chiamata a `IMobileServiceSyncTable.PullAsync`.

    Un'operazione pull effettua sempre un'operazione push all'inizio. Lo scopo è assicurare che tutte le tabelle nell'archivio locale con le relazioni siano sincronizzate.

    Esistono inoltre overload di `PullAsync()` che consentono di specificare una query per supportare la sincronizzazione incrementale. Se una query non viene passata, `PullAsync()` effettuerà il pull di tutte le righe nella corrispondente tabella (o query). È possibile passare la query per filtrare solo le modifiche necessarie per la sincronizzazione dell'app.


## Passaggi successivi

* [Gestione dei conflitti con il supporto offline per Servizi mobili]

<!-- Anchors. -->
[Aggiornare l'app per supportare le funzionalità offline]: #enable-offline-app
[Testare l'app in uno scenario offline]: #test-offline-app
[Aggiornare l'app per la riconnessione al servizio mobile]: #update-online-app
[Testare l'app connessa al servizio mobile]: #test-online-app
[Passaggi successivi]:#next-steps

<!-- Images -->
[0]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-todoitem-data-browse.png
[1]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-add-reference-sqlite-dialog.png
[2]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-sqlitestore-nuget.png
[3]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-sqlitepcl-nuget.png
[4]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-offline-app-run1.png
[5]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-online-app-run1.png
[6]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse.png
[7]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse2.png
[8]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-online-app-run2.png
[9]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-online-app-run3.png
[10]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse3.png
[11]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-add-wp81-reference-sqlite-dialog.png
[12]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/new-synchandler-class.png
[13]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/cpu-architecture.png


<!-- URLs. -->
[Gestione dei conflitti con il supporto offline per Servizi mobili]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/ 
[Esempio offline TodoList]: http://go.microsoft.com/fwlink/?LinkId=394777
[Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started/#create-new-service
[Per iniziare]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/
[Introduzione ai dati]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/
[Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-windows-store-get-started/
[SQLite for Windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776
[SQLite for Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkId=397953
[Esercitazione con Windows Phone 8 per Visual Studio 2012]: /it-it/documentation/articles/mobile-services-windows-phone-get-started-offline-data/


[Mobile Services SDK Nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.0-beta2
[SQLite store nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.0-beta2
