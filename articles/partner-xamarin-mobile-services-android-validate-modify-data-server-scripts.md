<properties 
	pageTitle="Uso degli script del server per convalidare e modificare i dati (Xamarin Android) | Mobile Developer Center" 
	description="Informazioni su come convalidare e modificare i dati inviati tramite script del server dall'app per Xamarin.Android." 
	documentationCenter="xamarin" 
	services="mobile-services" 
	authors="lindydonna" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-android" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/26/2014" 
	ms.author="donnam"/>

# Convalida e modifica dei dati in Servizi mobili mediante script del server

[AZURE.INCLUDE [mobile-services-selector-validate-modify-data](../includes/mobile-services-selector-validate-modify-data.md)]

Questo argomento illustra come usare gli script del server in Servizi mobili di Azure. Gli script del server vengono registrati in un servizio mobile e possono essere usati per eseguire numerose operazioni sui dati inseriti e aggiornati, incluse la convalida e la modifica dei dati. In questa esercitazione si procederà alla definizione e alla registrazione di script del server per la convalida e la modifica dei dati. Poiché il comportamento degli script sul lato server spesso influisce sul client, si procederà all'aggiornamento dell'app per Android per implementare questi nuovi comportamenti. Il codice compilato è disponibile nell'[app ValidateModifyData][GitHub] di esempio.

In questa esercitazione vengono descritte le operazioni di base seguenti:

1. [Aggiungere la convalida della lunghezza della stringa]
2. [Aggiornare il client per il supporto della convalida]
3. [Aggiungere un timestamp all'inserimento]
4. [Aggiornare il client per la visualizzazione del timestamp]

Questa esercitazione è basata sulle procedure e sull'app di esempio creata nell'esercitazione precedente [Introduzione ai dati]. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione ai dati].  

## <a name="string-length-validation"></a>Aggiungere la convalida

È sempre consigliabile convalidare la lunghezza dei dati inviati dagli utenti. A questo scopo, è innanzitutto necessario registrare uno script che convalida la lunghezza dei dati di stringa inviati al servizio mobile e rifiuta le stringhe troppo lunghe, in questo caso lunghe più di 10 caratteri.

1. Accedere al [portale di gestione di Azure], fare clic su **Servizi mobili** e quindi sull'app. 

	![][0]

2. Fare clic sulla scheda **Dati**, quindi sulla tabella **TodoItem**.

	![][1]

3. Fare clic su **Script**, quindi selezionare l'operazione **Inserisci**.

	![][2]

4. Sostituire lo script esistente con la funzione seguente e quindi fare clic su **Salva**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be 10 characters or less.');
            } else {
                request.execute();
            }
        }

    Questo script verifica la lunghezza della proprietà **text** e invia una risposta di errore quando la lunghezza supera i 10 caratteri. In caso contrario, viene chiamato il metodo **execute** per completare l'operazione di inserimento.

    > [AZURE.TIP] È possibile rimuovere uno script registrato nella scheda **Script** facendo clic su **Cancella**, quindi su **Salva**.

## <a name="update-client-validation"></a>Aggiornare il client

Ora che il servizio mobile convalida dati e invia risposte di errore, è necessario verificare che l'app gestisca correttamente le risposte di errore della convalida.

1. In Xamarin Studio aprire il progetto creato dopo aver completato l'esercitazione [Introduzione ai dati].

2. Nel file TodoActivity.cs individuare il metodo **AddItem** e sostituire la chiamata al metodo CreateAndShowDialog con il codice seguente:

    	var exDetail = ex.InnerException.InnerException as 	
			MobileServiceInvalidOperationException;
    	CreateAndShowDialog(exDetail.Message, "Error");

	Verrà visualizzato il messaggio di errore restituito dal servizio mobile. 

3. Fare clic su **Run** per avviare l'app, quindi digitare un testo di lunghezza superiore a 10 caratteri nella casella di testo e fare clic sul pulsante **Add**.

  	Si noti che l'errore viene gestito e che il messaggio di errore viene visualizzato all'utente.

## <a name="add-timestamp"></a>Aggiungere un timestamp

Nelle attività precedenti è stata eseguita la convalida di un'operazione di inserimento che è stata accettata o rifiutata. Ora, si procederà all'aggiornamento dei dati inseriti usando uno script server che aggiunge una proprietà timestamp all'oggetto prima dell'inserimento.

1. Nella scheda **Script** del [portale di gestione], sostituire lo script **Inserisci** corrente con la funzione seguente, quindi fare clic su **Salva**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                item.createdAt = new Date();
                request.execute();
            }
        }

    Questa funzione aumenta lo script insert precedente tramite l'aggiunta di una nuova proprietà timestamp **createdAt** all'oggetto prima che questo venga inserito dalla chiamata al metodo **request**.**execute**. 

    > [AZURE.IMPORTANT] È necessario abilitare lo schema dinamico la prima volta che viene eseguito lo script insert. Quando è abilitato lo schema dinamico, Servizi mobili aggiunge automaticamente la colonna **createdAt** alla tabella **TodoItem** alla prima esecuzione. Lo schema dinamico è abilitato per impostazione predefinita per un nuovo servizio mobile e deve essere disabilitato prima che l'app venga pubblicata.

2. Nel menu **Run** fare clic su **Run** per avviare l'app, quindi digitare un testo di lunghezza inferiore a 10 caratteri nella casella di testo e fare clic su **Add**.

   	Si noti che il nuovo timestamp non viene visualizzato nell'interfaccia utente dell'app.

3. Tornare al portale di gestione e fare clic sulla scheda **Sfoglia** nella tabella **todoitem**.
   
   	È ora presente una colonna **createdAt** che contiene un valore timestamp per il nuovo elemento inserito.
  
È ora necessario aggiornare l'app per Android affinché visualizzi questa nuova colonna.

## <a name="update-client-timestamp"></a>Aggiornare di nuovo il client

Il client del servizio mobile ignorerà i dati delle risposte che non è in grado di serializzare nelle proprietà del tipo definito. Nell'ultimo passaggio si procederà all'aggiornamento del client affinché visualizzi questi nuovi dati.

1. Aggiungere il codice seguente alle definizioni dei campi privati nella classe **TodoItem**:

        [DataMember(Name = "createdAt")]
        public DateTime? CreatedAt { get; set; }
  
    > [AZURE.NOTE] L'annotazione `DataMember's Name` comunica al client di eseguire il mapping della nuova proprietà `CreatedAt` dell'app alla colonna `createdAt` definita nella tabella TodoItem che presenta un nome diverso. Grazie a questa annotazione, i nomi di proprietà sugli oggetti dell'app possono essere diversi dai nomi di colonna in database SQL. Senza questa annotazione, viene generato un errore dovuto alle differenze di convenzione per l'uso di maiuscole e minuscole.

2. Nel metodo GetView aggiungere il codice seguente immediatamente prima del codice corrente che imposta <code>checkBox.Text</code> su <code>currentItem.Text</code>:

       	string displayDate = "missing";
       	if (currentItem.CreatedAt.HasValue)
       		displayDate = currentItem.CreatedAt.Value.ToShortTimeString();

   	Se esiste un valore di timestamp, verrà creata una stringa di data formattata. 

3. Individuare di nuovo il codice `checkBox.Text = currentItem.Text` e sostituire tale riga con quanto riportato di seguito:

		checkBox.Text = string.Format("{0} - {1}", currentItem.Text, displayDate);

	La data del timestamp verrà aggiunta all'elemento per la visualizzazione.
	
4. Nel menu **Run** fare clic su **Run** per avviare l'app. 

	Si noti che il timestamp è visualizzato solo per gli elementi inseriti dopo l'aggiornamento dello script insert.

5. In **TodoActivity.cs** sostituire la query esistente in **RefreshItemsFromTableAsync** con la query seguente:

		var list = await todoTable.Where(item => item.Complete == false && 
										 item.CreatedAt != null)
								  .ToListAsync();

	Questo metodo aggiorna la query affinché filtri anche gli elementi che non dispongono di un valore timestamp.
	
6. Nel menu **Run** fare clic su **Run** per avviare l'app.

  	Si noti che tutti gli elementi creati senza un valore timestamp non vengono più visualizzati nell'interfaccia utente.

L'esercitazione relativa all'uso dei dati è stata completata.

## <a name="next-steps"></a>Passaggi successivi

Dopo avere completato anche questa esercitazione, provare a svolgere l'ultima esercitazione della serie relativa ai dati: [Uso del paging per ridefinire le query].

Gli script del server vengono inoltre usati per l'autorizzazione degli utenti e per l'invio di notifiche push. Per altre informazioni, vedere le esercitazioni seguenti:

* [Autorizzare gli utenti con gli script]
  <br/>Informazioni su come filtrare i dati in base all'ID di un utente autenticato.

* [Introduzione alle notifiche push] 
  <br/>Informazioni sull'invio di una notifica push di base all'app.

* [Informazioni di riferimento sugli script del server di Servizi mobili]
  <br/>Altre informazioni sulla registrazione e l'uso di script del server.

<!-- Anchors. -->
[Aggiungere la convalida della lunghezza della stringa]: #string-length-validation
[Aggiornare il client per il supporto della convalida]: #update-client-validation
[Aggiungere un timestamp all'inserimento]: #add-timestamp
[Aggiornare il client per la visualizzazione del timestamp]: #update-client-timestamp
[Passaggi successivi]: #next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-android-validate-modify-data-server-scripts/mobile-services-selection.png
[1]: ./media/partner-xamarin-mobile-services-android-validate-modify-data-server-scripts/mobile-portal-data-tables.png
[2]: ./media/partner-xamarin-mobile-services-android-validate-modify-data-server-scripts/mobile-insert-script-users.png



<!-- URLs. -->
[Informazioni di riferimento sugli script del server di Servizi mobili]: http://go.microsoft.com/fwlink/?LinkId=262293
[Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started-xamarin-android
[Autorizzare gli utenti con gli script]: /it-it/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-android
[Uso del paging per ridefinire le query]: /it-it/develop/mobile/tutorials/add-paging-to-data-xamarin-android
[Introduzione ai dati]: /it-it/develop/mobile/tutorials/get-started-with-data-xamarin-android
[Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-xamarin-android
[Introduzione alle notifiche push]: /it-it/develop/mobile/tutorials/get-started-with-push-xamarin-android

[Portale di gestione]: https://manage.windowsazure.com/
[Portale di gestione di Azure]: https://manage.windowsazure.com/
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331330


<!--HONumber=42-->
