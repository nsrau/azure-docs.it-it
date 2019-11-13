---
title: Azure Data Factory grafici del flusso di dati
description: Come usare data factory grafici del flusso di dati
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 38f85dea554000daf89689a311c091bc9796a658
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74015899"
---
# <a name="mapping-data-flow-graphs"></a>Mapping dei grafici del flusso di dati

L'area di progettazione flussi di dati di mapping è una superficie di "costruzione" in cui i flussi di dati vengono compilati dall'alto verso il basso e da sinistra verso destra. A ogni trasformazione contrassegnata da un segno più (+) è associata una casella degli strumenti. È possibile concentrarsi sulla logica di business invece di collegare i nodi tramite le estremità in un ambiente DAG in formato libero.

Di seguito sono riportati i meccanismi predefiniti per gestire il grafico del flusso di dati.

## <a name="move-nodes"></a>Spostamento di nodi

![Opzioni di trasformazione aggregazione](media/data-flow/agghead.png "Aggregator (intestazione)")

Senza un paradigma di trascinamento della selezione, il modo per "spostare" un nodo di trasformazione consiste nel modificare il flusso in ingresso. Con questa funzionalità si possono invece spostare le trasformazioni nell'area di progettazione modificando il "flusso in ingresso".

## <a name="streams-of-data-inside-of-data-flow"></a>Flussi di dati all'interno del flusso di dati

In Flusso di dati di Azure Data Factory lo spostamento delle trasformazioni viene rappresentato tramite flussi. Nel riquadro Impostazioni trasformazione verrà visualizzato un campo "flusso in ingresso". Nel campo è indicato il flusso di dati in ingresso destinato a tale trasformazione. È possibile modificare la posizione fisica del nodo della trasformazione nel grafico facendo clic sul nome del flusso in ingresso e selezionando un altro flusso di dati. La trasformazione corrente su tale flusso, insieme a tutte le trasformazioni successive, verranno così spostate nella nuova posizione.

Se si sposta una trasformazione con una o più trasformazioni successive, la nuova posizione nel flusso di dati verrà unita tramite un nuovo ramo.

Se dopo il nodo selezionato non sono presenti trasformazioni successive, nella nuova posizione verrà spostata solo la trasformazione associata a tale nodo.

## <a name="hide-graph-and-show-graph"></a>Nascondi grafico e Mostra grafico

È presente un pulsante all'estrema destra del riquadro di configurazione inferiore, in cui è possibile espandere il riquadro inferiore fino a schermo intero quando si lavora con le configurazioni di trasformazione. Questo consentirà di usare i pulsanti "indietro" e "Avanti" per spostarsi tra le configurazioni del grafo. Per tornare alla visualizzazione grafico, fare clic sul pulsante giù e tornare alla schermata di divisione.

## <a name="search-graph"></a>Grafico di ricerca

È possibile cercare il grafico con il pulsante Cerca nell'area di progettazione.

![Ricerca](media/data-flow/search001.png "Grafico di ricerca")

## <a name="next-steps"></a>Passaggi successivi

Dopo aver completato la progettazione del flusso di dati, attivare il pulsante debug e testarlo in modalità debug direttamente nella [finestra di progettazione del flusso di dati](concepts-data-flow-debug-mode.md) o nel debug della [pipeline](control-flow-execute-data-flow-activity.md).
