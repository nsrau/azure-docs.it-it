<properties
	pageTitle="Usare gli script del server per convalidare e modificare i dati (Xamarin iOS) | Mobile Dev Center"
	description="Informazioni su come convalidare e modificare i dati inviati tramite script del server dall'app per Xamarin iOS."
	services="mobile-services"
	documentationCenter="xamarin"
	authors="lindydonna"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm=""
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/26/2014"
	ms.author="donnam"/>

# Convalidare e modificare i dati in Servizi mobili mediante script del server

[AZURE.INCLUDE [mobile-services-selector-validate-modify-data](../includes/mobile-services-selector-validate-modify-data.md)]

Questo argomento descrive come usare gli script del server in Servizi mobili di Azure. Gli script del server vengono registrati in un servizio mobile e possono essere usati per eseguire numerose operazioni sui dati inseriti e aggiornati, incluse la convalida e la modifica dei dati. In questa esercitazione si procederà alla definizione e alla registrazione di script del server per la convalida e la modifica dei dati. Poiché il comportamento degli script sul lato server spesso influisce sul client, si procederà quindi all'aggiornamento dell'app per iOS per implementare questi nuovi comportamenti. Il codice compilato è disponibile nell'esempio di [ValidateModifyData app][GitHub].

Questa esercitazione spiega come eseguire le operazioni di base seguenti:

1. [Aggiungere la convalida della lunghezza della stringa]
2. [Aggiornare il client per il supporto della convalida]
3. [Aggiungere un timestamp all'inserimento]
4. [Aggiornare il client per la visualizzazione del timestamp]

Questa esercitazione è basata sulle procedure e sull'app di esempio creata nell'esercitazione precedente [Introduzione ai dati]. Prima di iniziare questa esercitazione è necessario completare l'esercitazione [Introduzione ai dati].  

## <a name="string-length-validation"></a>Aggiungere la convalida

È sempre consigliabile convalidare la lunghezza dei dati inviati dagli utenti. A questo scopo, è innanzitutto necessario registrare uno script che convalida la lunghezza dei dati di stringa inviati al servizio mobile e rifiuta le stringhe troppo lunghe, in questo caso lunghe più di 10 caratteri.

1. Accedere al [portale di gestione di Azure], fare clic su **Servizi mobili** e quindi sull'app.

	![][0]

2. Fare clic sulla scheda **Dati** e quindi sulla tabella **TodoItem**.

   	![][1]

3. Fare clic su **Script**, quindi selezionare l'operazione **Inserisci**.

   	![][2]

4. Sostituire lo script esistente con la seguente funzione e quindi fare clic su **Salva**.

				function insert(item, user, request) {
        	if (item.text.length > 10) {
            request.respond(statusCodes.BAD_REQUEST, 'Text length must be 10 characters or less.');
          } else {
            request.execute();
          }
        }

    Questo script verifica la lunghezza della proprietà **text** e invia una risposta di errore quando la lunghezza supera i 10 caratteri. In caso contrario, viene chiamato il metodo **execute** per completare l'operazione di inserimento.

    > [AZURE.NOTE] È possibile rimuovere uno script registrato nella scheda **Script** facendo clic su **Cancella** e quindi su **Salva**.

## <a name="update-client-validation"></a>Aggiornare il client

Ora che il servizio mobile convalida dati e invia risposte di errore, è necessario aggiornare l'app .NET affinché sia in grado di gestire le risposte di errore dalla convalida.

1. In Xamarin Studio aprire il progetto modificato durante l'esercitazione [Introduzione ai dati].

2. Premere il pulsante **Run** per compilare il progetto e avviare l'app, quindi immettere un testo più lungo di 10 caratteri nella casella di testo e fare clic sull'icona con il segno più (**+**).

	Si noti che l'app genera un errore non gestito a seguito della risposta 400 (Richiesta non valida) restituita dal servizio mobile.

3. Nel file TodoService.cs individuare la gestione delle eccezioni <code>try/catch</code> corrente nel metodo **InsertTodoItemAsync**, quindi sostituire la parte relativa a <code>catch</code> con:

    	catch (Exception ex) {
        var exDetail = (ex.InnerException.InnerException as MobileServiceInvalidOperationException);
        Console.WriteLine(exDetail.Message);

        UIAlertView alert = new UIAlertView() {
            	Title = "Error",
            	Message = exDetail.Message
        } ;
        alert.AddButton("Ok");
        alert.Show();

        return -1;
		}

	Ciò determina l'apertura di una finestra popup in cui è visualizzato l'errore.

4. Individuare il metodo **OnAdd** in **TodoListViewController.cs**. Aggiornare il metodo per assicurarsi che il valore <code>index</code> restituito non sia <code>-1</code> come restituito nella gestione eventi in **InsertTodoItemAsync**. In questo caso non verrà aggiunta una nuova riga a <code>TableView</code>.

    	if (index != -1) {
        TableView.InsertRows(new [] { NSIndexPath.FromItemSection(index, 0) },
            UITableViewRowAnimation.Top);
        itemText.Text = "";
    	}


5. Ricompilare e avviare l'app.

	![][4]

	Si noti che l'errore viene gestito e che il messaggio di errore viene visualizzato all'utente.


## <a name="next-steps"> </a>Passaggi successivi

Dopo avere completato anche questa esercitazione, provare a svolgere l'ultima esercitazione della serie relativa ai dati: [Usare il paging per ridefinire le query].

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
[0]: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-services-selection.png
[1]: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-portal-data-tables.png
[2]: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-insert-script-users.png

[4]: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-quickstart-data-error-ios.png

<!-- URLs. -->
[Informazioni di riferimento sugli script del server di Servizi mobili]: http://go.microsoft.com/fwlink/?LinkId=262293
[Introduzione a Servizi mobili]: /develop/mobile/tutorials/get-started-xamarin-ios
[Autorizzare gli utenti con gli script]: /develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios
[Usare il paging per ridefinire le query]: /develop/mobile/tutorials/add-paging-to-data-xamarin-ios
[Introduzione ai dati]: /develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Introduzione all'autenticazione]: /develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Introduzione alle notifiche push]: /develop/mobile/tutorials/get-started-with-push-xamarin-ios

[Portale di gestione]: https://manage.windowsazure.com/
[Portale di gestione di Azure]: https://manage.windowsazure.com/
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331330


<!--HONumber=52-->