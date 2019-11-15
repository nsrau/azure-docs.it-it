---
title: Che cos'è il servizio Language Understanding (LUIS)?
titleSuffix: Azure Cognitive Services
description: Language Understanding (LUIS) è un servizio API basato sul cloud che applica l'intelligence dell'apprendimento automatico personalizzato al testo in linguaggio naturale parlato di un utente per prevedere il significato generale ed estrarre informazioni rilevanti e dettagliate.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 11/04/2019
ms.author: diberry
ms.openlocfilehash: 8ee22a2a8a12eb85439e191bc21e6cf391bea3f8
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73612844"
---
# <a name="what-is-language-understanding-luis"></a>Che cos'è il servizio Language Understanding (LUIS)?

Language Understanding (LUIS) è un servizio API basato sul cloud che applica l'intelligence dell'apprendimento automatico personalizzato al testo in linguaggio naturale parlato di un utente per prevedere il significato generale ed estrarre informazioni rilevanti e dettagliate. 

Un'applicazione client per LUIS può essere una qualsiasi applicazione di linguaggio parlato che comunica con un utente in linguaggio naturale per completare un compito. Esempi di applicazioni client includono app di social media, chatbot e applicazioni per desktop con funzionalità vocali.  

![Immagine concettuale di tre applicazioni client che funzionano con LUIS (Language Understanding) di Servizi cognitivi](./media/luis-overview/luis-entry-point.png "Immagine concettuale di tre applicazioni client che funzionano con LUIS (Language Understanding) di Servizi cognitivi")

## <a name="use-luis-in-a-chat-bot"></a>Usare LUIS in un chatbot

<a name="Accessing-LUIS"></a>

Una volta pubblicata l'app LUIS, un'applicazione client invia le espressioni (testo) all'[API][endpoint-apis] dell'endpoint di elaborazione del linguaggio naturale LUIS e riceve i risultati come risposte JSON. Un'applicazione client comune per LUIS è un chatbot.


![Immagini concettuali di LUIS che funziona con un chatbot per prevedere il testo utente con la comprensione del linguaggio naturale (NLP)](./media/luis-overview/LUIS-chat-bot-request-response.svg "Immagini concettuali di LUIS che funziona con un chatbot per prevedere il testo utente con la comprensione del linguaggio naturale (NLP)")

|Passaggio|Azione|
|:--|:--|
|1|L'applicazione client invia l'_espressione_ dell'utente (testo con le sue parole), "I want to call my HR rep" (Voglio chiamare il mio responsabile RU) all'endpoint LUIS come richiesta HTTP.|
|2|LUIS consente di creare modelli linguistici personalizzati per aggiungere intelligence all'applicazione. I modelli linguistici basati su Machine Learning accettano il testo di input non strutturato dell'utente e restituiscono una risposta con formattazione JSON, con una finalità principale, `HRContact`. La risposta dell'endpoint JSON contiene almeno l'espressione della query e la finalità con il punteggio più alto. Può anche estrarre dati, ad esempio l'entità _Contact Type_.|
|3|L'applicazione client usa la risposta JSON per prendere decisioni su come soddisfare le richieste dell'utente. Queste decisioni possono includere un albero delle decisioni nel codice del framework del bot e le chiamate ad altri servizi. |

L'app LUIS fornisce intelligence in modo che l'applicazione client possa effettuare scelte intelligenti. LUIS non fornisce tali scelte. 

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-processing"></a>Elaborazione del linguaggio naturale

L'app LUIS contiene un modello linguistico naturale specifico del dominio. È possibile avviare l'app LUIS con un modello di dominio precompilato, creare il proprio o unire parti di un dominio precompilato con le proprie informazioni personalizzate.

* **Modello predefinito** Il servizio LUIS dispone di molti modelli di dominio predefiniti che includono finalità, espressioni ed entità predefinite. È possibile usare le entità predefinite senza dover usare le finalità e le espressioni del modello predefinito. I [modelli di dominio predefiniti](luis-how-to-use-prebuilt-domains.md) includono l'intero progetto e sono un ottimo modo per iniziare a usare rapidamente il servizio LUIS.

* **Modello personalizzato** Il servizio LUIS offre diversi modi per identificare i modelli personalizzati, comprese finalità ed entità. Le entità includono entità basate su Machine Learning, entità specifiche o letterali e una combinazione di entità basate su Machine Learning e letterali.

## <a name="build-the-luis-model"></a>Compilare il modello LUIS
Creare il modello con le API di [creazione](https://go.microsoft.com/fwlink/?linkid=2092087) o con il [portale LUIS](https://www.luis.ai).

Il modello LUIS inizia con le categorie di intenzioni dell'utente, denominate **[finalità](luis-concept-intent.md)** . Per ogni finalità sono necessari esempi di **[espressioni](luis-concept-utterance.md)** dell'utente. Ogni espressione può fornire dati che devono essere estratti. 

|Espressione utente di esempio|Finalità|Dati estratti|
|-----------|-----------|-----------|
|`Book a flight to __Seattle__?`|BookFlight (PrenotaVolo)|Seattle|
|`When does your store __open__?`|OrariePosizioneNegozio|apre|
|`Schedule a meeting at __1pm__ with __Bob__ in Distribution`|ProgrammaRiunione|13:00, Bob|

## <a name="query-prediction-endpoint"></a>Eseguire query sull'endpoint di stima

Dopo il training e la pubblicazione dell'app nell'endpoint, l'applicazione client invia le espressioni all'API dell'[endpoint](https://go.microsoft.com/fwlink/?linkid=2092356) di stima. L'API applica l'app all'espressione per l'analisi e risponde con i risultati della stima in un formato JSON.  

La risposta dell'endpoint JSON contiene almeno l'espressione della query e la finalità con il punteggio più alto. Può anche estrarre dati, ad esempio l'entità **Contact Type** seguente e il sentiment generale. 

```JSON
{
    "query": "I want to call my HR rep",
    "prediction": {
        "normalizedQuery": "i want to call my hr rep",
        "topIntent": "HRContact",
        "intents": {
            "HRContact": {
                "score": 0.8582669
            }
        },
        "entities": {
            "Contact Type": [
                "call"
            ]
        },
        "sentiment": {
            "label": "negative",
            "score": 0.103343368
        }
    }
}
```

## <a name="improve-model-prediction"></a>Migliorare la stima del modello

Dopo che l'app LUIS viene pubblicata e riceve espressioni utente reali, il servizio LUIS fornisce l'[apprendimento attivo](luis-concept-review-endpoint-utterances.md) delle espressioni dell'endpoint per rendere più precisa la stima. 

<a name="using-luis"></a>

## <a name="development-lifecycle"></a>Ciclo di vita di sviluppo
Il servizio LUIS fornisce strumenti, controllo delle versioni e collaborazione con altri autori LUIS per l'integrazione nell'intero [ciclo di vita di sviluppo](luis-concept-app-iteration.md). 

## <a name="implementing-luis"></a>Implementazione di LUIS
LUIS (Language Understanding), come API REST, può essere usato con qualsiasi prodotto, servizio o framework con una richiesta HTTP. L'elenco seguente contiene i principali prodotti e servizi Microsoft usati con LUIS.

L'applicazione client principale per LUIS è:
* [Bot app Web](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0) crea rapidamente un chatbot abilitato per LUIS per comunicare con un utente tramite input di testo. Usa [Bot Framework][bot-framework] versione [4.x](https://github.com/Microsoft/botbuilder-dotnet) per un'esperienza bot completa.

Strumenti per usare LUIS in modo rapido e semplice con un bot:
* [Interfaccia della riga di comando di LUIS](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) Il pacchetto NPM fornisce funzionalità di creazione e stima con uno strumento da riga di comando autonomo oppure sotto forma di importazione. 
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen) LUISGen è uno strumento per generare codice C# fortemente tipizzato e codice sorgente TypeScript da un modello LUIS esportato.
* [Dispatch](https://aka.ms/dispatch-tool) consente di usare diverse app LUIS e QnA Maker da un'app padre usando il modello dispatcher.
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) LUDown è uno strumento da riga di comando che consente di gestire i modelli linguistici per il bot.
* [Bot Framework - Composer](https://github.com/microsoft/BotFramework-Composer): uno strumento di sviluppo integrato che consente a sviluppatori e team multidisciplinari di creare bot ed esperienze conversazionali con Microsoft Bot Framework

Altri servizi cognitivi usati con LUIS:
* [QnA Maker][qnamaker] consente di combinare diversi tipi di testo in una knowledge base di domande e risposte.
* [Servizio Voce](../Speech-Service/overview.md) converte le richieste di linguaggio parlato in testo. 
* [Conversation Learner](https://docs.microsoft.com/azure/cognitive-services/labs/conversation-learner/overview) consente di creare più velocemente conversazioni bot con LUIS.

Esempi che usano LUIS:
* Repository GitHub di [intelligenza artificiale colloquiale](https://github.com/Microsoft/AI).
* [Bot Framework - Esempi di bot](https://github.com/microsoft/BotBuilder-Samples)

## <a name="next-steps"></a>Passaggi successivi

* [Novità](whats-new.md)
* Creare una nuova app LUIS con un dominio [precompilato](luis-get-started-create-app.md) o [personalizzato](luis-quickstart-intents-only.md).
* [Eseguire query sull'endpoint di stima](luis-get-started-get-intent-from-browser.md) di un'app IoT pubblica. 
* [Risorse per sviluppatori](developer-reference-resource.md) per LUIS. 

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/
