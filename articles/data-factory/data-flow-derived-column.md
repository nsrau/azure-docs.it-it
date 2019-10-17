---
title: Trasformazione colonna derivata nel flusso di dati di mapping-Azure Data Factory | Microsoft Docs
description: Informazioni su come trasformare i dati su larga scala in Azure Data Factory con la trasformazione colonna derivata flusso di dati mapping.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 5a4ee90717d46fe593d9e10083b349e069216dac
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72436759"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>Trasformazione colonna derivata nel flusso di dati di mapping

Utilizzare la trasformazione colonna derivata per generare nuove colonne nel flusso di dati o per modificare campi esistenti.

## <a name="derived-column-settings"></a>Impostazioni colonna derivata

Per eseguire l'override di una colonna esistente, selezionarla tramite l'elenco a discesa colonna. In caso contrario, usare il campo di selezione della colonna come casella di testo e digitare il nome della nuova colonna. Per compilare l'espressione della colonna derivata, fare clic sulla casella "immettere l'espressione" per aprire il [Generatore di espressioni del flusso di dati](concepts-data-flow-expression-builder.md).

![Impostazioni colonna derivata](media/data-flow/dc1.png "Impostazioni colonna derivata")

Per aggiungere altre colonne derivate, passare il mouse su una colonna derivata esistente e fare clic sull'icona del segno più. Scegliere **Aggiungi colonna** o **Aggiungi modello colonna**. I modelli di colonna possono essere utili se i nomi delle colonne sono variabili dalle origini. Per ulteriori informazioni, vedere [modelli di colonna](concepts-data-flow-column-pattern.md).

![Nuova selezione colonna derivata](media/data-flow/columnpattern.png "Nuova selezione colonna derivata")

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

L'esempio seguente è una colonna derivata denominata `CleanData` che accetta un flusso in ingresso `MoviesYear` e crea due colonne derivate. La prima colonna derivata sostituisce la colonna `Rating` con il valore della classificazione come tipo Integer. La seconda colonna derivata è un modello che corrisponde a ogni colonna il cui nome inizia con "Movies". Per ogni colonna corrispondente, viene creata una colonna `movie` uguale al valore della colonna corrispondente con prefisso ' movie_'. In Data Factory UX questa trasformazione è simile all'immagine seguente:

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

- Altre informazioni sul [linguaggio delle espressioni del flusso di dati di mapping](data-flow-expression-functions.md).
