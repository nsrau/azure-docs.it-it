---
title: Modificare il feed nell'API del database Cosmos di Azure per CassandraChange feed in the Azure Cosmos DB API for Cassandra
description: Informazioni su come usare il feed di modifiche nell'API di Azure Cosmos DB per Cassandra per ottenere le modifiche apportate ai dati.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 11/25/2019
ms.author: thvankra
ms.openlocfilehash: c2c695608653130b97bf29cc9ce48e2fbb429209
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74694623"
---
# <a name="change-feed-in-the-azure-cosmos-db-api-for-cassandra"></a>Modificare il feed nell'API del database Cosmos di Azure per CassandraChange feed in the Azure Cosmos DB API for Cassandra

[Il](change-feed.md) supporto dei feed di modifiche nell'API del database Cosmos di Azure per Cassandra è disponibile tramite i predicati di query nel linguaggio CQL (Cassandra Query Language). Usando queste condizioni di predicato, è possibile eseguire una query sull'API del feed di modifiche. Le applicazioni possono ottenere le modifiche apportate a una tabella usando la chiave primaria (nota anche come chiave di partizione) come richiesto in CQL. È quindi possibile eseguire ulteriori azioni in base ai risultati. Le modifiche apportate alle righe della tabella vengono acquisite nell'ordine dell'ora di modifica e l'ordinamento è garantito per ogni chiave di partizione.

L'esempio seguente mostra come ottenere un feed di modifiche in tutte le righe di una tabella Keyspace API Cassandra utilizzando .NET. Il predicato COSMOS_CHANGEFEED_START_TIME() viene utilizzato direttamente all'interno di CQL per eseguire query sugli elementi nel feed di modifiche da un'ora di inizio specificata (in questo caso datetime corrente). È possibile scaricare l'esempio completo [qui](https://docs.microsoft.com/samples/azure-samples/azure-cosmos-db-cassandra-change-feed/cassandra-change-feed/).

In ogni iterazione, la query riprende all'ultimo punto in cui sono state lette le modifiche, utilizzando lo stato di paging. Possiamo vedere un flusso continuo di nuove modifiche alla tabella nel Keyspace. Verranno visualizzate le modifiche apportate alle righe inserite o aggiornate. Il controllo delle operazioni di eliminazione tramite il feed di modifiche nell'API Cassandra non è attualmente supportato. 

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

Per ottenere le modifiche apportate a una singola riga in base alla chiave primaria, è possibile aggiungere la chiave primaria nella query. Nell'esempio seguente viene illustrato come tenere traccia delle modifiche per la riga in cui "user_id s 1"

```C#
    //Return the latest change for all row in 'user' table where user_id = 1
    IStatement changeFeedQueryStatement = new SimpleStatement(
    $"SELECT * FROM uprofile.user where user_id = 1 AND COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");

```

## <a name="current-limitations"></a>Limitazioni correnti

Le limitazioni seguenti sono applicabili quando si usa il feed di modifiche con l'API Cassandra:

* Gli inserimenti e gli aggiornamenti sono attualmente supportati. L'operazione di eliminazione non è ancora supportata. In alternativa, è possibile aggiungere un indicatore soft marker sulle righe che vengono eliminate. Ad esempio, aggiungere un campo nella riga denominato "deleted" e impostarlo su "true".
* L'ultimo aggiornamento viene salvato in modo permanente come nell'API SQL di base e gli aggiornamenti intermedi all'entità non sono disponibili.


## <a name="error-handling"></a>Gestione degli errori

I seguenti codici di errore e messaggi sono supportati quando si utilizza il feed di modifiche nell'API Cassandra:

* Codice di **errore HTTP 429** - Quando il feed di modifiche è limitato, restituisce una pagina vuota.

## <a name="next-steps"></a>Passaggi successivi

* [Gestire le risorse API Cassandra di Azure Cosmos DB usando i modelli di Azure Resource ManagerManage Azure Cosmos DB Cassandra API resources using Azure Resource Manager templates](manage-cassandra-with-resource-manager.md)
