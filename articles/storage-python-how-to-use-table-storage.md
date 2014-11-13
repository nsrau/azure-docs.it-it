<properties urlDisplayName="Table Service" pageTitle="Come usare il servizio di archiviazione tabelle (Python) | Microsoft Azure" metaKeywords="Azure table Python, creating table Azure, deleting table Azure, inserting table Azure, querying table Azure" description="Informazioni su come usare il Servizio tabelle da Python per creare ed eliminare una tabella e per inserire, eliminare ed eseguire query su tabelle." metaCanonical="" services="storage" documentationCenter="Python" title="Come usare il servizio di archiviazione tabelle di Python" authors="huvalo" solutions="" manager="wpickett" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="09/19/2014" ms.author="huvalo" />

# Come usare il servizio di archiviazione tabelle di Python

Questa guida illustra come eseguire scenari comuni del servizio di archiviazione tabelle di Windows
Azure. Gli esempi sono scritti usando l'API
Python. Gli scenari presentati includono la **creazione e l'eliminazione di una
tabella, l'inserimento e l'esecuzione di query sulle entità in una tabella**. Per altre
informazioni sulle tabelle, vedere la sezione [Passaggi successivi][Passaggi successivi].

## Sommario

[Informazioni sul Servizio tabelle][Informazioni sul Servizio tabelle]
 [Concetti][Concetti]
 [Creare un account di archiviazione di Azure][Creare un account di archiviazione di Azure]
 [Procedura: Creare una tabella][Procedura: Creare una tabella]
 [Procedura: Aggiungere un'entità a una tabella][Procedura: Aggiungere un'entità a una tabella]
 [Procedura: Aggiornare un'entità][Procedura: Aggiornare un'entità]
 [Procedura: Modificare un gruppo di entità][Procedura: Modificare un gruppo di entità]
 [Procedura: Eseguire query su un'entità][Procedura: Eseguire query su un'entità]
 [Procedura: Eseguire query su un set di entità][Procedura: Eseguire query su un set di entità]
 [Procedura: Eseguire query su un subset di proprietà di entità][Procedura: Eseguire query su un subset di proprietà di entità]
 [Procedura: Eliminare un'entità][Procedura: Eliminare un'entità]
 [Procedura: Eliminare una tabella][Procedura: Eliminare una tabella]
 [Passaggi successivi][Passaggi successivi]

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

## <a name="create-account"> </a>Creare un account di archiviazione di Azure

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

**Nota:** se è necessario installare Python o le librerie client, vedere la [guida all'installazione di Python][guida all'installazione di Python].

## <a name="create-table"> </a>Come creare una tabella

L'oggetto **TableService** consente di usare i servizi tabelle. Il
codice seguente consente di creare un oggetto **TableService**. Aggiungere il codice seguente vicino
all'inizio del file Python da cui si vuole accedere all'archiviazione di Azure a livello di codice:

    from azure.storage import TableService, Entity

Il codice seguente consente di creare un oggetto **TableService** usando il nome dell'account di archiviazione e la chiave dell'account. Sostituire 'myaccount' e 'mykey' con l'account e la chiave reali.

    table_service = TableService(account_name='myaccount', account_key='mykey')

    table_service.create_table('tasktable')

## <a name="add-entity"> </a>Come aggiungere un'entità a una tabella

Per aggiungere un'entità, creare innanzitutto un dizionario che definisca i nomi e i valori della
proprietà dell'entità. Si noti che per ogni entità è necessario
specificare un oggetto **PartitionKey** e un oggetto **RowKey**. Si tratta di identificatori
univoci dell'entità e sono valori che possono essere interrogati molto più
velocemente di altre proprietà. Il sistema usa **PartitionKey** per
distribuire automaticamente le entità della tabella su molti nodi di archiviazione.
Le entità con la stessa chiave **PartitionKey** sono archiviate sullo stesso nodo.
**RowKey** è l'ID univoco dell'entità all'interno della partizione cui
appartiene.

Per aggiungere un'entità alla tabella, passare un oggetto
dizionario al metodo **insert\_entity**.

    task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the trash', 'priority' : 200}
    table_service.insert_entity('tasktable', task)

È anche possibile passare un'istanza della classe **Entity** al metodo **insert\_entity**.

    task = Entity()
    task.PartitionKey = 'tasksSeattle'
    task.RowKey = '2'
    task.description = 'Wash the car'
    task.priority = 100
    table_service.insert_entity('tasktable', task)

## <a name="update-entity"> </a>Come aggiornare un'entità

Questo codice indica come sostituire la versione precedente di un'entità
esistente con una versione aggiornata.

    task = {'description' : 'Take out the garbage', 'priority' : 250}
    table_service.update_entity('tasktable', 'tasksSeattle', '1', task)

Se l'entità da aggiornare non esiste, l'operazione di aggiornamento
non riuscirà. Se si vuole archiviare un'entità
indipendentemente dalla sua precedente esistenza, usare **insert\_or\_replace\_entity**.
Nell'esempio seguente, la prima chiamata sostituirà l'entità esistente. La seconda chiamata inserirà una nuova entità, poiché nella tabella non esiste alcuna entità con oggetto **PartitionKey** ed **RowKey**.

    task = {'description' : 'Take out the garbage again', 'priority' : 250}
    table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '1', task)

    task = {'description' : 'Buy detergent', 'priority' : 300}
    table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '3', task)

## <a name="change-entities"> </a>Come modificare un gruppo di entità

È talvolta consigliabile inviare più operazioni in un
batch per garantire l'elaborazione atomica da parte del server. A questo scopo, viene
usato il metodo **begin\_batch** su **TableService** e quindi viene chiamata normalmente
la serie di operazioni. Quando si vuole inviare il
batch, si chiamerà il metodo **commit\_batch**. Si noti che, affinché sia possibile modificarle come batch, tutte le entità devono trovarsi nella stessa partizione. Nell'esempio seguente vengono aggiunte due entità assieme in un batch.

    task10 = {'PartitionKey': 'tasksSeattle', 'RowKey': '10', 'description' : 'Go grocery shopping', 'priority' : 400}
    task11 = {'PartitionKey': 'tasksSeattle', 'RowKey': '11', 'description' : 'Clean the bathroom', 'priority' : 100}
    table_service.begin_batch()
    table_service.insert_entity('tasktable', task10)
    table_service.insert_entity('tasktable', task11)
    table_service.commit_batch()

## <a name="query-for-entity"> </a>Come eseguire query su un'entità

Per eseguire una query su un'entità in una tabella, usare il metodo **get\_entity** passando
i parametri **PartitionKey** e **RowKey**.

    task = table_service.get_entity('tasktable', 'tasksSeattle', '1')
    print(task.description)
    print(task.priority)

## <a name="query-set-entities"> </a>Come eseguire query su un set di entità

In questo esempio vengono individuate tutte le attività di Seattle in base a **PartitionKey**.

    tasks = table_service.query_entities('tasktable', "PartitionKey eq 'tasksSeattle'")
    for task in tasks:
        print(task.description)
        print(task.priority)

## <a name="query-entity-properties"> </a>Come eseguire query su un subset di proprietà di entità

Una query su una tabella può restituire solo poche proprietà da un'entità.
Questa tecnica, chiamata *proiezione*, riduce la larghezza di banda e può migliorare le prestazioni della
query, in particolare per entità di grandi dimensioni. Usare il parametro **select**
 e passare i nomi delle proprietà da inoltrare
al client.

La query nel codice seguente restituisce solo le **descrizioni** delle entità nella tabella.

*Si noti che il frammento seguente funziona solo su un servizio di archiviazione
cloud e non è supportato dall'emulatore di
archiviazione.*

    tasks = table_service.query_entities('tasktable', "PartitionKey eq 'tasksSeattle'", 'description')
    for task in tasks:
        print(task.description)

## <a name="delete-entity"> </a>Come eliminare un'entità

È possibile eliminare un'entità usando le relative chiavi di riga e di partizione.

    table_service.delete_entity('tasktable', 'tasksSeattle', '1')

## <a name="delete-table"> </a>Come eliminare una tabella

Nell'esempio di codice seguente viene illustrato come eliminare una tabella da un account di archiviazione.

    table_service.delete_table('tasktable')

## <a name="next-steps"> </a>Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base sull'archiviazione tabelle, visitare
i collegamenti seguenti per altre informazioni sulle attività di archiviazione più complesse.

-   Riferimento in MSDN: [Archiviazione e accesso ai dati in Azure][Archiviazione e accesso ai dati in Azure]
-   [Blog del team di archiviazione di Azure][Blog del team di archiviazione di Azure]

  [Passaggi successivi]: #next-steps
  [Informazioni sul Servizio tabelle]: #what-is
  [Concetti]: #concepts
  [Creare un account di archiviazione di Azure]: #create-account
  [Procedura: Creare una tabella]: #create-table
  [Procedura: Aggiungere un'entità a una tabella]: #add-entity
  [Procedura: Aggiornare un'entità]: #update-entity
  [Procedura: Modificare un gruppo di entità]: #change-entities
  [Procedura: Eseguire query su un'entità]: #query-for-entity
  [Procedura: Eseguire query su un set di entità]: #query-set-entities
  [Procedura: Eseguire query su un subset di proprietà di entità]: #query-entity-properties
  [Procedura: Eliminare un'entità]: #delete-entity
  [Procedura: Eliminare una tabella]: #delete-table
  [guida all'installazione di Python]: ../python-how-to-install/
  [Archiviazione e accesso ai dati in Azure]: http://msdn.microsoft.com/it-it/library/windowsazure/gg433040.aspx
  [Blog del team di archiviazione di Azure]: http://blogs.msdn.com/b/windowsazurestorage/
