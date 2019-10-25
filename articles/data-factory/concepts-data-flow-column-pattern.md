---
title: Modelli di colonna nel flusso di dati del mapping Azure Data Factory
description: Creare modelli di trasformazione dei dati generalizzati usando modelli di colonna in Azure Data Factory il mapping di flussi di dati
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/21/2019
ms.openlocfilehash: 0c9a3c2ef05f4a11933ca7fc81c7c0f87a612293
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72789886"
---
# <a name="using-column-patterns-in-mapping-data-flow"></a>Utilizzo di modelli di colonna nel flusso di dati di mapping

Diverse trasformazioni del flusso di dati di mapping consentono di fare riferimento a colonne modello basate su modelli anziché nomi di colonna hardcoded. Questa corrispondenza è nota come *modelli di colonna*. È possibile definire criteri per la corrispondenza delle colonne in base al nome, al tipo di dati, al flusso o alla posizione anziché richiedere nomi di campo esatti. Esistono due scenari in cui i modelli di colonna sono utili:

* Se i campi di origine in ingresso cambiano spesso, ad esempio se si modificano le colonne nei file di testo o nei database NoSQL. Questo scenario è noto come [Drift dello schema](concepts-data-flow-schema-drift.md).
* Se si desidera eseguire un'operazione comune su un gruppo di colonne di grandi dimensioni. Ad esempio, se si desidera eseguire il cast di ogni colonna con ' Total ' nel nome della colonna in un valore Double.

I criteri di colonna sono attualmente disponibili nelle trasformazioni Colonna derivata, aggregazione, selezione e sink.

## <a name="column-patterns-in-derived-column-and-aggregate"></a>Modelli di colonna in colonna derivata e aggregazione

Per aggiungere un modello di colonna in una colonna derivata o nella scheda Aggregazioni di una trasformazione aggregazione, fare clic sull'icona a forma di segno più a destra di una colonna esistente. Selezionare **Aggiungi modello di colonna**. 

![modelli di colonna](media/data-flow/columnpattern.png "Criteri delle colonne")

Utilizzare il [Generatore di espressioni](concepts-data-flow-expression-builder.md) per immettere la condizione di corrispondenza. Creare un'espressione booleana che corrisponda alle colonne basate sulla `name`, `type`, `stream`e `position` della colonna. Il criterio influirà su qualsiasi colonna, a cui viene applicata la deviazione o definita, in cui la condizione restituisce true.

Le due caselle di espressione sotto la condizione di corrispondenza specificano i nuovi nomi e i nuovi valori delle colonne interessate. Usare `$$` per fare riferimento al valore esistente del campo corrispondente. Nella casella espressione a sinistra viene definito il nome e la casella espressione a destra definisce il valore.

![modelli di colonna](media/data-flow/columnpattern2.png "Criteri delle colonne")

Il modello di colonna precedente corrisponde a ogni colonna di tipo Double e crea una colonna di aggregazione per ogni corrispondenza. Il nome della nuova colonna è il nome della colonna corrispondente concatenato con "_ Total". Il valore della nuova colonna è costituito dalla somma aggregata arrotondata del valore Double esistente.

Per verificare che la condizione di corrispondenza sia corretta, è possibile convalidare lo schema di output delle colonne definite nella scheda **Controlla** oppure ottenere uno snapshot dei dati nella scheda **Anteprima dati** . 

![modelli di colonna](media/data-flow/columnpattern3.png "Criteri delle colonne")

## <a name="rule-based-mapping-in-select-and-sink"></a>Mapping basato su regole in Select e sink

Quando si esegue il mapping delle colonne nell'origine e si selezionano le trasformazioni, è possibile aggiungere mapping fissi o basati su regole. Se si conosce lo schema dei dati e si prevede che colonne specifiche del set di dati di origine corrispondano sempre a nomi statici specifici, utilizzare il mapping fisso. Se si lavora con schemi flessibili, usare il mapping basato su regole per creare una corrispondenza dei criteri basata sulla `name`, `type`, `stream`e `position` di colonne. È possibile avere qualsiasi combinazione di mapping corretti e basati su regole. 

Per aggiungere un mapping basato su regole, fare clic su **Aggiungi mapping** e selezionare **mapping basato su regole**.

![mapping basato su regole](media/data-flow/rule2.png "Mapping basato su regole")

Nella casella espressione a sinistra immettere la condizione di corrispondenza booleana. Nella casella espressione a destra specificare l'elemento a cui verrà eseguito il mapping della colonna corrispondente. Usare `$$` per fare riferimento al nome esistente del campo corrispondente.

Se si fa clic sull'icona con la freccia di espansione verso il basso, è possibile specificare una condizione di mapping Regex.

Fare clic sull'icona degli occhiali accanto a un mapping basato su regole per visualizzare quali colonne definite corrispondono e a cosa sono mappate.

![mapping basato su regole](media/data-flow/rule1.png "Mapping basato su regole")

Nell'esempio precedente vengono creati due mapping basati sulle regole. Il primo accetta tutte le colonne non denominate "Movie" e ne esegue il mapping ai valori esistenti. La seconda regola USA Regex per trovare la corrispondenza con tutte le colonne che iniziano con "Movie" e ne esegue il mapping alla colonna "movieId".

Se la regola restituisce più mapping identici, abilitare **Ignora input duplicati** o **Ignora output duplicati** per evitare duplicati.

## <a name="pattern-matching-expression-values"></a>Criteri di ricerca dei valori di espressione.

* `$$` viene convertito nel nome o nel valore di ogni corrispondenza in fase di esecuzione
* `name` rappresenta il nome di ogni colonna in ingresso
* `type` rappresenta il tipo di dati di ogni colonna in ingresso
* `stream` rappresenta il nome associato a ogni flusso o trasformazione nel flusso
* `position` è la posizione ordinale delle colonne nel flusso di dati

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sul [linguaggio delle espressioni](data-flow-expression-functions.md) del flusso di dati di mapping per le trasformazioni dei dati
* Usare i modelli di colonna nella [trasformazione sink](data-flow-sink.md) e [selezionare trasformazione](data-flow-select.md) con mapping basato su regole
