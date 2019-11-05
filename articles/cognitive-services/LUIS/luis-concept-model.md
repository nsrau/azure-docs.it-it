---
title: Progettare con i modelli-LUIS
titleSuffix: Azure Cognitive Services
description: La comprensione del linguaggio offre diversi tipi di modelli. Alcuni modelli possono essere usati in più modi.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 4cbca96ed95167615f3ff2876e27e546d08d92f5
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507715"
---
# <a name="design-with-intent-and-entity-models"></a>Progettazione con modelli di entità e finalità 

La comprensione del linguaggio offre diversi tipi di modelli. Alcuni modelli possono essere usati in più modi. 

## <a name="v3-authoring-uses-machine-teaching"></a>La creazione di V3 usa l'insegnamento del computer

LUIS consente agli utenti di insegnare facilmente i concetti a un computer. Il computer può quindi compilare modelli, ovvero approssimazioni funzionali di concetti quali i classificatori e gli estrattori, che possono essere usati per potenziare le applicazioni intelligenti. Mentre LUIS è basato sull'apprendimento automatico, non è necessario conoscere l'apprendimento automatico per usarlo. Al contrario, gli insegnanti del computer comunicano i concetti con LUIS mostrando esempi positivi e negativi del concetto e spiegano come un concetto deve essere modellato usando altri concetti correlati. Gli insegnanti possono inoltre migliorare il modello di LUIS in modo interattivo identificando e correggendo gli errori di stima. 

## <a name="v3-authoring-model-decomposition"></a>Versione V3 creazione di un modello di scomposizione

LUIS supporta la _scomposizione dei modelli_ con le API di creazione V3, suddividendo il modello in parti più piccole. In questo modo è possibile compilare i modelli in tutta sicurezza per la costruzione e la stima delle varie parti.

La scomposizione dei modelli include le parti seguenti:

* [Intent](#intents-classify-utterances)
    * [descrittori](#descriptors-are-features) forniti dalle funzionalità
* [entità apprese dal computer](#machine-learned-entities)
    * [sottocomponenti](#entity-subcomponents-help-extract-data) (anche entità apprese dal computer)
        * [descrittori](#descriptors-are-features) forniti dalle funzionalità 
        * [vincoli](#constraints-are-text-rules) forniti da entità non apprese dal computer, ad esempio (espressioni regolari ed elenchi)

## <a name="v2-authoring-models"></a>V2 modelli di creazione

LUIS supporta le entità composite con le API di creazione V2. Questa operazione fornisce una scomposizione del modello simile, ma non corrisponde alla scomposizione dei modelli V3. L'architettura del modello consigliata consiste nel passare alla scomposizione dei modelli nelle API di creazione di V3. 

## <a name="intents-classify-utterances"></a>Espressioni di classificazione degli Intent

Una finalità classifica le espressioni di esempio per insegnare a LUIS lo scopo. Le espressioni di esempio all'interno di un preventivo vengono usate come esempi positivi dell'espressione. Queste stesse espressioni vengono usate come esempi negativi in tutti gli altri Intent.

Si consideri un'app che deve determinare l'intenzione di un utente di ordinare un libro e un'app che richiede l'indirizzo di spedizione del cliente. Questa app è costituita da due Intent: `OrderBook` e `ShippingLocation`.

Il seguente enunciato è un **esempio positivo** per l'intent `OrderBook` e un **esempio negativo** per la `ShippingLocation` e `None` Intent: 

`Buy the top-rated book on bot architecture.`

Il risultato di un progetto ben progettato, con le espressioni di esempio, è una stima ad alta finalità. 

## <a name="entities-extract-data"></a>Entità Estrai dati

Un'entità rappresenta un'unità di dati che si desidera estrarre dall'espressione. 

### <a name="machine-learned-entities"></a>Entità apprese dal computer

Un'entità appresa dal computer è un'entità di primo livello che contiene sottocomponenti, che sono anche entità apprese dal computer. 

**Usare un'entità appresa dal computer**:

* Quando i sottocomponenti sono necessari per l'applicazione client
* per consentire all'algoritmo di Machine Learning di scomporre le entità

Ogni sottocomponente può avere:

* sottocomponenti
* vincoli (entità di espressioni regolari o entità elenco)
* descrittori (funzionalità come un elenco di frasi) 

Un esempio di entità appresa dal computer è un ordine per un ticket del piano. Concettualmente si tratta di una singola transazione con molte unità di dati più piccole, ad esempio data, ora, quantità di sedili, tipo di posto, ad esempio prima classe o allenatore, posizione di origine, posizione di destinazione e scelta pasto.


### <a name="entity-subcomponents-help-extract-data"></a>I sottocomponenti dell'entità consentono di estrarre i dati

Un sottocomponente è un'entità figlio acquisita dal computer all'interno di un'entità padre appresa dal computer. 

**Usare il sottocomponente per**:

* scomporre le parti dell'entità appresa dal computer (entità padre).

Di seguito viene rappresentata un'entità appresa dal computer con tutte le parti di dati separate:

* TravelOrder (entità Machine-learned)
    * DateTime (datetimeV2 predefinito)
    * Località (entità appresa dal computer)
        * Origin (Role trovato tramite il contesto, ad esempio `from`)
        * Destinazione (Role trovato tramite il contesto, ad esempio `to`)
    * Posto di seduta (entità appresa dal computer)
        * Quantity (numero predefinito)
        * Qualità (entità appresa dal computer con descrittore dell'elenco di frasi)
    * Pasti (entità appresa dal computer con vincolo dell'entità di elenco come scelte alimentari)

Alcuni di questi dati, ad esempio la posizione di origine e la posizione di destinazione, devono essere appresi dal contesto dell'enunciato, forse con la formulazione `from` e `to`. È possibile estrarre altre parti di dati con corrispondenze di stringa esatte (`Vegan`) o entità predefinite (geographyV2 di `Seattle` e `Cairo`). 

Si progetta il modo in cui i dati vengono confrontati ed estratti con i modelli scelti e come vengono configurati.

### <a name="constraints-are-text-rules"></a>I vincoli sono regole di testo

Un vincolo è una regola di corrispondenza del testo, fornita da un'entità non appresa dal computer, ad esempio l'entità di espressioni regolari o un'entità elenco. Il vincolo viene applicato in fase di stima per limitare la stima e fornire la risoluzione dell'entità richiesta dall'applicazione client. Queste regole vengono definite durante la creazione del sottocomponente. 

**Usare un vincolo**:
* Quando si conosce il testo esatto da estrarre.

I vincoli includono:

* entità di [espressioni regolari](reference-entity-regular-expression.md)
* [Elenca](reference-entity-list.md) entità 
* entità [predefinite](luis-reference-prebuilt-entities.md)

Continuando con l'esempio del ticket aereo, i codici aeroportuali possono trovarsi in un'entità di elenco per le corrispondenze esatte del testo. 

Per un elenco di aeroporti, la voce dell'elenco per Seattle è il nome della città, `Seattle` e i sinonimi per Seattle includono il codice aeroportuale per Seattle insieme alle città e alle città circostanti:

|`Seattle` elencare i sinonimi di entità|
|--|
|`Sea`|
|`seatac`|
|`Bellevue`|

Se si desidera riconoscere solo 3 codici letterali per i codici aeroportuali, utilizzare un'espressione regolare come vincolo. 

`/^[A-Z]{3}$/`

## <a name="intents-versus-entities"></a>Intent rispetto a entità

Uno scopo è il risultato desiderato dell' _intero_ enunciato, mentre le entità sono parti di dati estratte dall'espressione. In genere gli Intent sono collegati alle azioni che l'applicazione client deve prendere e le entità sono necessarie per eseguire questa azione. Dal punto di vista della programmazione, un'intenzione attiverà una chiamata al metodo e le entità verrebbero utilizzate come parametri per la chiamata al metodo.

Questo enunciato _deve_ avere un preventivo e _può_ avere entità:

`Buy a airline ticket from Seattle to Cairo`

Questo enunciato ha una sola intenzione:

* Acquisto di un biglietto aereo

Questo enunciato _può_ avere diverse entità:

* Località di Seattle (Origin) e Cairo (Destination)
* Quantità di un singolo ticket

## <a name="descriptors-are-features"></a>I descrittori sono funzionalità

Un descrittore è una funzionalità applicata a un modello in fase di training, inclusi elenchi di frasi ed entità. 

**Utilizzare un descrittore quando si desidera**:

* aumentare il significato di parole e frasi identificate dal descrittore
* fare in modo che LUIS suggerisca nuovo testo o frasi da consigliare per il descrittore
* correggere un errore nei dati di training

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su [Intent](luis-concept-intent.md) ed [entità](luis-concept-entity-types.md). 