---
title: Trasformazione Join del flusso di dati di Azure Data Factory
description: Trasformazione Join del flusso di dati di Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/07/2019
ms.openlocfilehash: 48cf9d58c8acd85e545a5bcb5104d7069670e349
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029329"
---
# <a name="mapping-data-flow-join-transformation"></a>Mapping trasformazione join flusso di dati



Usare Join per combinare dati appartenenti a due tabelle all'interno del flusso di dati. Fare clic sulla trasformazione che diventerà la relazione di sinistra e aggiungere la trasformazione Join dalla casella degli strumenti. All'interno della trasformazione Join, selezionare un altro flusso di dati dal proprio flusso di dati, che diverrà la relazione di destra.

![Trasformazione Join](media/data-flow/join.png "Join")

## <a name="join-types"></a>Tipi di join

Per la trasformazione join è necessario selezionare tipo di join.

### <a name="inner-join"></a>Inner join

Inner join passerà attraverso solo le righe che corrispondono alle condizioni della colonna da entrambe le tabelle.

### <a name="left-outer"></a>Left outer join

Tutte le righe del flusso di dati di destra che non soddisfano le condizioni di join vengono analizzate e le colonne di output dell'altra tabella vengono contrassegnate come NULL in aggiunta a tutte le righe restituite dall'inner join.

### <a name="right-outer"></a>Right outer join

Tutte le righe del flusso di dati di destra che non soddisfano le condizioni di join vengono analizzate e le colonne di output che corrispondono all'altra tabella vengono contrassegnate come NULL insieme a tutte le righe restituite dall'inner join.

### <a name="full-outer"></a>Full outer join

Full outer produce tutte le colonne e le righe di entrambi i lati con valori NULL per le colonne non presenti nell'altra tabella.

### <a name="cross-join"></a>Cross join

Specificare il prodotto incrociato dei due flussi con un'espressione. Questa operazione può essere utilizzata per creare condizioni di join personalizzate.

## <a name="specify-join-conditions"></a>Specificare le condizioni di join

La condizione del join di sinistra proviene dal flusso di dati connesso alla parte sinistra del join. La condizione del join di destra corrisponde al secondo flusso di dati connesso al join nella parte inferiore, il quale potrebbe essere sia una connessione diretta a un altro flusso sia un riferimento a un altro flusso.

È necessario inserire almeno 1 (1..n) condizioni di join. Possono essere campi a cui si fa riferimento direttamente, selezionati da un menu a discesa o espressioni.

## <a name="join-performance-optimizations"></a>Ottimizzazione delle performance del join

A differenza di Merge join in strumenti come SSIS, un join nel flusso di dati ADF non rappresenta un'operazione di merge obbligatoria. Di conseguenza, le chiavi di join non devono essere filtrate per prime. L'operazione di join viene eseguita in base all'operazione di join ottimale in Spark: Join Broadcast/lato mappa:

![Ottimizzare la trasformazione di join](media/data-flow/joinoptimize.png "Ottimizzazione di Join")

Se il set di dati può adattarsi alla memoria del nodo di lavoro, è possibile ottimizzare le prestazioni del join. È anche possibile specificare il partizionamento dei dati sull'operazione di join per creare set di dati che possono adattarsi meglio in memoria per ogni ruolo di lavoro.

## <a name="self-join"></a>Self-join

È possibile ottenere le condizioni di self-join nel flusso di dati di Azure Data factory tramite la trasformazione Select per creare un alias del flusso di dati esistente. In primo luogo, creare un "ramo nuovo" da un flusso, quindi aggiungere un'istruzione Select per creare un alias dell'intero flusso originale.

![Self-join](media/data-flow/selfjoin.png "Self-join")

Nel diagramma precedente, la trasformazione Select si trova nella parte superiore. Non si tratta di altro che creare un alias del flusso originale in "OrigSourceBatting". Nella trasformazione Join evidenziata sotto, è possibile vedere che viene usato un flusso alias Select come join di destra, consentendo di fare riferimento alla medesima chiave relativamente agli inner Join di destra e di sinistra.

## <a name="composite-and-custom-keys"></a>Chiavi composite e personalizzate

È possibile creare chiavi personalizzate e composte in tempo reale all'interno della trasformazione join. Aggiungere righe per colonne di join aggiuntive con il segno più (+) accanto a ogni riga di relazione. In alternativa, calcolare un nuovo valore di chiave nel generatore di espressioni per un valore di join immediato.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver unito i dati, è possibile [creare nuove colonne](data-flow-derived-column.md) e [affondare i dati in un archivio dati di destinazione](data-flow-sink.md).
