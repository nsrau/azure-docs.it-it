---
title: Trasformazione del filtro nel mapping del flusso di datiFilter transformation in mapping data flow
description: Filtrare le righe usando la trasformazione del filtro nel flusso di dati di mapping di Azure Data FactoryFilter out rows using the filter transformation in Azure Data Factory mapping data flow
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: 3460c789909c6acd0537ef89a9ec8509bf6e135d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413739"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>Trasformazione del filtro nel mapping del flusso di datiFilter transformation in mapping data flow

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Il filtro trasforma consente il filtraggio delle righe in base a una condizione. Il flusso di output include tutte le righe che corrispondono alla condizione di filtro. La trasformazione del filtro è simile a una clausola WHERE in SQL.

## <a name="configuration"></a>Configurazione

Utilizzare il generatore di espressioni del flusso di dati per immettere un'espressione per la condizione di filtro. Per aprire il generatore di espressioni, fare clic sulla casella blu. La condizione di filtro deve essere di tipo booleano. Per altre informazioni su come creare un'espressione, vedere la documentazione del [generatore di espressioni.](concepts-data-flow-expression-builder.md)

![Trasformazione del filtro](media/data-flow/filter1.png "Trasformazione del filtro")

## <a name="data-flow-script"></a>Script del flusso di dati

### <a name="syntax"></a>Sintassi

```
<incomingStream>
    filter(
        <conditionalExpression>
    ) ~> <filterTransformationName>
```

### <a name="example"></a>Esempio

L'esempio seguente è `FilterBefore1960` una trasformazione `CleanData`di filtro denominata che accetta nel flusso in ingresso. La condizione di `year <= 1960`filtro è l'espressione .

Nell'esperienza utente di Data Factory questa trasformazione è simile all'immagine seguente:In the Data Factory UX, this transformation looks like the below image:

![Trasformazione del filtro](media/data-flow/filter1.png "Trasformazione del filtro")

Lo script del flusso di dati per questa trasformazione si trova nel frammento di codice seguente:The data flow script for this transformation is in the snippet below:

```
CleanData
    filter(
        year <= 1960
    ) ~> FilterBefore1960

```

## <a name="next-steps"></a>Passaggi successivi

Filtrare le colonne con la [trasformazione di selezione](data-flow-select.md)
