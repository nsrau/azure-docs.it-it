---
title: Introduzione all'archiviazione di Azure e ai servizi relativi a Visual Studio (progetti WebJob)
description: "Informazioni su come iniziare a usare l’archiviazione tabella di Azure in un progetto WebJobs di Azure in Visual Studio dopo aver eseguito la connessione a un account di archiviazione con i servizi connessi di Visual Studio."
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: 061a6c46-0592-4e5d-aced-ab7498481cde
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: kraigb
ms.openlocfilehash: 4e0c77e08bff971277a09d6066f259db84617616
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="getting-started-with-azure-storage-azure-webjob-projects"></a>Introduzione all'Archiviazione di Azure (progetti Azure WebJob)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Panoramica
Questo articolo fornisce esempi di codice C# che illustrano come usare Azure WebJobs SDK versione 1.x con il servizio di archiviazione tabelle di Azure. Gli esempi di codice usano [WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) versione 1.x.

Il servizio di archiviazione tabelle di Azure consente di archiviare grandi quantità di dati strutturati. Il servizio è un datastore NoSQL che accetta chiamate autenticate dall'interno e dall'esterno del cloud di Azure. Le tabelle di Azure sono ideali per l'archiviazione di dati strutturati non relazionali.  Per altre informazioni, vedere [Introduzione all'archiviazione tabelle di Azure con .NET](../cosmos-db/table-storage-how-to-use-dotnet.md#create-a-table) .

Alcuni dei frammenti di codice illustrano l'attributo **Tabella** usato nelle funzioni chiamate manualmente e non mediante uno degli attributi del trigger.

## <a name="how-to-add-entities-to-a-table"></a>Come aggiungere entità a una tabella
Per aggiungere entità a una tabella, usare l'attributo **Table** con un parametro **ICollector<T>** o **IAsyncCollector<T>** dove **T** specifica lo schema delle entità da aggiungere. Il costruttore dell'attributo accetta un parametro di stringa che specifica il nome della tabella.

L’esempio di codice seguente aggiunge le entità **Persona** a una tabella denominata *Ingresso*.

        [NoAutomaticTrigger]
        public static void IngressDemo(
            [Table("Ingress")] ICollector<Person> tableBinding)
        {
            for (int i = 0; i < 100000; i++)
            {
                tableBinding.Add(
                    new Person() {
                        PartitionKey = "Test",
                        RowKey = i.ToString(),
                        Name = "Name" }
                    );
            }
        }

In genere il tipo usato con **ICollector** deriva da **TableEntity** o implementa **ITableEntity**, ma non obbligatoriamente. Ognuna delle seguenti classi **Person** funziona con il codice illustrato nel precedente metodo **Ingress**.

        public class Person : TableEntity
        {
            public string Name { get; set; }
        }

        public class Person
        {
            public string PartitionKey { get; set; }
            public string RowKey { get; set; }
            public string Name { get; set; }
        }

Se si desidera usare direttamente l’API dell'account di archiviazione di Azure, è anche possibile aggiungere un parametro **CloudStorageAccount** alla firma del metodo.

## <a name="real-time-monitoring"></a>Monitoraggio in tempo reale
Poiché spesso le funzioni di ingresso ai dati (Ingress) elaborano volumi elevati di informazioni, il dashboard di WebJobs SDK fornisce dati di monitoraggio in tempo reale. La sezione **Invocation Log** indica se la funzione è ancora in esecuzione.

![Funzione Ingress in esecuzione](./media/vs-storage-webjobs-getting-started-tables/ingressrunning.png)

La pagina **Invocation Details** restituisce lo stato di avanzamento della funzione (numero di entità scritte) mentre è in esecuzione e offre la possibilità di interromperla.

![Funzione Ingress in esecuzione](./media/vs-storage-webjobs-getting-started-tables/ingressprogress.png)

Al termine della funzione, la pagina **Invocation Details** indica il numero di righe scritte.

![Funzione Ingress completata](./media/vs-storage-webjobs-getting-started-tables/ingresssuccess.png)

## <a name="how-to-read-multiple-entities-from-a-table"></a>Come leggere più entità da una tabella
Per leggere una tabella, usare l'attributo **Tabella** con un parametro **IQueryable<T>** in cui il tipo **T** deriva da **TableEntity** o implementa **ITableEntity**.

Il seguente esempio di codice legge e registra tutte le righe dalla tabella **Ingresso** :

        public static void ReadTable(
            [Table("Ingress")] IQueryable<Person> tableBinding,
            TextWriter logger)
        {
            var query = from p in tableBinding select p;
            foreach (Person person in query)
            {
                logger.WriteLine("PK:{0}, RK:{1}, Name:{2}",
                    person.PartitionKey, person.RowKey, person.Name);
            }
        }

### <a name="how-to-read-a-single-entity-from-a-table"></a>Come leggere una singola entità da una tabella
È disponibile un costruttore dell'attributo **Tabella** con due parametri aggiuntivi che consentono di specificare la chiave di partizione e la chiave di riga quando si desidera l'associazione a un'entità di tabella singola.

Il seguente esempio di codice legge una riga della tabella per un'entità **Persona** basata sui valori della chiave di partizione e della chiave di riga ricevuti in un messaggio di coda:  

        public static void ReadTableEntity(
            [QueueTrigger("inputqueue")] Person personInQueue,
            [Table("persontable","{PartitionKey}", "{RowKey}")] Person personInTable,
            TextWriter logger)
        {
            if (personInTable == null)
            {
                logger.WriteLine("Person not found: PK:{0}, RK:{1}",
                        personInQueue.PartitionKey, personInQueue.RowKey);
            }
            else
            {
                logger.WriteLine("Person found: PK:{0}, RK:{1}, Name:{2}",
                        personInTable.PartitionKey, personInTable.RowKey, personInTable.Name);
            }
        }


La classe **Person** in questo esempio non deve implementare **ITableEntity**.

## <a name="how-to-use-the-net-storage-api-directly-to-work-with-a-table"></a>Come usare l'API di archiviazione .NET direttamente con una tabella
È possibile usare l'attributo **Tabella** anche con un oggetto **CloudTable** per una maggiore flessibilità nell'uso di una tabella.

Il seguente esempio di codice usa un oggetto **CloudTable** per aggiungere una singola entità alla tabella *Ingresso* .

        public static void UseStorageAPI(
            [Table("Ingress")] CloudTable tableBinding,
            TextWriter logger)
        {
            var person = new Person()
                {
                    PartitionKey = "Test",
                    RowKey = "100",
                    Name = "Name"
                };
            TableOperation insertOperation = TableOperation.Insert(person);
            tableBinding.Execute(insertOperation);
        }

Per altre informazioni su come usare l'oggetto **CloudTable** , vedere [Introduzione all'archiviazione tabelle di Azure con .NET](../storage/storage-dotnet-how-to-use-tables.md).

## <a name="related-topics-covered-by-the-queues-how-to-article"></a>Argomenti correlati trattati nell'articolo delle procedure sulle code
Per informazioni su come gestire l'elaborazione di tabelle attivata da un messaggio di coda o per scenari di WebJobs SDK non legati all'elaborazione di tabelle, vedere [Introduzione all'archiviazione di accodamento di Azure e ai servizi relativi a Visual Studio (progetti WebJob)](../storage/vs-storage-webjobs-getting-started-queues.md).

## <a name="next-steps"></a>Passaggi successivi
Questo articolo ha fornito esempi di codice che illustrano come gestire scenari comuni per l'uso di tabelle di Azure. Per altre informazioni su come usare Processi Web di Azure e WebJobs SDK, vedere le [risorse di documentazione di Processi Web di Azure](http://go.microsoft.com/fwlink/?linkid=390226).

