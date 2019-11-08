---
title: Concetti relativi al riconoscimento viso
titleSuffix: Azure Cognitive Services
description: In questo articolo vengono illustrati i concetti relativi alle operazioni Verify, find analoghe, Group e identificate per il riconoscimento viso e le strutture di dati sottostanti.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pafarley
ms.openlocfilehash: 164e5a8c107f445b376d26f9be7db92a7983b0d3
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73743070"
---
# <a name="face-recognition-concepts"></a>Concetti relativi al riconoscimento viso

In questo articolo vengono illustrati i concetti relativi alle operazioni Verify, find analoghe, Group e identificate per il riconoscimento viso e le strutture di dati sottostanti. In generale, il riconoscimento descrive il lavoro di confronto tra due visi diversi per determinare se sono simili o appartengono alla stessa persona.

## <a name="recognition-related-data-structures"></a>Strutture di dati correlate al riconoscimento

Le operazioni di riconoscimento utilizzano principalmente le strutture di dati seguenti. Questi oggetti vengono archiviati nel cloud ed è possibile farvi riferimento dalle relative stringhe ID. Le stringhe ID sono sempre univoche all'interno di una sottoscrizione. I campi nome possono essere duplicati.

|Name|Descrizione|
|:--|:--|
|DetectedFace| Questa rappresentazione con una sola faccia viene recuperata dall'operazione di [rilevamento della faccia](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md) . Il relativo ID scade 24 ore dopo la creazione.|
|PersistedFace| Quando gli oggetti DetectedFace vengono aggiunti a un gruppo, ad esempio facet o person, diventano oggetti PersistedFace. Possono essere [recuperati](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c) in qualsiasi momento e non scadono.|
|[Facet](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b) o [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)| Questa struttura di dati è un elenco assortito di oggetti PersistedFace. Un oggetto facet ha un ID univoco, una stringa del nome e, facoltativamente, una stringa di dati utente.|
|[Person](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)| Questa struttura di dati è un elenco di oggetti PersistedFace che appartengono alla stessa persona. Ha un ID univoco, una stringa del nome e, facoltativamente, una stringa di dati utente.|
|[Gruppo](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) o [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)| Questa struttura di dati è un elenco assortito di oggetti Person. Ha un ID univoco, una stringa del nome e, facoltativamente, una stringa di dati utente. Per poter essere utilizzato nelle operazioni di riconoscimento, è necessario eseguire il [Training](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) di un gruppo.|

## <a name="recognition-operations"></a>Operazioni di riconoscimento

In questa sezione viene illustrato in dettaglio come le quattro operazioni di riconoscimento utilizzano le strutture di dati descritte in precedenza. Per una descrizione generale di ogni operazione di riconoscimento, vedere [Panoramica](../Overview.md).

### <a name="verify"></a>Verificare

L'operazione [Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) accetta un ID faccia da DetectedFace o PersistedFace e da un altro ID facciale o un oggetto Person e determina se appartengono alla stessa persona. Se si passa un oggetto Person, è possibile passare facoltativamente un gruppo a cui appartiene l'utente per migliorare le prestazioni.

### <a name="find-similar"></a>Ricerca di simili

L'operazione [Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) accetta un ID faccia da DetectedFace o PersistedFace e da un facet o una matrice di altri ID Face. Con un oggetto Facet, viene restituito un facet più piccolo di visi simili alla faccia specificata. Con una matrice di ID viso, restituisce in modo analogo una matrice più piccola.

### <a name="group"></a>Gruppo

L'operazione di [raggruppamento](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) accetta una matrice di ID viso assortiti da DetectedFace o PersistedFace e restituisce gli stessi ID raggruppati in diverse matrici più piccole. Ogni matrice "groups" contiene ID facciali simili. Una singola matrice "messyGroup" contiene ID viso per i quali non sono state trovate analogie.

### <a name="identify"></a>Identificare

L'operazione di [Identificazione](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) accetta uno o più ID Face da DetectedFace o PersistedFace e da un gruppo e restituisce un elenco di oggetti Person a cui ogni face potrebbe appartenere. Gli oggetti Person restituiti vengono incapsulati come oggetti candidati, che hanno un valore di confidenza per la stima.

## <a name="input-data"></a>Dati di input

Usare i suggerimenti seguenti per assicurarsi che le immagini di input forniscano i risultati di riconoscimento più accurati:

* I formati di immagine di input supportati sono JPEG, PNG, GIF (il primo frame), BMP.
* Le dimensioni del file di immagine non devono essere superiori a 4 MB.
* Quando si creano oggetti Person, usare foto che presentano diversi tipi di angoli e illuminazione.
* Alcuni visi potrebbero non essere riconosciuti a causa di problemi tecnici, ad esempio:
  * Immagini con illuminazione eccessiva, ad esempio, backlighting grave.
  * Ostacoli che bloccano uno o entrambi gli occhi.
  * Differenze tra il tipo di capelli o i peli facciali.
  * Modifiche all'aspetto del viso a causa dell'età.
  * Espressioni facciali estreme.

## <a name="next-steps"></a>Passaggi successivi

Ora che si ha familiarità con i concetti relativi al riconoscimento facciale, è possibile scrivere uno script che identifichi i visi rispetto a un gruppo con training.

* [Identificare i visi nelle immagini](../Face-API-How-to-Topics/HowtoIdentifyFacesinImage.md)