---
title: Trasformazione di aggregazione nel mapping del flusso di datiAggregate transformation in mapping data flow
description: Informazioni su come aggregare i dati su larga scala in Azure Data Factory con la trasformazione Di aggregazione del flusso di dati di mapping.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/24/2020
ms.openlocfilehash: 1830a16108e6d8bb251d7ca45ae471e2f606874b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240603"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>Trasformazione di aggregazione nel mapping del flusso di datiAggregate transformation in mapping data flow 

La trasformazione Aggregazione definisce le aggregazioni delle colonne nei flussi di dati. Utilizzando il Generatore di espressioni, è possibile definire diversi tipi di aggregazioni, ad esempio SUM, MIN, MAX e COUNT raggruppate in base a colonne esistenti o calcolate.

## <a name="group-by"></a>Group by

Selezionare una colonna esistente o creare una nuova colonna calcolata da utilizzare come clausola group by per l'aggregazione. Per utilizzare una colonna esistente, selezionarla dall'elenco a discesa. Per creare una nuova colonna calcolata, passare il mouse sulla clausola e fare clic su **Colonna calcolata**. Verrà aperto il [generatore di espressioni del flusso](concepts-data-flow-expression-builder.md)di dati . Dopo aver creato la colonna calcolata, immettere il nome della colonna di output nel campo **Nome come.** Se si desidera aggiungere un'ulteriore clausola group by, passare il mouse su una clausola esistente e fare clic sull'icona più.

![Aggregare il gruppo di trasformazioni in base alle impostazioni](media/data-flow/agg.png "Aggregare il gruppo di trasformazioni in base alle impostazioni")

Una clausola group by è facoltativa in una trasformazione Aggregazione.A group by clause is optional in an Aggregate transformation.

## <a name="aggregate-column"></a>Colonna di aggregazione 

Passare alla scheda **Aggregazioni** per creare espressioni di aggregazione. È possibile sovrascrivere una colonna esistente con un'aggregazione oppure creare un nuovo campo con un nuovo nome. L'espressione di aggregazione viene immessa nella casella a destra accanto al selettore del nome della colonna. Per modificare l'espressione, fare clic sulla casella di testo per aprire il generatore di espressioni. Per aggiungere altre aggregazioni, passare il mouse su un'espressione esistente e fare clic sull'icona più per creare un nuovo modello di colonna o [colonna](concepts-data-flow-column-pattern.md)di aggregazione.

Ogni espressione di aggregazione deve contenere almeno una funzione di aggregazione.

![Impostazioni di aggregazione delle aggregazioni per le aggregazioni](media/data-flow/agg2.png "Impostazioni di aggregazione delle aggregazioni per le aggregazioni")


> [!NOTE]
> In modalità Debug, il generatore di espressioni non può produrre anteprime dei dati con funzioni di aggregazione. Per visualizzare le anteprime dei dati per le trasformazioni di aggregazione, chiudere il generatore di espressioni e visualizzare i dati tramite la scheda 'Anteprima dati'.

## <a name="reconnect-rows-and-columns"></a>Riconnettere righe e colonne

Le trasformazioni di aggregazione sono simili alle query di selezione di aggregazione SQL. Le colonne non incluse nella clausola group by o nelle funzioni di aggregazione non passeranno all'output della trasformazione di aggregazione. Se si desidera includere altre colonne nell'output aggregato, eseguire uno dei metodi seguenti:

* Utilizzare una funzione `last()` di `first()` aggregazione, ad esempio o per includere tale colonna aggiuntiva.
* Riaggiungere le colonne al flusso di output utilizzando il [modello di self join](https://mssqldude.wordpress.com/2018/12/20/adf-data-flows-self-join/).

## <a name="removing-duplicate-rows"></a>Rimozione di righe duplicate

Un utilizzo comune della trasformazione di aggregazione è la rimozione o l'identificazione di voci duplicate nei dati di origine. Questo processo è noto come deduplicazione. In base a un set di chiavi raggruppa in base a categorie, utilizzare un'euristica di propria scelta per determinare quale riga duplicata mantenere. L'euristica `first()`comune `last()` `max()`è `min()`, , , e . Utilizzare [i modelli](concepts-data-flow-column-pattern.md) di colonna per applicare la regola a tutte le colonne ad eccezione del raggruppamento per colonne.

![Deduplicazione](media/data-flow/agg-dedupe.png "Deduplicazione")

Nell'esempio precedente, `ProductID` `Name` le colonne e vengono utilizzate per il raggruppamento. Se due righe hanno gli stessi valori per queste due colonne, vengono considerate duplicati. In questa trasformazione di aggregazione, i valori della prima riga corrispondente verranno mantenuti e tutti gli altri verranno eliminati. Utilizzando la sintassi del modello di `ProductID` `Name` colonna, tutte le colonne i cui nomi non sono e vengono mappate al nome di colonna esistente e viene fornito il valore delle prime righe corrispondenti. Lo schema di output è lo stesso dello schema di input.

Per gli scenari `count()` di convalida dei dati, la funzione può essere utilizzata per contare il numero di duplicati presenti.

## <a name="data-flow-script"></a>Script del flusso di dati

### <a name="syntax"></a>Sintassi

```
<incomingStream>
    aggregate(
           groupBy(
                <groupByColumnName> = <groupByExpression1>,
                <groupByExpression2>
               ),
           <aggregateColumn1> = <aggregateExpression1>,
           <aggregateColumn2> = <aggregateExpression2>,
           each(
                match(matchExpression),
                <metadataColumn1> = <metadataExpression1>,
                <metadataColumn2> = <metadataExpression2>
               )
          ) ~> <aggregateTransformationName>
```

### <a name="example"></a>Esempio

Nell'esempio seguente viene `MoviesYear` utilizzato un `year`flusso in ingresso e vengono raggruppate le righe per colonna . La trasformazione crea `avgrating` una colonna di aggregazione che restituisce la media della colonna `Rating`. Questa trasformazione `AvgComedyRatingsByYear`di aggregazione è denominata .

Nell'esperienza utente di Data Factory questa trasformazione è simile all'immagine seguente:In the Data Factory UX, this transformation looks like the below image:

![Raggruppa per esempio](media/data-flow/agg-script1.png "Raggruppa per esempio")

![Esempio di aggregazione](media/data-flow/agg-script2.png "Esempio di aggregazione")

Lo script del flusso di dati per questa trasformazione si trova nel frammento di codice seguente.

```
MoviesYear aggregate(
                groupBy(year),
                avgrating = avg(toInteger(Rating))
            ) ~> AvgComedyRatingByYear
```

## <a name="next-steps"></a>Passaggi successivi

* Definire l'aggregazione basata su finestre usando la [trasformazione FinestraDefine](data-flow-window.md) window-based aggregation using the Window transformation
