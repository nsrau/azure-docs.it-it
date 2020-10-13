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
ms.date: 09/15/2020
ms.openlocfilehash: a6e89883ec0e1e493bad9572876af86f4a0d3853
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91324438"
---
# <a name="copy-activity-performance-and-scalability-guide"></a>Guida alla scalabilità e alle prestazioni dell'attività di copia

> [!div class="op_single_selector" title1="Selezionare la versione di Azure Data Factory in uso:"]
> * [Versione 1](v1/data-factory-copy-activity-performance.md)
> * [Versione corrente](copy-activity-performance.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A volte si vuole eseguire una migrazione dei dati su larga scala da data Lake o Enterprise data warehouse (EDW) in Azure. Altre volte si vuole inserire grandi quantità di dati, da origini diverse in Azure, per Big Data analisi. In ogni caso, è fondamentale ottenere prestazioni e scalabilità ottimali.

Azure Data Factory (ADF) fornisce un meccanismo per inserire i dati. ADF presenta i vantaggi seguenti:

* Gestisce grandi quantità di dati
* È molto efficiente
* Economica

Questi vantaggi rendono ADF un ottimo adattamento per gli ingegneri di dati che desiderano creare pipeline di inserimento di dati scalabili altamente performanti.

Dopo la lettura di questo articolo, si potrà rispondere alle domande seguenti:

* Quale livello di prestazioni e scalabilità è possibile ottenere utilizzando l'attività di copia ADF per la migrazione dei dati e gli scenari di inserimento dei dati?
* Quali operazioni è necessario eseguire per ottimizzare le prestazioni dell'attività di copia ADF?
* Quali manopole di ottimizzazione delle prestazioni di ADF è possibile utilizzare per ottimizzare le prestazioni per una singola esecuzione dell'attività di copia?
* Quali altri fattori all'esterno di ADF considerare durante l'ottimizzazione delle prestazioni di copia?

> [!NOTE]
> Se non si ha familiarità con l'attività di copia in generale, vedere la [Panoramica dell'attività di copia](copy-activity-overview.md) prima di leggere questo articolo.

## <a name="copy-performance-and-scalability-achievable-using-adf"></a>Prestazioni di copia e scalabilità ottenibili con ADF

ADF offre un'architettura senza server che consente il parallelismo a livelli diversi.

Questa architettura consente di sviluppare pipeline che ottimizzano la velocità effettiva di spostamento dei dati per l'ambiente in uso. Queste pipeline utilizzano completamente le risorse seguenti:

* Larghezza di banda di rete
* Operazioni di input/output di archiviazione al secondo (IOPS) e larghezza di banda

Questo utilizzo completo significa che è possibile stimare la velocità effettiva complessiva misurando la velocità effettiva minima disponibile con le risorse seguenti:

* Archivio dati di origine
* Archivio dati di destinazione
* Larghezza di banda di rete tra gli archivi dati di origine e di destinazione

La tabella seguente calcola la durata della copia. La durata è basata sulle dimensioni dei dati e sul limite della larghezza di banda per l'ambiente.

&nbsp;

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
| | |  | | |  | | |

La copia di ADF è scalabile a livelli diversi:

![scalabilità delle copie di ADF](media/copy-activity-performance/adf-copy-scalability.png)

* Il flusso di controllo ADF può avviare più attività di copia in parallelo, ad esempio usando il [ciclo For Each](control-flow-for-each-activity.md).

* Una singola attività di copia può sfruttare le risorse di calcolo scalabili.
  * Quando si usa il runtime di integrazione di Azure, è possibile specificare [fino a 256 unità di integrazione dati (DIUs)](#data-integration-units) per ogni attività di copia, in modo senza server.
  * Quando si usa il runtime di integrazione self-hosted, è possibile adottare uno degli approcci seguenti:
    * Ridimensionare manualmente il computer.
    * Scalabilità orizzontale in più computer ([fino a 4 nodi](create-self-hosted-integration-runtime.md#high-availability-and-scalability)) e una singola attività di copia partiziona il set di file in tutti i nodi.

* Una singola attività di copia legge e scrive nell'archivio dati usando più thread [in parallelo](#parallel-copy).

## <a name="performance-tuning-steps"></a>Procedura di ottimizzazione delle prestazioni

Per ottimizzare le prestazioni del servizio Azure Data Factory con l'attività di copia, seguire questa procedura:

1. **Selezionare un set di dati di test e stabilire una linea di base.**

    Durante lo sviluppo, testare la pipeline usando l'attività di copia su un campione di dati rappresentativo. Il set di dati scelto deve rappresentare i modelli di dati tipici insieme ai seguenti attributi:

    * Struttura di cartelle
    * Schema file
    * Schema dei dati

    E il set di dati deve essere sufficientemente grande da valutare le prestazioni di copia. Per completare l'attività di copia, è necessario disporre di almeno 10 minuti. Raccogliere i dettagli di esecuzione e le caratteristiche delle prestazioni dopo il [monitoraggio dell'attività di copia](copy-activity-monitoring.md).

2. **Come ottimizzare le prestazioni di una singola attività di copia**:

    Si consiglia di ottimizzare prima le prestazioni usando una singola attività di copia.

    * **Se l'attività di copia viene eseguita in un runtime di integrazione di _Azure_ :**

        Iniziare con i valori predefiniti per le [unità di integrazione dati (DIU)](#data-integration-units) e le impostazioni di [copia parallela](#parallel-copy) .

    * **Se l'attività di copia viene eseguita in un runtime di integrazione _self-hosted_ :**

        Si consiglia di utilizzare un computer dedicato per ospitare il runtime di integrazione. Il computer deve essere separato dal server che ospita l'archivio dati. Iniziare con i valori predefiniti per l'impostazione di [copia parallela](#parallel-copy) e usare un singolo nodo per il runtime di integrazione self-hosted.

    Eseguire un'esecuzione del test delle prestazioni. Prendere nota delle prestazioni conseguite. Includere i valori effettivi utilizzati, ad esempio DIUs e copie parallele. Vedere [monitoraggio dell'attività di copia](copy-activity-monitoring.md) su come raccogliere i risultati dell'esecuzione e le impostazioni delle prestazioni usate. Informazioni su come [risolvere i problemi relativi alle prestazioni dell'attività di copia](copy-activity-performance-troubleshooting.md) per identificare e risolvere il collo di bottiglia.

    Eseguire l'iterazione per eseguire esecuzioni di test aggiuntive seguendo le indicazioni per la risoluzione dei problemi e l'ottimizzazione. Quando l'esecuzione di un'attività di copia singola non può ottenere una velocità effettiva migliore, valutare se ottimizzare la velocità effettiva aggregata eseguendo più copie contemporaneamente. Questa opzione è illustrata nel punto elenco successivo.

3. **Come ottimizzare la velocità effettiva aggregata eseguendo più copie simultaneamente:**

    A questo punto sono state ottimizzate le prestazioni di una singola attività di copia. Se non sono ancora stati realizzati i limiti massimi di velocità effettiva dell'ambiente, è possibile eseguire più attività di copia in parallelo. È possibile eseguire in parallelo usando i costrutti del flusso di controllo di ADF. Uno di questi costrutti è il [ciclo for each](control-flow-for-each-activity.md). Per ulteriori informazioni, vedere gli articoli seguenti relativi ai modelli di soluzione:

    * [Copiare file da più contenitori](solution-template-copy-files-multiple-containers.md)
    * [Migrare i dati da Amazon S3 a ADLS Gen2](solution-template-migration-s3-azure.md)
    * [Copia bulk con una tabella di controllo](solution-template-bulk-copy-with-control-table.md)

4. **Espandere la configurazione per l'intero set di dati.**

    Quando si è soddisfatti dei risultati e delle prestazioni di esecuzione, è possibile espandere la definizione e la pipeline per coprire l'intero set di dati.

## <a name="troubleshoot-copy-activity-performance"></a>Risolvere i problemi delle prestazioni dell'attività di copia

Seguire i [passaggi di ottimizzazione delle prestazioni](#performance-tuning-steps) per pianificare ed eseguire il test delle prestazioni per lo scenario. Per informazioni su come risolvere i problemi relativi alle prestazioni dell'esecuzione dell'attività di copia in Azure Data Factory, vedere [risolvere i problemi relativi alle prestazioni dell'attività di copia](copy-activity-performance-troubleshooting.md)

## <a name="copy-performance-optimization-features"></a>Funzionalità di ottimizzazione delle prestazioni di copia

Azure Data Factory offre le seguenti funzionalità di ottimizzazione delle prestazioni:

* [Unità di integrazione dati](#data-integration-units)
* [Scalabilità del runtime di integrazione self-hosted](#self-hosted-integration-runtime-scalability)
* [Copia parallela](#parallel-copy)
* [copia di staging](#staged-copy)

### <a name="data-integration-units"></a>Unità di integrazione dati

Una Data Integration Unit (DIU) è una misura che rappresenta la potenza di una singola unità in Azure Data Factory. Power è una combinazione di CPU, memoria e allocazione di risorse di rete. DIU si applica solo al [runtime di integrazione di Azure](concepts-integration-runtime.md#azure-integration-runtime). DIU non si applica al [runtime di integrazione self-hosted](concepts-integration-runtime.md#self-hosted-integration-runtime). [Altre informazioni](copy-activity-performance-features.md#data-integration-units)sono disponibili qui.

### <a name="self-hosted-integration-runtime-scalability"></a>Scalabilità del runtime di integrazione self-hosted

Potrebbe essere necessario ospitare un carico di lavoro simultaneo crescente. In alternativa, è possibile ottenere prestazioni più elevate nel livello di carico di lavoro attuale. È possibile migliorare la scalabilità dell'elaborazione con gli approcci seguenti:

* È _possibile scalare_ verticalmente il runtime di integrazione self-hosted, aumentando il numero di [processi simultanei](create-self-hosted-integration-runtime.md#scale-up) che possono essere eseguiti in un nodo.  
La scalabilità verticale funziona solo se il processore e la memoria del nodo sono meno del totale utilizzato.
* È _possibile scalare_ verticalmente il runtime di integrazione self-hosted aggiungendo più nodi (computer).

Per altre informazioni, vedere:

* [Funzionalità di ottimizzazione delle prestazioni dell'attività di copia: scalabilità di Integration Runtime self-hosted](copy-activity-performance-features.md#self-hosted-integration-runtime-scalability)
* [Creare e configurare un runtime di integrazione self-hosted: considerazioni sulla scalabilità](create-self-hosted-integration-runtime.md#scale-considerations)

### <a name="parallel-copy"></a>Copia parallela

È possibile impostare la `parallelCopies` proprietà per indicare il parallelismo che si desidera venga utilizzato dall'attività di copia. Considerare questa proprietà come il numero massimo di thread all'interno dell'attività di copia. I thread operano in parallelo. I thread leggono dall'origine o scrivono negli archivi dati sink. [Altre informazioni](copy-activity-performance-features.md#parallel-copy)

### <a name="staged-copy"></a>copia di staging

Un'operazione di copia dei dati può inviare i dati _direttamente_ all'archivio dati sink. In alternativa, è possibile scegliere di usare l'archiviazione BLOB come archivio di _staging provvisorio_ . [Altre informazioni](copy-activity-performance-features.md#staged-copy)

## <a name="next-steps"></a>Passaggi successivi

Vedere gli altri articoli relativi all'attività di copia:

* [Panoramica dell'attività di copia](copy-activity-overview.md)
* [Risolvere i problemi delle prestazioni dell'attività di copia](copy-activity-performance-troubleshooting.md)
* [Funzionalità di ottimizzazione delle prestazioni dell'attività di copia](copy-activity-performance-features.md)
* [Usare Azure Data Factory per migrare i dati dal data Lake o da data warehouse ad Azure](data-migration-guidance-overview.md)
* [Eseguire la migrazione dei dati da un archivio dati Amazon S3 ad Archiviazione di Azure](data-migration-guidance-s3-azure-storage.md)
