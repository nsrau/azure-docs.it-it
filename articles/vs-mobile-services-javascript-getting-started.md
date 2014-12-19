<properties title="Getting Started with Mobile Services" pageTitle="" metaKeywords="Azure, Getting Started, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="vs-getting-started" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
> - [Per iniziare](/documentation/articles/vs-mobile-services-javascript-getting-started/)
> - [Risultati](/documentation/articles/vs-mobile-services-javascript-what-happened/)

## Introduzione a Servizi mobili

Il primo passaggio da eseguire per attenersi al codice riportato in questi esempi dipende dal tipo di servizio mobile a cui si è connessi.

Per un servizio mobile back-end JavaScript, creare una tabella denominata TodoItem.  Per creare una tabella, individuare il servizio mobile nel nodo Azure di Esplora server, fare clic con il pulsante destro del mouse sul nodo per aprire il menu di scelta rapida, quindi scegliere **Crea tabella**. Immettere "TodoItem" come nome della tabella.

Se invece si è connessi a un servizio mobile back-end .NET, nel modello di progetto predefinito creato da Visual Studio è già presente una tabella TodoItem, che deve però essere pubblicata in Azure. Per eseguire la pubblicazione, aprire il menu di scelta rapida per il progetto del servizio mobile in Esplora soluzioni e scegliere **Pubblica sito Web**. Accettare le impostazioni predefinite, quindi scegliere **Pubblica**.

#####Ottenere un riferimento a una tabella

L'oggetto client è già stato aggiunto al progetto.  Il nome corrisponde al nome del servizio mobile con l'aggiunta di "Client". Il codice seguente ottiene un riferimento a una tabella contenente i dati per TodoItem, che può essere usato in operazioni successive per leggere e aggiornare la tabella dati.

	var todoTable = yourMobileServiceClient.getTable('TodoItem');

#####Aggiungere una voce 

Inserire un nuovo elemento in una tabella dati. Un ID (un GUID di tipo string) viene creato automaticamente come chiave primaria per la nuova riga. Non cambiare il tipo della colonna ID, poiché è già usato dall'infrastruttura di servizi mobili.

    var todoTable = client.getTable('TodoItem');
    var todoItems = new WinJS.Binding.List();
    var insertTodoItem = function (todoItem) {
        todoTable.insert(todoItem).done(function (item) {
            todoItems.push(item);
        });
    };

#####Leggere una tabella o eseguirvi query

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

Per altri esempi di query disponibili per l'utilizzo, vedere la pagina relativa all'[oggetto query](http://msdn.microsoft.com/library/azure/jj613353.aspx).

#####Aggiornare una voce

Aggiornare una riga in una tabella dati. In questo esempio todoItem è l'elemento aggiornato e l'elemento corrisponde all'elemento restituito dal servizio mobile. Quando il servizio mobile risponde, l'elemento verrà aggiornato nell'elenco todoItems locale tramite il metodo [splice](http://msdn.microsoft.com/library/windows/apps/Hh700810.aspx). Chiamare il metodo [done] () sull'oggetto [Promise] () restituito per ottenere una copia dell'oggetto inserito e gestire eventuali errori.

        todoTable.update(todoItem).done(function (item) {
            todoItems.splice(todoItems.indexOf(item), 1, item);
        });

#####Eliminare una voce

Eliminare una riga in una tabella dati. Chiamare il metodo [done] () sull'oggetto [Promise] () restituito per ottenere una copia dell'oggetto inserito e gestire eventuali errori.

	todoTable.delete(todoItem).done(function (item) {
	    todoItems.splice(todoItems.indexOf(item), 1);
    }



[Altre informazioni sui servizi mobili](http://azure.microsoft.com/documentation/services/mobile-services/)
