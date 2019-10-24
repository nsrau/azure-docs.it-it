---
title: Archiviazione transazionale e analitica distribuita a livello globale per i contenitori di Azure Cosmos
description: Informazioni sull'archiviazione transazionale e analitica e le relative opzioni di configurazione per i contenitori di Azure Cosmos.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/30/2019
ms.reviewer: sngun
ms.openlocfilehash: abf222b7a6d6e8fd053fa83c066d2b7850f575ab
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756895"
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
|Prezzo per GB    |   $0,25/GB      |  $0.02/GB       |
|Prezzo per le transazioni di archiviazione    | La velocità effettiva con provisioning viene addebitata a $0,008 per 100 ur/sec con fatturazione oraria.        |  La velocità effettiva basata sul consumo viene addebitata a $0,05 per 10.000 transazioni di scrittura e $0,004 per le transazioni di lettura 10.000.       |

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

### <a name="on-demand-snapshots-and-time-travel-analytics"></a>Snapshot su richiesta e analisi del tempo di viaggio

È possibile creare snapshot dei dati archiviati nell'archiviazione analitica dei contenitori di Azure Cosmos, in qualsiasi momento, chiamando il comando `CreateSnapshot (name, timestamp)` nel contenitore. Gli snapshot sono denominati "segnalibri" nella cronologia degli aggiornamenti che sono stati eseguiti nel contenitore.

![Snapshot su richiesta e analisi del tempo di viaggio](./media/globally-distributed-transactional-analytical-storage/ondemand-analytical-data-snapshots.png)

Al momento della creazione dello snapshot, oltre al nome, è possibile specificare il timestamp che definisce lo stato del contenitore nella cronologia degli aggiornamenti. È quindi possibile caricare i dati dello snapshot in Spark ed eseguire le query.

Attualmente, è possibile creare snapshot su richiesta in qualsiasi momento nel contenitore, la possibilità di creare automaticamente snapshot in base a una pianificazione o a un criterio personalizzato non è ancora supportata.

### <a name="configure-and-tier-data-between-transactional-and-analytical-storage-independently"></a>Configurare e impostare i dati di livello tra archiviazione transazionale e analitica in modo indipendente

A seconda dello scenario, è possibile abilitare o disabilitare in modo indipendente ognuno dei due motori di archiviazione. Di seguito sono riportate le configurazioni per ogni scenario:

|Scenario |Impostazione di archiviazione transazionale  |Impostazione di archiviazione analitica |
|---------|---------|---------|
|Esecuzione esclusiva dei carichi di lavoro analitici (con conservazione infinita) |  DefaultTimeToLive = 0       |  ColumnStoreTimeToLive =-1       |
|Esecuzione esclusiva dei carichi di lavoro transazionali (con conservazione infinita)  |   DefaultTimeToLive =-1      |  ColumnStoreTimeToLive = 0       |
|Esecuzione di carichi di lavoro transazionali e analitici (con conservazione infinita)   |   DefaultTimeToLive =-1      | ColumnStoreTimeToLive =-1        |
|Esecuzione di carichi di lavoro transazionali e analitici (con intervalli di conservazione diversi, noti anche come suddivisione in livelli di archiviazione)  |  DefaultTimeToLive = <Value1>       |     ColumnStoreTimeToLive = <Value2>    |

1. **Configurare il contenitore esclusivamente per i carichi di lavoro analitici (con conservazione infinita)**

   È possibile configurare il contenitore di Azure Cosmos esclusivamente per i carichi di lavoro analitici. Questa configurazione offre un vantaggio per cui non è necessario pagare per l'archiviazione transazionale. Se l'obiettivo consiste nell'usare il contenitore solo per i carichi di lavoro analitici, è possibile disabilitare l'archiviazione transazionale impostando `DefaultTimeToLive` su 0 nel contenitore Cosmos ed è possibile abilitare l'archiviazione analitica con conservazione infinita impostando `ColumnStoreTimeToLive` su-1.

   ![Carichi di lavoro analitici con conservazione infinita](./media/globally-distributed-transactional-analytical-storage/analytical-workload-configuration.png)

1. **Configurare il contenitore esclusivamente per i carichi di lavoro transazionali (con conservazione infinita)**

   È possibile configurare il contenitore di Azure Cosmos esclusivamente per i carichi di lavoro transazionali. È possibile disabilitare l'archiviazione analitica impostando `ColumnStoreTimeToLive` su 0 nel contenitore ed è possibile abilitare l'archiviazione analitica con conservazione infinita impostando `DefaultTimeToLive` su-1.

   ![Carichi di lavoro transazionali con conservazione infinita](./media/globally-distributed-transactional-analytical-storage/transactional-workload-configuration.png)

1. **Configurare il contenitore per i carichi di lavoro transazionali e analitici (con conservazione infinita)**

   È possibile configurare il contenitore di Azure Cosmos per i carichi di lavoro transazionali e analitici con un isolamento completo delle prestazioni. È possibile abilitare l'archiviazione analitica impostando `ColumnStoreTimeToLive` su-1 e abilitare l'archiviazione transazionale con conservazione infinita impostando `DefaultTimeToLive ` su-1.

   ![Carichi di lavoro transazionali e analitici con conservazione infinita](./media/globally-distributed-transactional-analytical-storage/analytical-transactional-configuration-infinite-retention.png)

1. **Configurare il contenitore per carichi di lavoro transazionali e analitici con suddivisione in livelli di archiviazione**

   È possibile configurare il contenitore di Azure Cosmos per i carichi di lavoro transazionali e analitici con un isolamento completo delle prestazioni tra di essi con intervalli di conservazione diversi. Azure Cosmos DB impone che l'archiviazione analitica venga sempre mantenuta per un periodo di tempo più lungo rispetto all'archiviazione transazionale.

   È possibile abilitare l'archiviazione transazionale con conservazione infinita impostando `DefaultTimeToLive` su < valore 1 > e abilitare l'archiviazione analitica impostando `ColumnStoreTimeToLive` su < valore 2 >. Azure Cosmos DB imporrà che < valore 2 > è sempre maggiore di < valore 1 >.

   ![Carichi di lavoro transazionali e analitici con suddivisione in livelli di archiviazione](./media/globally-distributed-transactional-analytical-storage/analytical-transactional-configuration-specified-retention.png)

## <a name="next-steps"></a>Passaggi successivi

* [Durata Azure Cosmos DB](time-to-live.md)
