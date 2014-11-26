Ora che il servizio mobile è pronto, è possibile aggiornare l'app in modo che gli elementi vengano archiviati in Servizi mobili anziché nella raccolta locale.

1.  Se non lo si è già fatto, installare adesso l'[Mobile Services SDK per iOS][Mobile Services SDK per iOS].

2.  Nella struttura del progetto in Xcode aprire i file TodoService.m e TodoService.h nella cartella Quickstart e aggiungere l'istruzione import seguente

        #import <WindowsAzureMobileServices/WindowsAzureMobileServices.h>  

3.  Nel file ToDoService.h individuare la riga di codice commentata seguente:

        // Create an MSClient property comment in the #interface declaration for the TodoService. 

    Dopo questo commento, aggiungere la riga di codice seguente:

        @property (nonatomic, strong)   MSClient *client;

    Verrà creata una proprietà che rappresenta il client Servizi mobili che si connette al servizio

4.  Nel file TodoService.m individuare la riga di codice commentata seguente:

        // Create an MSTable property for your items. 

    Dopo questo commento, aggiungere la riga di codice seguente all'interno della dichiarazione @interface:

        @property (nonatomic, strong)   MSTable *table;

    Verrà creata una rappresentazione della proprietà per la tabella di Servizi mobili.

5.  Nel portale di gestione fare clic su **Mobile Services** e quindi sul servizio mobile appena creato.

6.  Fare clic sulla scheda **Dashboard** e prendere nota del valore di **Site URL**, quindi fare clic su **Manage keys** e prendere nota del valore di **Application key**.

    ![][ ]

    Questi valori sono necessari per accedere al servizio mobile dal codice dell'app.

7.  In Xcode, aprire il file TodoService.m e individuare la riga di codice commentata seguente:

        // Initialize the Mobile Service client with your URL and key.

    Dopo questo commento, aggiungere la riga di codice seguente:

        self.client = [MSClient clientWithApplicationURLString:@"APPURL" withApplicationKey:@"APPKEY"];

    Verrà creata un'istanza del client di Servizi mobili.

8.  Nel codice sostituire **APPURL** e **APPKEY** con l'URL del servizio mobile e la chiave dell'applicazione del servizio mobile acquisiti nel passaggio 6.

9.  Individuare la riga di codice commentata seguente:

        // Create an MSTable instance to allow us to work with the TodoItem table.

    Dopo questo commento, aggiungere la riga di codice seguente:

        self.table = [self.client getTable:@"TodoItem"];

    Verrà creata un'istanza della tabella TodoItem.

10. Individuare la riga di codice commentata seguente:

        // Create a predicate that finds items where complete is false comment in the refreshDataOnSuccess method. 

    Dopo questo commento, aggiungere la riga di codice seguente:

        NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];

    Verrà creata una query che restituisce tutte le attività non ancora completate.

11. Individuare la riga di codice commentata seguente:

        // Query the TodoItem table and update the items property with the results from the service.

    Sostituire il commento e la chiamata al blocco **completion** successiva con il codice seguente:

        // Query the TodoItem table and update the items property with the results from the service
        [self.table readWhere:predicate completion:^(NSArray *items, NSInteger totalCount, NSError *error)
        {
           self.items = [items mutableCopy];
           completion();
        }]; 

12. Individuare il metodo **addItem** e aggiungere al corpo del metodo il codice seguente:

        // Insert the item into the TodoItem table and add to the items array on completion
        [self.table insert:item completion:^(NSDictionary *result, NSError *error) {
            NSUInteger index = [items count];
            [(NSMutableArray *)items insertObject:item atIndex:index];

            // Let the caller know that we finished
            completion(index);
        }];

    Il codice invia una richiesta insert al servizio mobile.

13. Individuare il metodo **completeItem** e aggiungere al corpo del metodo il codice seguente:

        // Update the item in the TodoItem table and remove from the items array on completion
        [self.table update:mutable completion:^(NSDictionary *item, NSError *error) {

            // TODO
            // Get a fresh index in case the list has changed
            NSUInteger index = [items indexOfObjectIdenticalTo:mutable];

            [mutableItems removeObjectAtIndex:index];

            // Let the caller know that we have finished
            completion(index);
        }]; 

    Il codice rimuove gli elementi TodoItems dopo che sono stati contrassegnati come completati.

Una volta aggiornata l'app per consentire l'utilizzo di Servizi mobili per l'archiviazione back-end, è possibile verificarne il funzionamento in Servizi mobili.

  [Mobile Services SDK per iOS]: https://go.microsoft.com/fwLink/p/?LinkID=266533
  [ ]: ./media/mobile-services-ios-enable-mobile-service-access/mobile-dashboard-tab.png
