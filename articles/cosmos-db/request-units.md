---
title: Unità richiesta e velocità effettiva in Azure Cosmos DB
description: Informazioni su come specificare e stimare i requisiti relativi alle unità richiesta in Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: a0058bf309e0ff4fbe687731d676e907d1c3fd82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246591"
---
# <a name="request-units-in-azure-cosmos-db"></a>Unità richiesta in Azure Cosmos DB

Con Azure Cosmos DB, viene addebitato un costo per la velocità effettiva sottoposta a provisioning e per le risorse di archiviazione utilizzate su base oraria. È necessario il provisioning della velocità effettiva per assicurarsi che siano costantemente disponibili risorse di sistema sufficienti per il database di Azure Cosmos, Sono necessarie risorse [sufficienti](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)per soddisfare o superare i contratti di archiviazione del database di Azure .

Azure Cosmos DB supporta un'ampia gamma di API, come SQL, MongoDB, Cassandra, Gremlin e Tabella. Ogni API ha il proprio set di operazioni di database, da semplici operazioni di lettura e scrittura puntuali a query complesse. Ogni operazione di database utilizza le risorse di sistema a seconda della complessità. 

Il costo di tutte le operazioni di database è normalizzato da Azure Cosmos DB ed è espresso da *unità di richiesta* (o RU, in breve). L'unità di misura UR al secondo può essere considerata come la valuta della velocità effettiva. UR al secondo è una valuta basata sulla frequenza, che viene determinata in base all'astrazione delle risorse di sistema, come CPU, operazioni di I/O al secondo e memoria, necessarie per eseguire le operazioni di database supportate da Azure Cosmos DB. 

Il costo per leggere un elemento da 1 kB è pari a 1 unità richiesta ( o 1 UR). Per archiviare ogni 1 GB di dati è necessario almeno 10 RU/s. In modo analogo, a tutte le altre operazioni di database viene assegnato un costo in termini di UR. I costi vengono sempre misurati in UR, indipendentemente dall'API usata per interagire con il contenitore Azure Cosmos. Sia che l'operazione di database sia una scrittura, una lettura o una query, i costi sono sempre misurati in UR.

Nell'immagine seguente viene illustrata l'idea generale delle UR:

![Utilizzo delle unità richiesta da parte delle operazioni di database](./media/request-units/request-units.png)

Per gestire e pianificare la capacità, Azure Cosmos DB garantisce che il numero di UR per una specifica operazione di database su un determinato set di dati sia deterministico. È possibile esaminare l'intestazione della risposta per tenere traccia del numero di UR utilizzate da qualsiasi operazione di database. Quando si [comprendono](request-units.md#request-unit-considerations) i fattori che influiscono sui costi RU e sui requisiti di velocità effettiva dell'applicazione, è possibile eseguire l'applicazione in modo conveniente.

Il provisioning del numero di UR per l'applicazione viene effettuato in base a incrementi di 100 UR al secondo. Per ridimensionare la velocità effettiva di cui viene effettuato il provisioning per l'applicazione, è possibile aumentare o diminuire il numero di UR in qualsiasi momento, in base a incrementi o decrementi di 100 UR, a livello di codice o tramite il portale di Azure. I costi vengono addebitati su base oraria.

È possibile effettuare il provisioning della velocità effettiva a due diversi livelli di granularità: 

* **Contenitori:** per altre informazioni, vedere [Effettuare il provisioning della velocità effettiva in un contenitore Cosmos](how-to-provision-container-throughput.md)di Azure.Containers : For more information, see Provision throughput on an Azure Cosmos container .
* **Database:** per altre informazioni, vedere [Effettuare il provisioning della velocità effettiva in un database di Azure Cosmos.](how-to-provision-database-throughput.md)

## <a name="request-unit-considerations"></a>Considerazioni sulle unità richiesta

Per stimare il numero di UR al secondo di cui effettuare il provisioning, considerare i fattori seguenti:

* **Dimensione dell'elemento**: Con l'aumentare delle dimensioni di un elemento, aumenta anche il numero di RU utilizzate per leggere o scrivere l'elemento.

* **Indicizzazione degli**elementi: per impostazione predefinita, ogni elemento viene indicizzato automaticamente. Se si sceglie di non indicizzare alcuni degli elementi in un contenitore, viene utilizzato un numero inferiore di UR.

* **Conteggio proprietà elemento**: Supponendo che l'indicizzazione predefinita sia su tutte le proprietà, il numero di RU utilizzate per scrivere un elemento aumenta con l'aumentare del numero di proprietà dell'elemento.

* **Proprietà indicizzate**: un criterio di indice in ogni contenitore determina quali proprietà vengono indicizzate per impostazione predefinita. Per ridurre l'utilizzo di UR per le operazioni di scrittura, limitare il numero di proprietà indicizzate.

* **Coerenza dei dati**: I livelli di coerenza di stantio forte e limitato consumano circa due volte più RU durante l'esecuzione di operazioni di lettura rispetto a quella di altri livelli di coerenza rilassati.

* **Modelli di query:** la complessità di una query influisce sul numero di RU utilizzate per un'operazione. I fattori che influiscono sul costo delle operazioni di query sono: 
    
    - Il numero di risultati della query
    - Il numero di predicati
    - La natura dei predicati
    - Il numero di funzioni definite dall'utente
    - Le dimensioni dei dati di origine
    - Le dimensioni del set di risultati
    - Proiezioni

  Azure Cosmos DB assicura che la stessa query sugli stessi dati costi sempre lo stesso numero di UR per esecuzioni ripetute.

* **Utilizzo script:** come per le query, le stored procedure e i trigger utilizzano unità di gestione in base alla complessità delle operazioni eseguite. Durante lo sviluppo dell'applicazione, esaminare [l'intestazione](optimize-cost-queries.md#evaluate-request-unit-charge-for-a-query) di addebito della richiesta per comprendere meglio la quantità di capacità di RU utilizzata da ogni operazione.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su come [effettuare il provisioning della velocità effettiva per contenitori e database di Azure Cosmos](set-throughput.md).
* Ulteriori informazioni sulle [partizioni logiche](partition-data.md).
* [Ridimensionamento a livello globale della velocità effettiva sottoposta a provisioning](scaling-throughput.md)
* [Effettuare il provisioning della velocità effettiva in un contenitore di Azure Cosmos](how-to-provision-container-throughput.md)
* [Effettuare il provisioning della velocità effettiva in un database di Azure Cosmos](how-to-provision-database-throughput.md)
* Informazioni su come [trovare l'addebito dell'unità richiesta per un'operazione.](find-request-unit-charge.md)
* Informazioni su come ottimizzare il costo della velocità effettiva di cui è stato eseguito il [provisioning in Azure Cosmos DB.](optimize-cost-throughput.md)
* Informazioni su come ottimizzare il costo di [lettura e scrittura in Azure Cosmos DB](optimize-cost-reads-writes.md).
* Informazioni su come ottimizzare il costo delle [query in Azure Cosmos DB](optimize-cost-queries.md).
* Informazioni su come [utilizzare le metriche per monitorare](use-metrics.md)la velocità effettiva.
