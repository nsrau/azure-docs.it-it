---
title: Guida alla scalabilità e alle prestazioni dell'attività di copia
description: Informazioni sui fattori chiave che influiscono sulle prestazioni dello spostamento dei dati in Azure Data Factory quando si usa l'attività di copia.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/11/2020
ms.openlocfilehash: aedb3df69821d1436b03b2eb1f12873b624d426e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81414166"
---
# <a name="copy-activity-performance-and-scalability-guide"></a>Guida alla scalabilità e alle prestazioni dell'attività di copia

> [!div class="op_single_selector" title1="Selezionare la versione di Azure Data Factory in uso:"]
> * [Versione 1](v1/data-factory-copy-activity-performance.md)
> * [Versione corrente](copy-activity-performance.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Se si vuole eseguire una migrazione dei dati su larga scala da data Lake o Enterprise data warehouse (EDW) ad Azure o si vuole inserire dati su larga scala da origini diverse in Azure per Big Data analisi, è fondamentale ottenere prestazioni e scalabilità ottimali.  Azure Data Factory fornisce un meccanismo efficiente, resiliente ed economicamente conveniente per inserire i dati su larga scala, rendendolo ideale per gli ingegneri di dati che desiderano creare pipeline di inserimento dati altamente performanti e scalabili.

Dopo la lettura di questo articolo, si potrà rispondere alle domande seguenti:

- Quale livello di prestazioni e scalabilità è possibile ottenere utilizzando l'attività di copia ADF per la migrazione dei dati e gli scenari di inserimento dei dati?

- Quali operazioni è necessario eseguire per ottimizzare le prestazioni dell'attività di copia ADF?
- Quali manopole di ottimizzazione delle prestazioni di ADF è possibile utilizzare per ottimizzare le prestazioni per una singola esecuzione dell'attività di copia?
- Quali altri fattori all'esterno di ADF considerare durante l'ottimizzazione delle prestazioni di copia?

> [!NOTE]
> Se non si ha familiarità con l'attività di copia in generale, vedere la [Panoramica dell'attività di copia](copy-activity-overview.md) prima di leggere questo articolo.

## <a name="copy-performance-and-scalability-achievable-using-adf"></a>Prestazioni di copia e scalabilità ottenibili con ADF

ADF offre un'architettura serverless che consente il parallelismo a livelli diversi, consentendo agli sviluppatori di compilare pipeline per sfruttare al meglio la larghezza di banda di rete, nonché le operazioni di I/O al secondo di archiviazione e la larghezza di banda per ottimizzare la velocità effettiva di spostamento dei dati per l'ambiente.  Ciò significa che la velocità effettiva che è possibile ottenere può essere stimata misurando la velocità effettiva minima offerta dall'archivio dati di origine, l'archivio dati di destinazione e la larghezza di banda di rete tra l'origine e la destinazione.  La tabella seguente calcola la durata della copia in base alle dimensioni dei dati e al limite di larghezza di banda per l'ambiente. 

| Dimensioni dati/ <br/> bandwidth | 50 Mbps    | 100 Mbps  | 500 Mbps  | 1 Gbps   | 5 Gbps   | 10 Gbps  | 50 Gbps   |
| --------------------------- | ---------- | --------- | --------- | -------- | -------- | -------- | --------- |
| **1 GB**                    | 2,7 min    | 1,4 min   | 0,3 min   | 0,1 min  | 0,03 min | 0,01 min | 0,0 min   |
| **10 GB**                   | 27,3 min   | 13,7 min  | 2,7 min   | 1,3 min  | 0,3 min  | 0,1 min  | 0,03 min  |
| **100 GB**                  | 4,6 ore    | 2,3 ore   | 0,5 ore   | 0,2 ore  | 0,05 ore | 0,02 ore | 0,0 ore   |
| **1 TB**                    | 46,6 ore   | 23,3 ore  | 4,7 ore   | 2,3 ore  | 0,5 ore  | 0,2 ore  | 0,05 ore  |
| **10 TB**                   | 19,4 giorni  | 9,7 giorni  | 1,9 giorni  | 0,9 giorni | 0,2 giorni | 0,1 giorni | 0,02 giorni |
| **100 TB**                  | 194,2 giorni | 97,1 giorni | 19,4 giorni | 9,7 giorni | 1,9 giorni | 1 giorno    | 0,2 giorni  |
| **1 PB**                    | 64,7 mo    | 32,4 Mo   | 6,5 Mo    | 3,2 Mo   | 0,6 Mo   | 0,3 Mo   | 0,06 mo   |
| **10 PB**                   | 647,3 Mo   | 323,6 Mo  | 64,7 mo   | 31,6 Mo  | 6,5 Mo   | 3,2 Mo   | 0,6 Mo    |

La copia di ADF è scalabile a livelli diversi:

![scalabilità delle copie di ADF](media/copy-activity-performance/adf-copy-scalability.png)

- Il flusso di controllo ADF può avviare più attività di copia in parallelo, ad esempio usando il [ciclo For Each](control-flow-for-each-activity.md).
- Una singola attività di copia può sfruttare le risorse di calcolo scalabili: quando si usa Azure Integration Runtime, è possibile specificare [fino a 256 unità di integrazione dati](#data-integration-units) per ogni attività di copia in modalità serverless; quando si usa il runtime di integrazione self-hosted è possibile aumentare manualmente le prestazioni del computer o aumentare le istanze in più computer ([fino a 4 nodi](create-self-hosted-integration-runtime.md#high-availability-and-scalability)) e una singola attività di copia eseguirà la partizione del set di file in tutti i nodi.
- Una singola attività di copia legge e scrive nell'archivio dati usando più thread [in parallelo](#parallel-copy).

## <a name="performance-tuning-steps"></a>Procedura di ottimizzazione delle prestazioni

Eseguire questi passaggi per ottimizzare le prestazioni del servizio Azure Data Factory con l'attività di copia.

1. **Selezionare un set di dati di test e stabilire una linea di base.** Durante la fase di sviluppo, testare la pipeline usando l'attività di copia su un campione di dati rappresentativi. Il set di dati scelto deve rappresentare i modelli di dati tipici (struttura di cartelle, modello di file, schema di dati e così via) ed è sufficientemente grande per valutare le prestazioni di copia, ad esempio per completare l'attività di copia è necessario 10 minuti o oltre. Raccogliere i dettagli di esecuzione e le caratteristiche delle prestazioni dopo il [monitoraggio dell'attività di copia](copy-activity-monitoring.md).

2. **Come ottimizzare le prestazioni di una singola attività di copia**:

   Per iniziare, è consigliabile innanzitutto ottimizzare le prestazioni usando una singola attività di copia.

   - **Se l'attività di copia viene eseguita in un Azure Integration Runtime:** iniziare con i valori predefiniti per le [unità di integrazione dati (DIU)](#data-integration-units) e le impostazioni di [copia parallela](#parallel-copy) . 

   - **Se l'attività di copia viene eseguita in un Integration Runtime self-hosted:** si consiglia di usare un computer dedicato separato dal server che ospita l'archivio dati per ospitare Integration Runtime. Iniziare con i valori predefiniti per l'impostazione di [copia parallela](#parallel-copy) e usare un singolo nodo per il runtime di integrazione self-hosted.  

   Eseguire un'esecuzione dei test delle prestazioni e prendere nota delle prestazioni, oltre ai valori effettivi usati come DIUs e copie parallele. Per informazioni su come raccogliere i risultati e le impostazioni delle prestazioni usati, vedere [monitoraggio dell'attività di copia](copy-activity-monitoring.md) e informazioni su come risolvere i [problemi relativi alle prestazioni dell'attività di copia](copy-activity-performance-troubleshooting.md) per identificare e risolvere il collo di bottiglia. 

   Eseguire l'iterazione per eseguire esecuzioni di test aggiuntive seguendo le indicazioni per la risoluzione dei problemi e l'ottimizzazione. Una volta che l'esecuzione dell'attività di copia singola non può ottenere una velocità effettiva migliore, provare a massimizzare la velocità effettiva aggregata eseguendo più copie simultaneamente facendo riferimento al passaggio 3.


3. **Come ottimizzare la velocità effettiva aggregata eseguendo più copie simultaneamente:**

   Ora che sono state ingrandite le prestazioni di una singola attività di copia, se non sono ancora stati superati i limiti massimi di velocità effettiva dell'ambiente, ovvero la rete, l'archivio dati di origine e l'archivio dati di destinazione, è possibile eseguire più attività di copia in parallelo usando i costrutti del flusso di controllo di ADF, ad esempio [per ciascun ciclo](control-flow-for-each-activity.md). Vedere [copiare i file da più contenitori](solution-template-copy-files-multiple-containers.md), [migrare i dati da Amazon S3 a ADLS Gen2 o eseguire](solution-template-migration-s3-azure.md) [una copia bulk con](solution-template-bulk-copy-with-control-table.md) i modelli di soluzione della tabella di controllo come esempio generale.

5. **Espandere la configurazione per l'intero set di dati.** Quando si è soddisfatti dei risultati e delle prestazioni di esecuzione, è possibile espandere la definizione e la pipeline per coprire l'intero set di dati.

## <a name="troubleshoot-copy-activity-performance"></a>Risolvere i problemi delle prestazioni dell'attività di copia

Seguire i [passaggi di ottimizzazione delle prestazioni](#performance-tuning-steps) per pianificare ed eseguire il test delle prestazioni per lo scenario. Per informazioni su come risolvere i problemi relativi alle prestazioni dell'esecuzione dell'attività di copia in Azure Data Factory, vedere [risolvere i problemi relativi alle prestazioni dell'attività di copia](copy-activity-performance-troubleshooting.md)

## <a name="copy-performance-optimization-features"></a>Funzionalità di ottimizzazione delle prestazioni di copia

Azure Data Factory offre le seguenti funzionalità di ottimizzazione delle prestazioni:

- [Unità di integrazione dati](#data-integration-units)
- [Scalabilità del runtime di integrazione self-hosted](#self-hosted-integration-runtime-scalability)
- [Copia parallela](#parallel-copy)
- [copia di staging](#staged-copy)

### <a name="data-integration-units"></a>Unità di integrazione dati

Un'unità di integrazione dati è una misura che rappresenta la potenza, ovvero una combinazione di CPU, memoria e allocazione di risorse di rete, di una singola unità in Azure Data Factory. L'unità di integrazione dati si applica solo al [runtime di integrazione di Azure](concepts-integration-runtime.md#azure-integration-runtime), ma non al runtime di [integrazione self-hosted](concepts-integration-runtime.md#self-hosted-integration-runtime). [Altre informazioni](copy-activity-performance-features.md#data-integration-units)

### <a name="self-hosted-integration-runtime-scalability"></a>Scalabilità del runtime di integrazione self-hosted

Per ospitare un aumento del carico di lavoro simultaneo o per ottenere prestazioni più elevate, è possibile aumentare o ridurre il numero di dimensioni del Integration Runtime self-hosted. [Altre informazioni](copy-activity-performance-features.md#self-hosted-integration-runtime-scalability)

### <a name="parallel-copy"></a>Copia parallela

È possibile impostare la copia parallela per indicare il parallelismo che si desidera venga utilizzato dall'attività di copia. È possibile considerare questa proprietà come il numero massimo di thread all'interno dell'attività di copia che leggono dall'origine o scrivono negli archivi dati sink in parallelo. [Altre informazioni](copy-activity-performance-features.md#parallel-copy)

### <a name="staged-copy"></a>copia di staging

Quando si copiano dati da un archivio dati di origine a un archivio dati sink, è possibile scegliere di usare un archivio BLOB come archivio di staging provvisorio. [Altre informazioni](copy-activity-performance-features.md#staged-copy)

## <a name="next-steps"></a>Passaggi successivi
Vedere gli altri articoli relativi all'attività di copia:

- [Panoramica dell'attività di copia](copy-activity-overview.md)
- [Risolvere i problemi delle prestazioni dell'attività di copia](copy-activity-performance-troubleshooting.md)
- [Funzionalità di ottimizzazione delle prestazioni dell'attività di copia](copy-activity-performance-features.md)
- [Usare Azure Data Factory per migrare i dati dal data Lake o da data warehouse ad Azure](data-migration-guidance-overview.md)
- [Eseguire la migrazione dei dati da un archivio dati Amazon S3 ad Archiviazione di Azure](data-migration-guidance-s3-azure-storage.md)
