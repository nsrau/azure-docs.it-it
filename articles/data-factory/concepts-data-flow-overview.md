---
title: Flussi di dati di mapping
description: Cenni preliminari sul mapping dei flussi di dati in Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/09/2020
ms.openlocfilehash: 850879675d4554329f24c86f2ac28660b303084c
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475567"
---
# <a name="what-are-mapping-data-flows"></a>Che cosa sono i flussi di dati di mapping?

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I flussi di dati di mapping sono le trasformazioni di dati progettate visivamente in Azure Data Factory. I flussi di dati consentono agli ingegneri di dati di sviluppare la logica di trasformazione dei dati grafici senza scrivere codice. I flussi di dati risultanti vengono eseguiti come attività all'interno di Azure Data Factory pipeline che usano cluster Apache Spark con scalabilità orizzontale. Le attività del flusso di dati possono essere impegnate tramite le funzionalità di pianificazione, controllo, flusso e monitoraggio Data Factory esistenti.

I flussi di dati di mapping forniscono un'esperienza visiva completamente senza necessità di scrivere codice. I flussi di dati vengono eseguiti nel cluster di esecuzione per l'elaborazione dei dati con scalabilità orizzontale. Azure Data Factory gestisce tutta la conversione del codice, l'ottimizzazione del percorso e l'esecuzione dei processi del flusso di dati.

![Architettura](media/data-flow/adf-data-flows.png "Architecture")

## <a name="getting-started"></a>Guida introduttiva

Per creare un flusso di dati, selezionare il segno più in **risorse Factory**, quindi selezionare **flusso di dati**. 

![Nuovo flusso di dati](media/data-flow/newdataflow2.png "nuovo flusso di dati")

Questa azione consente di passare all'area di disegno del flusso di dati, in cui è possibile creare la logica di trasformazione. Selezionare **Aggiungi origine** per avviare la configurazione della trasformazione origine. Per ulteriori informazioni, vedere [trasformazione origine](data-flow-source.md).

## <a name="data-flow-canvas"></a>Area di disegno del flusso di dati

L'area di disegno del flusso di dati è suddivisa in tre parti: la barra superiore, il grafico e il pannello di configurazione. 

![Canvas](media/data-flow/canvas1.png "Canvas")

### <a name="graph"></a>Grafico

Il grafico mostra il flusso di trasformazione. Mostra la derivazione dei dati di origine durante il flusso in uno o più sink. Per aggiungere una nuova origine, selezionare **Aggiungi origine**. Per aggiungere una nuova trasformazione, selezionare il segno più in basso a destra di una trasformazione esistente.

![Canvas](media/data-flow/canvas2.png "Canvas")

### <a name="azure-integration-runtime-data-flow-properties"></a>Proprietà del flusso di dati del runtime di integrazione di Azure

![Pulsante debug](media/data-flow/debugbutton.png "Pulsante debug")

Quando si inizia a usare i flussi di dati in ADF, è necessario attivare l'opzione "debug" per i flussi di dati nella parte superiore dell'interfaccia utente del browser. Questo consente di creare un cluster Spark da usare per il debug interattivo, le anteprime dei dati e le esecuzioni di debug della pipeline. È possibile impostare le dimensioni del cluster usato scegliendo una [Azure Integration Runtime](concepts-integration-runtime.md)personalizzata. La sessione di debug rimane attiva per un massimo di 60 minuti dopo l'ultima anteprima dei dati o dell'ultima esecuzione della pipeline di debug.

Quando si rendere operativo le pipeline con le attività flusso di dati, ADF usa il Azure Integration Runtime associato all' [attività](control-flow-execute-data-flow-activity.md) nella proprietà "Esegui su".

Il Azure Integration Runtime predefinito è un piccolo cluster di nodi di lavoro singolo a 4 core che consente di visualizzare in anteprima i dati e di eseguire rapidamente pipeline di debug a costi minimi. Impostare una configurazione di Azure IR più grande se si eseguono operazioni su set di impostazioni di grandi dimensioni.

È possibile indicare ad ADF di mantenere un pool di risorse cluster (VM) impostando una durata (TTL) nelle proprietà del flusso di dati Azure IR. Questa azione comporta una maggiore esecuzione del processo sulle attività successive.

#### <a name="azure-integration-runtime-and-data-flow-strategies"></a>Strategie del runtime di integrazione di Azure e del flusso di dati

##### <a name="execute-data-flows-in-parallel"></a>Esecuzione di flussi di dati in parallelo

Se si eseguono flussi di dati in una pipeline in parallelo, ADF crea cluster Spark distinti per ogni esecuzione di attività in base alle impostazioni del Azure Integration Runtime associate a ogni attività. Per progettare esecuzioni parallele nelle pipeline di ADF, aggiungere le attività del flusso di dati senza vincoli di precedenza nell'interfaccia utente.

Di queste tre opzioni, questa opzione viene probabilmente eseguita nell'intervallo di tempo più breve. Tuttavia, ogni flusso di dati parallelo viene eseguito contemporaneamente in cluster distinti, quindi l'ordine degli eventi è non deterministico.

Se si eseguono le attività del flusso di dati in parallelo all'interno delle pipeline, è consigliabile non usare la durata (TTL). Questa azione è dovuta al fatto che le esecuzioni parallele del flusso di dati che utilizzano simultaneamente lo stesso Azure Integration Runtime generano più istanze di pool calde per l'data factory.

##### <a name="overload-single-data-flow"></a>Overload di un singolo flusso di dati

Se si inserisce tutta la logica all'interno di un singolo flusso di dati, ADF esegue lo stesso contesto di esecuzione del processo in una singola istanza del cluster Spark.

Questa opzione può essere più complessa da seguire e risolvere i problemi perché le regole di business e la logica di business possono essere confuse tra loro. Questa opzione non fornisce inoltre una maggiore riusabilità.

##### <a name="execute-data-flows-sequentially"></a>Eseguire flussi di dati in sequenza

Se si eseguono le attività del flusso di dati in sequenza nella pipeline ed è stata impostata una durata (TTL) nella configurazione di Azure IR, ADF riutilizzerà le risorse di calcolo (VM), ottenendo tempi di esecuzione successivi più rapidi. Si riceverà comunque un nuovo contesto Spark per ogni esecuzione.

Di queste tre opzioni, questa azione richiede probabilmente il tempo più lungo per l'esecuzione end-to-end. Ma fornisce una netta separazione delle operazioni logiche in ogni passaggio del flusso di dati.

### <a name="configuration-panel"></a>Pannello di configurazione

Il pannello configurazione Mostra le impostazioni specifiche della trasformazione attualmente selezionata. Se non è selezionata alcuna trasformazione, viene visualizzato il flusso di dati. Nella configurazione del flusso di dati complessiva, è possibile modificare il nome e la descrizione nella scheda **generale** oppure aggiungere parametri tramite la scheda **parametri** . Per ulteriori informazioni, vedere [mapping dei parametri del flusso di dati](parameters-data-flow.md).

Ogni trasformazione contiene almeno quattro schede di configurazione.

#### <a name="transformation-settings"></a>Impostazioni di trasformazione

La prima scheda del riquadro di configurazione di ogni trasformazione contiene le impostazioni specifiche della trasformazione. Per ulteriori informazioni, vedere la pagina della documentazione relativa alla trasformazione.

![Scheda Impostazioni di origine](media/data-flow/source1.png "Scheda Impostazioni di origine")

#### <a name="optimize"></a>Ottimizzazione

La scheda **ottimizza** contiene le impostazioni per la configurazione degli schemi di partizionamento. Per ulteriori informazioni su come ottimizzare i flussi di dati, vedere la [Guida alle prestazioni del flusso di dati di mapping](concepts-data-flow-performance.md).

![Optimize](media/data-flow/optimize.png "Ottimizzazione") (Ottimizza)

#### <a name="inspect"></a>Controllare

La scheda **Controlla** fornisce una visualizzazione dei metadati del flusso di dati che si sta trasformando. È possibile visualizzare i conteggi delle colonne, le colonne modificate, le colonne aggiunte, i tipi di dati, l'ordine delle colonne e i riferimenti alle colonne. **Esaminare** è una visualizzazione di sola lettura dei metadati. Non è necessario che la modalità di debug sia abilitata per visualizzare i metadati nel riquadro di **controllo** .

![Controllare](media/data-flow/inspect1.png "Controllare")

Quando si modifica la forma dei dati tramite le trasformazioni, il flusso delle modifiche dei metadati verrà visualizzato nel riquadro **Controlla** . Se non è presente uno schema definito nella trasformazione origine, i metadati non saranno visibili nel riquadro **ispezione** . La mancanza di metadati è comune negli scenari di drifting dello schema.

#### <a name="data-preview"></a>Anteprima dati

Se la modalità di debug è attiva, la scheda **Anteprima dati** fornisce uno snapshot interattivo dei dati a ogni trasformazione. Per altre informazioni, vedere [anteprima dei dati in modalità debug](concepts-data-flow-debug-mode.md#data-preview).

### <a name="top-bar"></a>Barra superiore

La barra superiore contiene azioni che interessano l'intero flusso di dati, ad esempio il salvataggio e la convalida. È anche possibile passare tra le modalità grafico e configurazione usando i pulsanti **Mostra grafico** e **Nascondi grafico** .

![Nascondi grafico](media/data-flow/hideg.png "Nascondi grafico")

Se si nasconde il grafo, è possibile esplorare i nodi di trasformazione in un secondo momento tramite i pulsanti **indietro** e **Avanti** .

![Pulsanti avanti e indietro](media/data-flow/showhide.png "pulsanti avanti e indietro")

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come creare una [trasformazione di origine](data-flow-source.md).
* Informazioni su come creare flussi di dati in [modalità debug](concepts-data-flow-debug-mode.md).
