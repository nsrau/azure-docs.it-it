---
title: Usare il riconoscimento di entità con l'API Analisi del testo
titleSuffix: Azure Cognitive Services
description: Scopri come identificare e disambiguare l'identità di un'entità presente nel testo con l'API REST Di analisi del testo.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 02/10/2020
ms.author: aahi
ms.openlocfilehash: 243086ddaae47eba20eea6877fe6d7f8f9889290
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79203492"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>Come utilizzare il riconoscimento delle entità denominate nell'analisi del testo

L'API Analisi del testo ti consente di eseguire testo non strutturato e di restituire un elenco di entità senza ambiguità, con collegamenti a ulteriori informazioni sul Web. L'API supporta sia il riconoscimento delle entità denominate (NER) che il collegamento di entità.

### <a name="entity-linking"></a>Collegamento delle entità

Il collegamento di entità è la capacità di identificare e disambiguare l'identità di un'entità trovata nel testo (ad esempio, determinare se un'occorrenza della parola `Mars` si riferisce al pianeta o al dio romano della guerra). Questo processo richiede la presenza di una Knowledge Base in una lingua appropriata, per collegare entità riconosciute nel testo. Entity Linking usa [Wikipedia](https://www.wikipedia.org/) come base di conoscenze.


### <a name="named-entity-recognition-ner"></a>Riconoscimento delle entità denominate (NER)

Il riconoscimento delle entità denominate (NER) è la capacità di identificare entità diverse nel testo e categorizzarle in classi o tipi predefiniti, ad esempio: persona, luogo, evento, prodotto e organizzazione.  

A partire dalla versione 3, questa funzionalità dell'API Analisi del testo può anche identificare tipi di informazioni personali e sensibili, ad esempio: numero di telefono, numero di previdenza sociale, indirizzo e-mail e numero di conto bancario.  L'identificazione di queste entità può aiutare a classificare i documenti sensibili e a rendere più le informazioni personali.

## <a name="named-entity-recognition-versions-and-features"></a>Versioni e funzionalità di Riconoscimento entità denominate

L'API Analisi del testo offre due versioni di Named Entity Recognition: v2 e v3. Versione 3 (anteprima pubblica) fornisce maggiori dettagli nelle entità che possono essere rilevate e categorizzate.

| Funzionalità                                                         | NER v2 | NER v3 |
|-----------------------------------------------------------------|--------|--------|
| Metodi per richieste singole e batch                          | X      | X      |
| Riconoscimento di base delle entità in diverse categorie              | X      | X      |
| Classificazione espansa per le entità riconosciute                 |        | X      |
| Endpoint separati per l'invio di richieste di collegamento di entità e NER. |        | X      |
| Gestione della versione dei modelli                                                |        | X      |

Per informazioni, vedere [Supporto linguistico.](../language-support.md#sentiment-analysis-key-phrase-extraction-and-named-entity-recognition)


#### <a name="version-30-preview"></a>[Versione 3.0-preview](#tab/version-3)

### <a name="entity-types"></a>Tipi di entità

Il riconoscimento delle entità denominate v3 offre il rilevamento espanso tra più tipi. Attualmente, NER v3 è in grado di riconoscere le seguenti categorie di entità:

* Generale
* Informazioni personali 

Per un elenco dettagliato delle entità e dei linguaggi supportati, vedere l'articolo sui [tipi di entità supportati da NER v3.](../named-entity-types.md)

### <a name="request-endpoints"></a>Richiedi endpoint

Il riconoscimento delle entità denominate v3 utilizza endpoint separati per le richieste NER e di collegamento di entità. Utilizza un formato URL di seguito in base alla tua richiesta:

Ner
* Entità generali -`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* Informazioni personali -`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

Collegamento di entità
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

### <a name="model-versioning"></a>Gestione della versione dei modelli

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

#### <a name="version-21"></a>[Versione 2.1](#tab/version-2)

### <a name="entity-types"></a>Tipi di entità

> [!NOTE]
> Il riconoscimento delle entità denominate (NER) versione 2 supporta solo le entità seguenti. NER v3 è in anteprima pubblica ed espande notevolmente il numero e la profondità delle entità riconosciute nel testo.   

| Type  | Sottotipo | Esempio |
|:-----------   |:------------- |:---------|
| Persona        | N/D\*         | "Jeff", "Bill Gates"     |
| Location      | N/D\*         | "Redmond, Washington", "Parigi"  |
| Organization  | N/D\*         | "Microsoft"   |
| Quantità      | Number        | "6", "sei"     |
| Quantità      | Percentuale    | "50%", "cinquanta percento"|
| Quantità      | Ordinal       | "2°", "secondo"     |
| Quantità      | Age           | "90 giorni", "30 anni"    |
| Quantità      | Valuta      | "$ 10,99"     |
| Quantità      | Dimension     | "10 miglia", "40 cm"     |
| Quantità      | Temperatura   | "32 gradi"    |
| Datetime      | N/D\*         | "4 febbraio 2012 18:30"      |
| Datetime      | Data          | "2 maggio 2017", "02/05/2017"   |
| Datetime      | Tempo          | "8", "8:00"  |
| Datetime      | DateRange     | "Dal 2 maggio al 5 maggio"    |
| Datetime      | TimeRange     | "dalle 18 alle 19"     |
| Datetime      | Duration      | "1 minuto e 45 secondi"   |
| Datetime      | Set           | "ogni martedì"     |
| URL           | N/D\*         | "https:\//www.bing.com"    |
| Email         | N/D\*         | "support@contoso.com" |
| Numero di telefono DEGLI Stati Uniti  | N/D\*         | (solo numeri di telefono USA) "(312) 555-0176" |
| Indirizzo IP    | N/D\*         | "10.0.0.100" |

\*A seconda delle entità immesse ed estratte, alcune entità possono omettere `SubType`.  Tutti i tipi di entità supportati elencati sono disponibili solo per le lingue inglese, cinese semplificato, francese, tedesco e spagnolo.

### <a name="request-endpoints"></a>Richiedi endpoint

Il riconoscimento delle entità denominate v2 usa un singolo endpoint per le richieste NER e di collegamento di entità:Named Entity Recognition v2 uses a single endpoint for NER and entity linking requests:

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`

---

## <a name="sending-a-rest-api-request"></a>Invio di una richiesta all'API REST

### <a name="preparation"></a>Preparazione

È necessario disporre di documenti JSON in questo formato: ID, testo, lingua.

Ogni documento deve essere inferiore a 5.120 caratteri e puoi avere fino a 1.000 elementi (ID) per raccolta. La raccolta viene inviata nel corpo della richiesta.

### <a name="structure-the-request"></a>Strutturare la richiesta

Creare una richiesta POST. È possibile [utilizzare Postman](text-analytics-how-to-call-api.md) o la console di **test API** nei collegamenti seguenti per strutturare e inviarne rapidamente uno. 

> [!NOTE]
> È possibile trovare la chiave e l'endpoint per la risorsa Analisi del testo nel portale di Azure. Si trovano in **Gestione risorse** nella pagina **Avvio rapido** della risorsa. 

#### <a name="version-30-preview"></a>[Versione 3.0-preview](#tab/version-3)

[Informazioni di riferimento sul riconoscimento delle entità denominate v3](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral)

La versione 3 usa endpoint separati per le richieste NER e di collegamento di entità. Utilizza un formato URL di seguito in base alla tua richiesta:

Ner
* Entità generali -`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* Entità di informazioni personali -`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

Collegamento di entità
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

#### <a name="version-21"></a>[Versione 2.1](#tab/version-2)

[Riferimento al riconoscimento delle entità denominate (NER) v2](https://eastus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

La versione 2 usa l'endpoint seguente per il collegamento di entità e le richieste NER:Version 2 uses the following endpoint for entity linking and NER requests: 

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`

---

Impostare un'intestazione della richiesta per includere la chiave dell'API Analisi del testo. Nel corpo della richiesta fornire i documenti JSON preparati.

### <a name="example-ner-request"></a>Esempio di richiesta NER 

Di seguito è riportato un esempio di contenuto che è possibile inviare all'API. Il formato della richiesta è identico per entrambe le versioni dell'API.

```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "I had a wonderful trip to Seattle last week."
    }
  ]
}
```

## <a name="post-the-request"></a>Pubblicare la richiesta

Al momento della ricezione della richiesta viene eseguita l'analisi. Vedere la sezione relativa ai [limiti dei data](../overview.md#data-limits) nella panoramica per informazioni sulle dimensioni e il numero di richieste che è possibile inviare al minuto e al secondo.

L'API Analisi del testo è senza stato. Non vengono archiviati dati nell'account e i risultati vengono restituiti immediatamente nella risposta.

## <a name="view-results"></a>Visualizzazione dei risultati

Tutte le richieste POST restituiscono una risposta in formato JSON con gli ID e le proprietà dell'entità rilevate.

L'output viene restituito immediatamente. Si possono trasmettere i risultati a un'applicazione che accetta JSON o salvare l'output in un file nel sistema locale e quindi importarlo in un'applicazione che consente di ordinare, cercare e modificare i dati. A causa del supporto multilingue ed emoji, la risposta può contenere offset di testo. Per ulteriori informazioni, vedere [come elaborare gli offset](../concepts/text-offsets.md) di testo.

#### <a name="version-30-preview"></a>[Versione 3.0-anteprima)](#tab/version-3)

### <a name="example-v3-responses"></a>Risposte v3 di esempio

La versione 3 fornisce endpoint separati per NER e il collegamento di entità. Le risposte per entrambe le operazioni sono riportate di seguito. 

#### <a name="example-ner-response"></a>Esempio di risposta NER

```json
{
    "documents": [{
    "id": "1",
    "entities": [{
        "text": "Seattle",
        "type": "Location",
        "offset": 26,
        "length": 7,
        "score": 0.80624294281005859
    }, {
        "text": "last week",
        "type": "DateTime",
        "subtype": "DateRange",
        "offset": 34,
        "length": 9,
        "score": 0.8
    }]
    }],
    "errors": [],
    "modelVersion": "2019-10-01"
}
```

#### <a name="example-entity-linking-response"></a>Risposta di collegamento di entità di esempioExample entity linking response

```json
{
  "documents": [{
    "id": "1",
    "entities": [{
      "name": "Seattle",
      "matches": [{
        "text": "Seattle",
        "offset": 26,
        "length": 7,
        "score": 0.15046201222847677
      }],
      "language": "en",
      "id": "Seattle",
      "url": "https://en.wikipedia.org/wiki/Seattle",
      "dataSource": "Wikipedia"
    }]
  }],
  "errors": [],
  "modelVersion": "2019-10-01"
}
```

#### <a name="version-21"></a>[Versione 2.1](#tab/version-2)

### <a name="example-ner-v2-response"></a>Esempio di risposta NER v2
```json
{
  "documents": [{
    "id": "1",
    "entities": [{
      "name": "Seattle",
      "matches": [{
        "wikipediaScore": 0.15046201222847677,
        "entityTypeScore": 0.80624294281005859,
        "text": "Seattle",
        "offset": 26,
        "length": 7
      }],
      "wikipediaLanguage": "en",
      "wikipediaId": "Seattle",
      "wikipediaUrl": "https://en.wikipedia.org/wiki/Seattle",
      "bingId": "5fbba6b8-85e1-4d41-9444-d9055436e473",
      "type": "Location"
    }, {
      "name": "last week",
      "matches": [{
        "entityTypeScore": 0.8,
        "text": "last week",
        "offset": 34,
        "length": 9
      }],
      "type": "DateTime",
      "subType": "DateRange"
    }]
  }],
  "errors": []
}
```

---

## <a name="summary"></a>Riepilogo

In questo articolo si sono appresi i concetti e il flusso di lavoro per il collegamento delle entità usando Analisi del testo nei Servizi cognitivi. In sintesi:

* Il riconoscimento delle entità denominate è disponibile per le lingue selezionate in due versioni.
* I documenti JSON nel corpo della richiesta includono un ID, il testo e il codice della lingua.
* Le richieste POST vengono inviate a uno o più endpoint, usando una chiave di accesso personalizzata [e un endpoint](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) valido per la sottoscrizione.
* L'output di risposta, che consiste di entità collegate (inclusi punteggi di attendibilità, offset e collegamenti Web per ogni ID di documento) può essere usato in qualsiasi applicazione

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica di Analisi del testo](../overview.md)
* [Uso della libreria client di Analisi del testo](../quickstarts/text-analytics-sdk.md)
* [Novità](../whats-new.md)
