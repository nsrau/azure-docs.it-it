---
title: Progettare con modelli - LUIS
titleSuffix: Azure Cognitive Services
description: La comprensione del linguaggio fornisce diversi tipi di modelli. Alcuni modelli possono essere utilizzati in più modi.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: d721ceb25b3ce2408563a0bed16457d05affe7b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219995"
---
# <a name="design-with-intent-and-entity-models"></a>Progettare con finalità ed entity model 

La comprensione del linguaggio fornisce diversi tipi di modelli. Alcuni modelli possono essere utilizzati in più modi. 

## <a name="v3-authoring-uses-machine-teaching"></a>V3 Authoring utilizza l'insegnamento automatico

LUIS consente alle persone di insegnare facilmente i concetti a una macchina. La macchina può quindi costruire modelli (approssimazioni funzionali di concetti come classificatori ed estrattori) che possono essere utilizzati per alimentare applicazioni intelligenti. Sebbene LUIS sia basato sull'apprendimento automatico, la comprensione dell'apprendimento automatico non è necessaria per usarlo. Al contrario, gli insegnanti di macchine comunicano i concetti a LUIS mostrando esempi positivi e negativi del concetto e spiegando come un concetto deve essere modellato utilizzando altri concetti correlati. Gli insegnanti possono anche migliorare il modello di LUIS in modo interattivo identificando e correggendo gli errori di previsione. 

## <a name="v3-authoring-model-decomposition"></a>V3 Creazione di scomposizione del modello

LUIS supporta la _scomposizione_ del modello con le API di creazione V3, suddividendo il modello in parti più piccole. Ciò consente di costruire i modelli con fiducia nel modo in cui le varie parti sono costruite e previste.

La scomposizione del modello è composta dalle seguenti parti:

* [Intenti](#intents-classify-utterances)
    * [descrittori](#descriptors-are-features) forniti da funzionalità
* [entità apprese automaticamente](#machine-learned-entities)
    * [sottocomponenti](#entity-subcomponents-help-extract-data) (anche entità apprese automaticamente)
        * [descrittori](#descriptors-are-features) forniti da funzionalità 
        * [vincoli](#constraints-are-text-rules) forniti da entità non apprese dal computer, ad esempio espressioni regolari ed elenchi

## <a name="v2-authoring-models"></a>Modelli di authoring V2

LUIS supporta entità composite con le API di creazione V2. Ciò fornisce una decomposizione del modello simile, ma non è uguale alla decomposizione del modello V3. L'architettura del modello consigliata consiste nel passare alla scomposizione del modello nelle API di creazione V3. 

## <a name="intents-classify-utterances"></a>Le finalità classificano le espressioniIntents classify utterances

Una finalità classifica le espressioni di esempio per insegnare a LUIS la finalità. Le espressioni di esempio all'interno di una finalità vengono usate come esempi positivi dell'espressione. Queste stesse espressioni vengono usate come esempi negativi in tutte le altre finalità.

Si consideri un'app che deve determinare l'intenzione di un utente di ordinare un libro e un'app che richiede l'indirizzo di spedizione per il cliente. Questa applicazione ha due `OrderBook` `ShippingLocation`finalità: e .

L'espressione seguente è un `OrderBook` esempio positivo per la finalità e un esempio negativo per le finalità e :The following utterance is a positive **example** for the intent and a **negative example** for the `ShippingLocation` e `None` intents: 

`Buy the top-rated book on bot architecture.`

Il risultato di finalità ben progettate, con le espressioni di esempio, è una stima con finalità elevata. 

## <a name="entities-extract-data"></a>Le entità estraggono i dati

Un'entità rappresenta un'unità di dati che si desidera estrarre dall'espressione. 

### <a name="machine-learned-entities"></a>Entità apprese automaticamente

Un'entità appresa dal computer è un'entità di primo livello contenente sottocomponenti, che sono anche entità apprese dal computer. 

**Usare un'entità appresa dal computer:**

* quando i sottocomponenti sono necessari per l'applicazione client
* per aiutare l'algoritmo di apprendimento automatico a scomporre le entità

Ogni sottocomponente può avere:

* Sottocomponenti
* vincoli (entità espressione regolare o entità elenco)
* descrittori (caratteristiche come un elenco di frasi) 

Un esempio di entità appresa dalla macchina è un ordine per un ticket aereo. Concettualmente si tratta di una singola transazione con molte unità di dati più piccole, come la data, l'ora, la quantità di posti a sedere, il tipo di posto, come prima classe o allenatore, la posizione di origine, la posizione di destinazione e la scelta del pasto.


### <a name="entity-subcomponents-help-extract-data"></a>I sottocomponenti delle entità consentono di estrarre i dati

Un sottocomponente è un'entità figlio appresa dal computer all'interno di un'entità padre appresa dal computer. 

**Utilizzare il sottocomponente per**:

* scomporre le parti dell'entità appresa dal computer (entità padre).

Di seguito è riportato un'entità appresa dal computer con tutti questi dati separati:The following represents a machine-learned entity with all these separate pieces of data:

* TravelOrder (entità appresa dalla macchina)
    * DateTime (precompilato datetimeV2)
    * Posizione (entità appresa dalla macchina)
        * Origine (ruolo trovato tramite contesto, ad `from`esempio )
        * Destinazione (ruolo trovato tramite contesto, ad `to`esempio )
    * Posti a sedere (entità appresa in modo automatico)
        * Quantità (numero predefinito)
        * Qualità (entità appresa automaticamente con descrittore dell'elenco di frasi)
    * Pasti (entità appresa dalla macchina con vincolo dell'entità elenco come scelte alimentari)

Alcuni di questi dati, ad esempio la posizione di origine e la posizione di destinazione, `from` `to`devono essere appresi dal contesto dell'espressione, ad esempio con una dicitura come e . Altre parti di dati possono essere estratte con corrispondenze di stringhe esatte (`Vegan`) o entità predefinite (geographyV2 di `Seattle` e `Cairo`). 

È possibile progettare la modalità di corrispondenza ed estrazione dei dati in base ai modelli scelti e alla modalità di configurazione.

### <a name="constraints-are-text-rules"></a>I vincoli sono regole di testo

Un vincolo è una regola di corrispondenza del testo, fornita da un'entità non appresa dal computer, ad esempio l'entità di espressione regolare o un'entità di elenco. Il vincolo viene applicato in fase di stima per limitare la stima e fornire la risoluzione dell'entità necessaria per l'applicazione client. Queste regole vengono definite durante la creazione del sottocomponente. 

**Utilizzare un vincolo**:
* quando si conosce il testo esatto da estrarre.

I vincoli includono:

* entità [di espressioni regolari](reference-entity-regular-expression.md)
* [entità elenco](reference-entity-list.md) 
* entità [predefinite](luis-reference-prebuilt-entities.md)

Continuando con l'esempio del biglietto aereo, i codici aeroportuali possono essere in un'entità Lista per corrispondenze di testo esatte. 

Per un elenco aeroportuale, la voce dell'elenco per Seattle è il nome della città `Seattle` e i sinonimi di Seattle includono il codice aeroportuale per Seattle insieme alle città circostanti:

|`Seattle`Sinonimi dell'entità elencoList entity synonyms|
|--|
|`Sea`|
|`seatac`|
|`Bellevue`|

Se si desidera riconoscere solo 3 codici lettera per i codici aeroportuali, utilizzare un'espressione regolare come vincolo. 

`/^[A-Z]{3}$/`

## <a name="intents-versus-entities"></a>Finalità con entità

Una finalità è il risultato desiderato _dell'intera_ espressione, mentre le entità sono parti di dati estratti dall'espressione. In genere, le finalità sono legate alle azioni che l'applicazione client deve eseguire e le entità sono informazioni necessarie per eseguire questa azione. Dal punto di vista della programmazione, una finalità attiverebbe una chiamata al metodo e le entità verrebbero utilizzate come parametri per tale chiamata al metodo.

Questa espressione deve avere una finalità e può avere entità:This utterance _must_ have an intent and _may_ have entities:

`Buy an airline ticket from Seattle to Cairo`

Questa espressione ha una singola intenzione:This utterance has a single intention:

* Acquisto di un biglietto aereo

Questa espressione può avere diverse entità:This utterance _may_ have several entities:

* Località di Seattle (origine) e Cairo (destinazione)
* La quantità di un singolo biglietto

## <a name="descriptors-are-features"></a>I descrittori sono caratteristiche

Un descrittore è una feature applicata a un modello in fase di addestramento, inclusi elenchi di frasi ed entità. 

**Utilizzare un descrittore quando si desidera:**

* aumentare il significato delle parole e delle frasi identificate dal descrittore
* hanno LUIS raccomandare nuovo testo o frasi da raccomandare per il descrittore
* correggere un errore sui dati di training

## <a name="next-steps"></a>Passaggi successivi

* Comprendere [finalità](luis-concept-intent.md) ed [entità](luis-concept-entity-types.md). 