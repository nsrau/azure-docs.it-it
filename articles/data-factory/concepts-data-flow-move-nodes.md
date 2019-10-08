---
title: Spostare i nodi in Flusso di dati di Azure Data Factory
description: Come spostare nodi in un diagramma del flusso di dati di mapping Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 853fff955a953052b94ad4e7e1dd55371069c30b
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030198"
---
# <a name="mapping-data-flow-move-nodes"></a>Nodi di spostamento del flusso di dati di mapping



![Opzioni della trasformazione Aggregazione](media/data-flow/agghead.png "intestazione aggregatore")

L'area di progettazione di Flusso di dati di Azure Data Factory offre una superficie in cui creare flussi di dati dall'alto verso il basso e da sinistra verso destra. A ogni trasformazione contrassegnata da un segno più (+) è associata una casella degli strumenti. È possibile concentrarsi sulla logica di business invece di collegare i nodi tramite le estremità in un ambiente DAG in formato libero.

Senza un paradigma di trascinamento della selezione, per spostare il nodo di una trasformazione è necessario modificare il codice relativo al flusso in ingresso. Con questa funzionalità si possono invece spostare le trasformazioni nell'area di progettazione modificando il "flusso in ingresso".

## <a name="streams-of-data-inside-of-data-flow"></a>Flussi di dati all'interno del flusso di dati

In Flusso di dati di Azure Data Factory lo spostamento delle trasformazioni viene rappresentato tramite flussi. Nel riquadro delle impostazioni della trasformazione si noterà un campo "Incoming stream" (Flusso in ingresso). Nel campo è indicato il flusso di dati in ingresso destinato a tale trasformazione. È possibile modificare la posizione fisica del nodo della trasformazione nel grafico facendo clic sul nome del flusso in ingresso e selezionando un altro flusso di dati. La trasformazione corrente su tale flusso, insieme a tutte le trasformazioni successive, verranno così spostate nella nuova posizione.

Se si sposta una trasformazione con una o più trasformazioni successive, la nuova posizione nel flusso di dati verrà unita tramite un nuovo ramo.

Se dopo il nodo selezionato non sono presenti trasformazioni successive, nella nuova posizione verrà spostata solo la trasformazione associata a tale nodo.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver completato la progettazione del flusso di dati, attivare il pulsante debug e testarlo in modalità debug direttamente nella [finestra di progettazione del flusso di dati](concepts-data-flow-debug-mode.md) o nel debug della [pipeline](control-flow-execute-data-flow-activity.md).
