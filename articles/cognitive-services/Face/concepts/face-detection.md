---
title: Rilevamento viso e i concetti di attributi
titleSuffix: Azure Cognitive Services
description: Informazioni su concetti sul rilevamento del viso e attributi facciali.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: pafarley
ms.openlocfilehash: 3293067190386738efbfeb433bd38453c9e5699f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/26/2019
ms.locfileid: "64572545"
---
# <a name="face-detection-and-attributes"></a>Rilevamento viso e attributi

Questo articolo illustra i concetti di rilevamento del viso e dati dell'attributo viso. Rilevamento viso è l'azione di individuazione visi in un'immagine e, facoltativamente, restituendo un'ampia gamma di dati correlati alla superficie.

Si utilizza il [affrontano: rilevare](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) rileva i visi in un'immagine dell'operazione. Come minimo, ogni rilevata viso corrisponderà a un **faceRectangle** campo nella risposta. Si tratta di un set di coordinate pixel (left, top, width, height) contrassegnando l'immagine si trova. Usando queste coordinate, è possibile ottenere il percorso del viso, nonché le dimensioni. Nella risposta API, le facce sono elencate in ordine di dimensioni dal più grande al più piccolo.

## <a name="face-id"></a>ID viso

L'ID del volto è semplicemente una stringa identificatore univoco per ogni volto rilevato in un'immagine. È possibile richiedere un ID del volto nel [affrontano: rilevare](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) chiamata API.

## <a name="face-landmarks"></a>Punti di riferimento del viso

Riquadri sono un set di punti di facile da trovare in un viso, ad esempio gli alunni o il suggerimento di punta. Per impostazione predefinita, esistono 27 punti di riferimento predefiniti. La figura seguente mostra tutti i punti di 27:

![Un diagramma di viso con tutti i 27 riferimenti con etichettata](../Images/landmarks.1.jpg)

Le coordinate dei punti vengono restituite in unità di pixel.

## <a name="attributes"></a>Attributi

Gli attributi sono un set di funzionalità faccia aggiuntiva che può facoltativamente essere rilevati dai [affrontano: rilevare](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API. Di seguito sono gli attributi che possono essere rilevati:

* **Età** la durata stimata, nel corso degli anni, di un viso specifico.
* **Offusca** sfocatura del viso nell'immagine. Questo metodo restituisce un valore tra zero e uno, nonché una valutazione informale ("bassa", "medium", "alta").
* **L'esposizione** l'esposizione del viso nell'immagine. Questo restituisce un valore compreso tra zero e uno, nonché una valutazione informale ("la sottoesposizione", "goodExposure", "sovraesposizione").
* **Emozioni** un elenco delle emozioni in tutta sicurezza il rilevamento per il viso specificato. Vengono normalizzati i punteggi di confidenza: i punteggi in tutte le emozioni verranno aggiunto a una. Le emozioni restituite sono felicità, tristezza, neutro, rabbia, biasimo, disgusto, sorpresa e la paura.
* **Selettore di precisione facciale** la presenza di stimato facciale selettore di precisione e lunghezza del viso specifico.
* **Sesso** il sesso stimato del viso specifico. I valori possibili sono "male", "female" e "genderless".
* **Occhiali** se il viso specificato dispone a forma di occhiali. I valori possibili sono "NoGlasses", "ReadingGlasses", "Occhiali da sole" e "Occhiali di protezione Swimming".
* **Selettore di precisione** lo stile del selettore di precisione della faccia. Mostra se il selettore di precisione è visibile, se viene rilevato baldness e colori selettore di precisione vengono rilevate.
* **Head costituire** orientamento del viso nello spazio 3D. Questa procedura è descritta da passo, esegue il rollup e degli angoli di rotazione in gradi. Intervalli di valori sono [-90, 90], [-180, 180] e [-90, 90] gradi rispettivamente. Vedere il diagramma seguente per i mapping angolo:

    ![Un'intestazione con il tono, eseguire il rollback e rotazione assi con etichetta](../Images/headpose.1.jpg)
* **Struttura** se il viso dispone di composizione. Restituisce un valore booleano per "eyeMakeup" e "lipMakeup".
* **Rumore** qualsiasi disturbo visivo rilevato nell'immagine del viso. Questo metodo restituisce un valore tra zero e uno, nonché una valutazione informale ("bassa", "medium", "alta").
* **Relative all'occlusione** se sono presenti oggetti parti della faccia di blocco. Restituisce un valore booleano per "eyeOccluded", "foreheadOccluded" e "mouthOccluded".
* **Smile** smile espressione del volto specificato. Si tratta di un valore compreso tra zero (non smile) e l'altra (smile chiaro).

> [!IMPORTANT]
> Attributi facciali stimati tramite l'uso di algoritmi statistici e potrebbero non essere sempre accurati. Prestare attenzione quando si eseguono decisioni basate sui dati dell'attributo.

## <a name="input-data"></a>Dati di input

Usare i suggerimenti seguenti per assicurarsi che le immagini di input forniscono i risultati più accurati di rilevamento:

* I formati di immagine di input supportati sono JPEG, PNG, GIF (il primo fotogramma), BMP.
* Dimensioni del file immagine devono essere non superiore a 4 MB.
* L'intervallo delle dimensioni rilevabili del volto va da 36x36 a 4096x4096 pixel. Non verranno rilevati volti di fuori di questo intervallo.
* Alcuni volti non possono essere rilevati a causa di problemi tecnici. Angoli del volto Extreme (head posa) o occlusione viso (oggetti, ad esempio occhiali da sole o mani della faccia parte di blocco) può hanno effetto sui rilevamenti. I volti frontali e quasi frontali offrono i migliori risultati.

## <a name="next-steps"></a>Passaggi successivi

Ora che si ha familiarità con i concetti di rilevamento viso, informazioni su come scrivere uno script semplice che rileva i visi in una determinata immagine.

* [Come rilevare i visi in un'immagine](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)