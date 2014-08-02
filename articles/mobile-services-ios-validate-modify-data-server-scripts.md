<properties linkid="develop-mobile-tutorials-validate-modify-and-augment-data-ios" urlDisplayName="Validate Data" pageTitle="Use server scripts to validate and modify data (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to validate and modify data sent using server scripts from your iOS app." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="" solutions="" manager="" editor="" />

Convalida e modifica dei dati in Servizi mobili mediante script del server
==========================================================================

[Windows Store C\#](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet "Windows Store C#")[Windows Store JavaScript](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-js "Windows Store JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-ios "iOS")[Android](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-android "Android")[HTML](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android "Xamarin.Android")

In questo argomento viene illustrato come utilizzare gli script del server in Servizi mobili di Azure. Gli script del server vengono registrati in un servizio mobile e possono essere utilizzati per eseguire numerose operazioni sui dati inseriti e aggiornati, incluse la convalida e la modifica dei dati. In questa esercitazione si procederà alla definizione e alla registrazione di script del server per la convalida e la modifica dei dati. Poiché il comportamento degli script sul lato server spesso influisce sul client, si procederà quindi all'aggiornamento dell'app per iOS per implementare questi nuovi comportamenti.

In questa esercitazione vengono descritte le operazioni di base seguenti:

1.  [Aggiunta della convalida della lunghezza della stringa](#string-length-validation)
2.  [Aggiornamento del client per il supporto della convalida](#update-client-validation) &lt;!--3. [Aggiunta di un timestamp all'inserimento](#add-timestamp)
3.  [Aggiornamento del client per la visualizzazione del timestamp](#update-client-timestamp)--\>

Questa esercitazione è basata sulle procedure e sull'app di esempio creata nell'esercitazione precedente [Introduzione ai dati](/en-us/develop/mobile/tutorials/get-started-with-data-ios). Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione ai dati](/en-us/develop/mobile/tutorials/get-started-with-data-ios).

Aggiunta della convalida
------------------------

È sempre consigliabile convalidare la lunghezza dei dati inviati dagli utenti. A questo scopo, è innanzitutto necessario registrare uno script che convalida la lunghezza dei dati di stringa inviati al servizio mobile e rifiuta le stringhe troppo lunghe, in questo caso lunghe più di 10 caratteri.

1.  Accedere al [portale di gestione di Azure](https://manage.windowsazure.com/), fare clic su **Mobile Services** e quindi sull'app.

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

    **Nota**

    È possibile rimuovere uno script registrato nella scheda **Script** facendo clic su **Clear** e quindi su **Save**.

Aggiornamento del client
------------------------

Ora che il servizio mobile convalida dati e invia risposte di errore, è necessario aggiornare l'app .NET affinché sia in grado di gestire le risposte di errore dalla convalida.

1.  In Xcode aprire il progetto modificato dopo aver completato l'esercitazione [Introduzione ai dati](/en-us/develop/mobile/tutorials/get-started-with-data-ios).

2.  Premere il pulsante **Run** (Comando + R) per compilare il progetto e avviare l'app, quindi immettere un testo più lungo di 10 caratteri nella casella di testo e fare clic sull'icona con il segno più (**+**).

   	Si noti che l'app genera un errore non gestito a seguito della risposta 400 (Richiesta non valida) restituita dal servizio mobile.   

3.  Nel file QSTodoService.m individuare la riga di codice seguente nel metodo **addItem**:

         [self logErrorIfNotNil:error]; 

   	Dopo questa riga, sostituire la parte rimanente del blocco di completamento con il codice seguente:

         BOOL goodRequest = !((error) && (error.code == MSErrorMessageErrorCode));

         // detect text validation error from service.
         if (goodRequest) // The service responded appropriately
         {
             NSUInteger index = [items count];
             [(NSMutableArray *)items insertObject:result atIndex:index];
            
             // Let the caller know that we finished
             completion(index);
         }
         else{
            
             // if there's an error that came from the service
             // log it, and popup up the returned string.
             if (error && error.code == MSErrorMessageErrorCode) {
                 NSLog(@"ERROR %@", error);
                 UIAlertView *av =
                 [[UIAlertView alloc]
                  initWithTitle:@"Request Failed"
                  message:error.localizedDescription
                  delegate:nil
                  cancelButtonTitle:@"OK"
                  otherButtonTitles:nil
                  ];
                 [av show];
             }
         }

   	L'errore verrà registrato nella finestra di output e visualizzato all'utente. 

4.  Ricompilare e avviare l'app.

   	![][4]

Si noti che l'errore viene gestito e che il messaggio di errore viene visualizzato all'utente.

Passaggi successivi
-------------------

Dopo avere completato anche questa esercitazione, provare a svolgere l'ultima esercitazione della serie relativa ai dati: [Utilizzo del paging per ridefinire le query](/en-us/develop/mobile/tutorials/add-paging-to-data-ios).

Gli script server vengono inoltre utilizzati per l'autorizzazione degli utenti e per l'invio di notifiche push. Per ulteriori informazioni, vedere le esercitazioni seguenti:

-   [Autorizzazione di utenti con script](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-ios)

    Informazioni su come filtrare i dati in base all'ID di un utente autenticato.

-   [Introduzione alle notifiche push](/en-us/develop/mobile/tutorials/get-started-with-push-ios)

    Informazioni sull'invio di una notifica push di base all'app.

-   [Riferimento per gli script del server di Servizi mobili](http://go.microsoft.com/fwlink/?LinkId=262293)

    Ulteriori informazioni sulla registrazione e l'utilizzo di script del server.

<!-- Images. -->
[0]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-insert-script-users.png

[4]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-quickstart-data-error-ios.png
