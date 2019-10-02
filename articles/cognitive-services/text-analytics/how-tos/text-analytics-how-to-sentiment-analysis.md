---
title: Analisi del sentiment con l'API REST Analisi del testo di Servizi cognitivi di Azure
titleSuffix: Azure Cognitive Services
description: Informazioni su come rilevare il sentiment con l'API REST Analisi del testo.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 09/23/2019
ms.author: aahi
ms.openlocfilehash: ea145239d38a4030423a4517fe02c62b8eefa08a
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71211779"
---
# <a name="example-detect-sentiment-with-text-analytics"></a>Esempio: Rilevare il sentiment con Analisi del testo

L'[API Analisi del sentiment di Azure](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) valuta l'input di testo e restituisce un punteggio del sentiment per ogni documento. I punteggi sono compresi tra 0 (negativo) e 1 (positivo).

Questa funzionalità è utile per il rilevamento del sentiment positivo e negativo nei social media, nelle recensioni dei clienti e nei forum di discussione. Il contenuto viene fornito dall'utente. I modelli e i dati di training vengono forniti dal servizio.

L'API Analisi del sentiment attualmente supporta le lingue inglese, tedesco, spagnolo e francese. Altre lingue sono disponibili in anteprima. Per altre informazioni, vedere [Linguaggi supportati](../text-analytics-supported-languages.md).

> [!TIP]
> L'API Analisi del testo di Azure offre anche un'immagine del contenitore Docker basata su Linux per l'analisi del sentiment, di conseguenza è possibile [installare ed eseguire il contenitore di Analisi del testo](text-analytics-how-to-install-containers.md) vicino ai dati.

## <a name="concepts"></a>Concetti

La funzione Analisi del testo usa un algoritmo di classificazione basato sull'apprendimento automatico per generare un punteggio di sentiment compreso tra 0 e 1. I punteggi più vicini a 1 indicano un sentiment positivo, mentre quelli più vicini allo 0 indicano un sentiment negativo. Il modello viene sottoposto a training con un corpo di testo completo con associazioni di sentiment. Non è attualmente possibile per l'utente specificare i propri dati di training. Durante l'analisi del testo il modello usa una combinazione di tecniche, che includono l'elaborazione del testo, l'analisi delle parti del discorso e la posizione e l'associazione delle parole. Per altre informazioni sull'algoritmo, vedere [Introducing Text Analytics](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/) (Introduzione ad Analisi del testo).

La funzione Analisi del sentiment viene eseguita sull'intero documento, a differenza dell'estrazione del sentiment per una particolare entità nel testo. Nella pratica, la precisione nell'assegnazione del punteggio tende a essere maggiore quando i documenti contengono una o due frasi anziché un blocco di testo di grandi dimensioni. Durante una fase di valutazione dell'obiettività, il modello determina se un documento nel suo complesso è obiettivo o contiene sentiment. Un documento principalmente obiettivo non passa alla fase di rilevamento del sentiment e restituisce un punteggio pari a 0,5 senza ulteriore elaborazione. Per i documenti che proseguono nella pipeline, la fase successiva genera un punteggio superiore o inferiore a 0,5. Il punteggio dipende dal grado di sentiment rilevato nel documento.

## <a name="preparation"></a>Operazioni preliminari

L'analisi del sentiment genera risultati di qualità superiore quando viene eseguita su blocchi di testo di dimensioni ridotte, a differenza dell'estrazione di espressioni chiave, che offre prestazioni migliori in blocchi di testo di maggiori dimensioni. Per ottenere risultati ottimali da entrambe le operazioni, provare a ristrutturare gli input di conseguenza.

È necessario disporre di documenti JSON nel formato seguente: ID, testo e lingua.

Le dimensioni dei documenti devono essere inferiori a 5.120 caratteri per documento. Ogni raccolta può contenere fino a 1.000 elementi (ID). La raccolta viene inviata nel corpo della richiesta. Di seguito è riportato un esempio del contenuto che può essere inviato per l'analisi del sentiment:

```json
    {
        "documents": [
            {
                "language": "en",
                "id": "1",
                "text": "We love this trail and make the trip every year. The views are breathtaking and well worth the hike!"
            },
            {
                "language": "en",
                "id": "2",
                "text": "Poorly marked trails! I thought we were goners. Worst hike ever."
            },
            {
                "language": "en",
                "id": "3",
                "text": "Everyone in my family liked the trail but thought it was too challenging for the less athletic among us. Not necessarily recommended for small children."
            },
            {
                "language": "en",
                "id": "4",
                "text": "It was foggy so we missed the spectacular views, but the trail was ok. Worth checking out if you are in the area."
            },
            {
                "language": "en",
                "id": "5",
                "text": "This is my favorite trail. It has beautiful views and many places to stop and rest"
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>Passaggio 1: Strutturare la richiesta

Per altre informazioni sulla definizione della richiesta, vedere [Chiamare l'API Analisi del testo](text-analytics-how-to-call-api.md). Per comodità si ridefiniscono i punti seguenti:

+ Creare una richiesta POST. Per esaminare la documentazione dell'API per la richiesta, vedere [API Analisi del sentiment](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9).

+ Impostare l'endpoint HTTP per l'analisi del sentiment usando una risorsa di Analisi del testo in Azure oppure un [contenitore di Analisi del testo](text-analytics-how-to-install-containers.md) di cui è stata creata un'istanza. È necessario includere `/text/analytics/v2.1/sentiment` nell'URL. Ad esempio: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/sentiment`.

+ Impostare un'intestazione della richiesta in modo da includere la [chiave di accesso](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) per le operazioni di Analisi del testo.

+ Nel corpo della richiesta specificare la raccolta di documenti JSON preparata per l'analisi.

> [!Tip]
> Usare [Postman](text-analytics-how-to-call-api.md) oppure aprire la **console di test dell'API** nella [documentazione](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) per strutturare la richiesta e inviarla al servizio.

## <a name="step-2-post-the-request"></a>Passaggio 2: Pubblicare la richiesta

Al momento della ricezione della richiesta viene eseguita l'analisi. Per informazioni sulle dimensioni e sul numero delle richieste che è possibile inviare al minuto e al secondo, vedere la sezione [Limiti dei dati](../overview.md#data-limits) nella panoramica.

Tenere presente che il servizio è senza stato. Nessun dato viene archiviato nell'account. I risultati vengono restituiti immediatamente nella risposta.


## <a name="step-3-view-the-results"></a>Passaggio 3: View the results

L'analizzatore del sentiment classifica il testo come prevalentemente positivo o negativo, assegnando un punteggio compreso tra 0 e 1. I valori vicini a 0,5 sono neutri o indeterminati. Un punteggio pari a 0,5 indica neutralità. Se una stringa non può essere analizzata per valutarne il sentiment o non viene riscontrato alcun sentiment, il punteggio è sempre 0,5. Se ad esempio si passa una stringa in spagnolo con un codice di lingua inglese, il punteggio è pari a 0,5.

L'output viene restituito immediatamente. È possibile trasmettere i risultati a un'applicazione che accetta JSON o salvare l'output in un file nel sistema locale. Importare quindi l'output in un'applicazione che consente di ordinare, cercare e modificare i dati.

L'esempio seguente mostra la risposta per la raccolta di documenti di questo articolo:

```json
    {
        "documents": [
            {
                "score": 0.9999237060546875,
                "id": "1"
            },
            {
                "score": 0.0000540316104888916,
                "id": "2"
            },
            {
                "score": 0.99990355968475342,
                "id": "3"
            },
            {
                "score": 0.980544924736023,
                "id": "4"
            },
            {
                "score": 0.99996328353881836,
                "id": "5"
            }
        ],
        "errors": []
    }
```

## <a name="sentiment-analysis-v3-public-preview"></a>Anteprima pubblica di Analisi del sentiment versione 3

La [prossima versione di Analisi del sentiment](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-preview/operations/56f30ceeeda5650db055a3c9) è ora disponibile per l'anteprima pubblica. Offre miglioramenti significativi nell'accuratezza e nel dettaglio dell'API per la categorizzazione del testo e l'assegnazione del punteggio.

> [!NOTE]
> * Il formato della richiesta e i [limiti dei dati](../overview.md#data-limits) di Analisi del sentiment versione 3 sono gli stessi della versione precedente.
> * Attualmente, Analisi del sentiment versione 3:
>    * Attualmente supporta le lingue inglese, francese, italiano, giapponese, cinese semplificato e cinese tradizionale.
>    * È disponibile nelle aree seguenti: `Australia East`, `Central Canada`, `Central US`, `East Asia`, `East US`, `East US 2`, `North Europe`, `Southeast Asia`, `South Central US`, `UK South`, `West Europe` e `West US 2`.

|Funzionalità |DESCRIZIONE  |
|---------|---------|
|Miglioramento della precisione     | Miglioramento significativo nel rilevamento di una valutazione positiva, indifferente, negativa e mista nei documenti di testo rispetto alle versioni precedenti.           |
|Punteggio del sentiment a livello di documento e di frase     | Rilevamento della valutazione per un documento e per le rispettive singole frasi. Se il documento include più frasi, viene assegnato un punteggio di valutazione anche a ogni frase.         |
|Categoria e punteggio di valutazione     | Oltre a un punteggio, l'API restituisce ora le categorie di sentiment del testo. Le categorie sono `positive`, `negative`,`neutral` e `mixed`.       |
| Miglioramento dell'output | L'analisi del sentiment restituisce ora informazioni sia per un intero documento di testo sia per le singole frasi. |

### <a name="sentiment-labeling"></a>Assegnazione di etichette per la valutazione

Analisi del sentiment versione 3 può restituire punteggi ed etichette a livello di frase e di documento. I punteggi e le etichette sono `positive`, `negative` e `neutral`. A livello di documento può essere restituita anche l'etichetta (non il punteggio) di sentiment `mixed`. Il sentiment del documento è determinato dall'aggregazione dei punteggi delle frasi.

| Valutazione della frase                                                        | Etichetta di documento restituita |
|---------------------------------------------------------------------------|----------------|
| Almeno una frase positiva, mentre le altre sono neutre. | `positive`     |
| Almeno una frase negativa, mentre le altre sono neutre.  | `negative`     |
| Almeno una frase negativa e almeno una frase positiva.         | `mixed`        |
| Tutte le frasi sono neutre.                                                 | `neutral`      |

### <a name="sentiment-analysis-v3-example-request"></a>Richiesta di esempio di Analisi del sentiment versione 3

Il codice JSON seguente è un esempio di richiesta alla nuova versione di Analisi del sentiment. La formattazione della richiesta è uguale a quella della versione precedente:

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

### <a name="sentiment-analysis-v3-example-response"></a>Risposta di esempio di Analisi del sentiment versione 3

Anche se il formato della richiesta è identico a quello della versione precedente, il formato della risposta è stato modificato. Il codice JSON seguente è un esempio di risposta restituita dalla nuova versione dell'API:

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

### <a name="example-c-code"></a>Codice C# di esempio

Un'applicazione C# di esempio che chiama questa versione di Analisi del sentiment è disponibile in [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/dotnet/Language/SentimentV3.cs).

## <a name="summary"></a>Summary

In questo articolo si sono appresi i concetti e il flusso di lavoro per l'analisi del sentiment con Analisi del testo di Servizi cognitivi di Azure. In sintesi:

+ L'[API Analisi del sentiment](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) è disponibile solo per determinate lingue.
+ I documenti JSON nel corpo della richiesta includono un ID, il testo e il codice della lingua.
+ La richiesta POST viene inviata a un endpoint `/sentiment` usando [una chiave di accesso e un endpoint](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) personalizzati validi per la sottoscrizione.
+ L'output di risposta, costituito da un punteggio del sentiment per ogni ID documento, può essere trasmesso a qualsiasi app che accetta JSON, ad esempio Excel e Power BI, per citarne alcune.

## <a name="see-also"></a>Vedere anche

 [Panoramica di Analisi del testo](../overview.md) [Domande frequenti](../text-analytics-resource-faq.md)</br>
 [Pagina del prodotto Analisi del testo](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Estrarre le espressioni chiave](text-analytics-how-to-keyword-extraction.md)
