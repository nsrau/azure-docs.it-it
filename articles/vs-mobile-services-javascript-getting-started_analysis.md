<properties title="Getting Started with Mobile Services" pageTitle="" metaKeywords="Azure, Getting Started, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

### Operazione eseguita

###### Aggiunta di riferimenti

La libreria di Servizi mobili di Microsoft Azure è stata aggiunta al progetto sotto forma di un file MobileServices.js.

###### Valori della stringa di connessione per Servizi mobili

Nella cartella services\\mobileServices\\settings è stato generato un nuovo file JavaScript (con estensione js) con MobileServiceClient, contenente l'URL applicazione e la chiave applicazione del servizio mobile selezionato.

### Introduzione a Servizi mobili

###### Ottenere un riferimento a una tabella

L'oggetto client è già stato aggiunto al progetto. Il nome corrisponde al nome del servizio mobile con l'aggiunta di "Client". Il codice seguente ottiene un riferimento a una tabella contenente i dati per TodoItem, che può essere usato in operazioni successive per leggere e aggiornare la tabella dati.

    var todoTable = yourMobileServiceClient.getTable('TodoItem');

###### Aggiungere una voce

Inserire un nuovo elemento in una tabella dati. Un ID (un GUID di tipo string) viene creato automaticamente come chiave primaria per la nuova riga. Non cambiare il tipo della colonna ID, poiché è già usato dall'infrastruttura di servizi mobili.

    var todoTable = client.getTable('TodoItem');
    var todoItems = new WinJS.Binding.List();
    var insertTodoItem = function (todoItem) {
        todoTable.insert(todoItem).done(function (item) {
            todoItems.push(item);
        });
    };

###### Leggere una tabella o eseguirvi query

Il codice seguente esegue in una tabella una query relativa a tutti gli elementi, aggiorna una raccolta locale e associa il risultato all'elemento listItems dell'interfaccia utente.

        // This code refreshes the entries in the list view 
        // by querying the TodoItems table.
        todoTable.where()
            .read()
            .done(function (results) {
                todoItems = new WinJS.Binding.List(results);
                listItems.winControl.itemDataSource = todoItems.dataSource;
            });

È possibile usare il metodo Where per modificare la query. Di seguito è disponibile un esempio che permette di escludere tramite filtro gli elementi completati.

    todoTable.where(function () {
        return (this.complete === false && this.createdAt !== null);
    })
    .read()
    .done(function (results) {
        todoItems = new WinJS.Binding.List(results);
        listItems.winControl.itemDataSource = todoItems.dataSource;
    });

Per altri esempi di query disponibili per l'utilizzo, vedere la pagina relativa all'[oggetto query][oggetto query].

###### Aggiornare una voce

Aggiornare una riga in una tabella dati. In questo esempio todoItem è l'elemento aggiornato e l'elemento corrisponde all'elemento restituito dal servizio mobile. Quando il servizio mobile risponde, l'elemento verrà aggiornato nell'elenco todoItems locale tramite il metodo [splice][splice]. Chiamare il metodo [done]() sull'oggetto [Promise]() restituito per ottenere una copia dell'oggetto inserito e gestire eventuali errori.

        todoTable.update(todoItem).done(function (item) {
            todoItems.splice(todoItems.indexOf(item), 1, item);
        });

###### Eliminare una voce

Eliminare una riga in una tabella dati. Chiamare il metodo [done]() sull'oggetto [Promise]() restituito per ottenere una copia dell'oggetto inserito e gestire eventuali errori.

    todoTable.delete(todoItem).done(function (item) {
        todoItems.splice(todoItems.indexOf(item), 1);
    }

  [oggetto query]: http://msdn.microsoft.com/library/azure/jj613353.aspx
  [splice]: http://msdn.microsoft.com/library/windows/apps/Hh700810.aspx
   
