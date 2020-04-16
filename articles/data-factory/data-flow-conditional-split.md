---
title: Trasformazione di divisione condizionale nel mapping del flusso di datiConditional split transformation in mapping data flow
description: Dividere i dati in flussi diversi usando la trasformazione suddivisione condizionale nel flusso di dati di mapping di Azure Data FactorySplit data into different streams using the conditional split transformation in Azure Data Factory mapping data flow
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: 20def8ca51f21d914e7090999e8284244c5f3ec7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416505"
---
# <a name="conditional-split-transformation-in-mapping-data-flow"></a>Trasformazione di divisione condizionale nel mapping del flusso di datiConditional split transformation in mapping data flow

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

La trasformazione suddivisione condizionale indirizza le righe di dati a flussi diversi in base alle condizioni di corrispondenza. La trasformazione di divisione condizionale è simile a una struttura decisionale CASE in un linguaggio di programmazione. La trasformazione valuta le espressioni e, in base ai risultati, indirizza la riga di dati al flusso specificato.

## <a name="configuration"></a>Configurazione

L'impostazione **Dividi in** determina se la riga di dati scorre nel primo flusso corrispondente o in ogni flusso a cui corrisponde.

Utilizzare il generatore di espressioni del flusso di dati per immettere un'espressione per la condizione di divisione. Per aggiungere una nuova condizione, fare clic sull'icona più in una riga esistente. È inoltre possibile aggiungere un flusso predefinito per le righe che non soddisfano alcuna condizione.

![divisione condizionale](media/data-flow/conditionalsplit1.png "opzioni di suddivisione condizionale")

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

L'esempio seguente è una `SplitByYear` trasformazione di `CleanData`divisione condizionale denominata che accetta nel flusso in ingresso. Questa trasformazione ha `year < 1960` `year > 1980`due condizioni divise e . `disjoint`è false perché i dati passano alla prima condizione corrispondente. Ogni riga che corrisponde alla `moviesBefore1960`prima condizione viene restituita al flusso di output. Tutte le righe rimanenti che `moviesAFter1980`corrispondono alla seconda condizione vengono rese nel flusso di output. Tutte le altre righe `AllOtherMovies`scorrono nel flusso predefinito.

Nell'esperienza utente di Data Factory questa trasformazione è simile all'immagine seguente:In the Data Factory UX, this transformation looks like the below image:

![divisione condizionale](media/data-flow/conditionalsplit1.png "opzioni di suddivisione condizionale")

Lo script del flusso di dati per questa trasformazione si trova nel frammento di codice seguente:The data flow script for this transformation is in the snippet below:

```
CleanData
    split(
        year < 1960,
        year > 1980,
        disjoint: false
    ) ~> SplitByYear@(moviesBefore1960, moviesAfter1980, AllOtherMovies)
```

## <a name="next-steps"></a>Passaggi successivi

Le trasformazioni comuni del flusso di dati utilizzate con la divisione condizionale sono la [trasformazione join](data-flow-join.md), la [trasformazione di ricerca](data-flow-lookup.md)e la [trasformazione select](data-flow-select.md)
