---
title: Spostare i nodi in Flusso di dati di Azure Data Factory
description: Come spostare nodi in un diagramma del flusso di dati di mapping Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 631a103491b70ca016b94af01995aeeb3f29c77a
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754679"
---
# <a name="mapping-data-flow-move-nodes"></a>Mapping dei nodi di spostamento del flusso di dati



![Opzioni di trasformazione aggregazione](media/data-flow/agghead.png "Aggregator (intestazione)")

L'area di progettazione di Flusso di dati di Azure Data Factory offre una superficie in cui creare flussi di dati dall'alto verso il basso e da sinistra verso destra. A ogni trasformazione contrassegnata da un segno più (+) è associata una casella degli strumenti. È possibile concentrarsi sulla logica di business invece di collegare i nodi tramite le estremità in un ambiente DAG in formato libero.

Senza un paradigma di trascinamento della selezione, per spostare il nodo di una trasformazione è necessario modificare il codice relativo al flusso in ingresso. Con questa funzionalità si possono invece spostare le trasformazioni nell'area di progettazione modificando il "flusso in ingresso".

## <a name="streams-of-data-inside-of-data-flow"></a>Flussi di dati all'interno del flusso di dati

In Flusso di dati di Azure Data Factory lo spostamento delle trasformazioni viene rappresentato tramite flussi. Nel riquadro Impostazioni trasformazione verrà visualizzato un campo "flusso in ingresso". Nel campo è indicato il flusso di dati in ingresso destinato a tale trasformazione. È possibile modificare la posizione fisica del nodo della trasformazione nel grafico facendo clic sul nome del flusso in ingresso e selezionando un altro flusso di dati. La trasformazione corrente su tale flusso, insieme a tutte le trasformazioni successive, verranno così spostate nella nuova posizione.

Se si sposta una trasformazione con una o più trasformazioni successive, la nuova posizione nel flusso di dati verrà unita tramite un nuovo ramo.

Se dopo il nodo selezionato non sono presenti trasformazioni successive, nella nuova posizione verrà spostata solo la trasformazione associata a tale nodo.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver completato la progettazione del flusso di dati, attivare il pulsante debug e testarlo in modalità debug direttamente nella [finestra di progettazione del flusso di dati](concepts-data-flow-debug-mode.md) o nel debug della [pipeline](control-flow-execute-data-flow-activity.md).
