<properties linkid="develop-mobile-tutorials-validate-modify-and-augment-data-android" urlDisplayName="Validate Data - Android" pageTitle="Use server scripts to validate and modify data (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to validate and modify data sent using server scripts from your Android app." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="ricksal" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal" />

# Convalida e modifica dei dati in Servizi mobili mediante script del server

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/it-it/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet" title="Windows Store C#">Windows Store C#</a><a href="/it-it/develop/mobile/tutorials/validate-modify-and-augment-data-js" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/it-it/develop/mobile/tutorials/validate-modify-and-augment-data-wp8" title="Windows Phone">Windows Phone</a><a href="/it-it/develop/mobile/tutorials/validate-modify-and-augment-data-ios" title="iOS">iOS</a><a href="/it-it/develop/mobile/tutorials/validate-modify-and-augment-data-android" title="Android" class="current">Android</a><a href="/it-it/develop/mobile/tutorials/validate-modify-and-augment-data-html" title="HTML">HTML</a><a href="/it-it/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/it-it/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>
</div>

In questo argomento viene illustrato come utilizzare gli script del server in Servizi mobili di Azure. Gli script del server vengono registrati in un servizio mobile e possono essere utilizzati per eseguire numerose operazioni sui dati inseriti e aggiornati, incluse la convalida e la modifica dei dati. In questa esercitazione si procederà alla definizione e alla registrazione di script del server per la convalida e la modifica dei dati. Poiché il comportamento degli script sul lato server spesso influisce sul client, si procederà all'aggiornamento dell'app per Android per implementare questi nuovi comportamenti.

In questa esercitazione vengono descritte le operazioni di base seguenti:

1.  [Aggiunta della convalida della lunghezza della stringa][Aggiunta della convalida della lunghezza della stringa]
2.  [Aggiornamento del client per il supporto della convalida][Aggiornamento del client per il supporto della convalida]
3.  [Aggiunta di un timestamp all'inserimento][Aggiunta di un timestamp all'inserimento]
4.  [Aggiornamento del client per la visualizzazione del timestamp][Aggiornamento del client per la visualizzazione del timestamp]

Questa esercitazione è basata sulle procedure e sull'app di esempio creata nell'esercitazione precedente [Introduzione ai dati][Introduzione ai dati]. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione ai dati][Introduzione ai dati].

## <a name="string-length-validation"></a>Aggiunta della convalida

È sempre consigliabile convalidare la lunghezza dei dati inviati dagli utenti. A questo scopo, è innanzitutto necessario registrare uno script che convalida la lunghezza dei dati di stringa inviati al servizio mobile e rifiuta le stringhe troppo lunghe, in questo caso lunghe più di 10 caratteri.

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure], fare clic su **Mobile Services** e quindi sull'app.

    ![][0]

2.  Fare clic sulla scheda **Data** e quindi sulla tabella **TodoItem**.

    ![][1]

3.  Fare clic su **Script** e quindi selezionare l'operazione **Insert**.

    ![][2]

4.  Sostituire lo script esistente con la funzione seguente e quindi fare clic su **Save**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be 10 characters or less.');
            } else {
                request.execute();
            }
        }

    Questo script verifica la lunghezza della proprietà **text** e invia una risposta di errore quando la lunghezza supera i 10 caratteri. In caso contrario, viene chiamato il metodo **execute** per completare l'operazione di inserimento.

    <div class="dev-callout"> 
<b>Nota</b> 
<p>&Egrave; possibile rimuovere uno script registrato nella scheda <strong>Script</strong> facendo clic su <strong>Clear</strong> e quindi su <strong>Save</strong>.</p></div>

## <a name="update-client-validation"></a>Aggiornamento del client

Ora che il servizio mobile convalida dati e invia risposte di errore, è necessario verificare che l'app gestisca correttamente le risposte di errore della convalida.

1.  In Eclipse aprire il progetto creato durante l'esercitazione [Introduzione ai dati][Introduzione ai dati].

2.  Nel file ToDoActivity.java individuare il metodo **addItem** e sostituire la chiamata al metodo createAndShowDialog con il codice seguente:

        createAndShowDialog(exception.getCause().getMessage(), "Error");

    Verrà visualizzato il messaggio di errore restituito dal servizio mobile.

3.  Scegliere **Run** dal menu **Run** per avviare l'app, quindi digitare un testo di lunghezza superiore a 10 caratteri nella casella di testo e fare clic sul pulsante **Add**.

Si noti che l'errore viene gestito e che il messaggio di errore viene visualizzato all'utente.

## <a name="add-timestamp"></a>Aggiunta di un timestamp

Nelle attività precedenti si è proceduto alla convalida di un'operazione di inserimento che è accettata o rifiutata. Ora, si procederà all'aggiornamento dei dati inseriti utilizzando uno script server che aggiunge una proprietà timestamp all'oggetto prima dell'inserimento.

<div class="dev-callout"><b>Nota</b>
<p>La propriet&agrave; timestamp <b>createdAt</b> illustrata in questo esempio &egrave; ora ridondante. Servizi mobili crea automaticamente una propriet&agrave; di sistema <b>__createdAt</b> per ogni tabella.</p>
</div>

1.  Nella scheda **Scripts** del [portale di gestione][portale di gestione di Azure] sostituire lo script **Insert** corrente con la funzione seguente, quindi fare clic su **Save**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                item.createdAt = new Date();
                request.execute();
            }
        }

    Questa funzione aumenta lo script insert precedente tramite l'aggiunta di una nuova proprietà timestamp **createdAt** all'oggetto prima che questo venga inserito dalla chiamata al metodo **request**.**execute**.

    <div class="dev-callout"><b>Nota</b>
<p>&Egrave; necessario abilitare lo schema dinamico la prima volta che viene eseguito lo script insert. Quando &egrave; abilitato lo schema dinamico, Servizi mobili aggiunge automaticamente la colonna <strong>createdAt</strong> alla tabella <strong>TodoItem</strong> alla prima esecuzione. Lo schema dinamico &egrave; abilitato per impostazione predefinita per un nuovo servizio mobile e deve essere disabilitato prima che l'app venga pubblicata.</p>
</div>

2.  Scegliere **Run** dal menu **Run** per avviare l'app e quindi digitare un testo di lunghezza inferiore a 10 caratteri nella casella di testo e fare clic su **Add**.

    Si noti che il nuovo timestamp non viene visualizzato nell'interfaccia utente dell'app.

3.  Tornare al portale di gestione e fare clic sulla scheda **Browse** nella tabella **todoitem**.

    È ora presente una colonna **createdAt** che contiene un valore timestamp per il nuovo elemento inserito.

È ora necessario aggiornare l'app per Android affinché visualizzi questa nuova colonna.

## <a name="update-client-timestamp"></a>Nuovo aggiornamento del client

Il client del servizio mobile ignorerà i dati delle risposte che non è in grado di serializzare nelle proprietà del tipo definito. Nell'ultimo passaggio si procederà all'aggiornamento del client affinché visualizzi questi nuovi dati.

1.  In Package Explorer aprire il file ToDoItem.java, quindi aggiungere l'istruzione **import** seguente:

        import java.util.Date;

2.  Aggiungere il codice seguente alle definizioni dei campi privati nella classe **TodoItem**:

        /**
         * Timestamp of the item inserted by the service.
         */
        @com.google.gson.annotations.SerializedName("createdAt")
        private Date mCreatedAt;

    <div class="dev-callout"><b>Nota</b>
<p>L'annotazione <code data-inline="1">SerializedName</code> comunica al client di eseguire il mapping della nuova propriet&agrave; <code data-inline="1">mCreatedAt</code> dell'app alla colonna <code data-inline="1">createdAt</code> definita nella tabella TodoItem che presenta un nome diverso. Grazie a questa annotazione, i nomi di propriet&agrave; sugli oggetti dell'app possono essere diversi dai nomi di colonna in database SQL. Senza questa annotazione, viene generato un errore dovuto alle differenze di convenzione per l'utilizzo di maiuscole e minuscole.</p>
</div>

3.  Aggiungere i metodi seguenti alla classe ToDoItem per ottenere e impostare la nuova proprietà mCreatedAt:

        /**
         * Sets the timestamp.
         * 
         * @param date
         *            timestamp to set
         */
        public final void setCreatedAt(Date date) {
            mCreatedAt = date;
        }

        /**
         * Returns the timestamp.
         */
        public Date getCreatedAt() {
            return mCreatedAt;
        }

4.  In Package Explorer aprire il file ToDoItemAdapter.java, quindi aggiungere l'istruzione **import** seguente:

        import java.text.DateFormat;

5.  Nel metodo GetView aggiungere il codice seguente:

        String createdAtText = "";
        if (currentItem.getCreatedAt() != null){
            DateFormat formatter = DateFormat.getDateInstance(DateFormat.SHORT);
            createdAtText = formatter.format(currentItem.getCreatedAt());
        }

    Se esiste un valore di timestamp, verrà generata una stringa di data formattata.

6.  Individuare il codice `checkBox.setText(currentItem.getText());` e sostituire questa riga con il codice seguente:

        checkBox.setText(currentItem.getText() + " " + createdAtText);

    La data del timestamp verrà aggiunta all'elemento per la visualizzazione.

7.  Scegliere **Run** dal menu **Run** per avviare l'app.

    Si noti che il timestamp è visualizzato solo per gli elementi inseriti dopo l'aggiornamento dello script insert.

8.  Sostituire il metodo **RefreshItemsFromTable** esistente con il codice seguente:

        private void refreshItemsFromTable() {

            mToDoTable.where().field("complete").eq(false).and().field("createdAt").ne((String)null)
                    .execute(new TableQueryCallback<ToDoItem>() {

                        public void onCompleted(List<ToDoItem> result, int count,
                                Exception exception, ServiceFilterResponse response) {
                            if (exception == null) {
                                mAdapter.clear();

                                for (ToDoItem item : result) {
                                    mAdapter.add(item);
                                }

                            } else {
                                createAndShowDialog(exception, "Error");
                            }
                        }
                    });
        }

    Questo metodo aggiorna la query affinché filtri anche gli elementi che non dispongono di un valore timestamp.

9.  Scegliere **Run** dal menu **Run** per avviare l'app.

    Si noti che tutti gli elementi creati senza un valore timestamp non vengono più visualizzati nell'interfaccia utente.

L'esercitazione relativa all'utilizzo dei dati è stata completata.

## <a name="next-steps"> </a>Passaggi successivi

Dopo avere completato anche questa esercitazione, provare a svolgere l'ultima esercitazione della serie relativa ai dati: [Utilizzo del paging per ridefinire le query][Utilizzo del paging per ridefinire le query].

Gli script server vengono inoltre utilizzati per l'autorizzazione degli utenti e per l'invio di notifiche push. Per ulteriori informazioni, vedere le esercitazioni seguenti:

-   [Autorizzazione di utenti con script][Autorizzazione di utenti con script]
    Informazioni su come filtrare i dati in base all'ID di un utente autenticato.

-   [Introduzione alle notifiche push][Introduzione alle notifiche push]
    Informazioni sull'invio di una notifica push di base all'app.

-   [Riferimento per gli script del server di Servizi mobili][Riferimento per gli script del server di Servizi mobili]
    Ulteriori informazioni sulla registrazione e l'utilizzo di script del server.



  [Aggiunta della convalida della lunghezza della stringa]: #string-length-validation
  [Aggiornamento del client per il supporto della convalida]: #update-client-validation
  [Aggiunta di un timestamp all'inserimento]: #add-timestamp
  [Aggiornamento del client per la visualizzazione del timestamp]: #update-client-timestamp
  [Introduzione ai dati]: /it-it/develop/mobile/tutorials/get-started-with-data-android
  [portale di gestione di Azure]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-android-validate-modify-data-server-scripts/mobile-services-selection.png
  [1]: ./media/mobile-services-android-validate-modify-data-server-scripts/mobile-portal-data-tables.png
  [2]: ./media/mobile-services-android-validate-modify-data-server-scripts/mobile-insert-script-users.png
  [Utilizzo del paging per ridefinire le query]: /it-it/develop/mobile/tutorials/add-paging-to-data-android
  [Autorizzazione di utenti con script]: /it-it/develop/mobile/tutorials/authorize-users-in-scripts-android
  [Introduzione alle notifiche push]: /it-it/develop/mobile/tutorials/get-started-with-push-android
  [Riferimento per gli script del server di Servizi mobili]: http://go.microsoft.com/fwlink/?LinkId=262293
