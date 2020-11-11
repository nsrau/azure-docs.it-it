---
title: 'Avvio rapido: Libreria client di Analisi del testo v3 per Node.js | Microsoft Docs'
description: Introduzione alla libreria client di Analisi del testo v3 per Node.js.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 10/07/2020
ms.author: aahi
ms.reviewer: sumeh, assafi
ms.custom: devx-track-js
ms.openlocfilehash: 60b7a87b4673fd1f5b1d95e5e2835878c263e784
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94371631"
---
<a name="HOLTop"></a>

# <a name="version-31-preview"></a>[Versione 3.1-preview](#tab/version-3-1)

[Documentazione di riferimento v3](/javascript/api/overview/azure/ai-text-analytics-readme) | [Codice sorgente della libreria v3](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics) | [Pacchetto (NPM) v3](https://www.npmjs.com/package/@azure/ai-text-analytics) | [Esempi v3](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)


# <a name="version-30"></a>[Versione 3.0](#tab/version-3)

[Documentazione di riferimento v3](/javascript/api/overview/azure/ai-text-analytics-readme) | [Codice sorgente della libreria v3](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics) | [Pacchetto (NPM) v3](https://www.npmjs.com/package/@azure/ai-text-analytics) | [Esempi v3](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)


# <a name="version-21"></a>[Versione 2.1](#tab/version-2)

[Documentazione di riferimento v2](/javascript/api/@azure/cognitiveservices-textanalytics) | [Codice sorgente della libreria v2](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesTextAnalytics) | [Pacchetto (NPM) v2](https://www.npmjs.com/package/@azure/cognitiveservices-textanalytics) | [Esempi v2](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/)

---

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services)
* Versione corrente di [Node.js](https://nodejs.org/).
* Dopo aver creato la sottoscrizione di Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="creare una risorsa di Analisi del testo"  target="_blank">creare una risorsa di Analisi del testo <span class="docon docon-navigate-external x-hidden-focus"></span></a> nel portale di Azure per ottenere la chiave e l'endpoint. Al termine della distribuzione, fare clic su **Vai alla risorsa**.
    * La chiave e l'endpoint della risorsa creata sono necessari per connettere l'applicazione all'API Analisi del testo. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
    * È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.

## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-nodejs-application"></a>Creare una nuova applicazione Node.js

In una finestra della console, ad esempio cmd, PowerShell o Bash, creare e passare a una nuova directory per l'app. 

```console
mkdir myapp 

cd myapp
```

Eseguire il comando `npm init` per creare un'applicazione Node con un file `package.json`. 

```console
npm init
```
### <a name="install-the-client-library"></a>Installare la libreria client

# <a name="version-31-preview"></a>[Versione 3.1-preview](#tab/version-3-1)

Installare i pacchetti NPM `@azure/ai-text-analytics`:

```console
npm install --save @azure/ai-text-analytics@5.1.0-beta.1
```

> [!TIP]
> Si vuole visualizzare l'intero file di codice dell'argomento di avvio rapido? È possibile trovarlo [in GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/TextAnalytics/text-analytics-v3-client-library.js), che contiene gli esempi di codice di questo argomento. 


# <a name="version-30"></a>[Versione 3.0](#tab/version-3)

Installare i pacchetti NPM `@azure/ai-text-analytics`:

```console
npm install --save @azure/ai-text-analytics@5.0.0
```

> [!TIP]
> Si vuole visualizzare l'intero file di codice dell'argomento di avvio rapido? È possibile trovarlo [in GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/TextAnalytics/text-analytics-v3-client-library.js), che contiene gli esempi di codice di questo argomento. 

# <a name="version-21"></a>[Versione 2.1](#tab/version-2)

Installare i pacchetti NPM `@azure/cognitiveservices-textanalytics`:

```console
npm install --save @azure/cognitiveservices-textanalytics
```

> [!TIP]
> Si vuole visualizzare l'intero file di codice dell'argomento di avvio rapido? È possibile trovarlo [in GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/textAnalytics.js), che contiene gli esempi di codice di questo argomento. 

---

Il file `package.json` dell'app viene aggiornato con le dipendenze.
Creare un file denominato `index.js` e aggiungere quanto segue:

# <a name="version-31-preview"></a>[Versione 3.1-preview](#tab/version-3-1)

```javascript
"use strict";

const { TextAnalyticsClient, AzureKeyCredential } = require("@azure/ai-text-analytics");
```

# <a name="version-30"></a>[Versione 3.0](#tab/version-3)

```javascript
"use strict";

const { TextAnalyticsClient, AzureKeyCredential } = require("@azure/ai-text-analytics");
```

# <a name="version-21"></a>[Versione 2.1](#tab/version-2)

```javascript
"use strict";

const { TextAnalyticsClient, CognitiveServicesCredential } = require("@azure/cognitiveservices-textanalytics");
```
---

Creare le variabili per l'endpoint e la chiave di Azure della risorsa.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```javascript
const key = '<paste-your-text-analytics-key-here>';
const endpoint = '<paste-your-text-analytics-endpoint-here>';
```

## <a name="object-model"></a>Modello a oggetti

Il client di Analisi del testo è un oggetto `TextAnalyticsClient` che esegue l'autenticazione in Azure tramite la chiave. Il client fornisce diversi metodi per l'analisi del testo, come singola stringa o batch.

Il testo viene inviato all'API come elenco di `documents`, che sono oggetti `dictionary` contenenti una combinazione di attributi `id`, `text` e `language`, a seconda del metodo usato. L'attributo `text` archivia il testo da analizzare in base all'attributo `language` di origine, mentre `id` può essere un valore qualsiasi. 

L'oggetto risposta è un elenco contenente le informazioni di analisi per ogni documento. 

## <a name="code-examples"></a>Esempi di codice

* [Autenticazione client](#client-authentication)
* [Analisi del sentiment](#sentiment-analysis) 
* [Opinion mining](#opinion-mining)
* [Rilevamento della lingua](#language-detection)
* [Riconoscimento di entità denominate](#named-entity-recognition-ner)
* [Collegamento di entità](#entity-linking)
* Informazioni personali
* [Estrazione delle frasi chiave](#key-phrase-extraction)

## <a name="client-authentication"></a>Client Authentication (Autenticazione client)

# <a name="version-31-preview"></a>[Versione 3.1-preview](#tab/version-3-1)

Creare un nuovo oggetto `TextAnalyticsClient` con la chiave e l'endpoint come parametri.

```javascript
const textAnalyticsClient = new TextAnalyticsClient(endpoint,  new AzureKeyCredential(key));
```

# <a name="version-30"></a>[Versione 3.0](#tab/version-3)

Creare un nuovo oggetto `TextAnalyticsClient` con la chiave e l'endpoint come parametri.

```javascript
const textAnalyticsClient = new TextAnalyticsClient(endpoint,  new AzureKeyCredential(key));
```

# <a name="version-21"></a>[Versione 2.1](#tab/version-2)

Creare un nuovo oggetto [TextAnalyticsClient](/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient) con `credentials` e `endpoint` come parametri.

[!code-javascript[Authentication and client creation](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=authentication)]

---

## <a name="sentiment-analysis"></a>Analisi del sentiment

# <a name="version-31-preview"></a>[Versione 3.1-preview](#tab/version-3-1)

Creare una matrice di stringhe contenente il documento da analizzare. Chiamare il metodo `analyzeSentiment()` del client e ottenere l'oggetto `SentimentBatchResult` restituito. Scorrere l'elenco dei risultati e stampare l'ID di ogni documento, il sentiment a livello di documento con i punteggi di attendibilità. Per ogni documento, il risultato contiene il sentiment a livello di frase oltre a scostamenti, lunghezza e punteggi di attendibilità.

```javascript
async function sentimentAnalysis(client){

    const sentimentInput = [
        "I had the best day of my life. I wish you were there with me."
    ];
    const sentimentResult = await client.analyzeSentiment(sentimentInput);

    sentimentResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.confidenceScores.positive.toFixed(2)} \tNegative: ${document.confidenceScores.negative.toFixed(2)} \tNeutral: ${document.confidenceScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.confidenceScores.positive.toFixed(2)} \tNegative: ${sentence.confidenceScores.negative.toFixed(2)} \tNeutral: ${sentence.confidenceScores.neutral.toFixed(2)}`);
        });
    });
}
sentimentAnalysis(textAnalyticsClient)
```

Eseguire il codice con `node index.js` nella finestra della console.

### <a name="output"></a>Output

```console
ID: 0
        Document Sentiment: positive
        Document Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
        Sentences Sentiment(2):
                Sentence sentiment: positive
                Sentences Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
                Sentence sentiment: neutral
                Sentences Scores:
                Positive: 0.21  Negative: 0.02  Neutral: 0.77
```

### <a name="opinion-mining"></a>Opinion mining

Per eseguire l'analisi del sentiment con la funzionalità di opinion mining, creare una matrice di stringhe contenente il documento da analizzare. Chiamare il metodo `analyzeSentiment()` del client aggiungendo il flag di opzione `includeOpinionMining: true` e ottenere l'oggetto `SentimentBatchResult` restituito. Scorrere l'elenco dei risultati e stampare l'ID di ogni documento, il sentiment a livello di documento con i punteggi di attendibilità. Il risultato per ogni documento contiene non solo il sentiment a livello di frase ma anche il sentiment a livello di aspetto e opinione.

```javascript
async function sentimentAnalysisWithOpinionMining(client){

    const sentimentInput = [
        {
            text: "The food and service were unacceptable, but the concierge were nice",
            id: "0",
            language: "en"
        }
    ];
    const sentimentResult = await client.analyzeSentiment(sentimentInput, { includeOpinionMining: true });

    sentimentResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.confidenceScores.positive.toFixed(2)} \tNegative: ${document.confidenceScores.negative.toFixed(2)} \tNeutral: ${document.confidenceScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.confidenceScores.positive.toFixed(2)} \tNegative: ${sentence.confidenceScores.negative.toFixed(2)} \tNeutral: ${sentence.confidenceScores.neutral.toFixed(2)}`);
            console.log("    Mined opinions");
            for (const { aspect, opinions } of sentence.minedOpinions) {
                console.log(`      - Aspect text: ${aspect.text}`);
                console.log(`        Aspect sentiment: ${aspect.sentiment}`);
                console.log("        Aspect confidence scores:", aspect.confidenceScores);
                console.log("        Aspect opinions");
                for (const { text, sentiment } of opinions) {
                console.log(`        - Text: ${text}`);
                console.log(`          Sentiment: ${sentiment}`);
                }
            }
        });
    });
}
sentimentAnalysisWithOpinionMining(textAnalyticsClient)
```

Eseguire il codice con `node index.js` nella finestra della console.

### <a name="output"></a>Output

```console
ID: 0
        // Document Sentiment: positive
        // Document Scores:
                // Positive: 0.84  Negative: 0.16  Neutral: 0.00
        // Sentences Sentiment(1):
                // Sentence sentiment: positive
                // Sentences Scores:
                // Positive: 0.84  Negative: 0.16  Neutral: 0.00
    // Mined opinions
      // - Aspect text: food
        // Aspect sentiment: negative
        // Aspect confidence scores: { positive: 0.01, negative: 0.99 }
        // Aspect opinions
        // - Text: unacceptable
          // Sentiment: negative
      // - Aspect text: service
        // Aspect sentiment: negative
        // Aspect confidence scores: { positive: 0.01, negative: 0.99 }
        // Aspect opinions
        // - Text: unacceptable
          // Sentiment: negative
      // - Aspect text: concierge
        // Aspect sentiment: positive
        // Aspect confidence scores: { positive: 1, negative: 0 }
        // Aspect opinions
        // - Text: nice
          // Sentiment: positive
```

# <a name="version-30"></a>[Versione 3.0](#tab/version-3)

Creare una matrice di stringhe contenente il documento da analizzare. Chiamare il metodo `analyzeSentiment()` del client e ottenere l'oggetto `SentimentBatchResult` restituito. Scorrere l'elenco dei risultati e stampare l'ID di ogni documento, il sentiment a livello di documento con i punteggi di attendibilità. Per ogni documento, il risultato contiene il sentiment a livello di frase oltre a scostamenti, lunghezza e punteggi di attendibilità.

```javascript
async function sentimentAnalysis(client){

    const sentimentInput = [
        "I had the best day of my life. I wish you were there with me."
    ];
    const sentimentResult = await client.analyzeSentiment(sentimentInput);

    sentimentResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.confidenceScores.positive.toFixed(2)} \tNegative: ${document.confidenceScores.negative.toFixed(2)} \tNeutral: ${document.confidenceScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.confidenceScores.positive.toFixed(2)} \tNegative: ${sentence.confidenceScores.negative.toFixed(2)} \tNeutral: ${sentence.confidenceScores.neutral.toFixed(2)}`);
        });
    });
}
sentimentAnalysis(textAnalyticsClient)
```

Eseguire il codice con `node index.js` nella finestra della console.

### <a name="output"></a>Output

```console
ID: 0
        Document Sentiment: positive
        Document Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
        Sentences Sentiment(2):
                Sentence sentiment: positive
                Sentences Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
                Sentence sentiment: neutral
                Sentences Scores:
                Positive: 0.21  Negative: 0.02  Neutral: 0.77
```

# <a name="version-21"></a>[Versione 2.1](#tab/version-2)

Creare un elenco di oggetti dizionario contenenti i documenti da analizzare. Chiamare il metodo [sentiment()](/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#sentiment-models-textanalyticsclientsentimentoptionalparams-) del client e ottenere l'oggetto [SentimentBatchResult](/javascript/api/@azure/cognitiveservices-textanalytics/sentimentbatchresult) restituito. Eseguire l'iterazione attraverso l'elenco dei risultati e stampare l'ID di ogni documento e il punteggio del sentiment. I punteggi più vicini a 0 indicano un sentiment negativo, mentre quelli più vicini a 1 indicano un sentiment positivo.

[!code-javascript[Sentiment analysis](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=sentimentAnalysis)]

Eseguire il codice con `node index.js` nella finestra della console.

### <a name="output"></a>Output

```console
[ { id: '1', score: 0.87 } ]
[ { id: '2', score: 0.11 } ]
[ { id: '3', score: 0.44 } ]
[ { id: '4', score: 1.00 } ]
```

---

## <a name="language-detection"></a>Rilevamento della lingua

# <a name="version-31-preview"></a>[Versione 3.1-preview](#tab/version-3-1)

Creare una matrice di stringhe contenente il documento da analizzare. Chiamare il metodo `detectLanguage()` del client e ottenere l'oggetto `DetectLanguageResultCollection` restituito. Scorrere quindi i risultati e stampare l'ID di ogni documento con la rispettiva lingua principale.

```javascript
async function languageDetection(client) {

    const languageInputArray = [
        "Ce document est rédigé en Français."
    ];
    const languageResult = await client.detectLanguage(languageInputArray);

    languageResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tPrimary Language ${document.primaryLanguage.name}`)
    });
}
languageDetection(textAnalyticsClient);
```

Eseguire il codice con `node index.js` nella finestra della console.

### <a name="output"></a>Output

```console
ID: 0
        Primary Language French
```

# <a name="version-30"></a>[Versione 3.0](#tab/version-3)

Creare una matrice di stringhe contenente il documento da analizzare. Chiamare il metodo `detectLanguage()` del client e ottenere l'oggetto `DetectLanguageResultCollection` restituito. Scorrere quindi i risultati e stampare l'ID di ogni documento con la rispettiva lingua principale.

```javascript
async function languageDetection(client) {

    const languageInputArray = [
        "Ce document est rédigé en Français."
    ];
    const languageResult = await client.detectLanguage(languageInputArray);

    languageResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tPrimary Language ${document.primaryLanguage.name}`)
    });
}
languageDetection(textAnalyticsClient);
```

Eseguire il codice con `node index.js` nella finestra della console.

### <a name="output"></a>Output

```console
ID: 0
        Primary Language French
```

# <a name="version-21"></a>[Versione 2.1](#tab/version-2)

Creare un elenco di oggetti dizionario contenenti i documenti. Chiamare il metodo [detectLanguage()](/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#detectlanguage-models-textanalyticsclientdetectlanguageoptionalparams-) del client e ottenere l'oggetto [LanguageBatchResult](/javascript/api/@azure/cognitiveservices-textanalytics/languagebatchresult) restituito. Quindi eseguire l'iterazione attraverso i risultati e stampare l'ID di ogni documento e la lingua.

[!code-javascript[Language detection](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=languageDetection)]

Eseguire il codice con `node index.js` nella finestra della console.

### <a name="output"></a>Output

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

---

## <a name="named-entity-recognition-ner"></a>Riconoscimento delle entità denominate (NER)

# <a name="version-31-preview"></a>[Versione 3.1-preview](#tab/version-3-1)

> [!NOTE]
> Nella versione `3.1`:
> * Il collegamento di entità è una richiesta distinta rispetto al riconoscimento di entità denominate.

Creare una matrice di stringhe contenente il documento da analizzare. Chiamare il metodo `recognizeEntities()` del client e ottenere l'oggetto `RecognizeEntitiesResult` restituito. Scorrere l'elenco dei risultati e stampare il nome, il tipo, il sottotipo, lo scostamento, la lunghezza e il punteggio dell'entità.

```javascript
async function entityRecognition(client){

    const entityInputs = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800",
        "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."
    ];
    const entityResults = await client.recognizeEntities(entityInputs);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tCategory: ${entity.category} \tSubcategory: ${entity.subCategory ? entity.subCategory : "N/A"}`);
            console.log(`\tScore: ${entity.confidenceScore}`);
        });
    });
}
entityRecognition(textAnalyticsClient);
```

Eseguire il codice con `node index.js` nella finestra della console.

### <a name="output"></a>Output

```console
Document ID: 0
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Score: 0.29
        Name: Bill Gates        Category: Person        Subcategory: N/A
        Score: 0.78
        Name: Paul Allen        Category: Person        Subcategory: N/A
        Score: 0.82
        Name: April 4, 1975     Category: DateTime      Subcategory: Date
        Score: 0.8
        Name: 8800      Category: Quantity      Subcategory: Number
        Score: 0.8
Document ID: 1
        Name: 21        Category: Quantity      Subcategory: Number
        Score: 0.8
        Name: Seattle   Category: Location      Subcategory: GPE
        Score: 0.25
```

### <a name="entity-linking"></a>Collegamento delle entità

Creare una matrice di stringhe contenente il documento da analizzare. Chiamare il metodo `recognizeLinkedEntities()` del client e ottenere l'oggetto `RecognizeLinkedEntitiesResult` restituito. Scorrere l'elenco dei risultati e stampare il nome, l'ID, l'origine dati, l'URL e le corrispondenze dell'entità. Ogni oggetto della matrice `matches` conterrà lo scostamento, la lunghezza e il punteggio corrispondenti.

```javascript
async function linkedEntityRecognition(client){

    const linkedEntityInput = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800. During his career at Microsoft, Gates held the positions of chairman, chief executive officer, president and chief software architect, while also being the largest individual shareholder until May 2014."
    ];
    const entityResults = await client.recognizeLinkedEntities(linkedEntityInput);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.name} \tID: ${entity.dataSourceEntityId} \tURL: ${entity.url} \tData Source: ${entity.dataSource}`);
            console.log(`\tMatches:`)
            entity.matches.forEach(match => {
                console.log(`\t\tText: ${match.text} \tScore: ${match.confidenceScore.toFixed(2)}`);
        })
        });
    });
}
linkedEntityRecognition(textAnalyticsClient);
```

Eseguire il codice con `node index.js` nella finestra della console.

### <a name="output"></a>Output

```console
Document ID: 0
        Name: Altair 8800       ID: Altair 8800         URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800       Score: 0.88
        Name: Bill Gates        ID: Bill Gates  URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates        Score: 0.63
                Text: Gates     Score: 0.63
        Name: Paul Allen        ID: Paul Allen  URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen        Score: 0.60
        Name: Microsoft         ID: Microsoft   URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft         Score: 0.55
                Text: Microsoft         Score: 0.55
        Name: April 4   ID: April 4     URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4   Score: 0.32
        Name: BASIC     ID: BASIC       URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC     Score: 0.33
```

### <a name="personally-identifying-information-pii-recognition"></a>Riconoscimento delle informazioni personali (PII)

Creare una matrice di stringhe contenente il documento da analizzare. Chiamare il metodo `recognizePiiEntities()` del client e ottenere l'oggetto `RecognizePIIEntitiesResult` restituito. Scorrere l'elenco dei risultati e visualizzare il nome, il tipo e il punteggio dell'entità.

```javascript
async function piiRecognition(client) {

    const documents = [
        "The employee's phone number is (555) 555-5555."
    ];

    const results = await client.recognizePiiEntities(documents, "en");
    for (const result of results) {
        if (result.error === undefined) {
            console.log("Redacted Text: ", result.redactedText);
            console.log(" -- Recognized PII entities for input", result.id, "--");
            for (const entity of result.entities) {
                console.log(entity.text, ":", entity.category, "(Score:", entity.confidenceScore, ")");
            }
        } else {
            console.error("Encountered an error:", result.error);
        }
    }
}
piiRecognition(textAnalyticsClient)
```

Eseguire il codice con `node index.js` nella finestra della console.

### <a name="output"></a>Output

```console
Redacted Text:  The employee's phone number is **************.
 -- Recognized PII entities for input 0 --
(555) 555-5555 : Phone Number (Score: 0.8 )
```

# <a name="version-30"></a>[Versione 3.0](#tab/version-3)

> [!NOTE]
> Nella versione `3.0`:
> * Il collegamento di entità è una richiesta distinta rispetto al riconoscimento di entità denominate.

Creare una matrice di stringhe contenente il documento da analizzare. Chiamare il metodo `recognizeEntities()` del client e ottenere l'oggetto `RecognizeEntitiesResult` restituito. Scorrere l'elenco dei risultati e stampare il nome, il tipo, il sottotipo, lo scostamento, la lunghezza e il punteggio dell'entità.

```javascript
async function entityRecognition(client){

    const entityInputs = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800",
        "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."
    ];
    const entityResults = await client.recognizeEntities(entityInputs);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tCategory: ${entity.category} \tSubcategory: ${entity.subCategory ? entity.subCategory : "N/A"}`);
            console.log(`\tScore: ${entity.confidenceScore}`);
        });
    });
}
entityRecognition(textAnalyticsClient);
```

Eseguire il codice con `node index.js` nella finestra della console.

### <a name="output"></a>Output

```console
Document ID: 0
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Score: 0.29
        Name: Bill Gates        Category: Person        Subcategory: N/A
        Score: 0.78
        Name: Paul Allen        Category: Person        Subcategory: N/A
        Score: 0.82
        Name: April 4, 1975     Category: DateTime      Subcategory: Date
        Score: 0.8
        Name: 8800      Category: Quantity      Subcategory: Number
        Score: 0.8
Document ID: 1
        Name: 21        Category: Quantity      Subcategory: Number
        Score: 0.8
        Name: Seattle   Category: Location      Subcategory: GPE
        Score: 0.25
```

### <a name="entity-linking"></a>Collegamento delle entità

Creare una matrice di stringhe contenente il documento da analizzare. Chiamare il metodo `recognizeLinkedEntities()` del client e ottenere l'oggetto `RecognizeLinkedEntitiesResult` restituito. Scorrere l'elenco dei risultati e stampare il nome, l'ID, l'origine dati, l'URL e le corrispondenze dell'entità. Ogni oggetto della matrice `matches` conterrà lo scostamento, la lunghezza e il punteggio corrispondenti.

```javascript
async function linkedEntityRecognition(client){

    const linkedEntityInput = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800. During his career at Microsoft, Gates held the positions of chairman, chief executive officer, president and chief software architect, while also being the largest individual shareholder until May 2014."
    ];
    const entityResults = await client.recognizeLinkedEntities(linkedEntityInput);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.name} \tID: ${entity.dataSourceEntityId} \tURL: ${entity.url} \tData Source: ${entity.dataSource}`);
            console.log(`\tMatches:`)
            entity.matches.forEach(match => {
                console.log(`\t\tText: ${match.text} \tScore: ${match.confidenceScore.toFixed(2)}`);
        })
        });
    });
}
linkedEntityRecognition(textAnalyticsClient);
```

Eseguire il codice con `node index.js` nella finestra della console.

### <a name="output"></a>Output

```console
Document ID: 0
        Name: Altair 8800       ID: Altair 8800         URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800       Score: 0.88
        Name: Bill Gates        ID: Bill Gates  URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates        Score: 0.63
                Text: Gates     Score: 0.63
        Name: Paul Allen        ID: Paul Allen  URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen        Score: 0.60
        Name: Microsoft         ID: Microsoft   URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft         Score: 0.55
                Text: Microsoft         Score: 0.55
        Name: April 4   ID: April 4     URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4   Score: 0.32
        Name: BASIC     ID: BASIC       URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC     Score: 0.33
```

# <a name="version-21"></a>[Versione 2.1](#tab/version-2)

> [!NOTE]
> Nella versione 2.1 il collegamento di entità è incluso nella risposta del riconoscimento di entità denominate.

Creare un elenco di oggetti contenenti i documenti. Chiamare il metodo [entities()](/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#entities-models-textanalyticscliententitiesoptionalparams-) del client e ottenere l'oggetto [EntitiesBatchResult](/javascript/api/@azure/cognitiveservices-textanalytics/entitiesbatchresult). Eseguire l'iterazione attraverso l'elenco de risultati e stampare l'ID di ogni documento. Per ogni entità rilevata, stampare il relativo nome di wikipedia, il tipo e i sottotipi (se presenti), nonché le posizioni nel testo originale.

[!code-javascript[Entity recognition](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=entityRecognition)]

Eseguire il codice con `node index.js` nella finestra della console.

### <a name="output"></a>Output

```console
Document ID: 1
    Name: Microsoft,        Type: Organization,     Sub-Type: N/A
    Offset: 0, Length: 9,   Score: 1.0
    Name: Bill Gates,       Type: Person,   Sub-Type: N/A
    Offset: 25, Length: 10, Score: 0.999847412109375
    Name: Paul Allen,       Type: Person,   Sub-Type: N/A
    Offset: 40, Length: 10, Score: 0.9988409876823425
    Name: April 4,  Type: Other,    Sub-Type: N/A
    Offset: 54, Length: 7,  Score: 0.8
    Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
    Offset: 54, Length: 13, Score: 0.8
    Name: BASIC,    Type: Other,    Sub-Type: N/A
    Offset: 89, Length: 5,  Score: 0.8
    Name: Altair 8800,      Type: Other,    Sub-Type: N/A
    Offset: 116, Length: 11,        Score: 0.8

Document ID: 2
    Name: Microsoft,        Type: Organization,     Sub-Type: N/A
    Offset: 21, Length: 9,  Score: 0.999755859375
    Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
    Offset: 60, Length: 7,  Score: 0.9911284446716309
    Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
    Offset: 71, Length: 13, Score: 0.8
    Name: Seattle,  Type: Location, Sub-Type: N/A
    Offset: 88, Length: 7,  Score: 0.9998779296875
```

---

## <a name="key-phrase-extraction"></a>Estrazione di frasi chiave

# <a name="version-31-preview"></a>[Versione 3.1-preview](#tab/version-3-1)

Creare una matrice di stringhe contenente il documento da analizzare. Chiamare il metodo `extractKeyPhrases()` del client e ottenere l'oggetto `ExtractKeyPhrasesResult` restituito. Eseguire l'iterazione attraverso i risultati e stampare l'ID di ogni documento e le frasi chiave rilevate.

```javascript
async function keyPhraseExtraction(client){

    const keyPhrasesInput = [
        "My cat might need to see a veterinarian.",
    ];
    const keyPhraseResult = await client.extractKeyPhrases(keyPhrasesInput);
    
    keyPhraseResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Key Phrases: ${document.keyPhrases}`);
    });
}
keyPhraseExtraction(textAnalyticsClient);
```

Eseguire il codice con `node index.js` nella finestra della console.

### <a name="output"></a>Output

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```

# <a name="version-30"></a>[Versione 3.0](#tab/version-3)

Creare una matrice di stringhe contenente il documento da analizzare. Chiamare il metodo `extractKeyPhrases()` del client e ottenere l'oggetto `ExtractKeyPhrasesResult` restituito. Eseguire l'iterazione attraverso i risultati e stampare l'ID di ogni documento e le frasi chiave rilevate.

```javascript
async function keyPhraseExtraction(client){

    const keyPhrasesInput = [
        "My cat might need to see a veterinarian.",
    ];
    const keyPhraseResult = await client.extractKeyPhrases(keyPhrasesInput);
    
    keyPhraseResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Key Phrases: ${document.keyPhrases}`);
    });
}
keyPhraseExtraction(textAnalyticsClient);
```

Eseguire il codice con `node index.js` nella finestra della console.

### <a name="output"></a>Output

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```

# <a name="version-21"></a>[Versione 2.1](#tab/version-2)

Creare un elenco di oggetti contenenti i documenti. Chiamare il metodo [keyPhrases()](/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#keyphrases-models-textanalyticsclientkeyphrasesoptionalparams-) del client e ottenere l'oggetto [KeyPhraseBatchResult](/javascript/api/@azure/cognitiveservices-textanalytics/keyphrasebatchresult) restituito. Eseguire l'iterazione attraverso i risultati e stampare l'ID di ogni documento e le frasi chiave rilevate.

[!code-javascript[Key phrase extraction](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=keyPhraseExtraction)]

Eseguire il codice con `node index.js` nella finestra della console.

### <a name="output"></a>Output

```console
[
    { id: '1', keyPhrases: [ '幸せ' ] }
    { id: '2', keyPhrases: [ 'Stuttgart', "hotel", "Fahrt", "Fu" ] }
    { id: '3', keyPhrases: [ 'cat', 'veterinarian' ] }
    { id: '3', keyPhrases: [ 'fútbol' ] }
]
```

---

## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire l'applicazione con il comando `node` nel file quickstart.

```console
node index.js
```