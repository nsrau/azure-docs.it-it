---
title: Protocollo di estensione HTTP - Azure
description: Questo articolo illustra come usare il protocollo di estensione HTTP per inviare messaggi tra il modulo Analisi video live e il modulo di intelligenza artificiale o visione artificiale.
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: f1e1fb0e8fe63b3a83c59a4ec48abdac7f22096a
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92016655"
---
# <a name="http-extension-protocol"></a>Protocollo di estensione HTTP

Questo articolo illustra come usare il protocollo di estensione HTTP per inviare messaggi tra il modulo Analisi video live e il modulo di intelligenza artificiale o visione artificiale.

Il contratto HTTP viene definito tra i due componenti seguenti:

* Server HTTP
* Il modulo Analisi video live in IoT Edge funge da client HTTP

## <a name="request"></a>Richiesta

Le richieste inviate dal modulo Analisi video live al server HTTP saranno le seguenti:

|Chiave|Valore|
|---|---|
|POST|`https://hostname/optional-path?optional-query`|
|Accetta|application/json,  */*|
|Autorizzazione|Basic, Digest, Bearer (tramite il supporto per intestazioni personalizzate)|
|Content-Type|image/jpeg<br/>image/png<br/>image/bmp<br/>image/x-raw|
|Lunghezza del corpo di Content-Length, in byte|
|User-Agent|Servizi multimediali di Azure|
|Corpo|Byte immagine, con codifica binaria in uno dei tipi di contenuto supportati.|

### <a name="example"></a>Esempio

```
POST http://localhost:8080/inference HTTP/1.1
Host: localhost:8080
x-ms-client-request-id: d6050cd4-c9f2-42d3-9adc-53ba7e440f17
Content-Type: image/bmp
Content-Length: 519222

(Image Binary Content)
```

## <a name="response"></a>Risposta

Le risposte inviate dal modulo personale al modulo Analisi video live devono essere le seguenti:

|Chiave|Valore|
|---|---|
|Codici di stato|200 OK - Risultati dell'inferenza trovati<br/>204 Nessun contenuto - Il modulo di intelligenza artificiale non ha trovato contenuti<br/>400 Richiesta non valida - Non previsto<br/>500 Errore interno del server - Non previsto<br/>503 Server occupato - Servizi multimediali di Azure verrà disattivato in base all'intestazione "Retry-After" o in base a un intervallo di tempo predefinito nel caso in cui l'intestazione non sia preimpostata.|
|Content-Type|application/json|
|Content-Length|Lunghezza del corpo, in byte|
|Corpo|Oggetto JSON con un'unica proprietà "inferences".|

### <a name="example"></a>Esempio

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

Per la restituzione delle risposte è vivamente consigliato l'uso di documenti JSON validi conformi allo schema prestabilito definito di seguito. In questo modo si garantisce una migliore interoperabilità con altri componenti e con possibili funzionalità future aggiunte al modulo Analisi video live.

Se il modulo restituisce una risposta in cui il tipo di contenuto non è "application/json", Analisi video live codifica il messaggio come contenuto in formato Base64 e lo serializza come payload JSON opaco.

Se il modulo restituisce una risposta con tipo di contenuto "application/json", ma lo schema JSON non è conforme allo schema dei metadati di inferenza descritto di seguito, il payload del messaggio verrà inoltrato tramite la pipeline, ma l'interoperabilità risulterà ridotta.

> [!NOTE]
> Se il modulo non produce alcun risultato, deve restituire il codice di stato HTTP 204 (Nessun contenuto) con un corpo della risposta vuoto. Analisi video live interpreterà il risultato come vuoto e non inoltrerà l'evento tramite la pipeline.

## <a name="data-contracts---class-hierarchy"></a>Contratti di dati - gerarchia di classi

![gerarchia di classi](./media/http-extension-protocol/class-hierarchy.png)

## <a name="next-steps"></a>Passaggi successivi

[Contratto di dati gRPC](./grpc-extension-protocol.md)