---
title: Come usare l&quot;archiviazione tabelle da Python | Microsoft Docs
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
ms.date: 12/08/2016
ms.author: marsma
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 34d075658514045e28d6a784c579528bb3eac376
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017


---
# <a name="how-to-use-table-storage-from-python"></a>Come usare l'archiviazione tabelle da Python
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-langsoon-tip-include](../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

## <a name="overview"></a>Panoramica
In questa guida viene illustrato come eseguire scenari comuni utilizzando il servizio di archiviazione tabelle di Azure. Gli esempi sono scritti in Python e usano [Microsoft Azure Storage SDK per Python](https://github.com/Azure/azure-storage-python). Gli scenari presentati includono la creazione ed eliminazione di una tabella, oltre all’inserimento di entità ed esecuzione di query sulle entità in una tabella.

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-table"></a>Creare una tabella
L'oggetto **TableService** consente di utilizzare i servizi tabelle. Il codice seguente consente di creare un oggetto **TableService** . Aggiungere il codice seguente vicino all'inizio del file Python da cui si desidera accedere all'archiviazione di Azure a livello di codice:

```python
from azure.storage.table import TableService, Entity
```

Il codice seguente crea un oggetto **TableService** usando il nome dell'account di archiviazione e la chiave dell'account.  Sostituire 'myaccount' e 'mykey' con l'account e la chiave da usare.

```python
table_service = TableService(account_name='myaccount', account_key='mykey')

table_service.create_table('tasktable')
```

## <a name="add-an-entity-to-a-table"></a>Aggiungere un'entità a una tabella
Per aggiungere un'entità, creare prima un dizionario o un'entità che definisca i nomi e i valori della proprietà dell'entità. Si noti che per ogni entità è necessario specificare un oggetto **PartitionKey** e un oggetto **RowKey**. Questi sono identificatori univoci delle entità. Con questi valori è possibile eseguire query molto più rapidamente rispetto a quanto sia possibile fare per altre proprietà. Il sistema utilizza **PartitionKey** per distribuire automaticamente le entità della tabella su molti nodi di archiviazione.
Le entità con lo stesso oggetto **PartitionKey** vengono archiviate nello stesso nodo. **RowKey** è l'ID univoco dell'entità all'interno della partizione cui appartiene.

Per aggiungere un'entità alla tabella, passare un oggetto dizionario al metodo **insert\_entity**.

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the trash', 'priority' : 200}
table_service.insert_entity('tasktable', task)
```

È anche possibile passare un'istanza della classe **Entity** al metodo **insert\_entity**.

```python
task = Entity()
task.PartitionKey = 'tasksSeattle'
task.RowKey = '2'
task.description = 'Wash the car'
task.priority = 100
table_service.insert_entity('tasktable', task)
```

## <a name="update-an-entity"></a>Aggiornare un'entità
Questo codice indica come sostituire la versione precedente di un'entità esistente con una versione aggiornata.

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the garbage', 'priority' : 250}
table_service.update_entity('tasktable', 'tasksSeattle', '1', task, content_type='application/atom+xml')
```

Se l'entità da aggiornare non esiste, l'operazione di aggiornamento non riuscirà. Se si vuole archiviare un'entità indipendentemente dalla sua precedente esistenza, usare **insert\_or\_replace_entity**.
Nell'esempio seguente, la prima chiamata sostituirà l'entità esistente. La seconda chiamata inserirà una nuova entità, poiché nella tabella non esiste alcuna entità con oggetto **PartitionKey** ed **RowKey**.

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the garbage again', 'priority' : 250}
table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '1', task, content_type='application/atom+xml')

task = {'PartitionKey': 'tasksSeattle', 'RowKey': '3', 'description' : 'Buy detergent', 'priority' : 300}
table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '1', task, content_type='application/atom+xml')
```

## <a name="change-a-group-of-entities"></a>Modificare un gruppo di entità
È talvolta consigliabile inviare più operazioni in un batch per garantire l'elaborazione atomica da parte del server. A tale scopo, usare la classe **TableBatch** . Quando si desidera inviare il batch, si chiamerà **commit\_batch**. Si noti che, affinché sia possibile modificarle come batch, tutte le entità devono trovarsi nella stessa partizione. Nell'esempio seguente vengono aggiunte due entità assieme in un batch.

```python
from azure.storage.table import TableBatch
batch = TableBatch()
task10 = {'PartitionKey': 'tasksSeattle', 'RowKey': '10', 'description' : 'Go grocery shopping', 'priority' : 400}
task11 = {'PartitionKey': 'tasksSeattle', 'RowKey': '11', 'description' : 'Clean the bathroom', 'priority' : 100}
batch.insert_entity(task10)
batch.insert_entity(task11)
table_service.commit_batch('tasktable', batch)
```

I batch possono essere usati anche con la sintassi di gestione contesto:

```python
task12 = {'PartitionKey': 'tasksSeattle', 'RowKey': '12', 'description' : 'Go grocery shopping', 'priority' : 400}
task13 = {'PartitionKey': 'tasksSeattle', 'RowKey': '13', 'description' : 'Clean the bathroom', 'priority' : 100}

with table_service.batch('tasktable') as batch:
    batch.insert_entity(task12)
    batch.insert_entity(task13)
```

## <a name="query-for-an-entity"></a>Eseguire una query su un'entità
Per eseguire una query su un'entità in una tabella, usare il metodo **get\_entity** passando i parametri **PartitionKey** e **RowKey**.

```python
task = table_service.get_entity('tasktable', 'tasksSeattle', '1')
print(task.description)
print(task.priority)
```

## <a name="query-a-set-of-entities"></a>Eseguire query su un set di entità
In questo esempio vengono individuate tutte le attività di Seattle basate su **PartitionKey**.

```python
tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'")
for task in tasks:
    print(task.description)
    print(task.priority)
```

## <a name="query-a-subset-of-entity-properties"></a>Eseguire query su un subset di proprietà di entità
Mediante una query su una tabella è possibile recuperare solo alcune proprietà da un'entità.
Questa tecnica, denominata *proiezione*, consente di ridurre la larghezza di banda e di migliorare le prestazioni della query, in particolare per entità di grandi dimensioni. Utilizzare il parametro **select** e passare i nomi delle proprietà da inoltrare al client.

La query nel codice seguente restituisce solo le descrizioni delle entità nella tabella.

> [!NOTE]
> Il frammento di codice seguente funziona solo in Archiviazione di Azure. Non è supportato dall'emulatore di archiviazione.
>
>

```python
tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'", select='description')
for task in tasks:
    print(task.description)
```

## <a name="delete-an-entity"></a>Eliminare un'entità
È possibile eliminare un'entità usando le relative chiavi di partizione e di riga.

```python
table_service.delete_entity('tasktable', 'tasksSeattle', '1')
```

## <a name="delete-a-table"></a>Eliminare una tabella
Nell'esempio di codice seguente viene illustrato come eliminare una tabella da un account di archiviazione.

```python
table_service.delete_table('tasktable')
```

## <a name="next-steps"></a>Passaggi successivi

* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) è un'app autonoma gratuita di Microsoft che consente di rappresentare facilmente dati di Archiviazione di Azure in Windows, macOS e Linux.
* [Centro per sviluppatori Python](/develop/python/)
* [API REST dei servizi di archiviazione di Azure](http://msdn.microsoft.com/library/azure/dd179355)
* [Microsoft Azure Storage SDK per Python](https://github.com/Azure/azure-storage-python)
