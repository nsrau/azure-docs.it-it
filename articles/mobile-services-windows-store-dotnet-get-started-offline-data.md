<properties urlDisplayName="Using Offline Data" pageTitle="Uso dei dati offline in Servizi mobili (Windows Store) | Mobile Dev Center" metaKeywords="" description="Informazioni su come usare Servizi mobili di Azure per memorizzare nella cache e sincronizzare dati offline nell'applicazione di Windows Store." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Using offline data sync in Mobile Services" authors="wesmc" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/25/2014" ms.author="wesmc" />

# Uso della sincronizzazione dei dati offline in Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-offline](../includes/mobile-services-selector-offline.md)]


<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Questa esercitazione illustra come aggiungere il supporto offline a un'app di Windows Store universale mediante Servizi mobili di Azure. Il supporto offline consente di interagire con un database locale quando l'app si trova in uno scenario offline. Non appena l'app è online con il database back-end, è possibile sincronizzare le modifiche locali mediante le funzionalità offline. 
</p>
<p>Se si preferisce guardare un video, nel clip a destra vengono eseguiti gli stessi passaggi dell'esercitazione.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="label">guarda l'esercitazione</a> <a style="background-image: url('http://video.ch9.ms/ch9/ea1c/ffed2371-4db1-4a8e-8869-80013859ea1c/BuildOfflineAppsAzureMobileServices_220.jpg') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Riproduci video</span></a> <span class="time">14:36</span></div>
</div>


In questa esercitazione si aggiornerà il progetto di app universale creato nell'esercitazione [Introduzione a Servizi mobili] per supportare le funzionalità offline di Servizi mobili di Azure. Quindi, verranno aggiunti dati in uno scenario offline, verrà effettuata la sincronizzazione degli elementi con il database online, quindi si effettuerà l'accesso al portale di gestione di Azure per visualizzare le modifiche apportate durate l'esecuzione dell'app.


>[WACOM.NOTE] Questa esercitazione è stata progettata per illustrare come Servizi mobili consente di usare Azure per archiviare e recuperare i dati di un'app di Windows Store. Se questa è la prima esperienza con Servizi mobili, è consigliabile iniziare dall'esercitazione [Introduzione a Servizi mobili].
>
>Per completare l'esercitazione, è necessario un account Azure. Se non si ha un account, è possibile iscriversi per accedere a una versione di valutazione di Azure e ottenere un massimo di 10 servizi mobili gratuiti che potranno essere usati anche dopo il termine del periodo di valutazione. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">versione di valutazione gratuita di Azure</a>. 
>
>L'esercitazione di Windows Phone 8 meno recente per Visual Studio 2012 è ancora disponibile qui, [Esercitazione di Windows Phone 8 per Visual Studio 2012].


In questa esercitazione vengono descritte le operazioni di base seguenti:

1. [Aggiornamento dell'app per supportare le funzionalità offline]
2. [Test dell'app in uno scenario offline] 
3. [Aggiornamento dell'app per la riconnessione al servizio mobile]
4. [Test dell'app connessa al servizio mobile]

Per completare questa esercitazione, è necessario disporre di:

* Visual Studio 2013 in esecuzione su Windows 8.1
* Esercitazione [Introduzione a Servizi mobili] completata
* [Azure Mobile Services SDK versione 1.3.0 (o successiva)][Pacchetto NuGet Mobile Services SDK]
* [Azure Mobile Services SQLite Store versione 1.0.0 (o successiva)][Pacchetto NuGet SQLite Store]
* SQLite for Windows 8.1

>[AZURE.NOTE] Per completare l'esercitazione, è necessario un account Azure. Se non si ha un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">versione di valutazione gratuita di Azure</a>. 

## <a name="enable-offline-app"></a>Aggiornamento dell'app per supportare le funzionalità offline

Servizi mobili di Azure consente di interagire con un database locale quando si utilizza un servizio mobile in modalità offline. Per usare queste funzionalità nell'app, inizializzare `MobileServiceClient.SyncContext` in un archivio locale. Quindi, fare riferimento alla tabella tramite l'interfaccia `IMobileServiceSyncTable`. In questa esercitazione si userà SQLite per l'archivio locale.

>[AZURE.NOTE] È possibile ignorare questa sezione e scaricare semplicemente il progetto di esempio già dotato di supporto offline dall'archivio degli esempi di GitHub per Servizi mobili. Il progetto di esempio con supporto offline abilitato è disponibile qui, [Esempio offline TodoList].


1. Installare il runtime SQLite per Windows 8.1 e Windows Phone 8.1. 

    * **Windows 8.1:** Installare il runtime SQLite per Windows 8.1 da questo collegamento, [SQLite per Windows 8.1].
    * **Windows Phone 8.1:** Installare il runtime SQLite per Windows Phone 8.1 da questo collegamento, [SQLite per Windows Phone 8.1].

    >[AZURE.NOTE] Quando si fa clic sul collegamento di installazione di SQLite in Internet Explorer, è possibile che venga chiesto di scaricare il file con estensione vsix come zip. Salvare il file in un percorso del disco rigido usando l'estensione vsix anziché zip. Fare quindi doppio clic sul file con estensione vsix in Esplora risorse per eseguire l'installazione.

2. In Visual Studio aprire il progetto completato nell'esercitazione [Introduzione a Servizi mobili]. In Esplora soluzioni fare clic con il pulsante destro del mouse sul nodo **Riferimenti** per i progetti relativi alle piattaforme Windows 8.1 e Windows Phone 8.1 e aggiungere un riferimento a SQLite, che si trova nella sezione **Estensioni**. 

    ![][1]
    </br>**Windows 8.1**

    ![][11]
    </br>**Windows Phone 8.1**

3. Per l'esecuzione del runtime SQLite è necessario che sia impostata l'architettura di processore **x86**, **x64** o **ARM** per il progetto creato. **Qualsiasi CPU** non è supportata. In Esplora soluzioni di Visual Studio fare clic sulla soluzione nella parte superiore della schermata, quindi impostare la casella a discesa dell'architettura di processore su una delle opzioni supportate che si desidera testare.

    ![][13]

4. Installare il pacchetto NuGet **WindowsAzure.MobileServices.SQLiteStore** per i progetti relativi a Windows 8.1 e Windows Phone 8.1.

    * **Windows 8.1:** In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto per Windows 8.1 e scegliere **Gestisci pacchetti Nuget** per eseguire Gestione pacchetti NuGet. Cercare **SQLiteStore** per installare il pacchetto WindowsAzure.MobileServices.SQLiteStore.
    * **Windows Phone 8.1:** Fare clic con il pulsante destro del mouse sul progetto per Windows Phone 8.1 e scegliere **Gestisci pacchetti Nuget** per eseguire Gestione pacchetti NuGet. Cercare **SQLiteStore** per installare il pacchetto WindowsAzure.MobileServices.SQLiteStore.     

    >[WACOM.NOTE] Se l'installazione crea un riferimento a una versione precedente di SQLite, è sufficiente eliminare il riferimento duplicato. 

    ![][2]

5. In Esplora soluzioni di Visual Studio, nel progetto condiviso, aprire il file MainPage.cs. Aggiungere le istruzioni using seguenti all'inizio del file:

        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;
        using Microsoft.WindowsAzure.MobileServices.Sync;

6. Nel file condiviso, Mainpage.cs, sostituire la dichiarazione `todoTable` con una dichiarazione di tipo `IMobileServicesSyncTable` inizializzata mediante una chiamata a `MobileServicesClient.GetSyncTable()`.

        //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
        private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>();


7. Nel file condiviso, MainPage.cs, aggiornare il gestore eventi `OnNavigatedTo` in modo che inizializzi il contesto di sincronizzazione del client con un archivio SQLite. L'archivio SQLite viene creato con una tabella che corrisponde allo schema della tabella di Servizi mobili, ma deve includere la proprietà di sistema **Version** che si aggiungerà nel passaggio successivo.

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


8. In Esplora soluzioni di Visual Studio, nel progetto condiviso, espandere **DataModel** e aprire TodoItem.cs. Aggiungere un'istruzione `using` per lo spazio dei nomi MobileServices. Questa operazione è necessaria per risolvere l'attributo della versione per la proprietà di sistema Version.

        using Microsoft.WindowsAzure.MobileServices;

      Aggiornare quindi la classe `TodoItem` in modo da includere la proprietà di sistema **Version**, come illustrato di seguito. Per supportare le funzionalità offline, lo schema della tabella deve includere la proprietà di sistema **Version**, come illustrato di seguito.

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




9. Aggiornare quindi l'interfaccia utente per i progetti relativi a Windows 8.1 e Windows Phone 8.1 in modo da includere i pulsanti per il supporto delle operazioni di sincronizzazione offline tra il database offline locale e il database di Servizi mobili in Azure. 

    * **Windows 8.1:** In Esplora soluzioni di Visual Studio aprire MainPage.xaml nel progetto relativo a Windows 8.1. Individuare il pulsante denominato **ButtonRefresh**. Sostituire tale elemento con il seguente pannello Stack di pulsanti. 

            <StackPanel Orientation="Horizontal">
                <Button Margin="72,0,0,0" Height="44" Name="ButtonRefresh" Click="ButtonRefresh_Click">
                	Refresh
                </Button>
                <Button Margin="10,0,0,0" Name="ButtonPull" Click="ButtonPull_Click">
                    <StackPanel Orientation="Horizontal">
                        <TextBlock Margin="5">Pull</TextBlock>
                        <SymbolIcon Symbol="Download"/>
                    </StackPanel>
                </Button>
                <Button Margin="10,0,0,0" Name="ButtonPush" Click="ButtonPush_Click">
                    <StackPanel Orientation="Horizontal">
                        <TextBlock Margin="5">Push</TextBlock>
                        <SymbolIcon Symbol="Upload"/>
                    </StackPanel>
                </Button>
            </StackPanel>

    * **Windows Phone 8.1:** In Esplora soluzioni di Visual Studio aprire MainPage.xaml nel progetto relativo a Windows Phone 8.1. Individuare il pulsante denominato **ButtonRefresh**. Sostituire tale elemento con il seguente pannello Stack di pulsanti. 

            <StackPanel Orientation="Horizontal" Grid.Row="3" Grid.ColumnSpan="2">
                <Button Name="ButtonRefresh" Click="ButtonRefresh_Click" Margin="10,0,0,0">
                    Refresh
                </Button>
                <Button Name="ButtonPull" Click="ButtonPull_Click" Margin="10,0,0,0">
                    <StackPanel Orientation="Horizontal">
                        <TextBlock Margin="5">Pull</TextBlock>
                        <SymbolIcon Symbol="Download"/>
                    </StackPanel>
                </Button>
                <Button Name="ButtonPush" Click="ButtonPush_Click" Margin="10,0,0,0">
                    <StackPanel Orientation="Horizontal">
                        <TextBlock Margin="5">Push</TextBlock>
                        <SymbolIcon Symbol="Upload"/>
                    </StackPanel>
                </Button>
        </StackPanel>
        


10. Nel file MainPage.cs condiviso aggiungere gestori eventi Click per i pulsanti **Push** e **Pull**. Salvare il file. 
        
    Si noti che `MobileServicePushFailedException` può verificarsi per un'operazione sia push che pull. Questo avviene perché l'operazione pull esegue internamente un push per garantire che tutte le tabelle e le eventuali relazioni siano sincronizzate. La prossima esercitazione, [Gestione dei conflitti con il supporto offline per Servizi mobili], illustra come gestire queste eccezioni correlate alla sincronizzazione.

    Per supportare la sincronizzazione dei record eliminati con la sincronizzazione dei dati offline, è consigliabile abilitare [Soft Delete](/it-it/documentation/articles/mobile-services-using-soft-delete/). In alternativa, è necessario rimuovere manualmente i record nell'archivio locale oppure chiamare `IMobileServiceSyncTable::PurgeAsync()` per ripulire l'archivio locale.

    In questo esempio viene passata una query alla chiamata del metodo `PullAsync` per supportare la sincronizzazione incrementale. Ciò risulta utile quando si vuole evitare di effettuare il pull dell'intera tabella durante la sincronizzazione. In questo scenario non è necessario apportare modifiche al testo degli elementi todo. È sufficiente effettuare il pull degli elementi completati in modo da spuntarli dalla todolist.

        private async void ButtonPull_Click(object sender, Windows.UI.Xaml.RoutedEventArgs e)
        {
            string errorString = null;

            // Prevent extra clicking while Pull is in progress
            // and visibly show the action is in progress. 
            ButtonPull.Focus(FocusState.Programmatic);
            ButtonPull.IsEnabled = false;

            try
            {
                // All items should be synced since other clients might mark an item as complete
                // The first parameter is a query ID that uniquely identifies the query.
                // This is used in incremental sync to get only newer items the next time PullAsync is called
                await todoTable.PullAsync("allTodoItems", todoTable.CreateQuery());

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

11. Compilare la soluzione e controllare che non si siano verificati errori di compilazione nei progetti.


## <a name="test-offline-app"></a>Test dell'app in uno scenario offline

In questa sezione verrà interrotta la connessione dell'app al servizio mobile per simulare uno scenario offline. Quindi, verranno aggiunti alcuni elementi di dati che saranno conservati nell'archivio locale.

Si noti che in questa sezione l'app non deve essere connessa ad alcun servizio mobile. Pertanto, se vengono testati, i pulsanti **Push** e **Pull** genereranno eccezioni. Nella sezione successiva si connetterà di nuovo questa app client al servizio mobile per testare le operazioni **push** e **pull** allo scopo di sincronizzare l'archivio con il database del servizio mobile.


1. In Esplora soluzioni di Visual Studio aprire App.xaml.cs nel progetto condiviso. Impostare un indirizzo non valido per l'inizializzazione di **MobileServiceClient** sostituendo "**azure-mobile.net**" con "**azure-mobile.xxx**" per l'URL. Salvare il file.

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.xxx/",
            "AppKey"
        );

2. In Visual Studio premere **F5** per compilare ed eseguire l'app. Immettere nuovi elementi todo e fare clic su **Salva** per ciascuno di essi. I nuovi elementi todo esistono solo nell'archivio locale fino a quando è possibile effettuarne il push al servizio mobile. L'app client si comporta come se fosse connessa al servizio mobile, supportando tutte le operazioni CRUD (creazione, lettura, aggiornamento ed eliminazione).

    ![][4]

3. Chiudere l'app e riavviarla per verificare che i nuovi elementi creati siano salvati in modo permanente nell'archivio locale.

## <a name="update-online-app"></a>Aggiornamento dell'app per la riconnessione al servizio mobile

In questa sezione verrà effettuata la riconnessione dell'app al servizio mobile. Viene simulato il passaggio dell'app dallo stato offline allo stato online con il servizio mobile.


1. In Esplora soluzioni di Visual Studio aprire App.xaml.cs nel progetto condiviso. Ripristinare l'indirizzo corretto per l'inizializzazione di **MobileServiceClient** sostituendo "**azure-mobile.xxx**" con "**azure-mobile.net**" per l'URL. Salvare il file.

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.net/",
            "Your AppKey"
        );


## <a name="test-online-app"></a>Test dell'app connessa al servizio mobile


In questa sezione verrà effettuato il test delle operazioni push e pull per sincronizzare l'archivio locale con il database del servizio mobile. È possibile testare il client di Windows Store 8.1 o Windows Phone 8.1. Nelle schermate seguenti è illustrato il client di Windows Store 8.1. 

1. In Visual Studio premere **F5** per ricompilare ed eseguire l'app. Si noti che i dati appaiono uguali a quelli dello scenario offline nonostante l'app sia ora connessa al servizio mobile. Questo avviene perché l'app funziona sempre con l'interfaccia `IMobileServiceSyncTable` che fa riferimento all'archivio locale.

    ![][4]

2. Accedere al portale di gestione di Microsoft Azure ed esaminare il database relativo al servizio mobile. Se il servizio usa il back-end JavaScript per i servizi mobili, è possibile visualizzare i dati dalla scheda **Dati** del servizio mobile. Se si usa il back-end .NET per il servizio mobile, è possibile fare clic sul pulsante **Gestisci** relativo al database nell'estensione SQL Azure per eseguire una query sulla tabella.

    Si noti che i dati non sono stati sincronizzati tra il database e l'archivio locale.

    ![][6]

3. Nell'app scegliere il pulsante **Push**. L'app effettuerà una chiamata a `MobileServiceClient.SyncContext.PushAsync`. Con questa operazione push il database del servizio mobile riceve i dati dall'archivio. Tuttavia, l'archivio locale non riceve gli elementi dal database del servizio mobile.

    Un'operazione push viene eseguita da `MobileServiceClient.SyncContext` anziché da `IMobileServicesSyncTable` ed effettua il push delle modifiche a tutte le tabelle associate allo specifico contesto di sincronizzazione. Questa operazione serve per gli scenari in cui sono presenti relazioni tra tabelle.

€

4. Nell'app fare clic sulla casella di controllo accanto ad alcuni elementi da completare nell'archivio locale. 

    ![][8]

5. Questa volta premere il pulsante **Pull** nell'app. L'app effettua una chiamata a `IMobileServiceSyncTable.PullAsync()` e `RefreshTodoItems`.  Si noti che tutti i dati provenienti dal database del servizio mobile sono stati estratti nell'archivio locale e vengono mostrati nell'app. Tuttavia, si noti anche che tutti i dati dell'archivio locale sono stati comunque sottoposti a push nel database del servizio mobile. Questo avviene perché **un'operazione pull effettua sempre un'operazione push all'inizio** in modo da garantire che tutte le tabelle nell'archivio locale e le eventuali relazioni rimangano sincronizzate.
 
    ![][9]

    ![][10] 
  

##Riepilogo

Per supportare le funzionalità offline di Servizi mobili è stata usata l'interfaccia `IMobileServiceSyncTable` ed è stato inizializzato `MobileServiceClient.SyncContext` con un archivio locale. In questo caso l'archivio locale era un database SQLite.

Le normali operazioni CRUD per Servizi mobili funzionano come se l'app fosse ancora connessa, ma tutte le operazioni si verificano nell'archivio locale.

Per sincronizzare l'archivio locale con il server sono stati usati i metodi `IMobileServiceSyncTable.PullAsync` e `MobileServiceClient.SyncContext.PushAsync`.

*  Per eseguire il push delle modifiche nel server, è stata effettuata una chiamata a `IMobileServiceSyncContext.PushAsync()`. Questo metodo è un membro di `IMobileServicesSyncContext` anziché della tabella di sincronizzazione perché effettua il push delle modifiche in tutte le tabelle:

    Solo i record che sono stati in qualche modo modificati localmente (tramite le operazioni CRUD) verranno inviate al server.
   
* Per eseguire il pull dei dati da una tabella del server all'app, è stata effettuata una chiamata a `IMobileServiceSyncTable.PullAsync`.

    Un'operazione pull effettua sempre un'operazione push all'inizio in modo da garantire che tutte le tabelle nell'archivio locale e le eventuali relazioni rimangano sincronizzate.

    Esistono inoltre overload di `PullAsync()` che consentono di specificare una query per supportare la sincronizzazione incrementale. Se non viene passata una query, `PullAsync()` effettuerà il pull di tutte le righe nella tabella (o query) corrispondente. È possibile passare la query per filtrare solo le modifiche con cui l'app deve eseguire la sincronizzazione.


## Passaggi successivi

* [Gestione dei conflitti con il supporto offline per Servizi mobili]

<!-- Anchors. -->
[Aggiornamento dell'app per supportare le funzionalità offline]: #enable-offline-app
[Test dell'app in uno scenario offline]: #test-offline-app
[Aggiornamento dell'app per la riconnessione al servizio mobile]: #update-online-app
[Test dell'app connessa al servizio mobile]: #test-online-app
[Passaggi successivi]: #next-steps

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
[SQLite per Windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776
[SQLite per Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkId=397953
[Esercitazione di Windows Phone 8 per Visual Studio 2012]: /it-it/documentation/articles/mobile-services-windows-phone-get-started-offline-data/


[Pacchetto NuGet Mobile Services SDK]: http://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.0
[Pacchetto NuGet SQLite Store]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.0

<!--HONumber=35.2-->
