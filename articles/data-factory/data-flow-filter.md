---
title: Trasformazione filtro nel flusso di dati di mapping
description: Filtrare le righe utilizzando la trasformazione filtro nel flusso di dati del mapping Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/26/2020
ms.openlocfilehash: 8189228d6707812fb943e9925dc2bbf1b6da4972
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84112802"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>Trasformazione filtro nel flusso di dati di mapping

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Le trasformazioni di filtro consentono di filtrare le righe in base a una condizione. Il flusso di output include tutte le righe che corrispondono alla condizione di filtro. La trasformazione filtro è simile a una clausola WHERE in SQL.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4xnxN]

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

L'esempio seguente è una trasformazione filtro denominata `FilterBefore1960` che accetta il flusso in ingresso `CleanData` . La condizione di filtro è l'espressione `year <= 1960` .

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
