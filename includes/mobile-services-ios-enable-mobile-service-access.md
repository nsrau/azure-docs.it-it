
Ora che il servizio mobile è pronto, è possibile aggiornare l'app in modo che gli elementi vengano archiviati in Servizi mobili anziché nella raccolta locale.

1. Se non lo si è già fatto, installare adesso [Mobile Services iOS SDK](https://go.microsoft.com/fwLink/p/?LinkID=266533). Una volta installato, copiare la directory WindowsAzureMobileServices.framework sovrascrivendo la directory WindowsAzureMobileServices.framework inclusa nel progetto scaricato. In questo modo si sarà certi di usare l'SDK di Servizi mobili di Azure più recente.

2. Nel file TodoService.h individuare la riga di codice commentata seguente:

        // TODO - create an MSClient proeprty

   	Dopo questo commento, aggiungere la riga di codice seguente:

        @property (nonatomic, strong)   MSClient *client;

   Verrà creata una proprietà che rappresenta il client Servizi mobili che si connette al servizio

3. Nel file TodoService.m individuare la riga di codice commentata seguente:

        // TODO - create an MSTable property for your items

   Dopo questo commento, aggiungere la riga di codice seguente all'interno della dichiarazione @interface:

        @property (nonatomic, strong)   MSTable *table;

   Verrà creata una rappresentazione della proprietà per la tabella di Servizi mobili.

4. Nel portale di gestione fare clic su **Servizi mobili** e quindi sul servizio mobile appena creato.

5. Fare clic sulla scheda **Dashboard** e prendere nota del valore di **URL sito**, quindi fare clic su **Gestisci chiavi** e prendere nota del valore di **Chiave applicazione**.

   	![](./media/mobile-services-ios-enable-mobile-service-access/mobile-dashboard-tab.png)

  	Questi valori sono necessari per accedere al servizio mobile dal codice dell'app.

6. In Xcode, aprire il file TodoService.m e individuare la riga di codice commentata seguente:

        // Initialize the Mobile Service client with your URL and key.

    Dopo questo commento, aggiungere la riga di codice seguente:

        self.client = [MSClient clientWithApplicationURLString:@"APPURL" applicationKey:@"APPKEY"];

    Verrà creata un'istanza del client di Servizi mobili.

7. Nel codice sostituire **APPURL** e **APPKEY** con l'URL del servizio mobile e la chiave dell'applicazione del servizio mobile acquisiti nel passaggio 6.

8. Individuare la riga di codice commentata seguente:

        // Create an MSTable instance to allow us to work with the TodoItem table.

    Dopo questo commento, aggiungere la riga di codice seguente:

        self.table = [self.client tableWithName:@"TodoItem"];

    Verrà creata un'istanza della tabella TodoItem.

9. Individuare la riga di codice commentata seguente:

 	    // Create a predicate that finds items where complete is false

    Dopo questo commento, aggiungere la riga di codice seguente:

        NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];

    Verrà creata una query che restituisce tutte le attività non ancora completate.

10. Individuare la riga di codice commentata seguente:

        // Query the TodoItem table and update the items property with the results from the service

   	Sostituire il commento e la chiamata al blocco **completion** successiva con il codice seguente:

        [self.table readWhere:predicate completion:^(NSArray *results, NSInteger totalCount, NSError *error)
		{
		   self.items = [results mutableCopy];
           completion();
        }];

11. Individuare il metodo **addItem** e sostituire il corpo del metodo con il codice seguente:

        // Insert the item into the TodoItem table and add to the items array on completion
        [self.table insert:item completion:^(NSDictionary *result, NSError *error) {
            NSUInteger index = [items count];
            [(NSMutableArray *)items insertObject:item atIndex:index];

            // Let the caller know that we finished
            completion(index);
        }];

    Il codice invia una richiesta insert al servizio mobile.

12. Individuare il metodo **completeItem** e trovare la riga di codice commentata seguente:

        // Update the item in the TodoItem table and remove from the items array on completion

    Sostituire il corpo del metodo da quel punto alla fine del metodo, con il codice seguente:

        // Update the item in the TodoItem table and remove from the items array on completion
        [self.table update:mutable completion:^(NSDictionary *item, NSError *error) {

            // Get a fresh index in case the list has changed
            NSUInteger index = [items indexOfObjectIdenticalTo:mutable];

            [mutableItems removeObjectAtIndex:index];

            // Let the caller know that we have finished
            completion(index);
	    }];

   	Il codice rimuove gli elementi TodoItems dopo che sono stati contrassegnati come completati.

13. In TodoListController.m individuare il metodo **onAdd** e sovrascriverlo con il codice seguente:

		- (IBAction)onAdd:(id)sender
		  {
          if (itemText.text.length  == 0)
          {
              return;
          }

          NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @NO };
          UITableView *view = self.tableView;
          [self.todoService addItem:item completion:^(NSUInteger index)
          {
              NSIndexPath *indexPath = [NSIndexPath indexPathForRow:index inSection:0];
              [view insertRowsAtIndexPaths:@[ indexPath ]
                          withRowAnimation:UITableViewRowAnimationTop];
          }];

          itemText.text = @"";
		}


Una volta aggiornata l'app per consentire l'uso di Servizi mobili per l'archiviazione back-end, è possibile verificarne il funzionamento in Servizi mobili.

<!--HONumber=42-->
