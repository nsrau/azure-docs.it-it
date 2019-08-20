---
title: 'Guida introduttiva: Libreria client di Analisi del testo per Node.js | Microsoft Docs'
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
ms.openlocfilehash: 8590acbbd6001c1f214589298e454c1e75f93d67
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883527"
---
# <a name="quickstart-text-analytics-client-library-for-nodejs"></a>Guida introduttiva: Libreria client di Analisi del testo per Node.js
<a name="HOLTop"></a>

Introduzione alla libreria client di Analisi del testo per Node.js. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base. 

Usare la libreria client di Analisi del testo per Node.js per eseguire queste operazioni:

* Analisi del sentiment
* Rilevamento della lingua
* Riconoscimento delle entità
* Estrazione di frasi chiave

[Documentazione di riferimento](https://docs.microsoft.com/javascript/api/overview/azure/cognitiveservices/textanalytics?view=azure-node-latest) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesTextAnalytics) | [Pacchetto (NPM)](https://www.npmjs.com/package/azure-cognitiveservices-textanalytics) | [Esempi](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/)

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)
* Versione corrente di [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Configurazione

### <a name="create-a-text-analytics-azure-resource"></a>Creare una risorsa di Azure per Analisi del testo

I Servizi cognitivi di Azure sono rappresentati dalle risorse di Azure a cui si effettua la sottoscrizione. Creare una risorsa per Analisi del testo usando il [portale di Azure](../../cognitive-services-apis-create-account.md) o l'[interfaccia della riga di comando di Azure](../../cognitive-services-apis-create-account-cli.md) nel computer locale. È anche possibile:

* Ottenere un [codice della versione di valutazione gratuita](https://azure.microsoft.com/try/cognitive-services/#decision), valido per 7 giorni. Dopo aver eseguito l'iscrizione, sarà disponibile sul [sito Web di Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Visualizzare questa risorsa nel [portale di Azure](https://portal.azure.com/)

Dopo aver ottenuto una chiave dalla sottoscrizione di valutazione o dalla risorsa, [creare una variabile di ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) per la chiave, denominata `TEXTANALYTICS_SUBSCRIPTION_KEY`.

### <a name="create-a-new-nodejs-application"></a>Creare una nuova applicazione Node.js

In una finestra della console, ad esempio cmd, PowerShell o Bash, creare e passare a una nuova directory per l'app. 

```console
mkdir myapp && cd myapp
```

Eseguire il comando `npm init` per creare un'applicazione Node con un file `package.json`. 

```console
npm init
```

Creare un file denominato `index.js` e importare le librerie seguenti:

```javascript
const CognitiveServicesCredentials = require("ms-rest-azure").CognitiveServicesCredentials;
const TextAnalyticsAPIClient = require("azure-cognitiveservices-textanalytics");
```

Creare le variabili per l'endpoint e la chiave di Azure della risorsa. Se la variabile di ambiente è stata creata dopo l'avvio dell'applicazione, per accedervi sarà necessario chiudere e riaprire l'editor, l'IDE o la shell in cui è in esecuzione.

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

```javascript
// replace this endpoint with the correct one for your Azure resource. 
let endpoint = "https://westus.api.cognitive.microsoft.com/";
// This sample assumes you have created an environment variable for your key
let key = var apiKey = process.env.TEXTANALYTICS_SUBSCRIPTION_KEY;
let credentials = new CognitiveServicesCredentials(
    key
);
```

### <a name="install-the-client-library"></a>Installare la libreria client

Installare i pacchetti NPM `ms-rest-azure` e `azure-cognitiveservices-textanalytics`:

```console
npm install azure-cognitiveservices-textanalytics ms-rest-azure
```

Il file `package.json` dell'app viene aggiornato con le dipendenze.

## <a name="object-model"></a>Modello a oggetti

Il client di Analisi del testo è un oggetto [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/azure-cognitiveservices-textanalytics/textanalyticsclient?view=azure-node-latest) che esegue l'autenticazione in Azure tramite la chiave. Il client fornisce diversi metodi per l'analisi del testo, come singola stringa o batch.

Il testo viene inviato all'API come elenco di `documents`, che sono oggetti `dictionary` contenenti una combinazione di attributi `id`, `text` e `language`, a seconda del metodo usato. L'attributo `text` archivia il testo da analizzare in base all'attributo `language` di origine, mentre `id` può essere un valore qualsiasi. 

L'oggetto risposta è un elenco contenente le informazioni di analisi per ogni documento. 

## <a name="code-examples"></a>Esempi di codice

* [Autenticare il client](#authenticate-the-client)
* [Analisi del sentiment](#sentiment-analysis)
* [Rilevamento della lingua](#language-detection)
* [Riconoscimento delle entità](#entity-recognition)
* [Estrazione delle frasi chiave](#key-phrase-extraction)


## <a name="authenticate-the-client"></a>Autenticare il client

Creare un nuovo oggetto [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/azure-cognitiveservices-textanalytics/textanalyticsclient?view=azure-node-latest) con `credentials` e `endpoint` come parametri.

```javascript
//Replace 'westus' with the correct region for your Text Analytics subscription
let client = new TextAnalyticsAPIClient(
    credentials,
    endpoint
);
```

## <a name="sentiment-analysis"></a>Analisi del sentiment

Creare un elenco di oggetti contenenti i documenti da analizzare.

```javascript
const inputDocuments = {documents:[
    {language:"en", id:"1", text:"I had the best day of my life."}
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
[ { id: '1', score: 0.8723785877227783 } ]
```

## <a name="language-detection"></a>Rilevamento della lingua

Creare un elenco di oggetti contenenti i documenti.

```javascript
// The documents to be submitted for language detection. The ID can be any value.
const inputDocuments = {
    documents: [
        { id: "1", text: "This is a document written in English." }
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
```

## <a name="entity-recognition"></a>Riconoscimento delle entità

Creare un elenco di oggetti contenenti i documenti.

```javascript

    const inputDocuments = {documents:[
        {language:"en", id:"1", text:"Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800"}
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
```

## <a name="key-phrase-extraction"></a>Estrazione di frasi chiave

Creare un elenco di oggetti contenenti i documenti.

```javascript
    let inputLanguage = {
    documents: [
        {language:"en", id:"1", text:"My cat might need to see a veterinarian."}
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
    { id: '1', keyPhrases: [ 'cat', 'veterinarian' ] }
]
```

## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire l'applicazione con il comando `node` nel file quickstart.

```console
node index.js
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole pulire e rimuovere una sottoscrizione a Servizi cognitivi, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate.

* [Portale](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfaccia della riga di comando di Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Analisi del testo con Power BI](../tutorials/tutorial-power-bi-key-phrases.md)


* [Panoramica di Analisi del testo](../overview.md)
* [Analisi del sentiment](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Riconoscimento delle entità](../how-tos/text-analytics-how-to-entity-linking.md)
* [Rilevare la lingua](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Riconoscimento della lingua](../how-tos/text-analytics-how-to-language-detection.md)
* Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/textAnalytics.js).
