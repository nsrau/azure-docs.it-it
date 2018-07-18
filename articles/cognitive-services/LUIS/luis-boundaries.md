---
title: Limiti del servizio LUIS (Language Understanding) | Microsoft Docs
titleSuffix: Azure
description: Questo articolo contiene i limiti noti del servizio LUIS.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 7f46e55e11c4eb68b515a743b0f51392ffc1269e
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266805"
---
# <a name="luis-boundaries"></a>Limiti di LUIS
LUIS dispone di diverse aree di limiti. La prima è il [limite modello](#model-boundaries), che controlla finalità, entità e funzionalità in LUIS. La seconda area è [limiti di quota](#key-limits) basata sul tipo di chiave. Una terza area di limiti è rappresentata dalla [combinazione di tasti](#keyboard-controls) per il controllo del sito Web LUIS. Una quarta area è data dal [mapping dell'area globale](luis-reference-regions.md) tra il sito Web di creazione LUIS e le API dell'[endpoint LUIS](luis-glossary.md#endpoint). 


## <a name="model-boundaries"></a>Limiti di modello

|Area|Limite|
|--|:--|--|
| [Nome app][luis-get-started-create-app] | *Numero max predefinito di caratteri |
| [Test di batch][batch-testing]| 10 set di dati, 1000 espressioni per ogni set di dati|
| **[Composito](./luis-concept-entity-types.md)|100 con un massimo di 10 elementi figlio |
| Elenco esplicito | 50 per applicazione|
| **[Gerarchica](./luis-concept-entity-types.md) |100 con un massimo di 10 elementi figlio |
| [Finalità][intents]|500 per ogni applicazione<br>Applicazione [basata su invio](https://github.com/Microsoft/botbuilder-tools/tree/master/Dispatch) con 500 origini di spedizione corrispondenti|
| [Elenca entità](./luis-concept-entity-types.md) | Padre: 50, figlio: 20.000 elementi. Il nome canonico è il * numero max predefinito di caratteri. I sinonimi non hanno restrizioni di lunghezza. |
| [Criteri](luis-concept-patterns.md)|500 criteri per ogni applicazione.<br>Il criterio può contenere al massimo 400 caratteri.<br>3 entità pattern.any per criterio<br>Il criterio può contenere al massimo 2 testi facoltativi annidati|
| [Pattern.any](./luis-concept-entity-types.md)|100 per applicazione, 3 entità pattern.any per criterio |
| [Elenco di frasi][phrase-list]|10 elenchi di frase, 5.000 elementi per ogni elenco|
| [Entità predefinite](./Pre-builtEntities.md) | nessun limite|
| [Entità di espressione regolare](./luis-concept-entity-types.md)|20 entità<br>È consentito un numero massimo di 500 caratteri. per ogni criterio di entità di espressione regolare|
| [Ruoli](luis-concept-roles.md)|300 ruoli per ogni applicazione. 10 per entità|
| **[Simple](./luis-concept-entity-types.md)| 100 entità|
| [Espressione][utterances] | 500 caratteri|
| [Espressioni][utterances] | 15.000 per ogni applicazione|
| [Nome della versione][luis-how-to-manage-versions] | 10 caratteri limitati a caratteri alfanumerici e punto (.) |

*Il numero max predefinito di caratteri è 50. 

* * Il numero totale di entità semplici, gerarchiche e composite non può essere superiore a 100. Il conteggio totale di entità gerarchiche, composite o semplici e di entità figlio gerarchiche non può superare 330. 

## <a name="intent-and-entity-naming"></a>Denominazione di finalità e entità
Non utilizzare i seguenti caratteri nei nomi delle entità e delle finalità:

|Character|NOME|
|--|--|
|`{`|Parentesi graffa aperta|
|`}`|Parentesi graffa chiusa|
|`[`|Parentesi quadra aperta|
|`]`|Parentesi quadra chiusa|
|`\`|Barra rovesciata|

## <a name="key-limits"></a>Limiti delle chiavi
La chiave di creazione presenta diversi limiti per creazione e endpoint. La chiave di sottoscrizione del servizio LUIS è valida solo per le query di endpoint.

|Chiave|Creazione|Endpoint|Scopo|
|--|--|--|--|
|Creazione/Starter|1 milione/mese, 5/secondo|1.000/mese, 5/secondo|Creazione di app LUIS|
|[Sottoscrizione] [ pricing] - F0 - livello gratuito |non valido|10.000/mese, 5/secondo|Esecuzione di query per l'endpoint LUIS|
|[Sottoscrizione] [ pricing] - S0 - livello Basic|non valido|50/secondo|Esecuzione di query per l'endpoint LUIS|
|[Integrazione dell'Analisi del sentiment](publishapp.md#enable-sentiment-analysis)|non valido|nessun addebito|Aggiunta di informazioni sentiment inclusa l'estrazione dei dati di frase chiave |
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
