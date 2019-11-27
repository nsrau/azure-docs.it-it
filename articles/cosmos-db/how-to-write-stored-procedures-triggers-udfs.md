---
title: Come scrivere stored procedure, trigger e funzioni definite dall'utente in Azure Cosmos DB
description: Informazioni su come scrivere stored procedure, trigger e funzioni definite dall'utente in Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: mjbrown
ms.openlocfilehash: ffe002e4dced5b5020eb1436ca6d7d577402b077
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533161"
---
# <a name="how-to-write-stored-procedures-triggers-and-user-defined-functions-in-azure-cosmos-db"></a>Come scrivere stored procedure, trigger e funzioni definite dall'utente in Azure Cosmos DB

L'esecuzione integrata e transazionale di JavaScript con il linguaggio di Azure Cosmos DB permette di scrivere **stored procedure**, **trigger** e **funzioni definite dall'utente**. Quando si usa l'API SQL in Azure Cosmos DB, è possibile scrivere stored procedure, trigger e funzioni definite dall'utente (UDF) nel linguaggio JavaScript. È possibile scrivere la logica nel linguaggio JavaScript ed eseguirla all'interno del motore di database. È possibile creare ed eseguire trigger, stored procedure e UDF usando il [portale di Azure](https://portal.azure.com/), l'[API di query integrata nel linguaggio JavaScript in Azure Cosmos DB](javascript-query-api.md) e gli [SDK client dell'API SQL di Cosmos DB](sql-api-dotnet-samples.md). 

Per chiamare una stored procedure, un trigger e le funzioni definite dall'utente, è necessario registrarla. Per altre informazioni, vedere [Come usare stored procedure, trigger e funzioni definite dall'utente in Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md).

> [!NOTE]
> Per i contenitori partizionati, quando si esegue una stored procedure, è necessario specificare un valore della chiave di partizione nelle opzioni di richiesta. Le stored procedure hanno sempre come ambito una chiave di partizione. Gli elementi con un valore della chiave di partizione diverso non saranno visibili alla stored procedure. Questo vale anche per i trigger.

> [!Tip]
> Cosmos supporta la distribuzione di contenitori con stored procedure, trigger e funzioni definite dall'utente. Per altre informazioni [, vedere creare un contenitore Azure Cosmos DB con funzionalità lato server.](manage-sql-with-resource-manager.md#create-sproc)

## <a id="stored-procedures"></a>Come scrivere stored procedure

Le stored procedure vengono scritte in JavaScript, e possono creare, aggiornare, leggere, eseguire una query ed eliminare elementi all'interno di un contenitore di Azure Cosmos. Le stored procedure vengono registrate per ogni raccolta e funzionano in qualsiasi documento o allegato presente nella raccolta.

**Esempio**

Ecco una semplice stored procedure che restituisce una risposta "Hello World".

```javascript
var helloWorldStoredProc = {
    id: "helloWorld",
    serverScript: function () {
        var context = getContext();
        var response = context.getResponse();

        response.setBody("Hello, World");
    }
}
```

L'oggetto contesto offre accesso a tutte le operazioni che è possibile eseguire in Azure Cosmos DB, oltre all'accesso agli oggetti richiesta e risposta. In questo caso, si usa l'oggetto risposta per impostare il corpo della risposta da restituire al client.

Una volta scritta, la stored procedure deve essere registrata con una raccolta. Per altre informazioni, vedere l'articolo [Come usare stored procedure in Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md#stored-procedures).

### <a id="create-an-item"></a>Creare un elemento usando una stored procedure

Quando si crea un elemento usando stored procedure, l'elemento viene inserito nel contenitore Azure Cosmos e viene restituito un ID per l'elemento appena creato. La creazione di un elemento è un'operazione asincrona e dipende dalle funzioni di callback JavaScript. La funzione di callback ha due parametri: uno per l'oggetto errore in caso di errore dell'operazione e uno per il valore restituito (in questo caso, l'oggetto creato). All'interno del callback, è possibile gestire l'eccezione oppure generare un errore. Nel caso in cui non sia disponibile un callback e si verifichi un errore, il runtime di Azure Cosmos DB genererà un errore. 

La stored procedure include anche un parametro per impostare la descrizione, cioè un valore booleano. Quando il parametro è impostato su true e la descrizione non è presente, la stored procedure genererà un'eccezione. In caso contrario, il resto della stored procedure rimane in esecuzione.

L'esempio seguente stored procedure accetta un nuovo elemento di Azure Cosmos come input, lo inserisce nel contenitore Azure Cosmos e restituisce l'ID per l'elemento appena creato. In questo esempio, viene usato l'esempio ToDoList dall'[API SQL .NET di avvio rapido](create-sql-api-dotnet.md)

```javascript
function createToDoItem(itemToCreate) {

    var context = getContext();
    var container = context.getCollection();

    var accepted = container.createDocument(container.getSelfLink(),
        itemToCreate,
        function (err, itemCreated) {
            if (err) throw new Error('Error' + err.message);
            context.getResponse().setBody(itemCreated.id)
        });
    if (!accepted) return;
}
```

### <a name="arrays-as-input-parameters-for-stored-procedures"></a>Matrice come parametri di input per la stored procedure 

Quando si definisce una stored procedure nel portale di Azure, i parametri di input vengono sempre inviati sotto forma di stringa alla stored procedure. Anche se si passa una matrice di stringhe come input, la matrice viene convertita in stringa e inviata alla stored procedure. Per aggirare questo problema, è possibile definire una funzione all'interno della stored procedure che analizzi la stringa come matrice. Il codice seguente illustra come analizzare un parametro di input di stringa sotto forma di matrice:

```javascript
function sample(arr) {
    if (typeof arr === "string") arr = JSON.parse(arr);

    arr.forEach(function(a) {
        // do something here
        console.log(a);
    });
}
```

### <a id="transactions"></a>Transazioni all'interno di stored procedure

È possibile implementare le transazioni negli elementi all'interno di un contenitore usando una stored procedure. L'esempio seguente usa le transazioni in un'app di gioco di fantacalcio per scambiare giocatori tra due squadre in un'unica operazione. La stored procedure prova a leggere i due elementi di Azure Cosmos, ciascuno corrispondente all'ID del giocatore passato come argomento. Se vengono trovati entrambi i giocatori, la stored procedure aggiorna gli elementi scambiando le rispettive squadre. Se si verificano errori lungo il percorso, la stored procedure genera un'eccezione JavaScript che interrompe implicitamente la transazione.

```javascript
// JavaScript source code
function tradePlayers(playerId1, playerId2) {
    var context = getContext();
    var container = context.getCollection();
    var response = context.getResponse();

    var player1Document, player2Document;

    // query for players
    var filterQuery = 
    {     
        'query' : 'SELECT * FROM Players p where p.id = @playerId1',
        'parameters' : [{'name':'@playerId1', 'value':playerId1}] 
    };
            
    var accept = container.queryDocuments(container.getSelfLink(), filterQuery, {},
        function (err, items, responseOptions) {
            if (err) throw new Error("Error" + err.message);

            if (items.length != 1) throw "Unable to find both names";
            player1Item = items[0];

            var filterQuery2 = 
            {     
                'query' : 'SELECT * FROM Players p where p.id = @playerId2',
                'parameters' : [{'name':'@playerId2', 'value':playerId2}] 
            };
            var accept2 = container.queryDocuments(container.getSelfLink(), filterQuery2, {},
                function (err2, items2, responseOptions2) {
                    if (err2) throw new Error("Error" + err2.message);
                    if (items2.length != 1) throw "Unable to find both names";
                    player2Item = items2[0];
                    swapTeams(player1Item, player2Item);
                    return;
                });
            if (!accept2) throw "Unable to read player details, abort ";
        });

    if (!accept) throw "Unable to read player details, abort ";

    // swap the two players’ teams
    function swapTeams(player1, player2) {
        var player2NewTeam = player1.team;
        player1.team = player2.team;
        player2.team = player2NewTeam;

        var accept = container.replaceDocument(player1._self, player1,
            function (err, itemReplaced) {
                if (err) throw "Unable to update player 1, abort ";

                var accept2 = container.replaceDocument(player2._self, player2,
                    function (err2, itemReplaced2) {
                        if (err) throw "Unable to update player 2, abort"
                    });

                if (!accept2) throw "Unable to update player 2, abort";
            });

        if (!accept) throw "Unable to update player 1, abort";
    }
}
```

### <a id="bounded-execution"></a>Esecuzione vincolata all'interno di stored procedure

Di seguito è riportato un esempio di una stored procedure che importa in blocco gli elementi in un contenitore di Azure Cosmos. La stored procedure gestisce l'esecuzione vincolata verificando il valore booleano restituito da `createDocument` e quindi usa il numero di elementi inseriti in ogni chiamata della stored procedure per tracciare e riprendere l'avanzamento nei vari batch.

```javascript
function bulkImport(items) {
    var container = getContext().getCollection();
    var containerLink = container.getSelfLink();

    // The count of imported items, also used as current item index.
    var count = 0;

    // Validate input.
    if (!items) throw new Error("The array is undefined or null.");

    var itemsLength = items.length;
    if (itemsLength == 0) {
        getContext().getResponse().setBody(0);
    }

    // Call the create API to create an item.
    tryCreate(items[count], callback);

    // Note that there are 2 exit conditions:
    // 1) The createDocument request was not accepted.
    //    In this case the callback will not be called, we just call setBody and we are done.
    // 2) The callback was called items.length times.
    //    In this case all items were created and we don’t need to call tryCreate anymore. Just call setBody and we are done.
    function tryCreate(item, callback) {
        var isAccepted = container.createDocument(containerLink, item, callback);

        // If the request was accepted, callback will be called.
        // Otherwise report current count back to the client,
        // which will call the script again with remaining set of items.
        if (!isAccepted) getContext().getResponse().setBody(count);
    }

    // This is called when container.createDocument is done in order to process the result.
    function callback(err, item, options) {
        if (err) throw err;

        // One more item has been inserted, increment the count.
        count++;

        if (count >= itemsLength) {
            // If we created all items, we are done. Just set the response.
            getContext().getResponse().setBody(count);
        } else {
            // Create next document.
            tryCreate(items[count], callback);
        }
    }
}
```

## <a id="triggers"></a>Come scrivere i trigger

Azure Cosmos DB supporta pre-trigger e post-trigger. I pre-trigger vengono eseguiti prima di modificare un elemento nel database e i post-trigger vengono eseguiti dopo la modifica di un elemento nel database.

### <a id="pre-triggers"></a>Pre-trigger

Di seguito è riportato un esempio sull'uso di un pre-trigger per convalidare le proprietà di un elemento di Azure Cosmos in corso di creazione. In questo esempio, viene usato l'esempio ToDoList dall'[API SQL .NET di avvio rapido](create-sql-api-dotnet.md) per aggiungere una proprietà timestamp a un elemento appena aggiunto se non ne contiene uno.

```javascript
function validateToDoItemTimestamp() {
    var context = getContext();
    var request = context.getRequest();

    // item to be created in the current operation
    var itemToCreate = request.getBody();

    // validate properties
    if (!("timestamp" in itemToCreate)) {
        var ts = new Date();
        itemToCreate["timestamp"] = ts.getTime();
    }

    // update the item that will be created
    request.setBody(itemToCreate);
}
```

I pre-trigger non possono avere parametri di input. L'oggetto richiesta nel trigger viene usato per gestire il messaggio di richiesta associato all'operazione. Nell'esempio precedente si esegue il pre-trigger contemporaneamente alla creazione di un elemento di Azure Cosmos e il corpo del messaggio di richiesta contiene l'elemento da creare in formato JSON.

Quando i trigger vengono registrati, è possibile specificare le operazioni con le quali è possibile eseguirli. Questo trigger deve essere creato con un valore `TriggerOperation` di `TriggerOperation.Create`, quindi non è consentito l'uso del trigger in un'operazione di sostituzione come illustrato nel codice seguente.

Per esempi di come registrare e chiamare un pre-trigger, vedere gli articoli sui [pre-trigger](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) e i [post-trigger](how-to-use-stored-procedures-triggers-udfs.md#post-triggers). 

### <a id="post-triggers"></a>Post-trigger

L'esempio seguente mostra un post-trigger. Il trigger esegue query sull'elemento dei metadati, aggiornandolo con le informazioni relative all'elemento appena creato.


```javascript
function updateMetadata() {
var context = getContext();
var container = context.getCollection();
var response = context.getResponse();

// item that was created
var createdItem = response.getBody();

// query for metadata document
var filterQuery = 'SELECT * FROM root r WHERE r.id = "_metadata"';
var accept = container.queryDocuments(container.getSelfLink(), filterQuery,
    updateMetadataCallback);
if(!accept) throw "Unable to update metadata, abort";

function updateMetadataCallback(err, items, responseOptions) {
    if(err) throw new Error("Error" + err.message);
        if(items.length != 1) throw 'Unable to find metadata document';

        var metadataItem = items[0];

        // update metadata
        metadataItem.createdItems += 1;
        metadataItem.createdNames += " " + createdItem.id;
        var accept = container.replaceDocument(metadataItem._self,
            metadataItem, function(err, itemReplaced) {
                    if(err) throw "Unable to update metadata, abort";
            });
        if(!accept) throw "Unable to update metadata, abort";
        return;
}
```

Un aspetto importante da tenere presente è l'esecuzione transazionale dei trigger in Azure Cosmos DB. Questo post-trigger viene eseguito come parte della stessa transazione per lo stesso elemento sottostante. Un'eccezione durante l'esecuzione di post-trigger determinerà l'esito negativo dell'intera transazione. Verrà eseguito il rollback di qualsiasi elemento di cui è stato eseguito il commit e verrà restituita un'eccezione.

Per esempi di come registrare e chiamare un pre-trigger, vedere gli articoli sui [pre-trigger](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) e i [post-trigger](how-to-use-stored-procedures-triggers-udfs.md#post-triggers). 

## <a id="udfs"></a>Come scrivere funzioni definite dall'utente

L'esempio seguente crea una funzione definita dall'utente per calcolare l'imposta sul reddito per varie fasce di reddito. Questa funzione definita dall'utente viene quindi usata all'interno di una query. Ai fini di questo esempio si supponga che ci sia un contenitore denominato "Redditi" con le proprietà indicate di seguito:

```json
{
   "name": "Satya Nadella",
   "country": "USA",
   "income": 70000
}
```

Di seguito è riportata una definizione di funzione per calcolare l'imposta sul reddito per varie fasce di reddito:

```javascript
function tax(income) {

        if(income == undefined)
            throw 'no input';

        if (income < 1000)
            return income * 0.1;
        else if (income < 10000)
            return income * 0.2;
        else
            return income * 0.4;
    }
```

Per esempi su come registrare e usare una funzione definita dall'utente, vedere l'articolo [Come usare funzioni definite dall'utente in Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md#udfs).

## <a name="logging"></a>Registrazione 

Quando si usano stored procedure, trigger o funzioni definite dall'utente, è possibile registrare i passaggi usando il comando `console.log()`. Questo comando consente di concentrare una stringa per il debug quando `EnableScriptLogging` è impostato su true, come illustrato nell'esempio seguente:

```javascript
var response = await client.ExecuteStoredProcedureAsync(
document.SelfLink,
new RequestOptions { EnableScriptLogging = true } );
Console.WriteLine(response.ScriptLog);
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come scrivere o usare stored procedure, trigger e funzioni definite dall'utente in Azure Cosmos DB:

* [Come registrare e usare stored procedure, trigger e funzioni definite dall'utente in Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md)

* [Come scrivere stored procedure e trigger usando l'API di query di Javascript in Azure Cosmos DB](how-to-write-javascript-query-api.md)

* [Utilizzo delle stored procedure, dei trigger e delle funzioni definite dall'utente in Azure Cosmos DB](stored-procedures-triggers-udfs.md)

* [Utilizzo dell'API di query integrata nel linguaggio JavaScript in Azure Cosmos DB](javascript-query-api.md)
