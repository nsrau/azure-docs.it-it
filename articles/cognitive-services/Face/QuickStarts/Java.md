---
title: "Avvio rapido: Rilevare i visi in un'immagine con l'API REST di Azure e Java"
titleSuffix: Azure Cognitive Services
description: In questa guida introduttiva si userà l'API REST Viso di Azure con Java per rilevare i visi in un'immagine.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 11/23/2020
ms.custom: devx-track-java
ms.author: pafarley
ms.openlocfilehash: b8ea2908711497db5e86a7dd665548e33b9d9f25
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "96020858"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-rest-api-and-java"></a>Avvio rapido: Rilevare i visi in un'immagine con l'API REST e Java

In questa guida di avvio rapido si userà l'API REST Viso di Azure con Java per rilevare i visi umani in un'immagine.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/cognitive-services/) prima di iniziare. 

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services/)
* Dopo aver creato la sottoscrizione di Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="creare una risorsa Viso"  target="_blank">creare una risorsa Viso <span class="docon docon-navigate-external x-hidden-focus"></span></a> nel portale di Azure per ottenere la chiave e l'endpoint. Al termine della distribuzione, fare clic su **Vai alla risorsa**.
    * La chiave e l'endpoint della risorsa creata sono necessari per connettere l'applicazione all'API Viso. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
    * È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.
* Qualsiasi ambiente IDE Java a scelta.

## <a name="create-the-java-project"></a>Creare il progetto Java

1. Creare una nuova app Java della riga di comando nell'IDE e aggiungere una classe **Main** con un metodo **main**.
1. Importare le librerie seguenti nel progetto Java. Se si usa Maven, verranno fornite le coordinate di Maven per ogni libreria.
   - [Client HTTP Apache](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpclient:4.5.6)
   - [HTTP Apache core](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpcore:4.4.10)
   - [Libreria JSON](https://github.com/stleary/JSON-java) (org.json:json:20180130)
   - [Registrazioni di Apache Commons](https://commons.apache.org/proper/commons-logging/download_logging.cgi) (commons-logging:commons-logging:1.1.2)

## <a name="add-face-detection-code"></a>Aggiungere codice di rilevamento volto

Aprire la classe main del progetto. In questo caso, si aggiungerà il codice necessario per caricare immagini e rilevare i visi.

### <a name="import-packages"></a>Importare pacchetti

Aggiungere le istruzioni `import` seguenti all'inizio del file.

:::code language="java" source="~/cognitive-services-quickstart-code/java/Face/rest/detect.java" id="dependencies":::

### <a name="add-essential-fields"></a>Aggiungere campi fondamentali

Sostituire la classe **Main** con il codice seguente. Questi dati consentono di specificare come connettersi al servizio Viso e dove ottenere i dati di input. Sarà necessario aggiornare il campo `subscriptionKey` con il valore della chiave di sottoscrizione e modificare la stringa `uriBase` in modo che contenga la stringa dell'endpoint corretta. È anche possibile impostare il valore `imageWithFaces` su un percorso che punti a un file di immagine diverso.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

Il campo `faceAttributes` è semplicemente un elenco di alcuni tipi di attributi, che specifica quali informazioni recuperare sui visi rilevati.

:::code language="java" source="~/cognitive-services-quickstart-code/java/Face/rest/detect.java" id="environment":::

### <a name="call-the-face-detection-rest-api"></a>Chiamare l'API REST di rilevamento volto

Aggiungere il metodo **main** con il codice seguente. Il metodo costruisce una chiamata REST all'API Viso per rilevare informazioni sul viso nell'immagine remota (la stringa `faceAttributes` specifica quali attributi del viso recuperare), quindi scrive i dati di output in una stringa JSON.

:::code language="java" source="~/cognitive-services-quickstart-code/java/Face/rest/detect.java" id="main":::

### <a name="parse-the-json-response"></a>Analizzare la risposta JSON.

Direttamente sotto il codice precedente, aggiungere il blocco seguente, che converte i dati JSON restituiti in un formato più facilmente leggibile prima della stampa sulla console. Infine, chiudere il blocco try-catch, il metodo **main** e la classe **Main**.

:::code language="java" source="~/cognitive-services-quickstart-code/java/Face/rest/detect.java" id="print":::

## <a name="run-the-app"></a>Eseguire l'app

Compilare ed eseguire il codice. Una risposta con esito positivo visualizzerà i dati sui volti in formato JSON facilmente leggibile nella finestra della console. Ad esempio:

```json
[{
  "faceRectangle": {
    "top": 131,
    "left": 177,
    "width": 162,
    "height": 162
  }
}]
```

## <a name="extract-face-attributes"></a>Estrarre gli attributi del viso
 
Per estrarre gli attributi del viso, usare il modello di rilevamento 1 e aggiungere il parametro di query `returnFaceAttributes`.

```java
builder.setParameter("detectionModel", "detection_01");
builder.setParameter("returnFaceAttributes", "age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise");
```

La risposta includerà gli attributi del viso. Ad esempio:

```json
[{
  "faceRectangle": {
    "top": 131,
    "left": 177,
    "width": 162,
    "height": 162
  },
  "faceAttributes": {
    "makeup": {
      "eyeMakeup": true,
      "lipMakeup": true
    },
    "facialHair": {
      "sideburns": 0,
      "beard": 0,
      "moustache": 0
    },
    "gender": "female",
    "accessories": [],
    "blur": {
      "blurLevel": "low",
      "value": 0.06
    },
    "headPose": {
      "roll": 0.1,
      "pitch": 0,
      "yaw": -32.9
    },
    "smile": 0,
    "glasses": "NoGlasses",
    "hair": {
      "bald": 0,
      "invisible": false,
      "hairColor": [
        {
          "color": "brown",
          "confidence": 1
        },
        {
          "color": "black",
          "confidence": 0.87
        },
        {
          "color": "other",
          "confidence": 0.51
        },
        {
          "color": "blond",
          "confidence": 0.08
        },
        {
          "color": "red",
          "confidence": 0.08
        },
        {
          "color": "gray",
          "confidence": 0.02
        }
      ]
    },
    "emotion": {
      "contempt": 0,
      "surprise": 0.005,
      "happiness": 0,
      "neutral": 0.986,
      "sadness": 0.009,
      "disgust": 0,
      "anger": 0,
      "fear": 0
    },
    "exposure": {
      "value": 0.67,
      "exposureLevel": "goodExposure"
    },
    "occlusion": {
      "eyeOccluded": false,
      "mouthOccluded": false,
      "foreheadOccluded": false
    },
    "noise": {
      "noiseLevel": "low",
      "value": 0
    },
    "age": 22.9
  },
  "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f"
}]
```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stata creata una semplice applicazione console Java che usa le chiamate REST con l'API Viso di Azure per rilevare visi in un'immagine e restituirne gli attributi. Successivamente, esplorare la documentazione di riferimento dell'API Viso per altre informazioni sugli scenari supportati.

> [!div class="nextstepaction"]
> [API Viso](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
