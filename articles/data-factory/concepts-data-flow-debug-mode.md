---
title: Modalità di debug dei flussi di dati di mapping di Azure Data Factory
description: Avviare una sessione di debug interattivo durante la compilazione di flussi di dati
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: d86725718217caf7fd1d9dd6d5d67362e5de7270
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147378"
---
# <a name="mapping-data-flow-debug-mode"></a>Modalità di debug dei flussi di dati di mapping

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Modalità di debug della Azure Data Factory di Mapping del flusso di dati può essere attivata con il pulsante "Dati flusso di Debug" nella parte superiore dell'area di progettazione. Quando la progettazione di flussi di dati, attivare la modalità di debug modo sarà possibile controllare in modo interattivo i dati di forma trasformazione durante la compilazione e il debug dei flussi di dati. La sessione di Debug può essere usata sia in sessioni sulla progettazione del flusso di dati, nonché durante l'esecuzione del debug della pipeline di flussi di dati.

![Eseguire il debug sul pulsante](media/data-flow/debugbutton.png "sul pulsante di Debug")

## <a name="overview"></a>Panoramica
Quando la modalità di Debug è attivata, verrà compilata in modo interattivo il flusso di dati con un cluster Spark attivo. La sessione verrà chiusa quando si disattiva il debug in Azure Data Factory. È bene tenere conto dei costi orari addebitati da Azure Databricks durante il periodo in cui è attivata la sessione di debug.

Nella maggior parte dei casi, è consigliabile compilare i flussi di dati in modalità debug in modo che è possibile convalidare la logica di business e visualizzare le trasformazioni dei dati prima di pubblicare il proprio lavoro in Azure Data Factory. Usare il pulsante "Debug" nel Pannello di pipeline per testare il flusso di dati all'interno di una pipeline.

> [!NOTE]
> Mentre la luce in modalità di debug è verde nella barra degli strumenti di Data Factory, viene addebitata alla tariffa di debug del flusso di dati di 8 core/ora di calcolo generale con un minuto 60 time-to-live 

> [!NOTE]
>Quando è attiva la modalità di debug nel flusso di dati, i dati non vengono scritti nella trasformazione sink. Una sessione di Debug deve essere utilizzato come un test harness per le trasformazioni. I sink non sono necessari durante il debug e vengono ignorati nel flusso di dati. Se si vuole testare la scrittura dei dati nel Sink, eseguire il flusso di dati da una Pipeline di Data Factory di Azure e usare l'esecuzione di Debug da una pipeline.

## <a name="debug-settings"></a>Impostazioni di debug
Eseguire il debug impostazioni possono essere modificate, fare clic su "Impostazioni per Debug" sulla barra degli strumenti di disegno del flusso di dati. È possibile selezionare i limiti e/o l'origine di file da usare per ognuno dei qui le trasformazioni di origine. I limiti di riga in questa impostazione sono solo per la sessione di debug corrente. È anche possibile selezionare il servizio collegato da usare per un'origine SQL DW. 

![Impostazioni di debug](media/data-flow/debug-settings.png "impostazioni di Debug")

## <a name="cluster-status"></a>Stato del cluster
È un indicatore di stato del cluster nella parte superiore dell'area di progettazione che verrà diventi verde quando il cluster è pronto per il debug. Se il cluster è già pronto, l'indicatore verde viene visualizzato quasi immediatamente. Se il cluster non è stato già in esecuzione quando è stata attivata la modalità di debug, quindi è possibile attendere 5-7 minuti per il cluster creare rapidamente. L'indicatore ruoterà fino al relativo pronto.

Al termine in fase di debug, disattivare l'opzione di Debug in modo che il cluster Azure Databricks è possibile terminare e non sarà più l'addebito per l'attività di debug.

## <a name="data-preview"></a>Anteprima dati
Quando il debug è attivato, la scheda Anteprima dati sarà attivata nel pannello inferiore. Se la modalità di debug non è attivata, il flusso di dati mostra solo i metadati correnti da e verso ognuna delle trasformazioni nella scheda Ispeziona. L'anteprima dei dati solo una query il numero di righe che è impostato il limite nelle impostazioni di debug. Può essere necessario fare clic su "Recupera dati" per aggiornare l'anteprima dei dati.

![Anteprima dati](media/data-flow/datapreview.png "Anteprima dati")

## <a name="data-profiles"></a>Profili dati
Selezionando le singole colonne nella propria scheda di anteprima dei dati verrà visualizzato un grafico all'estrema destra della griglia di dati con statistiche dettagliate su ogni campo. Azure Data Factory determina quale tipo di grafico visualizzare in base al campionamento dei dati. I campi a cardinalità elevata verranno impostata automaticamente grafici /NOT NULL mentre i dati categorici e numerici con cardinalità bassa visualizzerà i grafici a barre che mostra la frequenza di valore di dati. Scoprirai anche/Lungh. max di min/max valori i campi numerici, sviluppo standard, i percentili, i conteggi, Media, i campi stringa. 

![Le statistiche di colonna](media/data-flow/stats.png "statistiche di colonna")

## <a name="next-steps"></a>Passaggi successivi

Dopo aver completato la compilazione e debug del flusso di dati, [eseguirla da una pipeline.](control-flow-execute-data-flow-activity.md)

Quando la pipeline con un flusso di dati di test, usare la pipeline [Debug eseguire l'opzione di esecuzione.](iterative-development-debugging.md)
