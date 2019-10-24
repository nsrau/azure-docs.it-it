---
title: Mapping di flussi di dati in Azure Data Factory | Microsoft Docs
description: Cenni preliminari sul mapping dei flussi di dati in Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/7/2019
ms.openlocfilehash: 7f6c131737ca63d120e111b3ef4504a36dbd7fc1
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754701"
---
# <a name="what-are-mapping-data-flows"></a>Che cosa sono i flussi di dati di mapping?

I flussi di dati di mapping sono le trasformazioni di dati progettate visivamente in Azure Data Factory. I flussi di dati consentono agli ingegneri di dati di sviluppare la logica di trasformazione dei dati grafici senza scrivere codice. I flussi di dati risultanti vengono eseguiti come attività all'interno di Azure Data Factory pipeline che usano cluster Spark con scalabilità orizzontale. Le attività del flusso di dati possono essere operative tramite le funzionalità di pianificazione, controllo, flusso e monitoraggio Data Factory esistenti.

Il mapping di flussi di dati offre un'esperienza visiva completamente senza necessità di scrivere codice. I flussi di dati vengono eseguiti nel proprio cluster di esecuzione per l'elaborazione dei dati con scalabilità orizzontale. Azure Data Factory gestisce tutta la conversione del codice, l'ottimizzazione del percorso e l'esecuzione dei processi del flusso di dati.

## <a name="getting-started"></a>Inizia ora

Per creare un flusso di dati, selezionare il segno più in **risorse Factory**, quindi selezionare **flusso di dati**. 

![Nuovo flusso di dati](media/data-flow/newdataflow2.png "nuovo flusso di dati")

Viene quindi riportata l'area di disegno del flusso di dati in cui è possibile creare la logica di trasformazione. Selezionare **Aggiungi origine** per avviare la configurazione della trasformazione origine. Per ulteriori informazioni, vedere [trasformazione origine](data-flow-source.md).

## <a name="data-flow-canvas"></a>Area di disegno del flusso di dati

L'area di disegno del flusso di dati è suddivisa in tre parti: la barra superiore, il grafico e il pannello di configurazione. 

![Disegno](media/data-flow/canvas1.png "Canvas")

### <a name="graph"></a>Grafo

Il grafico mostra il flusso di trasformazione. Mostra la derivazione dei dati di origine durante il flusso in uno o più sink. Per aggiungere una nuova origine, selezionare **Aggiungi origine**. Per aggiungere una nuova trasformazione, selezionare il segno più in basso a destra di una trasformazione esistente.

![Disegno](media/data-flow/canvas2.png "Canvas")

### <a name="azure-integration-runtime-data-flow-properties"></a>Proprietà del flusso di dati del runtime di integrazione di Azure

![Pulsante debug](media/data-flow/debugbutton.png "Pulsante debug")

Quando si inizia a usare i flussi di dati in ADF, è necessario attivare l'opzione "debug" per i flussi di dati nella parte superiore dell'interfaccia utente del browser. Verrà avviata una Azure Databricks cluster da utilizzare per il debug interattivo, le anteprime dei dati e le esecuzioni di debug della pipeline. È possibile impostare le dimensioni del cluster usato scegliendo una [Azure Integration Runtime](concepts-integration-runtime.md)personalizzata. La sessione di debug resterà attiva per un massimo di 60 minuti dopo l'ultima esecuzione dell'anteprima dei dati o dell'ultima esecuzione della pipeline di debug.

Quando si rendere operativo le pipeline con le attività flusso di dati, ADF utilizzerà il Azure Integration Runtime associato all' [attività](control-flow-execute-data-flow-activity.md) nella proprietà "Esegui su".

Il Azure Integration Runtime predefinito è un piccolo cluster di nodi di lavoro singolo a 4 core progettato per consentire all'utente di visualizzare in anteprima i dati ed eseguire rapidamente pipeline di debug a costi minimi. Impostare una configurazione di Azure IR più grande se si eseguono operazioni su set di impostazioni di grandi dimensioni.

È possibile indicare ad ADF di mantenere un pool di risorse cluster (VM) impostando una durata (TTL) nelle proprietà del flusso di dati Azure IR. Questo comporterà una maggiore esecuzione del processo sulle attività successive.

#### <a name="azure-integration-runtime-and-data-flow-strategies"></a>Strategie del runtime di integrazione di Azure e del flusso di dati

##### <a name="execute-data-flows-in-parallel"></a>Esecuzione di flussi di dati in parallelo

Se i flussi di dati vengono eseguiti in una pipeline in parallelo, ADF eseguirà il rollup di cluster Azure Databricks distinti per ogni esecuzione di attività in base alle impostazioni del Azure Integration Runtime associate a ogni attività. Per progettare esecuzioni parallele nelle pipeline di ADF, aggiungere le attività del flusso di dati senza vincoli di precedenza nell'interfaccia utente.

Di queste tre opzioni, questa opzione verrà probabilmente eseguita nell'intervallo di tempo più breve. Tuttavia, ogni flusso di dati parallelo verrà eseguito contemporaneamente in cluster distinti, quindi l'ordine degli eventi è non deterministico.

##### <a name="overload-single-data-flow"></a>Overload di un singolo flusso di dati

Se si inserisce tutta la logica all'interno di un singolo flusso di dati, ADF verrà eseguito tutti nello stesso contesto di esecuzione del processo in una singola istanza del cluster Spark.

Questa opzione può essere più difficile da seguire e risolvere i problemi perché le regole di business e la logica di business saranno confuse tra loro. Questa opzione non garantisce inoltre una riusabilità eccessiva.

##### <a name="execute-data-flows-serially"></a>Eseguire flussi di dati in modo seriale

Se si eseguono le attività del flusso di dati in serie nella pipeline ed è stata impostata una durata (TTL) nella configurazione di Azure IR, ADF riutilizzerà le risorse di calcolo (VM), ottenendo tempi di esecuzione successivi più rapidi. Si riceverà comunque un nuovo contesto Spark per ogni esecuzione.

Di queste tre opzioni, il tempo necessario per l'esecuzione end-to-end richiederà più tempo. Ma fornisce una netta separazione delle operazioni logiche in ogni passaggio del flusso di dati.

### <a name="configuration-panel"></a>Pannello di configurazione

Il pannello configurazione Mostra le impostazioni specifiche della trasformazione attualmente selezionata. Se non è selezionata alcuna trasformazione, viene visualizzato il flusso di dati. Nella configurazione del flusso di dati complessiva, è possibile modificare il nome e la descrizione nella scheda **generale** oppure aggiungere parametri tramite la scheda **parametri** . Per ulteriori informazioni, vedere [mapping dei parametri del flusso di dati](parameters-data-flow.md).

Ogni trasformazione include almeno quattro schede di configurazione.

#### <a name="transformation-settings"></a>Impostazioni di trasformazione

La prima scheda del riquadro di configurazione di ogni trasformazione contiene le impostazioni specifiche della trasformazione. Per ulteriori informazioni, vedere la pagina della documentazione relativa alla trasformazione.

![Scheda Impostazioni di origine](media/data-flow/source1.png "Scheda Impostazioni di origine")

#### <a name="optimize"></a>Ottimizzare

La scheda **ottimizza** contiene le impostazioni per la configurazione degli schemi di partizionamento.

![Optimize](media/data-flow/optimize1.png "Ottimizzare") (Ottimizza)

L'impostazione predefinita è **Usa il partizionamento corrente**, che indica Azure Data Factory di usare lo schema di partizionamento nativo per i flussi di dati in esecuzione in Spark. Nella maggior parte degli scenari è consigliabile questa impostazione.

Sono presenti istanze in cui è possibile modificare il partizionamento. Se ad esempio si desidera restituire le trasformazioni a un singolo file nel lago, selezionare **partizione singola** in una trasformazione sink.

Un altro caso in cui si potrebbe voler controllare gli schemi di partizionamento è l'ottimizzazione delle prestazioni. La regolazione del partizionamento consente di controllare la distribuzione dei dati tra i nodi di calcolo e le ottimizzazioni di località dei dati che possono avere effetti positivi e negativi sulle prestazioni complessive del flusso di dati. Per ulteriori informazioni, vedere la [Guida alle prestazioni del flusso di dati](concepts-data-flow-performance.md).

Per modificare il partizionamento in qualsiasi trasformazione, selezionare la scheda **ottimizza** e selezionare il pulsante di opzione **imposta partizionamento** . Verrà visualizzata una serie di opzioni per il partizionamento. Il metodo migliore per il partizionamento varia a seconda dei volumi di dati, delle chiavi candidate, dei valori null e della cardinalità. 

Una procedura consigliata consiste nell'iniziare con il partizionamento predefinito, quindi provare diverse opzioni di partizionamento. È possibile eseguire il test usando le esecuzioni di debug della pipeline e visualizzare l'ora di esecuzione e l'utilizzo delle partizioni in ogni raggruppamento di trasformazione dalla visualizzazione monitoraggio. Per altre informazioni, vedere [monitoraggio dei flussi di dati](concepts-data-flow-monitoring.md).

Sono disponibili le seguenti opzioni di partizionamento.

##### <a name="round-robin"></a>Round Robin 

Round Robin è una semplice partizione che distribuisce automaticamente i dati in modo uniforme tra le partizioni. Usare round robin quando non si hanno validi candidati chiave per implementare una strategia di partizionamento intelligente solida. È possibile impostare il numero di partizioni fisiche.

##### <a name="hash"></a>Hash

Azure Data Factory genera un hash di colonne per produrre partizioni uniformi in modo che le righe con valori simili rientrino nella stessa partizione. Quando si usa l'opzione hash, verificare la possibile asimmetria della partizione. È possibile impostare il numero di partizioni fisiche.

##### <a name="dynamic-range"></a>Intervallo dinamico

L'intervallo dinamico utilizzerà intervalli dinamici Spark basati sulle colonne o sulle espressioni fornite. È possibile impostare il numero di partizioni fisiche. 

##### <a name="fixed-range"></a>Intervallo fisso

Consente di compilare un'espressione che fornisce un intervallo fisso per i valori nelle colonne di dati partizionati. Per evitare l'inclinazione della partizione, è necessario avere una conoscenza corretta dei dati prima di utilizzare questa opzione. I valori immessi per l'espressione verranno utilizzati come parte di una funzione di partizione. È possibile impostare il numero di partizioni fisiche.

##### <a name="key"></a>Chiave

Se si dispone di una conoscenza corretta della cardinalità dei dati, il partizionamento delle chiavi potrebbe essere una strategia efficace. Il partizionamento con chiavi crea partizioni per ogni valore univoco nella colonna. Non è possibile impostare il numero di partizioni perché il numero sarà basato sui valori univoci nei dati.

#### <a name="inspect"></a>Ispezionare

La scheda **Controlla** fornisce una visualizzazione dei metadati del flusso di dati che si sta trasformando. È possibile visualizzare i conteggi delle colonne, le colonne modificate, le colonne aggiunte, i tipi di dati, l'ordinamento delle colonne e i riferimenti alle colonne. **Esaminare** è una visualizzazione di sola lettura dei metadati. Non è necessario che la modalità di debug sia abilitata per visualizzare i metadati nel riquadro di **controllo** .

![Ispezionare](media/data-flow/inspect1.png "Ispezionare")

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
