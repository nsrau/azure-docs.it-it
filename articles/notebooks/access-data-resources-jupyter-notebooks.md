---
title: Accedere alle risorse dati dal notebook di Jupyter su Azure
description: Come accedere a file, API REST, database e a varie risorse di archiviazione di Azure diverse da un notebook di Jupyter.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: ee867303-a5e5-4686-b2da-8a0108247d18
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 14a4191612a5d42836ae4be3ff902ca47a6b06d4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60634345"
---
# <a name="access-cloud-data-in-a-notebook"></a>Accedere ai dati cloud in un notebook

Per svolgere lavori interessanti in un notebook di Jupyter sono necessari i dati. I dati, in effetti, sono l'anima dei notebook.

È ovviamente possibile [importare i file di dati in un progetto](work-with-project-data-files.md), anche usando comandi `curl` da un notebook per scaricare direttamente un file. È probabile, tuttavia, che sia necessario lavorare con dati molto più estesi che sono disponibili da origini non file come API REST, database relazionali e risorse di archiviazione cloud come le tabelle di Azure.

Questo articolo illustra brevemente le varie opzioni. Poiché l'accesso ai dati risulta ottimale in azione, è possibile trovare il codice eseguibile negli [Esempi di notebook di Azure: accesso ai dati](https://github.com/Microsoft/AzureNotebooks/blob/master/Samples/Access%20your%20data%20in%20Azure%20Notebooks.ipynb).

## <a name="rest-apis"></a>API REST

In linea generale, la grande quantità di dati disponibili da Internet è accessibile non tramite i file ma tramite le API REST. Per fortuna, poiché una cella notebook può contenere qualsiasi codice che si desidera, è possibile usare codice per inviare richieste e ricevere dati JSON. È quindi possibile convertire tale JSON in qualsiasi formato che si desidera usare, per esempio un dataframe pandas.

Per accedere ai dati usando un'API REST, usare lo stesso codice nelle celle di codice del notebook che si usano in qualsiasi altra applicazione. La struttura generale tramite la libreria delle richieste è la seguente:

```python
import pandas
import requests

# New York City taxi data for 2014
data_url = 'https://data.cityofnewyork.us/resource/gkne-dk5s.json'

# General data request; include other API keys and credentials as needed in the data argument
response = requests.get(data_url, data={"limit" : "20"})

if response.status_code == 200:
    dataframe_rest2 = pandas.DataFrame.from_records(response.json())
    print(dataframe_rest2)
```

## <a name="azure-sql-databases"></a>Database SQL di Azure

È possibile accedere ai database di SQL Server con il supporto delle librerie di pyodbc o pymssql.

[Usare Python per eseguire query su un database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-python) fornisce istruzioni sulla creazione di un database che contiene i dati di AdventureWorks e illustra come eseguire una query su tali dati. Nel notebook di esempio per questo articolo viene illustrato lo stesso codice.

## <a name="azure-storage"></a>Archiviazione di Azure

Archiviazione di Azure offre diversi tipi di archiviazione non relazionale, a seconda del tipo di dati a disposizione e dal modo in cui bisogna accedervi:

- Archiviazione tabelle: offre una risorsa di archiviazione conveniente e dai volumi elevati per i dati tabulari, per esempio i log di sensore e i log di diagnostica raccolti e così via.
- Archivio BLOB: fornisce una risorsa di archiviazione simile a un file per qualsiasi tipo di dati.

Il notebook di esempio mostra come lavorare con tabelle e BLOB, e come usare una firma di accesso condiviso per consentire l'accesso di sola lettura ai BLOB.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Cosmos DB offre un archivio NoSQL completamente indicizzato per i documenti JSON). Gli articoli seguenti forniscono una serie di modi diversi per lavorare con Cosmos DB da Python:

- [Compilare una API SQL con Python](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-python)
- [Compilare un'app Flask con l'API Azure Cosmos DB per MongoDB](https://docs.microsoft.com/azure/cosmos-db/create-mongodb-flask)
- [Creare un database a grafo usando Python e l'API Gremlin](https://docs.microsoft.com/azure/cosmos-db/create-graph-python)
- [Creare un'app Cassandra con Python e Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-cassandra-python)
- [Creare un'app dell'API Tabella con Python e Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-table-python)

Quando si lavora con Cosmos DB, è possibile usare la libreria [azure-cosmosdb-table](https://pypi.org/project/azure-cosmosdb-table/).

## <a name="other-azure-databases"></a>Altri database di Azure

Azure offre una serie di altri tipi di database che è possibile usare. Gli articoli seguenti forniscono materiale sussidiario per accedere a tali database da Python:

- [Database di Azure per PostgreSQL: Usare Python per connettersi ed eseguire query sui dati](https://docs.microsoft.com/azure/postgresql/connect-python)
- [Avvio rapido: usare Cache Redis di Azure con Python](https://docs.microsoft.com/azure/redis-cache/cache-python-get-started)
- [Database di Azure per MySQL: usare Python per connettersi ed eseguire query sui dati](https://docs.microsoft.com/azure/mysql/connect-python)
- [Data factory di Azure](https://azure.microsoft.com/services/data-factory/)
  - [Copiare la procedura guidata per Azure Data Factory](https://azure.microsoft.com/updates/code-free-copy-wizard-for-azure-data-factory/)

## <a name="next-steps"></a>Passaggi successivi

- [Procedura: lavorare con i file di dati di progetto](work-with-project-data-files.md)
