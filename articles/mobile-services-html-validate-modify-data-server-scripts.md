<properties linkid="develop-mobile-tutorials-validate-modify-and-augment-data-html" urlDisplayName="Validate Data - HTML5" pageTitle="User server scripts to validate and modify data (HTML 5) | Mobile Dev Center" metaKeywords="" description="Learn how to validate and modify data sent using server scripts from your HTML app." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Convalida e modifica dei dati in Servizi mobili mediante script del server

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/it-it/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet" title="Windows Store C#">Windows Store C#</a><a href="/it-it/develop/mobile/tutorials/validate-modify-and-augment-data-js" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/it-it/develop/mobile/tutorials/validate-modify-and-augment-data-wp8" title="Windows Phone">Windows Phone</a><a href="/it-it/develop/mobile/tutorials/validate-modify-and-augment-data-ios" title="iOS">iOS</a><a href="/it-it/develop/mobile/tutorials/validate-modify-and-augment-data-android" title="Android">Android</a><a href="/it-it/develop/mobile/tutorials/validate-modify-and-augment-data-html" title="HTML" class="current">HTML</a><a href="/it-it/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/it-it/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android" title="Xamarin.Android" class="current">Xamarin.Android</a>
</div>

In questo argomento viene illustrato come utilizzare gli script del server in Servizi mobili di Azure. Gli script del server vengono registrati in un servizio mobile e possono essere utilizzati per eseguire numerose operazioni sui dati inseriti e aggiornati, incluse la convalida e la modifica dei dati. In questa esercitazione si procederà alla definizione e alla registrazione di script del server per la convalida e la modifica dei dati. Poiché il comportamento degli script sul lato server spesso influisce sul client, si procederà quindi all'aggiornamento dell'app HTML per implementare questi nuovi comportamenti.

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
                request.respond(statusCodes.BAD_REQUEST, {
                    error: "Text cannot exceed 10 characters"
                });
            } else {
                request.execute();
            }
        }

    Questo script verifica la lunghezza della proprietà **TodoItem.text** e invia una risposta di errore quando la lunghezza supera i 10 caratteri. In caso contrario, viene chiamata la funzione **execute** per completare l'operazione di inserimento.

    <div class="dev-callout"> 
<b>Nota</b> 
<p>&Egrave; possibile rimuovere uno script registrato nella scheda <strong>Script</strong> facendo clic su <strong>Clear</strong> e quindi su <strong>Save</strong>.</p></div>

## <a name="update-client-validation"></a>Aggiornamento del client

Ora che il servizio mobile convalida dati e invia risposte di errore, è necessario aggiornare l'app .NET affinché sia in grado di gestire le risposte di errore dalla convalida.

1.  Eseguire uno dei seguenti file di comando dalla sottocartella **server** del progetto modificato al termine dell'esercitazione [Introduzione ai dati][Introduzione ai dati].

    -   **launch-windows** (computer Windows)
    -   **launch-mac.command** (computer Mac OS X)
    -   **launch-linux.sh** (computer Linux)

    <div class="dev-callout"><b>Nota</b>
    <p>In un computer Windows, digitare 'R' quando PowerShell chiede di confermare che si desidera eseguire lo script. Il Web browser potrebbe visualizzare un avviso in cui si consiglia di non eseguire lo script in quanto scaricato da Internet. In questo caso, &egrave; necessario richiedere che il browser proceda nel caricamento dello script.</p>
</div>

    Verrà quindi avviato un server Web nel computer locale per ospitare l'app.

2.  Aprire il file app.js, quindi sostituire il gestore di eventi **$('\#add-item').submit()** con il codice seguente:

        $('#add-item').submit(function(evt) {
            var textbox = $('#new-item-text'),
                itemText = textbox.val();
            if (itemText !== '') {
                todoItemTable.insert({ text: itemText, complete: false })
                    .then(refreshTodoItems, function(error){
                    alert(JSON.parse(error.request.responseText).error);
                });
            }
            textbox.val('').focus();
            evt.preventDefault();
        });

3.  In un Web browser, passare a <http://localhost:8000/>, quindi digitare un testo in **Add new task** e fare clic su **Add**.

    Si noti che l'operazione ha esito negativo e il gestore degli errori visualizza la risposta di errore in una finestra di dialogo.

## <a name="add-timestamp"></a>Aggiunta di un timestamp

Nelle attività precedenti si è proceduto alla convalida di un'operazione di inserimento che è accettata o rifiutata. Ora, si procederà all'aggiornamento dei dati inseriti utilizzando uno script server che aggiunge una proprietà timestamp all'oggetto prima dell'inserimento.

<div class="dev-callout"><b>Nota</b>
<p>La propriet&agrave; timestamp <b>createdAt</b> illustrata in questo esempio &egrave; ora ridondante. Servizi mobili crea automaticamente una propriet&agrave; di sistema <b>__createdAt</b> per ogni tabella.</p>
</div>

1.  Nella scheda **Scripts** del [portale di gestione][portale di gestione di Azure] sostituire lo script **Insert** corrente con la funzione seguente, quindi fare clic su **Save**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, {
                    error: 'Text length must be under 10'
                });
            } else {
                item.createdAt = new Date();
                request.execute();
            }
        }

    Questa funzione aumenta lo script insert precedente tramite l'aggiunta di una nuova proprietà timestamp **createdAt** all'oggetto prima che questo venga inserito dalla chiamata al metodo **request**.**execute**.

    <div class="dev-callout"><b>Nota</b>
<p>&Egrave; necessario abilitare lo schema dinamico la prima volta che viene eseguito lo script insert. Quando &egrave; abilitato lo schema dinamico, Servizi mobili aggiunge automaticamente la colonna <strong>createdAt</strong> alla tabella <strong>TodoItem</strong> alla prima esecuzione. Lo schema dinamico &egrave; abilitato per impostazione predefinita per un nuovo servizio mobile e deve essere disabilitato prima che l'app venga pubblicata.</p>
</div>

2.  Ricaricare la pagina nel Web browser, quindi digitare un testo (non superiore ai 10 caratteri) in **Add new task** e fare clic su **Add**.

    Si noti che il nuovo timestamp non viene visualizzato nell'interfaccia utente dell'app.

3.  Tornare al portale di gestione e fare clic sulla scheda **Browse** nella tabella **todoitem**.

    È ora presente una colonna **createdAt** che contiene un valore timestamp per il nuovo elemento inserito.

È ora necessario aggiornare l'app affinché visualizzi questa nuova colonna.

## <a name="update-client-timestamp"></a>Nuovo aggiornamento del client

Il client del servizio mobile ignorerà i dati delle risposte che non è in grado di serializzare nelle proprietà del tipo definito. Nell'ultimo passaggio si procederà all'aggiornamento del client affinché visualizzi questi nuovi dati.

1.  Aprire il file app.js nell'edito, quindi sostituire la funzione **refreshTodoItems** con il codice seguente:

        function refreshTodoItems() {
            var query = todoItemTable.where(function () {
                return (this.complete === false && this.createdAt !== null);
            });

            query.read().then(function(todoItems) {
                var listItems = $.map(todoItems, function(item) {
                    return $('<li>')
                        .attr('data-todoitem-id', item.id)
                        .append($('<button class="item-delete">Delete</button>'))
                        .append($('<input type="checkbox" class="item-complete">')
                            .prop('checked', item.complete))
                        .append($('<div>').append($('<input class="item-text">').val(item.text))
                        .append($('<span class="timestamp">' 
                            + (item.createdAt && item.createdAt.toDateString() + ' '
                            + item.createdAt.toLocaleTimeString() || '') 
                            + '</span>')));

                });

                $('#todo-items').empty().append(listItems).toggle(listItems.length > 0);
                $('#summary').html('<strong>' + todoItems.length + '</strong> item(s)');
            });
        }

    Viene visualizzata la parte della data della nuova proprietà **createdAt**.

2.  Aprire il file style.css nell'editor e sostituire gli stili nella classe `item-text` con i seguenti:

        .item-text { width: 70%; height: 26px; line-height: 24px; 
            border: 1px solid transparent; background-color: transparent; }
        .timestamp { width: 30%; height: 40px; font-size: .75em; }

    La casella di testo viene ridimensionata e gli stili applicati al testo del nuovo timestamp.

3.  Ricaricare la pagina.

    Si noti che il timestamp è visualizzato solo per gli elementi inseriti dopo l'aggiornamento dello script insert.

4.  Di nuovo nella funzione **refreshTodoItems** sostituire la riga di codice che definisce la query con il codice seguente:

         var query = todoItemTable.where(function () {
                return (this.complete === false && this.createdAt !== null);
            });

    Questa funzione aggiorna la query affinché filtri anche gli elementi che non dispongono di un valore timestamp.

5.  Ricaricare la pagina.

    Si noti che tutti gli elementi creati senza un valore timestamp non vengono più visualizzati nell'interfaccia utente.

L'esercitazione relativa all'utilizzo dei dati è stata completata.

## <a name="next-steps"> </a>Passaggi successivi

Dopo avere completato anche questa esercitazione, provare a svolgere l'ultima esercitazione della serie relativa ai dati: [Utilizzo del paging per ridefinire le query][Utilizzo del paging per ridefinire le query].

Per ulteriori informazioni, vedere [Utilizzo degli script del server in Servizi mobili][Utilizzo degli script del server in Servizi mobili] e [Riferimento per i concetti e le procedure di Servizi mobili con HTML/JavaScript][Riferimento per i concetti e le procedure di Servizi mobili con HTML/JavaScript]



  [Aggiunta della convalida della lunghezza della stringa]: #string-length-validation
  [Aggiornamento del client per il supporto della convalida]: #update-client-validation
  [Aggiunta di un timestamp all'inserimento]: #add-timestamp
  [Aggiornamento del client per la visualizzazione del timestamp]: #update-client-timestamp
  [Introduzione ai dati]: /it-it/develop/mobile/tutorials/get-started-with-data-html
  [portale di gestione di Azure]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-html-validate-modify-data-server-scripts/mobile-services-selection.png
  [1]: ./media/mobile-services-html-validate-modify-data-server-scripts/mobile-portal-data-tables.png
  [2]: ./media/mobile-services-html-validate-modify-data-server-scripts/mobile-insert-script-users.png
  [Utilizzo del paging per ridefinire le query]: /it-it/develop/mobile/tutorials/add-paging-to-data-html
  [Utilizzo degli script del server in Servizi mobili]: /it-it/develop/mobile/how-to-guides/work-with-server-scripts
  [Riferimento per i concetti e le procedure di Servizi mobili con HTML/JavaScript]: /it-it/develop/mobile/how-to-guides/work-with-html-js-client
