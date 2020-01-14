---
title: Eseguire l'analisi del sentiment con l'API REST Analisi del testo
titleSuffix: Azure Cognitive Services
description: In questo articolo viene illustrato come rilevare il sentiment nel testo con l'API REST Analisi del testo di Servizi cognitivi di Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: 214c071e0d01908e2d46c932fcf87906de834102
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75644682"
---
# <a name="how-to-detect-sentiment-using-the-text-analytics-api"></a>Procedura: Rilevare il sentiment con l'API REST Analisi del testo

La funzionalità Analisi del sentiment dell'API Analisi del testo valuta il testo e restituisce i punteggi e le etichette del sentiment per ogni frase. Questa funzionalità è utile per il rilevamento del sentiment positivo e negativo nei social media, nelle recensioni dei clienti, nei forum di discussione e così via. I modelli di intelligenza artificiale usati dall'API vengono forniti dal servizio, quindi è sufficiente inviare contenuto per l'analisi.

> [!TIP]
> Analisi del testo offre anche un'immagine del contenitore Docker basata su Linux per il rilevamento della lingua, di conseguenza è possibile [installare ed eseguire il contenitore di Analisi del testo](text-analytics-how-to-install-containers.md) vicino ai dati.

Analisi del sentiment supporta una vasta gamma di linguaggi, mentre altri disponibili in anteprima. Per altre informazioni, vedere [Linguaggi supportati](../text-analytics-supported-languages.md).

## <a name="concepts"></a>Concetti

L'API Analisi del testo usa un algoritmo di classificazione basato su Machine Learning per generare un punteggio di sentiment compreso tra 0 e 1. I punteggi più vicini a 1 indicano un sentiment positivo, mentre quelli più vicini allo 0 indicano un sentiment negativo. L'analisi del sentiment viene eseguita sull'intero documento, invece che su singole entità del testo. Questo significa che i punteggi del sentiment vengono restituiti a livello di documento o di frase. 

Il modello usato viene sottoposto a training con una raccolta completa di associazioni di testo e sentiment. Utilizza una combinazione di tecniche di analisi, tra cui l'elaborazione del testo, l'analisi di parti del discorso, nonché il posizionamento e le associazioni delle parole. Per altre informazioni sull'algoritmo, vedere [Introducing Text Analytics](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/) (Introduzione ad Analisi del testo). Non è attualmente possibile per l'utente specificare i propri dati di training. 

L'accuratezza nell'assegnazione dei punteggi tende a essere maggiore quando i documenti contengono poche frasi invece di un blocco di testo di grandi dimensioni. Durante una fase di valutazione dell'obiettività, il modello determina se un documento nel suo complesso è obiettivo o contiene sentiment. Un documento principalmente obiettivo non passa alla fase di rilevamento del sentiment e restituisce un punteggio pari a 0,5 senza ulteriore elaborazione. Per i documenti che proseguono nella pipeline, la fase successiva genera un punteggio superiore o inferiore a 0,5. Il punteggio dipende dal grado di sentiment rilevato nel documento.

## <a name="sentiment-analysis-versions-and-features"></a>Versioni e funzionalità di Analisi del sentiment

L'API Analisi del testo prevede due versioni di Analisi del sentiment, v2 e v3. Analisi del sentiment v3 (anteprima pubblica) offre miglioramenti significativi nell'accuratezza e nel dettaglio per la classificazione del testo e l'assegnazione di punteggi dell'API.

> [!NOTE]
> * Il formato della richiesta e i [limiti dei dati](../overview.md#data-limits) di Analisi del sentiment versione 3 sono gli stessi della versione precedente.
> * Analisi del sentiment v3 è disponibile nelle aree `Australia East`, `Central Canada`, `Central US`, `East Asia`, `East US`, `East US 2`, `North Europe`, `Southeast Asia`, `South Central US`, `UK South`, `West Europe` e `West US 2`.

| Funzionalità                                   | Analisi del sentiment v2 | Analisi del sentiment v3 |
|-------------------------------------------|-----------------------|-----------------------|
| Metodi per richieste singole e batch    | X                     | X                     |
| Punteggi del sentiment per l'intero documento  | X                     | X                     |
| Punteggi del sentiment per singole frasi |                       | X                     |
| Assegnazione di etichette per la valutazione                        |                       | X                     |
| Gestione della versione dei modelli                   |                       | X                     |

#### <a name="version-2tabversion-2"></a>[Versione 2](#tab/version-2)

### <a name="sentiment-scoring"></a>Assegnazione di punteggi del sentiment

L'analizzatore del sentiment classifica il testo come prevalentemente positivo o negativo, assegnando un punteggio compreso tra 0 e 1. I valori vicini a 0,5 sono neutri o indeterminati. Un punteggio pari a 0,5 indica neutralità. Se una stringa non può essere analizzata per valutarne il sentiment o non viene riscontrato alcun sentiment, il punteggio è sempre 0,5. Se ad esempio si passa una stringa in spagnolo con un codice di lingua inglese, il punteggio è pari a 0,5.


#### <a name="version-3-public-previewtabversion-3"></a>[Versione 3 (anteprima pubblica)](#tab/version-3)

### <a name="sentiment-scoring"></a>Assegnazione di punteggi del sentiment

Analisi del sentiment V3 classifica il testo con le etichette del sentiment (descritte di seguito). I punteggi restituiti rappresentano l'attendibilità del modello che indica che il testo è positivo, negativo o neutro. I valori più elevati indicano una maggiore attendibilità. 

### <a name="sentiment-labeling"></a>Assegnazione di etichette per la valutazione

Analisi del sentiment versione 3 può restituire punteggi ed etichette a livello di frase e di documento. I punteggi e le etichette sono `positive`, `negative` e `neutral`. A livello di documento, l'etichetta del sentiment `mixed` può anche essere restituita senza punteggio. Il sentiment del documento è determinato di seguito:

| Valutazione della frase                                                                            | Etichetta di documento restituita |
|-----------------------------------------------------------------------------------------------|-------------------------|
| Nel documento è presente almeno una frase `positive`. Le restanti frasi sono `neutral`. | `positive`              |
| Nel documento è presente almeno una frase `negative`. Le restanti frasi sono `neutral`. | `negative`              |
| Nel documento è presente almeno una frase `negative` e almeno una frase `positive`.    | `mixed`                 |
| Tutte le frasi del documento sono `neutral`.                                                  | `neutral`               |

### <a name="model-versioning"></a>Gestione della versione dei modelli

> [!NOTE]
> Il controllo delle versioni dei modelli per l'analisi del sentiment è disponibile a partire dalla versione `v3.0-preview.1`.

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

### <a name="example-c-code"></a>Codice C# di esempio

Un'applicazione C# di esempio che chiama questa versione di Analisi del sentiment è disponibile in [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/dotnet/Language/SentimentV3.cs).

---

## <a name="sending-a-rest-api-request"></a>Invio di una richiesta all'API REST 

### <a name="preparation"></a>Preparazione

L'analisi del sentiment genera risultati di qualità superiore quando viene eseguita su quantità ridotte di testo, a differenza dell'estrazione di espressioni chiave, che offre prestazioni migliori in blocchi di testo di maggiori dimensioni. Per ottenere risultati ottimali da entrambe le operazioni, provare a ristrutturare gli input di conseguenza.

È necessario disporre di documenti JSON nel formato seguente: ID, testo e lingua.

Le dimensioni dei documenti devono essere inferiori a 5.120 caratteri per documento. Ogni raccolta può contenere fino a 1.000 elementi (ID). La raccolta viene inviata nel corpo della richiesta.

## <a name="structure-the-request"></a>Strutturare la richiesta

Creare una richiesta POST. È possibile [usare Postman](text-analytics-how-to-call-api.md) o la **console di test dell'API** nei collegamenti di riferimento seguenti per strutturarne e inviarne rapidamente una. 

#### <a name="version-2tabversion-2"></a>[Versione 2](#tab/version-2)

[Informazioni di riferimento su Analisi del sentiment v2](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)

#### <a name="version-3-public-previewtabversion-3"></a>[Versione 3 (anteprima pubblica)](#tab/version-3)

[Informazioni di riferimento su Analisi del sentiment v3](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment)

---

Impostare l'endpoint HTTP per l'analisi del sentiment usando una risorsa di Analisi del testo in Azure oppure un'istanza di [contenitore di Analisi del testo](text-analytics-how-to-install-containers.md). È necessario includere l'URL corretto per la versione che si vuole usare. Ad esempio:
    
[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

#### <a name="version-2tabversion-2"></a>[Versione 2](#tab/version-2)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/sentiment`

#### <a name="version-3-public-previewtabversion-3"></a>[Versione 3 (anteprima pubblica)](#tab/version-3)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/sentiment`

---

Impostare un'intestazione della richiesta per includere la chiave dell'API Analisi del testo. Nel corpo della richiesta specificare la raccolta di documenti JSON preparata per l'analisi.

### <a name="example-sentiment-analysis-request"></a>Esempio di richiesta di Analisi del sentiment 

Di seguito è riportato un esempio di contenuto che può essere inviato per l'analisi del sentiment. Il formato della richiesta è identico per entrambe le versioni dell'API.
    
```json
{
    "documents": [
    {
        "language": "en",
        "id": "1",
        "text": "Hello world. This is some input text that I love."
    },
    {
        "language": "en",
        "id": "2",
        "text": "It's incredibly sunny outside! I'm so happy."
    }
    ],
}
```

### <a name="post-the-request"></a>Pubblicare la richiesta

Al momento della ricezione della richiesta viene eseguita l'analisi. Per informazioni sulle dimensioni e sul numero delle richieste che è possibile inviare al minuto e al secondo, vedere la sezione [Limiti dei dati](../overview.md#data-limits) nella panoramica.

L'API Analisi del testo è senza stato. Non vengono archiviati dati nell'account e i risultati vengono restituiti immediatamente nella risposta.


### <a name="view-the-results"></a>View the results

L'analizzatore del sentiment classifica il testo come prevalentemente positivo o negativo, assegnando un punteggio compreso tra 0 e 1. I valori vicini a 0,5 sono neutri o indeterminati. Un punteggio pari a 0,5 indica neutralità. Se una stringa non può essere analizzata per valutarne il sentiment o non viene riscontrato alcun sentiment, il punteggio è sempre 0,5. Se ad esempio si passa una stringa in spagnolo con un codice di lingua inglese, il punteggio è pari a 0,5.

L'output viene restituito immediatamente. È possibile trasmettere i risultati a un'applicazione che accetta JSON o salvare l'output in un file nel sistema locale. Importare quindi l'output in un'applicazione che consente di ordinare, cercare e modificare i dati.

#### <a name="version-2tabversion-2"></a>[Versione 2](#tab/version-2)

### <a name="sentiment-analysis-v2-example-response"></a>Risposta di esempio di Analisi del sentiment v2

Le risposte di Analisi del sentiment v2 contengono i punteggi del sentiment per ogni documento inviato.

```json
{
  "documents": [{
    "id": "1",
    "score": 0.98690706491470337
  }, {
    "id": "2",
    "score": 0.95202046632766724
  }],
  "errors": []
}
```

#### <a name="version-3-public-previewtabversion-3"></a>[Versione 3 (anteprima pubblica)](#tab/version-3)

### <a name="sentiment-analysis-v3-example-response"></a>Risposta di esempio di Analisi del sentiment versione 3

Le risposte di Analisi del sentiment v3 contengono le etichette e i punteggi del sentiment per ogni frase e ogni documento analizzato. `documentScores` non viene restituito se l'etichetta del sentiment del documento è `mixed`.

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "documentScores": {
                "positive": 0.98570585250854492,
                "neutral": 0.0001625834556762,
                "negative": 0.0141316400840878
            },
            "sentences": [
                {
                    "sentiment": "neutral",
                    "sentenceScores": {
                        "positive": 0.0785155147314072,
                        "neutral": 0.89702343940734863,
                        "negative": 0.0244610067456961
                    },
                    "offset": 0,
                    "length": 12
                },
                {
                    "sentiment": "positive",
                    "sentenceScores": {
                        "positive": 0.98570585250854492,
                        "neutral": 0.0001625834556762,
                        "negative": 0.0141316400840878
                    },
                    "offset": 13,
                    "length": 36
                }
            ]
        },
        {
            "id": "2",
            "sentiment": "positive",
            "documentScores": {
                "positive": 0.89198976755142212,
                "neutral": 0.103382371366024,
                "negative": 0.0046278294175863
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "sentenceScores": {
                        "positive": 0.78401315212249756,
                        "neutral": 0.2067587077617645,
                        "negative": 0.0092281140387058
                    },
                    "offset": 0,
                    "length": 30
                },
                {
                    "sentiment": "positive",
                    "sentenceScores": {
                        "positive": 0.99996638298034668,
                        "neutral": 0.0000060341349126,
                        "negative": 0.0000275444017461
                    },
                    "offset": 31,
                    "length": 13
                }
            ]
        }
    ],
    "errors": []
}
```
---

## <a name="summary"></a>Summary

In questo articolo sono stati appresi i concetti e il flusso di lavoro per l'analisi del sentiment eseguita con l'API Analisi del testo. In sintesi:

+ Analisi del sentiment è disponibile solo per determinate lingue in due versioni.
+ I documenti JSON nel corpo della richiesta includono un ID, il testo e il codice della lingua.
+ La richiesta POST viene inviata a un endpoint `/sentiment` usando [una chiave di accesso e un endpoint](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) personalizzati validi per la sottoscrizione.
+ L'output di risposta, costituito da un punteggio del sentiment per ogni ID documento, può essere trasmesso a qualsiasi app che accetta JSON, Ad esempio, Excel e Power BI.

## <a name="see-also"></a>Vedere anche

* [Panoramica di Analisi del testo](../overview.md)
* [Uso della libreria client di Analisi del testo](../quickstarts/text-analytics-sdk.md)
* [Novità](../whats-new.md)
