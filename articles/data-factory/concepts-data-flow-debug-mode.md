---
title: Modalità di debug dei flussi di dati di mapping di Azure Data Factory
description: Avviare una sessione di debug interattivo durante la compilazione di flussi di dati
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 925e52f9f62860d8cd0951b9e72af09cbb7800fc
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027807"
---
# <a name="mapping-data-flow-debug-mode"></a>Modalità di debug dei flussi di dati di mapping



## <a name="overview"></a>Panoramica

Azure Data Factory modalità di debug del flusso di dati di mapping consente di controllare in modo interattivo la trasformazione della forma dei dati durante la compilazione e il debug dei flussi di dati. La sessione di debug può essere usata sia nelle sessioni di progettazione del flusso di dati che durante l'esecuzione del debug di pipeline di flussi di dati. Per attivare la modalità di debug, utilizzare il pulsante "debug flusso di dati" nella parte superiore dell'area di progettazione.

![](media/data-flow/debugbutton.png "Dispositivo di scorrimento") debug dispositivo di scorrimento

Una volta acceso il dispositivo di scorrimento, verrà richiesto di selezionare la configurazione del runtime di integrazione che si desidera utilizzare. Se si sceglie AutoResolveIntegrationRuntime, viene attivato un cluster con otto core di calcolo generale con una durata di 60 minuti. Per ulteriori informazioni sui runtime di integrazione del flusso di dati, vedere [prestazioni del flusso di dati](concepts-data-flow-performance.md#increasing-compute-size-in-azure-integration-runtime).

![Debug]selezione IR(media/data-flow/debugbutton2.png "debug") selezione Runtime

Quando la modalità di debug è attivata, il flusso di dati verrà compilato in modo interattivo con un cluster Spark attivo. La sessione verrà chiusa quando si disattiva il debug in Azure Data Factory. È bene tenere conto dei costi orari addebitati da Azure Databricks durante il periodo in cui è attivata la sessione di debug.

Nella maggior parte dei casi, è consigliabile compilare i flussi di dati in modalità di debug, in modo da poter convalidare la logica di business e visualizzare le trasformazioni dei dati prima di pubblicare il lavoro in Azure Data Factory. Usare il pulsante "debug" nel pannello della pipeline per testare il flusso di dati in una pipeline.

## <a name="cluster-status"></a>Stato del cluster

L'indicatore di stato del cluster nella parte superiore dell'area di progettazione viene trasformato in verde quando il cluster è pronto per il debug. Se il cluster è già pronto, l'indicatore verde viene visualizzato quasi immediatamente. Se il cluster non era già in esecuzione quando è stata attivata la modalità di debug, sarà necessario attendere 5-7 minuti prima che il cluster si avvii. L'indicatore verrà ruotato fino a quando non è pronto.

Al termine del debug, disattivare l'opzione debug in modo che il cluster di Azure Databricks possa terminare e non verrà più addebitato l'attività di debug.

## <a name="debug-settings"></a>Impostazioni di debug

È possibile modificare le impostazioni di debug facendo clic su "impostazioni di debug" sulla barra degli strumenti dell'area di disegno flusso di dati. È possibile selezionare il limite di righe o l'origine file da usare per ognuna delle trasformazioni di origine. I limiti delle righe in questa impostazione sono solo per la sessione di debug corrente. È anche possibile selezionare il servizio collegato di gestione temporanea da usare per un'origine SQL DW. 

Impostazioni ![debug impostazioni]di(media/data-flow/debug-settings.png "debug")

Se sono presenti parametri nel flusso di dati o in uno dei set di dati a cui viene fatto riferimento, è possibile specificare i valori da usare durante il debug selezionando la scheda **parametri** .

Parametri ![delle impostazioni di]debug parametri di impostazioni di(media/data-flow/debug-settings2.png "debug")

## <a name="data-preview"></a>Anteprima dati

Quando il debug è attivato, la scheda Anteprima dati sarà attivata nel pannello inferiore. Se la modalità di debug non è attivata, il flusso di dati mostra solo i metadati correnti da e verso ognuna delle trasformazioni nella scheda Ispeziona. Nell'anteprima dei dati viene eseguita una query solo sul numero di righe impostate come limite nelle impostazioni di debug. Fare clic su **Aggiorna** per recuperare l'anteprima dei dati.

![](media/data-flow/datapreview.png "Anteprima") dati Anteprima dati

> [!NOTE]
> Le origini file limitano solo le righe visualizzate, non le righe lette. Per i set di impostazioni di grandi dimensioni, è consigliabile eseguire una piccola parte del file e usarlo per i test. È possibile selezionare un file temporaneo nelle impostazioni di debug per ogni origine che corrisponde a un tipo di set di dati di file.

Quando è attiva la modalità di debug nel flusso di dati, i dati non vengono scritti nella trasformazione sink. Una sessione di debug è destinata a fungere da test harness per le trasformazioni. I sink non sono necessari durante il debug e vengono ignorati nel flusso di dati. Se si desidera testare la scrittura dei dati nel sink, eseguire il flusso di dati da una pipeline Azure Data Factory e utilizzare l'esecuzione del debug da una pipeline.

### <a name="testing-join-conditions"></a>Test delle condizioni di join

Quando si esegue il testing unità join, esiste o trasforma ricerca, assicurarsi di usare un piccolo set di dati noti per il test. È possibile usare l'opzione debug Settings precedente per impostare un file temporaneo da usare per il test. Questa operazione è necessaria perché quando si limitano o si campionano righe da un set di dati di grandi dimensioni, non è possibile prevedere quali righe e quali chiavi verranno lette nel flusso per il test. Il risultato è non deterministico, vale a dire che le condizioni di join potrebbero non riuscire.

### <a name="quick-actions"></a>Azioni rapide

Una volta visualizzata l'anteprima dei dati, è possibile generare una trasformazione rapida per typecast, rimuovere o apportare modifiche a una colonna. Fare clic sull'intestazione di colonna e quindi selezionare una delle opzioni dalla barra degli strumenti Anteprima dati.

Azioni(media/data-flow/quick-actions1.png "rapide") per ![azioni rapide]

Dopo aver selezionato una modifica, l'anteprima dei dati viene aggiornata immediatamente. Fare clic su **conferma** nell'angolo superiore destro per generare una nuova trasformazione.

Azioni(media/data-flow/quick-actions2.png "rapide") per ![azioni rapide]

**Typecast** e **Modify** genereranno una trasformazione colonna derivata e **Remove** genererà una trasformazione Select.

Azioni(media/data-flow/quick-actions3.png "rapide") per ![azioni rapide]

> [!NOTE]
> Se si modifica il flusso di dati, è necessario recuperare nuovamente l'anteprima dei dati prima di aggiungere una trasformazione rapida.

### <a name="data-profiling"></a>Profiling dati

Se si seleziona una colonna nella scheda Anteprima dati e si fa clic su **statistiche** nella barra degli strumenti di anteprima dati, viene visualizzato un grafico all'estrema destra della griglia dei dati con statistiche dettagliate su ogni campo. Azure Data Factory determina quale tipo di grafico visualizzare in base al campionamento dei dati. I campi con cardinalità elevata verranno predefiniti per i grafici null/NOT NULL, mentre i dati categorici e numerici con cardinalità bassa visualizzeranno grafici a barre che mostrano la frequenza del valore dei dati. Verrà inoltre visualizzata la lunghezza massima/Len dei campi stringa, i valori min/max in campi numerici, lo sviluppo standard, i percentile, i conteggi e la media.

![](media/data-flow/stats.png "Statistiche colonna") statistiche colonne

## <a name="next-steps"></a>Passaggi successivi

* Al termine della compilazione e del debug del flusso di dati, [eseguirlo da una pipeline.](control-flow-execute-data-flow-activity.md)
* Quando si esegue il test della pipeline con un flusso di dati, usare l' [opzione Esegui esecuzione del debug](iterative-development-debugging.md) della pipeline.
