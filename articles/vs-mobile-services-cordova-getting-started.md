<properties title="Getting Started with Mobile Services" pageTitle="" metaKeywords="Azure, Getting Started, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

### Operazione eseguita

### Aggiunta di riferimenti

È stato abilitato il plug-in client di Servizi mobili di Azure incluso in tutte le app ibride per più dispositivi.

### Valori della stringa di connessione per Servizi mobili

In services\\mobileServices\\settings è stato generato un nuovo file JavaScript (con estensione js) con un MobileServiceClient, contenente l'URL applicazione e la chiave applicazione del servizio mobile selezionato. Il file include l'inizializzazione dell'oggetto client di Servizi mobili, che usa codice analogo al seguente.

    var mobileServiceClient;
    document.addEventListener("deviceready", function() {
            mobileServiceClient = new WindowsAzure.MobileServiceClient(
            "<your mobile service name>.azure-mobile.net",
            "<insert your key>"
        );

### Introduzione a Servizi mobili

###### Ottenere un riferimento a una tabella

Il codice seguente ottiene un riferimento a una tabella contenente i dati per TodoItem, che può essere usato in operazioni successive per leggere e aggiornare la tabella dati. La tabella TodoItem viene creata automaticamente quando si crea un servizio mobile.

    var todoTable = mobileServiceClient.getTable('TodoItem');

Per il corretto funzionamento di questi esempio, le autorizzazioni della tabella devono essere impostate su **Chiunque con la chiave applicazione**. È possibile configurare l'autenticazione in un secondo momento. Vedere [Introduzione all'autenticazione][Introduzione all'autenticazione].

###### Aggiungere una voce

Inserire un nuovo elemento in una tabella dati. Un ID (un GUID di tipo string) viene creato automaticamente come chiave primaria per la nuova riga. Chiamare il metodo [done][done] sull'oggetto [Promise][done] restituito per ottenere una copia dell'oggetto inserito e gestire eventuali errori.

    function TodoItem(text) {
        this.text = text;
        this.complete = false;
    }

    var items = new Array();
    todoTable.insert(todoItem).done(function (item) {
        items.push(item)
        });
    };

###### Leggere una tabella o eseguirvi query

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

Per altri esempi di query disponibili per l'utilizzo, vedere la pagina relativa all'oggetto [query][query].

###### Aggiornare una voce

Aggiornare una riga in una tabella dati. In questo codice, l'elemento viene rimosso dall'elenco quando il servizio mobile risponde. Chiamare il metodo [done][done] sull'oggetto [Promise][done] restituito per ottenere una copia dell'oggetto inserito e gestire eventuali errori.

    todoTable.update(todoItem).done(function (item) {
        // Update a local collection of items.
        items.splice(items.indexOf(todoItem), 1, item);
    });

###### Eliminare una voce

Eliminare una voce nella tabella dati usando il metodo del. Chiamare il metodo [done]() sull'oggetto [Promise]() restituito per ottenere una copia dell'oggetto inserito e gestire eventuali errori.

    todoTable.del(todoItem).done(function (item) {
        items.splice(items.indexOf(todoItem), 1);
    });

  [Introduzione all'autenticazione]: http://azure.microsoft.com/en-us/documentation/articles/mobile-services-html-get-started-users/
 
  [query]: (http://msdn.microsoft.com/library/azure/jj613353.aspx)
