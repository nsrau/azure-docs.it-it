---
title: Unità richiesta e velocità effettiva in Azure Cosmos DB
description: Informazioni su come specificare e stimare i requisiti relativi alle unità richiesta in Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: a0058bf309e0ff4fbe687731d676e907d1c3fd82
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383101"
---
# <a name="request-units-in-azure-cosmos-db"></a>Unità richiesta in Azure Cosmos DB

Con Azure Cosmos DB, viene addebitato un costo per la velocità effettiva sottoposta a provisioning e per le risorse di archiviazione utilizzate su base oraria. Throughput must be provisioned to ensure that sufficient system resources are available for your Azure Cosmos database at all times. You need enough resources to meet or exceed the [Azure Cosmos DB SLAs](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/).

Azure Cosmos DB supporta un'ampia gamma di API, come SQL, MongoDB, Cassandra, Gremlin e Tabella. Ogni API ha il proprio set di operazioni di database, da semplici operazioni di lettura e scrittura puntuali a query complesse. Ogni operazione di database utilizza le risorse di sistema a seconda della complessità. 

The cost of all database operations is normalized by Azure Cosmos DB and is expressed by *Request Units* (or RUs, for short). L'unità di misura UR al secondo può essere considerata come la valuta della velocità effettiva. UR al secondo è una valuta basata sulla frequenza, che viene determinata in base all'astrazione delle risorse di sistema, come CPU, operazioni di I/O al secondo e memoria, necessarie per eseguire le operazioni di database supportate da Azure Cosmos DB. 

The cost to read a 1 KB item is 1 Request Unit (or 1 RU). A minimum of 10 RU/s is required to store each 1 GB of data. All other database operations are similarly assigned a cost using RUs. I costi vengono sempre misurati in UR, indipendentemente dall'API usata per interagire con il contenitore Azure Cosmos. Sia che l'operazione di database sia una scrittura, una lettura o una query, i costi sono sempre misurati in UR.

The following image shows the high-level idea of RUs:

![Utilizzo delle unità richiesta da parte delle operazioni di database](./media/request-units/request-units.png)

Per gestire e pianificare la capacità, Azure Cosmos DB garantisce che il numero di UR per una specifica operazione di database su un determinato set di dati sia deterministico. You can examine the response header to track the number of RUs that are consumed by any database operation. When you understand the [factors that affect RU charges](request-units.md#request-unit-considerations) and your application's throughput requirements, you can run your application cost effectively.

Il provisioning del numero di UR per l'applicazione viene effettuato in base a incrementi di 100 UR al secondo. Per ridimensionare la velocità effettiva di cui viene effettuato il provisioning per l'applicazione, è possibile aumentare o diminuire il numero di UR in qualsiasi momento, You can scale in increments or decrements of 100 RUs. a livello di codice o tramite il portale di Azure. You are billed on an hourly basis.

È possibile effettuare il provisioning della velocità effettiva a due diversi livelli di granularità: 

* **Containers**: For more information, see [Provision throughput on an Azure Cosmos container](how-to-provision-container-throughput.md).
* **Databases**: For more information, see [Provision throughput on an Azure Cosmos database](how-to-provision-database-throughput.md).

## <a name="request-unit-considerations"></a>Considerazioni sulle unità richiesta

Per stimare il numero di UR al secondo di cui effettuare il provisioning, considerare i fattori seguenti:

* **Item size**: As the size of an item increases, the number of RUs consumed to read or write the item also increases.

* **Item indexing**: By default, each item is automatically indexed. Se si sceglie di non indicizzare alcuni degli elementi in un contenitore, viene utilizzato un numero inferiore di UR.

* **Item property count**: Assuming the default indexing is on all properties, the number of RUs consumed to write an item increases as the item property count increases.

* **Indexed properties**: An index policy on each container determines which properties are indexed by default. Per ridurre l'utilizzo di UR per le operazioni di scrittura, limitare il numero di proprietà indicizzate.

* **Data consistency**: The strong and bounded staleness consistency levels consume approximately two times more RUs while performing read operations when compared to that of other relaxed consistency levels.

* **Query patterns**: The complexity of a query affects how many RUs are consumed for an operation. I fattori che influiscono sul costo delle operazioni di query sono: 
    
    - The number of query results
    - The number of predicates
    - The nature of the predicates
    - The number of user-defined functions
    - The size of the source data
    - The size of the result set
    - Proiezioni

  Azure Cosmos DB assicura che la stessa query sugli stessi dati costi sempre lo stesso numero di UR per esecuzioni ripetute.

* **Script usage**: As with queries, stored procedures and triggers consume RUs based on the complexity of the operations that are performed. As you develop your application, inspect the [request charge header](optimize-cost-queries.md#evaluate-request-unit-charge-for-a-query) to better understand how much RU capacity each operation consumes.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su come [effettuare il provisioning della velocità effettiva per contenitori e database di Azure Cosmos](set-throughput.md).
* [Partizionamento e scalabilità orizzontale in Azure Cosmos DB](partition-data.md)
* [Ridimensionamento a livello globale della velocità effettiva sottoposta a provisioning](scaling-throughput.md)
* [Effettuare il provisioning della velocità effettiva in un contenitore di Azure Cosmos](how-to-provision-container-throughput.md)
* [Effettuare il provisioning della velocità effettiva in un database di Azure Cosmos](how-to-provision-database-throughput.md)
* Learn how to [find the request unit charge for an operation](find-request-unit-charge.md).
* Learn how to [optimize provisioned throughput cost in Azure Cosmos DB](optimize-cost-throughput.md).
* Learn how to [optimize reads and writes cost in Azure Cosmos DB](optimize-cost-reads-writes.md).
* Learn how to [optimize query cost in Azure Cosmos DB](optimize-cost-queries.md).
* Learn how to [use metrics to monitor throughput](use-metrics.md).
