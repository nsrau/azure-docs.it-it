---
title: Trasformazione di ricerca nel mapping del flusso di datiLookup transformation in mapping data flow
description: Fare riferimento ai dati da un'altra origine usando la trasformazione di ricerca nel mapping del flusso di dati.
author: kromerm
ms.reviewer: daperlov
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/23/2020
ms.openlocfilehash: 24fe11610d2a91fcdb0f09b8e45ea6ff4b81bd70
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606388"
---
# <a name="lookup-transformation-in-mapping-data-flow"></a>Trasformazione di ricerca nel mapping del flusso di datiLookup transformation in mapping data flow

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Usare la trasformazione di ricerca per fare riferimento ai dati da un'altra origine in un flusso di dati. La trasformazione di ricerca aggiunge le colonne dai dati corrispondenti ai dati di origine.

Una trasformazione di ricerca è simile a un left outer join. Tutte le righe del flusso primario saranno presenti nel flusso di output con colonne aggiuntive dal flusso di ricerca. 

## <a name="configuration"></a>Configurazione

![Trasformazione Ricerca](media/data-flow/lookup1.png "Ricerca")

**Flusso principale:** Flusso di dati in ingresso. Questo flusso è equivalente al lato sinistro di un join.

**Flusso di ricerca:** Dati aggiunti al flusso primario. I dati aggiunti sono determinati dalle condizioni di ricerca. Questo flusso è equivalente al lato destro di un join.

**Corrispondenza di più righe:** Se abilitata, una riga con più corrispondenze nel flusso primario restituirà più righe. In caso contrario, verrà restituita una sola riga in base alla condizione 'Match on'.

**Corrispondenza su:** Visibile solo se 'Corrispondenza più righe' è abilitato. Scegliere se eseguire la corrispondenza in qualsiasi riga, prima corrispondenza o ultima corrispondenza. Qualsiasi riga è consigliata in quanto viene eseguita più velocemente. Se è selezionata la prima riga o l'ultima riga, sarà necessario specificare le condizioni di ordinamento.

**Condizioni di ricerca:** Scegliere le colonne in cui trovare la corrispondenza. Se viene soddisfatta la condizione di uguaglianza, le righe verranno considerate una corrispondenza. Passare il mouse e selezionare 'Colonna calcolata' per estrarre un valore utilizzando il linguaggio delle espressioni del flusso di [dati.](data-flow-expression-functions.md)

La trasformazione di ricerca supporta solo le corrispondenze di uguaglianza. Per personalizzare l'espressione di ricerca in modo da includere altri operatori, ad esempio maggiore di, è consigliabile utilizzare un [cross join nella trasformazione join.](data-flow-join.md#custom-cross-join) Un cross join eviterà eventuali errori di prodotto cartesiano durante l'esecuzione.

Tutte le colonne di entrambi i flussi sono incluse nei dati di output. Per eliminare le colonne duplicate o indesiderate, aggiungere una [trasformazione di selezione](data-flow-select.md) dopo la trasformazione di ricerca. Le colonne possono anche essere eliminate o rinominate in una trasformazione sink.

## <a name="analyzing-matched-rows"></a>Analisi delle righe corrispondentiAnalyzing matched rows

Dopo la trasformazione `isMatch()` di ricerca, la funzione può essere utilizzata per verificare se la ricerca corrisponde per le singole righe.

![Modello di ricerca](media/data-flow/lookup111.png "Modello di ricerca")

Un esempio di questo modello è l'utilizzo `isMatch()` della trasformazione di divisione condizionale per la divisione della funzione. Nell'esempio precedente, le righe corrispondenti passano attraverso il ```NoMatch``` flusso superiore e le righe non corrispondenti passano attraverso il flusso.

## <a name="testing-lookup-conditions"></a>Test delle condizioni di ricerca

Quando si esegue il test della trasformazione di ricerca con l'anteprima dei dati in modalità di debug, usare un piccolo set di dati noti. Quando si esegue il campionamento di righe da un set di dati di grandi dimensioni, non è possibile prevedere quali righe e chiavi verranno lette per il test. Il risultato è non deterministico, il che significa che le condizioni di join potrebbero non restituire alcuna corrispondenza.

## <a name="broadcast-optimization"></a>Ottimizzazione della trasmissione

In Azure Data Factory i flussi di dati di mapping vengono eseguiti in ambienti Spark con scalabilità orizzontale. Se il set di dati può essere inserito nello spazio di memoria del nodo worker, le prestazioni di ricerca possono essere ottimizzate abilitando la trasmissione.

![Trasmissione join](media/data-flow/broadcast.png "Trasmissione join")

L'abilitazione della trasmissione spinge l'intero set di dati in memoria. Per i set di dati più piccoli contenenti solo poche migliaia di righe, la trasmissione può migliorare notevolmente le prestazioni di ricerca. Per set di dati di grandi dimensioni, questa opzione può causare un'eccezione di memoria insufficiente.

## <a name="data-flow-script"></a>Script del flusso di dati

### <a name="syntax"></a>Sintassi

```
<leftStream>, <rightStream>
    lookup(
        <lookupConditionExpression>,
        multiple: { true | false },
        pickup: { 'first' | 'last' | 'any' },  ## Only required if false is selected for multiple
        { desc | asc }( <sortColumn>, { true | false }), ## Only required if 'first' or 'last' is selected. true/false determines whether to put nulls first
        broadcast: { 'none' | 'left' | 'right' | 'both' }
    ) ~> <lookupTransformationName>
```
### <a name="example"></a>Esempio

![Trasformazione Ricerca](media/data-flow/lookup-dsl-example.png "Ricerca")

Lo script del flusso di dati per la configurazione di ricerca precedente si trova nel frammento di codice seguente.

```
SQLProducts, DimProd lookup(ProductID == ProductKey,
    multiple: false,
    pickup: 'first',
    asc(ProductKey, true),
    broadcast: 'none')~> LookupKeys
```
## 
Passaggi successivi

* Le trasformazioni [join](data-flow-join.md) ed [exists](data-flow-exists.md) accettano entrambi più input di flusso
* Usare una [trasformazione di divisione condizionale](data-flow-conditional-split.md) con ```isMatch()``` per dividere le righe su valori corrispondenti e non corrispondenti
