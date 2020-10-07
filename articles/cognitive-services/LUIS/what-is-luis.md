---
title: Che cos'è il servizio Language Understanding (LUIS)?
description: Language Understanding (LUIS) è un servizio API basato sul cloud che applica il Machine Learning al linguaggio naturale delle conversazioni per stimare il significato ed estrarre le informazioni.
keywords: Azure, intelligenza artificiale, ia, elaborazione del linguaggio naturale, nlp, comprensione del linguaggio naturale, nlu, LUIS, IA conversazionale, chatbot di ia, ia nlp, azure luis
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 09/02/2020
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 242d131e79966ebdb286a20f75d20f91f5fa7406
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91334651"
---
# <a name="what-is-language-understanding-luis"></a>Che cos'è il servizio Language Understanding (LUIS)?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Language Understanding (LUIS) è un servizio di intelligenza artificiale conversazionale basato sul cloud che applica l'intelligenza di Machine Learning al testo in linguaggio naturale della conversazione di un utente per prevedere il significato generale ed estrarre informazioni pertinenti e dettagliate.

Un'applicazione client per LUIS può essere una qualsiasi applicazione di linguaggio parlato che comunica con un utente in linguaggio naturale per completare un compito. Esempi di applicazioni client includono app di social media, chatbot di IA e applicazioni per desktop con funzionalità vocali.

![Immagine concettuale di tre applicazioni client che funzionano con LUIS (Language Understanding) di Servizi cognitivi](./media/luis-overview/luis-entry-point.png "Immagine concettuale di tre applicazioni client che funzionano con LUIS (Language Understanding) di Servizi cognitivi")

## <a name="use-luis-in-a-chat-bot"></a>Usare LUIS in un chatbot

<a name="Accessing-LUIS"></a>

Una volta pubblicata l'app LUIS di Azure, un'applicazione client invia le espressioni (testo) all'[API][endpoint-apis] dell'endpoint di elaborazione del linguaggio naturale LUIS e riceve i risultati come risposte JSON. Un'applicazione client comune per LUIS è un chatbot.


![Immagini concettuali di LUIS che funziona con un chatbot per prevedere il testo utente con la comprensione del linguaggio naturale (NLP)](./media/luis-overview/LUIS-chat-bot-request-response.svg "Immagini concettuali di LUIS che funziona con un chatbot per prevedere il testo utente con la comprensione del linguaggio naturale (NLP)")

|Passaggio|Azione|
|:--|:--|
|1|L'applicazione client invia l'_espressione_ dell'utente (testo con le sue parole), "I want to call my HR rep" (Voglio chiamare il mio responsabile RU) all'endpoint LUIS come richiesta HTTP.|
|2|LUIS consente di creare modelli linguistici personalizzati per aggiungere intelligence all'applicazione. I modelli linguistici basati su Machine Learning accettano il testo di input non strutturato dell'utente e restituiscono una risposta con formattazione JSON, con una finalità principale, `HRContact`. La risposta dell'endpoint JSON contiene almeno l'espressione della query e la finalità con il punteggio più alto. Può anche estrarre dati, ad esempio l'entità _Contact Type_.|
|3|L'applicazione client usa la risposta JSON per prendere decisioni su come soddisfare le richieste dell'utente. Queste decisioni possono includere un albero delle decisioni nel codice del framework del bot e le chiamate ad altri servizi. |

L'app LUIS fornisce intelligence in modo che l'applicazione client possa effettuare scelte intelligenti. LUIS non fornisce tali scelte.

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-understanding-nlu"></a>Comprensione del linguaggio naturale

[LUIS fornisce intelligenza artificiale (IA)](artificial-intelligence.md "LUIS fornisce intelligenza artificiale (IA)") sotto forma di comprensione del linguaggio naturale (NLU, Natural Language Understanding), un sottoinsieme dell'intelligenza artificiale per l'elaborazione del linguaggio naturale.

L'app LUIS contiene un modello linguistico naturale specifico del dominio. È possibile avviare l'app LUIS con un modello di dominio precompilato, creare il proprio o unire parti di un dominio precompilato con le proprie informazioni personalizzate.

* **Modello predefinito** Il servizio LUIS dispone di molti modelli di dominio predefiniti che includono finalità, espressioni ed entità predefinite. È possibile usare le entità predefinite senza dover usare le finalità e le espressioni del modello predefinito. I [modelli di dominio predefiniti](luis-how-to-use-prebuilt-domains.md "Modelli di dominio predefiniti") includono l'intero progetto e sono un ottimo modo per iniziare a usare rapidamente il servizio LUIS.

* **Modello personalizzato** Il servizio LUIS offre diversi modi per identificare i modelli personalizzati, comprese finalità ed entità. Le entità includono entità di Machine Learning, entità specifiche o letterali e una combinazione di entità di Machine Learning e letterali.

Vedere altre informazioni sull'[IA NLP](artificial-intelligence.md "NLP") e sull'area di NLU specifica di LUIS.

## <a name="step-1-design-and-build-your-model"></a>Passaggio 1: Progettare e compilare il modello

Progettare il modello con categorie di intenzioni dell'utente denominate **[finalità](luis-concept-intent.md "intents")** . Per ogni finalità sono necessari esempi di **[espressioni](luis-concept-utterance.md "espressioni")** dell'utente. Ogni espressione può fornire dati che devono essere estratti con le [entità di Machine Learning](luis-concept-entity-types.md#effective-machine-learned-entities "entità di Machine Learning").

|Espressione utente di esempio|Finalità|Dati estratti|
|-----------|-----------|-----------|
|`Book a flight to Seattle?`|BookFlight (PrenotaVolo)|Seattle|
|`When does your store open?`|OrariePosizioneNegozio|apre|
|`Schedule a meeting at 1pm with Bob in Distribution`|ProgrammaRiunione|13:00, Bob|

Compilare il modello con le API di [creazione](https://go.microsoft.com/fwlink/?linkid=2092087 "creazione"), con il **[portale LUIS](https://www.luis.ai "Portale LUIS")** o con entrambi. Altre informazioni su come creare modelli con il [portale](get-started-portal-build-app.md "portal") e le [librerie client SDK](azure-sdk-quickstart.md "librerie client SDK").

## <a name="step-2-get-the-query-prediction"></a>Passaggio 2: Ottenere la stima della query

Dopo il training e la pubblicazione del modello dell'app nell'endpoint, un'applicazione client (ad esempio un chatbot) invia le espressioni all'API dell'[endpoint](https://go.microsoft.com/fwlink/?linkid=2092356 "endpoint") di stima. L'API applica il modello all'espressione per l'analisi e risponde con i risultati della stima in un formato JSON.

La risposta dell'endpoint JSON contiene almeno l'espressione della query e la finalità con il punteggio più alto. Può anche estrarre dati, ad esempio l'entità **Contact Type** seguente e il sentiment generale.

```JSON
{
    "query": "I want to call my HR rep",
    "prediction": {
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
            "label": "neutral",
            "score": 0.5
        }
    }
}
```

## <a name="step-3-improve-model-prediction"></a>Passaggio 3: Migliorare la stima del modello

Dopo che l'app LUIS viene pubblicata e riceve espressioni utente reali, il servizio LUIS fornisce l'[apprendimento attivo](luis-concept-review-endpoint-utterances.md "apprendimento attivo") delle espressioni dell'endpoint per rendere più precisa la stima. Esaminare questi suggerimenti come parte delle regolari attività di manutenzione nel ciclo di vita di sviluppo.

<a name="using-luis"></a>

## <a name="development-lifecycle-and-tools"></a>Ciclo di vita di sviluppo e relativi strumenti
Il servizio LUIS fornisce strumenti, controllo delle versioni e collaborazione con altri autori LUIS per l'integrazione nell'intero [ciclo di vita di sviluppo](luis-concept-app-iteration.md "ciclo di vita dello sviluppo").

LUIS (Language Understanding), come API REST, può essere usato con qualsiasi prodotto, servizio o framework con una richiesta HTTP. LUIS fornisce anche librerie client (SDK) per molti dei principali linguaggi di programmazione. Altre informazioni sulle [risorse di sviluppo](developer-reference-resource.md "risorse per sviluppatori") disponibili.

Strumenti per usare LUIS in modo rapido e semplice con un bot:
* [Interfaccia della riga di comando di LUIS](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS "interfaccia della riga di comando di LUIS") Il pacchetto NPM fornisce funzionalità di creazione e stima con uno strumento da riga di comando autonomo oppure sotto forma di importazione.
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen "LUISGen") LUISGen è uno strumento per generare codice C# fortemente tipizzato e codice sorgente TypeScript da un modello LUIS esportato.
* [Dispatch](https://aka.ms/dispatch-tool "Dispatch") consente di usare diverse app LUIS e QnA Maker da un'app padre usando il modello dispatcher.
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown "LUDown") LUDown è uno strumento da riga di comando che consente di gestire i modelli linguistici per il bot.

## <a name="integrate-with-a-bot"></a>Eseguire l'integrazione con un bot

Usare il [servizio Azure Bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0 "servizio Azure Bot") con [Microsoft Bot Framework](https://dev.botframework.com/ "Microsoft Bot Framework") per compilare e distribuire un chatbot. Per la progettazione e lo sviluppo, usare lo strumento con interfaccia grafica, [Composer](https://docs.microsoft.com/composer/ "Composer") o gli [esempi di bot](https://github.com/microsoft/BotBuilder-Samples "uso di esempi di bot") progettati per i principali scenari di bot.

## <a name="integrate-with-other-cognitive-services"></a>Eseguire l'integrazione con altri servizi cognitivi

Altri servizi cognitivi usati con LUIS:
* [QnA Maker](../QnAMaker/overview/overview.md "QnA Maker") consente di combinare diversi tipi di testo in una knowledge base di domande e risposte.
* [Servizio Voce](../Speech-Service/overview.md "Servizio Voce") converte le richieste di linguaggio parlato in testo.

Tra le risorse LUIS sono incluse anche funzionalità di Analisi del testo. Queste funzionalità includono l'[analisi del sentiment](luis-how-to-publish-app.md#configuring-publish-settings "analisi del sentiment") e l'[estrazione frasi chiave](luis-reference-prebuilt-keyphrase.md "estrazione di frasi chiave") con l'entità predefinita keyPhrase.

## <a name="learn-with-the-quickstarts"></a>Apprendere con le guide di avvio rapido

Per familiarizzare con LUIS, usare le guide di avvio rapido pratiche mediante il [portale](get-started-portal-build-app.md "portal") e le [librerie client SDK](azure-sdk-quickstart.md "librerie client SDK").


## <a name="next-steps"></a>Passaggi successivi

* [Novità](whats-new.md "Novità") del servizio e documentazione
* [Pianificare l'app](luis-how-plan-your-app.md "Pianificare l'app") con [finalità](luis-concept-intent.md "intents") ed [entità](luis-concept-entity-types.md "entities").
* [Eseguire query sull'endpoint di stima](luis-get-started-get-intent-from-browser.md "Eseguire query sull'endpoint di previsione").
* [Risorse per sviluppatori](developer-reference-resource.md "Risorse per sviluppatori") per LUIS.

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/
