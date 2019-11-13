---
title: Trasformazione join nel flusso di dati del mapping Azure Data Factory
description: Combinare i dati di due origini dati utilizzando la trasformazione join nel flusso di dati di mapping Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: aae502b350f2cf2e98849b2b6e25543516a0c547
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961830"
---
# <a name="join-transformation-in-mapping-data-flow"></a>Trasformazione join nel flusso di dati di mapping

Utilizzare la trasformazione join per combinare i dati di due origini o flussi in un flusso di dati di mapping. Il flusso di output includerà tutte le colonne di entrambe le origini corrispondenti in base a una condizione di join. 

## <a name="join-types"></a>Tipi di join

I flussi di dati di mapping attualmente supportano cinque tipi di join diversi.

### <a name="inner-join"></a>Inner join

Inner join restituisce solo le righe con valori corrispondenti per entrambe le tabelle.

### <a name="left-outer"></a>Left outer join

Left outer join restituisce tutte le righe dal flusso di sinistra e i record corrispondenti dal flusso destro. Se una riga del flusso di sinistra non corrisponde, le colonne di output del flusso di destra vengono impostate su NULL. L'output sarà costituito dalle righe restituite da un inner join più le righe senza corrispondenza del flusso di sinistra.

### <a name="right-outer"></a>Right outer join

Right outer join restituisce tutte le righe dal flusso destro e i record corrispondenti dal flusso di sinistra. Se una riga dal flusso destro non corrisponde, le colonne di output del flusso di sinistra vengono impostate su NULL. L'output sarà costituito dalle righe restituite da un inner join più le righe senza corrispondenza del flusso destro.

### <a name="full-outer"></a>Full outer join

Il outer join completo restituisce tutte le colonne e le righe di entrambi i lati con valori NULL per le colonne non corrispondenti.

### <a name="cross-join"></a>Cross join

Cross join restituisce il prodotto incrociato dei due flussi in base a una condizione. Se si usa una condizione che non è uguaglianza, specificare un'espressione personalizzata come condizione cross join. Il flusso di output sarà costituito da tutte le righe che soddisfano la condizione di join. Per creare un prodotto cartesiano che restituisce ogni combinazione di righe, specificare `true()` come condizione di join.

## <a name="configuration"></a>Configurazione

1. Scegliere il flusso di dati con cui si sta eseguendo l'Unione nell'elenco a discesa **flusso destro** .
1. Selezionare il **tipo di join**
1. Scegliere le colonne chiave per le quali si desidera trovare una corrispondenza per la condizione di join. Per impostazione predefinita, il flusso di dati cerca l'uguaglianza tra una colonna in ogni flusso. Per eseguire il confronto tramite un valore calcolato, passare il puntatore del mouse sull'elenco a discesa della colonna e selezionare **colonna calcolata**.

![Trasformazione join](media/data-flow/join.png "Join")

## <a name="optimizing-join-performance"></a>Ottimizzazione delle prestazioni di join

A differenza di merge join in strumenti come SSIS, la trasformazione join non è un'operazione di merge join obbligatoria. Le chiavi di join non richiedono l'ordinamento. L'operazione di join si verifica in base all'operazione di join ottimale in Spark, ovvero broadcast o join sul lato mappa.

![Ottimizzazione trasformazione join](media/data-flow/joinoptimize.png "Ottimizzazione del join")

Se uno o entrambi i flussi di dati rientrano nella memoria del nodo di lavoro, ottimizzare ulteriormente le prestazioni abilitando **broadcast** nella scheda Ottimizza. È anche possibile ripartizionare i dati nell'operazione di join in modo da renderli più adatti alla memoria per ogni thread di lavoro.

## <a name="self-join"></a>Self-join

Per aggiungere autonomamente un flusso di dati a se stesso, eseguire l'alias di un flusso esistente con una trasformazione Select. Creare un nuovo ramo facendo clic sull'icona a forma di segno più accanto a una trasformazione e selezionando **nuovo ramo**. Aggiungere una trasformazione SELECT per l'alias del flusso originale. Aggiungere una trasformazione join e scegliere il flusso originale come flusso di **sinistra** e la trasformazione selezione come **flusso a destra**.

![Self-join](media/data-flow/selfjoin.png "Self-join")

## <a name="testing-join-conditions"></a>Test delle condizioni di join

Quando si verificano le trasformazioni join con Anteprima dati in modalità di debug, utilizzare un piccolo set di dati noti. Quando si campionano righe da un set di dati di grandi dimensioni, non è possibile prevedere quali righe e chiavi verranno lette per il test. Il risultato è non deterministico, vale a dire che le condizioni di join non possono restituire corrispondenze.

## <a name="data-flow-script"></a>Script del flusso di dati

### <a name="syntax"></a>Sintassi

```
<leftStream>, <rightStream>
    join(
        <conditionalExpression>,
        joinType: { 'inner'> | 'outer' | 'left_outer' | 'right_outer' | 'cross' }
        broadcast: { 'none' | 'left' | 'right' | 'both' }
    ) ~> <joinTransformationName>
```

### <a name="inner-join-example"></a>Esempio di Inner join

Nell'esempio seguente viene illustrata una trasformazione join denominata `JoinMatchedData` che accetta il flusso sinistro `TripData` e il flusso destro `TripFare`.  La condizione di join è l'espressione `hack_license == { hack_license} && TripData@medallion == TripFare@medallion && vendor_id == { vendor_id} && pickup_datetime == { pickup_datetime}` che restituisce true se le colonne `hack_license`, `medallion`, `vendor_id`e `pickup_datetime` in ogni flusso corrispondono. Il `joinType` è `'inner'`. È in corso l'abilitazione della trasmissione solo nel flusso di sinistra, quindi `broadcast` ha un valore `'left'`.

In Data Factory UX questa trasformazione è simile all'immagine seguente:

![Esempio di join](media/data-flow/join-script1.png "Esempio di join")

Lo script del flusso di dati per questa trasformazione si trova nel frammento di codice seguente:

```
TripData, TripFare
    join(
        hack_license == { hack_license}
        && TripData@medallion == TripFare@medallion
        && vendor_id == { vendor_id}
        && pickup_datetime == { pickup_datetime},
        joinType:'inner',
        broadcast: 'left'
    )~> JoinMatchedData
```

### <a name="cross-join-example"></a>Esempio di cross join

Nell'esempio seguente viene illustrata una trasformazione join denominata `CartesianProduct` che accetta il flusso sinistro `TripData` e il flusso destro `TripFare`. Questa trasformazione accetta due flussi e restituisce un prodotto cartesiano delle relative righe. La condizione di join è `true()` perché restituisce un prodotto cartesiano completo. `joinType` in `cross`. È in corso l'abilitazione della trasmissione solo nel flusso di sinistra, quindi `broadcast` ha un valore `'left'`.

In Data Factory UX questa trasformazione è simile all'immagine seguente:

![Esempio di join](media/data-flow/join-script2.png "Esempio di join")

Lo script del flusso di dati per questa trasformazione si trova nel frammento di codice seguente:

```
TripData, TripFare
    join(
        true(),
        joinType:'cross',
        broadcast: 'left'
    )~> CartesianProduct
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver unito i dati, creare una [colonna derivata](data-flow-derived-column.md) e [affondare](data-flow-sink.md) i dati in un archivio dati di destinazione.
