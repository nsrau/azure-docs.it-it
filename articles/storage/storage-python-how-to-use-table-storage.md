---
title: Come usare l&quot;archiviazione tabelle di Azure con Python | Microsoft Docs
description: Archiviare dati non strutturati nel cloud con il servizio di archiviazione tabelle di Azure, ovvero un archivio dati NoSQL.
services: storage
documentationcenter: python
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 7ddb9f3e-4e6d-4103-96e6-f0351d69a17b
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 05/16/2017
ms.author: marsma
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: c310a52182bbc3cf44ed4dc6a04e97aa59200a64
ms.contentlocale: it-it
ms.lasthandoff: 05/12/2017


---
# <a name="how-to-use-table-storage-in-python"></a>Come usare l'archiviazione tabelle in Python

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-langsoon-tip-include](../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

Questa guida illustra come eseguire scenari comuni di archiviazione tabelle di Azure in Python usando l'[SDK di Archiviazione di Microsoft Azure per Python](https://github.com/Azure/azure-storage-python). Gli scenari presentati includono la creazione e l'eliminazione di una tabella, oltre che l'inserimento di entità e l'esecuzione di query sulle entità.

Mentre si lavora agli scenari di questa esercitazione, è possibile vedere le [informazioni di riferimento sull'API dell'SDK di Archiviazione di Azure per Python](https://azure-storage.readthedocs.io/en/latest/index.html).

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="install-the-azure-storage-sdk-for-python"></a>Installare l'SDK di Archiviazione di Azure per Python

Dopo aver creato un account di archiviazione, il passaggio successivo consiste nell'[installare l'SDK di Archiviazione di Microsoft Azure per Python](https://github.com/Azure/azure-storage-python). Per informazioni dettagliate sull'installazione dell'SDK, vedere il file [README.rst](https://github.com/Azure/azure-storage-python/blob/master/README.rst) nell'archivio relativo all'SDK di Archiviazione per Python su GitHub.

## <a name="create-a-table"></a>Creare una tabella

Per usare il servizio tabelle di Azure in Python, è necessario importare il modulo [TableService][py_TableService]. Poiché si useranno entità tabella, è necessaria anche la classe [Entity][py_Entity]. Aggiungere il codice seguente nella parte iniziale del file Python per importare entrambi gli elementi:

```python
from azure.storage.table import TableService, Entity
```

Creare un oggetto [TableService][py_TableService] passando il nome dell'account di archiviazione e la chiave dell'account. Sostituire `myaccount` e `mykey` con il nome e la chiave dell'account e chiamare [create_table][py_create_table] per creare la tabella in Archiviazione di Azure.

```python
table_service = TableService(account_name='myaccount', account_key='mykey')

table_service.create_table('tasktable')
```

## <a name="add-an-entity-to-a-table"></a>Aggiungere un'entità a una tabella

Per aggiungere un'entità, creare innanzitutto un oggetto che rappresenta l'entità, quindi passare l'oggetto al metodo [TableService][py_TableService].[insert_entity][py_insert_entity]. L'oggetto entità può essere un dizionario o un oggetto di tipo [Entity][py_Entity] e definisce i nomi e i valori delle proprietà dell'entità. Ogni entità deve includere le proprietà [PartitionKey e RowKey](#partitionkey-and-rowkey) necessarie, oltre a tutte le altre proprietà definite per l'entità.

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
from azure.storage.table import TableBatch
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

Eliminare un'entità passando le relative proprietà PartitionKey e RowKey al metodo [delete_entity][py_delete_entity].

```python
table_service.delete_entity('tasktable', 'tasksSeattle', '001')
```

## <a name="delete-a-table"></a>Eliminare una tabella

Se una tabella o una delle entità al suo interno non è più necessaria, chiamare il metodo [delete_table][py_delete_table] per eliminare definitivamente la tabella da Archiviazione di Azure.

```python
table_service.delete_table('tasktable')
```

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni di riferimento sull'API dell'SDK di Archiviazione di Azure per Python](https://azure-storage.readthedocs.io/en/latest/index.html)
* [SDK di Archiviazione di Azure per Python](https://github.com/Azure/azure-storage-python)
* [Centro per sviluppatori Python](https://azure.microsoft.com/develop/python/)
* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md): un'applicazione gratuita multipiattaforma per lavorare in modo visivo con i dati di Archiviazione di Azure in Windows, macOS e Linux.

[py_commit_batch]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.commit_batch
[py_create_table]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.create_table
[py_delete_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.delete_entity
[py_delete_table]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.delete_table
[py_Entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.models.html#azure.storage.table.models.Entity
[py_get_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.get_entity
[py_insert_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.insert_entity
[py_insert_or_replace_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.insert_or_replace_entity
[py_merge_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.merge_entity
[py_update_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.update_entity
[py_TableService]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html
[py_TableBatch]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tablebatch.html#azure.storage.table.tablebatch.TableBatch

