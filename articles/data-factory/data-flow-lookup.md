---
title: Trasformazione Ricerca nel flusso di dati di mapping
description: Riferimento ai dati di un'altra origine utilizzando la trasformazione Ricerca nel flusso di dati di mapping.
author: kromerm
ms.reviewer: daperlov
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/23/2020
ms.openlocfilehash: af4e33e2653aebe5d1c979aa314463e4beb7b0d7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82233398"
---
# <a name="lookup-transformation-in-mapping-data-flow"></a>Trasformazione Ricerca nel flusso di dati di mapping

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Utilizzare la trasformazione Ricerca per fare riferimento ai dati di un'altra origine in un flusso del flusso di dati. La trasformazione Ricerca Accoda le colonne dai dati corrispondenti ai dati di origine.

Una trasformazione ricerca è simile a una left outer join. Tutte le righe del flusso primario sono presenti nel flusso di output con colonne aggiuntive del flusso di ricerca. 

## <a name="configuration"></a>Configurazione

![Trasformazione Ricerca](media/data-flow/lookup1.png "Ricerca")

**Flusso primario:** Flusso di dati in ingresso. Questo flusso è equivalente al lato sinistro di un join.

**Flusso di ricerca:** Dati aggiunti al flusso primario. I dati aggiunti vengono determinati in base alle condizioni di ricerca. Questo flusso è equivalente al lato destro di un join.

**Corrisponde a più righe:** Se abilitata, una riga con più corrispondenze nel flusso primario restituirà più righe. In caso contrario, verrà restituita una sola riga in base alla condizione ' match on '.

**Corrispondenza in:** Visibile solo se è abilitata la corrispondenza di più righe. Scegliere se trovare la corrispondenza con qualsiasi riga, la prima corrispondenza o l'ultima corrispondenza. Ogni riga è consigliata perché esegue il più velocemente. Se la prima riga o l'ultima riga è selezionata, sarà necessario specificare le condizioni di ordinamento.

**Condizioni di ricerca:** Consente di scegliere le colonne su cui eseguire la corrispondenza. Se viene soddisfatta la condizione di uguaglianza, le righe verranno considerate corrispondenti. Passare il puntatore del mouse e selezionare "calcolata colonna" per estrarre un valore utilizzando il [linguaggio delle espressioni del flusso di dati](data-flow-expression-functions.md).

La trasformazione ricerca supporta solo corrispondenze di uguaglianza. Per personalizzare l'espressione di ricerca in modo da includere altri operatori, ad esempio maggiore di, è consigliabile utilizzare un [cross join nella trasformazione join](data-flow-join.md#custom-cross-join). Un cross join eviterà tutti i possibili errori di prodotto cartesiano durante l'esecuzione.

Tutte le colonne di entrambi i flussi sono incluse nei dati di output. Per eliminare colonne duplicate o indesiderate, aggiungere una [trasformazione Select](data-flow-select.md) dopo la trasformazione ricerca. Le colonne possono anche essere eliminate o rinominate in una trasformazione sink.

## <a name="analyzing-matched-rows"></a>Analisi delle righe corrispondenti

Dopo la trasformazione ricerca, la funzione `isMatch()` può essere utilizzata per verificare se la ricerca corrisponde a singole righe.

![Modello di ricerca](media/data-flow/lookup111.png "Modello di ricerca")

Un esempio di questo modello consiste nell'utilizzare la trasformazione Suddivisione condizionale per suddividere la `isMatch()` funzione. Nell'esempio precedente, le righe corrispondenti passano attraverso il flusso superiore e le righe non corrispondenti passano attraverso il ```NoMatch``` flusso.

## <a name="testing-lookup-conditions"></a>Test delle condizioni di ricerca

Quando si testa la trasformazione Ricerca con Anteprima dati in modalità di debug, utilizzare un piccolo set di dati noti. Quando si campionano righe da un set di dati di grandi dimensioni, non è possibile prevedere quali righe e chiavi verranno lette per il test. Il risultato è non deterministico, vale a dire che le condizioni di join non possono restituire corrispondenze.

## <a name="broadcast-optimization"></a>Ottimizzazione broadcast

![Join broadcast](media/data-flow/broadcast.png "Join broadcast")

In join, ricerche ed esiste una trasformazione, se uno o entrambi i flussi di dati rientrano nella memoria del nodo di lavoro, è possibile ottimizzare le prestazioni abilitando la **trasmissione**. Per impostazione predefinita, il motore Spark deciderà automaticamente se trasmettere o meno un lato. Per scegliere manualmente il lato da trasmettere, selezionare **fisso**.

Non è consigliabile disabilitare la trasmissione tramite l'opzione **off** a meno che i join non siano in errore di timeout.

## <a name="data-flow-script"></a>Script del flusso di dati

### <a name="syntax"></a>Sintassi

```
<leftStream>, <rightStream>
    lookup(
        <lookupConditionExpression>,
        multiple: { true | false },
        pickup: { 'first' | 'last' | 'any' },  ## Only required if false is selected for multiple
        { desc | asc }( <sortColumn>, { true | false }), ## Only required if 'first' or 'last' is selected. true/false determines whether to put nulls first
        broadcast: { 'auto' | 'left' | 'right' | 'both' | 'off' }
    ) ~> <lookupTransformationName>
```
### <a name="example"></a>Esempio

![Trasformazione Ricerca](media/data-flow/lookup-dsl-example.png "Ricerca")

Lo script del flusso di dati per la configurazione di ricerca precedente si trova nel frammento di codice riportato di seguito.

```
SQLProducts, DimProd lookup(ProductID == ProductKey,
    multiple: false,
    pickup: 'first',
    asc(ProductKey, true),
    broadcast: 'auto')~> LookupKeys
```
## 
Passaggi successivi

* Le trasformazioni [join](data-flow-join.md) e [Exists](data-flow-exists.md) accettano entrambi più input di flusso
* Usare una [trasformazione Suddivisione condizionale](data-flow-conditional-split.md) con ```isMatch()``` per suddividere le righe in valori corrispondenti e non corrispondenti
