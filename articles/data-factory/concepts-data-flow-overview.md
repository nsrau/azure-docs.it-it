---
title: Flussi di dati di mapping
description: Panoramica del mapping dei flussi di dati in Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/28/2020
ms.openlocfilehash: 39d1f15b771168b618bfbc4951f2036a8b95b027
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418457"
---
# <a name="what-are-mapping-data-flows"></a>Che cosa sono i flussi di dati di mapping?

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Mapping data flows are visually designed data transformations in Azure Data Factory. I flussi di dati consentono ai data engineer di sviluppare la logica di trasformazione dei dati grafici senza scrivere codice. I flussi di dati risultanti vengono eseguiti come attività all'interno di pipeline di Azure Data Factory che usano cluster Apache Spark con scalabilità orizzontale. Le attività del flusso di dati possono essere impegnate tramite le funzionalità di pianificazione, controllo, flusso e monitoraggio di Data Factory esistenti.

La mappatura dei flussi di dati offre un'esperienza completamente visiva senza necessità di codifica. I flussi di dati vengono eseguiti nel cluster di esecuzione per l'elaborazione dei dati con scalabilità orizzontale. Azure Data Factory gestisce tutta la traduzione del codice, l'ottimizzazione del percorso e l'esecuzione dei processi del flusso di dati.

## <a name="getting-started"></a>Introduzione

Per creare un flusso di dati, selezionare il segno più in **Risorse di fabbrica**, quindi selezionare Flusso di **dati**. 

![Nuovo flusso di dati](media/data-flow/newdataflow2.png "nuovo flusso di dati")

Questa azione consente di accedere all'area di disegno del flusso di dati, in cui è possibile creare la logica di trasformazione. Selezionare **Aggiungi origine** per avviare la configurazione della trasformazione di origine. Per ulteriori informazioni, consultate [Trasformazione Origine](data-flow-source.md).

## <a name="data-flow-canvas"></a>Area di disegno flusso di datiData flow canvas

L'area di disegno del flusso di dati è suddivisa in tre parti: la barra superiore, il grafico e il pannello di configurazione. 

![Canvas](media/data-flow/canvas1.png "Canvas")

### <a name="graph"></a>Grafico

Il grafico visualizza il flusso di trasformazione. Mostra il lignaggio dei dati di origine durante il flusso in uno o più sink. Per aggiungere una nuova origine, selezionare **Aggiungi origine**. Per aggiungere una nuova trasformazione, selezionare il segno più in basso a destra di una trasformazione esistente.

![Canvas](media/data-flow/canvas2.png "Canvas")

### <a name="azure-integration-runtime-data-flow-properties"></a>Proprietà del flusso di dati di runtime di integrazione di AzureAzure integration runtime data flow properties

![Pulsante Debug](media/data-flow/debugbutton.png "Pulsante Debug")

Quando si inizia a usare i flussi di dati in ADF, si desidera attivare l'opzione "Debug" per i flussi di dati nella parte superiore dell'interfaccia utente del browser. In questo modo viene eseguito lo spin-up di un cluster Spark da utilizzare per il debug interattivo, le anteprime dei dati e le esecuzioni di debug della pipeline. È possibile impostare le dimensioni del cluster utilizzato scegliendo un runtime di [integrazione](concepts-integration-runtime.md)di Azure personalizzato. La sessione di debug rimane attiva fino a 60 minuti dopo l'ultima anteprima dei dati o l'ultima esecuzione della pipeline di debug.

Quando si eseguono le pipeline con le attività del flusso di dati, ADF usa il runtime di integrazione di Azure associato [all'attività](control-flow-execute-data-flow-activity.md) nella proprietà "Esegui su".

Il runtime di integrazione di Azure predefinito è un piccolo cluster di nodi a lavoro singolo a 4 core che consente di visualizzare in anteprima i dati ed eseguire rapidamente le pipeline di debug a costi minimi. Impostare una configurazione di informazioni di riesecuzione/accesso condiviso di Azure più grande se si eseguono operazioni su set di dati di grandi dimensioni.

È possibile indicare ad ADF di gestire un pool di risorse cluster (VM) impostando un valore TTL nelle proprietà del flusso di dati di Runtime di runtime di Azure.You can struct ADF to maintain a pool of cluster resources (VMs) by setting a TTL in the Azure IR data flow properties. Questa azione comporta un'esecuzione più rapida dei processi nelle attività successive.

#### <a name="azure-integration-runtime-and-data-flow-strategies"></a>Strategie di runtime e flusso di dati di integrazione di AzureAzure integration runtime and data flow strategies

##### <a name="execute-data-flows-in-parallel"></a>Eseguire flussi di dati in paralleloExecute data flows in parallel

Se si eseguono flussi di dati in una pipeline in parallelo, ADF esegue cluster Spark separati per ogni esecuzione di attività in base alle impostazioni del runtime di integrazione di Azure associate a ogni attività. Per progettare esecuzioni parallele nelle pipeline ADF, aggiungere le attività del flusso di dati senza vincoli di precedenza nell'interfaccia utente.

Di queste tre opzioni, questa opzione probabilmente viene eseguita nel minor tempo possibile. Tuttavia, ogni flusso di dati parallelo viene eseguito contemporaneamente su cluster separati, pertanto l'ordinamento degli eventi non è deterministico.

Se si eseguono le attività del flusso di dati in parallelo all'interno delle pipeline, è consigliabile non usare TTL. Questa azione è dovuto al fatto che le esecuzioni parallele del flusso di dati contemporaneamente usando lo stesso runtime di integrazione di Azure generano più istanze di pool a caldo per la data factory.

##### <a name="overload-single-data-flow"></a>Sovraccaricare un singolo flusso di datiOverload single data flow

Se si inserisce tutta la logica all'interno di un singolo flusso di dati, ADF esegue lo stesso contesto di esecuzione del processo in una singola istanza del cluster Spark.If you put all of your logic inside a single data flow, ADF executes that same job execution context on a single Spark cluster instance.

Questa opzione può essere più complessa da seguire e risolvere i problemi perché le regole di business e la logica di business possono essere mescolate insieme. Questa opzione non fornisce molta riusabilità.

##### <a name="execute-data-flows-serially"></a>Eseguire flussi di dati in modo seriale

Se si eseguono le attività del flusso di dati in serie nella pipeline ed è stato impostato un valore TTL nella configurazione di Runtime di runtime di Azure, ADF riutilizza le risorse di calcolo (VM), con conseguente tempi di esecuzione successivi più rapidi. Si riceve comunque un nuovo contesto Spark per ogni esecuzione.

Di queste tre opzioni, questa azione richiede probabilmente più tempo per eseguire end-to-end. Ma fornisce una netta separazione delle operazioni logiche in ogni passaggio del flusso di dati.

### <a name="configuration-panel"></a>Pannello di configurazione

Il pannello di configurazione mostra le impostazioni specifiche della trasformazione attualmente selezionata. Se non è selezionata alcuna trasformazione, viene visualizzato il flusso di dati. Nella configurazione complessiva del flusso di dati, è possibile modificare il nome e la descrizione nella scheda **Generale** o aggiungere parametri tramite la scheda **Parametri.** Per ulteriori informazioni, vedere [Mapping dei parametri del flusso di dati](parameters-data-flow.md).

Ogni trasformazione contiene almeno quattro schede di configurazione.

#### <a name="transformation-settings"></a>Impostazioni di trasformazione

La prima scheda nel riquadro di configurazione di ogni trasformazione contiene le impostazioni specifiche di tale trasformazione. Per altre informazioni, vedere la pagina della documentazione di tale trasformazione.

![Scheda Impostazioni di origine](media/data-flow/source1.png "Scheda Impostazioni di origine")

#### <a name="optimize"></a>Ottimizzazione

La scheda **Ottimizza** contiene le impostazioni per configurare gli schemi di partizionamento.

![Ottimizzare](media/data-flow/optimize1.png "Ottimizzazione")

L'impostazione predefinita è Usa partizionamento corrente , che indica ad Azure Data Factory di usare lo schema di partizionamento nativo per i flussi di dati in esecuzione su Spark.The default setting is **Use current partitioning**, which isstructs Azure Data Factory to use the partitioning scheme native to data flows running on Spark. Nella maggior parte degli scenari, è consigliabile questa impostazione.

In alcuni casi è possibile modificare il partizionamento. Ad esempio, se si desidera eseguire l'output delle trasformazioni in un singolo file nel laghetto, selezionare **Partizione singola** in una trasformazione sink.

Un altro caso in cui è possibile controllare gli schemi di partizionamento è l'ottimizzazione delle prestazioni. La regolazione del partizionamento consente di controllare la distribuzione dei dati tra i nodi di calcolo e le ottimizzazioni della località dei dati che possono avere effetti positivi e negativi sulle prestazioni complessive del flusso di dati. Per ulteriori informazioni, vedere la Guida alle [prestazioni del flusso](concepts-data-flow-performance.md)di dati .

Per modificare il partizionamento in qualsiasi trasformazione, selezionare la scheda **Ottimizza** e selezionare il pulsante di opzione **Imposta partizionamento.** Viene presentata una serie di opzioni per il partizionamento. Il metodo migliore di partizionamento varia in base ai volumi di dati, alle chiavi candidate, ai valori Null e alla cardinalità. 

Una procedura consigliata consiste nell'iniziare con il partizionamento predefinito e quindi provare diverse opzioni di partizionamento. È possibile eseguire il test usando le esecuzioni di debug della pipeline e visualizzare il tempo di esecuzione e l'utilizzo delle partizioni in ogni raggruppamento di trasformazione dalla visualizzazione di monitoraggio. Per ulteriori informazioni, vedere [Monitoraggio dei flussi](concepts-data-flow-monitoring.md)di dati .

Sono disponibili le seguenti opzioni di partizionamento.

##### <a name="round-robin"></a>Robin rotondo 

Round robin è una partizione semplice che distribuisce automaticamente i dati in modo equo tra le partizioni. Usa il round robin quando non hai buoni candidati chiave per implementare una strategia di partizionamento intelligente e solida. È possibile impostare il numero di partizioni fisiche.

##### <a name="hash"></a>Hash

Azure Data Factory produce un hash di colonne per produrre partizioni uniformi in modo che le righe con valori simili cadano nella stessa partizione. Quando si utilizza l'opzione Hash, verificare la possibile inclinazione della partizione. È possibile impostare il numero di partizioni fisiche.

##### <a name="dynamic-range"></a>Gamma dinamica

L'intervallo dinamico utilizza intervalli dinamici Spark basati sulle colonne o espressioni fornite. È possibile impostare il numero di partizioni fisiche. 

##### <a name="fixed-range"></a>Gamma fissa

Creare un'espressione che fornisce un intervallo fisso per i valori all'interno delle colonne di dati partizionati. Per evitare l'inclinazione della partizione, è necessario avere una buona conoscenza dei dati prima di utilizzare questa opzione. I valori immessi per l'espressione vengono utilizzati come parte di una funzione di partizione. È possibile impostare il numero di partizioni fisiche.

##### <a name="key"></a>Chiave

Se si ha una buona conoscenza della cardinalità dei dati, il partizionamento delle chiavi potrebbe essere una buona strategia. Il partizionamento delle chiavi crea partizioni per ogni valore univoco nella colonna. Non è possibile impostare il numero di partizioni perché il numero è basato su valori univoci nei dati.

#### <a name="inspect"></a>Controllare

La scheda **Controlla** fornisce una visualizzazione dei metadati del flusso di dati che si sta trasformando. È possibile visualizzare i conteggi delle colonne, le colonne modificate, le colonne aggiunte, i tipi di dati, l'ordine delle colonne e i riferimenti di colonna. **Inspect** è una visualizzazione di sola lettura dei metadati. Non è necessario che la modalità di debug sia abilitata per visualizzare i metadati nel riquadro **Controlla.You** don't need to have debug mode enabled to see metadata in the Inspect pane.

![Controllare](media/data-flow/inspect1.png "Controllare")

Quando si modifica la forma dei dati tramite le trasformazioni, nel riquadro **Controlla** verrà visualizzato il flusso delle modifiche ai metadati. Se non è presente uno schema definito nella trasformazione di origine, i metadati non saranno visibili nel riquadro **Controlla.If** there isn't a defined schema in your source transformation, then metadata won't be visible in the Inspect pane. La mancanza di metadati è comune negli scenari di deriva dello schema.

#### <a name="data-preview"></a>Anteprima dati

Se la modalità di debug è attiva, la scheda **Anteprima dati** fornisce uno snapshot interattivo dei dati a ogni trasformazione. Per ulteriori informazioni, vedere [Anteprima dei dati in modalità di debug](concepts-data-flow-debug-mode.md#data-preview).

### <a name="top-bar"></a>Barra superiore

La barra superiore contiene azioni che influiscono sull'intero flusso di dati, ad esempio il salvataggio e la convalida. È inoltre possibile passare dalla modalità grafico alla modalità di configurazione e viceversa utilizzando i **pulsanti Mostra grafico** e **Nascondi grafico.**

![Nascondi grafico](media/data-flow/hideg.png "Nascondi grafico")

Se si nasconde il grafico, è possibile sfogliare i nodi di trasformazione lateralmente tramite i pulsanti **Precedente** e **Successivo.**

![Pulsanti Precedente e Successivo](media/data-flow/showhide.png "pulsanti precedente e successivo")

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come creare una [trasformazione di origine.](data-flow-source.md)
* Informazioni su come compilare i flussi di dati in [modalità di debug.](concepts-data-flow-debug-mode.md)
