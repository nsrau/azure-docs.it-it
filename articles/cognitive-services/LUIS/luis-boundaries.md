---
title: Limiti-LUIS
titleSuffix: Azure Cognitive Services
description: Questo articolo illustra i limiti di LUIS (Language Understanding) dei Servizi cognitivi di Azure. LUIS dispone di diverse aree di limiti. Il limite modello controlla finalità, entità e funzionalità in LUIS. I limiti di quota si basano sul tipo di chiave. La combinazione di tasti controlla il sito Web di LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 0654916b344cf47cf9942b883d62d392c0552979
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818942"
---
# <a name="boundaries-for-your-luis-model-and-keys"></a>Limiti per il modello LUIS e le chiavi
LUIS dispone di diverse aree di limiti. La prima è il [limite modello](#model-boundaries), che controlla finalità, entità e funzionalità in LUIS. La seconda area è [limiti di quota](#key-limits) basata sul tipo di chiave. Una terza area di limiti è rappresentata dalla [combinazione di tasti](#keyboard-controls) per il controllo del sito Web LUIS. Una quarta area è data dal [mapping dell'area globale](luis-reference-regions.md) tra il sito Web di creazione LUIS e le API dell'[endpoint LUIS](luis-glossary.md#endpoint). 


## <a name="model-boundaries"></a>Limiti di modello

Se l'app supera i limiti del modello LUIS, è consigliabile usare un'app [dispatch LUIS](luis-concept-enterprise.md#dispatch-tool-and-model) o un [contenitore LUIS](luis-container-howto.md). 

|Area|Limite|
|--|:--|
| [Nome app][luis-get-started-create-app] | *Numero max predefinito di caratteri |
| Applications| 500 applicazioni per ogni risorsa di creazione di Azure |
| [Test batch][batch-testing]| 10 set di dati, 1000 espressioni per ogni set di dati|
| Elenco esplicito | 50 per applicazione|
| Entità esterne | Nessun limite |
| [Intent][intents]|500 per applicazione: 499 Intent personalizzati e la finalità obbligatoria _None_ .<br>Applicazione [basata su invio](https://aka.ms/dispatch-tool) con 500 origini di spedizione corrispondenti.|
| [Elenca entità](./luis-concept-entity-types.md) | Padre: 50, figlio: 20.000 elementi. Il nome canonico è il *numero max predefinito di caratteri. I sinonimi non hanno restrizioni di lunghezza. |
| [Entità e ruoli appresi dal computer](./luis-concept-entity-types.md):<br> composito<br>semplice<br>ruolo entità|Limite di 100 entità padre o di 330 entità, a seconda del limite che viene raggiunto per primo dall'utente. Un ruolo viene conteggiato come entità ai fini di questo limite. Un esempio è costituito da un composito con un'entità semplice, che ha 2 ruoli: 1 composito + 1 Simple + 2 Roles = 4 delle entità 330.<br>I sottocomponenti possono essere annidati fino a 5 livelli.|
|Modello come funzionalità| Numero massimo di modelli che possono essere usati come descrittori (funzionalità) per un modello specifico come 10 modelli. Il numero massimo di elenchi di frasi utilizzato come descrittore (funzionalità) per un modello specifico è costituito da dieci elenchi di frasi.|
| [Anteprima-entità elenco dinamico](https://aka.ms/luis-api-v3-doc#dynamic-lists-passed-in-at-prediction-time)|2 elenchi di ~ 1K per richiesta dell'endpoint di stima di query|
| [Criteri](luis-concept-patterns.md)|500 criteri per ogni applicazione.<br>Il criterio può contenere al massimo 400 caratteri.<br>3 entità pattern.any per criterio<br>Il criterio può contenere al massimo 2 testi facoltativi annidati|
| [Pattern.any](./luis-concept-entity-types.md)|100 per applicazione, 3 entità pattern.any per criterio |
| [Elenco frasi][phrase-list]|500 elenchi di frasi. L'espressione non interscambiabile è costituita da un massimo di 5.000 frasi. L'espressione di frasi intercambiabili ha un numero massimo di 50.000 di frasi. Numero massimo di frasi totali per ogni applicazione di 500.000 frasi.|
| [Entità predefinite](./luis-prebuilt-entities.md) | nessun limite|
| [Entità di espressione regolare](./luis-concept-entity-types.md)|20 entità<br>È consentito un numero massimo di 500 caratteri. per ogni criterio di entità di espressione regolare|
| [Ruoli](luis-concept-roles.md)|300 ruoli per ogni applicazione. 10 per entità|
| [Espressione][utterances] | 500 caratteri|
| [Espressioni][utterances] | 15.000 per applicazione: non esiste alcun limite al numero di enunciati per finalità|
| [Versioni](luis-concept-version.md)| 100 versioni per applicazione |
| [Nome versione][luis-how-to-manage-versions] | 10 caratteri limitati a caratteri alfanumerici e punto (.) |

*Il numero max predefinito di caratteri è 50. 

<a name="intent-and-entity-naming"></a>

## <a name="name-uniqueness"></a>Univocità del nome

Utilizzare le seguenti regole di univocità dei nomi.

Gli elementi seguenti devono essere univoci all'interno di un'app LUIS:

* Nome versione
* intento
* Entità
* ruoli

Gli elementi seguenti devono essere univoci all'interno dell'ambito applicato:

* elenco frasi 

## <a name="object-naming"></a>Denominazione degli oggetti

Non usare i caratteri seguenti nei nomi seguenti.

|Oggetto|Escludi caratteri|
|--|--|
|Finalità, entità e nomi di ruoli|`:`<br>`$` <br> `&`|
|Nome versione|`\`<br> `/`<br> `:`<br> `?`<br> `&`<br> `=`<br> `*`<br> `+`<br> `(`<br> `)`<br> `%`<br> `@`<br> `$`<br> `~`<br> `!`<br> `#`|

## <a name="key-usage"></a>Uso della chiave

Language Understanding ha chiavi separate, un tipo per la creazione e un tipo per l'esecuzione di query sull'endpoint di stima. Per altre informazioni sulle differenze tra i tipi di chiave, vedere [Chiavi di creazione e di endpoint per query di stima in LUIS](luis-concept-keys.md).

<a name="key-limits"></a>

## <a name="resource-key-limits"></a>Limiti delle chiavi di risorsa

Le chiavi delle risorse hanno limiti diversi per la creazione e l'endpoint. La chiave dell'endpoint della query di stima LUIS è valida solo per le query endpoint. 

* 500 applicazioni per ogni risorsa di creazione di Azure 

|Chiave|Creazione|Endpoint|Scopo|
|--|--|--|--|
|Starter|1 milione/mese, 5/secondo|1\.000/mese, 5/secondo|Creazione di app LUIS|
|Livello gratuito F0 |1 milione/mese, 5/secondo|10.000/mese, 5/secondo|Esecuzione di query per l'endpoint LUIS|
|S0-livello Basic|-|50/secondo|Esecuzione di query per l'endpoint LUIS|
|S0-livello standard|-|50/secondo|Esecuzione di query per l'endpoint LUIS|
|[Integrazione dell'Analisi del sentiment](luis-how-to-publish-app.md#enable-sentiment-analysis)|-|-|L'aggiunta di informazioni sui sentimenti, inclusa l'estrazione dei dati di frasi chiave, viene fornita senza richiedere un'altra risorsa di Azure. |
|[Integrazione vocale](../speech-service/how-to-recognize-intents-from-speech-csharp.md)|-|1000 richieste endpoint per ogni costo unitario|Converte un'espressione parlata in un espressione di testo e restituisce i risultati LUIS|

[Altre informazioni sui prezzi.][pricing]

## <a name="keyboard-controls"></a>Controlli tastiera

|Input tastiera | Descrizione | 
|--|--|
|Controllo + E|passa dai token alle entità nell'elenco delle espressioni|

## <a name="website-sign-in-time-period"></a>Periodo di accesso del sito Web

Il periodo di tempo di accesso è di **60 minuti**. Trascorso questo tempo, si verificherà il seguente errore. Sarà necessario eseguire nuovamente l'accesso.

[luis-get-started-create-app]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app
[batch-testing]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-test#batch-testing
[intents]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-intent
[phrase-list]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-feature
[utterances]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-utterance
[luis-how-to-manage-versions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions
[pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
<!-- TBD: fix this link -->
[speech-to-intent-pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
