
Aggiornare l'app per memorizzare gli elementi in Servizi mobili di Azure anziché nella raccolta in memoria locale.

* In **TodoService.h** individuare la riga seguente:

```
// TODO - create an MSClient property
```

Sostituire il commento con la riga seguente. Verrà creata una proprietà che rappresenta `MSClient` che si connette al servizio.

```
@property (nonatomic, strong)   MSClient *client;
```


* In **TodoService.m** individuare la riga seguente:

```
// TODO - create an MSTable property for your items
```

Sostituire il commento con la riga seguente nella dichiarazione `@interface`. Verrà creata una rappresentazione della proprietà per la tabella di Servizi mobili.

```
@property (nonatomic, strong)   MSTable *table;
```


* Nel portale di gestione fare clic su **Servizi mobili** e quindi sul sevizio mobile. Fare clic sulla scheda **Dashboard** e prendere nota del valore in **URL sito**. Fare clic su **Gestisci chiavi** e prendere nota del valore in **Chiave applicazione**. Questi valori sono necessari per accedere al servizio mobile dal codice dell'app.


* In **TodoService.m** individuare la riga seguente:

```
// Initialize the Mobile Service client with your URL and key.
```

Dopo questo commento, aggiungere la riga di codice seguente: Sostituire `APPURL` e `APPKEY` con i valori dell'URL del sito e della chiave dell'applicazione ottenuti nel passaggio precedente.

```
self.client = [MSClient clientWithApplicationURLString:@"APPURL" applicationKey:@"APPKEY"];
```


* In **TodoService.m** individuare la riga seguente:

```
// Create an MSTable instance to allow us to work with the TodoItem table.
```

Sostituire il commento con la riga seguente. Verrà creata un'istanza della tabella TodoItem.

```
self.table = [self.client tableWithName:@"TodoItem"];
```


* In **TodoService.m** individuare la riga seguente:

```
// Create a predicate that finds items where complete is false
```

Sostituire il commento con la riga seguente. Verrà creata una query che restituisce tutte le attività non ancora completate.

```
NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
```


* Individuare la riga seguente:

```
// Query the TodoItem table and update the items property with the results from the service
```

Sostituire il commento e la chiamata al blocco **completion** successiva con il codice seguente:

```
[self.table readWhere:predicate completion:^(NSArray *results, NSInteger totalCount, NSError *error)
{
self.items = [results mutableCopy];
   completion();
}];
```


* Individuare il metodo **addItem** e sostituire il corpo del metodo con il codice seguente: Il codice invia una richiesta insert al servizio mobile.

```
// Insert the item into the TodoItem table and add to the items array on completion
[self.table insert:item completion:^(NSDictionary *result, NSError *error) {
    NSUInteger index = [items count];
    [(NSMutableArray *)items insertObject:item atIndex:index];

    // Let the caller know that we finished
    completion(index);
}];
```


* Individuare il metodo **completeItem** e trovare la riga di codice commentata seguente:

```
// Update the item in the TodoItem table and remove from the items array on completion
```

Sostituire il corpo del metodo da quel punto alla fine del metodo, con il codice seguente: Il codice rimuove gli elementi TodoItems dopo che sono stati contrassegnati come completati.

```
// Update the item in the TodoItem table and remove from the items array on completion
[self.table update:mutable completion:^(NSDictionary *item, NSError *error) {

    // Get a fresh index in case the list has changed
    NSUInteger index = [items indexOfObjectIdenticalTo:mutable];

    [mutableItems removeObjectAtIndex:index];

    // Let the caller know that we have finished
    completion(index);
}];
```


* In TodoListController.m, trovare il metodo **onAdd** e sovrascriverlo con il codice seguente:

```
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
```

<!--HONumber=54-->