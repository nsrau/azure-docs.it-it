---
title: Unità richiesta e velocità effettiva in Azure Cosmos DB
description: Informazioni su come specificare e stimare i requisiti relativi alle unità richiesta in Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 6e5d95a47261445e3031f55368f4e2cd8e2830a7
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754857"
---
# <a name="request-units-in-azure-cosmos-db"></a>Unità richiesta in Azure Cosmos DB

Con Azure Cosmos DB, viene addebitato un costo per la velocità effettiva sottoposta a provisioning e per le risorse di archiviazione utilizzate su base oraria. È necessario effettuare il provisioning della velocità effettiva per assicurarsi che siano sempre disponibili risorse di sistema sufficienti per il database Azure Cosmos. È necessario disporre di risorse sufficienti per soddisfare o superare i [contratti di Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/).

Azure Cosmos DB supporta un'ampia gamma di API, come SQL, MongoDB, Cassandra, Gremlin e Tabella. Ogni API ha il proprio set di operazioni di database, da semplici operazioni di lettura e scrittura puntuali a query complesse. Ogni operazione di database utilizza le risorse di sistema a seconda della complessità. 

Il costo di tutte le operazioni del database viene normalizzato da Azure Cosmos DB ed è espresso da *unità richiesta* (o ur, per brevità). L'unità di misura UR al secondo può essere considerata come la valuta della velocità effettiva. UR al secondo è una valuta basata sulla frequenza, che viene determinata in base all'astrazione delle risorse di sistema, come CPU, operazioni di I/O al secondo e memoria, necessarie per eseguire le operazioni di database supportate da Azure Cosmos DB. 

Il costo per la lettura di un elemento da 1 KB è 1 unità richiesta (o 1 UR). A tutte le altre operazioni del database viene assegnato un costo analogo usando ur. I costi vengono sempre misurati in UR, indipendentemente dall'API usata per interagire con il contenitore Azure Cosmos. Sia che l'operazione di database sia una scrittura, una lettura o una query, i costi sono sempre misurati in UR.

Nell'immagine seguente viene illustrata l'idea generale delle UR:

![Utilizzo delle unità richiesta da parte delle operazioni di database](./media/request-units/request-units.png)

Per gestire e pianificare la capacità, Azure Cosmos DB garantisce che il numero di UR per una specifica operazione di database su un determinato set di dati sia deterministico. È possibile esaminare l'intestazione della risposta per tenere traccia del numero di ur utilizzate da qualsiasi operazione del database. Quando si conoscono i [fattori che influiscono sugli addebiti](request-units.md#request-unit-considerations) delle unità richiesta e sui requisiti di velocità effettiva dell'applicazione, è possibile eseguire il costo dell'applicazione in modo efficiente.

Il provisioning del numero di UR per l'applicazione viene effettuato in base a incrementi di 100 UR al secondo. Per ridimensionare la velocità effettiva di cui viene effettuato il provisioning per l'applicazione, è possibile aumentare o diminuire il numero di UR in qualsiasi momento, Puoi ridimensionare in incrementi o decrementi di 100 ur. a livello di codice o tramite il portale di Azure. L'addebito viene addebitato su base oraria.

È possibile effettuare il provisioning della velocità effettiva a due diversi livelli di granularità: 

* **Contenitori**: per altre informazioni, vedere [provisioning della velocità effettiva in un contenitore di Azure Cosmos](how-to-provision-container-throughput.md).
* **Database**: per altre informazioni, vedere [provisioning della velocità effettiva in un database di Azure Cosmos](how-to-provision-database-throughput.md).

## <a name="request-unit-considerations"></a>Considerazioni sulle unità richiesta

Per stimare il numero di UR al secondo di cui effettuare il provisioning, considerare i fattori seguenti:

* **Dimensioni elemento**: con l'aumentare delle dimensioni di un elemento, aumenta anche il numero di ur utilizzate per la lettura o la scrittura dell'elemento.

* **Indicizzazione di elementi**: per impostazione predefinita, ogni elemento viene indicizzato automaticamente. Se si sceglie di non indicizzare alcuni degli elementi in un contenitore, viene utilizzato un numero inferiore di UR.

* **Conteggio proprietà elemento**: supponendo che l'indicizzazione predefinita sia su tutte le proprietà, il numero di ur utilizzate per scrivere un elemento aumenta man mano che aumenta il numero di proprietà dell'elemento.

* **Proprietà indicizzate**: un criterio di indice in ogni contenitore determina quali proprietà sono indicizzate per impostazione predefinita. Per ridurre l'utilizzo di UR per le operazioni di scrittura, limitare il numero di proprietà indicizzate.

* **Coerenza dei dati**: i livelli di coerenza con obsolescenza forte e limitata utilizzano circa due volte più ur durante l'esecuzione di operazioni di lettura rispetto a quelle di altri livelli di coerenza rilassati.

* **Modelli di query**: la complessità di una query influiscono sul numero di ur utilizzate per un'operazione. I fattori che influiscono sul costo delle operazioni di query sono: 
    
    - Numero di risultati della query
    - Il numero di predicati
    - Natura dei predicati
    - Il numero di funzioni definite dall'utente
    - Dimensione dei dati di origine
    - Dimensioni del set di risultati
    - Proiezioni

  Azure Cosmos DB assicura che la stessa query sugli stessi dati costi sempre lo stesso numero di UR per esecuzioni ripetute.

* **Utilizzo di script**: come per le query, le stored procedure e i trigger utilizzano le UR in base alla complessità delle operazioni eseguite. Durante lo sviluppo dell'applicazione, controllare l' [intestazione della richiesta di addebito](optimize-cost-queries.md#evaluate-request-unit-charge-for-a-query) per comprendere meglio la capacità di ur utilizzata da ogni operazione.

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
