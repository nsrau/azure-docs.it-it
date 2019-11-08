---
title: Concetti di rilevamento viso e attributi
titleSuffix: Azure Cognitive Services
description: Il rilevamento viso è l'azione di individuare i visi umani in un'immagine e, facoltativamente, restituire tipi diversi di dati correlati ai visi.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: pafarley
ms.openlocfilehash: 15e39eb9f5b8dd3556ea9ff8240bc2c9d252cd31
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73743060"
---
# <a name="face-detection-and-attributes"></a>Rilevamento viso e attributi

In questo articolo vengono illustrati i concetti relativi ai dati relativi al rilevamento viso e agli attributi Face. Il rilevamento viso è l'azione di individuare i visi umani in un'immagine e, facoltativamente, restituire tipi diversi di dati correlati ai visi.

Usare l'operazione di [rilevamento viso](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) per rilevare i visi in un'immagine. Come minimo, ogni face rilevato corrisponde a un campo faceRectangle nella risposta. Questo set di coordinate pixel per la parte sinistra, superiore, larghezza e altezza contrassegna la superficie individuata. Utilizzando queste coordinate, è possibile ottenere la posizione della faccia e le relative dimensioni. Nella risposta dell'API, le facce sono elencate in ordine di dimensioni dal più grande al più piccolo.

## <a name="face-id"></a>ID viso

Il Face ID è una stringa identificatore univoco per ogni volto rilevato in un'immagine. È possibile richiedere un ID viso nella chiamata dell'API di [rilevamento facciale](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) .

## <a name="face-landmarks"></a>Punti di riferimento del viso

I punti di interesse della faccia sono un set di punti di facile individuazione su una faccia, ad esempio gli alunni o la punta del naso. Per impostazione predefinita, esistono 27 punti di riferimento predefiniti. Nella figura seguente vengono illustrati tutti i 27 punti:

![Diagramma del viso con tutti i 27 punti di riferimento con etichetta](../Images/landmarks.1.jpg)

Le coordinate dei punti vengono restituite in unità di pixel.

## <a name="attributes"></a>Attributes

Gli attributi sono un set di funzionalità che possono essere rilevate facoltativamente dall'API di [rilevamento facciale](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) . È possibile rilevare gli attributi seguenti:

* Data di **scadenza**. Età stimata in anni di una particolare faccia.
* **Sfocatura**. Sfocatura della faccia nell'immagine. Questo attributo restituisce un valore compreso tra zero e uno e una classificazione informale di Low, medium o High.
* **Emozioni**. Elenco di emozioni con la relativa confidenza di rilevamento per il volto specificato. I punteggi di confidenza vengono normalizzati e i punteggi in tutte le emozioni ne aggiungono uno. Le emozioni restituite sono felicità, tristezza, neutralità, rabbia, disprezzo, disgusto, sorpresa e timore.
* **Esposizione**. Esposizione della faccia nell'immagine. Questo attributo restituisce un valore compreso tra zero e uno e una classificazione informale di sottoesposizione, goodExposure o overexposure.
* **Capelli facciali**. La presenza dei peli del viso stimata e la lunghezza per la superficie specificata.
* **Sesso**. Il sesso stimato del viso specificato. I valori possibili sono Malé, Female e Gender.
* **Occhiali**. Indica se il viso specificato ha occhiali. I valori possibili sono noglasses, ReadingGlasses, Sunglasses e occhiali da nuoto.
* **Capelli**. Tipo di capelli della faccia. Questo attributo indica se il pelo è visibile, se viene rilevata la calvizie e quali colori dei capelli vengono rilevati.
* **Responsabile**. Orientamento della faccia nello spazio 3D. Questo attributo è descritto dagli angoli pitch, roll e imbardata in gradi. Gli intervalli di valori sono da-90 gradi a 90 gradi, da-180 gradi a 180 gradi e da-90 gradi rispettivamente a 90 °. Vedere il diagramma seguente per i mapping degli angoli:

    ![Una testa con gli assi pitch, roll e imbardata con etichetta](../Images/headpose.1.jpg)
* **Trucco**. Indica se il viso è truccato. Questo attributo restituisce un valore booleano per eyeMakeup e lipMakeup.
* **Rumore**. Il rumore visivo rilevato nell'immagine della faccia. Questo attributo restituisce un valore compreso tra zero e uno e una classificazione informale di Low, medium o High.
* **Occlusione**. Indica se sono presenti oggetti che bloccano parti della faccia. Questo attributo restituisce un valore booleano per eyeOccluded, foreheadOccluded e mouthOccluded.
* **Smile**. Espressione Smile della faccia specificata. Questo valore è compreso tra zero per nessun Smile e uno per un sorriso chiaro.

> [!IMPORTANT]
> Gli attributi viso vengono stimati tramite l'uso di algoritmi statistici. Potrebbero non essere sempre accurate. Prestare attenzione quando si prendono decisioni in base ai dati degli attributi.

## <a name="input-data"></a>Dati di input

Usare i suggerimenti seguenti per assicurarsi che le immagini di input forniscano i risultati di rilevamento più accurati:

* I formati di immagine di input supportati sono JPEG, PNG, GIF per il primo frame e BMP.
* Le dimensioni del file di immagine non devono essere superiori a 4 MB.
* L'intervallo di dimensioni del volto rilevabili va da 36 x 36 a 4096 x 4096 pixel. Non verranno rilevati visi esterni a questo intervallo.
* Alcuni visi potrebbero non essere rilevati a causa di problemi tecnici. Gli angoli dei volti estremi (la forma Head) o l'occlusione della faccia (oggetti come occhiali da sole o mani che bloccano parte della faccia) possono influire sul rilevamento. I visi frontali e quasi frontali offrono i risultati migliori.

Se si rilevano visi da un feed video, è possibile migliorare le prestazioni modificando determinate impostazioni nella videocamera video:

* **Smussatura**: molte fotocamere video si applicano a un effetto uniforme. È necessario disattivare questa opzione se possibile perché crea una sfocatura tra i frame e riduce la chiarezza.
* **Velocità dell'otturatore**: una velocità di otturatore più veloce riduce la quantità di movimento tra i frame e rende più chiaro il frame. Si consiglia la velocità di scatto di 1/60 secondi o più velocemente.
* **Angolo**di apertura: alcune fotocamere specificano l'angolo di apertura anziché la velocità dell'otturatore. Se possibile, è consigliabile usare un angolo di apertura inferiore. Questa operazione determinerà un frame video più chiaro.

    >[!NOTE]
    > Una fotocamera con un angolo di scatto inferiore riceverà meno luce in ogni frame, quindi l'immagine sarà più scura. È necessario determinare il livello corretto da usare.

## <a name="next-steps"></a>Passaggi successivi

Ora che si ha familiarità con i concetti relativi al rilevamento dei volti, viene illustrato come scrivere uno script che rilevi i visi in una determinata immagine.

* [Rilevare i visi in un'immagine](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)