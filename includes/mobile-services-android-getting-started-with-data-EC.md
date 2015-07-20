Ora che il servizio mobile è pronto, è possibile aggiornare l'app in modo che gli elementi vengano archiviati in Servizi mobili anziché nella raccolta locale.

1. Se non si dispone di [Mobile Services SDK per Android], scaricarlo adesso ed espandere i file compressi.

2. Copiare i file `.jar` dalla cartella `mobileservices` dell'SDK nella cartella `libs` del progetto GetStartedWithData.

3. In Package Explorer in Eclipse fare clic con il pulsante destro del mouse sulla cartella `libs`, quindi scegliere **Refresh** per visualizzare i file con estensione jar copiati

  	Il riferimento a Mobile Services SDK verrà copiato nell'area di lavoro.

4. Aprire il file AndroidManifest.xml e aggiungere la riga seguente che consente all'app di accedere a Servizi mobili in Azure.

		<uses-permission android:name="android.permission.INTERNET" />

5. In Package Explorer aprire il file TodoActivity.java disponibile nel pacchetto com.example.getstartedwithdata e rimuovere i simboli di commento dalle righe di codice seguenti:

		import java.net.MalformedURLException;
		import android.os.AsyncTask;
		import com.google.common.util.concurrent.FutureCallback;
		import com.google.common.util.concurrent.Futures;
		import com.google.common.util.concurrent.ListenableFuture;
		import com.microsoft.windowsazure.mobileservices.MobileServiceClient;
		import com.microsoft.windowsazure.mobileservices.MobileServiceList;
		import com.microsoft.windowsazure.mobileservices.http.NextServiceFilterCallback;
		import com.microsoft.windowsazure.mobileservices.http.ServiceFilter;
		import com.microsoft.windowsazure.mobileservices.http.ServiceFilterRequest;
		import com.microsoft.windowsazure.mobileservices.http.ServiceFilterResponse;
		import com.microsoft.windowsazure.mobileservices.table.MobileServiceTable;

 
6. Impostare come commento le righe seguenti:

		import java.util.ArrayList;
		import java.util.List;

7. Si procederà quindi alla rimozione dell'elenco in memoria attualmente usato dall'app in modo da poterlo sostituire con un servizio mobile. Nella classe **ToDoActivity** impostare come commento la riga di codice seguente che definisce l'elenco **toDoItemList** esistente.

		public List<ToDoItem> toDoItemList = new ArrayList<ToDoItem>();

8. Salvare il file. Gli errori di compilazione verranno indicati nel progetto. Cercare le altre tre posizioni in cui viene usata la variabile `toDoItemList` e impostare come commento le sezioni indicate per procedere alla rimozione completa dell'elenco in memoria.

9. Ora si procederà all'aggiunta del servizio mobile. Rimuovere quindi i simboli di commento dalle righe di codice seguenti:

		private MobileServiceClient mClient;
		private private MobileServiceTable<ToDoItem> mToDoTable;

10. Trovare la classe *ProgressFilter* verso la fine del file e rimuovere i simboli di commento. Questa classe visualizza un indicatore 'loading' mentre *MobileServiceClient* esegue le operazioni di rete.


11. Nel portale di gestione fare clic su **Servizi mobili** e quindi sul servizio mobile appena creato.

12. Fare clic sulla scheda **Dashboard** e prendere nota del valore di **URL sito**, quindi fare clic su **Gestisci chiavi** e prendere nota del valore di **Chiave applicazione**.

   	![](./media/download-android-sample-code/mobile-dashboard-tab.png)

  	Questi valori sono necessari per accedere al servizio mobile dal codice dell'app.

13. Nel metodo **onCreate** rimuovere i simboli di commento dalle righe di codice seguenti che definiscono la variabile **MobileServiceClient**:

		try {
		// Create the Mobile Service Client instance, using the provided
		// Mobile Service URL and key
			mClient = new MobileServiceClient(
					"MobileServiceUrl",
					"AppKey", 
					this).withFilter(new ProgressFilter());

			// Get the Mobile Service Table instance to use
			mToDoTable = mClient.getTable(ToDoItem.class);
		} catch (MalformedURLException e) {
			createAndShowDialog(new Exception("There was an error creating the Mobile Service. Verify the URL"), "Error");
		}

  	Viene creata una nuova istanza di *MobileServiceClient* usata per accedere al servizio mobile. Viene creata anche l'istanza di *MobileServiceTable* usata per comunicare con l'archiviazione dati nel servizio mobile.

14. Nel codice precedente sostituire `MobileServiceUrl` e `AppKey` con l'URL e la chiave applicazione del servizio mobile, in quest'ordine.



15. Rimuovere i simboli di commento dalle righe seguenti del metodo **checkItem**:

	    new AsyncTask<Void, Void, Void>() {
	        @Override
	        protected Void doInBackground(Void... params) {
	            try {
	                mToDoTable.update(item).get();
	                runOnUiThread(new Runnable() {
	                    public void run() {
	                        if (item.isComplete()) {
	                            mAdapter.remove(item);
	                        }
	                        refreshItemsFromTable();
	                    }
	                });
	            } catch (Exception exception) {
	                createAndShowDialog(exception, "Error");
	            }
	            return null;
	        }
	    }.execute();

   	Verrà quindi inviato un aggiornamento dell'elemento al servizio mobile e verranno rimossi gli elementi dall'adattatore.
    
16. Rimuovere i simboli di commento dalle righe seguenti del metodo **addItem**:
	
		// Insert the new item
		new AsyncTask<Void, Void, Void>() {
	        @Override
	        protected Void doInBackground(Void... params) {
	            try {
	                mToDoTable.insert(item).get();
	                if (!item.isComplete()) {
	                    runOnUiThread(new Runnable() {
	                        public void run() {
	                            mAdapter.add(item);
	                        }
	                    });
	                }
	            } catch (Exception exception) {
	                createAndShowDialog(exception, "Error");
	            }
	            return null;
	        }
	    }.execute();
		

  	Verrà creato e inserito un nuovo elemento nella tabella nel servizio mobile remoto.

18. Rimuovere i simboli di commento dalle righe seguenti del metodo **refreshItemsFromTable**:

		// Get the items that weren't marked as completed and add them in the adapter
	    new AsyncTask<Void, Void, Void>() {
	        @Override
	        protected Void doInBackground(Void... params) {
	            try {
	                final MobileServiceList<ToDoItem> result = mToDoTable.where().field("complete").eq(false).execute().get();
	                runOnUiThread(new Runnable() {
	                    @Override
	                    public void run() {
	                        mAdapter.clear();

	                        for (ToDoItem item : result) {
	                            mAdapter.add(item);
	                        }
	                    }
	                });
	            } catch (Exception exception) {
	                createAndShowDialog(exception, "Error");
	            }
	            return null;
	        }
	    }.execute();

	Verrà interrogato il servizio mobile e verranno restituiti gli oggetti non contrassegnati come completati. Gli elementi verranno aggiunti all'adattatore per l'associazione.
		

<!-- URLs. -->
[Mobile Services SDK per Android]: http://aka.ms/Iajk6q

<!---HONumber=July15_HO2-->