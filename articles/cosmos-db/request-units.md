---
title: Unità richiesta come valuta di velocità effettiva e prestazioni in Azure Cosmos DB
description: Informazioni su come specificare e stimare i requisiti relativi alle unità richiesta in Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: 6831cb3f39c25eb69d16300156f456980cf57fa0
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88604834"
---
# <a name="request-units-in-azure-cosmos-db"></a>Unità richiesta in Azure Cosmos DB

Azure Cosmos DB supporta un'ampia gamma di API, come SQL, MongoDB, Cassandra, Gremlin e Tabella. Ogni API ha il proprio set di operazioni di database, da semplici operazioni di lettura e scrittura puntuali a query complesse. Ogni operazione di database utilizza le risorse di sistema a seconda della complessità.

Il costo di tutte le operazioni del database viene normalizzato da Azure Cosmos DB ed è espresso da *unità richiesta* (o ur, per brevità). È possibile considerare le UR come una valuta delle prestazioni astraendo le risorse di sistema, ad esempio CPU, IOPS e memoria, necessarie per eseguire le operazioni del database supportate da Azure Cosmos DB.

Il costo per la lettura di un punto (ad esempio il recupero di un singolo elemento in base al relativo ID e valore della chiave di partizione) per un elemento di 1 KB è 1 unità richiesta (o 1 UR). In modo analogo, a tutte le altre operazioni di database viene assegnato un costo in termini di UR. I costi vengono sempre misurati in UR, indipendentemente dall'API usata per interagire con il contenitore Azure Cosmos. Il fatto che l'operazione di database sia una scrittura, un punto di lettura o una query, i costi vengono sempre misurati in ur.

Nell'immagine seguente viene illustrata l'idea generale delle UR:

:::image type="content" source="./media/request-units/request-units.png" alt-text="Utilizzo delle unità richiesta da parte delle operazioni di database" border="false":::

Per gestire e pianificare la capacità, Azure Cosmos DB garantisce che il numero di UR per una specifica operazione di database su un determinato set di dati sia deterministico. È possibile esaminare l'intestazione della risposta per tenere traccia del numero di UR utilizzate da qualsiasi operazione di database. Quando si conoscono i [fattori che influiscono sugli addebiti](request-units.md#request-unit-considerations) delle unità richiesta e sui requisiti di velocità effettiva dell'applicazione, è possibile eseguire il costo dell'applicazione in modo efficiente.

Il tipo di account Azure Cosmos usato determina il modo in cui viene addebitato il consumo di ur:

- In modalità di [velocità effettiva con provisioning](set-throughput.md) viene effettuato il provisioning del numero di ur per l'applicazione in base al secondo con incrementi di 100 ur al secondo. Per ridimensionare la velocità effettiva con provisioning per l'applicazione, è possibile aumentare o diminuire il numero di ur in qualsiasi momento con incrementi o decrementi di 100 ur. Le modifiche possono essere apportate a livello di codice o tramite il portale di Azure. Viene addebitato su base oraria per la quantità di ur al secondo di cui è stato effettuato il provisioning. È possibile effettuare il provisioning della velocità effettiva a due diversi livelli di granularità:
  - **Contenitori**: per altre informazioni, vedere [provisioning della velocità effettiva in un contenitore di Azure Cosmos](how-to-provision-container-throughput.md).
  - **Database**: per altre informazioni, vedere [provisioning della velocità effettiva in un database di Azure Cosmos](how-to-provision-database-throughput.md).
- In modalità senza [Server](serverless.md) non è necessario effettuare il provisioning di una velocità effettiva durante la creazione di risorse nell'account Azure Cosmos. Alla fine del periodo di fatturazione, viene addebitata la quantità di unità richiesta che è stata utilizzata dalle operazioni del database.

## <a name="request-unit-considerations"></a>Considerazioni sulle unità richiesta

Mentre si stima il numero di ur utilizzate dal carico di lavoro, considerare i fattori seguenti:

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
* Scopri di più [su Azure Cosmos DB senza server](serverless.md).
* [Partizionamento e scalabilità orizzontale in Azure Cosmos DB](partition-data.md)
* [Ridimensionamento a livello globale della velocità effettiva sottoposta a provisioning](scaling-throughput.md)
* [Effettuare il provisioning della velocità effettiva in un contenitore di Azure Cosmos](how-to-provision-container-throughput.md)
* [Effettuare il provisioning della velocità effettiva in un database di Azure Cosmos](how-to-provision-database-throughput.md)
* Informazioni su come [trovare l'addebito delle unità richiesta per un'operazione](find-request-unit-charge.md).
* Informazioni su come [ottimizzare il costo della velocità effettiva con provisioning in Azure Cosmos DB](optimize-cost-throughput.md).
* Informazioni su come [ottimizzare i costi di lettura e scrittura in Azure Cosmos DB](optimize-cost-reads-writes.md).
* Informazioni su come [ottimizzare i costi delle query in Azure Cosmos DB](optimize-cost-queries.md).
* Informazioni su come [usare le metriche per monitorare la velocità effettiva](use-metrics.md).
