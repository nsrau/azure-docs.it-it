Ora che il servizio mobile è pronto, è possibile aggiornare l'app in modo che gli elementi vengano archiviati in Servizi mobili anziché nella raccolta locale. 

1. Verificare che le seguenti righe siano presenti nel tag **dependencies** nel file *build.gradle (Module app)*. Se non sono presenti, aggiungerle. Verranno aggiunti i riferimenti all'SDK di Servizi mobili per il client Android.

		compile 'com.android.support:support-v4:21.0.3'
    	compile 'com.google.code.gson:gson:2.2.2'
	    compile 'com.google.guava:guava:18.0'
	    compile 'com.microsoft.azure:azure-mobile-services-android-sdk:2.0.2-beta'


2. Ricompilare il progetto facendo clic su **Sync Project with Gradle Files**.

3. Aprire il file AndroidManifest.xml e aggiungere la seguente riga che consente all'app di accedere a Servizi mobili in Azure.

		<uses-permission android:name="android.permission.INTERNET" />


6. Aprire il file AndroidManifest.xml e aggiungere la seguente riga che consente all'app di accedere a Servizi mobili in Azure.

		<uses-permission android:name="android.permission.INTERNET" />

5. In Package Explorer aprire il file TodoActivity.java disponibile nella cartella **GetStartedWithData => app => src => java** e rimuovere i simboli di commento dalle seguenti righe di codice: 



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

 
5. Rimuovere quindi i simboli di commento dalle seguenti righe:

		import java.util.ArrayList;
		import java.util.List;

6. Si procederà quindi alla rimozione dell'elenco in memoria attualmente usato dall'app in modo da poterlo sostituire con un servizio mobile. Nella classe **ToDoActivity** impostare come commento la seguente riga di codice che definisce l'elenco **toDoItemList** esistente.

		public List<ToDoItem> toDoItemList = new ArrayList<ToDoItem>();

7. Salvare il file. Gli errori di compilazione verranno indicati nel progetto. Cercare le altre tre posizioni in cui viene usata la variabile `toDoItemList` e impostare come commento le sezioni indicate. In questo modo l'elenco in memoria viene rimosso completamente. 

8. Ora si procederà all'aggiunta del servizio mobile. Rimuovere quindi i simboli di commento dalle seguenti righe di codice:

		private MobileServiceClient mClient;
		private private MobileServiceTable<ToDoItem> mToDoTable;

9. Individuare la classe *ProgressFilter* verso la fine del file e rimuovere i simboli di commento. Questa classe visualizza un indicatore 'loading' mentre *MobileServiceClient* esegue le operazioni di rete.


10. Nel portale di gestione fare clic su **Servizi mobili** e quindi sul servizio mobile appena creato.

11. Fare clic sulla scheda **Dashboard** e prendere nota del valore di **URL sito**, quindi fare clic su **Gestisci chiavi** e prendere nota del valore di **Chiave applicazione**.

   	![](./media/download-android-sample-code/mobile-dashboard-tab.png)

  	Questi valori sono necessari per accedere al servizio mobile dal codice dell'app.

12. Nel metodo **onCreate** rimuovere i simboli di commento dalle seguenti righe di codice che definiscono la variabile **MobileServiceClient**:

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

  	Verrà creata una nuova istanza di *MobileServiceClient* usata per accedere al servizio mobile. Verrà inoltre creata l'istanza di *MobileServiceTable* usata per comunicare con l'archiviazione dati nel servizio mobile.

13. Nel codice precedente sostituire `MobileServiceUrl` e `AppKey` con l'URL del servizio mobile e la chiave applicazione, in quest'ordine.



14. Rimuovere i simboli di commento dalle seguenti righe del metodo **checkItem**:

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
    
15. Rimuovere i simboli di commento dalle seguenti righe del metodo **addItem**:
	
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

16. Rimuovere i simboli di commento dalle seguenti righe del metodo **refreshItemsFromTable**:

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
[Mobile Services Android SDK]: http://aka.ms/Iajk6q
<!--HONumber=47-->
