

Aggiornamento dell'app per la chiamata all'API personalizzata
-------------------------------------------------------------

1.  Verrà aggiunto un pulsante denominato "Complete All" accanto al pulsante esistente ed entrambi i pulsanti verranno spostati di una riga verso il basso. In Eclipse aprire il file *res\\layout\\activity\_to\_do.xml* del progetto di guida introduttiva e individuare l'elemento **LinearLayout** che contiene l'elemento **Button** denominato `buttonAddToDo`. Copiare l'elemento **LinearLayout** e incollarlo immediatamente dopo l'originale. Eliminare l'elemento **Button** dal primo elemento **LinearLayout**.

2.  Nel secondo elemento **LinearLayout** eliminare l'elemento **EditText** e aggiungere il codice seguente immediatamente dopo l'elemento **Button** esistente:

         <Button android:id="@+id/buttonCompleteItem"
             android:layout_width="wrap_content"
             android:layout_height="wrap_content"
             android:onClick="completeItem"
             android:text="@string/complete_button_text" />

    Verrà aggiunto un nuovo pulsante nella pagina, in una riga distinta, accanto al pulsante esistente.

3.  Il secondo elemento **LinearLayout** avrà ora l'aspetto seguente:

          <LinearLayout android:layout_width="match_parent" 
             android:layout_height="wrap_content" 
             android:background="#71BCFA"
             android:padding="6dip"  >
             <Button android:id="@+id/buttonAddToDo"
                 android:layout_width="wrap_content"
                 android:layout_height="wrap_content"
                 android:onClick="addItem"
                 android:text="@string/add_button_text" />
             <Button android:id="@+id/buttonCompleteItem"
                 android:layout_width="wrap_content"
                 android:layout_height="wrap_content"
                 android:onClick="completeItem"
                 android:text="@string/complete_button_text" />
         </LinearLayout>

4.  Aprire il file res\\values\\string.xml e aggiungere la riga di codice seguente:

    Complete All

5.  In Package Explorer fare clic con il pulsante destro del mouse nella cartella *src* (`com.example.{nome progetti}`), scegliere **New** e quindi **Class**. Nella finestra di dialogo immettere **MarkAllResult** nel campo del nome della classe, scegliere OK e sostituire la definizione della classe risultante con il codice seguente:

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

    Questa classe viene utilizzata per contenere il valore del conteggio di righe restituito dall'API personalizzata.

6.  Individuare il metodo **refreshItemsFromTable** nel file **ToDoActivity.java** e assicurarsi che la prima riga di codice inizi come indicato di seguito:

         mToDoTable.where().field("complete").eq(false)

    Gli elementi verranno filtrati in modo che quelli completati non vengano restituiti dalla query.

7.  Nel file **ToDoActivity.java** aggiungere il metodo seguente:

         public void completeItem(View view) {
             mClient.invokeApi("completeAll", MarkAllResult.class, new ApiOperationCallback<MarkAllResult>() {
                 @Override
                 public void onCompleted(MarkAllResult result, Exception error, ServiceFilterResponse response) {
                     if (error != null) {
                         createAndShowDialog(error, "Error");
                     } else {
                         createAndShowDialog(result.getCount() + " item(s) marked as complete.", "Completed Items");
                         refreshItemsFromTable();
                     }
                 }
             });
         }

    Questo metodo gestisce l'evento **Click** per il nuovo pulsante. Nel client viene chiamato il metodo **invokeApi**, che invia una richiesta POST alla nuova API personalizzata. Il risultato restituito dall'API personalizzata viene visualizzato in una finestra di dialogo con messaggio, insieme a eventuali errori.

Test dell'app
-------------

1.  Scegliere **Run** dal menu **Run** per avviare il progetto nell'emulatore di Android.

    L'app, compilata tramite Android SDK che utilizza la libreria client per inviare una query che restituisce gli elementi per il servizio mobile, verrà eseguita.

2.  Nell'app digitare un testo in **Insert a TodoItem**, quindi fare clic su **Add**.

3.  Ripetere il passaggio precedente fino ad aggiungere diversi elementi todo all'elenco.

4.  Fare clic sul pulsante **Complete All**.

	![](./media/mobile-services-android-call-custom-api/mobile-custom-api-android-completed.png)

    Verrà visualizzata una finestra di dialogo con messaggio indicante il numero di elementi contrassegnati come completati, quindi la query filtrata viene eseguita di nuovo cancellando tutti gli elementi dall'elenco.
