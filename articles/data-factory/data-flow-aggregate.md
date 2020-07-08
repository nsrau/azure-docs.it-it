---
title: Trasformazione aggregazione nel flusso di dati di mapping
description: Informazioni su come aggregare i dati su larga scala in Azure Data Factory con la trasformazione aggregazione del flusso di dati di mapping.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/24/2020
ms.openlocfilehash: 871f2b49e2dce9d762ef8a54923da04b0f24e4be
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81606529"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>Trasformazione aggregazione nel flusso di dati di mapping

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

La trasformazione aggregazione definisce le aggregazioni di colonne nei flussi di dati. Utilizzando il generatore di espressioni, è possibile definire diversi tipi di aggregazione, ad esempio SUM, MIN, MAX e COUNT raggruppati in base a colonne esistenti o calcolate.

## <a name="group-by"></a>Group by

Consente di selezionare una colonna esistente o di creare una nuova colonna calcolata da utilizzare come clausola Group by per l'aggregazione. Per utilizzare una colonna esistente, selezionarla nell'elenco a discesa. Per creare una nuova colonna calcolata, passare il puntatore del mouse sulla clausola e fare clic su **colonna calcolata**. Verrà aperto il [Generatore di espressioni del flusso di dati](concepts-data-flow-expression-builder.md). Dopo aver creato la colonna calcolata, immettere il nome della colonna di output sotto il campo **nome come** . Se si desidera aggiungere una clausola Group by aggiuntiva, passare il puntatore del mouse su una clausola esistente e fare clic sull'icona con il segno più.

![Raggruppamento della trasformazione aggregazione per impostazioni](media/data-flow/agg.png "Raggruppamento della trasformazione aggregazione per impostazioni")

Una clausola Group by è facoltativa in una trasformazione aggregazione.

## <a name="aggregate-column"></a>Colonna aggregazione 

Passare alla scheda **aggregazioni** per compilare le espressioni di aggregazione. È possibile sovrascrivere una colonna esistente con un'aggregazione oppure creare un nuovo campo con un nuovo nome. L'espressione di aggregazione viene immessa nella casella a destra accanto al selettore del nome di colonna. Per modificare l'espressione, fare clic sulla casella di testo per aprire il generatore di espressioni. Per aggiungere altre aggregazioni, passare il puntatore del mouse su un'espressione esistente e fare clic sull'icona del segno più per creare un nuovo [modello](concepts-data-flow-column-pattern.md)di colonna o colonna di aggregazione.

Ogni espressione di aggregazione deve contenere almeno una funzione di aggregazione.

![Impostazioni aggregate trasformazione aggregazione](media/data-flow/agg2.png "Impostazioni aggregate trasformazione aggregazione")


> [!NOTE]
> In modalità di debug, il generatore di espressioni non può produrre anteprime dei dati con funzioni di aggregazione. Per visualizzare le anteprime dei dati per le trasformazioni aggregate, chiudere il generatore di espressioni e visualizzare i dati tramite la scheda "Anteprima dati".

## <a name="reconnect-rows-and-columns"></a>Riconnettere righe e colonne

Le trasformazioni di aggregazione sono simili alle query SELECT di aggregazione SQL. Le colonne che non sono incluse nella clausola Group by o funzioni di aggregazione non passano attraverso l'output della trasformazione aggregazione. Se si desidera includere altre colonne nell'output aggregato, effettuare uno dei seguenti metodi:

* Utilizzare una funzione di aggregazione, ad esempio `last()` o, `first()` per includere la colonna aggiuntiva.
* Aggiungere nuovamente le colonne al flusso di output usando il [modello self join](https://mssqldude.wordpress.com/2018/12/20/adf-data-flows-self-join/).

## <a name="removing-duplicate-rows"></a>Rimozione di righe duplicate

Un utilizzo comune della trasformazione aggregazione consiste nella rimozione o nell'identificazione di voci duplicate nei dati di origine. Questo processo è noto come deduplicazione. In base a un set di chiavi Group by, usare l'euristica scelta per determinare la riga duplicata da contenere. Le euristiche comuni sono `first()` ,, `last()` `max()` e `min()` . Usare i [criteri di colonna](concepts-data-flow-column-pattern.md) per applicare la regola a ogni colonna, ad eccezione delle colonne Group by.

![Deduplicazione](media/data-flow/agg-dedupe.png "Deduplicazione")

Nell'esempio precedente le colonne `ProductID` e `Name` vengono utilizzate per il raggruppamento. Se due righe hanno gli stessi valori per queste due colonne, sono considerate duplicati. In questa trasformazione aggregazione i valori della prima riga corrispondente verranno mantenuti e tutti gli altri verranno eliminati. Utilizzando la sintassi del criterio di colonna, tutte le colonne i cui nomi non sono `ProductID` e `Name` vengono mappate al nome della colonna esistente e in base al valore delle prime righe corrispondenti. Lo schema di output corrisponde allo schema di input.

Per gli scenari di convalida dei dati, la `count()` funzione può essere utilizzata per contare il numero di duplicati.

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

L'esempio seguente accetta un flusso in ingresso `MoviesYear` e raggruppa le righe per colonna `year` . La trasformazione crea una colonna di aggregazione `avgrating` che restituisce la media della colonna `Rating` . Questa trasformazione aggregata è denominata `AvgComedyRatingsByYear` .

In Data Factory UX questa trasformazione è simile all'immagine seguente:

![Esempio di Group by](media/data-flow/agg-script1.png "Esempio di Group by")

![Esempio di aggregazione](media/data-flow/agg-script2.png "Esempio di aggregazione")

Lo script del flusso di dati per questa trasformazione si trova nel frammento di codice seguente.

```
MoviesYear aggregate(
                groupBy(year),
                avgrating = avg(toInteger(Rating))
            ) ~> AvgComedyRatingByYear
```

![Script del flusso di dati aggregato](media/data-flow/aggdfs1.png "Script del flusso di dati aggregato")

```MoviesYear```: Colonna derivata che definisce le colonne anno e titolo ```AvgComedyRatingByYear``` : trasformazione aggregazione per la valutazione media delle Comedie raggruppate per anno ```avgrating``` : nome della nuova colonna da creare per conservare il valore aggregato

```
MoviesYear aggregate(groupBy(year),
    avgrating = avg(toInteger(Rating))) ~> AvgComedyRatingByYear
```

## <a name="next-steps"></a>Passaggi successivi

* Definire un'aggregazione basata su finestra mediante la [trasformazione finestra](data-flow-window.md)
