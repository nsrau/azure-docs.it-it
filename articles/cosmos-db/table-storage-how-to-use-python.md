---
title: Introduzione all'archiviazione tabelle di Azure con Python | Microsoft Docs
description: Archiviare dati strutturati nel cloud con il servizio di archiviazione tabelle di Azure, ovvero un archivio dati NoSQL.
services: cosmos-db
documentationcenter: python
author: mimig1
manager: jhubbard
editor: tysonn
ms.assetid: 7ddb9f3e-4e6d-4103-96e6-f0351d69a17b
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/08/2018
ms.author: mimig
ms.openlocfilehash: 455479c9eb77093dd5611263fe5bdcf699b9d026
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="get-started-with-azure-table-storage-using-python"></a>Introduzione all'archiviazione tabelle di Azure con Python

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

L'archiviazione tabelle di Azure è un servizio che archivia dati NoSQL strutturati nel cloud, mettendo a disposizione un archivio di chiavi/attributi senza schema. Poiché l'archiviazione tabelle è senza schema, è facile adattare i dati con il variare delle esigenze dell'applicazione. L'accesso ai dati dell'archiviazione tabelle è rapido ed economico per molti tipi di applicazioni e presenta costi generalmente più bassi rispetto alle soluzioni SQL tradizionali per volumi di dati simili.

È possibile usare l'archiviazione tabelle per archiviare set di dati flessibili, ad esempio i dati utente per le applicazioni Web, le rubriche, le informazioni sui dispositivi o altri tipi di metadati richiesti dal servizio. In una tabella possono essere archiviate il numero desiderato di tabelle e un account di archiviazione può contenere un numero qualsiasi di tabelle, fino a che non viene raggiunto il limite di capacità dell'account di archiviazione.

### <a name="about-this-tutorial"></a>Informazioni sull'esercitazione
Questa esercitazione illustra come usare [Azure Cosmos DB Table SDK per Python](https://pypi.python.org/pypi/azure-cosmosdb-table/) all'interno di scenari comuni di archiviazione tabelle di Azure. Il nome dell'SDK indica che è destinato all'uso con Azure Cosmos DB. L'SDK funziona tuttavia sia con Azure Cosmos DB sia con l'archiviazione tabelle di Azure, ma ogni servizio ha un endpoint univoco. Questi scenari vengono esaminati tramite esempi Python che illustrano come:
* Creare ed eliminare tabelle
* Inserire ed eseguire query sulle entità
* Modificare entità

Mentre si lavora agli scenari di questa esercitazione, è possibile vedere le [informazioni di riferimento sull'API Azure Cosmos DB SDK per Python](https://azure.github.io/azure-cosmosdb-python/).

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione sono necessari gli elementi seguenti:

- [Python](https://www.python.org/downloads/) 2.7, 3.3, 3.4, 3.5 o 3.6
- [Azure Cosmos DB Table SDK per Python](https://pypi.python.org/pypi/azure-cosmosdb-table/) Questo SDK si connette sia all'archiviazione tabelle di Azure sia all'API Table di Azure Cosmos DB.
- [Account di archiviazione di Azure](https://docs.microsoft.com/en-us/azure/storage/common/storage-create-storage-account#create-a-storage-account) o [account Azure Cosmos DB](https://azure.microsoft.com/en-us/try/cosmosdb/)

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

## <a name="create-an-azure-service-account"></a>Creare un account del servizio di Azure

È possibile usare tabelle con l'archiviazione tabelle di Azure o con Azure Cosmos DB. Per altre informazioni sulle differenze tra i servizi, vedere [Offerte per tabelle](table-introduction.md#table-offerings). Sarà necessario creare un account per il servizio che si intende usare. 

### <a name="create-an-azure-storage-account"></a>Creare un account di archiviazione di Azure
Il modo più semplice per creare il primo account di archiviazione di Azure consiste nell'usare il [portale di Azure](https://portal.azure.com). Per altre informazioni, vedere [Creare un account di archiviazione](../storage/common/storage-create-storage-account.md#create-a-storage-account).

È possibile anche creare un account di archiviazione di Azure usando [Azure PowerShell](../storage/common/storage-powershell-guide-full.md) o l'[interfaccia della riga di comando di Azure](../storage/common/storage-azure-cli.md).

Se si preferisce non creare un account di archiviazione in questa fase, è anche possibile usare l'emulatore di archiviazione di Azure per eseguire e testare il codice in un ambiente locale. Per altre informazioni, vedere [Usare l'emulatore di archiviazione di Azure per sviluppo e test](../storage/common/storage-use-emulator.md).

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Creare un account per l'API Table di Azure Cosmos DB

Per istruzioni per la creazione di un account per l'API Table di Azure Cosmos DB, vedere [Creare un account per un'API di tabella](create-table-dotnet.md#create-a-database-account).

## <a name="install-the-azure-cosmos-db-table-sdk-for-python"></a>Installare Azure Cosmos DB Table SDK per Python

Dopo aver creato un account di archiviazione, il passaggio successivo consiste nell'installare [Microsoft Azure Cosmos DB Table SDK per Python](https://pypi.python.org/pypi/azure-cosmosdb-table/). Per informazioni dettagliate sull'installazione dell'SDK, vedere il file [README.rst](https://github.com/Azure/azure-cosmosdb-python/blob/master/azure-cosmosdb-table/README.rst) nel repository di Cosmos DB Table SDK per Python su GitHub.

## <a name="import-the-tableservice-and-entity-classes"></a>Importare le classi TableService ed Entity

Per usare le entità nel servizio tabelle di Azure in Python, è necessario usufruire delle classi [TableService](https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html) ed [Entity][py_Entity]. Aggiungere il codice seguente nella parte iniziale del file Python per importare entrambi gli elementi:

```python
from azure.cosmosdb.table.tableservice import TableService
from azure.cosmosdb.table.models import Entity
```

## <a name="connect-to-azure-table-service"></a>Connettersi al servizio tabelle di Azure

Per connettersi al servizio Tabella di archiviazione di Azure, creare un oggetto [TableService](https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html) e passare il nome dell'account di archiviazione e la chiave dell'account. Sostituire `myaccount` e `mykey` con il nome e la chiave dell'account.

```python
table_service = TableService(account_name='myaccount', account_key='mykey')
```

## <a name="connect-to-azure-cosmos-db"></a>Connettersi ad Azure Cosmos DB

Per connettersi ad Azure Cosmos DB, copiare la stringa di connessione primaria dal portale di Azure e creare un oggetto [TableService](https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html) usando la stringa di connessione copiata:

```python
table_service = TableService(connection_string='DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;TableEndpoint=myendpoint;)
```

## <a name="create-a-table"></a>Creare una tabella

Chiamare [create_table][py_create_table] per creare la tabella.

```python
table_service.create_table('tasktable')
```

## <a name="add-an-entity-to-a-table"></a>Aggiungere un'entità a una tabella

Per aggiungere un'entità, creare prima un oggetto che rappresenta l'entità e quindi passare l'oggetto al [metodo TableService.insert_entity][py_TableService]. L'oggetto entità può essere un dizionario o un oggetto di tipo [Entity][py_Entity] e definisce i nomi e i valori delle proprietà dell'entità. Ogni entità deve includere le proprietà [PartitionKey e RowKey](#partitionkey-and-rowkey) necessarie, oltre a tutte le altre proprietà definite per l'entità.

Questo esempio crea un oggetto dizionario che rappresenta un'entità e quindi passa l'oggetto al metodo [insert_entity][py_insert_entity] per aggiungerlo alla tabella:

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001', 'description' : 'Take out the trash', 'priority' : 200}
table_service.insert_entity('tasktable', task)
```

Questo esempio crea un oggetto [Entity][py_Entity] e quindi lo passa al metodo [insert_entity][py_insert_entity] per aggiungerlo alla tabella:

```python
task = Entity()
task.PartitionKey = 'tasksSeattle'
task.RowKey = '002'
task.description = 'Wash the car'
task.priority = 100
table_service.insert_entity('tasktable', task)
```

### <a name="partitionkey-and-rowkey"></a>PartitionKey e RowKey

È necessario specificare entrambe le proprietà **PartitionKey** e **RowKey** per ogni entità. Si tratta degli identificatori univoci delle entità, che insieme formano la chiave primaria di un'entità. È possibile eseguire query usando questi valori in modo molto più rapido rispetto a quanto è possibile fare su qualsiasi altra proprietà dell'entità, perché solo queste proprietà sono indicizzate.

Il servizio tabelle usa **PartitionKey** per distribuire in modo intelligente le entità della tabella nei nodi di archiviazione. Le entità con la stessa proprietà **PartitionKey** vengono archiviate nello stesso nodo. **RowKey** è l'ID univoco dell'entità all'interno della partizione a cui appartiene.

## <a name="update-an-entity"></a>Aggiornare un'entità

Per aggiornare tutti i valori delle proprietà di un'entità, chiamare il metodo [update_entity][py_update_entity]. Questo esempio mostra come sostituire un'entità esistente con una versione aggiornata:

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001', 'description' : 'Take out the garbage', 'priority' : 250}
table_service.update_entity('tasktable', task)
```

Se l'entità da aggiornare non esiste già, l'operazione di aggiornamento non riuscirà. Se si vuole archiviare un'entità indipendentemente dal fatto che esista o meno, usare [insert_or_replace_entity][py_insert_or_replace_entity]. Nell'esempio seguente, la prima chiamata sostituirà l'entità esistente. La seconda chiamata inserisce una nuova entità, perché nella tabella non c'è alcuna entità con le proprietà PartitionKey e RowKey specificate.

```python
# Replace the entity created earlier
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001', 'description' : 'Take out the garbage again', 'priority' : 250}
table_service.insert_or_replace_entity('tasktable', task)

# Insert a new entity
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '003', 'description' : 'Buy detergent', 'priority' : 300}
table_service.insert_or_replace_entity('tasktable', task)
```

> [!TIP]
> Il metodo [update_entity][py_update_entity] sostituisce tutte le proprietà e i valori di un'entità esistente e può anche essere usato per rimuovere le proprietà da un'entità esistente. È possibile usare il metodo [merge_entity][py_merge_entity] per aggiornare un'entità esistente con i valori delle proprietà nuovi o modificati senza sostituire completamente l'entità.

## <a name="modify-multiple-entities"></a>Modificare più entità

Per garantire l'elaborazione atomica delle richieste da parte del servizio tabelle, è possibile inviare più operazioni insieme in un batch. Usare prima di tutto la classe [TableBatch][py_TableBatch] per aggiungere più operazioni a un singolo batch. Chiamare quindi [TableService][py_TableService].[commit_batch][py_commit_batch] per inviare le operazioni in un'operazione atomica. Tutte le entità da modificare in batch devono trovarsi nella stessa partizione.

Questo esempio aggiunge due entità in un batch:

```python
from azure.cosmosdb.table.tablebatch import TableBatch
batch = TableBatch()
task004 = {'PartitionKey': 'tasksSeattle', 'RowKey': '004', 'description' : 'Go grocery shopping', 'priority' : 400}
task005 = {'PartitionKey': 'tasksSeattle', 'RowKey': '005', 'description' : 'Clean the bathroom', 'priority' : 100}
batch.insert_entity(task004)
batch.insert_entity(task005)
table_service.commit_batch('tasktable', batch)
```

I batch possono essere usati anche con la sintassi di gestione contesto:

```python
task006 = {'PartitionKey': 'tasksSeattle', 'RowKey': '006', 'description' : 'Go grocery shopping', 'priority' : 400}
task007 = {'PartitionKey': 'tasksSeattle', 'RowKey': '007', 'description' : 'Clean the bathroom', 'priority' : 100}

with table_service.batch('tasktable') as batch:
    batch.insert_entity(task006)
    batch.insert_entity(task007)
```

## <a name="query-for-an-entity"></a>Eseguire una query su un'entità

Per eseguire una query per un'entità in una tabella, passare le relative proprietà PartitionKey e RowKey al metodo [TableService][py_TableService].[get_entity][py_get_entity].

```python
task = table_service.get_entity('tasktable', 'tasksSeattle', '001')
print(task.description)
print(task.priority)
```

## <a name="query-a-set-of-entities"></a>Eseguire query su un set di entità

È possibile eseguire query per un set di entità specificando una stringa di filtro con il parametro **filter**. Questo esempio trova tutte le attività di Seattle applicando un filtro a PartitionKey:

```python
tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'")
for task in tasks:
    print(task.description)
    print(task.priority)
```

## <a name="query-a-subset-of-entity-properties"></a>Eseguire query su un subset di proprietà di entità

È anche possibile limitare le proprietà restituite per ogni entità in una query. Questa tecnica, denominata *proiezione*, consente di ridurre la larghezza di banda e di migliorare le prestazioni di query, in particolare per entità o set di risultati di grandi dimensioni. Usare il parametro **select** e passare i nomi delle proprietà da restituire al client.

La query nel codice seguente restituisce solo le descrizioni delle entità nella tabella.

> [!NOTE]
> Il frammento di codice seguente funziona solo in Archiviazione di Azure. Non è supportato dall'emulatore di archiviazione.

```python
tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'", select='description')
for task in tasks:
    print(task.description)
```

## <a name="delete-an-entity"></a>Eliminare un'entità

Eliminare un'entità passando le relative proprietà **PartitionKey** e **RowKey** al metodo [delete_entity][py_delete_entity].

```python
table_service.delete_entity('tasktable', 'tasksSeattle', '001')
```

## <a name="delete-a-table"></a>Eliminare una tabella

Se una tabella o una delle entità al suo interno non è più necessaria, chiamare il metodo [delete_table][py_delete_table] per eliminare definitivamente la tabella da Archiviazione di Azure.

```python
table_service.delete_table('tasktable')
```

## <a name="next-steps"></a>Passaggi successivi

* [Domande frequenti - Sviluppare con l'API Tabelle](https://docs.microsoft.com/en-us/azure/cosmos-db/faq#develop-with-the-table-api)
* [Informazioni di riferimento sull'API di Azure Cosmos DB SDK per Python](https://azure.github.io/azure-cosmosdb-python/)
* [Centro per sviluppatori Python](https://azure.microsoft.com/develop/python/)
* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md): un'applicazione gratuita multipiattaforma per lavorare in modo visivo con i dati di Archiviazione di Azure in Windows, macOS e Linux.
* [Working with Python in Visual Studio (Windows)](https://docs.microsoft.com/en-us/visualstudio/python/overview-of-python-tools-for-visual-studio) (Utilizzo di Python in Visual Studio - Windows)

[py_commit_batch]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_create_table]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_delete_entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_delete_table]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_Entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.models.html
[py_get_entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_insert_entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_insert_or_replace_entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_TableService]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_TableBatch]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tablebatch.html
[py_merge_entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_update_entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
