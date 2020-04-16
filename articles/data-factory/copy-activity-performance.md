---
title: Guida alle prestazioni e alla scalabilità dell'attività di copia
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414166"
---
# <a name="copy-activity-performance-and-scalability-guide"></a>Guida alle prestazioni e alla scalabilità dell'attività di copia

> [!div class="op_single_selector" title1="Selezionare la versione di Azure Data Factory in uso:Select the version of Azure Data Factory that you're using:"]
> * [Versione 1](v1/data-factory-copy-activity-performance.md)
> * [Versione corrente](copy-activity-performance.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Sia che si desideri eseguire una migrazione dei dati su larga scala da data lake o enterprise data warehouse (EDW) ad Azure o che si desideri inserire dati su larga scala da origini diverse in Azure per l'analisi dei Big Data, è fondamentale ottenere prestazioni e scalabilità ottimali.  Azure Data Factory offre un meccanismo performante, resiliente ed economico per l'inserimento dei dati su larga scala, il che lo rende ideale per gli ingegneri di dati che desiderano creare pipeline di inserimento dati altamente performanti e scalabili.

Dopo la lettura di questo articolo, si potrà rispondere alle domande seguenti:

- Quale livello di prestazioni e scalabilità è possibile ottenere utilizzando l'attività di copia ADF per la migrazione dei dati e gli scenari di inserimento dati?

- Quali passaggi è necessario eseguire per ottimizzare le prestazioni dell'attività di copia ADF?
- Quali manopole di ottimizzazione ADF perf è possibile utilizzare per ottimizzare le prestazioni per una singola esecuzione dell'attività di copia?
- Quali altri fattori al di fuori di ADF considerare quando si ottimizzano le prestazioni di copia?

> [!NOTE]
> Se non si ha familiarità con l'attività di copia in generale, vedere la [panoramica dell'attività](copy-activity-overview.md) di copia prima di leggere questo articolo.

## <a name="copy-performance-and-scalability-achievable-using-adf"></a>Prestazioni di copia e scalabilità ottene con ADF

ADF offre un'architettura senza server che consente il parallelismo a diversi livelli, che consente agli sviluppatori di creare pipeline per utilizzare completamente la larghezza di banda di rete, nonché le operazioni di I/O al secondo e la larghezza di banda di archiviazione per ottimizzare la velocità effettiva di spostamento dei dati per l'ambiente.  Ciò significa che la velocità effettiva che è possibile ottenere può essere stimata misurando la velocità effettiva minima offerta dall'archivio dati di origine, dall'archivio dati di destinazione e dalla larghezza di banda di rete tra l'origine e la destinazione.  La tabella seguente calcola la durata della copia in base alle dimensioni dei dati e al limite di larghezza di banda per l'ambiente. 

| Dimensioni dei dati / <br/> bandwidth | 50 Mbps    | 100 Mbps  | 500 Mbps  | 1 Gbps   | 5 Gbps   | 10 Gbps  | 50 Gbps   |
| --------------------------- | ---------- | --------- | --------- | -------- | -------- | -------- | --------- |
| **1 GB**                    | 2,7 min    | 1,4 min   | 0,3 min   | 0,1 min  | 0,03 min | 0,01 min | 0,0 min   |
| **10 GB**                   | 27,3 min   | 13,7 min  | 2,7 min   | 1,3 min  | 0,3 min  | 0,1 min  | 0,03 min  |
| **100 GB**                  | Ore 4,6    | 2.3 ore   | 0,5 ore   | 0,2 ore  | 0,05 ore | 0,02 ore | 0,0 ore   |
| **1 TB**                    | 46,6 ore   | Ore 23.3  | 4,7 ore   | 2.3 ore  | 0,5 ore  | 0,2 ore  | 0,05 ore  |
| **10 TB**                   | 19,4 giorni  | 9,7 giorni  | 1,9 giorni  | 0,9 giorni | 0,2 giorni | 0,1 giorni | 0,02 giorni |
| **100 TB**                  | 194,2 giorni | 97,1 giorni | 19,4 giorni | 9,7 giorni | 1,9 giorni | 1 giorno    | 0,2 giorni  |
| **1 PB**                    | 64,7 mo    | 32.4 mo   | 6.5 mo    | 3.2 mo   | 0,6 mo   | 0,3 mo   | 0,06 mo   |
| **10 PB**                   | 647.3 mo   | 323,6 mo  | 64,7 mo   | 31,6 mo  | 6.5 mo   | 3.2 mo   | 0,6 mo    |

La copia DI ADF è scalabile a diversi livelli:ADF copy is scalable at different levels:

![come scala la copia ADF](media/copy-activity-performance/adf-copy-scalability.png)

- Il flusso di controllo ADF può avviare più attività di copia in parallelo, ad esempio usando [Il ciclo For Each](control-flow-for-each-activity.md).
- Una singola attività di copia può sfruttare le risorse di calcolo scalabili: quando si usa Azure Integration Runtime, è possibile specificare [fino a 256 DIUff](#data-integration-units) per ogni attività di copia in modo senza server. Quando si utilizza il runtime di integrazione self-hosted, è possibile aumentare manualmente il computer o la scalabilità orizzontale su più computer[(fino a 4 nodi)](create-self-hosted-integration-runtime.md#high-availability-and-scalability)e una singola attività di copia partiziona il file impostato su tutti i nodi.
- Una singola attività di copia legge e scrive nell'archivio dati utilizzando più thread [in parallelo.](#parallel-copy)

## <a name="performance-tuning-steps"></a>Procedura di ottimizzazione delle prestazioni

Eseguire la procedura seguente per ottimizzare le prestazioni del servizio Azure Data Factory con l'attività di copia.

1. **Raccogliere un set di dati di test e stabilire una linea di base.** Durante la fase di sviluppo, testare la pipeline usando l'attività di copia su un campione di dati rappresentativo. Il set di dati scelto deve rappresentare i modelli di dati tipici (struttura di cartelle, modello di file, schema di dati e così via) ed è sufficientemente grande da valutare le prestazioni della copia, ad esempio sono necessari 10 minuti o oltre per il completamento dell'attività di copia. Raccogliere i dettagli di esecuzione e le caratteristiche delle prestazioni dopo [il monitoraggio dell'attività](copy-activity-monitoring.md)di copia.

2. **Come ottimizzare le prestazioni di una singola attività di copia**:

   Per iniziare, è consigliabile ottimizzare le prestazioni utilizzando una singola attività di copia.

   - **Se l'attività** di copia viene eseguita in un runtime di integrazione di Azure: iniziare con i valori predefiniti per le unità [DIU (Data Integration Units)](#data-integration-units) e le impostazioni di [copia parallela.](#parallel-copy) 

   - Se l'attività di copia viene eseguita in un runtime di **integrazione self-hosted:** è consigliabile usare un computer dedicato separato dal server che ospita l'archivio dati per ospitare il runtime di integrazione. Iniziare con i valori predefiniti per l'impostazione della [copia parallela](#parallel-copy) e usare un singolo nodo per il metodo di creazione di raggi-ir self-hosted.  

   Eseguire un'esecuzione dei test delle prestazioni e prendere nota delle prestazioni ottenute e dei valori effettivi utilizzati, ad esempio DIU e copie parallele. Fare riferimento al [monitoraggio dell'attività](copy-activity-monitoring.md) di copia su come raccogliere i risultati di esecuzione e le impostazioni delle prestazioni utilizzate e informazioni su come [risolvere le prestazioni dell'attività](copy-activity-performance-troubleshooting.md) di copia per identificare e risolvere il collo di bottiglia. 

   Eseguire l'iterazione per eseguire ulteriori esecuzioni di test delle prestazioni in seguito alle indicazioni per la risoluzione dei problemi e l'ottimizzazione. Quando l'esecuzione di una singola attività di copia non è in grado di ottenere una velocità effettiva migliore, è consigliabile ottimizzare la velocità effettiva aggregata eseguendo più copie contemporaneamente facendo riferimento al passaggio 3.Once single copy activity run cannot achieve better throughput, consider to maximize aggregate throughput by running multiple copies concurrently referring to step 3.


3. **Come ottimizzare la velocità effettiva aggregata eseguendo più copie contemporaneamente:How to maximize aggregate throughput by running multiple copies concurrently:**

   Dopo aver ingrandito le prestazioni di una singola attività di copia, se non sono ancora stati raggiunti i limiti massimi della velocità effettiva dell'ambiente, ovvero rete, archivio dati di origine e archivio dati di destinazione, è possibile eseguire più attività di copia in parallelo utilizzando costrutti del flusso di controllo ADF, ad esempio [Ciclo For Each](control-flow-for-each-activity.md). Fare riferimento a [Copia file da più contenitori](solution-template-copy-files-multiple-containers.md), Eseguire la migrazione dei dati da Amazon [S3 ad ADLS Gen2](solution-template-migration-s3-azure.md)o [Copia in blocco con modelli](solution-template-bulk-copy-with-control-table.md) di soluzione tabella di controllo come esempio generale.

5. **Espandere la configurazione per l'intero set di dati.** Quando si è soddisfatti dei risultati e delle prestazioni dell'esecuzione, è possibile espandere la definizione e la pipeline per coprire l'intero set di dati.

## <a name="troubleshoot-copy-activity-performance"></a>Risolvere i problemi relativi alle prestazioni dell'attività di copiaTroubleshoot copy activity

Seguire i passaggi di [ottimizzazione delle prestazioni](#performance-tuning-steps) per pianificare ed eseguire test delle prestazioni per lo scenario. Informazioni su come risolvere il problema di prestazioni di ogni esecuzione dell'attività di copia in Azure Data Factory da Risolvere le prestazioni dell'attività di [copia.](copy-activity-performance-troubleshooting.md)

## <a name="copy-performance-optimization-features"></a>Funzionalità di ottimizzazione delle prestazioni di copia

Azure Data Factory offre le funzionalità di ottimizzazione delle prestazioni seguenti:Azure Data Factory provides the following performance optimization features:

- [Unità di integrazione dati](#data-integration-units)
- [Scalabilità del runtime di integrazione self-hosted](#self-hosted-integration-runtime-scalability)
- [Copia parallela](#parallel-copy)
- [copia di staging](#staged-copy)

### <a name="data-integration-units"></a>Unità di integrazione dati

A Data Integration Unit is a measure that represents the power (a combination of CPU, memory, and network resource allocation) of a single unit in Azure Data Factory. L'unità di [integrazione](concepts-integration-runtime.md#azure-integration-runtime)dei dati si applica solo al runtime di integrazione di Azure, ma non al runtime di [integrazione self-hosted.](concepts-integration-runtime.md#self-hosted-integration-runtime) [Altre informazioni](copy-activity-performance-features.md#data-integration-units)

### <a name="self-hosted-integration-runtime-scalability"></a>Scalabilità del runtime di integrazione self-hosted

Per ospitare l'aumento del carico di lavoro simultaneo o per ottenere prestazioni più elevate, è possibile aumentare o ridurre il runtime di integrazione self-hosted. [Altre informazioni](copy-activity-performance-features.md#self-hosted-integration-runtime-scalability)

### <a name="parallel-copy"></a>Copia parallela

È possibile impostare una copia parallela per indicare il parallelismo che si desidera venga utilizzato dall'attività di copia. È possibile considerare questa proprietà come il numero massimo di thread all'interno dell'attività di copia che leggono dall'origine o scrivere negli archivi dati sink in parallelo. [Altre informazioni](copy-activity-performance-features.md#parallel-copy)

### <a name="staged-copy"></a>copia di staging

Quando si copiano dati da un archivio dati di origine a un archivio dati sink, è possibile scegliere di usare un archivio BLOB come archivio di staging provvisorio. [Altre informazioni](copy-activity-performance-features.md#staged-copy)

## <a name="next-steps"></a>Passaggi successivi
Vedere gli altri articoli sull'attività di copia:See the other copy activity articles:

- [Panoramica dell'attività di copia](copy-activity-overview.md)
- [Risolvere i problemi relativi alle prestazioni dell'attività di copiaTroubleshoot copy activity](copy-activity-performance-troubleshooting.md)
- [Funzionalità di ottimizzazione delle prestazioni dell'attività di copia](copy-activity-performance-features.md)
- [Usare Azure Data Factory per eseguire la migrazione dei dati dal data lake o dal data warehouse ad AzureUse Azure Data Factory to migrate data from your data lake or data warehouse to Azure](data-migration-guidance-overview.md)
- [Eseguire la migrazione dei dati da un archivio dati Amazon S3 ad Archiviazione di Azure](data-migration-guidance-s3-azure-storage.md)
