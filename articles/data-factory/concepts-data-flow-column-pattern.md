---
title: Modelli di colonna nel flusso di dati del mapping Azure Data Factory
description: Creare modelli di trasformazione dei dati generalizzati usando modelli di colonna in Azure Data Factory il mapping di flussi di dati
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/21/2019
ms.openlocfilehash: aacec8830948e08f66d71da88897670f7ef43788
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606129"
---
# <a name="using-column-patterns-in-mapping-data-flow"></a>Utilizzo di modelli di colonna nel flusso di dati di mapping

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Diverse trasformazioni del flusso di dati di mapping consentono di fare riferimento a colonne modello basate su modelli anziché nomi di colonna hardcoded. Questa corrispondenza è nota come *modelli di colonna*. È possibile definire criteri per la corrispondenza delle colonne in base al nome, al tipo di dati, al flusso o alla posizione anziché richiedere nomi di campo esatti. Esistono due scenari in cui i modelli di colonna sono utili:

* Se i campi di origine in ingresso cambiano spesso, ad esempio se si modificano le colonne nei file di testo o nei database NoSQL. Questo scenario è noto come [Drift dello schema](concepts-data-flow-schema-drift.md).
* Se si desidera eseguire un'operazione comune su un gruppo di colonne di grandi dimensioni. Ad esempio, se si desidera eseguire il cast di ogni colonna con ' Total ' nel nome della colonna in un valore Double.

I criteri di colonna sono attualmente disponibili nelle trasformazioni Colonna derivata, aggregazione, selezione e sink.

## <a name="column-patterns-in-derived-column-and-aggregate"></a>Modelli di colonna in colonna derivata e aggregazione

Per aggiungere un modello di colonna in una colonna derivata o nella scheda Aggregazioni di una trasformazione aggregazione, fare clic sull'icona a forma di segno più a destra di una colonna esistente. Selezionare **Aggiungi modello di colonna**. 

![modelli di colonna](media/data-flow/columnpattern.png "Criteri delle colonne")

Utilizzare il [Generatore di espressioni](concepts-data-flow-expression-builder.md) per immettere la condizione di corrispondenza. Creare un'espressione booleana che corrisponda alle colonne `name`basate `type`su `stream`,, `position` e della colonna. Il criterio influirà su qualsiasi colonna, a cui viene applicata la deviazione o definita, in cui la condizione restituisce true.

Le due caselle di espressione sotto la condizione di corrispondenza specificano i nuovi nomi e i nuovi valori delle colonne interessate. Usare `$$` per fare riferimento al valore esistente del campo corrispondente. Nella casella espressione a sinistra viene definito il nome e la casella espressione a destra definisce il valore.

![modelli di colonna](media/data-flow/columnpattern2.png "Criteri delle colonne")

Il modello di colonna precedente corrisponde a ogni colonna di tipo Double e crea una colonna di aggregazione per ogni corrispondenza. Il nome della nuova colonna è il nome della colonna corrispondente concatenato con "_total". Il valore della nuova colonna è costituito dalla somma aggregata arrotondata del valore Double esistente.

Per verificare che la condizione di corrispondenza sia corretta, è possibile convalidare lo schema di output delle colonne definite nella scheda **Controlla** oppure ottenere uno snapshot dei dati nella scheda **Anteprima dati** . 

![modelli di colonna](media/data-flow/columnpattern3.png "Criteri delle colonne")

## <a name="rule-based-mapping-in-select-and-sink"></a>Mapping basato su regole in Select e sink

Quando si esegue il mapping delle colonne nell'origine e si selezionano le trasformazioni, è possibile aggiungere mapping fissi o basati su regole. Corrispondenza basata sulle colonne `name`, `type`, `stream`e `position` . È possibile avere qualsiasi combinazione di mapping corretti e basati su regole. Per impostazione predefinita, tutte le proiezioni con colonne maggiori di 50 vengono predefinite in un mapping basato su regole che corrisponde a ogni colonna e restituisce il nome inputted. 

Per aggiungere un mapping basato su regole, fare clic su **Aggiungi mapping** e selezionare **mapping basato su regole**.

![mapping basato su regole](media/data-flow/rule2.png "mapping basato su regole")

Ogni mapping basato su regole richiede due input, ovvero la condizione su cui trovare la corrispondenza e gli elementi per i quali assegnare un nome a ogni colonna mappata. Entrambi i valori vengono inseriti tramite il [Generatore di espressioni](concepts-data-flow-expression-builder.md). Nella casella espressione a sinistra immettere la condizione di corrispondenza booleana. Nella casella espressione a destra specificare l'elemento a cui verrà eseguito il mapping della colonna corrispondente.

![mapping basato su regole](media/data-flow/rule-based-mapping.png "mapping basato su regole")

Utilizzare `$$` la sintassi per fare riferimento al nome di input di una colonna corrispondente. Utilizzando come esempio l'immagine precedente, si desidera che un utente desideri trovare la corrispondenza con tutte le colonne di stringa i cui nomi sono inferiori a sei caratteri. Se una colonna in ingresso è stata `test`denominata, l' `$$ + '_short'` espressione Rinomina la colonna `test_short`. Se è l'unico mapping esistente, tutte le colonne che non soddisfano la condizione verranno eliminate dai dati restituiti.

I modelli corrispondono alle colonne derivate e definite. Per visualizzare le colonne definite di cui è stato eseguito il mapping in base a una regola, fare clic sull'icona degli occhiali accanto alla regola. Verificare l'output usando l'anteprima dei dati.

### <a name="regex-mapping"></a>Mapping Regex

Se si fa clic sull'icona con la freccia di espansione verso il basso, è possibile specificare una condizione di mapping Regex. Una condizione di mapping Regex corrisponde a tutti i nomi di colonna che corrispondono alla condizione Regex specificata. Questo può essere usato in combinazione con i mapping standard basati su regole.

![mapping basato su regole](media/data-flow/regex-matching.png "mapping basato su regole")

L'esempio precedente corrisponde a un criterio `(r)` Regex o a qualsiasi nome di colonna che contiene un r minuscolo. Analogamente al mapping basato su regole standard, tutte le colonne corrispondenti vengono modificate dalla condizione a destra usando `$$` la sintassi.

### <a name="rule-based-hierarchies"></a>Gerarchie basate su regole

Se la proiezione definita dispone di una gerarchia, è possibile utilizzare il mapping basato su regole per eseguire il mapping delle sottocolonne delle gerarchie. Specificare una condizione di corrispondenza e la colonna complessa le cui sottocolonne si desidera mappare. Ogni sottocolonna corrispondente verrà restituita utilizzando la regola "name As" specificata a destra.

![mapping basato su regole](media/data-flow/rule-based-hierarchy.png "mapping basato su regole")

L'esempio precedente corrisponde a tutte le sottocolonne della colonna `a`complessa. `a`contiene due sottocolonne `b` e `c`. Lo schema di output includerà due `b` colonne `c` e la condizione ' name As ' sarà `$$`.

## <a name="pattern-matching-expression-values"></a>Criteri di ricerca dei valori di espressione.

* `$$`viene convertito nel nome o nel valore di ogni corrispondenza in fase di esecuzione
* `name`rappresenta il nome di ogni colonna in ingresso
* `type`rappresenta il tipo di dati di ogni colonna in ingresso
* `stream`rappresenta il nome associato a ogni flusso o trasformazione nel flusso
* `position`posizione ordinale delle colonne nel flusso di dati

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sul [linguaggio delle espressioni](data-flow-expression-functions.md) del flusso di dati di mapping per le trasformazioni dei dati
* Usare i modelli di colonna nella [trasformazione sink](data-flow-sink.md) e [selezionare trasformazione](data-flow-select.md) con mapping basato su regole
