
##<a name="update-app"></a>Aggiornare l'app per chiamare l'API personalizzata

1. Verrà aggiunto un pulsante denominato "Complete All" accanto al pulsante esistente ed entrambi i pulsanti verranno spostati di una riga verso il basso. In Android Studio aprire il file *res\layout\activity_to_do.xml* nel progetto di guida introduttiva e individuare l'elemento **LinearLayout** che contiene l'elemento **Button** denominato `buttonAddToDo`. Copiare l'elemento **LinearLayout** e incollarlo immediatamente dopo l'originale. Eliminare l'elemento **Button** dal primo elemento **LinearLayout**.

2. Nel secondo elemento **LinearLayout** eliminare l'elemento **EditText** e aggiungere il codice seguente immediatamente dopo l'elemento **Button** esistente:

        <Button
            android:id="@+id/buttonCompleteItem"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="completeItem"
            android:text="@string/complete_button_text" />

	Verrà aggiunto un nuovo pulsante nella pagina, in una riga distinta, accanto al pulsante esistente.

3. Il secondo elemento **LinearLayout** avrà ora l'aspetto seguente:

	     <LinearLayout
	        android:layout_width="match_parent" 
	        android:layout_height="wrap_content" 
	        android:background="#71BCFA"
	        android:padding="6dip"  >
	        <Button
	            android:id="@+id/buttonAddToDo"
	            android:layout_width="wrap_content"
	            android:layout_height="wrap_content"
	            android:onClick="addItem"
	            android:text="@string/add_button_text" />
	        <Button
	            android:id="@+id/buttonCompleteItem"
	            android:layout_width="wrap_content"
	            android:layout_height="wrap_content"
	            android:onClick="completeItem"
	            android:text="@string/complete_button_text" />
	    </LinearLayout>
	

4. Aprire il file res\values\string.xml e aggiungere la riga di codice seguente:

    	<string name="complete_button_text">Complete All</string>



5. In Project Explorer fare clic con il pulsante destro del mouse sul nome del progetto nella cartella *src* (`com.example.{your projects name}`), scegliere **Nuovo** quindi **Classe**. Nella finestra di dialogo immettere **MarkAllResult** nel campo del nome della classe, scegliere OK e sostituire la definizione della classe risultante con il codice seguente:

		import com.google.gson.annotations.SerializedName;
		
		public class MarkAllResult {
		    @SerializedName("count")
		    public int mCount;
		    
		    public int getCount() {
		        return mCount;
			}
		
			public void setCount(int count) {
			        this.mCount = count;
			}
		}

	Questa classe viene usata per contenere il valore del conteggio di righe restituito dall'API personalizzata.

6. Individuare il metodo **refreshItemsFromTable** nel file **ToDoActivity.java** e assicurarsi che la prima riga di codice del blocco `try` inizi come indicato di seguito:

        final MobileServiceList<ToDoItem> result = mToDoTable.where().field("complete").eq(false).execute().get();

	Gli elementi verranno filtrati in modo che quelli completati non vengano restituiti dalla query.

7. Assicurarsi che all'inizio del file **ToDoActivity.java** siano riportate le istruzioni import seguenti:

		import com.google.common.util.concurrent.FutureCallback;
		import com.google.common.util.concurrent.Futures;
		import com.google.common.util.concurrent.ListenableFuture;

8. Nel file **ToDoActivity.java** aggiungere il metodo seguente:

	public void completeItem(View view) {
	    
	    ListenableFuture<MarkAllResult> result = mClient.invokeApi( "completeAll2", MarkAllResult.class ); 
	    	
	    	Futures.addCallback(result, new FutureCallback<MarkAllResult>() {
	    		@Override
	    		public void onFailure(Throwable exc) {
	    			createAndShowDialog((Exception) exc, "Error");
	    		}
	    		
	    		@Override
	    		public void onSuccess(MarkAllResult result) {
	    			createAndShowDialog(result.getCount() + " item(s) marked as complete.", "Completed Items");
	                refreshItemsFromTable();	
	    		}
	    	});
	    }
	
	Questo metodo gestisce l'evento **Click** per il nuovo pulsante. Nel client viene chiamato il metodo **invokeApi**, che invia una richiesta POST alla nuova API personalizzata. Il risultato restituito dall'API personalizzata viene visualizzato in una finestra di dialogo con messaggio, insieme a eventuali errori.

## Testare l'app

1. Nel menu **Run** scegliere **Run app** per avviare il progetto nell'emulatore di Android o in un dispositivo Android connesso.

	L'app, compilata tramite Android SDK, che usa la libreria client per inviare una query che restituisce gli elementi per il servizio mobile, verrà eseguita.


2. Nell'app digitare un testo in **Insert a TodoItem**, quindi fare clic su **Add**.

3. Ripetere il passaggio precedente fino ad aggiungere diversi elementi todo all'elenco.

4. Fare clic sul pulsante **Complete All**.

  	![](./media/mobile-services-android-call-custom-api/mobile-custom-api-android-completed.png)

	Verrà visualizzata una finestra di dialogo con messaggio indicante il numero di elementi contrassegnati come completati, quindi la query filtrata verrà eseguita di nuovo cancellando tutti gli elementi dall'elenco.

<!---HONumber=July15_HO1-->