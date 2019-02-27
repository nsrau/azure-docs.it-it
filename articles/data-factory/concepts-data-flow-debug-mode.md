---
title: Modalità di debug dei flussi di dati di mapping di Azure Data Factory
description: Avviare una sessione di debug interattivo durante la compilazione di flussi di dati
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 567f64b8b720588807caeb5e49bae15f14c5b0a7
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/16/2019
ms.locfileid: "56329797"
---
# <a name="mapping-data-flow-debug-mode"></a>Modalità di debug dei flussi di dati di mapping

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Per un flusso di dati di mapping di Azure Data Factory è disponibile una modalità di debug che può essere attivata con il pulsante Debug nella parte superiore dell'area di progettazione. Quando si progettano flussi di dati, l'attivazione della modalità di debug permette di osservare in modo interattivo la trasformazione della forma dei dati durante la compilazione e il debug dei flussi di dati.

<img src="media/data-flow/debugbutton.png" width="400">

## <a name="overview"></a>Panoramica
Quando la modalità di debug è attivata, è possibile compilare in modo interattivo il flusso di dati con un cluster interattivo di Azure Databricks in esecuzione. La sessione verrà chiusa quando si disattiva il debug in Azure Data Factory. È bene tenere conto dei costi orari addebitati da Azure Databricks durante il periodo in cui è attivata la sessione di debug.

Nella maggior parte dei casi, è consigliabile compilare i flussi di dati in modalità di debug in modo da poter convalidare la logica di business e visualizzare le trasformazioni dei dati prima di pubblicare il proprio lavoro in Azure Data Factory.

## <a name="debug-mode-on"></a>Attivazione della modalità di debug
Quando si attiva la modalità di debug, viene visualizzato un pannello laterale che chiede di puntare al cluster di Azure Databricks interattivo e selezionare le opzioni per il campionamento dell'origine. È necessario usare un cluster interattivo di Azure Databricks e selezionare una dimensione di campionamento da ognuna delle trasformazioni origine oppure selezionare un file di testo da usare per i dati di test.

<img src="media/data-flow/upload.png" width="400">

> [!NOTE]
>Quando è attiva la modalità di debug nel flusso di dati, i dati non vengono scritti nella trasformazione sink. Una sessione di debug è progettata per fungere da test harness per le trasformazioni. I sink non sono necessari durante il debug e vengono ignorati nel flusso di dati. Se si vuole testare la scrittura di dati nel sink, eseguire il flusso di dati da una pipeline di Azure Data Factory e usare l'esecuzione di debug dalla pipeline.

## <a name="debug-settings"></a>Impostazioni di debug
In base alle impostazioni di debug ogni origine del flusso di dati viene visualizzata nel pannello laterale e può anche essere modificata selezionando "Impostazioni origine" sulla barra degli strumenti di progettazione del flusso di dati. Qui è possibile selezionare i limiti e/o l'origine dei file da usare per ogni trasformazione origine. È anche possibile selezionare il cluster di Databricks che si vuole usare per il debug.

## <a name="cluster-status"></a>Stato del cluster
Nella parte superiore dell'area di progettazione è presente un indicatore dello stato del cluster che diventa verde quando il cluster è pronto per il debug. Se il cluster è già pronto, l'indicatore verde viene visualizzato quasi immediatamente. Se il cluster non è già in esecuzione quando viene attivata la modalità di debug, è necessario attendere da 5 a 7 minuti prima che il cluster si avvii. L'indicatore luminoso è giallo finché il cluster non è pronto. Quando il cluster è pronto per il debug del flusso di dati, l'indicatore luminoso diventa verde.

Al termine del debug, disattivare l'interruttore Debug in modo da terminare il cluster di Azure Databricks.

<img src="media/data-flow/datapreview.png" width="400">

## <a name="data-preview"></a>Anteprima dati
Quando il debug è attivato, la scheda Anteprima dati sarà attivata nel pannello inferiore. Se la modalità di debug non è attivata, il flusso di dati mostra solo i metadati correnti da e verso ognuna delle trasformazioni nella scheda Ispeziona. L'anteprima dei dati esegue query solo per il numero di righe impostato come limite nelle impostazioni dell'origine. Può essere necessario fare clic su "Recupera dati" per aggiornare l'anteprima dei dati.

<img src="media/data-flow/stats.png" width="400">

## <a name="data-profiles"></a>Profili dei dati
Quando si selezionano singole colonne nella scheda Anteprima dati, viene visualizzato un grafico popup all'estrema destra della griglia di dati con statistiche dettagliate su ogni campo. Azure Data Factory determina quale tipo di grafico visualizzare in base al campionamento dei dati. Per i campi a cardinalità elevata, per impostazione predefinita vengono usati grafici NULL/NON NULL, mentre per i dati categorici e numerici con cardinalità bassa vengono visualizzati grafici a barre che mostrano la frequenza dei valori di dati. Verranno visualizzati anche le lunghezze minime e massime dei campi stringa, i valori minimi e massimi nei campi numerici, lo sviluppo standard, i percentili, i conteggi e la media. 

<img src="media/data-flow/chart.png" width="400">
