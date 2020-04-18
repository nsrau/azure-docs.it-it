---
title: Modelli di colonna nel flusso di dati di mapping di Azure Data FactoryColumn patterns in Azure Data Factory mapping data flow
description: Creare modelli di trasformazione dei dati generalizzati usando modelli di colonna nei flussi di dati di mapping di Azure Data FactoryCreate generalized data transformation patterns using column patterns in Azure Data Factory mapping data flows
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/21/2019
ms.openlocfilehash: aacec8830948e08f66d71da88897670f7ef43788
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606129"
---
# <a name="using-column-patterns-in-mapping-data-flow"></a>Utilizzo di modelli di colonna nel mapping del flusso di datiUsing column patterns in mapping data flow

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Diverse trasformazioni del flusso di dati di mapping consentono di fare riferimento a colonne modello in base a modelli anziché a nomi di colonna hardcoded. Questa corrispondenza è nota come modelli di *colonna*. È possibile definire modelli che corrispondono alle colonne in base al nome, al tipo di dati, al flusso o alla posizione anziché richiedere nomi di campo esatti. Esistono due scenari in cui i modelli di colonna sono utili:There are two scenarios where column patterns are useful:

* Se i campi di origine in ingresso cambiano spesso, ad esempio nel caso di modifica delle colonne in file di testo o database NoSQL. Questo scenario è noto come [deriva dello schema](concepts-data-flow-schema-drift.md).
* Se si desidera eseguire un'operazione comune su un gruppo di colonne di grandi dimensioni. Ad esempio, desidera eseguire il cast di ogni colonna con il nome della colonna 'totale' in un valore double.

I modelli di colonna sono attualmente disponibili nelle trasformazioni di colonna, aggregazione, selezione e sink derivati.

## <a name="column-patterns-in-derived-column-and-aggregate"></a>Modelli di colonna nella colonna derivata e nell'aggregazioneColumn patterns in derived column and aggregate

Per aggiungere un modello di colonna in una colonna derivata o nella scheda Aggregazioni di una trasformazione di aggregazione, fare clic sull'icona più a destra di una colonna esistente. Selezionare **Aggiungi modello di colonna**. 

![modelli di colonna](media/data-flow/columnpattern.png "Criteri delle colonne")

Utilizzare il [generatore di espressioni](concepts-data-flow-expression-builder.md) per immettere la condizione di corrispondenza. Creare un'espressione booleana che `name` `type`corrisponda alle colonne in base a , , `stream`e `position` della colonna. Il modello avrà effetto su qualsiasi colonna, derapata o definita, in cui la condizione restituisce true.

Le due caselle di espressione sotto la condizione di corrispondenza specificano i nuovi nomi e i nuovi valori delle colonne interessate. Consente `$$` di fare riferimento al valore esistente del campo corrispondente. La casella di espressione a sinistra definisce il nome e la casella di espressione di destra definisce il valore.

![modelli di colonna](media/data-flow/columnpattern2.png "Criteri delle colonne")

Il modello di colonna precedente corrisponde a ogni colonna di tipo double e crea una colonna di aggregazione per corrispondenza. Il nome della nuova colonna è il nome della colonna corrispondente concatenato a '_total'. Il valore della nuova colonna è la somma arrotondata e aggregata del valore double esistente.

Per verificare che la condizione corrispondente sia corretta, è possibile convalidare lo schema di output delle colonne definite nella scheda **Controlla** o ottenere uno snapshot dei dati nella scheda **Anteprima dati.** 

![modelli di colonna](media/data-flow/columnpattern3.png "Criteri delle colonne")

## <a name="rule-based-mapping-in-select-and-sink"></a>Mapping basato su regole in Select e sink

Quando si esegue il mapping di colonne nell'origine e si selezionano trasformazioni, è possibile aggiungere mapping fissi o basati su regole. Trova la `name`corrispondenza `stream`in `position` base alle colonne , `type`, e . È possibile avere qualsiasi combinazione di mapping fissi e basati su regole. Per impostazione predefinita, per tutte le proiezioni con più di 50 colonne verrà associato per impostazione predefinita un mapping basato su regole che corrisponde a ogni colonna e restituisce il nome immesso. 

Per aggiungere un mapping basato su regole, fare clic su **Aggiungi mapping** e selezionare **Mapping basato su regole**.

![mapping basato su regole](media/data-flow/rule2.png "mapping basato su regole")

Ogni mapping basato su regole richiede due input: la condizione in base alla quale soddisfare e cosa denominare ogni colonna mappata. Entrambi i valori vengono immessi tramite il [generatore di espressioni.](concepts-data-flow-expression-builder.md) Nella casella dell'espressione a sinistra immettere la condizione di corrispondenza booleana. Nella casella di espressione a destra specificare a cosa verrà eseguito il mapping della colonna corrispondente.

![mapping basato su regole](media/data-flow/rule-based-mapping.png "mapping basato su regole")

Utilizzare `$$` la sintassi per fare riferimento al nome di input di una colonna corrispondente. Utilizzando l'immagine precedente come esempio, si supponga che un utente desideri trovare una corrispondenza in tutte le colonne stringa i cui nomi sono più brevi di sei caratteri. Se una colonna `test`in arrivo `$$ + '_short'` è denominata , l'espressione rinominerà la colonna `test_short`. Se questo è l'unico mapping esistente, tutte le colonne che non soddisfano la condizione verranno eliminate dai dati restituiti.

I modelli corrispondono sia alle colonne alla deriva che a quelle definite. Per visualizzare le colonne definite mappate da una regola, fare clic sull'icona degli occhiali accanto alla regola. Verificare l'output utilizzando l'anteprima dei dati.

### <a name="regex-mapping"></a>Mapping Regex

Se si fa clic sull'icona con la freccia rivolta verso il basso, è possibile specificare una condizione di mapping delle regex. Una condizione di mapping delle regole di espressione regolare corrisponde a tutti i nomi di colonna che corrispondono alla condizione di espressione regolare specificata. Può essere utilizzato in combinazione con mapping standard basati su regole.

![mapping basato su regole](media/data-flow/regex-matching.png "mapping basato su regole")

L'esempio precedente corrisponde `(r)` al modello regex o a qualsiasi nome di colonna che contiene una r minuscola. Analogamente al mapping standard basato su regole, tutte le `$$` colonne corrispondenti vengono modificate dalla condizione a destra utilizzando la sintassi.

### <a name="rule-based-hierarchies"></a>Gerarchie basate su regole

Se la proiezione definita ha una gerarchia, è possibile utilizzare il mapping basato su regole per eseguire il mapping delle sottocolonne delle gerarchie. Specificare una condizione corrispondente e la colonna complessa di cui si desidera mappare le sottocolonne. Ogni sottocolonna corrispondente verrà emessa utilizzando la regola 'Nome come' specificata a destra.

![mapping basato su regole](media/data-flow/rule-based-hierarchy.png "mapping basato su regole")

Nell'esempio precedente viene confrontato `a`con tutte le sottocolonne di column complex . `a`contiene due `b` sottocolonne e `c`. Lo schema di output `b` `c` includerà due colonne e `$$`come la condizione 'Nome come' è .

## <a name="pattern-matching-expression-values"></a>Valori delle espressioni di corrispondenza dei modelli.

* `$$`si traduce nel nome o nel valore di ogni corrispondenza in fase di esecuzione
* `name`rappresenta il nome di ogni colonna in ingresso
* `type`rappresenta il tipo di dati di ogni colonna in entrata
* `stream`rappresenta il nome associato a ogni flusso o la trasformazione nel flusso
* `position`è la posizione ordinale delle colonne nel flusso di dati

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sul [linguaggio](data-flow-expression-functions.md) delle espressioni del flusso di dati di mapping per le trasformazioni dei datiLearn more about the mapping data flow expression language for data transformations
* Usare i modelli di colonna nella [trasformazione sink](data-flow-sink.md) e selezionare la [trasformazione](data-flow-select.md) con il mapping basato su regoleUse column patterns in the sink transformation and select transformation with rule-based mapping
