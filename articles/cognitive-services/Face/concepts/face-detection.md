---
title: Concetti relativi al rilevamento dei volti e agli attributi
titleSuffix: Azure Cognitive Services
description: Il rilevamento volti è l'azione di individuare i volti umani in un'immagine e, facoltativamente, di restituire diversi tipi di dati relativi ai volti.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: pafarley
ms.openlocfilehash: 15e39eb9f5b8dd3556ea9ff8240bc2c9d252cd31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73743060"
---
# <a name="face-detection-and-attributes"></a>Rilevamento volti e attributi

In questo articolo vengono illustrati i concetti relativi al rilevamento volti e ai dati degli attributi facciali. Il rilevamento volti è l'azione di individuare i volti umani in un'immagine e, facoltativamente, di restituire diversi tipi di dati relativi ai volti.

Utilizzare l'operazione [Faccia - Rileva](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) per rilevare i volti in un'immagine. Come minimo, ogni volto rilevato corrisponde a un campo faceRectangle nella risposta. Questo set di coordinate in pixel per la parte sinistra, superiore, larghezza e altezza contrassegna la faccia posizionata. Utilizzando queste coordinate, è possibile ottenere la posizione della faccia e le sue dimensioni. Nella risposta API, i volti sono elencati per dimensioni dal più grande al più piccolo.

## <a name="face-id"></a>ID viso

L'ID volto è una stringa di identificazione univoca per ogni volto rilevato in un'immagine. Puoi richiedere un FACE ID nella chiamata [API Face - Detect.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)

## <a name="face-landmarks"></a>Punti di riferimento del viso

I punti di riferimento del viso sono un insieme di punti facili da trovare su un viso, come le pupille o la punta del naso. Per impostazione predefinita, esistono 27 punti di riferimento predefiniti. La figura seguente mostra tutti i 27 punti:

![Un diagramma facciale con tutti i 27 punti di riferimento etichettati](../Images/landmarks.1.jpg)

Le coordinate dei punti vengono restituite in unità di pixel.

## <a name="attributes"></a>Attributi

Gli attributi sono un insieme di funzionalità che possono essere rilevate facoltativamente dall'API [Face - Detect.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) È possibile rilevare i seguenti attributi:

* **Età**. L'età stimata in anni di un particolare volto.
* **Sfocatura**. La sfocatura del volto nell'immagine. Questo attributo restituisce un valore compreso tra zero e uno e una classificazione informale di basso, medio o alto.
* **Emozione**. Un elenco di emozioni con la loro fiducia di rilevamento per il volto dato. I punteggi di confidenza sono normalizzati e i punteggi in tutte le emozioni si sommano a uno. Le emozioni restituite sono la felicità, la tristezza, il neutro, la rabbia, il disprezzo, il disgusto, la sorpresa e la paura.
* **Esposizione**. L'esposizione del volto nell'immagine. Questo attributo restituisce un valore compreso tra zero e uno e una classificazione informale di underExposure, goodExposure o overExposure.
* **Capelli facciali**. La presenza stimata dei peli del viso e la lunghezza per il viso dato.
* **Sesso**. Il genere stimato del volto dato. I valori possibili sono maschio, femmina e genderless.
* **Occhiali**. Se il volto dato ha occhiali. I valori possibili sono NoGlasses, ReadingGlasses, Sunglasses e Swimming Goggles.
* **Capelli**. Il tipo di capelli del viso. Questo attributo indica se i capelli sono visibili, se viene rilevata calvizie e quali colori vengono rilevati.
* **Posa testa**. Orientamento della faccia nello spazio 3D. Questo attributo è descritto dagli angoli di passo, rollio e immatrale in gradi. Gli intervalli di valori sono rispettivamente da -90 gradi a 90 gradi, da -180 gradi a 180 gradi e da -90 gradi a 90 gradi. Vedere il diagramma seguente per i mapping degli angoli:See the following diagram for angle mappings:

    ![Una testa con gli assi pitch, roll e immato](../Images/headpose.1.jpg)
* **Trucco**. Se la faccia ha il trucco. Questo attributo restituisce un valore booleano per eyeMakeup e lipMakeup.
* **Rumore**. Il disturbo visivo rilevato nell'immagine del volto. Questo attributo restituisce un valore compreso tra zero e uno e una classificazione informale di basso, medio o alto.
* **Occlusione**. Se ci sono oggetti che bloccano parti della faccia. Questo attributo restituisce un valore booleano per eyeOccluded, foreheadOccluded e mouthOccluded.
* **Sorridi**. L'espressione sorriso del volto dato. Questo valore è compreso tra zero per nessun sorriso e uno per un sorriso chiaro.

> [!IMPORTANT]
> Gli attributi faccia vengono stimati tramite l'uso di algoritmi statistici. Potrebbero non essere sempre precisi. Prestare attenzione quando si prendono decisioni in base ai dati degli attributi.

## <a name="input-data"></a>Dati di input

Utilizzare i suggerimenti seguenti per assicurarsi che le immagini di input forniscano i risultati di rilevamento più accurati:

* I formati di immagine di input supportati sono JPEG, PNG, GIF per il primo fotogramma e BMP.
* La dimensione del file di immagine non deve superare i 4 MB.
* L'intervallo di dimensioni del volto rilevabili va da 36 x 36 a 4096 x 4096 pixel. Le facce al di fuori di questo intervallo non verranno rilevate.
* Alcuni volti potrebbero non essere rilevati a causa di problemi tecnici. Angoli estremi del viso (posa della testa) o occlusione del viso (oggetti come occhiali da sole o mani che bloccano parte del viso) possono influenzare il rilevamento. Le facce frontali e quasi frontali danno i migliori risultati.

Se stai rilevando volti da un feed video, potresti essere in grado di migliorare le prestazioni regolando alcune impostazioni della videocamera:

* **Arrotondamento**: Molte videocamere applicano un effetto levigante. È necessario disattivare questa opzione se è possibile perché crea una sfocatura tra i fotogrammi e riduce la chiarezza.
* **Velocità dell'otturatore**: Una velocità dell'otturatore più veloce riduce la quantità di movimento tra i fotogrammi e rende ogni fotogramma più chiaro. Si consiglia una velocità dell'otturatore di 1/60 secondi o superiore.
* **Angolo dell'otturatore**: Alcune telecamere specificano l'angolo dell'otturatore anziché la velocità dell'otturatore. Se possibile, è necessario utilizzare un angolo dell'otturatore inferiore. Questo si tradurrà in fotogrammi video più chiari.

    >[!NOTE]
    > Una fotocamera con un angolo dell'otturatore inferiore riceverà meno luce in ogni fotogramma, quindi l'immagine sarà più scura. È necessario determinare il livello corretto da utilizzare.

## <a name="next-steps"></a>Passaggi successivi

Ora che hai familiarità con i concetti di rilevamento volti, scopri come scrivere uno script che rilevi i volti in una determinata immagine.

* [Rilevare i volti in un'immagine](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)