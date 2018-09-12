---
title: API Visione artificiale - Guida introduttiva all'analisi di un'immagine locale con cURL | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: In questa guida introduttiva si analizza un'immagine locale usando Visione artificiale con cURL in Servizi cognitivi.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 93ca3ea6eee3743dfd0c25c9514375ae63a531ee
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2018
ms.locfileid: "43772408"
---
# <a name="quickstart-analyze-a-local-image---rest-curl"></a>Guida introduttiva: Analizzare un'immagine locale - REST, cURL

In questa guida introduttiva si analizza un'immagine locale per estrarre le caratteristiche visive usando Visione artificiale. Per analizzare un'immagine remota, vedere [Analizzare un'immagine remota con cURL](curl-analyze.md).

## <a name="prerequisites"></a>Prerequisiti

Per usare Visione artificiale, è necessario avere una chiave di sottoscrizione. A tale scopo, vedere [Obtaining Subscription Keys](../Vision-API-How-to-Topics/HowToSubscribe.md) (Come ottenere chiavi di sottoscrizione).

## <a name="analyze-a-local-image"></a>Analizzare un'immagine locale

Questo esempio è simile a quello illustrato in [Analizzare un'immagine remota con cURL](curl-analyze.md) tranne per il fatto che l'immagine da analizzare viene letta in locale dal disco. Sono necessarie tre modifiche:

- Modificare il valore di Content-Type impostando `"Content-Type: application/octet-stream"`.
- Sostituire l'opzione `-d` con `--data-binary`.
- Specificare l'immagine da analizzare usando la sintassi seguente: `@C:/Pictures/ImageToAnalyze.jpg`.

Per eseguire l'esempio, seguire questa procedura:

1. Copiare il codice seguente in un editor.
1. Sostituire `<Subscription Key>` con la propria chiave di sottoscrizione valida.
1. Modificare l'URL della richiesta (`https://westcentralus.api.cognitive.microsoft.com/vision/v2.0`) impostando l'indirizzo in cui si sono ottenute le chiavi di sottoscrizione, se necessario.
1. Sostituire `<Image To Analyze>` con l'immagine locale che si vuole analizzare.
1. Facoltativamente, modificare la lingua della risposta (`language=en`).
1. Aprire un prompt dei comandi in un computer in cui è installato cURL.
1. Incollare il codice nella finestra ed eseguire il comando.

>[!NOTE]
>Usare lo stesso percorso nella chiamata REST usto per ottenere le chiavi di sottoscrizione. Se ad esempio si sono ottenute le chiavi di sottoscrizione da westus, sostituire "westcentralus" nell'URL riportato di seguito con "westus".

```json
curl -H "Ocp-Apim-Subscription-Key: <Subscription Key>" -H "Content-Type: application/octet-stream" "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Categories,Description&details=Landmarks&language=en" --data-binary <Image To Analyze>
```

## <a name="analyze-image-response"></a>Riposta alla richiesta di analisi dell'immagine

Viene restituita una risposta con esito positivo in formato JSON, ad esempio:

```json
{
  "categories": [
    {
      "name": "outdoor_water",
      "score": 0.9921875,
      "detail": {
        "landmarks": []
      }
    }
  ],
  "description": {
    "tags": [
      "nature",
      "water",
      "waterfall",
      "outdoor",
      "rock",
      "mountain",
      "rocky",
      "grass",
      "hill",
      "covered",
      "hillside",
      "standing",
      "side",
      "group",
      "walking",
      "white",
      "man",
      "large",
      "snow",
      "grazing",
      "forest",
      "slope",
      "herd",
      "river",
      "giraffe",
      "field"
    ],
    "captions": [
      {
        "text": "a large waterfall over a rocky cliff",
        "confidence": 0.916458423253597
      }
    ]
  },
  "requestId": "b6e33879-abb2-43a0-a96e-02cb5ae0b795",
  "metadata": {
    "height": 959,
    "width": 1280,
    "format": "Jpeg"
  }
}
```

## <a name="next-steps"></a>Passaggi successivi

Esaminare le API Visione artificiale usate per analizzare un'immagine, rilevare celebrità e luoghi di interesse, creare un'anteprima ed estrarre testo scritto a mano e stampato. Per sperimentare rapidamente le API Visione artificiale, provare la [console di test delle API aperta](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Esaminare le API Visione artificiale](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
