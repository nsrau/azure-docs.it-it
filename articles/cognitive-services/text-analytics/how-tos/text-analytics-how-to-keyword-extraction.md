---
title: Estrazione di frasi chiave con l'API REST Analisi del testo | Microsoft Docs
description: Come estrarre frasi chiave usando l'API REST Analisi del testo di Servizi cognitivi di Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: bbf72847dd9d9a29bf1f2fa0574b83194d07a5c6
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2019
ms.locfileid: "56245610"
---
# <a name="example-how-to-extract-key-phrases-using-text-analytics"></a>Esempio: Come estrarre frasi chiave con Analisi del testo

L'API [Estrazione frasi chiave](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) valuta il testo non strutturato e restituisce un elenco di espressioni chiave per ogni documento JSON. 

Questa funzionalità è utile se è necessario identificare rapidamente i punti rilevanti in una raccolta di documenti. Ad esempio, dato il testo di input "Il cibo era delizioso e il personale era meraviglioso", il servizio restituisce i punti rilevanti del discorso, ovvero "cibo" e "personale meraviglioso".

Per l'estrazione di espressioni chiave sono attualmente supportate le lingue inglese, tedesco, spagnolo e giapponese. Altre lingue sono disponibili in anteprima. Per altre informazioni, vedere [Linguaggi supportati](../text-analytics-supported-languages.md).

> [!TIP]
> Analisi del testo offre anche un'immagine del contenitore Docker basata su Linux per l'estrazione delle frasi chiave, di conseguenza è possibile [installare ed eseguire il contenitore di Analisi del testo](text-analytics-how-to-install-containers.md) vicino ai dati.

## <a name="preparation"></a>Operazioni preliminari

L'estrazione di frasi chiave risulta più efficiente quando si elaborano grandi quantità di testo, diversamente dall'analisi del sentiment, che offre prestazioni migliori con quantità minori di testo. Per ottenere risultati ottimali da entrambe le operazioni, provare a ristrutturare gli input di conseguenza.

È necessario disporre di documenti JSON nel formato seguente: id, testo, lingua

Le dimensioni dei singoli documenti devono essere inferiori a 5000 caratteri e si possono avere fino a 1000 elementi (ID) per ogni raccolta. La raccolta viene inviata nel corpo della richiesta. L'esempio seguente illustra il contenuto che è possibile inviare per l'estrazione di espressioni chiave.

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

I dettagli sulla definizione della richiesta sono reperibili in [How to call the Text Analytics API](text-analytics-how-to-call-api.md) (Come chiamare l'API Analisi del testo). Per comodità si ridefiniscono i punti seguenti:

+ Creare una richiesta **POST**. Esaminare la documentazione dell'API per la richiesta: [API Frasi chiave](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)

+ Impostare l'endpoint HTTP per l'estrazione delle frasi chiave usando una risorsa di Analisi del testo in Azure oppure un [contenitore di Analisi del testo](text-analytics-how-to-install-containers.md) di cui è stata creata un'istanza. È necessario includere la risorsa `/keyPhrases`: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases`

+ Impostare un'intestazione della richiesta in modo da includere la chiave di accesso per le operazioni di Analisi del testo. Per altre informazioni, vedere [How to find endpoints and access keys](text-analytics-how-to-access-key.md) (Come trovare gli endpoint e le chiavi di accesso).

+ Nel corpo della richiesta specificare la raccolta di documenti JSON preparata per l'analisi.

> [!Tip]
> Usare [Postman](text-analytics-how-to-call-api.md) oppure aprire la **console di test dell'API** nella [documentazione](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) per strutturare una richiesta e inviarla tramite POST al servizio.

## <a name="step-2-post-the-request"></a>Passaggio 2: Pubblicare la richiesta

Al momento della ricezione della richiesta viene eseguita l'analisi. Il servizio accetta fino a 100 richieste al minuto. Ogni richiesta può essere al massimo di 1 MB.

Tenere presente che il servizio è senza stato. Nessun dato viene archiviato nell'account. I risultati vengono restituiti immediatamente nella risposta.

## <a name="step-3-view-results"></a>Passaggio 3: Visualizzare i risultati

Tutte le richieste POST restituiscono una risposta JSON formattata con gli ID e le proprietà rilevate.

L'output viene restituito immediatamente. Si possono trasmettere i risultati a un'applicazione che accetta JSON o salvare l'output in un file nel sistema locale e quindi importarlo in un'applicazione che consente di ordinare, cercare e modificare i dati.

Di seguito è illustrato un esempio dell'output di estrazione di frasi chiave:

```json
    "documents": [
        {
            "keyPhrases": [
                "year",
                "trail",
                "trip",
                "views"
            ],
            "id": "1"
        },
        {
            "keyPhrases": [
                "marked trails",
                "Worst hike",
                "goners"
            ],
            "id": "2"
        },
        {
            "keyPhrases": [
                "trail",
                "small children",
                "family"
            ],
            "id": "3"
        },
        {
            "keyPhrases": [
                "spectacular views",
                "trail",
                "area"
            ],
            "id": "4"
        },
        {
            "keyPhrases": [
                "places",
                "beautiful views",
                "favorite trail"
            ],
            "id": "5"
        }
```

Come indicato, l'analizzatore trova e ignora le parole non essenziali e mantiene singoli termini o espressioni che sembrano essere il soggetto o l'oggetto di una frase. 

## <a name="summary"></a>Summary

In questo articolo si sono appresi i concetti e il flusso di lavoro per l'estrazione di espressioni chiave tramite Analisi del testo in Servizi cognitivi. In sintesi:

+ L'[API Estrazione frasi chiave](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) è disponibile per le lingue selezionate.
+ I documenti JSON nel corpo della richiesta includono un ID, il testo e il codice della lingua.
+ La richiesta POST viene indirizzata a un endpoint `/keyphrases` usando una [chiave di accesso personalizzata e un endpoint](text-analytics-how-to-access-key.md) valido per la sottoscrizione.
+ L'output di risposta, costituito da parole o espressioni chiave per ogni ID documento, può essere trasmesso a qualsiasi app che accetta JSON, tra cui Excel e Power BI, per citarne alcune.

## <a name="see-also"></a>Vedere anche  

 [Panoramica di Analisi del testo](../overview.md)  
 [Domande frequenti (FAQ)](../text-analytics-resource-faq.md)</br>
 [Pagina del prodotto Analisi del testo](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [API Analisi del testo](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)
