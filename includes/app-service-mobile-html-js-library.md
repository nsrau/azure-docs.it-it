##<a name="create-client"></a>Procedura: creare Client

Creare una connessione client creando un oggetto `WindowsAzure.MobileServicesClient`. Sostituire `appUrl` con l'URL dell'app per dispositivi mobili.

```
var client = WindowsAzure.MobileServicesClient(appUrl);
```

##<a name="table-reference"></a>Procedura: Creare un riferimento alla tabella

Per l'accesso o l'aggiornamento dei dati, creare un riferimento alla tabella di back-end. Sostituire `tableName` con il nome della tabella

```
var table = client.getTable(tableName);
```

##<a name="querying"></a>Procedura: Eseguire query su un riferimento a tabella

Dopo aver creato un riferimento a tabella, è possibile usarlo per eseguire una query sui dati nel server. Le query vengono eseguite in un linguaggio "simile a LINQ". Per restituire tutti i dati dalla tabella, usare quanto segue:

```
/**
 * Process the results that are received by a call to table.read()
 *
 * @param {Object} results the results as a pseudo-array
 * @param {int} results.length the length of the results array
 * @param {Object} results[] the individual results
 */
function success(results) {
   var numItemsRead = results.length;

   for (var i = 0 ; i < results.length ; i++) {
       var row = results[i];
       // Each row is an object - the properties are the columns
   }
}

function failure(error) {
    throw new Error('Error loading data: ', error);
}

table
    .read()
    .then(success, failure);
```

La funzione success viene chiamata con l'oggetto results. Non usare `for (var i in results)` nella funzione success, perché scorre le informazioni incluse nei risultati quando si usano altre funzioni di query, ad esempio `.includeTotalCount()`.

Per altre informazioni sulla sintassi delle query, vedere la [documentazione relativa all'oggetto Query].

### Filtro dei dati nel server

È possibile usare una clausola `where` nel riferimento alla tabella:

```
table
    .where({ userId: user.userId, complete: false })
    .read()
    .then(success, failure);
```

È anche possibile usare una funzione che filtra l'oggetto. In questo caso la variabile `this` viene assegnata all'oggetto che si sta filtrando. A livello funzionale, l'esempio seguente è equivalente al precedente:

```
function filterByUserId(currentUserId) {
    return this.userId === currentUserId && this.complete === false;
}

table
    .where(filterByUserId, user.userId)
    .read()
    .then(success, failure);
```

### Paging dei dati

Usare i metodi take() e skip(). Ad esempio, se si vuole dividere la tabella in record di 100 righe:

```
var totalCount = 0, pages = 0;

// Step 1 - get the total number of records
table.includeTotalCount().take(0).read(function (results) {
    totalCount = results.totalCount;
    pages = Math.floor(totalCount/100) + 1;
    loadPage(0);
}, failure);

function loadPage(pageNum) {
    let skip = pageNum * 100;
    table.skip(skip).take(100).read(function (results) {
        for (var i = 0 ; i < results.length ; i++) {
            var row = results[i];
            // Process each row
        }
    }
}
```

Il metodo `.includeTotalCount()` viene usato per aggiungere un campo totalCount all'oggetto results. Se non si usa il paging, il campo totalCount viene compilato con il numero totale di record restituiti.

Si potrà quindi usare la variabile pages e alcuni pulsanti dell'interfaccia utente per fornire un elenco di pagine. Usare loadPage() per caricare i nuovi record per ogni pagina. È necessario implementare una sorta di caching per velocizzare l'accesso ai record già caricati.


###<a name="sorting-data"></a>Procedura: Restituire dati ordinati

Usare i metodi di query .orderBy() o .orderByDescending():

```
table
    .orderBy('name')
    .read()
    .then(success, failure);
```

Per altre informazioni sull'oggetto Query, vedere la [documentazione relativa all'oggetto Query].

##<a name="inserting"></a>Procedura: Inserire dati

Creare un oggetto JavaScript con la data appropriata e chiamare table.insert() in modo asincrono:

```
var newItem = {
    name: 'My Name',
    signupDate: new Date()
};

table
    .insert(newItem)
    .done(function (insertedItem) {
        var id = insertedItem.id;
    }, failure);
```

Una volta completato l'inserimento, viene restituito l'elemento inserito con i campi aggiuntivi necessari per le operazioni di sincronizzazione. È necessario aggiornare la cache con queste informazioni per gli aggiornamenti successivi.

Si noti che Node. js Server SDK per le app per dispositivi mobili supporta lo schema dinamico per scopi di sviluppo. Nel caso di schema dinamico, lo schema della tabella viene aggiornato in tempo reale, consentendo di aggiungere colonne alla tabella specificandole semplicemente in un'operazione di inserimento o aggiornamento. È consigliabile disattivare lo schema dinamico prima di trasferire l'applicazione in produzione.

##<a name="modifying"></a>Procedura: Modificare dati

In modo analogo al metodo .insert(), è consigliabile creare un oggetto Update e quindi chiamare .update(). L'oggetto update deve contenere l'ID del record da aggiornare, che si ottiene durante la lettura del record o quando si chiama .insert().

```
var updateItem = {
    id: '7163bc7a-70b2-4dde-98e9-8818969611bd',
    name: 'My New Name'
};

table
    .update(updateItem)
    .done(function (updatedItem) {
        // You can now update your cached copy
    }, failure);
```

##<a name="deleting"></a>Procedura: Eliminare dati

Chiamare il metodo .del() per eliminare un record. Passare l'ID in un riferimento all'oggetto:

```
table
    .del({ id: '7163bc7a-70b2-4dde-98e9-8818969611bd' })
    .done(function () {
        // Record is now deleted - update your cache
    }, failure);
```

<!---HONumber=AcomDC_0309_2016-->