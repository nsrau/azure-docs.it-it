<properties
    pageTitle="Abilitare la sincronizzazione offline per l'app per dispositivi mobili di Azure (Xamarin Android)"
    description="Informazioni su come usare l'app per dispositivi mobili del servizio app per memorizzare nella cache e sincronizzare offline i dati in un'applicazione Xamarin Android"
    documentationCenter="xamarin"
    authors="wesmc7777"
    manager="dwrede"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="article"
	ms.date="05/05/2016"
    ms.author="wesmc"/>

# Abilitare la sincronizzazione offline per l'app per dispositivi mobili Xamarin.Android

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## Panoramica

Questa esercitazione descrive la funzionalità di sincronizzazione offline di App per dispositivi mobili di Azure per Xamarin.Android. La sincronizzazione offline consente agli utenti finali di interagire con un'app, visualizzando, aggiungendo e modificando i dati, anche se non è disponibile una connessione di rete. Le modifiche vengono archiviate in un database locale. Quando il dispositivo torna online, vengono sincronizzate con il servizio remoto.

In questa esercitazione si aggiornerà il progetto client creato nell'esercitazione [Creare un'app Xamarin Android] per supportare le funzionalità offline di App per dispositivi mobili di Azure. Se non si usa il progetto server di avvio rapido scaricato, è necessario aggiungere al progetto il pacchetto di estensione per l'accesso ai dati. Per altre informazioni sui pacchetti di estensione server, vedere l'articolo relativo all'[utilizzo dell'SDK del server back-end .NET per app per dispositivi mobili di Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Per altre informazioni sulla funzionalità di sincronizzazione offline, vedere l'argomento [Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure].

## Verificare il codice di sincronizzazione del client

Il progetto client Xamarin scaricato dopo aver completato l'esercitazione [Creare un'app Xamarin Android] contiene già il codice che supporta la sincronizzazione offline mediante un database SQLite locale. Ecco un breve riepilogo degli elementi già inclusi nel codice dell'esercitazione. Per una panoramica concettuale della funzionalità, vedere [Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure].

* Prima di poter eseguire qualsiasi operazione su tabella, è necessario inizializzare l'archivio locale. Il database di archiviazione locale viene inizializzato quando `ToDoActivity.OnCreate()` esegue `ToDoActivity.InitLocalStoreAsync()`. Questo comporta la creazione di un nuovo database SQLite locale usando la classe `MobileServiceSQLiteStore` fornita dall'SDK del client delle app per dispositivi mobili di Azure.

	Il metodo `DefineTable` crea nell'archivio locale una tabella corrispondente ai campi del tipo specificato, `ToDoItem` in questo caso. Il tipo non deve necessariamente includere tutte le colonne presenti nel database remoto. È possibile archiviare solo un subset di colonne.

		// ToDoActivity.cs
        private async Task InitLocalStoreAsync()
        {
            // new code to initialize the SQLite store
            string path = Path.Combine(System.Environment
				.GetFolderPath(System.Environment.SpecialFolder.Personal), localDbFilename);

            if (!File.Exists(path))
            {
                File.Create(path).Dispose();
            }

            var store = new MobileServiceSQLiteStore(path);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            // To use a different conflict handler, pass a parameter to InitializeAsync.
			// For more details, see http://go.microsoft.com/fwlink/?LinkId=521416.
            await client.SyncContext.InitializeAsync(store);
        }


* Il membro `toDoTable` di `ToDoActivity` è di tipo `IMobileServiceSyncTable` invece che `IMobileServiceTable`. Per questo motivo, tutte le operazioni di creazione, lettura, aggiornamento ed eliminazione (CRUD, Create, Read, Update, Delete) sulle tabelle vengono indirizzate al database di archiviazione locale.

	È possibile decidere quando eseguire il push delle modifiche nel back-end dell'app per dispositivi mobili di Azure chiamando `IMobileServiceSyncContext.PushAsync()` con il contesto di sincronizzazione per la connessione client. Il contesto di sincronizzazione aiuta a mantenere le relazioni tra tabelle rilevando le modifiche apportate da un'app client in tutte le tabelle ed eseguendone il push quando viene chiamato `PushAsync`.

	Il codice fornito chiama `ToDoActivity.SyncAsync()` per eseguire la sincronizzazione ogni volta che l'elenco todoitem viene aggiornato o un oggetto todoitem viene aggiunto o completato. La sincronizzazione avviene quindi dopo ogni modifica locale, eseguendo un'operazione push nel contesto di sincronizzazione e un'operazione pull nella tabella di sincronizzazione. Tuttavia, è importante tenere presente che se viene eseguita un'operazione pull su una tabella con aggiornamenti locali in sospeso rilevati dal contesto, tale operazione attiva automaticamente un'operazione push sul contesto, che viene eseguita prima del pull. In questi casi (aggiornamento, aggiunta e completamento di elementi) è quindi possibile omettere la chiamata esplicita a `PushAsync`, che è ridondante.

    Nel codice fornito viene eseguita una query su tutti i record presenti nella tabella `TodoItem` remota, ma è anche possibile filtrare i record passando un ID di query e una query a `PushAsync`. Per altre informazioni, vedere la sezione *Sincronizzazione incrementale* in [Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure].

	<!-- Need updated conflict handling info : `InitializeAsync` uses the default conflict handler, which fails whenever there is a conflict. To provide a custom conflict handler, see the tutorial [Handling conflicts with offline support for Mobile Services].
	-->


		// ToDoActivity.cs
        private async Task SyncAsync()
        {
			try {
	            await client.SyncContext.PushAsync();
	            await toDoTable.PullAsync("allTodoItems", toDoTable.CreateQuery()); // query ID is used for incremental sync
			} catch (Java.Net.MalformedURLException) {
				CreateAndShowDialog (new Exception ("There was an error creating the Mobile Service. Verify the URL"), "Error");
			} catch (Exception e) {
				CreateAndShowDialog (e, "Error");
			}
        }


## Eseguire l'app client

Eseguire l'applicazione client almeno una volta per popolare il database di archiviazione locale. Nella sezione successiva verrà simulato uno scenario offline e verranno modificati i dati nell'archivio locale mentre l'app è offline.

## Aggiornare il comportamento di sincronizzazione dell'app client

In questa sezione si modificherà l'app client per simulare uno scenario offline usando un URL di applicazione non valido per il back-end. Quando si aggiungono o si modificano elementi di dati, queste modifiche vengono conservate nell'archivio locale, ma non vengono sincronizzate con l'archivio dati back-end fino a quando non viene ristabilita la connessione.

1. All'inizio di `ToDoActivity.cs` modificare l'inizializzazione di `applicationURL` in modo da puntare a URL non validi:

        const string applicationURL = @"https://your-service.azurewebsites.fail/";

	Se l'app usa anche una procedura di autenticazione, non sarà possibile eseguire l'accesso. È possibile anche illustrare il funzionamento offline disabilitando sul dispositivo le reti Wi-Fi e cellulare o impostando la modalità aereo.

2. Aggiornare `ToDoActivity.SyncAsync` in modo che gli oggetti `MobileServicePushFailedException` vengano rilevati e semplicemente ignorati, presupponendo di trovarsi offline.

        private async Task SyncAsync()
        {
			try {
	            await client.SyncContext.PushAsync();
	            await toDoTable.PullAsync("allTodoItems", toDoTable.CreateQuery()); // query ID is used for incremental sync
			} catch (Java.Net.MalformedURLException) {
				CreateAndShowDialog (new Exception ("There was an error creating the Mobile Service. Verify the URL"), "Error");
			} catch (MobileServicePushFailedException)
            {
                // Not reporting this exception. Assuming the app is offline for now
            }
            catch (Exception e) {
				CreateAndShowDialog (e, "Error");
			}
        }


3. Compilare ed eseguire l'app. Se viene visualizzata una finestra di dialogo di eccezione che segnala un'eccezione di risoluzione dei nomi, chiuderla. Aggiungere alcuni nuovi elementi todo e notare che l'app si comporta come se fosse connessa perché l'oggetto `MobileServicePushFailedException` viene gestito senza visualizzare la finestra di dialogo.

4. I nuovi elementi aggiunti sono presenti solo nell'archivio locale fino a quando non è possibile effettuarne il push al back-end mobile. Chiudere l'app e riavviarla per verificare che i nuovi elementi creati siano salvati in modo permanente nell'archivio locale.

5. (Facoltativo) Usare Visual Studio per visualizzare la tabella di database SQL di Azure per verificare che i dati nel database back-end non siano cambiati.

   	In Visual Studio aprire **Esplora server**. Passare al database in **Azure**->**Database SQL**. Fare clic con il pulsante destro del mouse sul database e scegliere **Apri in Esplora oggetti di SQL Server**. È ora possibile passare alla tabella di database SQL e al relativo contenuto.

6. (Facoltativo) Usare uno strumento REST come Fiddler o Postman per eseguire una query sul back-end mobile, usando una query GET nel formato `https://your-mobile-app-backend-name.azurewebsites.net/tables/TodoItem`.


## Aggiornare l'app client per la riconnessione al back-end mobile

In questa sezione viene riconnessa l'app al back-end mobile, azione che consente di simulare il ritorno dell'app nello stato online. Quando si esegue il movimento di aggiornamento, i dati verranno sincronizzati con il back-end mobile.

1. Aprire `ToDoActivity.cs`. Correggere `applicationURL` in modo che punti all'URL corretto.

2. Ricompilare ed eseguire l'app. L'app prova a eseguire la sincronizzazione con il back-end dell'app per dispositivi mobili di Azure dopo l'avvio. Verificare che non vengano create finestre di dialogo di eccezione.

3. (Facoltativo) Visualizzare i dati aggiornati usando Esplora oggetti di SQL Server o uno strumento REST come Fiddler. Si noti che i dati sono stati sincronizzati tra il database back-end dell'app per dispositivi mobili di Azure e l'archivio locale.

    Si noti che i dati sono stati sincronizzati tra il database e l'archivio locale e includono gli elementi aggiunti mentre ci si trovava offline.

## Risorse aggiuntive

* [Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure]

* [Cloud Cover: sincronizzazione offline in Servizi mobili di Azure] (nota: il video è relativo ai Servizi mobili, ma il funzionamento della sincronizzazione offline è simile nelle app per dispositivi mobili di Azure)

<!-- ##Summary

[AZURE.INCLUDE [mobile-services-offline-summary-csharp](../../includes/mobile-services-offline-summary-csharp.md)]

## Next steps

* [Handling conflicts with offline support for Mobile Services]

* [How to use the Xamarin Component client for Azure Mobile Services]
 -->

<!-- Images -->

<!-- URLs. -->
[Creare un'app Xamarin Android]: ../app-service-mobile-xamarin-android-get-started.md
[Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure]: ../app-service-mobile-offline-data-sync.md

[How to use the Xamarin Component client for Azure Mobile Services]: ../partner-xamarin-mobile-services-how-to-use-client-library.md

[Xamarin Studio]: http://xamarin.com/download
[Xamarin extension]: http://xamarin.com/visual-studio

[Cloud Cover: sincronizzazione offline in Servizi mobili di Azure]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri

<!---HONumber=AcomDC_0511_2016-->