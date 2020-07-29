---
title: Unità richiesta e velocità effettiva in Azure Cosmos DB
description: Informazioni su come specificare e stimare i requisiti relativi alle unità richiesta in Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/24/2020
ms.openlocfilehash: f1f203d17de9fb0fc9fe8bb0f6de80fe2b93ba8b
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87327804"
---
# <a name="request-units-in-azure-cosmos-db"></a>Unità richiesta in Azure Cosmos DB

Con Azure Cosmos DB, viene addebitato un costo per la velocità effettiva sottoposta a provisioning e per le risorse di archiviazione utilizzate su base oraria. È necessario il provisioning della velocità effettiva per assicurarsi che siano costantemente disponibili risorse di sistema sufficienti per il database di Azure Cosmos, È necessario disporre di risorse sufficienti per soddisfare o superare i [contratti di Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/).

Azure Cosmos DB supporta un'ampia gamma di API, come SQL, MongoDB, Cassandra, Gremlin e Tabella. Ogni API ha il proprio set di operazioni di database, da semplici operazioni di lettura e scrittura puntuali a query complesse. Ogni operazione di database utilizza le risorse di sistema a seconda della complessità.

Il costo di tutte le operazioni del database viene normalizzato da Azure Cosmos DB ed è espresso da *unità richiesta* (o ur, per brevità). L'unità di misura UR al secondo può essere considerata come la valuta della velocità effettiva. UR al secondo è una valuta basata sulla frequenza, che viene determinata in base all'astrazione delle risorse di sistema, come CPU, operazioni di I/O al secondo e memoria, necessarie per eseguire le operazioni di database supportate da Azure Cosmos DB. Per archiviare ogni 1 GB di dati, è necessario un minimo di 10 UR/sec.

Il costo per la lettura di un punto per un elemento di 1 KB è 1 unità richiesta (o 1 UR). In modo analogo, a tutte le altre operazioni di database viene assegnato un costo in termini di UR. I costi vengono sempre misurati in UR, indipendentemente dall'API usata per interagire con il contenitore Azure Cosmos. Il fatto che l'operazione di database sia una scrittura, un punto di lettura o una query, i costi vengono sempre misurati in ur.

Nell'immagine seguente viene illustrata l'idea generale delle UR:

:::image type="content" source="./media/request-units/request-units.png" alt-text="Utilizzo delle unità richiesta da parte delle operazioni di database" border="false":::

Per gestire e pianificare la capacità, Azure Cosmos DB garantisce che il numero di UR per una specifica operazione di database su un determinato set di dati sia deterministico. È possibile esaminare l'intestazione della risposta per tenere traccia del numero di UR utilizzate da qualsiasi operazione di database. Quando si conoscono i [fattori che influiscono sugli addebiti](request-units.md#request-unit-considerations) delle unità richiesta e sui requisiti di velocità effettiva dell'applicazione, è possibile eseguire il costo dell'applicazione in modo efficiente.

Il provisioning del numero di UR per l'applicazione viene effettuato in base a incrementi di 100 UR al secondo. Per ridimensionare la velocità effettiva di cui viene effettuato il provisioning per l'applicazione, è possibile aumentare o diminuire il numero di UR in qualsiasi momento, in base a incrementi o decrementi di 100 UR, Le modifiche possono essere apportate a livello di codice o tramite il portale di Azure. I costi vengono addebitati su base oraria.

È possibile effettuare il provisioning della velocità effettiva a due diversi livelli di granularità:

* **Contenitori**: per altre informazioni, vedere [provisioning della velocità effettiva in un contenitore di Azure Cosmos](how-to-provision-container-throughput.md).
* **Database**: per altre informazioni, vedere [provisioning della velocità effettiva in un database di Azure Cosmos](how-to-provision-database-throughput.md).

## <a name="request-unit-considerations"></a>Considerazioni sulle unità richiesta

Al momento di stimare il numero di UR al secondo di cui effettuare il provisioning è opportuno tenere in considerazione i fattori seguenti:

* **Dimensioni degli elementi**: con l'aumentare delle dimensioni di un elemento, aumenta anche il numero di UR utilizzate per la lettura o la scrittura dell'elemento.

* **Indicizzazione degli elementi**: Per impostazione predefinita, ogni elemento viene automaticamente indicizzato. Se si sceglie di non indicizzare alcuni degli elementi in un contenitore, viene utilizzato un numero inferiore di UR.

* **Numero di proprietà degli elementi**: supponendo che sia applicata l'indicizzazione predefinita a tutte le proprietà, il numero di UR utilizzate per scrivere un elemento aumenta proporzionalmente al numero delle proprietà dell'elemento.

* **Proprietà indicizzate**: I criteri di indicizzazione in ogni contenitore determinano le proprietà che vengono indicizzate per impostazione predefinita. Per ridurre il consumo di UR per le operazioni di scrittura, limitare il numero di proprietà indicizzate.

* **Coerenza dei dati**: i livelli di coerenza con obsolescenza forte e limitata utilizzano circa due volte più ur durante l'esecuzione di operazioni di lettura rispetto a quelle di altri livelli di coerenza rilassati.

* **Tipo di letture**: il costo delle letture di punti è notevolmente inferiore rispetto alle query.

* **Modelli di query**: la complessità di una query influisce sulla quantità di UR utilizzate per un'operazione. I fattori che influiscono sul costo delle operazioni di query sono: 
    
    - Il numero di risultati della query
    - Il numero di predicati
    - La natura dei predicati
    - Il numero di funzioni definite dall'utente
    - Le dimensioni dei dati di origine
    - Le dimensioni del set di risultati
    - Proiezioni

  Azure Cosmos DB assicura che la stessa query sugli stessi dati costi sempre lo stesso numero di UR per esecuzioni ripetute.

* **Utilizzo di script**: come per le query, le stored procedure e i trigger utilizzano le UR in base alla complessità delle operazioni eseguite. Durante lo sviluppo dell'applicazione, controllare l'[intestazione per l'addebito delle richieste](optimize-cost-queries.md#evaluate-request-unit-charge-for-a-query) per comprendere meglio quanta capacità in termini di UR viene utilizzata da ogni operazione.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su come [effettuare il provisioning della velocità effettiva per contenitori e database di Azure Cosmos](set-throughput.md).
* [Partizionamento e scalabilità orizzontale in Azure Cosmos DB](partition-data.md)
* [Ridimensionamento a livello globale della velocità effettiva sottoposta a provisioning](scaling-throughput.md)
* [Effettuare il provisioning della velocità effettiva in un contenitore di Azure Cosmos](how-to-provision-container-throughput.md)
* [Effettuare il provisioning della velocità effettiva in un database di Azure Cosmos](how-to-provision-database-throughput.md)
* Informazioni su come [trovare l'addebito delle unità richiesta per un'operazione](find-request-unit-charge.md).
* Informazioni su come [ottimizzare il costo della velocità effettiva con provisioning in Azure Cosmos DB](optimize-cost-throughput.md).
* Informazioni su come [ottimizzare i costi di lettura e scrittura in Azure Cosmos DB](optimize-cost-reads-writes.md).
* Informazioni su come [ottimizzare i costi delle query in Azure Cosmos DB](optimize-cost-queries.md).
* Informazioni su come [usare le metriche per monitorare la velocità effettiva](use-metrics.md).
