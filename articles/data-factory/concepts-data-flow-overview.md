---
title: Mapping di flussi di dati in Azure Data Factory | Microsoft Docs
description: Cenni preliminari sul mapping dei flussi di dati in Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/7/2019
ms.openlocfilehash: 37fec388acda78f3d13c8e85ddddf780ad099d69
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388007"
---
# <a name="what-are-mapping-data-flows"></a>Cosa sono i flussi di dati di mapping?

I flussi di dati di mapping sono le trasformazioni di dati progettate visivamente in Azure Data Factory. I flussi di dati consentono agli ingegneri di dati di sviluppare la logica di trasformazione dei dati grafici senza scrivere codice. I flussi di dati risultanti vengono eseguiti come attività all'interno di Azure Data Factory pipeline usando cluster Spark con scalabilità orizzontale. Le attività del flusso di dati possono essere operative tramite le funzionalità di pianificazione, controllo, flusso e monitoraggio Data Factory esistenti.

Il mapping di flussi di dati offre un'esperienza visiva completamente senza necessità di scrivere codice. I flussi di dati vengono eseguiti nel proprio cluster di esecuzione per l'elaborazione dei dati con scalabilità orizzontale. Azure Data Factory gestisce automaticamente tutte le attività di conversione del codice, ottimizzazione dei percorsi ed esecuzione dei processi dei flussi di dati.

## <a name="getting-started"></a>Inizia ora

Per creare un flusso di dati, fare clic sul segno più in risorse Factory. 

![nuovo flusso di dati](media/data-flow/newdataflow2.png "nuovo flusso di dati")

Viene quindi riportata l'area di disegno del flusso di dati in cui è possibile creare la logica di trasformazione. Fare clic sulla casella ' Aggiungi origine ' per avviare la configurazione della trasformazione origine. Per ulteriori informazioni, vedere [trasformazione origine](data-flow-source.md).

## <a name="data-flow-canvas"></a>Area di disegno del flusso di dati

L'area di disegno del flusso di dati è suddivisa in tre parti: la barra superiore, il grafico e il pannello di configurazione. 

![Disegno](media/data-flow/canvas1.png "Canvas")

### <a name="graph"></a>Grafo

Il grafico mostra il flusso di trasformazione. Mostra la derivazione dei dati di origine durante il flusso in uno o più sink. Per aggiungere una nuova origine, fare clic sulla casella "Aggiungi origine". Per aggiungere una nuova trasformazione, fare clic sul segno più nella parte inferiore destra di una trasformazione esistente.

![Disegno](media/data-flow/canvas2.png "Canvas")

### <a name="configuration-panel"></a>Pannello di configurazione

Il pannello configurazione Mostra le impostazioni specifiche della trasformazione attualmente selezionata oppure, se non è stata selezionata alcuna trasformazione, il flusso di dati. Nella configurazione del flusso di dati complessiva, è possibile modificare il nome e la descrizione nella scheda **generale** oppure aggiungere parametri tramite la scheda **parametri** . Per ulteriori informazioni, vedere [mapping dei parametri del flusso di dati](parameters-data-flow.md).

Per ogni trasformazione sono disponibili almeno quattro schede di configurazione:

#### <a name="transformation-settings"></a>Impostazioni di trasformazione

La prima scheda del riquadro di configurazione di ogni trasformazione contiene le impostazioni specifiche della trasformazione. Per ulteriori informazioni, vedere la pagina della documentazione relativa alla trasformazione.

![Scheda Impostazioni di origine](media/data-flow/source1.png "Scheda Impostazioni di origine")

#### <a name="optimize"></a>Ottimizzare

La scheda _ottimizza_ contiene le impostazioni per la configurazione degli schemi di partizionamento.

![Optimize](media/data-flow/optimize1.png "Ottimizzare") (Ottimizza)

L'impostazione predefinita è "usa il partizionamento corrente", che indica Azure Data Factory di usare lo schema di partizionamento nativo per i flussi di dati in esecuzione in Spark. Nella maggior parte degli scenari questa impostazione è l'approccio consigliato.

Sono presenti istanze in cui è possibile modificare il partizionamento. Se ad esempio si desidera restituire le trasformazioni a un singolo file nel lago, scegliere "partizione singola" in una trasformazione sink.

Un altro caso in cui si desidera controllare gli schemi di partizionamento è l'ottimizzazione delle prestazioni. La regolazione del partizionamento consente di controllare la distribuzione dei dati tra i nodi di calcolo e le ottimizzazioni di località dei dati che possono avere effetti positivi e negativi sulle prestazioni complessive del flusso di dati. Per ulteriori informazioni, vedere la [Guida alle prestazioni del flusso di dati](concepts-data-flow-performance.md).

Per modificare il partizionamento in qualsiasi trasformazione, fare clic sulla scheda Ottimizza e selezionare il pulsante di opzione "imposta partizionamento". Verrà visualizzata una serie di opzioni per il partizionamento. Il metodo migliore per il partizionamento varia a seconda dei volumi di dati, delle chiavi candidate, dei valori null e della cardinalità. Una procedura consigliata consiste nell'iniziare con il partizionamento predefinito, quindi provare diverse opzioni di partizionamento. È possibile eseguire il test usando le esecuzioni di debug della pipeline e visualizzare il tempo di esecuzione e l'utilizzo delle partizioni in ogni raggruppamento di trasformazione dalla visualizzazione monitoraggio. Per altre informazioni, vedere [monitoraggio dei flussi di dati](concepts-data-flow-monitoring.md).

Di seguito sono elencate le opzioni di partizionamento disponibili.

##### <a name="round-robin"></a>Round robin 

Questa opzione corrisponde a una semplice partizione che distribuisce automaticamente i dati in modo uniforme tra partizioni. Usare Round Robin se non si hanno candidati chiave validi per implementare una strategia di partizionamento intelligente solida. È possibile impostare il numero di partizioni fisiche.

##### <a name="hash"></a>Hash

Azure Data Factory genera un hash di colonne per produrre partizioni uniformi in modo che le righe con valori simili rientrino nella stessa partizione. Quando si usa questa opzione, testare la soluzione per individuare possibili asimmetrie delle partizioni. È possibile impostare il numero di partizioni fisiche.

##### <a name="dynamic-range"></a>Dynamic Range (Intervallo dinamico)

Questa opzione usa intervalli dinamici di Spark in base alle colonne o alle espressioni fornite. È possibile impostare il numero di partizioni fisiche. 

##### <a name="fixed-range"></a>Fixed Range (Intervallo fisso)

Consente di compilare un'espressione che fornisce un intervallo fisso per i valori nelle colonne di dati partizionati. Prima di utilizzare questa opzione, è necessario conoscere i dati prima di utilizzare questa opzione per evitare la distorsione della partizione. I valori immessi per l'espressione verranno utilizzati come parte di una funzione di partizione. È possibile impostare il numero di partizioni fisiche.

##### <a name="key"></a>Chiave

Se si ha una buona comprensione della cardinalità dei dati, il partizionamento con chiavi può essere una buona strategia di partizionamento. Il partizionamento con chiavi crea partizioni per ogni valore univoco nella colonna. Non è possibile impostare il numero di partizioni perché il numero sarà basato sui valori univoci nei dati.

#### <a name="inspect"></a>Ispezionare

La scheda _Controlla_ fornisce una visualizzazione dei metadati del flusso di dati che si sta trasformando. È possibile visualizzare i conteggi delle colonne, le colonne modificate, le colonne aggiunte, i tipi di dati, l'ordinamento delle colonne e i riferimenti alle colonne. Esaminare è una visualizzazione di sola lettura dei metadati. Non è necessario che la modalità di debug sia abilitata per visualizzare i metadati nel riquadro di controllo.

![Ispezionare](media/data-flow/inspect1.png "Ispezionare")

Quando si modifica la forma dei dati tramite le trasformazioni, si noterà che le modifiche ai metadati passano attraverso il riquadro di controllo. Se non è presente uno schema definito nella trasformazione origine, i metadati non saranno visibili nel riquadro ispezione. L'assenza di metadati è comune negli scenari di deviazione dello schema.

#### <a name="data-preview"></a>Anteprima dati

Se la modalità di debug è attiva, la scheda _Anteprima dati_ fornisce uno snapshot interattivo dei dati a ogni trasformazione. Per altre informazioni, vedere [anteprima dei dati in modalità debug](concepts-data-flow-debug-mode.md#data-preview).

### <a name="top-bar"></a>Barra superiore

La barra superiore contiene azioni che interessano l'intero flusso di dati, ad esempio il salvataggio e la convalida. È anche possibile passare tra le modalità grafico e configurazione usando i pulsanti **Mostra grafico** e **Nascondi grafico** .

![Nascondi grafico](media/data-flow/hideg.png "Nascondi grafico")

Se si nasconde il grafo, è possibile spostarsi tra i nodi di trasformazione in un secondo momento tramite i pulsanti **indietro** e **Avanti** .

![Navigare](media/data-flow/showhide.png "Navigare")

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come creare una [trasformazione di origine](data-flow-source.md)
* Informazioni su come creare flussi di dati in [modalità debug](concepts-data-flow-debug-mode.md)
