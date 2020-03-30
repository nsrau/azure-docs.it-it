---
title: Trasformazione di colonna derivata nel mapping del flusso di datiDerived column transformation in mapping data flow
description: Informazioni su come trasformare i dati su larga scala in Azure Data Factory con la trasformazione Colonna derivata del flusso di dati di mapping.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/15/2019
ms.openlocfilehash: 66396de52b3709c1d9357f32a375a29a8dcdbd1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77048743"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>Trasformazione di colonna derivata nel mapping del flusso di datiDerived column transformation in mapping data flow

Utilizzare la trasformazione di colonna derivata per generare nuove colonne nel flusso di dati o per modificare i campi esistenti.

## <a name="derived-column-settings"></a>Impostazioni delle colonne derivate

Per sostituire una colonna esistente, selezionarla tramite l'elenco a discesa delle colonne. In caso contrario, utilizzare il campo di selezione della colonna come casella di testo e digitare il nome della nuova colonna. Per compilare l'espressione della colonna derivata, fare clic sulla casella 'Inserisci espressione' per aprire il [Generatore di espressioni flusso](concepts-data-flow-expression-builder.md)di dati .

![Impostazioni delle colonne derivate](media/data-flow/dc1.png "Impostazioni delle colonne derivate")

Per aggiungere altre colonne derivate, passare il mouse su una colonna derivata esistente e fare clic sull'icona più. Scegliere **Aggiungi colonna** o **Aggiungi motivo colonna**. I modelli di colonna possono risultare utili se i nomi delle colonne sono variabili dalle origini. Per ulteriori informazioni, vedere [Modelli di colonna](concepts-data-flow-column-pattern.md).

![Nuova selezione di colonne derivate](media/data-flow/columnpattern.png "Nuova selezione di colonne derivate")

## <a name="build-schemas-in-output-schema-pane"></a>Compilare schemi nel riquadro Schema di output

Le colonne che si stanno modificando e aggiungendo allo schema sono elencate nel riquadro Schema di output. Qui è possibile creare in modo interattivo strutture di dati semplici e complesse. Per aggiungere altri campi, selezionare **Aggiungi colonna**. Per creare gerarchie, selezionare **Aggiungi colonna secondaria**.

![Aggiungi colonna secondaria](media/data-flow/addsubcolumn.png "Aggiungi colonna secondaria")

Per altre informazioni sulla gestione di tipi complessi nel flusso di dati, vedere Gestione JSON nel mapping del [flusso di dati.](format-json.md#mapping-data-flow-properties)

![Aggiungi colonna complessa](media/data-flow/complexcolumn.png "Aggiungere colonne")

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

L'esempio seguente è `CleanData` una colonna derivata denominata che accetta un flusso `MoviesYear` in ingresso e crea due colonne derivate. La prima colonna derivata sostituisce la colonna `Rating` con rating valore come tipo integer. La seconda colonna derivata è un modello che corrisponde a ogni colonna il cui nome inizia con 'movies'. Per ogni colonna corrispondente, crea `movie` una colonna uguale al valore della colonna corrispondente preceduta da 'movie_'. 

Nell'esperienza utente di Data Factory questa trasformazione è simile all'immagine seguente:In the Data Factory UX, this transformation looks like the below image:

![Esempio derivato](media/data-flow/derive-script1.png "Esempio derivato")

Lo script del flusso di dati per questa trasformazione si trova nel frammento di codice seguente:The data flow script for this transformation is in the snippet below:

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

- Ulteriori informazioni sul [linguaggio](data-flow-expression-functions.md)delle espressioni Mapping flusso di dati .
