---
title: Schema dei metadati di inferenza - Azure
description: In questo articolo verrà illustrato lo schema dei metadati di inferenza.
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: 6239713fc92b75b8ed026a8f04953e92a24c4596
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88701911"
---
# <a name="inference-metadata-schema"></a>Schema dei metadati di inferenza 

Ogni oggetto di inferenza, indipendentemente dal fatto che usi il contratto basato su HTTP o su gRPC, segue il modello a oggetti descritto in questo argomento.

## <a name="object-model"></a>Modello a oggetti

![Modello a oggetti](./media/inference-metadata-schema/object-model.png)
 
|Definizione del tipo|Descrizione|
|---|---|
|Tag|Tag o etichetta associata al risultato. Oltre al tag, si ottiene anche il valore di attendibilità associato al tag.|
|Attributo|Attributi aggiuntivi associati al risultato. È possibile aggiungere nuovi attributi ricevuti dal motore di inferenze insieme al valore di attendibilità.|
|Elenco degli attributi|Elenco di attributi facoltativi.|
|Rectangle|Area rettangolare relativa all'angolo superiore sinistro dell'immagine. Le proprietà obbligatorie saranno "lunghezza", "larghezza", "altezza" e "distanza bordo superiore dall'origine".|
|Classificazione|Etichetta del classificatore, spesso applicabile all'intero campione. Grazie a "tag", è possibile classificare il risultato.|
|Entità|Entità rilevata o identificata nell'esempio. Quando un'entità viene rilevata dal motore di inferenze, ottiene un "tag", attributi aggiuntivi che sono stati dedotti e le coordinate di una casella rettangolare intorno all'entità trovata.|
|Evento|Evento rilevato nell'esempio. Quando viene rilevato un evento nell'esempio, vengono restituiti il nome dell'evento e le proprietà specifiche dell'evento.|
|Movimento|Movimento rilevato nell'esempio. Quando viene rilevato un movimento nell'esempio, vengono restituite le coordinate di un rettangolo di delimitazione in cui viene rilevato il movimento.|
|Testo|Viene restituito il testo associato all'esempio insieme al timestamp di inizio e di fine del testo.|
|Altro|Restituisce altre informazioni generiche sul payload.|

L'esempio seguente contiene un solo evento con alcuni tipi di inferenza supportati:

```
[ 
  // Light Detection 
  { 
    "type": "classification", 
    "subtype": "lightDetection", 
    "classification": { 
      "tag": { "value": "daylight", "confidence": 0.86 }, 
      "attributes": [ 
          { "name": "isBlackAndWhite", "value": "false", "confidence": 0.71 } 
      ] 
    } 
  }, 
 
  // Motion Detection 
  { 
    "type": "motion", 
    "subtype": "motionDetection", 
    "motion": 
    { 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    } 
  }, 
 
  // Yolo V3 
  { 
    "type": "entity", 
    "subtype": "objectDetection",     
    "entity": 
    { 
      "tag": { "value": "dog", "confidence": 0.97 }, 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    } 
  }, 
 
  // Vehicle Identification 
  { 
    "type": "entity", 
    "subtype": "vehicleIdentification",     
    "entity": 
    { 
      "tag": { "value": "007-SPY", "confidence": 0.82 }, 
      "attributes":[   
        { "name": "color", "value": "black", "confidence": 0.90 }, 
        { "name": "body", "value": "coupe", "confidence": 0.87 }, 
        { "name": "make", "value": "Aston Martin", "confidence": 0.35 }, 
        { "name": "model", "value": "DBS V12", "confidence": 0.33 } 
      ], 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    } 
  }, 
 
  // People Identification 
  { 
    "type": "entity", 
    "subtype": "peopleIdentification",     
    "entity": 
    { 
      "tag": { "value":"Erwin Schrödinger", "confidence": 0.50 }, 
      "attributes":[   
        { "name": "age", "value": "73", "confidence": 0.87 }, 
        { "name": "glasses", "value": "yes", "confidence": 0.94 } 
      ], 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    }, 
 
    // Open type coming from the gRPC Map 
    "extensions":  
    { 
      "vector": "e1xkaXNwbGF5c3R5bGUgaVxoYmFyIHtcZnJhYyB7ZH17ZHR9fVx2ZXJ0IFxQc2kgKHQpXHJhbmdsZSA9e1xoYXQge0h9fVx2ZXJ0IFxQc2kgKHQpXHJhbmdsZSB9KQ==", 
      "skeleton": "p1,p2,p3,p4" 
    } 
  }, 
 
  // Captions 
  {     
    "type": "text", 
    "subtype": "speechToText",   
    "text": 
    { 
      "value": "Humor 75%. Confirmed. Self-destruct sequence in T minus 10… 9…", 
      "language": "en-US", 
      "startTimestamp": 12000, 
      "endTimestamp": 13000 
    } 
]
```

## <a name="next-steps"></a>Passaggi successivi

- [Contratto di dati gRPC](grpc-data-contract.md)
- [Contratto di dati HTTP](http-data-contract.md)
