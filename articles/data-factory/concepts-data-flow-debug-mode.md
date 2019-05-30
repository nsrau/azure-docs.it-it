---
title: Modalità di debug dei flussi di dati di mapping di Azure Data Factory
description: Avviare una sessione di debug interattivo durante la compilazione di flussi di dati
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: a50778db5fd57202c17f05407045259371912586
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239199"
---
# <a name="mapping-data-flow-debug-mode"></a>Modalità di debug dei flussi di dati di mapping

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Azure Data Factory di Mapping del flusso di dati include una modalità di debug, che può essere attivata con il pulsante nella parte superiore dell'area di progettazione flusso di eseguire il Debug dei dati. Quando si progettano flussi di dati, l'attivazione della modalità di debug permette di osservare in modo interattivo la trasformazione della forma dei dati durante la compilazione e il debug dei flussi di dati. La sessione di Debug può essere usata sia in sessioni sulla progettazione del flusso di dati, nonché durante l'esecuzione del debug della pipeline di flussi di dati.

![Eseguire il debug sul pulsante](media/data-flow/debugbutton.png "sul pulsante di Debug")

## <a name="overview"></a>Panoramica
Quando la modalità di Debug è attivata, verrà compilata in modo interattivo il flusso di dati con un cluster Spark attivo. La sessione verrà chiusa quando si disattiva il debug in Azure Data Factory. È bene tenere conto dei costi orari addebitati da Azure Databricks durante il periodo in cui è attivata la sessione di debug.

Nella maggior parte dei casi, è consigliabile compilare i flussi di dati in modalità di debug in modo da poter convalidare la logica di business e visualizzare le trasformazioni dei dati prima di pubblicare il proprio lavoro in Azure Data Factory. È anche necessario utilizzare il pulsante "Debug" nel Pannello di pipeline per testare il flusso di dati all'interno di una pipeline.

> [!NOTE]
> Mentre la luce in modalità di debug è verde nella barra degli strumenti di Data Factory, è addebitato alla tariffa di debug del flusso di dati di 8 core/ora di calcolo generale con un minuto 60 time-to-live 

## <a name="debug-mode-on"></a>Modalità di debug in
Quando si attiva la modalità di debug, viene visualizzato un pannello laterale che chiede di puntare al cluster di Azure Databricks interattivo e selezionare le opzioni per il campionamento dell'origine. È necessario usare un cluster interattivo di Azure Databricks e selezionare una dimensione di campionamento da ognuna delle trasformazioni origine oppure selezionare un file di testo da usare per i dati di test.

<img src="media/data-flow/upload.png" width="400">

> [!NOTE]
>Quando è attiva la modalità di debug nel flusso di dati, i dati non vengono scritti nella trasformazione sink. Una sessione di debug è progettata per fungere da test harness per le trasformazioni. I sink non sono necessari durante il debug e vengono ignorati nel flusso di dati. Se si vuole testare la scrittura di dati nel sink, eseguire il flusso di dati da una pipeline di Azure Data Factory e usare l'esecuzione di debug dalla pipeline.

## <a name="debug-settings"></a>Impostazioni di debug
Impostazioni di debug può essere ogni origine dal flusso di dati verrà visualizzato nel pannello laterale e possono anche essere modificate selezionando "impostazioni di origine" sulla barra degli strumenti della finestra di progettazione del flusso di dati. Qui è possibile selezionare i limiti e/o l'origine dei file da usare per ogni trasformazione origine. I limiti di riga in questa impostazione sono solo per la sessione di debug corrente. È inoltre possibile utilizzare l'impostazione di campionamento nell'origine per limitare le righe nella trasformazione origine.

## <a name="cluster-status"></a>Stato del cluster
Nella parte superiore dell'area di progettazione è presente un indicatore dello stato del cluster che diventa verde quando il cluster è pronto per il debug. Se il cluster è già pronto, l'indicatore verde viene visualizzato quasi immediatamente. Se il cluster non è già in esecuzione quando viene attivata la modalità di debug, è necessario attendere da 5 a 7 minuti prima che il cluster si avvii. L'indicatore luminoso è giallo finché il cluster non è pronto. Quando il cluster è pronto per il debug del flusso di dati, l'indicatore luminoso diventa verde.

Al termine in fase di debug, disattivare l'opzione di Debug in modo che il cluster Azure Databricks è possibile terminare e non è più avverrà per attività di debug.

<img src="media/data-flow/datapreview.png" width="400">

## <a name="data-preview"></a>Anteprima dati
Quando il debug è attivato, la scheda Anteprima dati sarà attivata nel pannello inferiore. Se la modalità di debug non è attivata, il flusso di dati mostra solo i metadati correnti da e verso ognuna delle trasformazioni nella scheda Ispeziona. L'anteprima dei dati solo una query il numero di righe che è impostato il limite nelle impostazioni di debug. Può essere necessario fare clic su "Recupera dati" per aggiornare l'anteprima dei dati.

<img src="media/data-flow/stats.png" width="400">

## <a name="data-profiles"></a>Profili dati
Quando si selezionano singole colonne nella scheda Anteprima dati, viene visualizzato un grafico popup all'estrema destra della griglia di dati con statistiche dettagliate su ogni campo. Azure Data Factory determina quale tipo di grafico visualizzare in base al campionamento dei dati. Per i campi a cardinalità elevata, per impostazione predefinita vengono usati grafici NULL/NON NULL, mentre per i dati categorici e numerici con cardinalità bassa vengono visualizzati grafici a barre che mostrano la frequenza dei valori di dati. Verranno visualizzati anche le lunghezze minime e massime dei campi stringa, i valori minimi e massimi nei campi numerici, lo sviluppo standard, i percentili, i conteggi e la media. 

<img src="media/data-flow/chart.png" width="400">

## <a name="next-steps"></a>Passaggi successivi

Dopo aver terminato di compilazione e debug del flusso di dati, [eseguirla da una pipeline.](control-flow-execute-data-flow-activity.md)

Quando la pipeline con un flusso di dati di test, usare la pipeline [Debug eseguire l'opzione di esecuzione.](iterative-development-debugging.md)
