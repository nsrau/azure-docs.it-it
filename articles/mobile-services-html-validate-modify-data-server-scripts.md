<properties 
	pageTitle="Usare gli script del server per convalidare e modificare i dati (HTML 5) | Mobile Dev Center" 
	description="Informazioni su come convalidare e modificare i dati inviati tramite script del server dall'app HTML." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-html" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="09/26/2014" 
	ms.author="glenga"/>

# Convalidare e modificare i dati in Servizi mobili mediante script del server 

[AZURE.INCLUDE [mobile-services-selector-validate-modify-data](../includes/mobile-services-selector-validate-modify-data.md)]

Questo argomento descrive come usare gli script del server in Servizi mobili di Azure. Gli script del server vengono registrati in un servizio mobile e possono essere usati per eseguire numerose operazioni sui dati inseriti e aggiornati, incluse la convalida e la modifica dei dati. In questa esercitazione si procederà alla definizione e alla registrazione di script del server per la convalida e la modifica dei dati. Poiché il comportamento degli script sul lato server spesso influisce sul client, si procederà quindi all'aggiornamento dell'app HTML per implementare questi nuovi comportamenti.

Questa esercitazione spiega come eseguire le operazioni di base seguenti:

1. [Aggiungere la convalida della lunghezza della stringa]
2. [Aggiornare il client per il supporto della convalida]
3. [Aggiungere un timestamp all'inserimento]
4. [Aggiornare il client per la visualizzazione del timestamp]

Questa esercitazione è basata sulle procedure e sull'app di esempio creata nell'esercitazione precedente [Introduzione ai dati]. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione ai dati].  

## <a name="string-length-validation"></a>Aggiungere la convalida

È sempre consigliabile convalidare la lunghezza dei dati inviati dagli utenti. A questo scopo, è innanzitutto necessario registrare uno script che convalida la lunghezza dei dati di stringa inviati al servizio mobile e rifiuta le stringhe troppo lunghe, in questo caso lunghe più di 10 caratteri.

1. Accedere al [portale di gestione di Azure], fare clic su **Servizi mobili** e quindi sull'app. 

   	![][0]

2. Fare clic sulla scheda **Dati** quindi sulla tabella **TodoItem**.

   	![][1]

3. Fare clic su **Script**, quindi selezionare l'operazione **Inserisci**.

   	![][2]

4. Sostituire lo script esistente con la funzione seguente e quindi fare clic su **Salva**.

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

    > [AZURE.TIP] È possibile rimuovere uno script registrato nella scheda **Script** facendo clic su **Cancella** e quindi su **Salva**.	

## <a name="update-client-validation"></a>Aggiornare il client

Ora che il servizio mobile convalida dati e invia risposte di errore, è necessario aggiornare l'app .NET affinché sia in grado di gestire le risposte di errore dalla convalida.

1. Eseguire uno dei seguenti file di comando dalla sottocartella **server** del progetto modificato al termine dell'esercitazione [Introduzione ai dati].

	+ **launch-windows** (computer Windows) 
	+ **launch-mac.command** (computer Mac OS X)
	+ **launch-linux.sh** (computer Linux)

	> [AZURE.NOTE] In un computer Windows digitare `R` quando PowerShell chiede di confermare che si vuole eseguire lo script. Il Web browser potrebbe visualizzare un avviso in cui si consiglia di non eseguire lo script in quanto scaricato da Internet. In questo caso, è necessario richiedere che il browser proceda nel caricamento dello script.

	Verrà quindi avviato un server Web nel computer locale per ospitare l'app.

1. 	Aprire il file app.js, quindi sostituire il gestore eventi **$('#add-item').submit()** con il codice seguente:

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

2. In un Web browser, passare a <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a>, quindi digitare un testo in **Add new task** e fare clic su **Add**.

   	Si noti che l'operazione ha esito negativo e il gestore degli errori visualizza la risposta di errore in una finestra di dialogo.

## <a name="add-timestamp"></a>Aggiungere un timestamp

Nelle attività precedenti è stata eseguita la convalida di un'operazione di inserimento che è stata quindi accettata o rifiutata. Ora, si procederà all'aggiornamento dei dati inseriti usando uno script server che aggiunge una proprietà timestamp all'oggetto prima dell'inserimento.

> [AZURE.NOTE] La proprietà timestamp **createdAt** illustrata in questo esempio è ora ridondante. Servizi mobili crea automaticamente una proprietà di sistema **__createdAt** per ogni tabella.

1. Nella scheda **Script** del [portale di gestione] sostituire lo script **Insert** corrente con la funzione seguente, quindi fare clic su **Salva**.

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

    Questa funzione estende lo script insert precedente aggiungendo una nuova proprietà timestamp **createdAt** all'oggetto prima che questo venga inserito dalla chiamata al metodo **request**.**execute**. 

    > [AZURE.IMPORTANT] È necessario abilitare lo schema dinamico la prima volta che viene eseguito lo script insert. Quando è abilitato lo schema dinamico, Servizi mobili aggiunge automaticamente la colonna **createdAt** alla tabella **TodoItem** alla prima esecuzione. Lo schema dinamico è abilitato per impostazione predefinita per un nuovo servizio mobile e deve essere disabilitato prima che l'app venga pubblicata.

2. Ricaricare la pagina nel Web browser, quindi digitare un testo (non superiore ai 10 caratteri) in **Add new task** e fare clic su **Add**.

   	Si noti che il nuovo timestamp non viene visualizzato nell'interfaccia utente dell'app.

3. Tornare al portale di gestione e fare clic sulla scheda **Sfoglia** nella tabella **todoitem**.
   
   	È ora presente una colonna **createdAt** che contiene un valore timestamp per il nuovo elemento inserito.
  
È ora necessario aggiornare l'app affinché visualizzi questa nuova colonna.

## <a name="update-client-timestamp"></a>Aggiornare di nuovo il client

Il client del servizio mobile ignorerà i dati delle risposte che non è in grado di serializzare nelle proprietà del tipo definito. Nell'ultimo passaggio si procederà all'aggiornamento del client affinché visualizzi questi nuovi dati.

1. Aprire il file app.js nell'editor, quindi sostituire la funzione **refreshTodoItems** con il codice seguente:

		function refreshTodoItems() {
			var query = todoItemTable.where(function () {
                return (this.complete === false);
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

				$('#todo-items').empty().append(listItems).toggle(listIte
	ms.length > 0);
				$('#summary').html('<strong>' + todoIte
	ms.length + '</strong> item(s)');
			});
		}

   	Viene visualizzata la parte della data della nuova proprietà **createdAt**. 

2. Aprire il file style.css nell'editor e sostituire gli stili nella classe  `item-text` con i seguenti:

		.item-text { width: 70%; height: 26px; line-height: 24px; 
			border: 1px solid transparent; background-color: transparent; }
		.timestamp { width: 30%; height: 40px; font-size: .75em; }

	La casella di testo viene ridimensionata e gli stili applicati al testo del nuovo timestamp.
	
6. Ricaricare la pagina. 	

   	Si noti che il timestamp è visualizzato solo per gli elementi inseriti dopo l'aggiornamento dello script insert.

7. Di nuovo nella funzione **refreshTodoItems** sostituire la riga di codice che definisce la query con il codice seguente:

         var query = todoItemTable.where(function () {
                return (this.complete === false && this.createdAt !== null);
            });

   	Questa funzione aggiorna la query affinché filtri anche gli elementi che non dispongono di un valore timestamp.
	
8. Ricaricare la pagina.

   	Si noti che tutti gli elementi creati senza un valore timestamp non vengono più visualizzati nell'interfaccia utente.

L'esercitazione relativa all'uso dei dati è stata completata.

## <a name="next-steps"> </a>Passaggi successivi

Dopo avere completato anche questa esercitazione, provare a svolgere l'ultima esercitazione della serie relativa ai dati: [Usare il paging per ridefinire le query].

Per altre informazioni, vedere [Uso degli script del server in Servizi mobili] e [Riferimento per i concetti e le procedure di Servizi mobili con HTML/JavaScript]


<!-- Anchors. -->
[Aggiungere la convalida della lunghezza della stringa]: #string-length-validation
[Aggiornare il client per il supporto della convalida]: #update-client-validation
[Aggiungere un timestamp all'inserimento]: #add-timestamp
[Aggiornare il client per la visualizzazione del timestamp]: #update-client-timestamp
[Passaggi successivi]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-html-validate-modify-data-server-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-html-validate-modify-data-server-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-html-validate-modify-data-server-scripts/mobile-insert-script-users.png


<!-- URLs. -->
[Uso degli script del server]: /it-it/develop/mobile/how-to-guides/work-with-server-scripts
[Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started-html
[Autorizzare gli utenti con gli script]: /it-it/develop/mobile/tutorials/authorize-users-html
[Usare il paging per ridefinire le query]: /it-it/develop/mobile/tutorials/add-paging-to-data-html
[Introduzione ai dati]: /it-it/develop/mobile/tutorials/get-started-with-data-html
[Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-html

[Portale di gestione]: https://manage.windowsazure.com/
[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Riferimento per i concetti e le procedure di Servizi mobili con HTML/JavaScript]: /it-it/develop/mobile/how-to-guides/work-with-html-js-client



<!--HONumber=42-->
