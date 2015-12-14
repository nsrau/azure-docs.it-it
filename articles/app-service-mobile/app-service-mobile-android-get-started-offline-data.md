<properties
	pageTitle="Abilitare la sincronizzazione offline per l'app per dispositivi mobili di Azure (Android)"
	description="Informazioni su come usare le app per dispositivi mobili del servizio app per memorizzare nella cache e sincronizzare i dati offline in un'applicazione Android"
	documentationCenter="android"
	authors="lindydonna"
	manager="dwrede"
	services="app-service\mobile"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="12/01/2015"
	ms.author="donnam"/>

# Abilitare la sincronizzazione offline per l'app per dispositivi mobili di Android

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

## Panoramica

Questa esercitazione descrive la funzionalità di sincronizzazione offline delle app per dispositivi mobili di Azure per Android. La sincronizzazione offline consente agli utenti finali di interagire con un'app, visualizzando, aggiungendo e modificando i dati, anche se non è disponibile una connessione di rete. Le modifiche vengono archiviate in un database locale. Quando il dispositivo torna online, vengono sincronizzate con il back-end remoto.

Se questa è la prima esperienza con le app per dispositivi mobili di Azure, è consigliabile completare prima l'esercitazione [Creare un'app Android]. Se non si usa il progetto server di avvio rapido scaricato, è necessario aggiungere al progetto il pacchetto di estensione per l'accesso ai dati. Per altre informazioni sui pacchetti di estensione server, vedere l'articolo relativo all'[utilizzo dell'SDK del server back-end .NET per app per dispositivi mobili di Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Per altre informazioni sulla funzionalità di sincronizzazione offline, vedere l'argomento [Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure].

## Aggiornare l'app per supportare la sincronizzazione offline

Con la sincronizzazione offline si legge e si scrive da una *tabella di sincronizzazione* (usando l'interfaccia *IMobileServiceSyncTable*), che fa parte di un database **SQLite** nel dispositivo.

Per eseguire il push e il pull delle modifiche tra il dispositivo e Servizi mobili di Azure, si usa un *contesto di sincronizzazione* (*MobileServiceClient.SyncContext*), inizializzato con il database locale usato per archiviare localmente i dati.

1. In `TodoActivity.java`, impostare come commento la definizione esistente di `mToDoTable` e rimuovere il commento della versione della tabella di sincronizzazione:
    
	    private MobileServiceSyncTable<ToDoItem> mToDoTable;

2. Nel metodo `onCreate`, impostare come commento l'inizializzazione esistente di `mToDoTable` e rimuovere il commento di questa definizione:

        mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);

3. In `refreshItemsFromTable`, impostare come commento la definizione di `results` e rimuovere il commento di questa definizione:

		// Offline Sync
		final List<ToDoItem> results = refreshItemsFromMobileServiceTableSyncTable();

4. Impostare come commento la definizione di `refreshItemsFromMobileServiceTable`.

5. Rimuovere il commento dalla definizione di `refreshItemsFromMobileServiceTableSyncTable`:

	    private List<ToDoItem> refreshItemsFromMobileServiceTableSyncTable() throws ExecutionException, InterruptedException {
	        //sync the data
	        sync().get();
	        Query query = QueryOperations.field("complete").
	                eq(val(false));
	        return mToDoTable.read(query).get();
	    }

6. Rimuovere il commento dalla definizione di `sync`:

	    private AsyncTask<Void, Void, Void> sync() {
	        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
	            @Override
	            protected Void doInBackground(Void... params) {
	                try {
	                    MobileServiceSyncContext syncContext = mClient.getSyncContext();
	                    syncContext.push().get();
	                    mToDoTable.pull(null).get();
	                } catch (final Exception e) {
	                    createAndShowDialogFromTask(e, "Error");
	                }
	                return null;
	            }
	        };
	        return runAsyncTask(task);
	    }

## Testare l'app

In questa sezione verrà testato il comportamento dell'app con il WiFi attivato e quindi il WiFi verrà disattivato per creare uno scenario offline.

Quando si aggiungono elementi di dati, gli elementi vengono archiviati nell'archivio di SQL Lite, ma vengono sincronizzati con il servizio mobile solo dopo la selezione del pulsante di **aggiornamento**. Altre app potrebbero avere requisiti diversi relativi a quando è necessario sincronizzare i dati, ma per, finalità dimostrative, in questa esercitazione la sincronizzazione viene richiesta esplicitamente dall'utente.

Quando si preme il pulsante, viene avviata una nuova attività in background e viene eseguito prima di tutto il push di tutte le modifiche apportate all'archivio locale, mediante il contesto di sincronizzazione. Viene infine eseguito il pull di tutti i dati modificati da Azure alla tabella locale.

### Test offline

1. Attivare la *Modalità aereo* per il dispositivo o il simulatore. in modo da creare uno scenario offline.

2. Aggiungere alcuni elementi *ToDo* o contrassegnare alcuni elementi come completati. Uscire dal dispositivo o dal simulatore (o forzare la chiusura dell'app) e riavviare. Verificare che le modifiche siano state rese persistenti nel dispositivo, poiché sono incluse nell'archivio SQLite locale.

3. Visualizzare il contenuto della tabella *TodoItem* di Azure mediante uno strumento SQL, come ad esempio *SQL Server Management Studio*, o mediante un client REST, ad esempio *Fiddler* o *Postman*. Verificare che i nuovi elementi _non_ siano stati sincronizzati con il server

   	+ Per un back-end Node.js, passare al [portale di Azure](https://portal.azure.com/), in nel back-end dell’App per dispositivi mobili, fare clic su **Tabelle facili** > **TodoItem** per visualizzare il contenuto della tabella `TodoItem`.
   	+ Per il back-end .NET, visualizzare il contenuto della tabella mediante uno strumento SQL, come ad esempio *SQL Server Management Studio*, o mediante un client REST, ad esempio *Fiddler* o *Postman*.

4. Attivare il WiFi nel dispositivo o nel simulatore. Premere quindi il pulsante di **aggiornamento**.

5. Visualizzare di nuovo i dati TodoItem nel portale di Azure. Dovrebbero essere visualizzati gli elementi TodoItems nuovi e modificati.

## Risorse aggiuntive

* [Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure]

* [Cloud Cover: sincronizzazione offline in Servizi mobili di Azure] (nota: il video è relativo ai Servizi mobili, ma il funzionamento della sincronizzazione offline è simile nelle app per dispositivi mobili di Azure)


<!-- URLs. -->

[Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure]: ../app-service-mobile-offline-data-sync.md

[Creare un'app Android]: ../app-service-mobile-android-get-started.md

[Cloud Cover: sincronizzazione offline in Servizi mobili di Azure]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

<!---HONumber=AcomDC_1203_2015--->