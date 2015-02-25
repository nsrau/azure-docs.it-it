<properties 
	pageTitle="" 
	description="" 
	services="mobile-services" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/8/2014" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [Introduzione](/documentation/articles/vs-mobile-services-cordova-getting-started/)
> - [Risultati](/documentation/articles/vs-mobile-services-cordova-what-happened/)

## Introduzione a Servizi mobili (progetti Cordova)

Il primo passaggio da eseguire per attenersi al codice riportato in questi esempi dipende dal tipo di servizio mobile a cui si è connessi.

Per un servizio mobile back-end JavaScript, creare una tabella denominata TodoItem.  Per creare una tabella, individuare il servizio mobile nel nodo Azure di Esplora server, fare clic con il pulsante destro del mouse sul nodo per aprire il menu di scelta rapida, quindi scegliere **Crea tabella**. Immettere "TodoItem" come nome della tabella.

Se invece si è connessi a un servizio mobile back-end .NET, nel modello di progetto predefinito creato da Visual Studio è già presente una tabella TodoItem, che deve però essere pubblicata in Azure. Per eseguire la pubblicazione, aprire il menu di scelta rapida per il progetto del servizio mobile in Esplora soluzioni e scegliere **Pubblica sito Web**. Accettare le impostazioni predefinite, quindi scegliere **Pubblica**.
  
>[AZURE.NOTE]**Nei progetti Cordova compilati mediante Visual Studio 2015 Preview, usare questa [soluzione](http://go.microsoft.com/fwlink/?LinkId=518765) per l'utilizzo con Servizi mobili di Azure. La soluzione non è necessaria per i progetti che usano versioni più recenti di Visual Studio 2015.**

#####Ottenere un riferimento a una tabella

Il codice seguente ottiene un riferimento a una tabella contenente i dati per TodoItem, che può essere usato in operazioni successive per leggere e aggiornare la tabella dati. La tabella TodoItem viene creata automaticamente quando si crea un servizio mobile.

	var todoTable = mobileServiceClient.getTable('TodoItem');

Per il corretto funzionamento di questi esempio, le autorizzazioni della tabella devono essere impostate su **Chiunque con la chiave applicazione**. È possibile configurare l'autenticazione in un secondo momento. Vedere [Introduzione all'autenticazione](http://azure.microsoft.com/it-it/documentation/articles/mobile-services-html-get-started-users/).

#####Aggiungere una voce 

Inserire un nuovo elemento in una tabella dati. Un ID (un GUID di tipo string) viene creato automaticamente come chiave primaria per la nuova riga. Chiamare il metodo [done]() sull'oggetto [Promise]() restituito per ottenere una copia dell'oggetto inserito e gestire eventuali errori.

    function TodoItem(text) {
        this.text = text;
        this.complete = false;
    }

    var items = new Array();
    todoTable.insert(todoItem).done(function (item) {
        items.push(item)
        });
    };

#####Leggere una tabella o eseguirvi query 

Il codice seguente esegue in una tabella una query relativa a tutti gli elementi, ordinati in base al campo di testo. È possibile aggiungere codice per elaborare i risultati della query nel gestore dell'evento con esito positivo. In questo caso, verrà aggiornata una matrice locale degli elementi.

    todoTable.orderBy('text')
        .read().done(function (results) {
            items = results.slice();
            });
        });

È possibile usare il metodo Where per modificare la query. Di seguito è disponibile un esempio che permette di escludere tramite filtro gli elementi completati.

	todoTable.where(function () {
                 return (this.complete === false);
              })
             .read().done(function (results) {
                items = results.slice();
             });

Per altri esempi di query disponibili per l'uso, vedere la pagina relativa all'oggetto [query]((http://msdn.microsoft.com/library/azure/jj613353.aspx)).

#####Aggiornare una voce

Aggiornare una riga in una tabella dati. In questo codice, l'elemento viene rimosso dall'elenco quando il servizio mobile risponde. Chiamare il metodo [done]() sull'oggetto [Promise]() restituito per ottenere una copia dell'oggetto inserito e gestire eventuali errori.

    todoTable.update(todoItem).done(function (item) {
        // Update a local collection of items.
        items.splice(items.indexOf(todoItem), 1, item);
    });

#####Eliminare una voce

Eliminare una voce nella tabella dati usando il metodo **del**. Chiamare il metodo [done]() sull'oggetto [Promise]() restituito per ottenere una copia dell'oggetto inserito e gestire eventuali errori.

	todoTable.del(todoItem).done(function (item) {
        items.splice(items.indexOf(todoItem), 1);
	});

[Altre informazioni sui servizi mobili](http://azure.microsoft.com/documentation/services/mobile-services/)

<!--HONumber=42-->
