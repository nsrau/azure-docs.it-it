---
title: Trasformazione Join del flusso di dati di Azure Data Factory
description: Trasformazione Join del flusso di dati di Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/07/2019
ms.openlocfilehash: 18f713198ef9aa45cb72a6718c0f7b086c019258
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61348557"
---
# <a name="mapping-data-flow-join-transformation"></a>Mapping dei dati del flusso di Join-trasformazione

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Usare Join per combinare dati appartenenti a due tabelle all'interno del flusso di dati. Fare clic sulla trasformazione che diventerà la relazione di sinistra e aggiungere la trasformazione Join dalla casella degli strumenti. All'interno della trasformazione Join, selezionare un altro flusso di dati dal proprio flusso di dati, che diverrà la relazione di destra.

![Trasformazione Join](media/data-flow/join.png "Join")

## <a name="join-types"></a>Tipi di join

Selezione tipo di Join è obbligatorio per la trasformazione di Join.

### <a name="inner-join"></a>Inner join

Inner join passerà solo le righe che soddisfano le condizioni di colonne di entrambe le tabelle.

### <a name="left-outer"></a>Left outer join

Tutte le righe del flusso di dati di destra che non soddisfano le condizioni di join vengono analizzate e le colonne di output dell'altra tabella vengono contrassegnate come NULL in aggiunta a tutte le righe restituite dall'inner join.

### <a name="right-outer"></a>Right outer join

Tutte le righe del flusso di dati di destra che non soddisfano le condizioni di join vengono analizzate e le colonne di output che corrispondono all'altra tabella vengono contrassegnate come NULL insieme a tutte le righe restituite dall'inner join.

### <a name="full-outer"></a>Full outer join

Full Outer produce tutte le colonne e righe da entrambe le parti con i valori NULL per le colonne che sono presentano in altra tabella.

### <a name="cross-join"></a>Cross join

Specificare il prodotto incrociato di due flussi con un'espressione. Ciò consente di creare condizioni di join personalizzate.

## <a name="specify-join-conditions"></a>Specificare le condizioni di join

La condizione del join di sinistra proviene dal flusso di dati connesso alla parte sinistra del join. La condizione del join di destra corrisponde al secondo flusso di dati connesso al join nella parte inferiore, il quale potrebbe essere sia una connessione diretta a un altro flusso sia un riferimento a un altro flusso.

È necessario inserire almeno 1 (1..n) condizioni di join. Possono essere campi a cui si fa riferimento direttamente, selezionati da un menu a discesa o espressioni.

## <a name="join-performance-optimizations"></a>Ottimizzazione delle performance del join

A differenza di Merge join in strumenti come SSIS, un join nel flusso di dati ADF non rappresenta un'operazione di merge obbligatoria. Di conseguenza, le chiavi di join non devono essere filtrate per prime. Verrà eseguita l'operazione di Join in Spark con Databricks in base all'operazione di join ottimale in Spark: Join Broadcast/lato mappa:

![Ottimizzare la trasformazione di join](media/data-flow/joinoptimize.png "Ottimizzazione di Join")

Se il set di dati è compatibile con la memoria nodo del ruolo di lavoro Databricks, è possibile ottimizzare le prestazioni del join. È anche possibile specificare il partizionamento dei dati sull'operazione di join per creare set di dati che possono adattarsi meglio in memoria per ogni ruolo di lavoro.

## <a name="self-join"></a>Self-join

È possibile ottenere le condizioni di self-join nel flusso di dati di Azure Data factory tramite la trasformazione Select per creare un alias del flusso di dati esistente. In primo luogo, creare un "ramo nuovo" da un flusso, quindi aggiungere un'istruzione Select per creare un alias dell'intero flusso originale.

![Self-join](media/data-flow/selfjoin.png "Self-join")

Nel diagramma precedente, la trasformazione Select si trova nella parte superiore. Non si tratta di altro che creare un alias del flusso originale in "OrigSourceBatting". Nella trasformazione Join evidenziata sotto, è possibile vedere che viene usato un flusso alias Select come join di destra, consentendo di fare riferimento alla medesima chiave relativamente agli inner Join di destra e di sinistra.

## <a name="composite-and-custom-keys"></a>Chiavi composte e personalizzate

È possibile creare le chiavi composite e personalizzate in tempo reale all'interno la trasformazione di Join. Aggiungere le righe per le colonne di join aggiuntivo con il segno più (+) accanto a ogni riga della relazione. O calcolare un nuovo valore della chiave del generatore di espressioni per un valore di join in tempo reale.

## <a name="next-steps"></a>Passaggi successivi

Dopo l'aggiunta dei dati, è quindi possibile [creare nuove colonne](data-flow-derived-column.md) e [i dati di sink a un archivio dati di destinazione](data-flow-sink.md).
