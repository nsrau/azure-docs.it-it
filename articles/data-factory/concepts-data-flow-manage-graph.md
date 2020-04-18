---
title: Grafici del flusso di dati
description: Come usare i grafici del flusso di dati della data factoryHow to work with data factory data flow graphs
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/04/2019
ms.openlocfilehash: 0d357c4c671070a5c5e9d4587e2f90b6628996f4
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605363"
---
# <a name="mapping-data-flow-graphs"></a>Mapping dei grafici del flusso di dati

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

L'area di progettazione dei flussi di dati di mapping è un'area di "costruzione" in cui si compilano i flussi di dati dall'alto verso il basso, da sinistra a destra. A ogni trasformazione contrassegnata da un segno più (+) è associata una casella degli strumenti. È possibile concentrarsi sulla logica di business invece di collegare i nodi tramite le estremità in un ambiente DAG in formato libero.

Di seguito sono riportati i meccanismi incorporati per gestire il grafico del flusso di dati.

## <a name="move-nodes"></a>Spostamento di nodi

![Opzioni di trasformazione Aggrega](media/data-flow/agghead.png "intestazione aggregatore")

Senza un paradigma di trascinamento della selezione, il modo per "spostare" un nodo di trasformazione, consiste nel modificare il flusso in ingresso. Con questa funzionalità si possono invece spostare le trasformazioni nell'area di progettazione modificando il "flusso in ingresso".

## <a name="streams-of-data-inside-of-data-flow"></a>Flussi di dati all'interno del flusso di dati

In Flusso di dati di Azure Data Factory lo spostamento delle trasformazioni viene rappresentato tramite flussi. Nel riquadro delle impostazioni di trasformazione verrà visualizzato un campo "Flusso in ingresso". Nel campo è indicato il flusso di dati in ingresso destinato a tale trasformazione. È possibile modificare la posizione fisica del nodo della trasformazione nel grafico facendo clic sul nome del flusso in ingresso e selezionando un altro flusso di dati. La trasformazione corrente su tale flusso, insieme a tutte le trasformazioni successive, verranno così spostate nella nuova posizione.

Se si sposta una trasformazione con una o più trasformazioni successive, la nuova posizione nel flusso di dati verrà unita tramite un nuovo ramo.

Se dopo il nodo selezionato non sono presenti trasformazioni successive, nella nuova posizione verrà spostata solo la trasformazione associata a tale nodo.

## <a name="hide-graph-and-show-graph"></a>Nascondi grafico e mostra grafico

Nell'estrema destra del riquadro di configurazione inferiore è presente un pulsante in cui è possibile espandere il riquadro inferiore a schermo intero quando si lavora sulle configurazioni di trasformazione. Questo vi permetterà di utilizzare i pulsanti "precedente" e "successivo" per navigare attraverso le configurazioni del grafico. Per tornare alla visualizzazione grafica, fare clic sul pulsante verso il basso e tornare alla visualizzazione a schermo diviso.

## <a name="search-graph"></a>Grafico di ricerca

È possibile cercare il grafico con il pulsante di ricerca nell'area di progettazione.

![Ricerca](media/data-flow/search001.png "Grafico di ricerca")

## <a name="next-steps"></a>Passaggi successivi

Dopo aver completato la progettazione del flusso di dati, attivare il pulsante di debug e testarlo in modalità di debug direttamente nella finestra di [progettazione](concepts-data-flow-debug-mode.md) del flusso di dati o nel [debug della pipeline.](control-flow-execute-data-flow-activity.md)
