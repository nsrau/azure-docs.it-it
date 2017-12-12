---
title: Confrontare le versioni 1 e 2 di Azure Data Factory | Microsoft Docs
description: Questo articolo mette a confronto le versioni 1 e 2 di Azure Data Factory.
services: data-factory
documentationcenter: 
author: kromerm
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/20/2017
ms.author: makromer
ms.openlocfilehash: f55348e9974de17c6d7e704e185f1ea9b21ff66e
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2017
---
# <a name="compare-azure-data-factory-versions-1-and-2"></a>Confrontare le versioni 1 e 2 di Azure Data Factory
Questo articolo mette a confronto le versioni 1 (V1) e 2 (V2) di Azure Data Factory. Per un'introduzione alla versione V1 del servizio, vedere [Data Factory versione 1 - Introduzione](v1/data-factory-introduction.md), mentre per la versione V2, vedere [Data Factory versione 2 - Introduzione](introduction.md).

## <a name="feature-comparison"></a>Confronto tra le funzionalità
La tabella seguente mette a confronto le funzionalità delle versioni V1 e V2. 

| Funzionalità | V1 | V2 | 
| ------- | --------- | --------- | 
| DATASETS | Vista denominata dei dati, che fa riferimento ai dati da usare nelle attività come input e output. I set di dati identificano i dati all'interno dei diversi archivi dati, come tabelle, file, cartelle e documenti. Un set di dati BLOB di Azure, ad esempio, specifica il contenitore BLOB e la cartella nell'archiviazione BLOB da cui l'attività deve leggere i dati.<br/><br/>La **disponibilità** definisce il modello di sezionamento della finestra di elaborazione per il set di dati (ad esempio ogni ora, ogni giorno e così via). | I set di dati sono analoghi nella versione V2. Non è tuttavia necessario definire pianificazioni della **disponibilità** per i set di dati. È possibile definire una risorsa trigger che può pianificare le pipeline da un paradigma di utilità di pianificazione basata sul tempo. Per altre informazioni, vedere [Trigger](concepts-pipeline-execution-triggers.md#triggers) e [Set di dati](concepts-datasets-linked-services.md). | 
| Servizi collegati | I servizi collegati sono molto simili a stringhe di connessione e definiscono le informazioni necessarie per la connessione di Data Factory a risorse esterne. | Analoghi ai servizi collegati di Data Factory V1, ma con una nuova proprietà **connectVia** per l'uso dell'ambiente di calcolo di Data Factory V2 Integration Runtime. Per ulteriori informazioni, vedere l'articolo relativo a [Integration Runtime](concepts-integration-runtime.md) e [Proprietà del servizio collegato per Archiviazione BLOB di Azure](connector-azure-blob-storage.md#linked-service-properties). |
| Pipeline | Una data factory può comprendere una o più pipeline. Una pipeline è un raggruppamento logico di attività che insieme eseguono un compito. Per pianificare ed eseguire le pipeline, si usano startTime, endTime e isPaused. | Le pipeline sono ancora gruppi di attività da eseguire sui dati. La pianificazione delle attività nella pipeline è tuttavia stata separata in nuove risorse trigger. È possibile pensare alle pipeline di Data Factory V2 come "unità di flusso di lavoro" che vengono pianificate separatamente tramite trigger. <br/><br/>Le pipeline non hanno "finestre" di esecuzione temporale in Data Factory V2. I concetti di startTime, endTime e isPaused di Data Factory V1 non sono più presenti in Data Factory V2. Per altre informazioni, vedere [Esecuzione e trigger di pipeline](concepts-pipeline-execution-triggers.md) e [Pipeline e attività](concepts-pipelines-activities.md). |
| Attività | Le attività definiscono le azioni da eseguire sui dati in una pipeline. Sono supportate attività di spostamento dei dati (attività di copia) e di trasformazione dei dati (ad esempio Hive, Pig e MapReduce). | In Data Factory V2 le attività sono ancora azioni definite all'interno di una pipeline. La versione V2 introduce le nuove [attività del flusso di controllo](concepts-pipelines-activities.md#control-activities). Queste attività vengono usate nel flusso di controllo (cicli e diramazioni). Le attività di spostamento dei dati e di trasformazione dei dati supportate nella versione V1 sono supportate anche nella versione V2. Nella versione V2 è possibile definire attività di trasformazione senza usare i set di dati. |
| Invio di attività e spostamento dei dati di tipo ibrido | La soluzione in precedenza nota come [Gateway di gestione dati](v1/data-factory-data-management-gateway.md) supportava lo spostamento dei dati tra ambiente locale e cloud. La soluzione è ora chiamata Integration Runtime.| Gateway di gestione dati è ora detto Integration Runtime (self-hosted). Fornisce le stesse funzionalità della versione V1. <br/><br/> Azure-SSIS Integration Runtime nella versione V2 supporta anche la distribuzione e l'esecuzione di pacchetti di SQL Server Integration Services (SSIS) nel cloud. Per informazioni dettagliate, vedere l'articolo su [Integration Runtime](concepts-integration-runtime.md).|
| Parametri | ND | I parametri sono coppie chiave-valore di impostazioni di configurazione di sola lettura definite nelle pipeline. È possibile passare argomenti per i parametri quando si esegue manualmente la pipeline. Se si usa un trigger per l'utilità di pianificazione, anche il trigger può passare valori per i parametri. Le attività all'interno della pipeline usano i valori dei parametri.  |
| Espressioni | Data Factory V1 consente l'uso di funzioni e variabili di sistema nelle query di selezione dei dati e nelle proprietà relative ad attività e set di dati. | In Data Factory V2 è possibile usare le espressioni in qualsiasi punto in un valore stringa JSON. Per altre informazioni, vedere [Espressioni e funzioni nella versione V2](control-flow-expression-language-functions.md).|
| Esecuzioni di pipeline | ND | Una singola istanza di esecuzione di pipeline. Ad esempio si dispone di una pipeline che viene eseguita alle ore 8, alle 9 e alle 10. Non vi saranno tre esecuzioni separate della pipeline (esecuzioni di pipeline) in questo caso. Ogni esecuzione di pipeline dispone di un ID di esecuzione della pipeline univoco, ovvero un GUID che definisce in modo univoco questa esecuzione di pipeline specifica. Le istanze delle esecuzioni di pipeline in genere vengono create mediante il passaggio di argomenti ai parametri definiti nelle pipeline. |
| Esecuzioni di attività | ND | Un'istanza di esecuzione di attività in una pipeline. | 
| Esecuzioni di trigger | ND | Un'istanza di esecuzione di trigger. Per altre informazioni, vedere [Trigger](concepts-pipeline-execution-triggers.md). |
| Pianificazione | La pianificazione si basa su ora di inizio e di fine della pipeline e disponibilità del set di dati. | Trigger dell'utilità di pianificazione o esecuzione tramite utilità di pianificazione esterna. Per altre informazioni, vedere [Esecuzione e trigger della pipeline](concepts-pipeline-execution-triggers.md). |

Le sezioni seguenti offrono altre informazioni sulle funzionalità della versione 2: 

## <a name="control-flow"></a>Flusso di controllo  
Per supportare modelli e flussi e di integrazione diversi nel data warehouse moderno, Data Factory V2 offre un nuovo modello di pipeline di dati flessibile che non è più associato ai dati delle serie temporali. Alcuni flussi comuni, in precedenza non possibili, sono ora abilitati:   

### <a name="chaining-activities"></a>Concatenamento di attività
Nella versione 1 è necessario configurare l'output di un'attività come input di un'altra attività per concatenare le attività. Nella versione V2 è possibile concatenare le attività in una sequenza all'interno di una pipeline. È possibile usare la proprietà **dependsOn** in una definizione di attività per concatenare l'attività a un'attività upstream. Per informazioni ed esempi, vedere [Pipeline e attività](concepts-pipelines-activities.md#multiple-activities-in-a-pipeline) e [Diramazione e concatenamento delle attività](tutorial-control-flow.md). 

### <a name="branching-activities"></a>Diramazione di attività
Nella versione V2 è ora possibile creare una diramazione delle attività all'interno di una pipeline. L'[attività If-condition](control-flow-if-condition-activity.md) svolge la stessa funzione dell'istruzione `if` nei linguaggi di programmazione. Valuta un set di attività se la condizione restituisce `true` e un altro set di attività se la condizione restituisce `false`. Per un esempio di attività con diramazione, vedere l'esercitazione [Diramazione e concatenamento delle attività](tutorial-control-flow.md).

### <a name="parameters"></a>Parametri 
È possibile definire parametri a livello di pipeline e passare argomenti quando si richiama la pipeline on demand o da un trigger. Le attività possono utilizzare gli argomenti passati alla pipeline. Per altre informazioni, vedere [Pipeline e trigger](concepts-pipeline-execution-triggers.md). 

### <a name="custom-state-passing"></a>Passaggio di stato personalizzato
Gli output delle attività che includono lo stato possono essere usati da un'attività successiva della pipeline. Nella definizione JSON di un'attività, ad esempio, è possibile accedere all'output dell'attività precedente tramite la sintassi seguente: `@activity('NameofPreviousActivity').output.value`. Questa funzionalità consente di creare flussi di lavoro in cui i valori possono passare nelle attività.

### <a name="looping-containers"></a>Contenitori di ciclo
L'[attività ForEach](control-flow-for-each-activity.md) definisce un flusso di controllo ripetuto nella pipeline. Questa attività viene usata per eseguire l'iterazione di una raccolta e attività specifiche in un ciclo. L'implementazione di cicli di questa attività è simile alla struttura di esecuzione in ciclo Foreach nei linguaggi di programmazione. 

L'attività [Until](control-flow-until-activity.md) svolge la stessa funzione della struttura di ciclo do-until nei linguaggi di programmazione. Esegue infatti un set di attività in un ciclo finché la condizione associata con l'attività restituisce true. È possibile specificare un valore di timeout per l'attività Until in Data Factory.  

### <a name="trigger-based-flows"></a>Flussi attivati da trigger
Le pipeline possono essere attivate su richiesta o con una pianificazione basata sul tempo. L'articolo [Pipeline e trigger](concepts-pipeline-execution-triggers.md) fornisce informazioni dettagliate sui trigger. 

### <a name="invoke-a-pipeline-from-another-pipeline"></a>Richiamare una pipeline da un'altra pipeline
L'[attività ExecutePipeline](control-flow-execute-pipeline-activity.md) consente a una pipeline di Data Factory di richiamare un'altra pipeline.

### <a name="delta-flows"></a>Flussi delta
Un importante caso d'uso nel modello ETL è quello dei "caricamenti delta", che consiste nel caricare solo i dati modificati dall'ultima iterazione di una pipeline. Le nuove funzionalità della versione 2, ad esempio l'[attività Lookup](control-flow-lookup-activity.md), la pianificazione flessibile e il flusso di controllo, consentono di attuare questo caso d'uso in modo naturale. Per un'esercitazione con istruzioni dettagliate, vedere [Esercitazione: Copia incrementale](tutorial-incremental-copy-powershell.md).

### <a name="other-control-flow-activities"></a>Altre attività di flusso di controllo
Ecco alcune altre attività di flusso di controllo supportate da Data Factory V2: 

Attività di controllo | Descrizione
---------------- | -----------
[ForEachActivity](control-flow-for-each-activity.md) | L'attività ForEach definisce un flusso di controllo ripetuto nella pipeline. Questa attività viene usata per eseguire l'iterazione su una raccolta ed esegue attività specificate in un ciclo. L'implementazione di cicli di questa attività è simile alla struttura di esecuzione cicli Foreach nei linguaggi di programmazione.
[WebActivity](control-flow-web-activity.md) | L'attività Web può essere usata per chiamare un endpoint REST personalizzato da una pipeline di Data Factory. È possibile passare set di dati e servizi collegati in modo che l'attività possa usarli e accedervi. 
[Attività di ricerca](control-flow-lookup-activity.md) | L'attività di ricerca può essere usata per la lettura o la ricerca di un nome/valore di record/tabella da qualsiasi origine esterna. Questo output può essere referenziato ulteriormente dalle attività successive. 
[Attività GetMetadata](control-flow-get-metadata-activity.md) | Questa attività può essere usata per recuperare i metadati di tutti i dati in Azure Data Factory. 
[Attività Wait](control-flow-wait-activity.md) | La pipeline attende (in pausa) il periodo di tempo specificato.

## <a name="deploy-ssis-packages-to-azure"></a>Distribuire i pacchetti SSIS in Azure 
Per spostare i carichi di lavoro di SQL Server Integration Services (SSIS) nel cloud, creare un'istanza di Data Factory V2 ed effettuare il provisioning di Azure-SSIS Integration Runtime (IR). Azure-SSIS IR è un cluster completamente gestito di macchine virtuali (nodi) di Azure dedicate all'esecuzione di pacchetti SSIS nel cloud. Una volta effettuato il provisioning di Azure-SSIS IR, è possibile usare gli stessi strumenti usati per distribuire i pacchetti SSIS in un ambiente SSIS locale. È ad esempio possibile usare SQL Server Data Tools (SSDT) o SQL Server Management Studio (SSMS) per distribuire pacchetti SSIS in questo runtime in Azure. Per istruzioni dettagliate, vedere l'esercitazione [Distribuire pacchetti SQL Server Integration Services in Azure](tutorial-deploy-ssis-packages-azure.md). 

## <a name="flexible-scheduling"></a>Pianificazione flessibile
In Data Factory V2 non è necessario definire pianificazioni della disponibilità per i set di dati. È possibile definire una risorsa trigger che può pianificare le pipeline da un paradigma di utilità di pianificazione basata sul tempo. È anche possibile passare parametri alle pipeline da un trigger per un modello di pianificazione/esecuzione flessibile. Le pipeline non hanno "finestre" di esecuzione temporale in Data Factory V2. I concetti di startTime, endTime e isPaused di Data Factory V1 non sono più presenti in Data Factory V2. Nell'articolo seguente è illustrato come creare e pianificare una pipeline in Data Factory V2: [Esecuzione e trigger di pipeline](concepts-pipeline-execution-triggers.md).

## <a name="support-for-more-data-stores"></a>Supporto per più archivi dati
La versione V2 supporta la copia di dati in e da più archivi dati rispetto alla versione V1. Per un elenco degli archivi dati supportati, vedere gli articoli seguenti:

- [V1 - Archivi dati supportati](v1/data-factory-data-movement-activities.md#supported-data-stores-and-formats)
- [V2 - Archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats)

## <a name="support-for-on-demand-spark-cluster"></a>Supporto per il cluster Spark on demand
La versione V2 supporta la creazione di un cluster HDInsight Spark on demand. Per creare un cluster Spark on demand, specificare il tipo di cluster Spark nella definizione del servizio collegato HDInsight on demand. È quindi possibile configurare l'attività Spark nella pipeline per usare il servizio collegato. In fase di esecuzione, quando l'attività viene eseguita, il servizio Data Factory crea automaticamente il cluster Spark. Per altre informazioni, vedere gli articoli seguenti:

- [Attività Spark nella versione V2](transform-data-using-spark.md)
- [Servizio collegato Azure HDInsight su richiesta](compute-linked-services.md#azure-hdinsight-on-demand-linked-service)

## <a name="custom-activities"></a>Attività personalizzate
Nella versione V1 il codice dell'attività DotNet (personalizzata) viene implementato mediante la creazione di un progetto di libreria di classi .NET con una classe che implementa il metodo Execute dell'interfaccia IDotNetActivity. Il codice personalizzato deve essere quindi scritto in .NET Framework 4.5.2 ed eseguito in nodi del pool di Azure Batch basati su Windows. 

Per un'attività personalizzata con la versione V2 non è necessario implementare un'interfaccia .NET. È ora possibile eseguire direttamente i comandi, gli script e il codice personalizzato compilato come eseguibile. 

Per altre informazioni, vedere [Differenza tra l'attività personalizzata nelle versioni V1 e V2](transform-data-using-dotnet-custom-activity.md#difference-between-custom-activity-in-azure-data-factory-v2-and-custom-dotnet-activity-in-azure-data-factory-v1).

## <a name="sdks"></a>SDK
La versione 2 di Data Factory offre un set più ampio di SDK che è possibile usare per creare, gestire e monitorare le pipeline.

- **.NET SDK**: NET SDK è aggiornato per la versione V2. 
- **PowerShell**: i cmdlet di PowerShell sono aggiornati per la versione V2. I cmdlet per la versione V2 hanno **DataFactoryV2** nel nome. Ad esempio: Get-AzureRmDataFactoryV2. 
- **Python SDK**: questo SDK è una novità per la versione V2.
- **API REST**: l'API REST è aggiornata per la versione V2. 

Gli SDK aggiornati per la versione V2 non sono compatibili con i client versione 1. 

## <a name="authoring-experience"></a>Esperienza di creazione
Data Factory V1 consente di creare pipeline usando l'editor di Data Factory nel portale di Azure. Attualmente, Data Factory V2 supporta solo la creazione di data factory a livello di codice (.NET SDK, API REST, PowerShell, Python e così via). Non è ancora disponibile il supporto dell'interfaccia utente.  Anche Data Factory V1 offre supporto per la creazione con SDK, REST e PowerShell.

## <a name="monitoring-experience"></a>Esperienza di monitoraggio
Nella versione V2 è anche possibile eseguire il monitoraggio di data factory con [Monitoraggio di Azure](monitor-using-azure-monitor.md). I nuovi cmdlet di PowerShell supportano il monitoraggio delle istanze di [Integration Runtime](monitor-integration-runtime.md). Sia la versione V1 che la versione V2 supportano il monitoraggio visivo tramite l'applicazione di monitoraggio che è possibile avviare dal portale di Azure.


## <a name="next-steps"></a>Passaggi successivi
Le seguenti guide introduttive offrono istruzioni dettagliate per la creazione di data factory: [PowerShell](quickstart-create-data-factory-powershell.md), [.NET](quickstart-create-data-factory-dot-net.md), [Python](quickstart-create-data-factory-python.md), [API REST](quickstart-create-data-factory-rest-api.md). 
