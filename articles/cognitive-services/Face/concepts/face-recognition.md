---
title: Concetti relativi al riconoscimento del viso
titleSuffix: Azure Cognitive Services
description: In questo articolo vengono illustrati i concetti delle operazioni Verifica, Trova simile, Raggruppa e Identifica riconoscimento facciale e le strutture di dati sottostanti.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pafarley
ms.openlocfilehash: 164e5a8c107f445b376d26f9be7db92a7983b0d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73743070"
---
# <a name="face-recognition-concepts"></a>Concetti relativi al riconoscimento del viso

In questo articolo vengono illustrati i concetti delle operazioni Verifica, Trova simile, Raggruppa e Identifica riconoscimento facciale e le strutture di dati sottostanti. In generale, il riconoscimento descrive il lavoro di confronto di due facce diverse per determinare se sono simili o appartengono alla stessa persona.

## <a name="recognition-related-data-structures"></a>Strutture di dati relativi al riconoscimento

Le operazioni di riconoscimento utilizzano principalmente le seguenti strutture di dati. Questi oggetti vengono archiviati nel cloud e possono essere referenziati dalle relative stringhe ID. Le stringhe ID sono sempre univoche all'interno di una sottoscrizione. I campi nome possono essere duplicati.

|Nome|Descrizione|
|:--|:--|
|DetectedFace| Questa rappresentazione singola faccia viene recuperata dall'operazione di [rilevamento faccia.](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md) Il relativo ID scade 24 ore dopo la creazione.|
|PersistedFace| Quando detectedFace gli oggetti vengono aggiunti a un gruppo, ad esempio FaceList o Person, diventano oggetti PersistedFace. Possono essere [recuperati](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c) in qualsiasi momento e non scadono.|
|[FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b) o [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)| Questa struttura di dati è un elenco assortito di PersistedFace oggetti. Un FaceList ha un ID univoco, una stringa del nome e, facoltativamente, una stringa di dati utente.|
|[Persona](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)| Questa struttura di dati è un elenco di persistedFace oggetti che appartengono alla stessa persona. Ha un ID univoco, una stringa del nome e, facoltativamente, una stringa di dati utente.|
|[PersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) o [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)| Questa struttura di dati è un elenco assortito di oggetti Person. Ha un ID univoco, una stringa del nome e, facoltativamente, una stringa di dati utente. Un PersonGroup deve essere [addestrato](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) prima di poter essere utilizzato nelle operazioni di riconoscimento.|

## <a name="recognition-operations"></a>Operazioni di riconoscimento

In questa sezione viene descritto in dettaglio come le quattro operazioni di riconoscimento utilizzano le strutture di dati descritte in precedenza. Per una descrizione generale di ogni operazione di riconoscimento, vedere [Panoramica](../Overview.md).

### <a name="verify"></a>Verifica

L'operazione [Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) accetta un ID volto da DetectedFace o PersistedFace e da un altro ID volto o da un oggetto Person e determina se appartengono alla stessa persona. Se si passa un oggetto Person, è possibile facoltativamente passare un PersonGroup a cui tale persona appartiene per migliorare le prestazioni.

### <a name="find-similar"></a>Ricerca di simili

L'operazione [Trova simile](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) accetta un ID volto da DetectedFace o PersistedFace e da FaceList o da una matrice di altri ID volto. Con un FaceList, restituisce un FaceList più piccolo di facce che sono simili alla faccia specificata. Con una matrice di ID faccia, restituisce in modo simile una matrice più piccola.

### <a name="group"></a>Gruppo

L'operazione [Group](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) accetta una matrice di ID faccia assortiti da DetectedFace o PersistedFace e restituisce gli stessi ID raggruppati in diversi array più piccoli. Ogni matrice "groups" contiene ID facciati che appaiono simili. Una singola matrice "messyGroup" contiene ID di faccia per i quali non sono state trovate somiglianze.

### <a name="identify"></a>Identify

L'operazione [Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) accetta uno o più ID faccia da DetectedFace o PersistedFace e un PersonGroup e restituisce un elenco di oggetti Person a cui ogni volto potrebbe appartenere. Gli oggetti Person restituiti vengono incapsulati come oggetti Candidate, che hanno un valore di attendibilità della stima.

## <a name="input-data"></a>Dati di input

Utilizzare i suggerimenti seguenti per assicurarsi che le immagini di input forniscano risultati di riconoscimento più accurati:

* I formati di immagine di input supportati sono JPEG, PNG, GIF (il primo fotogramma), BMP.
* Le dimensioni del file di immagine non devono superare i 4 MB.
* Quando si creano oggetti Persona, utilizzare foto che presentano diversi tipi di angoli e illuminazione.
* Alcuni volti potrebbero non essere riconosciuti a causa di problemi tecnici, come:
  * Immagini con illuminazione estrema, ad esempio retroilluminazione grave.
  * Ostruzioni che bloccano uno o entrambi gli occhi.
  * Differenze nel tipo di capelli o capelli facciali.
  * Cambiamenti nell'aspetto facciale a causa dell'età.
  * Espressioni facciali estreme.

## <a name="next-steps"></a>Passaggi successivi

Ora che si ha familiarità con i concetti di riconoscimento facciale, imparare a scrivere uno script che identifica i volti rispetto a un PersonGroup addestrato.

* [Identificare i visi nelle immagini](../Face-API-How-to-Topics/HowtoIdentifyFacesinImage.md)