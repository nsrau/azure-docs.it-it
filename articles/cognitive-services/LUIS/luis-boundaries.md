---
title: Limiti
titleSuffix: Language Understanding - Azure Cognitive Services
description: Questo articolo illustra i limiti di LUIS (Language Understanding) dei Servizi cognitivi di Azure. LUIS dispone di diverse aree di limiti. Il limite modello controlla finalità, entità e funzionalità in LUIS. I limiti di quota si basano sul tipo di chiave. La combinazione di tasti controlla il sito Web di LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/12/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 51f5355e365d0dcf7a103deab3356f015e75641e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57897514"
---
# <a name="boundaries-for-your-luis-model-and-keys"></a>Limiti per il modello LUIS e le chiavi
LUIS dispone di diverse aree di limiti. La prima è il [limite modello](#model-boundaries), che controlla finalità, entità e funzionalità in LUIS. La seconda area è [limiti di quota](#key-limits) basata sul tipo di chiave. Una terza area di limiti è rappresentata dalla [combinazione di tasti](#keyboard-controls) per il controllo del sito Web LUIS. Una quarta area è data dal [mapping dell'area globale](luis-reference-regions.md) tra il sito Web di creazione LUIS e le API dell'[endpoint LUIS](luis-glossary.md#endpoint). 


## <a name="model-boundaries"></a>Limiti di modello

Se l'app supera i limiti del modello LUIS, è consigliabile usare un'app [dispatch LUIS](luis-concept-enterprise.md#dispatch-tool-and-model) o un [contenitore LUIS](luis-container-howto.md). 

|Area|Limite|
|--|:--|
| [Nome app][luis-get-started-create-app] | *Numero max predefinito di caratteri |
| [Test di batch][batch-testing]| 10 set di dati, 1000 espressioni per ogni set di dati|
| Elenco esplicito | 50 per applicazione|
| [Finalità][intents]|500 per ogni applicazione: 499 finalità personalizzate e la finalità _None_ obbligatoria.<br>Applicazione [basata su invio](https://aka.ms/dispatch-tool) con 500 origini di spedizione corrispondenti.|
| [Elenca entità](./luis-concept-entity-types.md) | Padre: 50, figlio: 20.000 elementi. Il nome canonico è il *numero max predefinito di caratteri. I sinonimi non hanno restrizioni di lunghezza. |
| [Entità basate su Machine Learning](./luis-concept-entity-types.md):<br> Composita<br>  Gerarchica<br> Semplice|Un limite di 100 entità padre (esclusi gli elementi figlio gerarchici) o 330 entità (inclusi gli elementi figlio gerarchici), indipendentemente dal limite raggiunto dall'utente.<br><br>Un esempio di questa gerarchia è costituito da 30 elementi, ognuno con 10 elementi figlio.  Gli elementi figlio utilizzeranno in totale 300 entità e gli elementi di livello superiore utilizzeranno le restanti 30. |
| [Criteri](luis-concept-patterns.md)|500 criteri per ogni applicazione.<br>Il criterio può contenere al massimo 400 caratteri.<br>3 entità pattern.any per criterio<br>Il criterio può contenere al massimo 2 testi facoltativi annidati|
| [Pattern.any](./luis-concept-entity-types.md)|100 per applicazione, 3 entità pattern.any per criterio |
| [Elenco di frasi][phrase-list]|10 elenchi di frase, 5.000 elementi per ogni elenco|
| [Entità predefinite](./luis-prebuilt-entities.md) | nessun limite|
| [Entità di espressione regolare](./luis-concept-entity-types.md)|20 entità<br>È consentito un numero massimo di 500 caratteri. per ogni criterio di entità di espressione regolare|
| [Ruoli](luis-concept-roles.md)|300 ruoli per ogni applicazione. 10 per entità|
| [Espressione][utterances] | 500 caratteri|
| [Espressioni][utterances] | 15.000 per ogni applicazione - non sono previsti limiti al numero di espressioni per finalità|
| [Versioni](luis-concept-version.md)| nessun limite |
| [Nome della versione][luis-how-to-manage-versions] | 10 caratteri limitati a caratteri alfanumerici e punto (.) |

*Il numero max predefinito di caratteri è 50. 

## <a name="intent-and-entity-naming"></a>Denominazione di finalità e entità
Non utilizzare i seguenti caratteri nei nomi delle entità e delle finalità:

|Character|NOME|
|--|--|
|`{`|Parentesi graffa aperta|
|`}`|Parentesi graffa chiusa|
|`[`|Parentesi quadra aperta|
|`]`|Parentesi quadra chiusa|
|`\`|Barra rovesciata|

## <a name="key-usage"></a>Uso della chiave

Language Understanding ha chiavi separate, un tipo per la creazione e un tipo per l'esecuzione di query sull'endpoint di stima. Per altre informazioni sulle differenze tra i tipi di chiave, vedere [Chiavi di creazione e di endpoint per query di stima in LUIS](luis-concept-keys.md).

## <a name="key-limits"></a>Limiti delle chiavi

La chiave di creazione presenta diversi limiti per creazione e endpoint. La chiave endpoint del servizio LUIS è valida solo per le query di endpoint.


|Chiave|Creazione|Endpoint|Scopo|
|--|--|--|--|
|Creazione/Avvio di Language Understanding|1 milione/mese, 5/secondo|1.000/mese, 5/secondo|Creazione di app LUIS|
|[Sottoscrizione][pricing] di Language Understanding - F0 - livello gratuito |non valido|10.000/mese, 5/secondo|Esecuzione di query per l'endpoint LUIS|
|[Sottoscrizione][pricing] di Language Understanding - S0 - livello Basic|non valido|50/secondo|Esecuzione di query per l'endpoint LUIS|
|[Sottoscrizione][pricing] di Servizi cognitivi- S0 - livello Standard|non valido|50/secondo|Esecuzione di query per l'endpoint LUIS|
|[Integrazione dell'Analisi del sentiment](luis-how-to-publish-app.md#enable-sentiment-analysis)|non valido|nessun addebito|Aggiunta di informazioni sentiment inclusa l'estrazione dei dati di frase chiave |
|Integrazione riconoscimento vocale|non valido|Richieste di endpoint $5,50 USD/1.000|Converte un'espressione parlata in un espressione di testo e restituisce i risultati LUIS|

## <a name="keyboard-controls"></a>Controlli tastiera

|Input tastiera | DESCRIZIONE | 
|--|--|
|Controllo + E|passa dai token alle entità nell'elenco delle espressioni|

## <a name="website-sign-in-time-period"></a>Periodo di tempo per l'accesso al sito Web

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
