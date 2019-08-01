---
title: "Guida introduttiva: Uso di Node.js per chiamare l'API Analisi del testo"
titleSuffix: Azure Cognitive Services
description: Ottenere informazioni ed esempi di codice per iniziare a usare rapidamente l'API Analisi del testo.
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/30/2019
ms.author: shthowse
ms.openlocfilehash: 9b8a713d58d5753e04de050e0bc961b5e8388123
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68697488"
---
# <a name="quickstart-using-nodejs-to-call-the-text-analytics-cognitive-service"></a>Guida introduttiva: Uso di Node.js per chiamare il servizio cognitivo Analisi del testo
<a name="HOLTop"></a>

Usare questo argomento di avvio rapido per iniziare ad analizzare la lingua con Text Analytics SDK per Node.js. Sebbene l'API REST [Analisi del testo](//go.microsoft.com/fwlink/?LinkID=759711) sia compatibile con la maggior parte dei linguaggi di programmazione, l'SDK rappresenta un modo semplice per integrare il servizio nelle applicazioni. Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/textAnalytics.js).

Per la documentazione tecnica delle API, vedere le [definizioni delle API](//go.microsoft.com/fwlink/?LinkID=759346).

## <a name="prerequisites"></a>Prerequisiti

* [Node.js](https://nodejs.org/)
* Text Analytics [SDK per Node.js](https://www.npmjs.com/package/azure-cognitiveservices-textanalytics). È possibile installare l'SDK con:

    `npm install azure-cognitiveservices-textanalytics`

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

È inoltre necessario avere la [chiave di accesso e l'endpoint](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) generati automaticamente durante l'iscrizione.

## <a name="create-a-nodejs-application-and-install-the-sdk"></a>Creare un'applicazione Node.js e installare l'SDK

Dopo aver installato Node.js, creare un progetto node. Creare una nuova directory per l'app e spostarsi nella directory.

```mkdir myapp && cd myapp```

Eseguire ```npm init``` per creare un'applicazione node con un file package.json. Installare i pacchetti NPM `ms-rest-azure` e `azure-cognitiveservices-textanalytics`:

```npm install azure-cognitiveservices-textanalytics ms-rest-azure```

Il file package.json dell'app verrà aggiornato con le dipendenze.

## <a name="authenticate-your-credentials"></a>Eseguire l'autenticazione delle proprie credenziali

Creare un nuovo file `index.js` nella radice del progetto e importare le librerie installate

```javascript
const CognitiveServicesCredentials = require("ms-rest-azure").CognitiveServicesCredentials;
const TextAnalyticsAPIClient = require("azure-cognitiveservices-textanalytics");
```

Creare una variabile per la chiave di sottoscrizione di Analisi del testo.

```javascript
let credentials = new CognitiveServicesCredentials(
    "enter-your-key-here"
);
```

> [!Tip]
> Per la distribuzione sicura dei segreti in sistemi di produzione, è consigliabile usare [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-net).
>

## <a name="create-a-text-analytics-client"></a>Creare un client di Analisi del testo

Creare un nuovo oggetto `TextAnalyticsClient` con `credentials` come parametro. Usare l'area di Azure corretta per la sottoscrizione di Analisi del testo.

```javascript
//Replace 'westus' with the correct region for your Text Analytics subscription
let client = new TextAnalyticsAPIClient(
    credentials,
    "https://westus.api.cognitive.microsoft.com/"
);
```

## <a name="sentiment-analysis"></a>Analisi del sentiment

Creare un elenco di oggetti contenenti i documenti da analizzare. Il payload dell'API è costituito da un elenco di `documents`, che contengono gli attributi `id`, `language` e `text`. Nell'attributo `text` viene memorizzato il testo da analizzare, mentre `language` è la lingua del documento e `id` può essere un valore qualsiasi. 

```javascript
const inputDocuments = {documents:[
    {language:"en", id:"1", text:"I had the best day of my life."},
    {language:"en", id:"2", text:"This was a waste of my time. The speaker put me to sleep."},
    {language:"es", id:"3", text:"No tengo dinero ni nada que dar..."},
    {language:"it", id:"4", text:"L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."}
]}
```

Chiamare `client.sentiment` e ottenere i risultati. Quindi eseguire l'iterazione attraverso i risultati e stampare l'ID di ogni documento e il punteggio del sentiment. I punteggi più vicini a 0 indicano un sentiment negativo, mentre quelli più vicini a 1 indicano un sentiment positivo.

```javascript
const operation = client.sentiment({multiLanguageBatchInput: inputDocuments})
operation
.then(result => {
    console.log(result.documents);
})
.catch(err => {
    throw err;
});
```

Eseguire il codice con `node index.js` nella finestra della console.

### <a name="output"></a>Output

```console
[ { id: '1', score: 0.8723785877227783 },
  { id: '2', score: 0.1059873104095459 },
  { id: '3', score: 0.43635445833206177 },
  { id: '4', score: 1 } ]
```

## <a name="language-detection"></a>Rilevamento della lingua

Creare un elenco di oggetti contenenti i documenti. Il payload dell'API è costituito da un elenco di `documents`, che contengono gli attributi `id` e `text`. Nell'attributo `text` viene memorizzato il testo da analizzare, mentre l'`id` può essere un valore qualsiasi.

```javascript
// The documents to be submitted for language detection. The ID can be any value.
const inputDocuments = {
    documents: [
        { id: "1", text: "This is a document written in English." },
        { id: "2", text: "Este es un document escrito en Español." },
        { id: "3", text: "这是一个用中文写的文件" }
    ]
    };
```

Chiamare `client.detectLanguage()` e ottenere i risultati. Quindi eseguire l'iterazione attraverso i risultati e stampare l'ID di ogni documento e il primo linguaggio restituito.

```javascript
const operation = client.detectLanguage({
    languageBatchInput: inputDocuments
});
operation
    .then(result => {
    result.documents.forEach(document => {
        console.log(`ID: ${document.id}`);
        document.detectedLanguages.forEach(language =>
        console.log(`\tLanguage: ${language.name}`)
        );
    });
    })
    .catch(err => {
    throw err;
    });
```

Eseguire il codice con `node index.js` nella finestra della console.

### <a name="output"></a>Output

```console
===== LANGUAGE EXTRACTION ======
ID: 1 Language English
ID: 2 Language Spanish
ID: 3 Language Chinese_Simplified
```

## <a name="entity-recognition"></a>Riconoscimento delle entità

Creare un elenco di oggetti contenenti i documenti. Il payload dell'API è costituito da un elenco di `documents`, che contengono gli attributi `id`, `language` e `text`. Nell'attributo `text` viene memorizzato il testo da analizzare, mentre `language` è la lingua del documento e `id` può essere un valore qualsiasi.

```javascript

    const inputDocuments = {documents:[
        {language:"en", id:"1", text:"Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800"},
        {language:"es", id:"2", text:"La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."},
        ]}

}
```

Chiamare `client.entities()` e ottenere i risultati. Quindi eseguire l'iterazione attraverso i risultati e stampare l'ID di ogni documento. Per ogni entità rilevata, stampare il relativo nome di wikipedia, il tipo e i sottotipi (se presenti), nonché le posizioni nel testo originale.

```javascript
const operation = client.entities({
    multiLanguageBatchInput: inputDocuments
});
operation
    .then(result => {
    result.documents.forEach(document => {
        console.log(`Document ID: ${document.id}`)
        document.entities.forEach(e =>{
        console.log(`\tName: ${e.name} Type: ${e.type} Sub Type: ${e.type}`)
        e.matches.forEach(match => (
            console.log(`\t\tOffset: ${match.offset} Length: ${match.length} Score: ${match.entityTypeScore}`)
        ))
        })
    });
    })
    .catch(err => {
    throw err;
    });
```

Eseguire il codice con `node index.js` nella finestra della console.

### <a name="output"></a>Output

```console
Document ID: 1
    Name: Microsoft Type: Organization Sub Type: Organization
            Offset: 0 Length: 9 Score: 1
    Name: Bill Gates Type: Person Sub Type: Person
            Offset: 25 Length: 10 Score: 0.999786376953125
    Name: Paul Allen Type: Person Sub Type: Person
            Offset: 40 Length: 10 Score: 0.9988105297088623
    Name: April 4 Type: Other Sub Type: Other
            Offset: 54 Length: 7 Score: 0.8
    Name: April 4, 1975 Type: DateTime Sub Type: DateTime
            Offset: 54 Length: 13 Score: 0.8
    Name: BASIC Type: Other Sub Type: Other
            Offset: 89 Length: 5 Score: 0.8
    Name: Altair 8800 Type: Other Sub Type: Other
            Offset: 116 Length: 11 Score: 0.8
Document ID: 2
    Name: Microsoft Type: Organization Sub Type: Organization
            Offset: 21 Length: 9 Score: 0.999755859375
    Name: Redmond (Washington) Type: Location Sub Type: Location
            Offset: 60 Length: 7 Score: 0.9911284446716309
    Name: 21 kilómetros Type: Quantity Sub Type: Quantity
            Offset: 71 Length: 13 Score: 0.8
    Name: Seattle Type: Location Sub Type: Location
            Offset: 88 Length: 7 Score: 0.9998779296875
```

## <a name="key-phrase-extraction"></a>Estrazione di frasi chiave

Creare un elenco di oggetti contenenti i documenti. Il payload dell'API è costituito da un elenco di `documents`, che contengono gli attributi `id`, `language` e `text`. Nell'attributo `text` viene memorizzato il testo da analizzare, mentre `language` è la lingua del documento e `id` può essere un valore qualsiasi.

```javascript
    let inputLanguage = {
    documents: [
        {language:"ja", id:"1", text:"猫は幸せ"},
        {language:"de", id:"2", text:"Fahrt nach Stuttgart und dann zum Hotel zu Fu."},
        {language:"en", id:"3", text:"My cat might need to see a veterinarian."},
        {language:"es", id:"4", text:"A mi me encanta el fútbol!"}
    ]
    };
```

Chiamare `client.keyPhrases()` e ottenere i risultati. Eseguire quindi l'iterazione attraverso i risultati e stampare l'ID di ogni documento e le frasi chiave rilevate.

```javascript
    let operation = client.keyPhrases({
    multiLanguageBatchInput: inputLanguage
    });
    operation
    .then(result => {
        console.log(result.documents);
    })
    .catch(err => {
        throw err;
    });
```

Eseguire il codice con `node index.js` nella finestra della console.

### <a name="output"></a>Output

```console
[ 
    { id: '1', keyPhrases: [ '幸せ' ] },
    { id: '2', keyPhrases: [ 'Stuttgart', 'Hotel', 'Fahrt', 'Fu' ] },
    { id: '3', keyPhrases: [ 'cat', 'veterinarian' ] },
    { id: '4', keyPhrases: [ 'fútbol' ] } 
]
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Analisi del testo con Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Vedere anche

 [Panoramica di Analisi del testo](../overview.md) [Domande frequenti](../text-analytics-resource-faq.md)
