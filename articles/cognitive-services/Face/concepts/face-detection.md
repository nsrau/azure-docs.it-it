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
ms.openlocfilehash: e61048eeab9d7061c18f3237db22fc87ca52f526
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65891163"
---
# <a name="face-detection-and-attributes"></a>Rilevamento viso e attributi

Questo articolo illustra i concetti di rilevamento del viso e dati dell'attributo viso. Rilevamento viso è l'azione di individuazione visi in un'immagine e facoltativamente restituire diversi tipi di dati relativi all'immagine.

Si utilizza il [affrontano: rilevare](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) rileva i visi in un'immagine dell'operazione. Come minimo, ogni volto rilevato corrisponde a un campo faceRectangle nella risposta. Questo set di coordinate di pixel per la sinistra, alto, larghezza e altezza contrassegnare l'immagine si trova. Usando queste coordinate, è possibile ottenere il percorso del viso e le relative dimensioni. Nella risposta API, le facce sono elencate in ordine di dimensioni dal più grande al più piccolo.

## <a name="face-id"></a>ID viso

L'ID del volto è una stringa identificatore univoco per ogni volto rilevato in un'immagine. È possibile richiedere un ID del volto nel [affrontano: rilevare](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) chiamata API.

## <a name="face-landmarks"></a>Punti di riferimento del viso

Riquadri sono un set di punti di facile da trovare in un viso, ad esempio gli alunni o di punta. Per impostazione predefinita, esistono 27 punti di riferimento predefiniti. La figura seguente mostra tutti i punti di 27:

![Un diagramma di viso con tutti i 27 riferimenti con etichettata](../Images/landmarks.1.jpg)

Le coordinate dei punti vengono restituite in unità di pixel.

## <a name="attributes"></a>Attributi

Gli attributi sono un set di funzionalità che possono essere rilevati se lo si desidera per il [affrontano: rilevare](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API. È possibile rilevare gli attributi seguenti:

* **Età**. La validità stimata in anni di un viso specifico.
* **Blur**. Sfocatura del viso nell'immagine. Questo attributo restituisce un valore compreso tra zero e uno e un valore di classificazione informale di bassa, Media o alta.
* **Emozioni**. Elenco delle emozioni in tutta sicurezza il rilevamento per il viso specificato. Vengono normalizzati i punteggi di confidenza, nonché i punteggi in tutte le emozioni aggiungere fino a uno. Le emozioni restituite sono felicità, tristezza, neutro, rabbia, biasimo, disgusto, sorpresa e la paura.
* **L'esposizione**. L'esposizione del viso nell'immagine. Questo attributo restituisce un valore compreso tra zero e uno e un valore di classificazione informale la sottoesposizione, goodExposure o sovraesposizione.
* **Selettore di precisione facciale**. La presenza di stimato facciale selettore di precisione e la lunghezza per il viso specificato.
* **Sesso**. Il genere stimato del volto specificato. I possibili valori sono di sesso maschile, femminile e genderless.
* **Occhiali**. Indica se il viso specificato dispone a forma di occhiali. I valori possibili sono NoGlasses ReadingGlasses, occhiali da sole e Swimming occhiali di protezione.
* **Hair**. Il tipo di selettore di precisione della faccia. Questo attributo Mostra se il selettore di precisione è visibile, se viene rilevato baldness e colori selettore di precisione vengono rilevate.
* **Head costituire**. Orientamento del viso nello spazio 3D. Questo attributo viene descritto il passo, esegue il rollup e degli angoli di rotazione in gradi. Intervalli di valori sono-90 gradi a 90 gradi, - 180 gradi a 180 gradi e gradi-90 e 90 gradi, rispettivamente. Vedere il diagramma seguente per i mapping angolo:

    ![Un'intestazione con il tono, eseguire il rollback e rotazione assi con etichetta](../Images/headpose.1.jpg)
* **Struttura**. Il quadrante la presenza di composizione. Questo attributo restituisce un valore booleano per eyeMakeup e lipMakeup.
* **Rumore**. Qualsiasi disturbo visivo rilevato nell'immagine del viso. Questo attributo restituisce un valore compreso tra zero e uno e un valore di classificazione informale di bassa, Media o alta.
* **Relative all'occlusione**. Se sono presenti oggetti blocco parti della faccia. Questo attributo restituisce un valore booleano per eyeOccluded foreheadOccluded e mouthOccluded.
* **Smile**. L'espressione di smile del volto specificato. Questo valore è compreso tra 0 per nessun smile e uno per uno smile chiaro.

> [!IMPORTANT]
> Attributi facciali stimati tramite l'uso di algoritmi statistici. Si potrebbe non essere sempre accurate. Prestare attenzione quando si prendono decisioni basate sui dati di attributi.

## <a name="input-data"></a>Dati di input

Usare i suggerimenti seguenti per assicurarsi che le immagini di input forniscono i risultati più accurati di rilevamento:

* I formati di immagine di input supportati sono JPEG, PNG, GIF per il primo frame e BMP.
* Le dimensioni del file di immagine devono essere non superiore a 4 MB.
* L'intervallo di dimensioni del volto rilevabili va da 36 x 36 a 4096 x 4096 pixel. Non verranno rilevati volti di fuori di questo intervallo.
* Potrebbe essere che alcuni volti non vengano rilevati a causa di problemi tecnici. Angoli del volto Extreme (head posa) o occlusione viso (oggetti, ad esempio occhiali da sole o mani che bloccano l'ambito della faccia) può hanno effetto sui rilevamenti. I volti frontali e quasi frontali offrono i migliori risultati.

Se si sta rileva i volti da un feed di video, potrebbe essere in grado di migliorare le prestazioni modificando determinate impostazioni sulla videocamera:

* **Anti-aliasing**: Videocamere molti applicare un effetto di smoothing. È consigliabile disattivare in questo caso è possibile perché crea un' sfocatura tra frame e riduce maggiore chiarezza.
* **Velocità dell'otturatore**: Una maggiore velocità di ridimensionamento riduce la quantità di movimento tra frame e rende più chiara ogni fotogramma. Si consiglia di velocità di ridimensionamento pari a 1/60 secondi o superiore.
* **Otturatore angolo**: Alcuni fotocamere specificare angolo ridimensionamento invece di tempo di esposizione. Se possibile, è consigliabile usare un angolo di ridimensionamento inferiore. Ciò comporterà frame video più chiari.

    >[!NOTE]
    > Una fotocamera con un angolo di ridimensionamento inferiore riceveranno meno luce in ogni frame, in modo che l'immagine sarà più scura. È necessario determinare il livello a destra da usare.

## <a name="next-steps"></a>Passaggi successivi

Ora che si ha familiarità con i concetti di rilevamento viso, informazioni su come scrivere uno script che rileva i visi in una determinata immagine.

* [Rileva i visi in un'immagine](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)