---
title: Archiviazione transazionale e analitica distribuita a livello globale (in anteprima privata) per i contenitori Cosmos di AzureGlobally distributed transactional and analytical(in private preview) storage for Azure Cosmos containers
description: Informazioni sull'archiviazione transazionale e analitica e sulle relative opzioni di configurazione per i contenitori Cosmos di Azure.Learn about transactional and analytical storage and their configuration options for Azure Cosmos containers.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/30/2019
ms.reviewer: sngun
ms.openlocfilehash: 1c2b79f8d0641b1a1386329a2add14ded766bf5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623388"
---
# <a name="globally-distributed-transactional-and-analytical-storage-for-azure-cosmos-containers"></a>Archiviazione transazionale e analitica distribuita a livello globale per i contenitori Cosmos di AzureGlobally distributed transactional and analytical storage for Azure Cosmos containers

Il contenitore Cosmos di Azure è supportato internamente da due motori di archiviazione: il motore di archiviazione transazionale e un motore di archiviazione analitica aggiornabile (nell'anteprima privata). Entrambi i motori di archiviazione sono con struttura log e ottimizzati per la scrittura per aggiornamenti più rapidi. Tuttavia, ognuno di essi è codificato in modo diverso:

* **Motore di archiviazione transazionale:** è codificato in formato orientato alle righe per letture e query transazionali veloci.

* **Motore di archiviazione analitica** - È codificato in formato a colonne per query analitiche veloci e scansioni.

![Motori di archiviazione e mapping API database Cosmos di AzureStorage engines and Azure Cosmos DB API mapping](./media/globally-distributed-transactional-analytical-storage/storage-engines-api-mapping.png)

Il motore di archiviazione transazionale è supportato da SSD locali, mentre l'archiviazione analitica viene archiviata in un'archiviazione SSD off-cluster economica. Nella tabella seguente vengono acquisite le differenze notevoli tra l'archiviazione transazionale e l'archiviazione analitica.


|Funzionalità  |Archiviazione transazionale  |Stoccaggio analitico |
|---------|---------|---------|
|Spazio di archiviazione massimo per un contenitore di Azure CosmosMaximum storage per an Azure Cosmos container |   Nessuna limitazione      |    Nessuna limitazione     |
|Spazio di archiviazione massimo per una chiave di partizione logicaMaximum storage per a logical partition key   |   20 GB      |   Nessuna limitazione      |
|Codifica di archiviazione  |   Orientato alle righe, utilizzando un formato interno.   |   Orientato alla colonna, utilizzando il formato Apache Parquet. |
|Località di archiviazione |   Archiviazione replicata supportata da Unità SSD locali/intra-cluster. |  Archiviazione replicata supportata da SSD remoti/off-cluster poco costosi.       |
|Durata  |    99.99999 (7-9 s)     |  99.99999 (7-9 s)       |
|API che accedono ai dati  |   SQL, MongoDB, Cassandra, Gremlin, Tabelle e così via.       | Apache Spark         |
|Conservazione (Time-to-Live o TTL)   |  Basato su criteri, configurato nel contenitore `DefaultTimeToLive` Cosmos di Azure tramite la proprietà.       |   Basato su criteri, configurato nel contenitore `ColumnStoreTimeToLive` Cosmos di Azure tramite la proprietà.      |
|Prezzo per GB    |   Vedi la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/cosmos-db/)     |   Vedi la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/cosmos-db/)        |
|Prezzo per le transazioni di archiviazione    |  Vedi la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/cosmos-db/)         |   Vedi la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/cosmos-db/)        |

## <a name="benefits-of-transactional-and-analytical-storage"></a>Vantaggi dell'archiviazione transazionale e analitica

### <a name="no-etl-operations"></a>Nessuna operazione ETL

Le pipeline analitiche tradizionali sono complesse, con più fasi, ognuna delle quali richiede operazioni ETL (Extract-Transform-Load) da e verso i livelli di elaborazione e archiviazione. Si traduce in architetture di elaborazione dati complesse. Il che significa costi elevati per più fasi di archiviazione e calcolo e latenza elevata a causa di enormi volumi di dati trasferiti tra le varie fasi di archiviazione e calcolo.  

Non vi è alcun sovraccarico di eseguire operazioni ETL con Azure Cosmos DB. Ogni contenitore Cosmos di Azure è supportato da entrambi i motori di archiviazione transazionale e analitica e il trasferimento dei dati tra il motore di archiviazione transazionale e analitico viene eseguito all'interno del motore di database e senza hop di rete. La latenza e i costi risultanti sono significativamente inferiori rispetto alle soluzioni analitiche tradizionali. E ottieni un unico sistema di storage distribuito a livello globale sia per i carichi di lavoro transazionali che per i carichi di lavoro analitici.  

### <a name="store-multiple-versions-update-and-query-the-analytical-storage"></a>Archiviare più versioni, aggiornare ed eseguire query sull'archiviazione analitica

L'archiviazione analitica è completamente aggiornabile e contiene la cronologia completa delle versioni di tutti gli aggiornamenti transazionali che si sono verificati nel contenitore Cosmos di Azure.The analytical storage is fully updatable, and it contains the complete version history of all the transactional updates that occurred on the Azure Cosmos container.

Qualsiasi aggiornamento apportato all'archiviazione transazionale è garantito per essere visibile all'archiviazione analitica entro 30 secondi. 

### <a name="globally-distributed-multi-master-analytical-storage"></a>Archiviazione analitica multimaster distribuita a livello globale

Se l'ambito dell'account Cosmos di Azure è limitato a una singola area, anche i dati archiviati (nell'archiviazione transazionale e analitica) nei contenitori vengono attribuiti a una singola area. D'altra parte, se l'account Cosmos di Azure viene distribuito a livello globale, anche i dati archiviati nei contenitori vengono distribuiti a livello globale.

Per gli account Cosmos di Azure configurati con più aree di scrittura, le scritture nel contenitore (sia per l'archiviazione transazionale che per quella analitica) vengono sempre eseguite nell'area locale e pertanto sono veloci.

Per gli account Cosmos di Azure a singola o più aree, indipendentemente dal fatto che una singola area di scrittura (singolo master) o più aree di scrittura (nota anche come multimaster), letture/query transazionali e analitiche vengano eseguite localmente nell'area specificata.

### <a name="performance-isolation-between-transactional-and-analytical-workloads"></a>Isolamento delle prestazioni tra carichi di lavoro transazionali e analiticiPerformance isolation between transactional and analytical workloads

In una determinata area, i carichi di lavoro transazionali operano sull'archiviazione transazionale/riga del contenitore. D'altra parte, i carichi di lavoro analitici operano sull'archiviazione analitica/colonna del contenitore. I due motori di archiviazione operano in modo indipendente e forniscono un rigoroso isolamento delle prestazioni tra i carichi di lavoro.

I carichi di lavoro transazionali utilizzano la velocità effettiva di cui è stato eseguito il provisioning. A differenza dei carichi di lavoro transazionali, la velocità effettiva dei carichi di lavoro analitici si basa sul consumo effettivo. I carichi di lavoro analitici consumano risorse su richiesta.

## <a name="next-steps"></a>Passaggi successivi

* [Durata (TTL) in Azure Cosmos DB](time-to-live.md)
