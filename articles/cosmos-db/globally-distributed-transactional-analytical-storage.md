---
title: Archiviazione transazionale e analitica distribuita a livello globale per i contenitori di Azure Cosmos
description: Informazioni sull'archiviazione transazionale e analitica e le relative opzioni di configurazione per i contenitori di Azure Cosmos.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/30/2019
ms.reviewer: sngun
ms.openlocfilehash: 22bb36e3b22f65bbf9922bd31e4b2e041cdb8979
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73601222"
---
# <a name="globally-distributed-transactional-and-analytical-storage-for-azure-cosmos-containers"></a>Archiviazione transazionale e analitica distribuita a livello globale per i contenitori di Azure Cosmos

Azure Cosmos container è supportato internamente da due motori di archiviazione: il motore di archiviazione transazionale e un motore di archiviazione analitico aggiornabile. Entrambi i motori di archiviazione sono strutturati con log e ottimizzati per la scrittura per aggiornamenti più veloci. Ognuno di essi viene tuttavia codificato in modo diverso:

* **Motore di archiviazione transazionale** : è codificato in formato orientato alle righe per le letture e le query transazionali veloci.

* **Motore di archiviazione analitico** : è codificato in formato a colonne per le analisi e le query analitiche veloci.

![Motore di archiviazione e mapping API Azure Cosmos DB](./media/globally-distributed-transactional-analytical-storage/storage-engines-api-mapping.png)

Il motore di archiviazione transazionale è supportato da unità SSD locali, mentre l'archiviazione analitica è archiviata in un servizio di archiviazione SSD fuori cluster economico. Nella tabella seguente sono riportate le differenze rilevanti tra l'archiviazione transazionale e quella analitica.


|Funzionalità  |Archiviazione transazionale  |Archiviazione analitica |
|---------|---------|---------|
|Archiviazione massima per ogni contenitore di Azure Cosmos |   Illimitato      |    Illimitato     |
|Spazio di archiviazione massimo per chiave di partizione logica   |   10 GB      |   Illimitato      |
|Codifica archiviazione  |   Orientato alla riga, con un formato interno.   |   Orientato alle colonne, usando il formato parquet Apache. |
|Località di archiviazione |   Archiviazione replicata supportata da unità SSD locali/intra-cluster. |  Archiviazione replicata supportata da SSD economici remote/off-cluster.       |
|Durabilità  |    99,99999 (7-9 s)     |  99,99999 (7-9 s)       |
|API che accedono ai dati  |   SQL, MongoDB, Cassandra, Gremlin, tabelle e ETCD.       | Apache Spark         |
|Conservazione (TTL (time-to-Live)   |  Basato sui criteri, configurato nel contenitore Azure Cosmos usando la proprietà `DefaultTimeToLive`.       |   Basato sui criteri, configurato nel contenitore Azure Cosmos usando la proprietà `ColumnStoreTimeToLive`.      |
|Prezzo per GB    |   Vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/cosmos-db/)     |   Vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/cosmos-db/)        |
|Prezzo per le transazioni di archiviazione    |  Vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/cosmos-db/)         |   Vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/cosmos-db/)        |

## <a name="benefits-of-transactional-and-analytical-storage"></a>Vantaggi dell'archiviazione transazionale e analitica

### <a name="no-etl-operations"></a>Nessuna operazione ETL

Le pipeline analitiche tradizionali sono complesse con più fasi, ciascuna delle quali richiede operazioni ETL (Extract-Transform-Load) da e verso i livelli di calcolo e archiviazione. Produce architetture di elaborazione dati complesse. Ciò significa costi elevati per più fasi di archiviazione e calcolo e una latenza elevata a causa di volumi elevati di dati trasferiti tra varie fasi di archiviazione e calcolo.  

Non è previsto alcun sovraccarico per l'esecuzione di operazioni ETL con Azure Cosmos DB. Ogni contenitore di Azure Cosmos è supportato da motori di archiviazione transazionali e analitici e il trasferimento dei dati tra il motore di archiviazione transazionale e analitico viene eseguito all'interno del motore di database e senza alcun hop di rete. La latenza e i costi risultanti sono significativamente inferiori rispetto alle soluzioni analitiche tradizionali. E si ottiene un unico sistema di archiviazione distribuito a livello globale per i carichi di lavoro transazionali e analitici.  

### <a name="store-multiple-versions-update-and-query-the-analytical-storage"></a>Archiviare più versioni, aggiornare ed eseguire query sull'archiviazione analitica

L'archiviazione analitica è completamente aggiornabile e contiene la cronologia delle versioni completa di tutti gli aggiornamenti transazionali che si sono verificati nel contenitore Azure Cosmos.

Qualsiasi aggiornamento apportato all'archiviazione transazionale è sicuramente visibile per l'archiviazione analitica entro 30 secondi. 

### <a name="globally-distributed-multi-master-analytical-storage"></a>Archiviazione analitica multimaster distribuita a livello globale

Se l'ambito dell'account Azure Cosmos è una singola area, i dati archiviati (nell'archivio transazionale e analitico) nei contenitori hanno come ambito un'unica area. D'altra parte, se l'account Azure Cosmos viene distribuito a livello globale, anche i dati archiviati nei contenitori vengono distribuiti a livello globale.

Per gli account Azure Cosmos configurati con più aree di scrittura, le scritture nel contenitore (sia per l'archiviazione transazionale che per quella analitica) vengono sempre eseguite nell'area locale e quindi sono veloci.

Per gli account Azure Cosmos a una o più aree, indipendentemente dal fatto che l'area di scrittura singola (master singolo) o più aree di scrittura (noto anche come multi-master), sia le letture transazionali che quelle analitiche vengono eseguite localmente nell'area specificata.

### <a name="performance-isolation-between-transactional-and-analytical-workloads"></a>Isolamento delle prestazioni tra carichi di lavoro transazionali e analitici

In una determinata area, i carichi di lavoro transazionali operano sull'archiviazione transazionale/riga del contenitore. D'altra parte, i carichi di lavoro analitici operano sull'archiviazione analitica/colonna del contenitore. I due motori di archiviazione operano in modo indipendente e garantiscono un isolamento delle prestazioni rigoroso tra i carichi di lavoro.

I carichi di lavoro transazionali utilizzano la velocità effettiva con provisioning (UR). A differenza dei carichi di lavoro transazionali, la velocità effettiva dei carichi di lavoro analitici è basata sul consumo effettivo. I carichi di lavoro analitici utilizzano risorse su richiesta.

## <a name="next-steps"></a>Passaggi successivi

* [Durata Azure Cosmos DB](time-to-live.md)
