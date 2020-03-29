---
title: Come specificare un modello di riconoscimento - Faccia
titleSuffix: Azure Cognitive Services
description: Questo articolo illustra come scegliere il modello di riconoscimento da usare con l'applicazione Azure Face.This article show you how to choose which recognition model to use with your Azure Face application.
services: cognitive-services
author: longli0
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: longl
ms.openlocfilehash: da9ad5576d146c007e45124668875e9681860ce6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "76938832"
---
# <a name="specify-a-face-recognition-model"></a>Specificare un modello di riconoscimento volto

Questa guida illustra come specificare un modello di riconoscimento facciale per il rilevamento volto, l'identificazione e la ricerca di somiglianze usando il servizio Azure Face.This guide shows you how to specify a face recognition model for face detection, identification and similarity search using the Azure Face service.

Il servizio Face utilizza modelli di apprendimento automatico per eseguire operazioni sui volti umani nelle immagini. Continuiamo a migliorare l'accuratezza dei nostri modelli in base al feedback dei clienti e ai progressi nella ricerca, e forniamo questi miglioramenti come aggiornamenti del modello. Gli sviluppatori hanno la possibilità di specificare quale versione del modello di riconoscimento facciale desiderano utilizzare; possono scegliere il modello che meglio si adatta al loro caso d'uso.

Se sei un nuovo utente, ti consigliamo di utilizzare il modello più recente. Continuate a leggere per scoprire come specificarlo in diverse operazioni di Face evitando conflitti tra modelli. Se si è un utente avanzato e non si è certi di passare al modello più recente, passare alla sezione [Valutare modelli diversi](#evaluate-different-models) per valutare il nuovo modello e confrontare i risultati utilizzando il set di dati corrente.

## <a name="prerequisites"></a>Prerequisiti

È necessario avere familiarità con i concetti di aI face detection e identification. In caso contrario, consulta prima queste guide pratiche:

* [Come rilevare i volti in un'immagine](HowtoDetectFacesinImage.md)
* [Come identificare i volti in un'immagine](HowtoIdentifyFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Rilevare le facce con il modello specificato

Il rilevamento volti identifica i punti di riferimento visivi dei volti umani e trova le loro posizioni di riquadro di delimitazione. Estrae anche le caratteristiche del viso e le memorizza per l'uso in identificazione. Tutte queste informazioni costituisce la rappresentazione di una faccia.

Il modello di riconoscimento viene utilizzato quando vengono estratte le feature faccia, pertanto è possibile specificare una versione del modello quando si esegue l'operazione di rilevamento.

Quando si utilizza l'API Face - `recognitionModel` [Detect,] assegnare la versione del modello con il parametro . I valori disponibili sono:

* `recognition_01`
* `recognition_02`

Facoltativamente, è possibile specificare il parametro _returnRecognitionModel_ (default **false**) per indicare se _recognitionModel_ deve essere restituito in risposta. Pertanto, un URL di richiesta per l'API [REST Face - Detect] sarà simile al seguente:So, a request URL for the Face - Detect REST API will look like this:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel]&subscription-key=<Subscription key>`

Se si utilizza la libreria client, è `recognitionModel` possibile assegnare il valore per passando una stringa che rappresenta la versione.
Se lo si lascia non assegnato, verrà utilizzata la versione predefinita del modello (_recognition_01_). Vedere l'esempio di codice seguente per la libreria client .NET.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, recognitionModel: "recognition_02", returnRecognitionModel: true);
```

## <a name="identify-faces-with-specified-model"></a>Identificare le facce con il modello specificato

Il servizio Face può estrarre i dati del volto da un'immagine e associarli a un oggetto **Person** (tramite la chiamata all'API [Add face,](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) ad esempio) e più oggetti **Person** possono essere archiviati insieme in un **PersonGroup**. Quindi, è possibile confrontare un nuovo volto con un **PersonGroup** (con la chiamata [Face - Identify)] e la persona corrispondente all'interno di tale gruppo può essere identificata.

Un **PersonGroup** deve avere un modello di riconoscimento univoco per tutte `recognitionModel` le **persone**e può specificarlo utilizzando il parametro quando si crea il gruppo ([PersonGroup - Create] o [LargePersonGroup - Create]). Se non si specifica questo `recognition_01` parametro, viene utilizzato il modello originale. Un gruppo utilizzerà sempre il modello di riconoscimento con cui è stato creato e le nuove facce verranno associate a questo modello quando vengono aggiunte ad esso; non può essere modificato dopo la creazione di un gruppo. Per visualizzare il modello con cui è configurato un **PersonGroup,** utilizzare l'API [PersonGroup - Get] con il parametro _returnRecognitionModel_ impostato su **true**.

Vedere l'esempio di codice seguente per la libreria client .NET.

```csharp
// Create an empty PersonGroup with "recognition_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");
```

In questo codice viene creato `mypersongroupid` un **PersonGroup** con ID ed è impostato per utilizzare il modello _recognition_02_ per estrarre le feature del volto.

Di conseguenza, è necessario specificare quale modello utilizzare per il rilevamento di volti da confrontare con questo **PersonGroup** (tramite il [Face - Detect] API). Il modello utilizzato deve essere sempre coerente con la configurazione di **PersonGroup;** in caso contrario, l'operazione avrà esito negativo a causa di modelli incompatibili.

Non vi è alcun cambiamento nell'API [Face - Identify;] è sufficiente specificare la versione del modello nel rilevamento.

## <a name="find-similar-faces-with-specified-model"></a>Trova facce simili con il modello specificato

È inoltre possibile specificare un modello di riconoscimento per la ricerca di somiglianze. È possibile assegnare `recognitionModel` la versione del modello con quando si crea l'elenco di facce con [FaceList - Create] API o [LargeFaceList - Create]. Se non si specifica questo `recognition_01` parametro, viene utilizzato il modello originale. Un elenco di facce utilizzerà sempre il modello di riconoscimento con cui è stato creato e le nuove facce verranno associate a questo modello quando vengono aggiunte ad esso; questo non può essere modificato dopo la creazione. Per visualizzare il modello con cui è configurato un elenco di volti, utilizzare l'API [FaceList - Get] con il parametro _returnRecognitionModel_ impostato su **true**.

Vedere l'esempio di codice seguente per la libreria client .NET.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");
```

Questo codice crea un `My face collection`elenco di volti denominato , utilizzando il modello _recognition_02_ per l'estrazione delle funzionalità. Quando si cerca in questo elenco di facce facce simili a un nuovo volto rilevato, tale faccia deve essere stata rilevata ([Face - Detect]) utilizzando il modello _recognition_02._ Come nella sezione precedente, il modello deve essere coerente.

Non vi è alcun cambiamento nel Viso - Trova API [simile;] specificare solo la versione del modello nel rilevamento.

## <a name="verify-faces-with-specified-model"></a>Verificare le facce con il modello specificato

L'API [Faccia - Verifica] controlla se due facce appartengono alla stessa persona. Non viene apportata alcuna modifica nell'API di verifica per quanto riguarda i modelli di riconoscimento, ma è possibile confrontare solo i volti rilevati con lo stesso modello. Quindi, le due facce dovranno entrambi `recognition_01` `recognition_02`essere state rilevate utilizzando o .

## <a name="evaluate-different-models"></a>Valutare diversi modelli

Se desideri confrontare le prestazioni dei modelli _recognition_01_ e _recognition_02_ sui tuoi dati, dovrai:

1. Creare due **PersonGroup**con _recognition_01_ e _recognition_02_ rispettivamente.
1. Usare i dati immagine per rilevare i volti e registrarli in **Person**s per questi due **PersonGroup**e attivare il processo di training con [PersonGroup - Train] API.
1. Test con [faccia - Identificare] su entrambi Gli oggetti **PersonGroup**e confrontare i risultati.

Se normalmente si specifica una soglia di confidenza (un valore compreso tra zero e uno che determina la probabilità che il modello sia sicuro di identificare una faccia), potrebbe essere necessario utilizzare soglie diverse per modelli diversi. Una soglia per un modello non deve essere condivisa con un altro e non produrrà necessariamente gli stessi risultati.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come specificare il modello di riconoscimento da utilizzare con diverse API del servizio Face. Seguire quindi una guida introduttiva per iniziare a usare il rilevamento volti.

* [Face .NET SDK](../Quickstarts/csharp-sdk.md)
* [Face Python SDK](../Quickstarts/python-sdk.md)
* [Face Go SDK](../Quickstarts/go-sdk.md)

[Viso - Rilevamento]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Faccia - Trova simile]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Viso - Identificazione]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Faccia - Verifica]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - Train]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList - Crea]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[LargeFaceList - Creazione]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc
