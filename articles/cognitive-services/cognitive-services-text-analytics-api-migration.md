---
title: Aggiornamento alla versione 2 dell&quot;API Analisi del testo | Documentazione Microsoft
description: Analisi del testo di Azure Machine Learning - Aggiornamento alla versione 2
services: cognitive-services
documentationcenter: 
author: onewth
manager: jhubbard
editor: cgronlun
ms.assetid: bbf86f80-f677-42f3-8c17-118b16a23c34
ms.service: cognitive-services
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: onewth
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9b28666e384402935ae342788afff6b6e8c7eab8


---
# <a name="upgrading-to-version-2-of-the-text-analytics-api"></a>Aggiornamento alla versione 2 dell'API di analisi del testo
Questa guida illustra il processo di aggiornamento del codice dall'uso della [prima versione dell'API](../machine-learning/machine-learning-apps-text-analytics.md) all'uso della seconda versione. 

Se non si ha familiarità con l'API, è possibile trovare altre informazioni sull'API **[qui](//go.microsoft.com/fwlink/?LinkID=759711)** oppure **[vedere la guida introduttiva](//go.microsoft.com/fwlink/?LinkID=760860)**. Per la documentazione tecnica, vedere la **[definizione dell'API](//go.microsoft.com/fwlink/?LinkID=759346)**.

### <a name="part-1-get-a-new-key"></a>Parte 1. Ottenere una nuova chiave
Prima di tutto sarà necessario ottenere una nuova chiave API dal **portale di Azure**:

1. Accedere al servizio Analisi del testo da [Cortana Intelligence Gallery](//gallery.cortanaintelligence.com/MachineLearningAPI/Text-Analytics-2), dove sono disponibili anche collegamenti alla documentazione e a esempi di codice.
2. Fare clic su **Sign Up**. Questo collegamento apre il portale di gestione di Azure, dove è possibile iscriversi al servizio.
3. Selezionare un piano. È possibile selezionare il **livello gratuito per 5.000 transazioni al mese**. Essendo un piano gratuito, non verranno addebitati costi per l'uso del servizio. Sarà necessario accedere alla sottoscrizione di Azure. 
4. Dopo l'iscrizione ad Analisi del testo, verrà assegnata una **chiave API**. Copiare questa chiave, perché sarà necessaria quando si usano i servizi API.

### <a name="part-2-update-the-headers"></a>Parte 2. Aggiornare le intestazioni
Aggiornare i valori di intestazione inviati, come illustrato di seguito. Si noti che la chiave dell'account non è più codificata.

**Versione 1**

    Authorization: Basic base64encode(<your Data Market account key>)
    Accept: application/json

**Versione 2**

    Content-Type: application/json
    Accept: application/json
    Ocp-Apim-Subscription-Key: <your Azure Portal account key>


### <a name="part-3-update-the-base-url"></a>Parte 3. Aggiornare l'URL di base
**Versione 1**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/

**Versione 2**

    https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/

### <a name="part-4a-update-the-formats-for-sentiment-key-phrases-and-languages"></a>Parte 4a. Aggiornare i formati per valutazione, frasi chiave e lingue
#### <a name="endpoints"></a>Endpoint
Gli endpoint GET ora sono deprecati, quindi tutto l'input deve essere inviato come richiesta POST. Aggiornare gli endpoint a quelli illustrati di seguito.

|  | Endpoint singolo versione 1 | Endpoint batch versione 1 | Endpoint versione 2 |
| --- | --- | --- | --- |
| Tipo di chiamata |GET |POST |POST |
| Valutazione |```GetSentiment``` |```GetSentimentBatch``` |```sentiment``` |
| Frasi chiave |```GetKeyPhrases``` |```GetKeyPhrasesBatch``` |```keyPhrases``` |
| Lingue |```GetLanguage``` |```GetLanguageBatch``` |```languages``` |

#### <a name="input-formats"></a>Formati di input
Si noti che ora è accettato solo il formato POST, quindi è consigliabile riformattare di conseguenza gli input che prima usavano gli endpoint documento singoli. Gli input non fanno distinzione tra maiuscole/minuscole.

**Versione 1 (batch)**

    {
      "Inputs": [
        {
          "Id": "string",
          "Text": "string"
        }
      ]
    }

**Versione 2**

    {
      "documents": [
        {
          "id": "string",
          "text": "string"
        }
      ]
    }

#### <a name="output-from-sentiment"></a>Output della valutazione
**Versione 1**

    {
      "SentimentBatch":[{
        "Id":"string",
        "Score":"double"
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**Versione 2**

    {
      "documents":[{
        "id":"string",
        "score":"double"
      }],
      "errors" : [{
        "id":"string",
        "message":"string"
      }]
    }

#### <a name="output-from-key-phrases"></a>Output delle frasi chiave
**Versione 1**

    {
      "KeyPhrasesBatch":[{
        "Id":"string",
        "KeyPhrases":["string"]
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**Versione 2**

    {
      "documents":[{
        "id":"string",
        "keyPhrases":["string"]
      }],
      "errors" : [{
        "id":"string",
        "message":"string"
      }]
    }

#### <a name="output-from-languages"></a>Output delle lingue
**Versione 1**

    {
      "LanguageBatch":[{
        "id":"string",
        "detectedLanguages": [{
          "Score":"double"
          "Name":"string",
          "Iso6391Name":"string"
        }]
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**Versione 2**

    {
      "documents":[{
        "id":"string",
        "detectedLanguages": [{
          "score":"double"
          "name":"string",
          "iso6391Name":"string"
        }]
      }],
      "errors" : [{
        "id":"string",
        "message":"string"
      }]
    }


### <a name="part-4b-update-the-formats-for-topics"></a>Parte 4b. Aggiornare i formati per gli argomenti
#### <a name="endpoints"></a>Endpoint
|  | Endpoint versione 1 | Endpoint versione 2 |
| --- | --- | --- |
| Invio per rilevamento argomento (POST) |```StartTopicDetection``` |```topics``` |
| Recupero risultati argomento (GET) |```GetTopicDetectionResult?JobId=<jobId>``` |```operations/<operationId>``` |

#### <a name="input-formats"></a>Formati di input
**Versione 1**

    {
      "StopWords": [
        "string"
      ],
      "StopPhrases": [
        "string"
      ], 
      "Inputs": [
        {
          "Id": "string",
          "Text": "string"
        }
      ]
    }

**Versione 2**

    {
      "stopWords": [
        "string"
      ],
      "stopPhrases": [
        "string"
      ],
      "documents": [
        {
          "id": "string",
          "text": "string"
        }
      ]
    }

#### <a name="submission-results"></a>Risultati dell'invio
**Versione 1 (POST)**

Prima, al termine del processo, si riceveva l'output JSON seguente, in cui jobId veniva aggiunto a un URL per recuperare l'output.

    {
        "odata.metadata":"<url>",
        "JobId":"<JobId>"
    }

**Versione 2 (POST)**

La risposta includerà ora un valore di intestazione nel codice seguente, dove `operation-location` viene usato come endpoint per il polling dei risultati:

    'operation-location': 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/operations/<operationId>'

#### <a name="operation-results"></a>Operation results
**Versione 1 (GET)**

    {
      "TopicInfo" : [{
        "TopicId" : "string"
        "Score" : "double"
        "KeyPhrase" : "string"
      }],
      "TopicAssignment" : [{
        "Id" : "string",
        "TopicId" : "string",
        "Distance" : "double"
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**Versione 2 (GET)**

Come prima, **eseguire regolarmente il polling dell'output** (il periodo consigliato è ogni minuto) finché l'output non viene restituito. 

Al termine dell'esecuzione dell'API degli argomenti, verrà restituito lo stato `succeeded` . I risultati dell'output verranno quindi inclusi nel formato seguente:

    {
        "status": "succeeded",
        "createdDateTime": "string",
        "operationType": "topics",
        "processingResult": {
            "topics" : [{
            "id" : "string"
            "score" : "double"
            "keyPhrase" : "string"
          }],
          "topicAssignments" : [{
            "topicId" : "string",
            "documentId" : "string",
            "distance" : "double"
          }],
          "errors" : [{
              "id":"string",
              "message":"string"
          }]
        }
    }

### <a name="part-5-test-it"></a>Parte 5. Test
A questo punto si dovrebbe essere pronti. Testare il codice con un piccolo esempio per assicurarsi di riuscire a elaborare i dati.




<!--HONumber=Nov16_HO3-->


