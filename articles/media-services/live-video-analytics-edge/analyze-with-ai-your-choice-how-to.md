---
title: Analizza i video live con l'intelligenza artificiale scelta-Azure
description: In questo articolo si apprenderà come compilare un modulo IoT Edge che può essere integrato con analisi video in tempo reale su IoT Edge per analizzare i video live usando un modello di visione artificiale di propria scelta.
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: 0ac2af280eefd5ce293a8be422551d5ee6f6d3f3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84260644"
---
# <a name="analyze-live-video-with-ai-of-your-choice"></a>Analizzare i video live con intelligenza artificiale a scelta

In questo articolo si apprenderà come compilare un modulo IoT Edge che può essere integrato con analisi video in tempo reale su IoT Edge per analizzare i video live usando un modello di visione artificiale di propria scelta. 

## <a name="pre-reading"></a>Pre-lettura

[Concetti relativi al grafo multimediale](media-graph-concept.md)

## <a name="media-graph-extension"></a>Estensione grafico multimediale

Analisi video in tempo reale su IoT Edge definisce un modello di estendibilità che consente di estendere le funzionalità di elaborazione del grafico multimediale ai propri componenti di analisi di servizi multimediali tramite un'estensione Graph. Il componente Analytics può usare le tradizionali tecniche di elaborazione di immagini o i modelli di intelligenza artificiale per la visione artificiale. Le estensioni del grafo sono abilitate includendo il nodo [processore di estensione http](media-graph-concept.md#http-extension-processor) in un grafico multimediale. Il processore di estensione HTTP può inoltrare i fotogrammi video a un endpoint HTTP specificato dall'utente e funge da interfaccia per il componente tramite questo. La connessione può essere effettuata a un endpoint locale o remoto e può essere protetta tramite l'autenticazione e la crittografia TLS, se necessario. Inoltre, il processore consente la scalabilità e la codifica facoltative dei fotogrammi video prima che vengano inoltrati.

È possibile scegliere di eseguire il modello di inferenza desiderato in qualsiasi runtime di inferenza disponibile, ad esempio ONNX, TensorFlow, PyTorch o altri, nel proprio contenitore docker. È anche possibile usare il linguaggio di programmazione e le librerie desiderate per esporre le funzionalità di inferenza dell'immagine tramite un server HTTP nel contenitore. Il contenitore di inferenza deve essere distribuito insieme al modulo Edge di analisi video in tempo reale per ottenere prestazioni ottimali e verrà richiamato tramite il processore di estensione HTTP incluso nella topologia Graph.
Inoltre, la frequenza delle chiamate al modulo personalizzato può essere limitata aggiungendo facoltativamente un processore del rilevamento del [movimento](media-graph-concept.md#motion-detection-processor) e un processore di [filtro della frequenza dei fotogrammi](media-graph-concept.md#frame-rate-filter-processor) al processore di estensione http.

Il diagramma seguente illustra il flusso di dati di alto livello:

![Dispositivo perimetrale](./media/analyze-live-video-with-ai-your-choice-how-to/edge-device.png)

In questo modo è possibile analizzare i video usando i modelli di intelligenza artificiale scelti per soddisfare le esigenze aziendali esclusive. I modelli di intelligenza artificiale possono provenire dalla community open source o dai propri data scientist o da un provider di intelligenza artificiale specializzato.

## <a name="media-graph-http-extension-contract-definitions"></a>Definizioni del contratto di estensione HTTP del grafico multimediale

In questa sezione viene definito il contratto HTTP che definisce il flusso di dati.

### <a name="http-extensibility-protocol"></a>Protocollo di estensibilità HTTP  

Il contratto HTTP viene definito come segue:

* Il modulo funge da server HTTP.
* Analisi video in tempo reale sul modulo IoT Edge funge da client HTTP.

### <a name="request"></a>Richiesta

Le richieste dal modulo Live Video Analytics al modulo sono le seguenti:

|||
|---|---|
|POST| `https://hostname/optional-path?optional-query`|
|Accetta|Application/JSON,*/*|
|Autorizzazione| Basic, digest, Bearer (tramite supporto per intestazioni personalizzate)|
|Content-Type|image/jpeg<br/>image/png<br/>image/bmp<br/>image/x-RAW|
|Content-Length|Lunghezza del corpo, in byte|
|User-Agent|Servizi multimediali di Azure|
|Corpo|Byte immagine, con codifica binaria in uno dei tipi di contenuto supportati.|

#### <a name="example"></a>Esempio

```
POST http://localhost:8080/inference HTTP/1.1
Host: localhost:8080
x-ms-client-request-id: d6050cd4-c9f2-42d3-9adc-53ba7e440f17
Content-Type: image/bmp
Content-Length: 519222

(Image Binary Content)
```

### <a name="response"></a>Risposta

Le risposte dal modulo al modulo Live Video Analytics dovrebbero essere le seguenti

|||
|---|---|
|Codici di stato|200 OK-risultati dell'inferenza trovati<br/>204 nessun contenuto: nessun contenuto trovato dall'intelligenza artificiale<br/>400 richiesta non valida-non prevista<br/>500 errore interno del server-non previsto<br/>503 Server occupato-AMS eseguirà il backup in base all'intestazione "Retry-After" o in base a un periodo di tempo predefinito nell'intestazione del case non preimpostata.|
|Content-Type|application/json|
|Content-Length|    Lunghezza del corpo, in byte|
|Corpo|Oggetto JSON con singola proprietà "inferenza".|

#### <a name="example"></a>Esempio

```
HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 468
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 17 Apr 2020 04:44:01 GMT

{
  "inferences": [
    {
      "type": "entity",
      "entity": {
        "tag": { "value": "car", "confidence": 0.9048132 },
        "box": { "l": 0.42681578, "t": 0.47660735, "w": 0.019501392, "h": 0.020954132 }
      }
    },
    {
      "type": "entity",
      "entity": {
        "tag": { "value": "car", "confidence": 0.8953932 },
        "box": { "l": 0.55083525, "t": 0.4843858, "w": 0.046550274, "h": 0.046502113 }
      }
    }    
  ]
}
```

Si consiglia vivamente di restituire le risposte usando documenti JSON validi che seguono lo schema predefinito definito di seguito. In questo modo si garantisce una migliore interoperabilità con altri componenti e possibili funzionalità future aggiunte al modulo di analisi video live.

Se il modulo restituisce una risposta in cui il tipo di contenuto non è "application/json", analisi video in tempo reale codifica il messaggio come contenuto di base 64 e lo serializza come payload JSON opaco.

Se il modulo restituisce una risposta con tipo di contenuto "application/json", ma lo schema JSON non segue lo [schema dei metadati di inferenza descritto di seguito](#inference-metadata-schema), il payload del messaggio verrà inviato attraverso la pipeline, ma l'interoperabilità verrà ridotta.

> [!NOTE]
> Se il modulo non produce alcun risultato, deve restituire il codice di stato HTTP 204 (nessun contenuto) con un corpo della risposta vuoto. L'analisi video in tempo reale ne comprenderà il risultato come un risultato vuoto e non inoltrerà l'evento all'interno della pipeline.

### <a name="inference-metadata-schema"></a>Schema dei metadati di inferenza

Ogni oggetto inferenza segue questo schema superset:

```
{
  "type": "[classification|motion|entity|text|other]",
  "subtype": "",              // Free form subtype id

  // Object has *at most one of* the following
  "classification":
  {
    "tag": 
    {
      "value": "",            // Tag value
      "confidence": 0.0       // Tag confidence
    },
    "attributes":[            // Optional attributes
      {
        "name": "",           
        "value": "",          
        "confidence": 0.0     
      }
    ]
  },

  "motion":
  {
    "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
    // "regionId": ""
  },

  "entity":
  {
    "tag": 
    {
      "value": "",            // Tag value
      "confidence": 0.0       // Tag confidence
    },
    "attributes":[            // Optional attributes
      {
        "name": "",           
        "value": "",          
        "confidence": 0.0     
      }
    ],
    "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
  },

  "text":
  {
    "value": "",              // Text value
    "language": "",           // Optional BCP47 Language Code (https://tools.ietf.org/html/bcp47)
    "startTimestamp": 0,      // Optional start timestamp
    "endTimestamp": 0         // Optional end timestamp
  },

  // Plus every object can have zero or more extensions
  "extensions":
  {
    "name1": "value1",
    "name2": "value2"
    // ...
  }
}
```
 
### <a name="data-contracts---class-hierarchy"></a>Contratti dati-gerarchia di classi

![Contratti dati-gerarchia di classi](./media/analyze-live-video-with-ai-your-choice-how-to/data-contracts.png)

### <a name="examples"></a>Esempi  

L'esempio seguente contiene un solo evento con tutti i tipi di inferenza supportati:

```
{
  "inferences": [
    // Light detection
    {
      "type": "classification",
      "subtype": "lightDetection",
      "classification": {
        "tag": { "value": "daylight", "confidence": 0.86 },
        "attributes": [
            { "name": "isBlackAndWhite", "value": "false", "confidence": 0.71 }
        ]
      }
    },

    // Motion detection
    {
      "type": "motion",
      "subtype": "motionDetection",
      "motion":
      {
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      }
    },

    // Object detection
    {
      "type": "entity",
      "subtype": "objectDetection",    
      "entity":
      {
        "tag": { "value": "dog", "confidence": 0.97 },
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      }
    },

    // Vehicle identification
    {
      "type": "entity",
      "subtype": "vehicleIdentification",    
      "entity":
      {
        "tag": { "value": "007-SPY", "confidence": 0.82 },
        "attributes":[  
          { "name": "color", "value": "black", "confidence": 0.90 },
          { "name": "body", "value": "coupe", "confidence": 0.87 },
          { "name": "make", "value": "Aston Martin", "confidence": 0.35 },
          { "name": "model", "value": "DBS V12", "confidence": 0.33 }
        ],
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      }
    },

    // People identification
    {
      "type": "entity",
      "subtype": "peopleIdentification",    
      "entity":
      {
        "tag": { "value":"Erwin Schrödinger", "confidence": 0.50 },
        "attributes":[  
          { "name": "age", "value": "73", "confidence": 0.87 },
          { "name": "glasses", "value": "yes", "confidence": 0.94 }
        ],
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      },
    }
  ]
}
```
 
## <a name="sample-http-extension-modules"></a>Moduli di estensione HTTP di esempio

Alcuni moduli di estensione HTTP di esempio sono disponibili nel [repository GitHub di analisi video in tempo reale](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis). Uno di questi [esempi di analisi video](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) illustra come usare il modello [Yolov3](https://pjreddie.com/darknet/yolo/) [ONNX](http://onnx.ai/) per compilare un modulo IOT Edge per il rilevamento di oggetti. È possibile usare lo stesso approccio per creare un modulo personalizzato con un modello di intelligenza artificiale di propria scelta.

## <a name="next-steps"></a>Passaggi successivi

[Risolvere i problemi](troubleshoot-how-to.md)
