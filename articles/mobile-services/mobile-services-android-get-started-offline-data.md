<properties
	pageTitle="Aggiungere la sincronizzazione offline dei dati all'app di Servizi mobili per Android | Microsoft Azure"
	description="Informazioni su come usare Servizi mobili di Azure per memorizzare nella cache e sincronizzare i dati offline nell'applicazione Android."
	documentationCenter="android"
	authors="RickSaling"
	manager="dwrede"
	editor=""
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="10/01/2015"
	ms.author="ricksal"/>

# Aggiungere la sincronizzazione offline dei dati all'app di Servizi mobili per Android

[AZURE.INCLUDE [mobile-services-selector-offline](../../includes/mobile-services-selector-offline.md)]

## Riepilogo

Le app per dispositivi mobili possono perdere la connettività di rete in caso di spostamento in un'area senza servizio o a causa di problemi di rete. Ad esempio, un'app per il settore edilizio usata in un cantiere in un'area remota potrebbe necessitare dell'immissione di dati di pianificazione, che verranno sincronizzati successivamente in Azure. La sincronizzazione offline di Servizi mobili di Azure permette di continuare a lavorare anche quando si perde la connettività di rete. Questo aspetto è essenziale per molte app per dispositivi mobili. Con la sincronizzazione offline si lavora su una copia locale della tabella di Azure SQL Server e si ripete periodicamente la sincronizzazione tra le due tabelle.

In questa esercitazione verrà aggiornata l'app dell'[esercitazione Guida introduttiva per Servizi mobili] per abilitare la sincronizzazione offline e quindi l'app verrà testata mediante l'aggiunta di dati offline, la sincronizzazione degli elementi nel database online e la verifica delle modifiche nel portale di gestione di Azure.

Indipendentemente dallo stato offline o connesso, è possibile che si verifichino conflitti ogni volta che vengono apportate più modifiche ai dati. Un'esercitazione futura illustrerà la gestione dei conflitti di sincronizzazione, in cui si sceglie la versione delle modifiche da accettare. In questa esercitazione si presuppone che non siano presenti conflitti di sincronizzazione e che eventuali modifiche apportate ai dati esistenti vengano applicate direttamente ad Azure SQL Server.


## Elementi necessari per iniziare

[AZURE.INCLUDE [mobile-services-android-prerequisites](../../includes/mobile-services-android-prerequisites.md)]

## Aggiornare l'app per supportare la sincronizzazione offline

Con la sincronizzazione offline si legge e si scrive da una *tabella di sincronizzazione* (usando l'interfaccia *IMobileServiceSyncTable*), che fa parte di un database **SQL Light** nel dispositivo.

Per eseguire il push e il pull delle modifiche tra il dispositivo e Servizi mobili di Azure, si usa un *contesto di sincronizzazione* (*MobileServiceClient.SyncContext*), inizializzato con il database locale usato per archiviare localmente i dati.

1. Aggiungere autorizzazioni per la verifica della connettività di rete tramite l'aggiunta di questo codice al file *AndroidManifest.xml*:

	    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />


2. Aggiungere le istruzioni **import** seguenti a *ToDoActivity.java*:

		import java.util.Map;

		import android.widget.Toast;

		import com.microsoft.windowsazure.mobileservices.table.query.Query;
		import com.microsoft.windowsazure.mobileservices.table.sync.MobileServiceSyncContext;
		import com.microsoft.windowsazure.mobileservices.table.sync.MobileServiceSyncTable;
		import com.microsoft.windowsazure.mobileservices.table.sync.localstore.ColumnDataType;
		import com.microsoft.windowsazure.mobileservices.table.sync.localstore.SQLiteLocalStore;

3. Accanto alla parte superiore della classe `ToDoActivity`, cambiare la dichiarazione della variabile `mToDoTable` da una classe `MobileServiceTable<ToDoItem>` a una classe `MobileServiceSyncTable<ToDoItem>`.

		 private MobileServiceSyncTable<ToDoItem> mToDoTable;

	In questa fase si definisce la tabella di sincronizzazione.

4. Per gestire l'inizializzazione dell'archivio locale, nel metodo `onCreate` aggiungere le righe seguenti dopo la creazione dell'istanza di `MobileServiceClient`:

			// Saves the query which will be used for pulling data
			mPullQuery = mClient.getTable(ToDoItem.class).where().field("complete").eq(false);

			SQLiteLocalStore localStore = new SQLiteLocalStore(mClient.getContext(), "ToDoItem", null, 1);
			SimpleSyncHandler handler = new SimpleSyncHandler();
			MobileServiceSyncContext syncContext = mClient.getSyncContext();

			Map<String, ColumnDataType> tableDefinition = new HashMap<String, ColumnDataType>();
			tableDefinition.put("id", ColumnDataType.String);
			tableDefinition.put("text", ColumnDataType.String);
			tableDefinition.put("complete", ColumnDataType.Boolean);
			tableDefinition.put("__version", ColumnDataType.String);

			localStore.defineTable("ToDoItem", tableDefinition);
			syncContext.initialize(localStore, handler).get();

			// Get the Mobile Service Table instance to use
			mToDoTable = mClient.getSyncTable(ToDoItem.class);

5. Dopo il codice precedente, che si trova nel blocco `try`, aggiungere un altro blocco `catch` che segue il blocco `MalformedURLException`:

		} catch (Exception e) {
			Throwable t = e;
			while (t.getCause() != null) {
					t = t.getCause();
				}
			createAndShowDialog(new Exception("Unknown error: " + t.getMessage()), "Error");
		}

6. Aggiungere questo metodo per verificare la connettività di rete:

		private boolean isNetworkAvailable() {
			ConnectivityManager connectivityManager
					= (ConnectivityManager) getSystemService(Context.CONNECTIVITY_SERVICE);
			NetworkInfo activeNetworkInfo = connectivityManager.getActiveNetworkInfo();
			return activeNetworkInfo != null && activeNetworkInfo.isConnected();
		}


7. Aggiungere questo metodo per la sincronizzazione tra l'archivio *SQL Light* locale e Azure SQL Server:

		public void syncAsync(){
			if (isNetworkAvailable()) {
				new AsyncTask<Void, Void, Void>() {

					@Override
					protected Void doInBackground(Void... params) {
						try {
							mClient.getSyncContext().push().get();
							mToDoTable.pull(mPullQuery).get();

						} catch (Exception exception) {
							createAndShowDialog(exception, "Error");
						}
						return null;
					}
				}.execute();
			} else {
				Toast.makeText(this, "You are not online, re-sync later!" +
						"", Toast.LENGTH_LONG).show();
			}
		}


8. Nel metodo `onCreate` aggiungere questo codice come penultima riga, immediatamente prima della chiamata a `refreshItemsFromTable`:

			syncAsync();

	In fase di avvio, il dispositivo si sincronizzerà con la tabella di Azure. In caso contrario, verranno visualizzati i contenuti offline più recenti dell'archivio locale.



9. Aggiornare il codice nel metodo `refreshItemsFromTable` in modo da usare questa query (prima riga di codice nel blocco `try`):

		final MobileServiceList<ToDoItem> result = mToDoTable.read(mPullQuery).get();

10. Nel metodo `onOptionsItemSelected` sostituire i contenuti del blocco `if` con questo codice:

			syncAsync();
			refreshItemsFromTable();

	Il codice viene eseguito quando si preme il pulsante di **aggiornamento** nell'angolo superiore destro. Questa è la modalità principale per la sincronizzazione dell'archivio locale in Azure, oltre alla sincronizzazione in fase di avvio. Questa modalità incoraggia l'aggregazione in batch delle modifiche di sincronizzazione e risulta efficiente, poiché il pull da Azure è un'operazione relativamente costosa. È anche possibile progettare l'app in modo che si sincronizzi a ogni modifica, aggiungendo una chiamata a `syncAsync` nei metodi `addItem` e `checkItem`, se necessario per l'app.


## Testare l'app

In questa sezione verrà testato il comportamento dell'app con il WiFi attivato e quindi il WiFi verrà disattivato per creare uno scenario offline.

Quando si aggiungono elementi di dati, gli elementi vengono archiviati nell'archivio di SQL Light, ma vengono sincronizzati con il servizio mobile solo dopo la selezione del pulsante di **aggiornamento**. Altre app potrebbero avere requisiti diversi relativi a quando è necessario sincronizzare i dati, ma per, finalità dimostrative, in questa esercitazione la sincronizzazione viene richiesta esplicitamente dall'utente.

Quando si preme il pulsante, viene avviata una nuova attività in background e viene eseguito prima di tutto il push di tutte le modifiche apportate all'archivio locale, mediante il contesto di sincronizzazione. Viene infine eseguito il pull di tutti i dati modificati da Azure alla tabella locale.


### Test online

È possibile testare gli scenari seguenti.

1. Aggiungere nuovi elementi al dispositivo.
2. Verificare che gli elementi non vengano visualizzati nel portale.
3. Premere quindi il pulsante di **aggiornamento** e verificare che vengano visualizzati.
4. Cambiare o aggiungere un elemento nel portale, quindi premere il pulsante di **aggiornamento** e verificare che le modifiche vengano visualizzate nel dispositivo.

### Test offline

<!-- Now if you run the app and tap the refresh button, you should see all the items from the server. At that point you should be able to turn off the networking from the device by placing it in *Airplane Mode*, and continue making changes – the app will work just fine. When it’s time to sync the changes to the server, turn the network back on, and tap the **Refresh** button again.

One thing which is important to point out: if there are pending changes in the local store, a pull operation will first push those changes to the server (so that if there are changes in the same row, the push operation will fail and the application has an opportunity to handle the conflicts appropriately). That means that the push call in the code above isn’t necessarily required, but I think it’s always a good practice to be explicit about what the code is doing.
-->

1. Attivare la *Modalità aereo* per il dispositivo o il simulatore, in modo da creare uno scenario offline.

2. Aggiungere alcuni elementi *ToDo* o contrassegnare alcuni elementi come completati. Uscire dal dispositivo o dal simulatore (o forzare la chiusura dell'app) e riavviare. Verificare che le modifiche siano state rese persistenti nel dispositivo, poiché sono incluse nell'archivio SQL Light locale.

3. Visualizzare i contenuti della tabella *TodoItem* di Azure. Verificare che i nuovi elementi _non_ siano stati sincronizzati con il server:

   - Per il back-end JavaScript, accedere al portale di gestione e fare clic sulla scheda Dati per visualizzare i contenuti della tabella `TodoItem`.
   - Per il back-end .NET, visualizzare il contenuto della tabella mediante uno strumento SQL, ad esempio *SQL Server Management Studio*, o mediante un client REST, ad esempio *Fiddler* o *Postman*.

4. Attivare il WiFi nel dispositivo o nel simulatore. Premere quindi il pulsante di **aggiornamento**.

5. Visualizzare di nuovo i dati TodoItem nel portale di Azure. Dovrebbero essere visualizzati gli elementi TodoItems nuovi e modificati.


## Passaggi successivi

* [Uso dell'eliminazione temporanea in Servizi mobili][Soft Delete]

## Risorse aggiuntive

* [Cloud Cover: Sincronizzazione offline in Servizi mobili di Azure]

* [Azure Friday: App con supporto offline in Servizi mobili di Azure] (nota: le demo sono per Windows, la descrizione delle funzionalità si applica a tutte le piattaforme)


<!-- URLs. -->

[Get the sample app]: #get-app
[Review the Core Data model]: #review-core-data
[Review the Mobile Services sync code]: #review-sync
[Change the sync behavior of the app]: #setup-sync
[Test the app]: #test-app


[Mobile Services sample repository on GitHub]: https://github.com/Azure/mobile-services-samples


[Get started with Mobile Services]: mobile-services-android-get-started.md
[Handling Conflicts with Offline Support for Mobile Services]: mobile-services-android-handling-conflicts-offline-data.md
[Soft Delete]: mobile-services-using-soft-delete.md

[Cloud Cover: Sincronizzazione offline in Servizi mobili di Azure]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: App con supporto offline in Servizi mobili di Azure]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

[esercitazione Guida introduttiva per Servizi mobili]: mobile-services-android-get-started.md

<!---HONumber=Oct15_HO3-->