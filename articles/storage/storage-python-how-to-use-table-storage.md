<properties
	pageTitle="Come usare l'archiviazione tabelle da Python | Microsoft Azure"
	description="Informazioni su come usare il Servizio tabelle da Python per creare ed eliminare una tabella e per inserire, eliminare ed eseguire query su tabelle."
	services="storage"
	documentationCenter="python"
	authors="emgerner-msft"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="12/11/2015"
	ms.author="emgerner"/>


# Come usare l'archiviazione tabelle da Python

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

## Panoramica

In questa guida viene illustrato come eseguire scenari comuni utilizzando il servizio di archiviazione tabelle di Azure. Gli esempi sono scritti in Python e utilizzano il [pacchetto di archiviazione Python di Azure][]. Gli scenari presentati includono la creazione ed eliminazione di una tabella, oltre all’inserimento di entità ed esecuzione di query sulle entità in una tabella.

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.NOTE]Se è necessario installare Python o il [pacchetto Python di Azure][], vedere la [guida all'installazione di Python](../python-how-to-install.md).


## Creare una tabella

L'oggetto **TableService** consente di utilizzare i servizi tabelle. Il codice seguente consente di creare un oggetto **TableService**. Aggiungere il codice seguente vicino all'inizio del file Python da cui si desidera accedere all'archiviazione di Azure a livello di codice:

	from azure.storage.table import TableService, Entity

Il codice seguente crea un oggetto **TableService** usando il nome dell'account di archiviazione e la chiave dell'account. Sostituire 'myaccount' e 'mykey' con l'account e la chiave reali.

	table_service = TableService(account_name='myaccount', account_key='mykey')

	table_service.create_table('tasktable')

## Aggiungere un'entità a una tabella

Per aggiungere un'entità, creare innanzitutto un dizionario che definisca i nomi e i valori della proprietà dell'entità. Si noti che per ogni entità è necessario specificare un oggetto **PartitionKey** e un oggetto **RowKey**. Questi sono identificatori univoci delle entità. È possibile eseguire query di questi valori molto più rapidamente rispetto a quanto sia possibile fare per altre proprietà. Il sistema utilizza **PartitionKey** per distribuire automaticamente le entità della tabella su molti nodi di archiviazione. Le entità con lo stesso oggetto **PartitionKey** vengono archiviate nello stesso nodo. **RowKey** è l'ID univoco dell'entità all'interno della partizione cui appartiene.

Per aggiungere un'entità alla tabella, passare un oggetto dizionario al metodo **insert\_entity**.

	task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the trash', 'priority' : 200}
	table_service.insert_entity('tasktable', task)

È anche possibile passare un'istanza della classe **Entity** al metodo **insert\_entity**.

	task = Entity()
	task.PartitionKey = 'tasksSeattle'
	task.RowKey = '2'
	task.description = 'Wash the car'
	task.priority = 100
	table_service.insert_entity('tasktable', task)

## Aggiornare un'entità

Questo codice indica come sostituire la versione precedente di un'entità esistente con una versione aggiornata.

	task = {'description' : 'Take out the garbage', 'priority' : 250}
	table_service.update_entity('tasktable', 'tasksSeattle', '1', task)

Se l'entità da aggiornare non esiste, l'operazione di aggiornamento non riuscirà. Se si desidera archiviare un'entità indipendentemente dalla sua precedente esistenza, utilizzare **insert\_or\_replace\_entity**. Nell'esempio seguente, la prima chiamata sostituirà l'entità esistente. La seconda chiamata inserirà una nuova entità, poiché nella tabella non esiste alcuna entità con oggetto **PartitionKey** ed **RowKey**.

	task = {'description' : 'Take out the garbage again', 'priority' : 250}
	table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '1', task)

	task = {'description' : 'Buy detergent', 'priority' : 300}
	table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '3', task)

## Modificare un gruppo di entità

È talvolta consigliabile inviare più operazioni in un batch per garantire l'elaborazione atomica da parte del server. Per realizzare ciò, viene utilizzato il metodo **begin\_batch** su **TableService** e quindi viene chiamata normalmente la serie di operazioni. Quando si desidera inviare il batch, si chiamerà **commit\_batch**. Si noti che, affinché sia possibile modificarle come batch, tutte le entità devono trovarsi nella stessa partizione. Nell'esempio seguente vengono aggiunte due entità assieme in un batch.

	task10 = {'PartitionKey': 'tasksSeattle', 'RowKey': '10', 'description' : 'Go grocery shopping', 'priority' : 400}
	task11 = {'PartitionKey': 'tasksSeattle', 'RowKey': '11', 'description' : 'Clean the bathroom', 'priority' : 100}
	table_service.begin_batch()
	table_service.insert_entity('tasktable', task10)
	table_service.insert_entity('tasktable', task11)
	table_service.commit_batch()

## Eseguire una query su un'entità

Per eseguire una query su un'entità in una tabella, utilizzare il metodo **get\_entity** passando i parametri **PartitionKey** e **RowKey**.

	task = table_service.get_entity('tasktable', 'tasksSeattle', '1')
	print(task.description)
	print(task.priority)

## Eseguire query su un set di entità

In questo esempio vengono individuate tutte le attività di Seattle basate su **PartitionKey**.

	tasks = table_service.query_entities('tasktable', "PartitionKey eq 'tasksSeattle'")
	for task in tasks:
		print(task.description)
		print(task.priority)

## Eseguire query su un subset di proprietà di entità

Mediante una query su una tabella è possibile recuperare solo alcune proprietà da un'entità. Questa tecnica, denominata *proiezione*, consente di ridurre la larghezza di banda e di migliorare le prestazioni della query, in particolare per entità di grandi dimensioni. Utilizzare il parametro **select** e passare i nomi delle proprietà da inoltrare al client.

La query nel codice seguente restituisce solo le descrizioni delle entità nella tabella.

[AZURE.NOTE]Il frammento seguente funziona solo su un servizio di archiviazione cloud. Non è supportato dall'emulatore di archiviazione.

	tasks = table_service.query_entities('tasktable', "PartitionKey eq 'tasksSeattle'", 'description')
	for task in tasks:
		print(task.description)

## Eliminare un'entità

È possibile eliminare un'entità usando le relative chiavi di partizione e di riga.

	table_service.delete_entity('tasktable', 'tasksSeattle', '1')

## Eliminare una tabella

Nell'esempio di codice seguente viene illustrato come eliminare una tabella da un account di archiviazione.

	table_service.delete_table('tasktable')

## Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base sull'archiviazione delle tabelle, visitare i collegamenti seguenti per altre informazioni sulle attività di archiviazione più complesse.

-   Vedere la documentazione MSDN [Archiviazione di Azure][].
-   Visitare il [Blog del team di Archiviazione di Azure][]

Per ulteriori informazioni, vedere anche il [Centro per sviluppatori di Python](/develop/python/).

[Blog del team di Archiviazione di Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[pacchetto Python di Azure]: https://pypi.python.org/pypi/azure
[pacchetto di archiviazione Python di Azure]: https://pypi.python.org/pypi/azure-storage

<!---HONumber=AcomDC_1217_2015-->