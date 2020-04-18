---
title: Mapping del flusso di dati Seleziona trasformazione
description: Azure Data Factory mapping data flow Select Transformation
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/18/2020
ms.openlocfilehash: 71a7c812b71e67c7b729844d0672e520c88e2d08
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606339"
---
# <a name="select-transformation-in-mapping-data-flow"></a>Selezionare la trasformazione nel mapping del flusso di datiSelect transformation in mapping data flow

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Utilizzare la trasformazione di selezione per rinominare, eliminare o riordinare le colonne. Questa trasformazione non altera i dati di riga, ma sceglie le colonne da propagare a valle. 

In una trasformazione di selezione, gli utenti possono specificare mapping fissi, utilizzare modelli per eseguire il mapping basato su regole o abilitare il mapping automatico. I mapping fissi e basati su regole possono essere utilizzati entrambi all'interno della stessa trasformazione di selezione. Se una colonna non corrisponde a uno dei mapping definiti, verrà eliminata.

## <a name="fixed-mapping"></a>Mappatura fissa

Se nella proiezione sono presenti meno di 50 colonne definite, tutte le colonne definite avranno un mapping fisso per impostazione predefinita. Un mapping fisso accetta una colonna definita in ingresso e le associa un nome esatto.

![Mappatura fissa](media/data-flow/fixedmapping.png "Mappatura fissa")

> [!NOTE]
> Non è possibile mappare o rinominare una colonna alla deriva utilizzando un mapping fisso

### <a name="mapping-hierarchical-columns"></a>Mapping di colonne gerarchiche

I mapping fissi possono essere utilizzati per eseguire il mapping di una sottocolonna di una colonna gerarchica a una colonna di primo livello. Se si dispone di una gerarchia definita, utilizzare l'elenco a discesa delle colonne per selezionare una sottocolonna. La trasformazione di selezione creerà una nuova colonna con il valore e il tipo di dati della colonna secondaria.

![mappatura gerarchica](media/data-flow/select-hierarchy.png "mappatura gerarchica")

## <a name="rule-based-mapping"></a>mapping basato su regole

Se si desidera eseguire il mapping di più colonne contemporaneamente o passare colonne alla deriva a valle, utilizzare il mapping basato su regole per definire i mapping utilizzando modelli di colonna. Trova la `name`corrispondenza `stream`in `position` base alle colonne , `type`, e . È possibile avere qualsiasi combinazione di mapping fissi e basati su regole. Per impostazione predefinita, per tutte le proiezioni con più di 50 colonne verrà associato per impostazione predefinita un mapping basato su regole che corrisponde a ogni colonna e restituisce il nome immesso. 

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

Se nel nome della colonna sono presenti più corrispondenze `$n` regex, è possibile fare riferimento a corrispondenze specifiche usando dove 'n' si riferisce a quale corrispondenza. Ad esempio, ''2' si riferisce alla seconda corrispondenza all'interno di un nome di colonna.

### <a name="rule-based-hierarchies"></a>Gerarchie basate su regole

Se la proiezione definita ha una gerarchia, è possibile utilizzare il mapping basato su regole per eseguire il mapping delle sottocolonne delle gerarchie. Specificare una condizione corrispondente e la colonna complessa di cui si desidera mappare le sottocolonne. Ogni sottocolonna corrispondente verrà emessa utilizzando la regola 'Nome come' specificata a destra.

![mapping basato su regole](media/data-flow/rule-based-hierarchy.png "mapping basato su regole")

Nell'esempio precedente viene confrontato `a`con tutte le sottocolonne di column complex . `a`contiene due `b` sottocolonne e `c`. Lo schema di output `b` `c` includerà due colonne e `$$`come la condizione 'Nome come' è .

### <a name="parameterization"></a>Parametrizzazione

È possibile parametrizzare i nomi delle colonne utilizzando il mapping basato su regole. Utilizzare la ```name``` parola chiave per associare i nomi di colonna in ingresso a un parametro. Se ad esempio si dispone ```mycolumn```di un parametro del flusso di dati, ```mycolumn```è possibile creare una regola che corrisponda a qualsiasi nome di colonna uguale a . È possibile rinominare la colonna corrispondente con una stringa hardcoded, ad esempio 'chiave business', e farvi riferimento in modo esplicito. In questo esempio, la ```name == $mycolumn``` condizione corrispondente è e la condizione del nome è 'chiave business'. 

## <a name="auto-mapping"></a>Mappatura automatica

Quando si aggiunge una trasformazione di selezione, il **mapping automatico** può essere abilitato cambiando il dispositivo di scorrimento Mappatura automatica. Con il mapping automatico, la trasformazione di selezione esegue il mapping di tutte le colonne in ingresso, esclusi i duplicati, con lo stesso nome dell'input. Sono incluse le colonne con deriva, il che significa che i dati di output potrebbero contenere colonne non definite nello schema. Per ulteriori informazioni sulle colonne con deriva, vedere [deriva dello schema](concepts-data-flow-schema-drift.md).

![Mappatura automatica](media/data-flow/automap.png "Mappatura automatica")

Con il mapping automatico attivato, la trasformazione di selezione rispetterà le impostazioni di ignorare i duplicati e fornire un nuovo alias per le colonne esistenti. L'aliasing è utile quando si esegueno più join o ricerche nello stesso flusso e in scenari di self-join. 

## <a name="duplicate-columns"></a>Colonne duplicate

Per impostazione predefinita, la trasformazione di selezione elimina le colonne duplicate nella proiezione di input e di output. Le colonne di input duplicate provengono spesso da trasformazioni di join e di ricerca in cui i nomi delle colonne vengono duplicati su ogni lato del join. Colonne di output duplicate possono verificarsi se si esegue il mapping di due colonne di input diverse allo stesso nome. Scegliere se eliminare o trasferire colonne duplicate eseguendo l'alternazione della casella di controllo.

![Salta duplicati](media/data-flow/select-skip-dup.png "Salta duplicati")

## <a name="ordering-of-columns"></a>Ordinamento delle colonne

L'ordine dei mapping determina l'ordine delle colonne di output. Se una colonna di input viene mappata più volte, verrà rispettato solo il primo mapping. Per qualsiasi eliminazione di colonne duplicate, verrà mantenuta la prima corrispondenza.

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

Di seguito è riportato un esempio di mapping di selezione e relativo script del flusso di dati:Below is an example of a select mapping and its data flow script:

![Esempio di selezione dello script](media/data-flow/select-script-example.png "Esempio di selezione dello script")

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
* Dopo aver utilizzato Select per rinominare, riordinare e alias le colonne, utilizzare la [trasformazione Sink](data-flow-sink.md) per inserire i dati in un archivio dati.
