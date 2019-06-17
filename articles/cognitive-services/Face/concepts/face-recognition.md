---
title: Concetti di riconoscimento volto
titleSuffix: Azure Cognitive Services
description: Informazioni su concetti sul rilevamento viso.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pafarley
ms.openlocfilehash: fa38c492530cb8938e49bc15e13fdd39ed5b6f1c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65890888"
---
# <a name="face-recognition-concepts"></a>Concetti di riconoscimento volto

Questo articolo illustra i concetti delle operazioni di riconoscimento volto verificare, trovare simile, gruppo e identificare e strutture di dati sottostanti. Riconoscimento su vasta scala, descrive le operazioni di confronto di due visi diversi per determinare se sono simili o se appartengono alla stessa persona.

## <a name="recognition-related-data-structures"></a>Strutture di dati correlati al riconoscimento

Le operazioni di riconoscimento utilizzano principalmente le strutture di dati seguenti. Questi oggetti vengono archiviati nel cloud e le relative stringhe ID possono fare riferimento. Stringhe ID sono sempre univoche in una sottoscrizione. I campi nome possono essere duplicati.

|NOME|Descrizione|
|:--|:--|
|DetectedFace| Questa rappresentazione singolo carattere tipografico viene recuperata per la [rilevamento viso](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md) operazione. Il relativo ID scade 24 ore dopo averlo creato.|
|PersistedFace| Quando gli oggetti DetectedFace vengono aggiunti a un gruppo, ad esempio FaceList o di persona, diventano PersistedFace oggetti. Possono essere [recuperati](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c) in corrispondenza di qualsiasi ora e non scadono.|
|[FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b) o [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)| Questa struttura di dati è un elenco di oggetti PersistedFace vengono formattati diverse. Un FaceList ha un ID univoco, una stringa del nome e, facoltativamente, una stringa di dati utente.|
|[Person](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)| Questa struttura di dati è un elenco di oggetti PersistedFace appartenenti alla stessa persona. Dispone di un ID univoco, una stringa del nome e, facoltativamente, una stringa di dati utente.|
|[Gruppo di persone](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) o [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)| Questa struttura di dati è un elenco di oggetti Person vengono formattati diverse. Dispone di un ID univoco, una stringa del nome e, facoltativamente, una stringa di dati utente. Deve essere un gruppo di persone [sottoposto a training](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) prima di poter essere utilizzato nelle operazioni di riconoscimento.|

## <a name="recognition-operations"></a>Operazioni di riconoscimento

In questa sezione descrive in dettaglio come le operazioni di riconoscimento quattro usano strutture di dati descritte in precedenza. Per una vasta descrizione di ogni operazione di riconoscimento, vedere [Panoramica](../Overview.md).

### <a name="verify"></a>Verificare

Il [Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) operazione accetta un ID volto da DetectedFace o PersistedFace e un altro ID viso o un oggetto Person e determina se appartengono alla stessa persona. Se passa un oggetto Person, è possibile passare facoltativamente in un gruppo di persone a cui appartiene questa persona per migliorare le prestazioni.

### <a name="find-similar"></a>Ricerca di simili

Il [trovare simile](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) operazione accetta un ID volto da DetectedFace o PersistedFace e un FaceList o una matrice di altri ID dei volti. Con un FaceList, viene restituito un FaceList più piccoli di visi simili per il viso specificato. Con una matrice di ID volto, in modo analogo restituisce una matrice di più piccola.

### <a name="group"></a>Group

Il [gruppo](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) operazione accetta una matrice di ID volto vengono formattati diverse da DetectedFace o PersistedFace e restituisce gli stessi ID raggruppati in numerose matrici più piccole. Ogni matrice "groups" contiene face ID apparentemente simili. Una matrice di singolo "messyGroup" contiene face ID per cui sono state trovate non analogie.

### <a name="identify"></a>Identificare

Il [Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) operazione accetta uno o più face ID da DetectedFace o PersistedFace e un gruppo di persone e restituisce un elenco di oggetti Person appartenenti a ogni faccia. Restituiti come oggetti Candidate, che hanno un valore di probabilità di stima sono inclusi gli oggetti utente.

## <a name="input-data"></a>Dati di input

Usare i suggerimenti seguenti per assicurarsi che le immagini di input forniscono i risultati di riconoscimento più accurati:

* I formati di immagine di input supportati sono JPEG, PNG, GIF (il primo fotogramma), BMP.
* Dimensioni del file immagine devono essere non superiore a 4 MB.
* Quando si creano oggetti Person, usare foto che forniscono tipi diversi di angoli e illuminazione.
* Alcuni volti potrebbero non essere riconosciute a causa di problemi tecnici, ad esempio:
  * Le immagini con estrema illuminazione, ad esempio, grave retroilluminazione.
  * Ostacoli che bloccano gli occhi di uno o entrambi.
  * Differenze nelle sorriso facciale o tipo di selettore di precisione.
  * Modifiche nell'aspetto del volto a causa di età.
  * Espressioni del viso estreme.

## <a name="next-steps"></a>Passaggi successivi

Ora che si ha familiarità con i concetti di riconoscimento di volti, informazioni su come scrivere uno script che identifica i visi in un gruppo di persone con training.

* [Identificare visi nelle immagini](../Face-API-How-to-Topics/HowtoIdentifyFacesinImage.md)