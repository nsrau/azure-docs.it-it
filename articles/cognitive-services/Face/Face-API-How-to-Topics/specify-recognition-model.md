---
title: Come specificare un modello di riconoscimento-API Viso
titleSuffix: Azure Cognitive Services
description: In questo articolo viene illustrato come scegliere il modello di riconoscimento da usare con l'applicazione Azure API Viso.
services: cognitive-services
author: longli0
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: longl
ms.openlocfilehash: 5b84e078e3b674a539b61c07c4bb4370719e4799
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74771020"
---
# <a name="specify-a-face-recognition-model"></a>Specificare un modello di riconoscimento volto

Questa guida illustra come specificare un modello di riconoscimento viso per il rilevamento del viso, l'identificazione e la ricerca di somiglianza usando il API Viso di Azure.

Il API Viso usa i modelli di apprendimento automatico per eseguire operazioni sui visi umani nelle immagini. Continuiamo a migliorare l'accuratezza dei nostri modelli in base ai commenti e ai suggerimenti dei clienti e ai miglioramenti apportati alla ricerca e offriamo questi miglioramenti come aggiornamenti del modello. Gli sviluppatori hanno la possibilità di specificare la versione del modello di riconoscimento viso che si vuole usare. possono scegliere il modello più adatto al caso d'uso.

Se si è un nuovo utente, è consigliabile usare il modello più recente. Per informazioni su come specificarlo in operazioni viso diverse evitando conflitti tra i modelli, vedere. Se si è un utente avanzato e non si è certi se è necessario passare al modello più recente, passare alla sezione [valuta modelli diversi](#evaluate-different-models) per valutare il nuovo modello e confrontare i risultati con il set di dati corrente.

## <a name="prerequisites"></a>Prerequisiti

È necessario avere familiarità con i concetti di rilevamento e identificazione dei volti di intelligenza artificiale. In caso di assenza di questo, vedere le guide introduttive seguenti:

* [Come rilevare i visi in un'immagine](HowtoDetectFacesinImage.md)
* [Come identificare i visi in un'immagine](HowtoIdentifyFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Rileva visi con il modello specificato

Il rilevamento viso identifica i punti di riferimento visivi dei visi umani e trova le posizioni dei box. Estrae anche le funzionalità del viso e le archivia per l'uso nell'identificazione. Tutte queste informazioni formano la rappresentazione di una faccia.

Il modello di riconoscimento viene utilizzato quando vengono estratte le funzionalità del viso, pertanto è possibile specificare una versione del modello durante l'esecuzione dell'operazione di rilevamento.

Quando si usa l'API di [Rilevamento viso] , assegnare la versione del modello con il parametro `recognitionModel`. I valori disponibili sono:

* `recognition_01`
* `recognition_02`

Facoltativamente, è possibile specificare il parametro _returnRecognitionModel_ (impostazione predefinita **false**) per indicare se _recognitionModel_ deve essere restituito in risposta. Quindi, un URL di richiesta per l'API REST di [Rilevamento viso] sarà simile al seguente:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel]&subscription-key=<Subscription key>`

Se si usa la libreria client, è possibile assegnare il valore per `recognitionModel` passando una stringa che rappresenta la versione.
Se la si lascia non assegnata, verrà usata la versione predefinita del modello (_recognition_01_). Vedere l'esempio di codice seguente per la libreria client .NET.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, recognitionModel: "recognition_02", returnRecognitionModel: true);
```

## <a name="identify-faces-with-specified-model"></a>Identificare i visi con il modello specificato

Il API Viso è in grado di estrarre i dati facciali da un'immagine e di associarli a un oggetto **Person** (tramite la chiamata all'API [Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) , ad esempio) e più oggetti **Person** possono essere archiviati insieme in un **gruppo**. Quindi, è possibile confrontare una nuova faccia con una **gruppo** (con la chiamata di [Face - Identify] ) e la persona corrispondente all'interno del gruppo.

Un **gruppo** deve avere un solo modello di riconoscimento univoco per tutte le **persone**e può essere specificato usando il parametro `recognitionModel` quando si crea il gruppo ([PersonGroup - Create] o [LargePersonGroup - Create]). Se non si specifica questo parametro, viene utilizzato il modello di `recognition_01` originale. Un gruppo userà sempre il modello di riconoscimento con cui è stato creato e i nuovi visi verranno associati a questo modello quando vengono aggiunti al modello; Questa operazione non può essere modificata dopo la creazione di un gruppo. Per visualizzare il modello con cui è configurata una **gruppo** , usare l'API [gruppo-Get] con il parametro _returnRecognitionModel_ impostato su **true**.

Vedere l'esempio di codice seguente per la libreria client .NET.

```csharp
// Create an empty PersonGroup with "recognition_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");
```

In questo codice viene creato un **gruppo** con ID `mypersongroupid` e viene configurato per usare il modello di _recognition_02_ per estrarre le funzionalità del viso.

Al contrario, è necessario specificare il modello da usare quando si rilevano i visi da confrontare con questo **gruppo** (tramite l'API di [Rilevamento viso] ). Il modello usato deve essere sempre coerente con la configurazione di **gruppo**; in caso contrario, l'operazione avrà esito negativo a causa di modelli incompatibili.

Non sono state apportate modifiche all'API per l' [Face - Identify] . è sufficiente specificare la versione del modello in rilevamento.

## <a name="find-similar-faces-with-specified-model"></a>Trova visi simili con il modello specificato

È anche possibile specificare un modello di riconoscimento per la ricerca di somiglianza. È possibile assegnare la versione del modello con `recognitionModel` durante la creazione dell'elenco di visi con l'API [Facet-crea] o [LargeFaceList-crea]. Se non si specifica questo parametro, viene utilizzato il modello di `recognition_01` originale. Un elenco di visi utilizzerà sempre il modello di riconoscimento con cui è stato creato e i nuovi visi verranno associati a questo modello quando vengono aggiunti al modello; Questa operazione non può essere modificata dopo la creazione. Per visualizzare il modello con cui è stato configurato un elenco di visi, usare l'API [facet-Get] con il parametro _returnRecognitionModel_ impostato su **true**.

Vedere l'esempio di codice seguente per la libreria client .NET.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");
```

Questo codice crea un elenco di visi denominato `My face collection`, usando il modello di _recognition_02_ per l'estrazione delle funzionalità. Quando si esegue la ricerca di visi simili a un nuovo facet rilevato, quest'aspetto deve essere stato rilevato ([rilevamento viso]) usando il modello di _recognition_02_ . Come nella sezione precedente, il modello deve essere coerente.

Non sono state apportate modifiche all'API per la [Faccia-trova simile] . è possibile specificare solo la versione del modello in rilevamento.

## <a name="verify-faces-with-specified-model"></a>Verifica visi con il modello specificato

L'API di [Faccia-verifica] controlla se due visi appartengono alla stessa persona. Non sono state apportate modifiche all'API Verify per quanto riguarda i modelli di riconoscimento, ma è possibile confrontare solo i visi rilevati con lo stesso modello. Per questo motivo, è necessario che i due visi siano stati rilevati usando `recognition_01` o `recognition_02`.

## <a name="evaluate-different-models"></a>Valutare modelli diversi

Per confrontare le prestazioni del _recognition_01_ e _recognition_02_ i modelli sui dati, è necessario:

1. Creare due **gruppo**con _recognition_01_ e _recognition_02_ rispettivamente.
1. Usare i dati dell'immagine per rilevare i visi e registrarli in **Person**s per questi due **gruppo**e attivare il processo di training con l'API [PersonGroup - Train] .
1. Eseguire il test con [Face - Identify] in **gruppo**s e confrontare i risultati.

Se in genere si specifica una soglia di confidenza, ovvero un valore compreso tra zero e uno che determina il livello di attendibilità del modello per l'identificazione di una faccia, potrebbe essere necessario utilizzare soglie diverse per i diversi modelli. Una soglia per un modello non deve essere condivisa con un'altra e non produrrà necessariamente gli stessi risultati.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come specificare il modello di riconoscimento da usare con diverse API del servizio viso. Seguire quindi una guida introduttiva per iniziare a usare il rilevamento delle facce.

* [.NET SDK facet](../Quickstarts/csharp-sdk.md)
* [Python SDK per visi](../Quickstarts/python-sdk.md)

[Rilevamento viso]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Faccia-trova simile]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Face - Identify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Faccia-verifica]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[Gruppo-Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - Train]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[Facet-crea]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[Facet-Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[LargeFaceList-crea]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc
