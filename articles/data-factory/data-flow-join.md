---
title: Creare una trasformazione di join nel mapping del flusso di datiJoin transformation in mapping data flow
description: Combinare i dati da due origini dati usando la trasformazione join nel flusso di dati di mapping di Azure Data FactoryCombine data from two data sources using the join transformation in Azure Data Factory mapping data flow
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/02/2020
ms.openlocfilehash: 0c0e35f7f06afd0cafa4a1e353b7eda84ed226f2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413660"
---
# <a name="join-transformation-in-mapping-data-flow"></a>Creare una trasformazione di join nel mapping del flusso di datiJoin transformation in mapping data flow

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Usare la trasformazione Join per combinare i dati di due origini o flussi in un flusso di dati di mapping. Il flusso di output includerà tutte le colonne di entrambe le origini corrispondenti in base a una condizione di join. 

## <a name="join-types"></a>Tipi di join

Il mapping dei flussi di dati supporta attualmente cinque diversi tipi di join.

### <a name="inner-join"></a>Inner join

Il join interno restituisce solo le righe con valori corrispondenti in entrambe le tabelle.

### <a name="left-outer"></a>Left outer join

Left outer join restituisce tutte le righe dal flusso sinistro e i record corrispondenti dal flusso di destra. Se una riga del flusso sinistro non ha corrispondenza, le colonne di output del flusso di destra vengono impostate su NULL. L'output sarà le righe restituite da un inner join più le righe senza corrispondenza dal flusso di sinistra.

> [!NOTE]
> Il motore Spark utilizzato dai flussi di dati occasionalmente avrà esito negativo a causa di possibili prodotti cartesiani nelle condizioni di join. In questo caso, è possibile passare a un cross join personalizzato e immettere manualmente la condizione di join. Ciò potrebbe comportare un miglioramento delle prestazioni nei flussi di dati poiché il motore di esecuzione potrebbe dover calcolare tutte le righe da entrambi i lati della relazione e quindi filtrare le righe.

### <a name="right-outer"></a>Right outer join

Right outer join restituisce tutte le righe dal flusso di destra e i record corrispondenti dal flusso sinistro. Se una riga del flusso di destra non ha corrispondenza, le colonne di output del flusso sinistro vengono impostate su NULL. L'output sarà le righe restituite da un inner join più le righe non corrispondenti dal flusso di destra.

### <a name="full-outer"></a>Full outer join

Il full outer join restituisce tutte le colonne e le righe di entrambi i lati con valori NULL per le colonne non corrispondenti.

### <a name="custom-cross-join"></a>Cross join personalizzato

L'unione incrociata restituisce il prodotto incrociato dei due flussi in base a una condizione. Se si utilizza una condizione che non è uguaglianza, specificare un'espressione personalizzata come condizione di join incrociato. Il flusso di output sarà tutte le righe che soddisfano la condizione di join.

È possibile utilizzare questo tipo di join ```OR``` per join e condizioni non equi.

Se si desidera produrre in modo esplicito un prodotto cartesiano completo, utilizzare la trasformazione Colonna derivata in ognuno dei due flussi indipendenti prima del join per creare una chiave sintetica su cui trovare la corrispondenza. Ad esempio, creare una nuova colonna in ```SyntheticKey``` Colonna derivata ```1```in ogni flusso denominato e impostarla uguale a . Utilizzare ```a.SyntheticKey == b.SyntheticKey``` quindi come espressione di join personalizzata.

> [!NOTE]
> Assicurati di includere almeno una colonna da ogni lato della relazione sinistra e destra in un cross join personalizzato. L'esecuzione di cross join con valori statici anziché colonne da ogni lato comporta scansioni complete dell'intero set di dati, con prestazioni insufficienti per il flusso di dati.

## <a name="configuration"></a>Configurazione

1. Scegli il flusso di dati con cui ti unisci nell'elenco a discesa **Flusso a destra.**
1. Seleziona il tipo **di iscrizione**
1. Scegliere le colonne chiave da abbinare per la condizione di join. Per impostazione predefinita, il flusso di dati cerca l'uguaglianza tra una colonna in ogni flusso. Per eseguire il confronto tramite un valore calcolato, passare il mouse sull'elenco a discesa delle colonne e selezionare **Colonna calcolata**.

![Unisci trasformazione](media/data-flow/join.png "Join")

## <a name="optimizing-join-performance"></a>Ottimizzazione delle prestazioni di joinOptimizing join performance

A differenza dell'unione join in strumenti come SSIS, la trasformazione join non è un'operazione di merge join obbligatoria. Le chiavi di join non richiedono l'ordinamento. L'operazione di join viene eseguita in base all'operazione di join ottimale in Spark, broadcast o join lato mappa.

![Ottimizzazione trasformazione Join Transformation optimize](media/data-flow/joinoptimize.png "Ottimizzazione dei join")

Se uno o entrambi i flussi di dati rientrano nella memoria del nodo di lavoro, ottimizzare ulteriormente le prestazioni abilitando **Broadcast** nella scheda Ottimizza. È inoltre possibile ripartizionare i dati nell'operazione di join in modo che si adattino meglio alla memoria per ogni worker.

## <a name="self-join"></a>Self-join

Per unire autonomamente un flusso di dati con se stesso, aggiungere un alias a un flusso esistente con una trasformazione di selezione. Creare un nuovo ramo facendo clic sull'icona più accanto a una trasformazione e selezionando **Nuovo ramo**. Aggiungere una trasformazione di selezione all'alias del flusso originale. Aggiungere una trasformazione di join e scegliere il flusso originale come **flusso sinistro** e trasformazione di selezione come **flusso destro**.

![Self-join](media/data-flow/selfjoin.png "Self-join")

## <a name="testing-join-conditions"></a>Test delle condizioni di join

Quando si testano le trasformazioni di join con anteprima dei dati in modalità di debug, usare un piccolo set di dati noti. Quando si esegue il campionamento di righe da un set di dati di grandi dimensioni, non è possibile prevedere quali righe e chiavi verranno lette per il test. Il risultato è non deterministico, il che significa che le condizioni di join potrebbero non restituire alcuna corrispondenza.

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

### <a name="inner-join-example"></a>Esempio di inner join

L'esempio seguente è `JoinMatchedData` una trasformazione `TripData` join `TripFare`denominata che accetta flusso sinistro e flusso destro .  La condizione di `hack_license == { hack_license} && TripData@medallion == TripFare@medallion && vendor_id == { vendor_id} && pickup_datetime == { pickup_datetime}` join è l'espressione `vendor_id`che `pickup_datetime` restituisce true se le `hack_license`colonne , `medallion`, e in ogni flusso corrispondono. L'elemento `joinType` è `'inner'`. Stiamo abilitando la trasmissione solo `broadcast` nel `'left'`flusso sinistro in modo ha valore .

Nell'esperienza utente di Data Factory questa trasformazione è simile all'immagine seguente:In the Data Factory UX, this transformation looks like the below image:

![Esempio di join](media/data-flow/join-script1.png "Esempio di join")

Lo script del flusso di dati per questa trasformazione si trova nel frammento di codice seguente:The data flow script for this transformation is in the snippet below:

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

### <a name="custom-cross-join-example"></a>Esempio di cross join personalizzato

L'esempio seguente è `JoiningColumns` una trasformazione `LeftStream` join `RightStream`denominata che accetta flusso sinistro e flusso destro . Questa trasformazione accetta due flussi e unisce `leftstreamcolumn` tutte le `rightstreamcolumn`righe in cui column è maggiore di column . L'elemento `joinType` è `cross`. La trasmissione `broadcast` non `'none'`abilitata ha valore .

Nell'esperienza utente di Data Factory questa trasformazione è simile all'immagine seguente:In the Data Factory UX, this transformation looks like the below image:

![Esempio di join](media/data-flow/join-script2.png "Esempio di join")

Lo script del flusso di dati per questa trasformazione si trova nel frammento di codice seguente:The data flow script for this transformation is in the snippet below:

```
LeftStream, RightStream
    join(
        leftstreamcolumn > rightstreamcolumn,
        joinType:'cross',
        broadcast: 'none'
    )~> JoiningColumns
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver unito i dati, creare una [colonna derivata](data-flow-derived-column.md) e eseguire il [sink](data-flow-sink.md) dei dati in un archivio dati di destinazione.
