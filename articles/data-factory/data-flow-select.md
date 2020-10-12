---
title: Selezione trasformazione nel flusso di dati di mapping
description: Trasformazione Selezione flusso di dati mapping di Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/02/2020
ms.openlocfilehash: 70e0a95a85920562af8bf9d3fffa6633709dccc5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84322091"
---
# <a name="select-transformation-in-mapping-data-flow"></a>Selezione trasformazione nel flusso di dati di mapping

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Utilizzare la trasformazione seleziona per rinominare, eliminare o riordinare le colonne. Questa trasformazione non modifica i dati di riga, ma sceglie le colonne che vengono propagate a valle. 

In una trasformazione selezione, gli utenti possono specificare i mapping fissi, utilizzare i modelli per eseguire il mapping basato sulle regole o abilitare il mapping automatico. È possibile utilizzare entrambi i mapping corretti e basati su regole all'interno della stessa trasformazione Select. Se una colonna non corrisponde a uno dei mapping definiti, verrà eliminata.

## <a name="fixed-mapping"></a>Mapping fisso

Se nella proiezione sono definite meno di 50 colonne, per impostazione predefinita tutte le colonne definite avranno un mapping fisso. Un mapping fisso accetta una colonna definita in ingresso e ne esegue il mapping con un nome esatto.

![Mapping fisso](media/data-flow/fixedmapping.png "Mapping fisso")

> [!NOTE]
> Non è possibile eseguire il mapping o rinominare una colonna spostata utilizzando un mapping fisso

### <a name="mapping-hierarchical-columns"></a>Mapping di colonne gerarchiche

I mapping corretti possono essere utilizzati per eseguire il mapping di una sottocolonna di una colonna gerarchica a una colonna di livello superiore. Se si dispone di una gerarchia definita, utilizzare l'elenco a discesa colonna per selezionare una sottocolonna. La trasformazione selezione creerà una nuova colonna con il valore e il tipo di dati della sottocolonna.

![mapping gerarchico](media/data-flow/select-hierarchy.png "mapping gerarchico")

## <a name="rule-based-mapping"></a>mapping basato su regole


> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4xiXz]

Se si desidera eseguire il mapping di più colonne in una sola volta o passare a downstream le colonne, utilizzare il mapping basato su regole per definire i mapping utilizzando i modelli di colonna. Corrispondenza basata sulle `name` colonne, `type` , `stream` e `position` . È possibile avere qualsiasi combinazione di mapping corretti e basati su regole. Per impostazione predefinita, tutte le proiezioni con colonne maggiori di 50 vengono predefinite in un mapping basato su regole che corrisponde a ogni colonna e restituisce il nome inputted. 

Per aggiungere un mapping basato su regole, fare clic su **Aggiungi mapping** e selezionare **mapping basato su regole**.

![mapping basato su regole](media/data-flow/rule2.png "mapping basato su regole")

Ogni mapping basato su regole richiede due input, ovvero la condizione su cui trovare la corrispondenza e gli elementi per i quali assegnare un nome a ogni colonna mappata. Entrambi i valori vengono inseriti tramite il [Generatore di espressioni](concepts-data-flow-expression-builder.md). Nella casella espressione a sinistra immettere la condizione di corrispondenza booleana. Nella casella espressione a destra specificare l'elemento a cui verrà eseguito il mapping della colonna corrispondente.

![mapping basato su regole](media/data-flow/rule-based-mapping.png "mapping basato su regole")

Utilizzare `$$` la sintassi per fare riferimento al nome di input di una colonna corrispondente. Utilizzando come esempio l'immagine precedente, si desidera che un utente desideri trovare la corrispondenza con tutte le colonne di stringa i cui nomi sono inferiori a sei caratteri. Se una colonna in ingresso è stata denominata `test` , l'espressione `$$ + '_short'` Rinomina la colonna `test_short` . Se è l'unico mapping esistente, tutte le colonne che non soddisfano la condizione verranno eliminate dai dati restituiti.

I modelli corrispondono alle colonne derivate e definite. Per visualizzare le colonne definite di cui è stato eseguito il mapping in base a una regola, fare clic sull'icona degli occhiali accanto alla regola. Verificare l'output usando l'anteprima dei dati.

### <a name="regex-mapping"></a>Mapping Regex

Se si fa clic sull'icona con la freccia di espansione verso il basso, è possibile specificare una condizione di mapping Regex. Una condizione di mapping Regex corrisponde a tutti i nomi di colonna che corrispondono alla condizione Regex specificata. Questo può essere usato in combinazione con i mapping standard basati su regole.

![mapping basato su regole](media/data-flow/regex-matching.png "mapping basato su regole")

L'esempio precedente corrisponde a un criterio Regex `(r)` o a qualsiasi nome di colonna che contiene un r minuscolo. Analogamente al mapping basato su regole standard, tutte le colonne corrispondenti vengono modificate dalla condizione a destra usando la `$$` sintassi.

Se nel nome della colonna sono presenti più corrispondenze Regex, è possibile fare riferimento a corrispondenze specifiche usando `$n` Where ' n'indica la corrispondenza. Ad esempio, "$2" si riferisce alla seconda corrispondenza all'interno di un nome di colonna.

### <a name="rule-based-hierarchies"></a>Gerarchie basate su regole

Se la proiezione definita dispone di una gerarchia, è possibile utilizzare il mapping basato su regole per eseguire il mapping delle sottocolonne delle gerarchie. Specificare una condizione di corrispondenza e la colonna complessa le cui sottocolonne si desidera mappare. Ogni sottocolonna corrispondente verrà restituita utilizzando la regola "name As" specificata a destra.

![mapping basato su regole](media/data-flow/rule-based-hierarchy.png "mapping basato su regole")

L'esempio precedente corrisponde a tutte le sottocolonne della colonna complessa `a` . `a` contiene due sottocolonne `b` e `c` . Lo schema di output includerà due colonne `b` e `c` la condizione ' name As ' sarà `$$` .

### <a name="parameterization"></a>Parametrizzazione

È possibile parametrizzare i nomi delle colonne utilizzando il mapping basato su regole. Usare la parola chiave ```name``` per trovare la corrispondenza con i nomi di colonna in ingresso rispetto a un parametro. Se, ad esempio, si dispone di un parametro del flusso di dati ```mycolumn``` , è possibile creare una regola che corrisponde a qualsiasi nome di colonna uguale a ```mycolumn``` . È possibile rinominare la colonna corrispondente in una stringa hardcoded come ' business Key ' e farvi riferimento in modo esplicito. In questo esempio la condizione di corrispondenza è ```name == $mycolumn``` e la condizione del nome è "business Key". 

## <a name="auto-mapping"></a>Mapping automatico

Quando si aggiunge una trasformazione Select, è possibile abilitare il **mapping automatico** cambiando il dispositivo di scorrimento mapping automatico. Con il mapping automatico, la trasformazione SELECT esegue il mapping di tutte le colonne in ingresso, esclusi i duplicati, con lo stesso nome dell'input. In questo modo, le colonne vengono riportate alla deriva, ovvero i dati di output possono contenere colonne che non sono definite nello schema. Per ulteriori informazioni sulle colonne di cui è stato visualizzato il Drift, vedere [schema Drift](concepts-data-flow-schema-drift.md).

![Mapping automatico](media/data-flow/automap.png "Mapping automatico")

Con il mapping automatico in, la trasformazione Select rispetta le impostazioni Skip duplicate e fornisce un nuovo alias per le colonne esistenti. L'aliasing è utile quando si esegue più join o ricerche nello stesso flusso e in scenari self-join. 

## <a name="duplicate-columns"></a>Colonne duplicate

Per impostazione predefinita, la trasformazione Select Elimina le colonne duplicate nella proiezione di input e di output. Le colonne di input duplicate spesso derivano da trasformazioni join e di ricerca, in cui i nomi delle colonne vengono duplicati a ogni lato del join. Le colonne di output duplicate possono verificarsi se si esegue il mapping di due colonne di input diverse con lo stesso nome. Scegliere se eliminare o passare colonne duplicate attivando la casella di controllo.

![Ignora duplicati](media/data-flow/select-skip-dup.png "Ignora duplicati")

## <a name="ordering-of-columns"></a>Ordinamento delle colonne

L'ordine dei mapping determina l'ordine delle colonne di output. Se viene eseguito il mapping di una colonna di input più volte, verrà rispettato solo il primo mapping. Per tutte le colonne duplicate eliminate, la prima corrispondenza verrà mantenuta.

## <a name="data-flow-script"></a>Script del flusso di dati

### <a name="syntax"></a>Sintassi

```
<incomingStream>
    select(mapColumn(
        each(<hierarchicalColumn>, match(<matchCondition>), <nameCondition> = $$), ## hierarchical rule-based matching
        <fixedColumn>, ## fixed mapping, no rename
        <renamedFixedColumn> = <fixedColumn>, ## fixed mapping, rename
        each(match(<matchCondition>), <nameCondition> = $$), ## rule-based mapping
        each(patternMatch(<regexMatching>), <nameCondition> = $$) ## regex mapping
    ),
    skipDuplicateMapInputs: { true | false },
    skipDuplicateMapOutputs: { true | false }) ~> <selectTransformationName>
```

### <a name="example"></a>Esempio

Di seguito è riportato un esempio di un mapping Select e del relativo script del flusso di dati:

![Esempio di script Select](media/data-flow/select-script-example.png "Esempio di script Select")

```
DerivedColumn1 select(mapColumn(
        each(a, match(true())),
        movie,
        title1 = title,
        each(match(name == 'Rating')),
        each(patternMatch(`(y)`),
            $1 + 'regex' = $$)
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> Select1
```

## <a name="next-steps"></a>Passaggi successivi
* Dopo aver utilizzato SELECT per rinominare, riordinare e utilizzare le colonne alias, utilizzare la [trasformazione sink](data-flow-sink.md) per inserire i dati in un archivio dati.
