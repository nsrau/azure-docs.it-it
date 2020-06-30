---
title: Trasformazione Colonna derivata nel flusso di dati per mapping
description: Informazioni su come trasformare i dati su larga scala in Azure Data Factory con la trasformazione Colonna derivata nel flusso di dati per mapping.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/15/2019
ms.openlocfilehash: 38ec2d4619f47bf9fc4d1815cb6e9990cef72dcf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606498"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>Trasformazione Colonna derivata nel flusso di dati per mapping

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Usare la trasformazione Colonna derivata per generare nuove colonne nel flusso di dati o per modificare campi esistenti.

## <a name="derived-column-settings"></a>Impostazioni della colonna derivata

Per eseguire l'override di una colonna esistente, selezionarla tramite l'elenco a discesa della colonna. In caso contrario, usare il campo di selezione della colonna come casella di testo e digitare al suo interno il nome della nuova colonna. Per compilare l'espressione della colonna derivata, fare clic sulla casella "Immetti espressione" per aprire il [Generatore di espressioni del flusso di dati](concepts-data-flow-expression-builder.md).

![Impostazioni della colonna derivata](media/data-flow/dc1.png "Impostazioni della colonna derivata")

Per aggiungere altre colonne derivate, passare il mouse su una colonna derivata esistente e fare clic sull'icona del segno più. Scegliere **Aggiungi colonna** o **Aggiungi criteri di ricerca colonna**. I criteri di ricerca colonna possono essere utili se i nomi delle colonne variano rispetto alle origini. Per altre informazioni, vedere [Criteri di ricerca colonna](concepts-data-flow-column-pattern.md).

![Selezione di una nuova colonna derivata](media/data-flow/columnpattern.png "Selezione di una nuova colonna derivata")

## <a name="build-schemas-in-output-schema-pane"></a>Schemi di compilazione nel riquadro Schema di output

Le colonne che vengono modificate e aggiunte allo schema sono elencate nel riquadro Schema di output, in cui è possibile compilare in modo interattivo strutture di dati semplici e complesse. Per aggiungere altri campi, selezionare **Aggiungi colonna**. Per compilare le gerarchie, selezionare **Aggiungi sottocolonna**.

![Aggiungi sottocolonna](media/data-flow/addsubcolumn.png "Aggiungi sottocolonna")

Per altre informazioni sulla gestione di tipi complessi nel flusso di dati, vedere [Gestione del formato JSON nel flusso di dati per mapping](format-json.md#mapping-data-flow-properties).

![Aggiungi colonna complessa](media/data-flow/complexcolumn.png "Aggiungi colonne")

## <a name="data-flow-script"></a>Script del flusso di dati

### <a name="syntax"></a>Sintassi

```
<incomingStream>
    derive(
           <columnName1> = <expression1>,
           <columnName2> = <expression2>,
           each(
                match(matchExpression),
                <metadataColumn1> = <metadataExpression1>,
                <metadataColumn2> = <metadataExpression2>
               )
          ) ~> <deriveTransformationName>
```

### <a name="example"></a>Esempio

L'esempio seguente rappresenta una colonna derivata denominata `CleanData` che accetta un flusso in ingresso `MoviesYear` e crea due colonne derivate. La prima colonna derivata sostituisce la colonna `Rating` con un valore di valutazione come tipo Integer. La seconda colonna derivata è un modello a cui corrisponde ogni colonna il cui nome inizia con "movies". Per ogni colonna corrispondente, viene creata una colonna `movie` uguale al valore della colonna corrispondente con il prefisso ' movie_'. 

In Data Factory UX questa trasformazione è simile all'immagine seguente:

![Esempio di derivazione](media/data-flow/derive-script1.png "Esempio di derivazione")

Lo script del flusso di dati per questa trasformazione si trova nel frammento di codice seguente:

```
MoviesYear derive(
                Rating = toInteger(Rating),
                each(
                    match(startsWith(name,'movies')),
                    'movie' = 'movie_' + toString($$)
                )
            ) ~> CleanData
```

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sul [Linguaggio delle espressioni del flusso di dati per mapping](data-flow-expression-functions.md).
