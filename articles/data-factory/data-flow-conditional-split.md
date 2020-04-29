---
title: Trasformazione Suddivisione condizionale nel flusso di dati di mapping
description: Suddividere i dati in flussi diversi usando la trasformazione Suddivisione condizionale nel flusso di dati del mapping Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: bd9241e526d7cf42f0697afb8635c085a08c80d8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606491"
---
# <a name="conditional-split-transformation-in-mapping-data-flow"></a>Trasformazione Suddivisione condizionale nel flusso di dati di mapping

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

La trasformazione Suddivisione condizionale instrada le righe di dati a flussi diversi in base alle condizioni di corrispondenza. La trasformazione Suddivisione condizionale è simile a una struttura decisionale CASE in un linguaggio di programmazione. La trasformazione valuta le espressioni e, in base ai risultati, indirizza la riga di dati al flusso specificato.

## <a name="configuration"></a>Configurazione

L'impostazione **Split on** determina se la riga di dati fluisce sul primo flusso corrispondente o su ogni flusso a cui corrisponde.

Utilizzare il generatore di espressioni del flusso di dati per immettere un'espressione per la condizione di divisione. Per aggiungere una nuova condizione, fare clic sull'icona a forma di segno più in una riga esistente. È possibile aggiungere un flusso predefinito anche per le righe che non soddisfano alcuna condizione.

![Suddivisione condizionale](media/data-flow/conditionalsplit1.png "opzioni di Suddivisione condizionale")

## <a name="data-flow-script"></a>Script del flusso di dati

### <a name="syntax"></a>Sintassi

```
<incomingStream>
    split(
        <conditionalExpression1>
        <conditionalExpression2>
        ...
        disjoint: {true | false}
    ) ~> <splitTx>@(stream1, stream2, ..., <defaultStream>)
```

### <a name="example"></a>Esempio

L'esempio seguente è una trasformazione Suddivisione condizionale `SplitByYear` denominata che accetta il flusso `CleanData`in ingresso. Questa trasformazione presenta due condizioni `year < 1960` di divisione `year > 1980`e. `disjoint`è false perché i dati passano alla prima condizione corrispondente. Ogni riga che corrisponde alla prima condizione passa al flusso `moviesBefore1960`di output. Tutte le righe rimanenti corrispondenti alla seconda condizione passano al `moviesAFter1980`flusso di output. Tutte le altre righe passano attraverso il flusso `AllOtherMovies`predefinito.

In Data Factory UX questa trasformazione è simile all'immagine seguente:

![Suddivisione condizionale](media/data-flow/conditionalsplit1.png "opzioni di Suddivisione condizionale")

Lo script del flusso di dati per questa trasformazione si trova nel frammento di codice seguente:

```
CleanData
    split(
        year < 1960,
        year > 1980,
        disjoint: false
    ) ~> SplitByYear@(moviesBefore1960, moviesAfter1980, AllOtherMovies)
```

## <a name="next-steps"></a>Passaggi successivi

Le trasformazioni comuni del flusso di dati utilizzate con Suddivisione condizionale sono la trasformazione [join](data-flow-join.md), la [trasformazione Ricerca](data-flow-lookup.md)e la [trasformazione selezione](data-flow-select.md)
