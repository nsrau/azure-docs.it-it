---
title: Feed delle modifiche nell'API Azure Cosmos DB per Cassandra
description: Informazioni su come usare il feed delle modifiche nell'API Azure Cosmos DB per Cassandra per ottenere le modifiche apportate ai dati.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 11/25/2019
ms.author: thvankra
ms.openlocfilehash: c2c695608653130b97bf29cc9ce48e2fbb429209
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74694623"
---
# <a name="change-feed-in-the-azure-cosmos-db-api-for-cassandra"></a>Feed delle modifiche nell'API Azure Cosmos DB per Cassandra

Il supporto del [feed delle modifiche](change-feed.md) nell'API Azure Cosmos DB per Cassandra è disponibile tramite i predicati di query del linguaggio di query Cassandra (CQL). Utilizzando queste condizioni del predicato, è possibile eseguire una query sull'API del feed delle modifiche. Le applicazioni possono ottenere le modifiche apportate a una tabella usando la chiave primaria, nota anche come chiave di partizione, come richiesto in CQL. È quindi possibile eseguire ulteriori azioni in base ai risultati. Le modifiche apportate alle righe nella tabella vengono acquisite nell'ordine in cui sono state modificate e il tipo di ordinamento è garantito per ogni chiave di partizione.

Nell'esempio seguente viene illustrato come ottenere un feed delle modifiche in tutte le righe di una tabella API Cassandra spazio per la spaziatura con .NET. Il predicato COSMOS_CHANGEFEED_START_TIME () viene usato direttamente all'interno di CQL per eseguire query sugli elementi nel feed di modifiche da un'ora di inizio specificata (in questo caso DateTime corrente). È possibile scaricare l'esempio completo [qui](https://docs.microsoft.com/samples/azure-samples/azure-cosmos-db-cassandra-change-feed/cassandra-change-feed/).

In ogni iterazione, la query riprende in corrispondenza dell'ultimo punto in cui sono state lette le modifiche, usando lo stato di paging. È possibile visualizzare un flusso continuo di nuove modifiche apportate alla tabella nello spazio. Verranno visualizzate le modifiche apportate alle righe inserite o aggiornate. Il monitoraggio delle operazioni di eliminazione tramite il feed delle modifiche in API Cassandra non è attualmente supportato. 

```C#
    //set initial start time for pulling the change feed
     DateTime timeBegin = DateTime.UtcNow;

    //initialise variable to store the continuation token
    byte[] pageState = null;
    while (true)
    {
        try
        {

            //Return the latest change for all rows in 'user' table    
            IStatement changeFeedQueryStatement = new SimpleStatement(
            $"SELECT * FROM uprofile.user where COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");
            if (pageState != null)
            {
                changeFeedQueryStatement = changeFeedQueryStatement.SetPagingState(pageState);
            }
            Console.WriteLine("getting records from change feed at last page state....");
            RowSet rowSet = session.Execute(changeFeedQueryStatement);

            //store the continuation token here
            pageState = rowSet.PagingState;

            List<Row> rowList = rowSet.ToList();
            if (rowList.Count != 0)
            {
                for (int i = 0; i < rowList.Count; i++)
                {
                    string value = rowList[i].GetValue<string>("user_name");
                    int key = rowList[i].GetValue<int>("user_id");
                    // do something with the data - e.g. compute, forward to another event, function, etc.
                    // here, we just print the user name field
                    Console.WriteLine("user_name: " + value);
                }
            }
            else
            {
                Console.WriteLine("zero documents read");
            }
        }
        catch (Exception e)
        {
            Console.WriteLine("Exception " + e);
        }
    }

```

Per ottenere le modifiche apportate a una singola riga tramite chiave primaria, è possibile aggiungere la chiave primaria nella query. Nell'esempio seguente viene illustrato come tenere traccia delle modifiche per la riga in cui "user_id = 1"

```C#
    //Return the latest change for all row in 'user' table where user_id = 1
    IStatement changeFeedQueryStatement = new SimpleStatement(
    $"SELECT * FROM uprofile.user where user_id = 1 AND COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");

```

## <a name="current-limitations"></a>Limitazioni correnti

Quando si usa il feed di modifiche con API Cassandra, è possibile applicare le limitazioni seguenti:

* Gli inserimenti e gli aggiornamenti sono attualmente supportati. L'operazione di eliminazione non è ancora supportata. Come soluzione alternativa, è possibile aggiungere un marcatore Soft sulle righe da eliminare. Aggiungere ad esempio un campo nella riga denominato "Deleted" e impostarlo su "true".
* L'ultimo aggiornamento è permanente come nell'API SQL di base e gli aggiornamenti intermedi per l'entità non sono disponibili.


## <a name="error-handling"></a>Gestione degli errori

I codici di errore e i messaggi seguenti sono supportati quando si usa il feed delle modifiche in API Cassandra:

* **Codice di errore HTTP 429** : quando il feed delle modifiche è limitato, viene restituita una pagina vuota.

## <a name="next-steps"></a>Passaggi successivi

* [Gestire Azure Cosmos DB API Cassandra risorse tramite modelli Azure Resource Manager](manage-cassandra-with-resource-manager.md)
