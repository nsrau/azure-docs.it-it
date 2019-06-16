---
title: Trasformazione Finestra per il flusso di dati di mapping di Azure Data Factory
description: Trasformazione Finestra per il flusso di dati di mapping di Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 6f3f06ff54fc76416ba63f4f09835897d546f8dc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61349971"
---
# <a name="azure-data-factory-window-transformation"></a>Trasformazione di finestra di Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

La trasformazione Finestra è la posizione in cui verranno definite le aggregazioni basate su finestra delle colonne nei flussi di dati. Nel generatore di espressioni è possibile definire diversi tipi di aggregazioni basate su finestre di dati o temporali (clausola OVER SQL), ad esempio LEAD, LAG, NTILE, CUMEDIST, RANK e così via. Verrà generato un nuovo campo nell'output che include queste aggregazioni. È anche possibile includere campi di raggruppamento facoltativi.

![Opzioni della finestra](media/data-flow/windows1.png "Finestra 1")

## <a name="over"></a>Over (Selezione)
Impostare il partizionamento dei dati di colonna per la trasformazione Finestra. L'equivalente SQL è ```Partition By``` nella clausola Over in SQL. Se si vuole creare un calcolo o un'espressione da usare per il partizionamento, è possibile passare il mouse sul nome della colonna e selezionare "computed column" (colonna calcolata).

![Opzioni della finestra](media/data-flow/windows4.png "Finestra 4")

## <a name="sort"></a>Ordina
Un'altra parte della clausola Over è l'impostazione di ```Order By```. Questa parte imposterà l'ordinamento dei dati. È anche possibile creare un'espressione per calcolare un valore in questo campo della colonna per l'ordinamento.

![Opzioni della finestra](media/data-flow/windows5.png "Finestra 5")

## <a name="range-by"></a>Range By (Criteri intervallo)
Impostare poi l'intervallo della finestra come Unbounded (Non vincolato) o Bounded (Vincolato). Per impostare un intervallo della finestra non vincolato, impostare il dispositivo di scorrimento su Unbounded (Non vincolato) su entrambe le estremità. Se si sceglie un'impostazione compresa tra Unbounded (Non vincolato) e Current Row (Riga corrente), è necessario impostare i valori di inizio e fine per Offset. Entrambi i valori saranno numeri interi positivi. È possibile usare numeri relativi o valori dai dati.

Nel dispositivo di scorrimento della finestra è necessario impostare due valori: i valori prima della riga corrente e i valori dopo la riga corrente. L'offset di inizio e di fine corrisponde ai due selettori nel dispositivo di scorrimento.

![Opzioni della finestra](media/data-flow/windows6.png "Finestra 6")

## <a name="window-columns"></a>Colonne della finestra
Infine, usare il generatore di espressioni per definire le aggregazioni da usare con le finestre di dati, come RANK, COUNT, MIN, MAX, DENSE RANK, LEAD, LAG e così via.

![Opzioni della finestra](media/data-flow/windows7.png "Finestra 7")

L'elenco completo delle funzioni di aggregazione e analisi disponibili per l'uso nel linguaggio per le espressioni per i flussi di dati di Azure Data Factory tramite il generatore di espressioni è disponibile qui: https://aka.ms/dataflowexpressions.

## <a name="next-steps"></a>Passaggi successivi

Se si sta cercando un'aggregazione di group by semplice, usare il [trasformazione aggregazione](data-flow-aggregate.md)
