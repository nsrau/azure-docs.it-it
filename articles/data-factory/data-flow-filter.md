---
title: Trasformazione filtro nel flusso di dati di mapping
description: Filtrare le righe utilizzando la trasformazione filtro nel flusso di dati del mapping Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: 32a40f3d4da93318c6d2ec25295c89a7b53141e4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606433"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>Trasformazione filtro nel flusso di dati di mapping

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Le trasformazioni di filtro consentono di filtrare le righe in base a una condizione. Il flusso di output include tutte le righe che corrispondono alla condizione di filtro. La trasformazione filtro è simile a una clausola WHERE in SQL.

## <a name="configuration"></a>Configurazione

Utilizzare il generatore di espressioni del flusso di dati per immettere un'espressione per la condizione di filtro. Per aprire il generatore di espressioni, fare clic sulla casella blu. La condizione di filtro deve essere di tipo booleano. Per ulteriori informazioni su come creare un'espressione, vedere la documentazione del [Generatore di espressioni](concepts-data-flow-expression-builder.md) .

![Filtra trasformazione](media/data-flow/filter1.png "Filtra trasformazione")

## <a name="data-flow-script"></a>Script del flusso di dati

### <a name="syntax"></a>Sintassi

```
<incomingStream>
    filter(
        <conditionalExpression>
    ) ~> <filterTransformationName>
```

### <a name="example"></a>Esempio

L'esempio seguente è una trasformazione filtro denominata `FilterBefore1960` che accetta il flusso `CleanData`in ingresso. La condizione di filtro è l' `year <= 1960`espressione.

In Data Factory UX questa trasformazione è simile all'immagine seguente:

![Filtra trasformazione](media/data-flow/filter1.png "Filtra trasformazione")

Lo script del flusso di dati per questa trasformazione si trova nel frammento di codice seguente:

```
CleanData
    filter(
        year <= 1960
    ) ~> FilterBefore1960

```

## <a name="next-steps"></a>Passaggi successivi

Filtrare le colonne con la [trasformazione Select](data-flow-select.md)
