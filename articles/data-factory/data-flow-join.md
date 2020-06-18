---
title: Trasformazione tramite join nel flusso di dati per mapping
description: Combinare i dati di due origini dati usando la trasformazione tramite join nel flusso di dati per mapping di Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/15/2020
ms.openlocfilehash: f95f35fe0d17afdeec864674d3360fc3b172cad1
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683392"
---
# <a name="join-transformation-in-mapping-data-flow"></a>Trasformazione tramite join nel flusso di dati per mapping

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Usare la trasformazione tramite join per combinare i dati di due origini o flussi in un flusso di dati di mapping. Nel flusso di output saranno contenute tutte le colonne di entrambe le origini, associate in base a una condizione di join. 

## <a name="join-types"></a>Tipi di join

I flussi di dati di mapping supportano attualmente cinque tipi di join.

### <a name="inner-join"></a>Inner join

L'operazione inner join restituisce solo le righe con valori corrispondenti in entrambe le tabelle.

### <a name="left-outer"></a>Left outer join

L'operazione left outer join restituisce tutte le righe del flusso di sinistra e i record corrispondenti del flusso di destra. Se una riga del flusso di sinistra non ha corrispondenze, le colonne di output del flusso di destra vengono impostate su NULL. L'output sarà quindi costituito dalle righe restituite da un inner join, più le righe senza corrispondenza del flusso di sinistra.

> [!NOTE]
> In alcuni casi, è possibile che il motore Spark usato dai flussi di dati abbia esito negativo a causa di possibili prodotti cartesiani nelle condizioni di join. In questo caso, è possibile passare a un cross join personalizzato e inserire manualmente la condizione di join. Questo può determinare un rallentamento delle prestazioni nei flussi di dati, poiché è possibile che il motore di esecuzione debba calcolare e filtrare tutte le righe di entrambi i lati della relazione.

### <a name="right-outer"></a>Right outer join

L'operazione right outer join restituisce tutte le righe del flusso di destra e i record corrispondenti del flusso di sinistra. Se una riga del flusso di destra non ha corrispondenze, le colonne di output del flusso di sinistra vengono impostate su NULL. L'output sarà quindi costituito dalle righe restituite da un inner join, più le righe senza corrispondenza del flusso di destra.

### <a name="full-outer"></a>Full outer join

L'operazione full outer join restituisce tutte le colonne e le righe di entrambi i lati con valori NULL relativi alle colonne che non hanno corrispondenze.

### <a name="custom-cross-join"></a>Cross join personalizzato

L'operazione cross join restituisce il prodotto incrociato dei due flussi in base a una condizione. Se si usa una condizione diversa dall'uguaglianza, specificare un'espressione personalizzata come condizione di cross join. Il flusso di output sarà costituito da tutte le righe che soddisfano la condizione di join.

È possibile usare questo tipo di join per i join non uguali e le condizioni ```OR```.

Se si vuole produrre esplicitamente un prodotto cartesiano completo, usare la trasformazione Colonna derivata in ognuno dei due flussi indipendenti prima del join, in modo da creare una chiave sintetica per la corrispondenza. Creare, ad esempio, una nuova colonna in Colonna derivata nell'ambito di ogni flusso denominato ```SyntheticKey``` e impostarla uguale a ```1```. Usare quindi ```a.SyntheticKey == b.SyntheticKey``` come espressione di join personalizzata.

> [!NOTE]
> Assicurarsi di includere nel cross join personalizzato almeno una colonna di ogni lato della relazione sinistra e di quella destra. L'esecuzione di cross join con valori statici anziché con colonne di entrambi i lati produce analisi complete dell'intero set di dati, con una conseguente riduzione delle prestazioni del flusso di dati.

## <a name="configuration"></a>Configurazione

1. Nell'elenco a discesa **Right stream** (Flusso destro) scegliere il flusso di dati di cui si vuole eseguire il join.
1. Selezionare il **Tipo di join**
1. Scegliere le colonne chiave per le quali si vuole trovare una corrispondenza con la condizione di join. Per impostazione predefinita, il flusso di dati cerca l'uguaglianza tra una colonna per ogni flusso. Per eseguire il confronto tramite un valore calcolato, passare il puntatore del mouse sull'elenco a discesa della colonna e selezionare **Colonna calcolata**.

![Trasformazione tramite join](media/data-flow/join.png "Join")

### <a name="non-equi-joins"></a>Join non uguali

Per usare un operatore condizionale come non uguale (! =) o maggiore di (>) nelle condizioni di join, modificare l'elenco a discesa operatore tra le due colonne. Per i join non uguali è necessario che almeno uno dei due flussi venga trasmesso usando la broadcast **fissa** nella scheda **Ottimizza**.

![Join non uguali](media/data-flow/non-equi-join.png "Join non uguali")

## <a name="optimizing-join-performance"></a>Ottimizzazione delle prestazioni di join

A differenza di Merge join in strumenti come SSIS, la trasformazione tramite join non rappresenta un'operazione di merge obbligatoria. Le chiavi di join non richiedono l'ordinamento. L'operazione di join viene eseguita in base all'operazione di join ottimale in Spark, ovvero join Broadcast o lato mappa.

![Ottimizzazione della trasformazione tramite join](media/data-flow/joinoptimize.png "Ottimizzazione dei join")

Nelle trasformazioni tramite join, Ricerche ed Esiste, se uno o entrambi i flussi di dati rientrano nella memoria del nodo di lavoro, è possibile ottimizzare le prestazioni abilitando la **trasmissione**. Per impostazione predefinita, il motore Spark deciderà automaticamente se trasmettere o meno un lato. Per scegliere manualmente il lato da trasmettere, selezionare **Fisso**.

Non è consigliabile disabilitare la trasmissione tramite l'opzione **Off** a meno che i join non siano in errore di timeout.

## <a name="self-join"></a>Self-join

Per eseguire il self-join di un flusso di dati con se stesso, creare un alias di un flusso esistente con una trasformazione Select. Creare un nuovo ramo facendo clic sull'icona a forma di più accanto a una trasformazione e selezionando **Nuovo ramo**. Aggiungere una trasformazione SELECT per creare un alias del flusso originale. Aggiungere una trasformazione join e scegliere il flusso originale come **flusso di sinistra** e "trasformazione" come **flusso di destra**.

![Self-join](media/data-flow/selfjoin.png "Self-join")

## <a name="testing-join-conditions"></a>Test delle condizioni di join

Quando si testa una trasformazione tramite join con anteprima dati in modalità di debug, usare un piccolo set di dati noti. Quando si campionano righe da un set di dati di grandi dimensioni, non è possibile prevedere quali righe e chiavi verranno lette per il test. Il risultato è non deterministico, vale a dire che le condizioni di join potrebbero non restituire corrispondenze.

## <a name="data-flow-script"></a>Script del flusso di dati

### <a name="syntax"></a>Sintassi

```
<leftStream>, <rightStream>
    join(
        <conditionalExpression>,
        joinType: { 'inner'> | 'outer' | 'left_outer' | 'right_outer' | 'cross' }
        broadcast: { 'auto' | 'left' | 'right' | 'both' | 'off' }
    ) ~> <joinTransformationName>
```

### <a name="inner-join-example"></a>Esempio di Inner join

Nell'esempio seguente viene illustrata una trasformazione join denominata `JoinMatchedData` che accetta il flusso sinistro `TripData` e il flusso destro `TripFare`.  La condizione di join è l'espressione `hack_license == { hack_license} && TripData@medallion == TripFare@medallion && vendor_id == { vendor_id} && pickup_datetime == { pickup_datetime}` che restituisce true se esiste una corrispondenza tra le colonne `hack_license`, `medallion`, `vendor_id` e `pickup_datetime` di ogni flusso. Il `joinType` è `'inner'`. La trasmissione verrà abilitata solo nel flusso sinistro e `broadcast` avrà quindi il valore `'left'`.

In Data Factory UX questa trasformazione è simile all'immagine seguente:

![Esempio di join](media/data-flow/join-script1.png "esempio di join")

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

### <a name="custom-cross-join-example"></a>Esempio di cross join personalizzato

Nell'esempio seguente viene illustrata una trasformazione join denominata `JoiningColumns` che accetta il flusso sinistro `LeftStream` e il flusso destro `RightStream`. Questa trasformazione accetta due flussi e unisce tutte le righe in cui la colonna `leftstreamcolumn` è maggiore della colonna `rightstreamcolumn`. Il `joinType` è `cross`. La trasmissione non è abilitata e `broadcast` avrà quindi il valore `'none'`.

In Data Factory UX questa trasformazione è simile all'immagine seguente:

![Esempio di join](media/data-flow/join-script2.png "esempio di join")

Lo script del flusso di dati per questa trasformazione si trova nel frammento di codice seguente:

```
LeftStream, RightStream
    join(
        leftstreamcolumn > rightstreamcolumn,
        joinType:'cross',
        broadcast: 'none'
    )~> JoiningColumns
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver unito i dati, creare un [colonna derivata](data-flow-derived-column.md) ed effettuare il [sink](data-flow-sink.md) dei dati a un archivio dati di destinazione.
