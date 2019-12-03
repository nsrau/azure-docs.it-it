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
ms.date: 11/22/2019
ms.author: diberry
ms.openlocfilehash: 99f312521727658788e96a57b619a7c0e3d4751b
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456559"
---
# <a name="what-is-language-understanding-luis"></a>Che cos'è il servizio Language Understanding (LUIS)?

Language Understanding (LUIS) è un servizio API basato sul cloud che applica l'intelligence dell'apprendimento automatico personalizzato al testo in linguaggio naturale per prevedere il significato generale ed estrarre informazioni rilevanti e dettagliate. 

Ad esempio, quando un'applicazione client invia il testo `find me a wireless keyboard for $30`, LUIS risponde con il seguente oggetto JSON. 

```JSON
{
    "query": "find me a wireless keyboard for $30",
    "prediction": {
        "topIntent": "Finditem",
        "intents": {
            "Finditem": {
                "score": 0.934672
            }
        },
        "entities": {
            "item": [
                "wireless keyboard"
            ],
            "money": [
        {
            "number": 30,
            "units": "Dollar"
        }
           ]
        }
        
    }
}
```
Nell'esempio precedente la _**finalità**_ o il significato generale della frase è che l'utente sta tentando di trovare un elemento. Le informazioni dettagliate estratte de LUIS vengono chiamate _**entità**_ . In questo caso, le entità sono il nome dell'elemento che l'utente sta cercando e la quantità di denaro che si vuole spendere.

Le applicazioni client usano il codice JSON restituito da LUIS, la _finalità_ (categoria) e le _entità_ (informazioni dettagliate estratte) per guidare le azioni nell'applicazione client. Un'applicazione client per LUIS è spesso un'applicazione di linguaggio parlato che comunica con un utente in linguaggio naturale per completare un compito. Esempi di applicazioni client includono app di social media, chatbot e applicazioni per desktop con funzionalità vocali. 

![Immagine concettuale di tre applicazioni client che funzionano con LUIS (Language Understanding) di Servizi cognitivi](./media/luis-overview/luis-entry-point.png "Immagine concettuale di tre applicazioni client che funzionano con LUIS (Language Understanding) di Servizi cognitivi")

## <a name="example-use-luis-in-a-chat-bot"></a>Esempio dell'uso di LUIS in un chatbot

<a name="Accessing-LUIS"></a>

Un'applicazione client invia le espressioni (testo) all'[API][endpoint-apis] dell'endpoint di elaborazione del linguaggio naturale LUIS pubblicata e riceve i risultati come risposte JSON. Un'applicazione client comune per LUIS è un chatbot.


![Immagini concettuali di LUIS che funziona con un chatbot per prevedere il testo utente con la comprensione del linguaggio naturale (NLP)](./media/luis-overview/LUIS-chat-bot-request-response.svg "Immagini concettuali di LUIS che funziona con un chatbot per prevedere il testo utente con la comprensione del linguaggio naturale (NLP)")

|Passaggio|Azione|
|:--|:--|
|1|L'applicazione client invia l'_espressione_ dell'utente (testo con le sue parole), "I want to call my HR rep" (Voglio chiamare il mio responsabile RU) all'endpoint LUIS come richiesta HTTP.|
|2|LUIS applica i modelli linguistici basati su Machine Learning al testo di input non strutturato dell'utente e restituiscono una risposta con formattazione JSON, con una finalità principale, `HRContact`. La risposta dell'endpoint JSON contiene almeno l'espressione della query e la finalità con il punteggio più alto. Può anche estrarre dati, ad esempio l'entità _Contact Type_.|
|3|L'applicazione client usa la risposta JSON per prendere decisioni su come soddisfare le richieste dell'utente. Queste decisioni possono includere un albero delle decisioni nel bot e le chiamate ad altri servizi. |

L'app LUIS fornisce intelligence in modo che l'applicazione client possa effettuare scelte intelligenti. LUIS non fornisce tali scelte. 

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-processing"></a>Elaborazione del linguaggio naturale

L'app LUIS contiene modelli linguistici naturali specifici del dominio che interagiscono tra di loro. È possibile avviare l'app LUIS con uno o più modelli predefiniti, creare il proprio modello o unire i modelli predefiniti con le proprie informazioni personalizzate.

* **Modello predefinito** LUIS dispone di molti domini predefiniti che includono modelli di entità e finalità che interagiscono tra loro per completare scenari di utilizzo comuni. Questi domini includono espressioni etichettate che possono essere ispezionate e modificate, consentendone la personalizzazione. I [modelli di dominio predefiniti](luis-how-to-use-prebuilt-domains.md) includono l'intero progetto e sono un ottimo modo per iniziare a usare rapidamente il servizio LUIS. Sono inoltre presenti entità predefinite, ad esempio valuta e numero, che è possibile usare in modo indipendente dai domini predefiniti.

* **Modello personalizzato** Il servizio LUIS offre diversi modi per creare i modelli personalizzati, comprese finalità ed entità. Le entità includono entità basate su Machine Learning, entità con criteri di ricerca e una combinazione di entità basate su Machine Learning ed entità con criteri di ricerca.

## <a name="build-the-luis-app"></a>Compilare l'app LUIS
Compilare l'app con le API di [creazione](https://go.microsoft.com/fwlink/?linkid=2092087) o con il [portale LUIS](https://www.luis.ai).

L'app LUIS inizia con le categorie di testo di input denominate **[finalità](luis-concept-intent.md)** . Per ogni finalità sono necessari esempi di **[espressioni](luis-concept-utterance.md)** dell'utente. Ogni espressione può fornire dati che devono essere estratti. 

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

## <a name="iterative-development-lifecycle"></a>Ciclo di vita di sviluppo iterativo
Il servizio LUIS fornisce strumenti, controllo delle versioni e collaborazione con altri autori LUIS per l'integrazione nell'intero [ciclo di vita di sviluppo](luis-concept-app-iteration.md) iterativo. 

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
* Creare una nuova app LUIS con un dominio [predefinito](luis-get-started-create-app.md) o [personalizzato](luis-quickstart-intents-only.md)
* [Eseguire query sull'endpoint di stima](luis-get-started-get-intent-from-browser.md) di un'app IoT pubblica. 
* [Risorse per sviluppatori](developer-reference-resource.md) per LUIS. 

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/
