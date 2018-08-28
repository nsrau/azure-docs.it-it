---
title: Informazioni su Language Understanding (LUIS) - Servizi cognitivi di Azure | Microsoft Docs
description: Language Understanding (LUIS) è un servizio API basato sul cloud che applica l'intelligence dell'apprendimento automatico personalizzato al testo in linguaggio naturale parlato di un utente per prevedere il significato generale ed estrarre informazioni rilevanti e dettagliate. Un'applicazione client per LUIS può essere una qualsiasi applicazione di linguaggio parlato che comunica con un utente in linguaggio naturale per completare un compito. Esempi di applicazioni client includono app di social media, chatbot e applicazioni per desktop con funzionalità vocali.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: overview
ms.date: 08/15/2018
ms.author: diberry
ms.openlocfilehash: e74abb30709f186d3c1139793cf34d3e033ff967
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/17/2018
ms.locfileid: "40191641"
---
# <a name="what-is-language-understanding-luis"></a>Che cos'è il servizio Language Understanding (LUIS)?

Language Understanding (LUIS) è un servizio API basato sul cloud che applica l'intelligence dell'apprendimento automatico personalizzato al testo in linguaggio naturale parlato di un utente per prevedere il significato generale ed estrarre informazioni rilevanti e dettagliate. 

Un'applicazione client per LUIS può essere una qualsiasi applicazione di linguaggio parlato che comunica con un utente in linguaggio naturale per completare un compito. Esempi di applicazioni client includono app di social media, chatbot e applicazioni per desktop con funzionalità vocali.  

![Immagine concettuale di 3 applicazioni client che operano con Language Understanding (LUIS) di Servizi cognitivi](./media/luis-overview/luis-entry-point.png "Immagine concettuale di 3 applicazioni client che operano con Language Understanding (LUIS) di Servizi cognitivi")

## <a name="use-luis-in-a-chat-bot"></a>Usare LUIS in un chatbot

<a name="Accessing-LUIS"></a>

Una volta pubblicata l'app LUIS, un'applicazione client invia le espressioni (testo) all'[API][endpoint-apis] dell'endpoint di elaborazione del linguaggio naturale LUIS e riceve i risultati come risposte JSON. Un'applicazione client comune per LUIS è un chatbot.


![Immagine concettuale di LUIS che opera con un chatbot per prevedere il testo dell'utente con l'elaborazione del linguaggio naturale (NLP)](./media/luis-overview/luis-overview-process-2.png "Immagine concettuale di LUIS che opera con un chatbot per prevedere il testo dell'utente con l'elaborazione del linguaggio naturale (NLP)")

|Passaggio|Azione|
|:--|:--|
|1|L'applicazione client invia l'_espressione_ dell'utente (testo con le sue parole), "I want to call my HR rep" (Voglio chiamare il mio responsabile RU) all'endpoint LUIS come richiesta HTTP.|
|2|LUIS si applica il modello appreso al testo in linguaggio naturale per fornire la comprensione intelligente dell'input dell'utente. LUIS restituisce una risposta in formato JSON, con una finalità principale, "HRContact" (Contatto RU). La risposta dell'endpoint JSON contiene almeno l'espressione della query e la finalità con il punteggio più alto. Può anche estrarre dati, ad esempio l'entità Contact Type.|
|3|L'applicazione client usa la risposta JSON per prendere decisioni su come soddisfare le richieste dell'utente. Queste decisioni possono includere un albero delle decisioni nel codice del framework del bot e le chiamate ad altri servizi. |

L'app LUIS fornisce intelligence in modo che l'applicazione client possa effettuare scelte intelligenti. LUIS non fornisce tali scelte. 

<!--

### Example of JSON endpoint response

The minimum JSON endpoint response contains the query utterance, and the top scoring intent. It can also extract data such as the following **Contact Type** entity. 

```JSON
{
  "query": "I want to call my HR rep.",
  "topScoringIntent": {
    "intent": "HRContact",
    "score": 0.921233
  },
  "entities": [
    {
      "entity": "call",
      "type": "Contact Type",
      "startIndex": 10,
      "endIndex": 13,
      "score": 0.7615982
    }
  ]
}
```
-->

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-processing"></a>Elaborazione del linguaggio naturale

Un'app LUIS contiene un modello di linguaggio naturale specifico per il dominio. È possibile avviare l'app LUIS con un modello di dominio precompilato, creare il proprio o unire parti di un dominio precompilato con le proprie informazioni personalizzate.

* **Modello predefinito** Il servizio LUIS dispone di molti modelli di dominio predefiniti che includono finalità, espressioni ed entità predefinite. È possibile usare le entità predefinite senza dover usare le finalità e le espressioni del modello predefinito. I [modelli di dominio predefiniti](luis-how-to-use-prebuilt-domains.md) includono l'intero progetto e sono un ottimo modo per iniziare a usare rapidamente il servizio LUIS.

* **Entità personalizzate** LUIS offre diversi modi per identificare le finalità e le entità personalizzate, comprese le entità apprese automaticamente, le entità specifiche o con valore letterale e una combinazione di entità apprese automaticamente e con valore letterale.

## <a name="build-the-luis-model"></a>Compilare il modello LUIS
Compilare il modello con le API di [creazione](https://aka.ms/luis-authoring-apis) o con il portale LUIS.

Il modello LUIS inizia con le categorie di intenzioni dell'utente, denominate **[finalità](luis-concept-intent.md)**. Per ogni finalità sono necessari esempi di **[espressioni](luis-concept-utterance.md)** dell'utente. Ogni espressione può fornire una serie di dati che devono essere estratti con le **[entità](luis-concept-entity-types.md)**. 

|Espressione utente di esempio|Finalità|Entità|
|-----------|-----------|-----------|
|"Prenota un volo per __Seattle__?"|BookFlight (PrenotaVolo)|Seattle|
|"Quando __apre__ il negozio?"|OrariePosizioneNegozio|apre|
|"Programma una riunione alle ore __13:00__ con __Bob__ presso la distribuzione"|ProgrammaRiunione|13:00, Bob|

<!--
## What is a natural language model?

A model begins with a list of general user intentions, called _intents_, such as "Book Flight" or "Contact Help Desk." You provide user's example text, called _example utterances_ for the intents. Then mark significant words or phrases in the utterance, called _entities_.


A model includes:

* **[intents](#intents)**: categories of user intentions (overall intended action or result)
* **[entities](#entities)**: specific types of data in utterances such as number, email, or name contained in text
* **[example utterances](#example-utterances)**: example text a user might enter in the client application

### Intents 

An [intent](luis-how-to-add-intents.md), short for _intention_, is a purpose or goal expressed in a user's utterance, such as booking a flight, paying a bill, or finding a news article. You create an intent for each action. A LUIS travel app may define an intent named "BookFlight." Each prediction query includes the top scored intent. 

The client application can use the top scoring intent to trigger an action. For example, when "BookFlight" intent is returned from LUIS, a client application could trigger an API call to an external service for booking a plane ticket.

### Entities

An [entity](luis-how-to-add-entities.md) represents detailed information found within the utterance that is relevant to the user's request. For example, in the utterance "Book a ticket to Paris",  a single ticket is requested, and "Paris" is a location. Two entities are found "a ticket" indicating a single ticket and "Paris" indicating the destination. 

After LUIS returns the entities found in the user’s utterance, the client application can use the list of entities as parameters to trigger an action. For example, booking a flight requires entities like the travel destination, date, and airline.
-->
<!--
### Example utterances

An example [utterance](luis-how-to-add-example-utterances.md) is text input from the user that the client application needs to understand. It may be a sentence, like "Book a ticket to Paris", or a fragment of a sentence, like "Booking" or "Paris flight." Utterances aren't always well-formed, and there can be many utterance variations for a particular intent. Add 10 to 20 example utterances to each intent and mark entities in every utterance.

|Example user utterance|Intent|Entities|
|-----------|-----------|-----------|
|"Book a flight to __Seattle__?"|BookFlight|Seattle|
|"When does your store __open__?"|StoreHoursAndLocation|open|
|"Schedule a meeting at __1pm__ with __Bob__ in Distribution"|ScheduleMeeting|1pm, Bob|
-->
## <a name="query-prediction-endpoint"></a>Eseguire query sull'endpoint di stima

Dopo la compilazione e la pubblicazione del modello nell'endpoint, l'applicazione client invia le espressioni all'API dell'[endpoint di stima](https://aka.ms/luis-endpoint-apis) pubblicato. L'API applica il modello al testo per l'analisi. L'API risponde con i risultati della stima in formato JSON.  

La risposta dell'endpoint JSON contiene almeno l'espressione della query e la finalità con il punteggio più alto. Può anche estrarre dati, ad esempio l'entità **Contact Type** seguente. 

```JSON
{
  "query": "I want to call my HR rep.",
  "topScoringIntent": {
    "intent": "HRContact",
    "score": 0.921233
  },
  "entities": [
    {
      "entity": "call",
      "type": "Contact Type",
      "startIndex": 10,
      "endIndex": 13,
      "score": 0.7615982
    }
  ]
}
```

## <a name="improve-model-prediction"></a>Migliorare la stima del modello

Dopo che il modello LUIS è stato pubblicato e ha ricevuto espressioni utente reali, LUIS fornisce diversi metodi per migliorare l'accuratezza della stima: [apprendimento attivo](#active-learning) di espressioni endpoint [elenchi di frasi](#phrase-lists) per l'inclusione delle parole nel dominio e [modelli](#patterns) per ridurre il numero di espressioni necessarie.
<!--
### Active learning

In the [active learning](luis-how-to-review-endoint-utt.md) process, LUIS allows you to adapt the LUIS app to real-world utterances by selecting utterances it received at the endpoint for your review. You can accept or correct the endpoint prediction, retrain, and republish. LUIS learns quickly with this iterative process, taking the minimum amount of your time and effort. 

### Phrase lists 

LUIS provides [phrases lists](luis-concept-feature.md) so you can indicate important words or phrases of the model. LUIS uses these lists to add additional significance to those words and phrases that would otherwise not be found in the model.

### Patterns 

Patterns allow you to simplify an intent's utterance collection into common [templates](luis-concept-patterns.md) of word choice and word order. This allows LUIS to learn quicker by needing fewer example utterances for the intents. Patterns are a hybrid system of regular expressions and machine-learned expressions. 
-->
<a name="using-luis"></a>
<!--
## Authoring and accessing models
Author LUIS from the [authoring](https://aka.ms/luis-authoring-apis) APIs or from the LUIS portal. Query the published prediction endpoint of the model from the [endpoint](https://aka.ms/luis-endpoint-apis) APIs.
-->

## <a name="integrating-with-luis"></a>Integrazione con LUIS
LUIS, come API REST, può essere usato con qualsiasi prodotto, servizio o framework che invia richieste HTTP. L'elenco seguente contiene i principali prodotti e servizi Microsoft usati con LUIS.

Le applicazioni client Microsoft per LUIS includono:
* [Bot app Web](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-3.0) crea rapidamente un chatbot abilitato per LUIS per comunicare con un utente tramite input di testo. Usa [Bot Framework][bot-framework] versione [3.x](https://github.com/Microsoft/BotBuilder) o [4.x](https://github.com/Microsoft/botbuilder-dotnet) per un'esperienza bot completa.
* [Windows Mixed Reality](https://docs.microsoft.com/windows/mixed-reality/) - scopri di più con questo [corso su Mixed Reality](https://docs.microsoft.com/windows/mixed-reality/mr-azure-303) con LUIS. 

Strumenti Microsoft per usare LUIS con un bot:
* [Dispatch](https://github.com/Microsoft/botbuilder-tools/tree/master/Dispatch) consente di usare diverse app LUIS e QnA Maker da un'app padre usando il modello dispatcher.
* [Conversation Learner](https://docs.microsoft.com/azure/cognitive-services/labs/conversation-learner/overview) consente di creare più velocemente conversazioni bot con LUIS.
* [Personality Chat](https://docs.microsoft.com/azure/cognitive-services/project-personality-chat/overview) per gestire conversazioni leggere dei bot.

Altri servizi cognitivi usati con LUIS:
* [QnA Maker][qnamaker] consente di combinare diversi tipi di testo in una base di conoscenze con domande e risposte.
* [API Controllo ortografico Bing](../bing-spell-check/proof-text.md) fornisce la correzione del testo prima della previsione. 
* [Servizio Voce](../Speech-Service/overview.md) converte le richieste di linguaggio parlato in testo. 



## <a name="next-steps"></a>Passaggi successivi

Creare una nuova app LUIS con un dominio [precompilato](luis-get-started-create-app.md) o [personalizzato](luis-quickstart-intents-only.md). [Eseguire query sull'endpoint di stima](luis-get-started-cs-get-intent.md) di un'app IoT pubblica.

<!-- Reference-style links -->
[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://aka.ms/luis-authoring-api
[endpoint-apis]: https://aka.ms/luis-endpoint-apis
[qnamaker]: https://qnamaker.ai/