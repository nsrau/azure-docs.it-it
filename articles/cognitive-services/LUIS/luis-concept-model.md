---
title: Progettare con i modelli-LUIS
description: La comprensione del linguaggio offre diversi tipi di modelli. Alcuni modelli possono essere usati in più modi.
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: bbb1f0d43b2a3fd2e8a2dff2201a09622ecaf977
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683925"
---
# <a name="design-with-intent-and-entity-models"></a>Progettazione con modelli di entità e finalità

La comprensione del linguaggio fornisce due tipi di modelli per definire lo schema dell'app. Lo schema dell'app determina quali informazioni vengono ricevute dalla stima di un nuovo enunciato utente.

Lo schema dell'app è creato con i modelli creati usando l' [insegnamento del computer](#authoring-uses-machine-teaching):
* Gli [Intent](#intents-classify-utterances) classificano le espressioni utente
* Le [entità](#entities-extract-data) estraggono i dati da un enunciato

## <a name="authoring-uses-machine-teaching"></a>La creazione usa l'insegnamento del computer

La metodologia di apprendimento automatico di LUIS consente di insegnare facilmente i concetti a un computer. Non è necessario comprendere l' _apprendimento automatico_ per l'uso di Luis. Al contrario, l'insegnante comunica un concetto di LUIS fornendo esempi del concetto e spiegando come un concetto deve essere modellato usando altri concetti correlati. L'insegnante può inoltre migliorare in modo interattivo il modello di LUIS identificando e correggendo gli errori di stima.

<a name="v3-authoring-model-decomposition"></a>

## <a name="intents-classify-utterances"></a>Espressioni di classificazione degli Intent

Una finalità classifica le espressioni di esempio per insegnare a LUIS lo scopo. Le espressioni di esempio all'interno di un preventivo vengono usate come esempi positivi dell'espressione. Queste stesse espressioni vengono usate come esempi negativi in tutti gli altri Intent.

Si consideri un'app che deve determinare l'intenzione di un utente di ordinare un libro e un'app che richiede l'indirizzo di spedizione del cliente. Questa app è costituita da due Intent: `OrderBook` e `ShippingLocation` .

Il seguente enunciato è un **esempio positivo** per lo `OrderBook` scopo e un **esempio negativo** per gli Intent `ShippingLocation` e `None` :

`Buy the top-rated book on bot architecture.`

## <a name="entities-extract-data"></a>Entità Estrai dati

Un'entità rappresenta un'unità di dati che si desidera estrarre dall'espressione. Un'entità di Machine Learning è un'entità di primo livello che contiene sottoentità, che sono anche entità di machine learning.

Un esempio di un'entità di Machine Learning è un ordine per un ticket del piano. Concettualmente si tratta di una singola transazione con molte unità di dati più piccole, ad esempio data, ora, quantità di sedili, tipo di posto, ad esempio prima classe o allenatore, posizione di origine, posizione di destinazione e scelta pasto.

## <a name="intents-versus-entities"></a>Intent rispetto a entità

Uno scopo è il risultato desiderato dell' _intero_ enunciato, mentre le entità sono parti di dati estratte dall'espressione. In genere gli Intent sono collegati a azioni, che devono essere eseguite dall'applicazione client. Le entità sono le informazioni necessarie per eseguire questa azione. Dal punto di vista della programmazione, un'intenzione attiverà una chiamata al metodo e le entità verrebbero utilizzate come parametri per la chiamata al metodo.

Questo enunciato _deve_ avere un preventivo e _può_ avere entità:

`Buy an airline ticket from Seattle to Cairo`

Questo enunciato ha una sola intenzione:

* Acquisto di un biglietto aereo

Questo enunciato _può_ avere diverse entità:

* Località di Seattle (Origin) e Cairo (Destination)
* Quantità di un singolo ticket

## <a name="entity-model-decomposition"></a>Scomposizione del modello di entità

LUIS supporta la _scomposizione dei modelli_ con le API di creazione, suddividendo un concetto in parti più piccole. In questo modo è possibile compilare i modelli in tutta sicurezza per la costruzione e la stima delle varie parti.

La scomposizione dei modelli include le parti seguenti:

* [Intent](#intents-classify-utterances)
    * [funzionalità](#features)
* [entità di Machine Learning](reference-entity-machine-learned-entity.md)
    * sottoentità (anche entità di machine learning)
        * [funzionalità](#features)
            * [elenco frasi](luis-concept-feature.md)
            * [entità non di apprendimento automatico](luis-concept-feature.md) , ad esempio [espressioni regolari](reference-entity-regular-expression.md), [elenchi](reference-entity-list.md)e [entità predefinite](luis-reference-prebuilt-entities.md)

<a name="entities-extract-data"></a>
<a name="machine-learned-entities"></a>

## <a name="features"></a>Funzionalità

Una [funzionalità](luis-concept-feature.md) è un tratto di distinzione o un attributo di dati osservato dal sistema. Le funzionalità di Machine Learning offrono a LUIS importanti indicazioni per la posizione in cui cercare gli elementi che distinguono un concetto. Sono suggerimenti che LUIS può usare, ma non regole rigide. Questi hint vengono usati insieme alle etichette per trovare i dati.

## <a name="patterns"></a>Modelli

I [modelli](luis-concept-patterns.md) sono progettati per migliorare l'accuratezza quando più espressioni sono molto simili. Un modello consente di ottenere maggiore accuratezza in relazione a una finalità senza fornire molte altre espressioni.

## <a name="extending-the-app-at-runtime"></a>Estensione dell'app in fase di esecuzione

Lo schema dell'app (modelli e funzionalità) viene sottoposto a training e pubblicato nell'endpoint di stima. Per aumentare la stima, è possibile [passare nuove informazioni](schema-change-prediction-runtime.md)insieme all'espressione dell'utente all'endpoint di stima.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su [Intent](luis-concept-intent.md) ed [entità](luis-concept-entity-types.md).
* Altre informazioni sulle [funzionalità](luis-concept-feature.md)