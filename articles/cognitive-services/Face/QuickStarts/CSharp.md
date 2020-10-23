---
title: "Avvio rapido: Rilevare i visi in un'immagine con l'API REST di Azure e C#"
titleSuffix: Azure Cognitive Services
description: In questa guida introduttiva si userà l'API REST Viso di Azure con C# per rilevare i visi in un'immagine.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: 1b8bcaf283e612b3ebe6d6b7bb5660e8b3179ad3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91858371"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-c"></a>Avvio rapido: Rilevare i visi in un'immagine con l'API REST Viso e C#

In questa guida di avvio rapido si userà l'API REST Viso di Azure con C# per rilevare i visi umani in un'immagine.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/cognitive-services/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services/)
* Dopo aver creato la sottoscrizione di Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="creare una risorsa Viso"  target="_blank">creare una risorsa Viso <span class="docon docon-navigate-external x-hidden-focus"></span></a> nel portale di Azure per ottenere la chiave e l'endpoint. Al termine della distribuzione, fare clic su **Vai alla risorsa**.
    * La chiave e l'endpoint della risorsa creata sono necessari per connettere l'applicazione all'API Viso. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
    * È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.
- Qualsiasi edizione di [Visual Studio](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Creare il progetto di Visual Studio

1. In Visual Studio creare un nuovo progetto **App console (.NET Framework)** e assegnargli il nome **FaceDetection**.
1. Se la soluzione contiene anche altri progetti, selezionare questo come progetto di avvio singolo.

## <a name="add-face-detection-code"></a>Aggiungere codice di rilevamento volto

Aprire il nuovo file *Program.cs* del progetto. In questo caso, si aggiungerà il codice necessario per caricare immagini e rilevare i visi.

### <a name="include-namespaces"></a>Includere gli spazi dei nomi

Aggiungere le istruzioni `using` seguenti all'inizio del file *Program.cs*.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="dependencies":::

### <a name="add-essential-fields"></a>Aggiungere campi fondamentali

Aggiungere la classe **Program** contenente i campi seguenti. Questi dati consentono di specificare come connettersi al servizio Viso e dove ottenere i dati di input. Sarà necessario aggiornare il campo `subscriptionKey` con il valore della chiave di sottoscrizione e potrebbe essere necessario modificare la stringa `uriBase` in modo che contenga la stringa dell'endpoint della risorsa.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="environment":::

### <a name="receive-image-input"></a>Ricevere l'input dell'immagine

Aggiungere il codice seguente al metodo **Main** della classe **Program**. Questo codice scrive un prompt nella console che chiede all'utente di immettere il percorso del file di immagine locale. Viene quindi chiamato un altro metodo, **MakeAnalysisRequest**, per elaborare l'immagine nel percorso specificato.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="main":::

### <a name="call-the-face-detection-rest-api"></a>Chiamare l'API REST di rilevamento volto

Aggiungere il metodo seguente alla classe **Program**. Il metodo costruisce una chiamata REST all'API Viso per rilevare informazioni sul viso nell'immagine remota (la stringa `requestParameters` specifica quali attributi del viso recuperare), quindi scrive i dati di output in una stringa JSON.

Nei passaggi seguenti verranno definiti i metodi helper.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="request":::

### <a name="process-the-input-image-data"></a>Elaborare i dati di immagine di input

Aggiungere il metodo seguente alla classe **Program**. Questo metodo converte in una matrice di byte l'immagine in corrispondenza del percorso file specificato.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="getimage":::

### <a name="parse-the-json-response"></a>Analizzare la risposta JSON.

Aggiungere il metodo seguente alla classe **Program**. Questo metodo formatta l'input JSON in modo da renderlo più facilmente leggibile. L'app scriverà i dati stringa nella console. È quindi possibile chiudere la classe e lo spazio dei nomi.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="print":::

## <a name="run-the-app"></a>Eseguire l'app

Una risposta con esito positivo visualizzerà i dati sui volti in formato JSON facilmente leggibile. Ad esempio:

```json
[
   {
      "faceId": "f7eda569-4603-44b4-8add-cd73c6dec644",
      "faceRectangle": {
         "top": 131,
         "left": 177,
         "width": 162,
         "height": 162
      }
   }
]
```

## <a name="extract-face-attributes"></a>Estrarre gli attributi del viso
 
Per estrarre gli attributi del viso, usare il modello di rilevamento 1 e aggiungere il parametro di query `returnFaceAttributes`.

```csharp
string requestParameters = "detectionModel=detection_01&returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise";
```

La risposta includerà gli attributi del viso. Ad esempio:

```json
[
   {
      "faceId": "f7eda569-4603-44b4-8add-cd73c6dec644",
      "faceRectangle": {
         "top": 131,
         "left": 177,
         "width": 162,
         "height": 162
      },
      "faceAttributes": {
         "smile": 0.0,
         "headPose": {
            "pitch": 0.0,
            "roll": 0.1,
            "yaw": -32.9
         },
         "gender": "female",
         "age": 22.9,
         "facialHair": {
            "moustache": 0.0,
            "beard": 0.0,
            "sideburns": 0.0
         },
         "glasses": "NoGlasses",
         "emotion": {
            "anger": 0.0,
            "contempt": 0.0,
            "disgust": 0.0,
            "fear": 0.0,
            "happiness": 0.0,
            "neutral": 0.986,
            "sadness": 0.009,
            "surprise": 0.005
         },
         "blur": {
            "blurLevel": "low",
            "value": 0.06
         },
         "exposure": {
            "exposureLevel": "goodExposure",
            "value": 0.67
         },
         "noise": {
            "noiseLevel": "low",
            "value": 0.0
         },
         "makeup": {
            "eyeMakeup": true,
            "lipMakeup": true
         },
         "accessories": [

         ],
         "occlusion": {
            "foreheadOccluded": false,
            "eyeOccluded": false,
            "mouthOccluded": false
         },
         "hair": {
            "bald": 0.0,
            "invisible": false,
            "hairColor": [
               {
                  "color": "brown",
                  "confidence": 1.0
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
         }
      }
   }
]
```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stata creata una semplice applicazione console .NET che usa le chiamate REST con il servizio Viso di Azure per rilevare i visi in un'immagine e restituire i relativi attributi. Successivamente, esplorare la documentazione di riferimento dell'API Viso per altre informazioni sugli scenari supportati.

> [!div class="nextstepaction"]
> [API Viso](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
