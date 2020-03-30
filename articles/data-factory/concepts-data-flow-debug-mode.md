---
title: Mapping data flow Debug Mode
description: Avviare una sessione di debug interattivo durante la compilazione di flussi di dati
ms.author: makromer
author: kromerm
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/06/2019
ms.openlocfilehash: 18848695327a374f12fbe5a34d03366b050d8b65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74928354"
---
# <a name="mapping-data-flow-debug-mode"></a>Mapping data flow Debug Mode

## <a name="overview"></a>Panoramica

La modalità di debug del flusso di dati di mapping di Azure Data Factory consente di osservare in modo interattivo la trasformazione della forma dei dati durante la compilazione e il debug dei flussi di dati. La sessione di debug può essere utilizzata sia nelle sessioni di progettazione del flusso di dati che durante l'esecuzione del debug della pipeline dei flussi di dati. Per attivare la modalità di debug, utilizzare il pulsante "Debug flusso di dati" nella parte superiore dell'area di progettazione.

![Dispositivo di scorrimento Debug](media/data-flow/debugbutton.png "Dispositivo di scorrimento Debug")

Una volta attivato il dispositivo di scorrimento, verrà richiesto di selezionare la configurazione di runtime di integrazione che si desidera utilizzare. Se si prescelto AutoResolveIntegrationRuntime, verrà selezionato un cluster con otto core di calcolo generale con un tempo di 60 minuti di live. Per altre informazioni sui runtime di integrazione del flusso di dati, vedere Prestazioni del [flusso](concepts-data-flow-performance.md#increasing-compute-size-in-azure-integration-runtime)di dati.

![Eseguire il debug della selezione dei prodotti di ruandea](media/data-flow/debugbutton2.png "Eseguire il debug della selezione dei prodotti di ruandea")

Quando la modalità di debug è attiva, è possibile creare in modo interattivo il flusso di dati con un cluster Spark attivo. La sessione verrà chiusa quando si disattiva il debug in Azure Data Factory. È bene tenere conto dei costi orari addebitati da Azure Databricks durante il periodo in cui è attivata la sessione di debug.

Nella maggior parte dei casi, è consigliabile compilare i flussi di dati in modalità di debug in modo da poter convalidare la logica di business e visualizzare le trasformazioni dei dati prima di pubblicare il lavoro in Azure Data Factory.In most cases, it's good practice to build your Data Flows in debug mode so that you can validate your business logic and view your data transformations before publishing your work in Azure Data Factory. Usare il pulsante "Debug" nel pannello della pipeline per testare il flusso di dati in una pipeline.

## <a name="cluster-status"></a>Stato del cluster

L'indicatore di stato del cluster nella parte superiore dell'area di progettazione diventa verde quando il cluster è pronto per il debug. Se il cluster è già pronto, l'indicatore verde viene visualizzato quasi immediatamente. Se il cluster non era già in esecuzione quando è stata attiva la modalità di debug, sarà necessario attendere 5-7 minuti per il cluster per eseguire il giro. L'indicatore girerà fino a quando non è pronto.

Al termine del debug, disattivare l'opzione Debug in modo che il cluster Azure Databricks possa terminare e non verrà più addebitata l'attività di debug.

## <a name="debug-settings"></a>Impostazioni di debug

Le impostazioni di debug possono essere modificate facendo clic su "Impostazioni di debug" sulla barra degli strumenti dell'area di disegno Flusso di dati. È possibile selezionare qui il limite di riga o l'origine file da utilizzare per ognuna trasformazione Origine. I limiti di riga in questa impostazione sono solo per la sessione di debug corrente. È inoltre possibile selezionare il servizio collegato alla gestione temporanea da utilizzare per un'origine SQL DW. 

![Impostazioni di debug](media/data-flow/debug-settings.png "Impostazioni di debug")

Se nel flusso di dati sono presenti parametri o in uno dei relativi set di dati di riferimento, è possibile specificare i valori da utilizzare durante il debug selezionando la scheda **Parametri.**

![Parametri delle impostazioni di debug](media/data-flow/debug-settings2.png "Parametri delle impostazioni di debug")

## <a name="data-preview"></a>Anteprima dati

Quando il debug è attivato, la scheda Anteprima dati sarà attivata nel pannello inferiore. Senza la modalità di debug attivata, Flusso di dati mostrerà solo i metadati correnti in ingresso e in uscita da ognuna delle trasformazioni nella scheda Controlla. L'anteprima dei dati eseguirà query solo sul numero di righe impostate come limite nelle impostazioni di debug. Fare clic su **Aggiorna** per recuperare l'anteprima dei dati.

![Anteprima dati](media/data-flow/datapreview.png "Anteprima dati")

> [!NOTE]
> Le origini file limitano solo le righe visualizzate, non le righe da leggere. Per set di dati di grandi dimensioni, è consigliabile prendere una piccola parte di tale file e utilizzarlo per il test. È possibile selezionare un file temporaneo in Impostazioni di debug per ogni origine che è un tipo di dataset di file.

Quando è attiva la modalità di debug nel flusso di dati, i dati non vengono scritti nella trasformazione sink. Una sessione di debug deve essere utilizzata come test harness per le trasformazioni. I sink non sono necessari durante il debug e vengono ignorati nel flusso di dati. Se si vuole testare la scrittura dei dati nel sink, eseguire il flusso di dati da una pipeline di Azure Data Factory e usare l'esecuzione di debug da una pipeline.

### <a name="testing-join-conditions"></a>Test delle condizioni di join

Quando si unisce agli unit test, esiste o ricerca trasformazioni, assicurarsi di utilizzare un piccolo set di dati noti per il test. È possibile utilizzare l'opzione Impostazioni di debug sopra riportata per impostare un file temporaneo da utilizzare per il test. Ciò è necessario perché quando si limitano o si esegue il campionamento di righe da un set di dati di grandi dimensioni, non è possibile prevedere quali righe e quali chiavi verranno lette nel flusso per il test. Il risultato è non deterministico, il che significa che le condizioni di join potrebbero non riuscire.

### <a name="quick-actions"></a>Azioni rapide

Una volta visualizzata l'anteprima dei dati, è possibile generare una trasformazione rapida per il cast, rimuovere o apportare una modifica a una colonna. Fare clic sull'intestazione di colonna e quindi selezionare una delle opzioni dalla barra degli strumenti di anteprima dei dati.

![Azioni rapide](media/data-flow/quick-actions1.png "Azioni rapide")

Dopo aver selezionato una modifica, l'anteprima dei dati verrà aggiornata immediatamente. Fare clic su **Conferma** nell'angolo in alto a destra per generare una nuova trasformazione.

![Azioni rapide](media/data-flow/quick-actions2.png "Azioni rapide")

Typecast e Modify genereranno una trasformazione Colonna derivata e Remove genereranno una trasformazione Select.Typecast and **Modify** will generate a Derived Column transformation and **Remove** will generate a Select transformation. **Typecast**

![Azioni rapide](media/data-flow/quick-actions3.png "Azioni rapide")

> [!NOTE]
> Se si modifica il flusso di dati, è necessario recuperare nuovamente l'anteprima dei dati prima di aggiungere una trasformazione rapida.

### <a name="data-profiling"></a>Profilazione dei dati

Selezionando una colonna nella scheda Di anteprima dei dati e facendo clic su **Statistiche** nella barra degli strumenti di anteprima dei dati, verrà visualizzato un grafico all'estrema destra della griglia dei dati con statistiche dettagliate su ogni campo. Azure Data Factory determina quale tipo di grafico visualizzare in base al campionamento dei dati. Per i campi ad alta cardinalità verranno i grafici NULL/NOT NULL, mentre i dati categorici e numerici con cardinalità bassa visualizzeranno grafici a barre che mostrano la frequenza dei valori dei dati. Vedrai anche la lunghezza massima/len dei campi stringa, i valori min/max nei campi numerici, gli sviluppatori standard, i percentili, i conteggi e la media.

![Statistiche della colonna](media/data-flow/stats.png "Statistiche della colonna")

## <a name="next-steps"></a>Passaggi successivi

* Al termine della compilazione e del debug del flusso di dati, [eseguirlo da una pipeline.](control-flow-execute-data-flow-activity.md)
* Quando si testa la pipeline con un flusso di dati, usare l'opzione di esecuzione esecuzione del debug della [pipeline.](iterative-development-debugging.md)
