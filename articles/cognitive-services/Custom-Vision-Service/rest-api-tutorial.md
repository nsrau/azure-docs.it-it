---
title: "Esercitazione: Usare l'API REST di Servizio visione artificiale personalizzato"
titlesuffix: Azure Cognitive Services
description: Usare l'API REST per creare, eseguire il training, testare ed esportare un modello di Servizio visione artificiale personalizzato.
services: cognitive-services
author: blackmist
manager: cgronlun
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 08/07/2018
ms.author: larryfr
ms.openlocfilehash: 2f28af14c0579a7a6b514d66d2b66a540adb8fac
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55226520"
---
# <a name="tutorial-use-the-custom-vision-rest-api"></a>Esercitazione: Usare l'API REST di Servizio visione artificiale personalizzato

Informazioni sull'uso dell'API REST di Servizio visione artificiale personalizzato per creare, eseguire il training, testare ed esportare un modello.

Le informazioni contenute in questo documento illustrano come usare un client REST per lavorare con l'API REST per il training del Servizio visione artificiale personalizzato. Gli esempi mostrano come usare l'API tramite l'utilità `curl` da un ambiente bash e `Invoke-WebRequest` da Windows PowerShell.

> [!div class="checklist"]
> * Ottenere le chiavi
> * Creare un progetto
> * Creare tag
> * Aggiungere immagini
> * Eseguire il training e il test del modello
> * Esportare il modello

## <a name="prerequisites"></a>Prerequisiti

* Conoscenza di base del trasferimento di stato rappresentativo (REST). Questo documento non fornisce informazioni dettagliate su aspetti quali verbi HTTP, JSON o altri elementi in genere usati con REST.

* Sia un bash (Bourne Again Shell) con utilità [curl](https://curl.haxx.se) che Windows PowerShell 3.0 (o versione successiva).

* Un account di Servizio visione artificiale personalizzato. Per altre informazioni, vedere il documento [Creare un classificatore](getting-started-build-a-classifier.md).

## <a name="get-keys"></a>Ottenere le chiavi

Quando si usa l'API REST, è necessario eseguire l'autenticazione usando una chiave. Quando si eseguono operazioni di training o di gestione, viene usata la __chiave di training__. Quando si usa il modello per fare stime, viene usata la __chiave di stima__.

Quando si effettua una richiesta, la chiave viene inviata come intestazione della richiesta.

Per ottenere le chiavi usate per l'account, visitare la [pagina Web di Servizio visione artificiale personalizzato](https://customvision.ai) e selezionare l'__icona a forma di ingranaggio__ in alto a destra. Nella sezione __Account__ copiare i valori dei campi __Training Key__ (Chiave di training) e __Prediction Key__ (Chiave di stima).

![Immagine dell'interfaccia utente delle chiavi](./media/rest-api-tutorial/training-prediction-keys.png)

> [!IMPORTANT]
> Poiché le chiavi vengono usate per autenticare ogni richiesta, gli esempi contenuti in questo documento presuppongono che i valori delle chiavi siano contenuti nelle variabili di ambiente. Usare i comandi seguenti per archiviare le chiavi per le variabili di ambiente prima di usare eventuali altri frammenti di codice in questo documento:
>
> ```bash
> read -p "Enter the training key: " TRAININGKEY
> read -p "Enter the prediction key: " PREDICTIONKEY
> ```
>
> ```powershell
> $trainingKey = Read-Host 'Enter the training key'
> $predictionKey = Read-Host 'Enter the prediction key'
> ```

## <a name="create-a-new-project"></a>Creare un nuovo progetto

Negli esempi seguenti viene creato un nuovo progetto denominato `myproject` nell'istanza del servizio visione artificiale personalizzato. Questo servizio è impostato per impostazione predefinita sul dominio `General`:

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects?name=myproject" -H "Training-Key: $TRAININGKEY" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects?name=myproject" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey" }
$resp.Content
```

La risposta alla richiesta è simile al seguente documento JSON:

```json
{
  "id":"45d1b19b-69b6-4a22-8e7e-d1ca37504686",
  "name":"myproject",
  "description":"",
  "settings":{
    "domainId":"ee85a72c-405e-4adc-bb47-ffa8ca0c9f31",
    "useNegativeSet":true,
    "classificationType":"Multilabel",
    "detectionParameters":null
  },
  "created":"2018-08-10T17:39:02.5633333",
  "lastModified":"2018-08-10T17:39:02.5633333",
  "thumbnailUri":null
}
```

> [!TIP]
> La voce `id` nella risposta è l'ID del nuovo progetto. Questo verrà usato in altri esempi più avanti in questo documento.

Per altre informazioni sulla richiesta, vedere [CreateProject](https://go.microsoft.com/fwlink/?linkid=865446).

### <a name="specific-domains"></a>Domini specifici

Per creare un progetto per un dominio specifico, è possibile fornire l'__ID dominio__ come parametro facoltativo. Gli esempi seguenti illustrano come recuperare un elenco di domini disponibili:

```bash
curl -X GET "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/domains" -H "Training-Key: $TRAININGKEY" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'GET' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/domains" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey" }
$resp.Content
```

La risposta alla richiesta è simile al seguente documento JSON:

```json
[
    {
        "id": "ee85a74c-405e-4adc-bb47-ffa8ca0c9f31",
        "name": "General",
        "type": "Classification",
        "exportable": false,
        "enabled": true
    },
    {
        "id": "c151d5b5-dd07-472a-acc8-15d29dea8518",
        "name": "Food",
        "type": "Classification",
        "exportable": false,
        "enabled": true
    },
    {
        "id": "ca455789-012d-4b50-9fec-5bb63841c793",
        "name": "Landmarks",
        "type": "Classification",
        "exportable": false,
        "enabled": true
    },
    ...
]
```

Per altre informazioni su questa richiesta, vedere [GetDomains](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a827d).

L'esempio seguente illustra la creazione di un nuovo progetto che usa il dominio __Riferimenti__:

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects?name=myproject&domainId=ca455789-012d-4b50-9fec-5bb63841c793" -H "Training-Key: $TRAININGKEY" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects?name=myproject&domainId=ca455789-012d-4b50-9fec-5bb63841c793" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey" }
$resp.Content
```

## <a name="create-tags"></a>Creare tag

Per assegnare un tag alle immagini, è necessario usare un ID di tag. Nell'esempio seguente viene illustrato come creare un nuovo tag denominato `cat` e ottenere un ID di tag. Sostituire `{projectId}` con l'ID del progetto. Usare il parametro `name=` per specificare il nome del tag:

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/tags?name=cat" -H "Training-Key: $TRAININGKEY" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/tags?name=cat" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey" }
$resp.Content
```

La risposta alla richiesta è simile al seguente documento JSON: 

```json
{"id":"ed6f7ab6-5132-47ad-8649-3ec42ee62d43","name":"cat","description":null,"imageCount":0}
```

Salvare il valore `id`, che verrà usato durante l'assegnazione di tag alle immagini.

Per altre informazioni sulla richiesta, vedere [CreateTag](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a829d).

## <a name="add-images"></a>Aggiungere immagini

Gli esempi seguenti illustrano l'aggiunta di un file dall'URL. Sostituire `{projectId}` con l'ID del progetto. Sostituire `{tagId}` con l'ID del tag dell'immagine:

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/images/urls" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii '{"images": [{"url": "http://myimages/cat.jpg","tagIds": ["{tagId}"],"regions": [{"tagId": "{tagId}","left": 119.0,"top": 94.0,"width": 240.0,"height": 140.0}]}], "tagIds": ["{tagId}"]}'
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/images/urls" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" } `
    -Body '{"images": [{"url": "http://myimages/cat.jpg","tagIds": ["{tagId}"],"regions": [{"tagId": "{tagId}","left": 119.0,"top": 94.0,"width": 240.0,"height": 140.0}]}], "tagIds": ["{tagId}"]}'
$resp.Content
```

La risposta alla richiesta è simile al seguente documento JSON:

```json
{
    "isBatchSuccessful": true,
    "images": [
        {
            "sourceUrl": "http://myimages/cat.jpg",
            "status": "OK",
            "image": {
                "id": "081adaee-a76b-4d94-a70e-e4fd0935a28f",
                "created": "2018-08-13T13:24:22.0815638",
                "width": 640,
                "height": 480,
                "imageUri": "https://linktoimage",
                "thumbnailUri": "https://linktothumbnail",
                "tags": [
                    {
                        "tagId": "ed6f7ab6-5132-47ad-8649-3ec42ee62d43",
                        "tagName": null,
                        "created": "2018-08-13T13:24:22.104936"
                    }
                ],
                "regions": [
                    {
                        "regionId": "40f206a1-3f8a-4de7-a6c3-c7b4643117df",
                        "tagName": null,
                        "created": "2018-08-13T13:24:22.104936",
                        "tagId": "ed6f7ab6-5132-47ad-8649-3ec42ee62d43",
                        "left": 119,
                        "top": 94,
                        "width": 240,
                        "height": 140
                    }
                ]
            }
        }
    ]
}
```

Per altre informazioni sulla richiesta, vedere [CreateImagesFromUrls](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a8287).

## <a name="train-the-model"></a>Eseguire il training del modello

Gli esempi seguenti illustrano come eseguire il training del modello. Sostituire `{projectId}` con l'ID del progetto:

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/train" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/train" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" } 
$resp.Content
```

La risposta alla richiesta è simile al seguente documento JSON:

```json
{
    "id": "23de09d6-42a1-413e-839e-8db6ee6d3496",
    "name": "Iteration 1",
    "isDefault": false,
    "status": "Training",
    "created": "2018-08-10T17:39:02.5766667",
    "lastModified": "2018-08-16T17:15:07.5250661",
    "projectId": "45d1b19b-69b8-4b22-8e7e-d1ca37504686",
    "exportable": false,
    "domainId": null
}
```

Salvare il valore `id`, che verrà usato per testare ed esportare il modello.

Per altre informazioni, vedere [TrainProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a8294).

## <a name="test-the-model"></a>Testare un modello

Gli esempi seguenti illustrano come eseguire il test del modello. Sostituire `{projectId}` con l'ID del progetto. Sostituire `{iterationId}` con l'ID restituito dal training del modello. Sostituire `https://linktotestimage` con il percorso dell'immagine di test.

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/quicktest/url?iterationId={iterationId}" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii '{"url":"https://linktotestimage"}'
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/quicktest/url?iterationId={iterationId}" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" } `
    -Body '{"url":"https://linktotestimage"}'
$resp.Content
```

La risposta alla richiesta è simile al seguente documento JSON:

```json
{
    "id": "369b010b-2a92-4f48-a918-4c1a0af91888",
    "project": "45d1b19b-69b8-4b22-8e7e-d1ca37504686",
    "iteration": "23de09d6-42a1-413e-839e-8db6ee6d3496",
    "created": "2018-08-16T17:39:20.7944508Z",
    "predictions": [
        {
            "probability": 0.8390652,
            "tagId": "ed6f7ab6-5132-47ad-8649-3ec42ee62d43",
            "tagName": "cat"
        }
    ]
}
```

Per altre informazioni, vedere [QuickTestImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a828d).

## <a name="export-the-model"></a>Esportare il modello

L'esportazione di un modello è un processo in due passaggi. Prima di tutto è necessario specificare il formato di modello e quindi richiedere l'URL per il modello esportato.

### <a name="request-a-model-export"></a>Richiesta di esportazione di un modello

Gli esempi seguenti illustrano come esportare un modello `coreml`. Sostituire `{projectId}` con l'ID del progetto. Sostituire `{iterationId}` con l'ID restituito dal training del modello.

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/iterations/{iterationId}/export?platform=coreml" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii ''
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/iterations/{iterationId}/export?platform=coreml" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" }
$resp.Content
```

La risposta alla richiesta è simile al seguente documento JSON:

```json
{
    "platform": "CoreML",
    "status": "Exporting",
    "downloadUri": null,
    "flavor": null
}
```

Per altre informazioni, vedere [ExportIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a829b).

### <a name="download-the-exported-model"></a>Scaricare il modello esportato

Gli esempi seguenti illustrano come recuperare l'URL del modello esportato. Sostituire `{projectId}` con l'ID del progetto. Sostituire `{iterationId}` con l'ID restituito dal training del modello.

```bash
curl -X GET "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/iterations/{iterationId}/export" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii ''
```

```powershell
$resp = Invoke-WebRequest -Method 'GET' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/iterations/{iterationId}/export" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" }
$resp.Content
```

La risposta alla richiesta è simile al seguente documento JSON:

```json
[
    {
        "platform": "CoreML",
        "status": "Done",
        "downloadUri": "https://linktoexportedmodel",
        "flavor": null
    }
]
```

Per altre informazioni, vedere [GetExports](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a829a).
