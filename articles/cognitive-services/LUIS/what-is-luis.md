---
title: Informazioni su Language Understanding (LUIS) - Servizi cognitivi di Azure | Microsoft Docs
description: Language Understanding (LUIS) è un servizio API basato sul cloud che applica l'intelligence dell'apprendimento automatico personalizzato al testo in linguaggio naturale parlato di un utente per prevedere il significato generale ed estrarre informazioni rilevanti e dettagliate.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 97632ad27a09451f47012dfb7bbc9d406cd37c89
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55879259"
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

Dopo che il modello LUIS è stato pubblicato e ha ricevuto espressioni utente reali, LUIS fornisce diversi metodi per migliorare l'accuratezza della stima: [apprendimento attivo](luis-concept-review-endpoint-utterances.md) di espressioni endpoint [elenchi di frasi](luis-concept-feature.md) per l'inclusione delle parole nel dominio e [modelli](luis-concept-patterns.md) per ridurre il numero di espressioni necessarie.

<a name="using-luis"></a>

## <a name="development-lifecycle"></a>Ciclo di vita di sviluppo
LUIS fornisce strumenti, controllo delle versioni e collaborazione con altri autori LUIS per l'integrazione nell'intero ciclo di vita dello sviluppo a livello dell'applicazione client e del modello linguistico. 

## <a name="implementing-luis"></a>Implementazione di LUIS
LUIS, come API REST, può essere usato con qualsiasi prodotto, servizio o framework che invia richieste HTTP. L'elenco seguente contiene i principali prodotti e servizi Microsoft usati con LUIS.

L'applicazione client principale per LUIS è:
* [Bot app Web](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-3.0) crea rapidamente un chatbot abilitato per LUIS per comunicare con un utente tramite input di testo. Usa [Bot Framework][bot-framework] versione [3.x](https://github.com/Microsoft/BotBuilder) o [4.x](https://github.com/Microsoft/botbuilder-dotnet) per un'esperienza bot completa.

Strumenti per usare LUIS in modo rapido e semplice con un bot:
* [Interfaccia della riga di comando di LUIS](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) Il pacchetto NPM fornisce funzionalità di creazione e previsione con uno strumento da riga di comando autonomo oppure sotto forma di importazione. 
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen) LUISGen è uno strumento per generare codice C# fortemente tipizzato e codice sorgente TypeScript da un modello LUIS esportato.
* [Dispatch](https://aka.ms/dispatch-tool) consente di usare diverse app LUIS e QnA Maker da un'app padre usando il modello dispatcher.
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) LUDown è uno strumento da riga di comando che consente di gestire i modelli linguistici per il bot.

Altri servizi cognitivi usati con LUIS:
* [QnA Maker][qnamaker] consente di combinare diversi tipi di testo in una base di conoscenze con domande e risposte.
* [API Controllo ortografico Bing](../bing-spell-check/proof-text.md) fornisce la correzione del testo prima della previsione. 
* [Servizio Voce](../Speech-Service/overview.md) converte le richieste di linguaggio parlato in testo. 
* [Conversation Learner](https://docs.microsoft.com/azure/cognitive-services/labs/conversation-learner/overview) consente di creare più velocemente conversazioni bot con LUIS.
* [Personality Chat](https://docs.microsoft.com/azure/cognitive-services/project-personality-chat/overview) per gestire conversazioni leggere dei bot.

Esempi che usano LUIS:
* Repository GitHub di [intelligenza artificiale colloquiale](https://github.com/Microsoft/AI).
* Esempi di Azure di [Language Understanding](https://github.com/Azure-Samples/cognitive-services-language-understanding)

## <a name="next-steps"></a>Passaggi successivi

Creare una nuova app LUIS con un dominio [precompilato](luis-get-started-create-app.md) o [personalizzato](luis-quickstart-intents-only.md). [Eseguire query sull'endpoint di stima](luis-get-started-cs-get-intent.md) di un'app IoT pubblica.

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://aka.ms/luis-authoring-api
[endpoint-apis]: https://aka.ms/luis-endpoint-apis
[qnamaker]: https://qnamaker.ai/
