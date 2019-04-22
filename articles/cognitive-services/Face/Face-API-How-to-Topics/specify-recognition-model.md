---
title: Come specificare un modello di riconoscimento - API viso
titleSuffix: Azure Cognitive Services
description: Questo articolo illustrerà come scegliere il modello di riconoscimento da utilizzare con l'applicazione API viso di Azure.
services: cognitive-services
author: longl
manager: nitinme
ms.service: cognitive-services
ms.component: face-api
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: longl
ms.openlocfilehash: 33348e637143b923719425b9674f99a475d848d9
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58806661"
---
# <a name="specify-a-face-recognition-model"></a>Specificare un modello di riconoscimento volto

Questa guida illustra come specificare un modello di riconoscimento volto per rilevamento del viso, identificazione e la ricerca di somiglianza mediante l'API viso di Azure.

L'API viso Usa modelli di machine learning per eseguire operazioni sui visi umani nelle immagini. Continuiamo a migliorare la precisione dei nostri modelli basati su commenti e suggerimenti dei clienti e i progressi nelle ricerche e offrire tali funzionalità e miglioramenti come modellare gli aggiornamenti. Gli sviluppatori hanno la possibilità di specificare quale versione del modello di riconoscimento volto desiderano utilizzare; è possibile scegliere il modello che meglio si adatta rispettivi casi d'uso.

Se sei un nuovo utente, è consigliabile che usare il modello più recente. Continuare a leggere per informazioni su come specificare in diverse operazioni di visi e si evitano conflitti di modello. Se si è un utente avanzato e non è certi se deve passare al modello più recente, passare al [valutare modelli diversi](#evaluate-different-models) sezione per valutare il nuovo modello e confrontare i risultati usando il set di dati corrente.

## <a name="prerequisites"></a>Prerequisiti

È necessario avere familiarità con i concetti di rilevamento dei visi per intelligenza artificiale e l'identificazione. Se non si, vedere queste guide alle procedure prima di tutto:

* [Come rilevare i visi in un'immagine](HowtoDetectFacesinImage.md)
* [Come identificare i visi in un'immagine](HowtoIdentifyFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Rileva i visi con il modello specificato

Rilevamento viso identifica i punti di riferimento visual di volti umani e rileva le relative posizioni di riquadro. Inoltre, estrae caratteristiche del viso e li archivia per l'utilizzo nell'identificazione. Tutte queste informazioni costituisce la rappresentazione di una faccia.

Il modello di riconoscimento viene utilizzato quando vengono estratte le caratteristiche del viso, pertanto è possibile specificare una versione del modello quando si esegue l'operazione di rilevamento.

Quando si usa la [Viso: rilevare] API, assegnare la versione del modello con il `recognitionModel` parametro. I valori disponibili sono:

* `recognition_01`
* `recognition_02`

Facoltativamente, è possibile specificare il _returnRecognitionModel_ parametro (impostazione predefinita **false**) per indicare se _recognitionModel_ devono essere restituiti nella risposta. Pertanto, un URL della richiesta per il [Viso: rilevare] API REST avrà un aspetto simile al seguente:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel]
&subscription-key=<Subscription key>`

Se si usa la libreria client, è possibile assegnare il valore per `recognitionModel` passando una stringa che rappresenta la versione.
Se si lascia non assegnati, la versione del modello predefinito (_recognition_01_) verrà usato. Vedere l'esempio di codice seguente per la libreria client .NET.

```csharp
string imageUrl = "http://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceServiceClient.Face.DetectWithUrlAsync(imageUrl, true, true, recognitionModel: "recognition_02", returnRecognitionModel: true);
```

## <a name="identify-faces-with-specified-model"></a>Identifica i visi con il modello specificato

L'API viso può estrarre i dati di volti da un'immagine e associalo con un **persona** oggetto (tramite il [aggiungere viso](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) chiamare API, ad esempio) e più **persona** gli oggetti possono essere archiviati insieme in una **gruppo di persone**. Quindi, un nuovo viso può essere confrontato con un **gruppo di persone** (con il [Face - Identify] chiamare), e l'utente corrisponda all'interno di tale gruppo può essere identificato.

A **gruppo di persone** deve avere un modello di riconoscimento univoco per tutte la **persona**s ed è possibile specificare questa opzione utilizzando il `recognitionModel` parametro quando si crea il gruppo ([PersonGroup - Create] oppure [LargePersonGroup - Create]). Se non si specifica questo parametro, originale `recognition_01` modello viene usato. Un gruppo userà sempre il modello di riconoscimento che è stato creato e nuove facce diventeranno associate con questo modello quando vengono aggiunti a esso. non può essere modificato dopo la creazione di un gruppo. Per visualizzare il modello di un **gruppo di persone** è configurato con, utilizzare il [gruppo di persone - Get] API con il _returnRecognitionModel_ parametro impostato come **true**.

Vedere l'esempio di codice seguente per la libreria client .NET.

```csharp
// Create an empty PersonGroup with "recognition_02" model
string personGroupId = "mypersongroupid";
await faceServiceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");
```

In questo codice, un **gruppo di persone** con ID `mypersongroupid` viene creato, ed è configurato per usare i _recognition_02_ modello per l'estrazione delle funzionalità di visi.

Di conseguenza, è necessario specificare quale modello da utilizzare per rilevare i volti da confrontare con questo **gruppo di persone** (tramite il [Viso: rilevare] API). Il modello è utilizzare deve essere sempre coerenza con il **gruppo di persone**della configurazione; in caso contrario, l'operazione avrà esito negativo a causa di modelli non compatibili.

Non viene modificata nel [Face - Identify] API; è sufficiente specificare la versione del modello di rilevamento.

## <a name="find-similar-faces-with-specified-model"></a>Trovare visi simili con il modello specificato

È anche possibile specificare un modello di riconoscimento per la ricerca di somiglianza. È possibile assegnare la versione del modello con `recognitionModel` quando si crea l'elenco di visi con [FaceList - creare] API o [LargeFaceList - creare]. Se non si specifica questo parametro, originale `recognition_01` modello viene usato. Un elenco di visi userà sempre il modello di riconoscimento che è stato creato e nuove facce diventeranno associate con questo modello quando vengono aggiunti a esso. non può essere modificato dopo la creazione. Per visualizzare il modello è configurato con un elenco di visi, usare il [FaceList - Get] API con il _returnRecognitionModel_ parametro impostato come **true**.

Vedere l'esempio di codice seguente per la libreria client .NET.

```csharp
await faceServiceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");
```

Questo codice crea un elenco di visi chiamato `My face collection`, usando il _recognition_02_ modello per l'estrazione delle funzionalità. Quando si cerca questo elenco di visi per i visi simili a un nuovo viso rilevato, che devono affrontare debba sono stati rilevati ([Viso: rilevare]) tramite il _recognition_02_ modello. Come nella sezione precedente, il modello deve essere coerente.

Non viene modificata nel [Volto - ricerca simili] API; è specificare solo la versione del modello di rilevamento.

## <a name="verify-faces-with-specified-model"></a>Verifica i visi con modello specificato

Il [Viso: verificare] API verifica se due visi appartengano alla stessa persona. Non sussiste alcuna modifica nell'API verificare per quanto riguarda i modelli di riconoscimento, ma è possibile confrontare solo i volti rilevati con lo stesso modello. Pertanto, i due visi entrambi dovrà sono stati rilevati mediante `recognition_01` o `recognition_02`.

## <a name="evaluate-different-models"></a>Valutare i modelli diversi

Se si desidera confrontare le prestazioni dei _recognition_01_ e _recognition_02_ modelli sui dati, è necessario:

1. Creare due **gruppo di persone**s con _recognition_01_ e _recognition_02_ rispettivamente.
1. Usare i dati di immagine per rilevare i visi e registrarli al **Person**s per questi due **gruppo di persone**s e i corsi di formazione di trigger di processo con [PersonGroup - Train] API.
1. Eseguire il test con [Face - Identify] sia **gruppo di persone**s e confrontare i risultati.

Se è in genere specificare una soglia di probabilità (un valore compreso tra zero e uno che determina come sicuri il modello deve essere per identificare un viso), occorre utilizzare soglie diverse per modelli differenti. Una soglia per un modello non deve essere condiviso in un altro e non produrrà necessariamente gli stessi risultati.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato descritto come specificare il modello di riconoscimento da utilizzare con le API del servizio faccia diversi. Successivamente, eseguire un avvio rapido per iniziare a usare il rilevamento viso.

* [Rileva i visi in un'immagine](../quickstarts/csharp-detect-sdk.md)

[Viso: rilevare]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Volto - ricerca simili]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Face - Identify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Viso: verificare]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[Gruppo di persone - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - Train]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList - creare]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[LargeFaceList - creare]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc
