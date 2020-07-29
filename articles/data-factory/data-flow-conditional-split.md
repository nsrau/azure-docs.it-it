---
title: Trasformazione Suddivisione condizionale nel flusso di dati per mapping
description: Suddividere i dati in flussi diversi usando la trasformazione Suddivisione condizionale nel flusso di dati per mapping di Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/21/2020
ms.openlocfilehash: eece6f97e82f3800d4f59ac1849b34c2a1e4635b
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83800080"
---
# <a name="conditional-split-transformation-in-mapping-data-flow"></a>Trasformazione Suddivisione condizionale nel flusso di dati per mapping

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

La trasformazione Suddivisione condizionale instrada le righe di dati a flussi diversi in base alle condizioni di corrispondenza. La trasformazione Suddivisione condizionale è simile a una struttura decisionale CASE in un linguaggio di programmazione. La trasformazione valuta le espressioni e, in base ai risultati, indirizza la riga di dati al flusso specificato.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4wKCX]

## <a name="configuration"></a>Configurazione

L'impostazione **SplitOn** determina se la riga di dati fluisce sul primo flusso corrispondente o su ogni flusso a cui corrisponde.

Utilizzare il generatore di espressioni del flusso di dati per immettere un'espressione per la condizione di divisione. Per aggiungere una nuova condizione, fare clic sull'icona con il segno più in una riga esistente. È possibile aggiungere un flusso predefinito anche per le righe che non soddisfano alcuna condizione.

![suddivisione condizionale](media/data-flow/conditionalsplit1.png "opzioni di suddivisione condizionale")

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

Nell'esempio seguente viene illustrata una trasformazione Suddivisione condizionale denominata `SplitByYear` che accetta il flusso in ingresso `CleanData`. Questa trasformazione presenta due condizioni di divisione `year < 1960` e `year > 1980`. `disjoint` è false perché i dati passano alla prima condizione corrispondente. Ogni riga che corrisponde alla prima condizione passa al flusso di output `moviesBefore1960`. Tutte le righe rimanenti corrispondenti alla seconda condizione passano al flusso di output `moviesAFter1980`. Tutte le altre righe passano attraverso il flusso predefinito `AllOtherMovies`.

In Data Factory UX questa trasformazione è simile all'immagine seguente:

![suddivisione condizionale](media/data-flow/conditionalsplit1.png "opzioni di suddivisione condizionale")

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

Le trasformazioni comuni del flusso di dati utilizzate con Suddivisione condizionale sono la [trasformazione Join](data-flow-join.md), la [trasformazione Ricerca](data-flow-lookup.md) e la [trasformazione Selezione](data-flow-select.md)
