---
title: API Visione artificiale - Guida introduttiva all'analisi di un'immagine remota con cURL | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: In questa guida introduttiva si analizza un'immagine remota usando Visione artificiale con cURL in Servizi cognitivi.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: c092432adbcba9cf7a131584131fd05991d1edef
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2018
ms.locfileid: "43772281"
---
# <a name="quickstart-analyze-a-remote-image---rest-curl"></a>Guida introduttiva: Analizzare un'immagine remota - REST, cURL

In questa guida introduttiva si analizza un'immagine remota per estrarre le caratteristiche visive usando Visione artificiale. Per analizzare un'immagine locale, vedere [Analizzare un'immagine locale con cURL](curl-disk.md).

## <a name="prerequisites"></a>Prerequisiti

Per usare Visione artificiale, è necessario avere una chiave di sottoscrizione. A tale scopo, vedere [Obtaining Subscription Keys](../Vision-API-How-to-Topics/HowToSubscribe.md) (Come ottenere chiavi di sottoscrizione).

## <a name="analyze-a-remote-image"></a>Analizzare un'immagine remota

Con il [metodo Analyze Image](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa), è possibile estrarre caratteristiche visive in base al contenuto di un'immagine. È possibile caricare un'immagine o specificare l'URL di un'immagine e scegliere le caratteristiche da restituire, tra cui:

* Un elenco dettagliato di tag correlati al contenuto dell'immagine.
* Una frase completa di descrizione del contenuto dell'immagine.
* Le coordinate, il sesso e l'età di tutti i visi presenti nell'immagine.
* Il tipo di immagine (ClipArt o disegno di linee).
* Il colore principale, il colore dominante o l'indicazione se un'immagine è in bianco e nero.
* La categoria definita in questa [tassonomia](../Category-Taxonomy.md).
* L'indicazione se l'immagine presenta contenuto per adulti o con riferimenti sessuali.

Per eseguire l'esempio, seguire questa procedura:

1. Copiare il codice seguente in un editor.
1. Sostituire `<Subscription Key>` con la propria chiave di sottoscrizione valida.
1. Modificare l'URL della richiesta (`https://westcentralus.api.cognitive.microsoft.com/vision/v2.0`) impostando l'indirizzo in cui si sono ottenute le chiavi di sottoscrizione, se necessario.
1. Facoltativamente, modificare l'immagine (`{\"url\":\"...`) da analizzare.
1. Facoltativamente, modificare la lingua della risposta (`language=en`).
1. Aprire un prompt dei comandi in un computer in cui è installato cURL.
1. Incollare il codice nella finestra ed eseguire il comando.

>[!NOTE]
>Usare lo stesso percorso nella chiamata REST usto per ottenere le chiavi di sottoscrizione. Se ad esempio si sono ottenute le chiavi di sottoscrizione da westus, sostituire "westcentralus" nell'URL riportato di seguito con "westus".

## <a name="analyze-image-request"></a>Richiesta di analisi dell'immagine

```json
curl -H "Ocp-Apim-Subscription-Key: <Subscription Key>" -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Categories,Description&details=Landmarks&language=en" -d "{\"url\":\"http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg\"}"
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
