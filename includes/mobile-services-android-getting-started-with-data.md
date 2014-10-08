Ora che il servizio mobile è pronto, è possibile aggiornare l'app in modo che gli elementi vengano archiviati in Servizi mobili anziché nella raccolta locale.

1.  Se non si dispone di [Mobile Services Android SDK][], scaricarlo adesso ed espandere i file compressi.

2.  Copiare i file `.jar` dalla cartella `mobileservices` dell'SDK nella cartella `libs` del progetto GetStartedWithData.

3.  In Package Explorer in Eclipse fare clic con il pulsante destro del mouse sulla cartella `libs`, quindi scegliere **Refresh** per visualizzare i file jar copiati

    Il riferimento a Mobile Services SDK verrà copiato nell'area di lavoro.

4.  Aprire il file AndroidManifest.xml e aggiungere la riga seguente che consente all'app di accedere a Servizi mobili in Azure.

        <uses-permission android:name="android.permission.INTERNET" />

5.  In Package Explorer aprire il file TodoActivity.java disponibile nel pacchetto com.example.getstartedwithdata e rimuovere i simboli di commento dalle righe di codice seguenti:

        import com.microsoft.windowsazure.mobileservices.MobileServiceClient;
        import com.microsoft.windowsazure.mobileservices.MobileServiceTable;
        import com.microsoft.windowsazure.mobileservices.NextServiceFilterCallback;
        import com.microsoft.windowsazure.mobileservices.ServiceFilter;
        import com.microsoft.windowsazure.mobileservices.ServiceFilterRequest;
        import com.microsoft.windowsazure.mobileservices.ServiceFilterResponse;
        import com.microsoft.windowsazure.mobileservices.ServiceFilterResponseCallback;
        import com.microsoft.windowsazure.mobileservices.TableOperationCallback;
        import com.microsoft.windowsazure.mobileservices.TableQueryCallback;

        import java.net.MalformedURLException;

6.  Si procederà quindi alla rimozione dell'elenco in memoria attualmente utilizzato dall'app in modo da poterlo sostituire con un servizio mobile. Nella classe **ToDoActivity** impostare come commento la riga di codice seguente che definisce l'elenco **toDoItemList** esistente.

        public List<ToDoItem> toDoItemList = new ArrayList<ToDoItem>();

7.  Salvare il file. Gli errori di compilazione verranno indicati nel progetto. Cercare le altre tre posizioni in cui viene usata la variabile `toDoItemList` e impostare come commento le sezioni indicate. Rimuovere anche `import java.util.ArrayList`. per procedere alla rimozione completa dell'elenco in memoria.

8.  Ora si procederà all'aggiunta del servizio mobile. Rimuovere quindi i simboli di commento dalle righe di codice seguenti:

        private MobileServiceClient mClient;
        private private MobileServiceTable<ToDoItem> mToDoTable;

9.  Individuare la classe ProgressFilter verso la fine del file e rimuovere i simboli di commento. Questa classe visualizza un indicatore 'loading' mentre MobileServiceClient esegue le operazioni di rete.

10. Nel portale di gestione fare clic su **Mobile Services** e quindi sul servizio mobile appena creato.

11. Fare clic sulla scheda **Dashboard** e prendere nota del valore di **Site URL**, quindi fare clic su **Manage keys** e prendere nota del valore di **Application key**.

    ![](./media/download-android-sample-code/mobile-dashboard-tab.png)

    Questi valori sono necessari per accedere al servizio mobile dal codice dell'app.

12. Nel metodo **onCreate** rimuovere i simboli di commento dalle righe di codice seguenti che definiscono la variabile **MobileServiceClient**:

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

    Verrà creata una nuova istanza di MobileServiceClient utilizzata per accedere al servizio mobile. Verrà inoltre creata l'istanza di MobileServiceTable utilizzata per comunicare con l'archiviazione dati nel servizio mobile.

13. Nel codice precedente sostituire `MobileServiceUrl` e `AppKey` con l'URL del servizio mobile e la chiave applicazione, in quest'ordine.

14. Rimuovere i simboli di commento dalle righe seguenti del metodo **checkItem**:

        mToDoTable.update(item, new TableOperationCallback<ToDoItem>() {    
            public void onCompleted(ToDoItem entity, Exception exception,
                    ServiceFilterResponse response) {
                if(exception == null){
                    if (entity.isComplete()) {
                        mAdapter.remove(entity);
                    }
                } else {
                    createAndShowDialog(exception, "Error");    
                }
            }
        });

    Verrà quindi inviato un aggiornamento dell'elemento al servizio mobile e verranno rimossi gli elementi dall'adattatore.

15. Rimuovere i simboli di commento dalle righe seguenti del metodo **addItem**:

        mToDoTable.insert(item, new TableOperationCallback<ToDoItem>() {

            public void onCompleted(ToDoItem entity, Exception exception,
                    ServiceFilterResponse response) {
                if(exception == null){
                    if (!entity.isComplete()) {
                        mAdapter.add(entity);
                    }
                } else {
                    createAndShowDialog(exception, "Error");
                }               
            }
        });

    Verrà creato e inserito un nuovo elemento nella tabella nel servizio mobile remoto.

16. Rimuovere i simboli di commento dalle righe seguenti del metodo **refreshItemsFromTable**:

        mToDoTable.where().field("complete").eq(false)
        .execute(new TableQueryCallback<ToDoItem>() {
             public void onCompleted(List<ToDoItem> result, 
                     int count, Exception exception, 
                     ServiceFilterResponse response) {

                        if(exception == null){
                            mAdapter.clear();

                            for (ToDoItem item : result) {
                                mAdapter.add(item);
                            }
                        } else {
                            createAndShowDialog(exception, "Error");
                        }
                    }
                }); 

    Verrà interrogato il servizio mobile e verranno restituiti gli oggetti non contrassegnati come completati. Gli elementi verranno aggiunti all'adattatore per l'associazione.

<!-- URLs. -->

  [Mobile Services Android SDK]: http://go.microsoft.com/fwlink/p/?LinkID=280126
   
