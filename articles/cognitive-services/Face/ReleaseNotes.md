---
title: Note sulla versione-servizio Face
titleSuffix: Azure Cognitive Services
description: Le note sulla versione per il servizio viso includono una cronologia delle modifiche di versione per varie versioni.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: yluiu
ms.openlocfilehash: 767c9dec373a2bda806d75d602b194edde98c6b5
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76165859"
---
# <a name="face-release-notes"></a>Note sulla versione del volto

Questo articolo è relativo alla versione 1,0 del servizio Face.

### <a name="release-changes-in-june-2019"></a>Modifiche della versione nel 2019 giugno

* È stato aggiunto un nuovo modello di rilevamento viso con maggiore precisione sui visi piccoli, a visualizzazione laterale, bloccati e sfocati. Usare il tipo di rilevamento [viso](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), facet [-Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList-Add](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)visi, [gruppo person-Add](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) Face e [LargePersonGroup person-Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) , specificando il nuovo nome del modello di rilevamento viso `detection_02` `detectionModel` parametro. Ulteriori informazioni su [come specificare un modello di rilevamento](Face-API-How-to-Topics/specify-detection-model.md).

### <a name="release-changes-in-april-2019"></a>Modifiche della versione nel 2019 aprile

* Accuratezza complessiva migliorata degli attributi `age` e `headPose`. Anche l'attributo `headPose` viene aggiornato con il valore di `pitch` abilitato ora. Usare questi attributi specificando questi attributi nel parametro `returnFaceAttributes` del parametro `returnFaceAttributes` del [rilevamento delle facce](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) . 

* Maggiore velocità di [rilevamento viso](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), facet [-Add](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)visi, [LargeFaceList-Add](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)face, [gruppo person-Add](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) Face e [LargePersonGroup person-Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42).

### <a name="release-changes-in-march-2019"></a>Modifiche della versione nel 2019 marzo

* È stato aggiunto un nuovo modello di riconoscimento viso con maggiore accuratezza. Usare il [rilevamento delle facce](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [facet-create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [LargeFaceList-create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [gruppo-create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) e [LargePersonGroup-create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d) specificando il nuovo nome del modello di riconoscimento della faccia `recognition_02` in `recognitionModel` parametro. Ulteriori informazioni su [come specificare un modello di riconoscimento](Face-API-How-to-Topics/specify-recognition-model.md).

### <a name="release-changes-in-january-2019"></a>Modifiche alla versione di gennaio 2019

* Aggiunta della funzionalità snapshot per supportare la migrazione dei dati tra sottoscrizioni: [snapshot](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/snapshot-get). Altre informazioni su [come eseguire la migrazione dei dati volti a una sottoscrizione di un altro viso](Face-API-How-to-Topics/how-to-migrate-face-data.md).

### <a name="release-changes-in-october-2018"></a>Modifiche alla versione di ottobre 2018

* Perfezionata la descrizione per `status`, `createdDateTime`, `lastActionDateTime` e `lastSuccessfulTrainingDateTime` in [PersonGroup - Get Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247), [LargePersonGroup - Get Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae32c6ac60f11b48b5aa5) e [LargeFaceList - Get Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a1582f8d2de3616c086f2cf).

### <a name="release-changes-in-may-2018"></a>Modifiche alla versione di maggio 2018

* L'attributo `gender` è stato migliorato significativamente, inoltre sono stati modificati anche gli attributi `age`, `glasses`, `facialHair`, `hair` e `makeup`. Usare i parametri tramite il parametro [di rilevamento delle facce](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes`. 

* Il limite delle dimensioni del file di immagine di input è stato aumentato da 4 MB a 6 MB in [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) e [LargePersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42).

### <a name="release-changes-in-march-2018"></a>Modifiche alla versione di marzo 2018

* È stato aggiunto il contenitore Million-Scale: [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) e [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d). Maggiori dettagli sono descritti in [Come usare la funzionalità su larga scala](Face-API-How-to-Topics/how-to-use-large-scale.md).

* Aumentare [il](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) valore di `maxNumOfCandidatesReturned` parametro da [1, 5] a [1, 100] e il valore predefinito è 10.

### <a name="release-changes-in-may-2017"></a>Modifiche alla versione di maggio 2017

* Sono stati aggiunti attributi `hair`, `makeup`, `accessory`, `occlusion`, `blur`, `exposure`e `noise` nel parametro del `returnFaceAttributes` del [rilevamento delle facce](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) .

* PersonGroup e [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) supportano 10.000 persone.

* È supportata la paginazione in [PersonGroup Person - List](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241) con i parametri facoltativi: `start` e `top`.

* È supportata la concorrenza nell'aggiunta/nell'eliminazione di volti in FaceList diversi e persone diverse in PersonGroup.

### <a name="release-changes-in-march-2017"></a>Modifiche alla versione di marzo 2017
* Aggiunta `emotion` attributo nei parametri di `returnFaceAttributes` del [rilevamento delle facce](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) .

* È stato risolto il problema di impossibilità di rilevare il viso con la restituzione di un rettangolo da [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) come `targetFace` in [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) e [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

* È stato risolto il problema delle dimensioni del viso rilevabili per assicurarsi che non superino assolutamente 36 x 36 pixel a 4096 x 4096 pixel.

### <a name="release-changes-in-november-2016"></a>Modifiche alla versione di novembre 2016
* È stata aggiunta la sottoscrizione standard ad Archiviazione visi per archiviare visi persistenti aggiuntivi quando si usa [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) o [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) per l'identificazione o la ricerca di analogie. Il costo delle immagini archiviate è di 0,5 dollari per 1.000 visi e alla tariffa viene applicata la ripartizione proporzionale giornaliera. Le sottoscrizioni del livello gratuito continuano a essere limitate a un totale di 1.000 persone.

### <a name="release-changes-in-october-2016"></a>Modifiche alla versione di ottobre 2016
* Il messaggio di errore che indica più di un viso in targetFace è cambiato da 'There are more than one face in the image' (Più visi presenti nell'immagine) a 'There is more than one face in the image' (Più di un viso presente nell'immagine) in [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) e [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

### <a name="release-changes-in-july-2016"></a>Modifiche alla versione di luglio 2016
* È supportata l'autenticazione dell'oggetto Face to Person in [Face - Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

* È stato aggiunto il parametro `mode` facoltativo che consente la selezione di due modalità di lavoro: `matchPerson` e `matchFace` in [Face - Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), mentre il parametro predefinito è `matchPerson`.

* È stato aggiunto il parametro `confidenceThreshold` facoltativo per consentire all'utente di impostare la soglia che definisce se un viso appartiene a un oggetto Person in [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

* Sono stati aggiunti i parametri `start` e `top` facoltativi in [PersonGroup - List](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395248) per consentire all'utente di specificare il punto iniziale e il numero totale di PersonGroups all'elenco.

### <a name="v10-changes-from-v0"></a>Modifiche da V1.0 a V0
* L'endpoint radice del servizio è stato aggiornato da ```https://westus.api.cognitive.microsoft.com/face/v0/``` a ```https://westus.api.cognitive.microsoft.com/face/v1.0/```. Sono state apportate modifiche a: [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [Face - Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) e [Face - Group](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

* Le dimensioni minime rilevabili per i visi sono state aggiornate a 36 x 36 pixel. I visi di dimensioni inferiori a 36 x 36 pixel non vengono rilevati.

* I dati di PersonGroup e Person in Viso V0 sono deprecati. Non è possibile accedere a tali dati con il servizio Viso V1.0.

* L'endpoint V0 dell'API Viso è deprecato dal 30 giugno 2016.
