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
ms.openlocfilehash: 8c0bf001c2bdbedaa041dbd10b5c7edb08eec4c6
ms.sourcegitcommit: 524625dd12e0537173616a991802075e2dc9da12
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2019
ms.locfileid: "64416000"
---
# <a name="face-recognition-concepts"></a>Concetti di riconoscimento volto

Questo articolo illustra i concetti di varie operazioni di riconoscimento volto (verifica, ricerca in modo analogo, il raggruppamento, identificazione) e le strutture di dati sottostante. Su vasta scala, riconoscimento descrive le operazioni di confronto di due visi diversi per determinare se sono simili o appartenere alla stessa persona.

## <a name="recognition-related-data-structures"></a>Strutture di dati correlati al riconoscimento

Le operazioni di riconoscimento utilizzano principalmente le strutture di dati seguenti. Questi oggetti vengono archiviati nel cloud e le relative stringhe ID possono fare riferimento. Stringhe ID sono pertanto univoche all'interno di una sottoscrizione, sempre mentre i campi nome possono essere duplicati.

|NOME|DESCRIZIONE|
|:--|:--|
|**DetectedFace**| Questa è una rappresentazione singolo carattere tipografico recuperata tramite il [rilevamento viso](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md) operazione. Il relativo ID scade 24 ore dopo averlo creato.|
|**PersistedFace**| Quando **DetectedFace** gli oggetti vengono aggiunti a un gruppo (ad esempio **FaceList** o **persona**), questi diventano **PersistedFace** oggetti, che può essere essere [recuperati](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c) in corrispondenza di qualsiasi ora e che non scadono.|
|**[FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b)**/**[LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)**| Si tratta di un elenco di vengono formattati diverse **PersistedFace** oggetti. Oggetto **FaceList** dispone di un ID univoco, una stringa del nome e, facoltativamente, una stringa di dati utente.|
|**[Person](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)**| Si tratta di un elenco delle **PersistedFace** oggetti appartenenti alla stessa persona. Dispone di un ID univoco, una stringa del nome e, facoltativamente, una stringa di dati utente.|
|**[PersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)**/**[LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)**| Si tratta di un elenco di vengono formattati diverse **persona** oggetti. Dispone di un ID univoco, una stringa del nome e, facoltativamente, una stringa di dati utente. Oggetto **gruppo di persone** deve essere [sottoposto a training](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) prima di poter essere utilizzato nelle operazioni di riconoscimento.|

## <a name="recognition-operations"></a>Operazioni di riconoscimento

In questa sezione descrive in dettaglio come le operazioni di riconoscimento quattro usano strutture di dati precedente. Vedere le [Panoramica](../Overview.md) per una descrizione di ogni operazione di riconoscimento ampia.

### <a name="verification"></a>Verifica

Il [Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) operazione accetta un ID viso (**DetectedFace** oppure **PersistedFace**) e l'ID viso un'altra o un **persona** oggetto e Determina se appartengono alla stessa persona. Se si passa un **Person**, è possibile passare facoltativamente un **gruppo di persone** in cui **persona** appartiene per migliorare le prestazioni.

### <a name="find-similar"></a>Argomenti correlati

Il [simili](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) operazione accetta un ID viso (**DetectedFace** oppure **PersistedFace**) e il valore un **FaceList** o una matrice di altri viso ID. Con un **FaceList**, restituisce un minore **FaceList** di visi simili per il viso specificato. Con una matrice di ID volto, in modo analogo restituisce una matrice di più piccola.

### <a name="grouping"></a>Raggruppamento

Il [gruppo](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) operazione accetta una matrice di ID volto vengono formattati diverse (**DetectedFace** oppure **PersistedFace**) e restituisce gli stessi ID raggruppati in numerose matrici più piccole. Ogni matrice "groups" contenente face ID che è simile e una matrice di singolo "messyGroup" face ID per cui sono state trovate non analogie.

### <a name="identification"></a>Identificazione

Il [Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) operazione accetta uno o più face ID (**DetectedFace** oppure **PersistedFace**) e un **gruppo di persone** e restituisce un elenco dei **persona** oggetti appartenenti a ogni faccia. Restituite **Person** sono inclusi gli oggetti come **candidato** gli oggetti che hanno un valore di probabilità di stima.

## <a name="input-data"></a>Dati di input

Usare i suggerimenti seguenti per assicurarsi che le immagini di input forniscono i risultati di riconoscimento più accurati:

* I formati di immagine di input supportati sono JPEG, PNG, GIF (il primo fotogramma), BMP.
* Dimensioni del file immagine devono essere non superiore a 4 MB.
* Durante la creazione **persona** oggetti, è consigliabile usare foto che forniscono un'ampia gamma di angoli e illuminazione.
* Alcuni volti potrebbero non essere riconosciute a causa di problemi tecnici:
  * Immagini con estrema illuminazione (ad esempio, retroilluminazione).
  * Limitazioni bloccando gli occhi di uno o entrambi.
  * Differenze nel selettore di stile o facciale precisione selettore di precisione.
  * Modifiche nell'aspetto volto a causa di età.
  * Espressioni del viso estreme.

## <a name="next-steps"></a>Passaggi successivi

Ora che si ha familiarità con i concetti di riconoscimento di volti, informazioni su come scrivere uno script semplice che identifica i visi in un training **gruppo di persone**.

* [Come identificare i visi nelle immagini](../Face-API-How-to-Topics/HowtoIdentifyFacesinImage.md)