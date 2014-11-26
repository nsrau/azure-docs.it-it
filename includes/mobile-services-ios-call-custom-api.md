## <a name="update-app"></a><span class="short-header">Aggiornamento dell'app </span>Aggiornamento dell'app per la chiamata all'API personalizzata

1.  Creare un pulsante su cui è possibile fare clic per chiamare l'API personalizzata. Trascinare **Round Rect Button** da **Object Library** nella parte inferiore del riquadro **Utilities** e posizionarlo sotto o accanto al campo di testo. Fare doppio clic per aggiungere il testo **"All"**.

    Verrà aggiunto un nuovo pulsante **"All"**.

2.  Aprire il file di codice **QSTodoService.m**, individuare il metodo `refreshDataOnSuccess` e assicurarsi che contenga il codice seguente:

        - (void)refreshDataOnSuccess:(QSCompletionBlock)completion
        {          
            // Create a predicate that finds items where complete is false
            NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];

            // Query the TodoItem table and update the items property with the results from the service
            [self.table readWithPredicate:predicate completion:^(NSArray *results, NSInteger totalCount, NSError *error)
            {
                [self logErrorIfNotNil:error];

                items = [results mutableCopy];

                // Let the caller know that we finished
                completion();
            }];                                 
        }

    Gli elementi verranno filtrati in modo che quelli completati non vengano restituiti dalla query.

3.  A questo punto, connettere l'oggetto al codice sorgente del controller di visualizzazione. **Premere CTRL e fare clic** sul nuovo pulsante **All**, quindi trascinare il mouse per posizionarsi prima della riga `@end` in **QSTodoListViewController.h**. Connettere l'oggetto a un nuovo oggetto **Action** denominato `onCompleteAll` in **QSTodoListViewController**. Xcode inserisce automaticamente la riga seguente prima della riga `@end`:

           - (IBAction)onCompleteAll:(id)sender;

4.  L'obiettivo di questo metodo `onCompleteAll` è gestire l'evento Click per il nuovo pulsante. Richiama un nuovo metodo `completeAll` che viene aggiunto alla classe personalizzata, che a sua volta invia una richiesta POST alla nuova API personalizzata. Il risultato restituito dall'API personalizzata viene visualizzato in una finestra di dialogo con messaggio, insieme a eventuali errori. Modificare **QSTodoListViewController.m** per aggiungere l'implementazione seguente prima della riga `@end`:

           - (IBAction)onCompleteAll:(id)sender {
            [self.todoService completeAll:^(id result, NSHTTPURLResponse* response, NSError* error)
             {
                 if (error)
                 {
                     NSString* errorMessage = @"There was a problem! ";
                     errorMessage = [errorMessage stringByAppendingString:[error localizedDescription]];
                     UIAlertView* myAlert = [[UIAlertView alloc]
                                             initWithTitle:@"Error!"
                                             message:errorMessage
                                             delegate:nil
                                             cancelButtonTitle:@"Okay"
                                             otherButtonTitles:nil];
                     [myAlert show];
                     [self refresh];
                 } else {
                     NSString* successMessage = [NSString stringWithFormat:@"%d items marked as complete", [[result objectForKey:@"count"] integerValue]];                   
                     UIAlertView* myAlert = [[UIAlertView alloc]
                                             initWithTitle:@"Success!"
                                             message:successMessage
                                             delegate:nil
                                             cancelButtonTitle:@"Okay"
                                             otherButtonTitles:nil];
                     [myAlert show];
                     [self refresh];
                 }
             }];
           }

5.  Si noti che il codice precedente fa riferimento a un nuovo metodo `completeAll` che non è ancora stato definito in **QSTodoService**. Modificare **QSTodoService.h** e aggiungere la riga seguente prima della riga `@end`:

        - (void) completeAll:(MSAPIBlock)completion;

6.  Aggiungere l'implementazione corrispondente di `completeAll` in **QSTodoService.m** prima della riga `@end`. iOS è simile a JavaScript, in quanto non supporta la serializzazione JSON di tipi arbitrari. Di conseguenza, include un'API piuttosto semplice per richiamare anche API personalizzate, costituita dal metodo `invokeAPI`.

        - (void) completeAll:(MSAPIBlock)completion
        {
            [self.client
             invokeAPI:@"completeall"
             body:nil
             HTTPMethod:@"POST"
             parameters:nil
             headers:nil
             completion:completion ];
        }

## <a name="test-app"></a>Test dell'app

1.  In Xcode selezionare un emulatore in cui effettuare la distribuzione (iPhone o iPad), premere il pulsante **Run** (o il tasto **Comando+R**) per ricompilare il progetto e avviare l'app. Verrà eseguito il client di Servizi mobili di Azure, compilato con l'SDK per iOS, e verrà eseguita una query sugli elementi dal servizio mobile.

2.  Immettere del testo nell'apposito campo e quindi fare clic sul pulsante **+**. Un nuovo elemento verrà inviato come inserimento al servizio mobile.

3.  Ripetere il passaggio precedente fino ad aggiungere diversi elementi all'elenco.

4.  Toccare il pulsante **All**. Verrà visualizzata una finestra di avviso indicante il numero di elementi contrassegnati come completati, quindi la query filtrata viene eseguita di nuovo cancellando tutti gli elementi dall'elenco.

    ![][0]

  [0]: ./media/mobile-services-ios-call-custom-api/mobile-custom-api-ios-completed.png
