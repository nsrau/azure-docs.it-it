---
title: Video Indexer scene, scatti e fotogrammi chiave-Azure
titlesuffix: Azure Media Services
description: In questo argomento viene illustrata una panoramica delle Video Indexer scene, degli scatti e dei fotogrammi chiave.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: cdabc1b6bfed519098f656710ef49a946e676cf2
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815659"
---
# <a name="scenes-shots-and-keyframes"></a>Scene, scatti e fotogrammi chiave

Video Indexer supporta la segmentazione dei video in unità temporali in base alle proprietà strutturali e semantiche. Questa funzionalità consente ai clienti di esplorare, gestire e modificare facilmente i contenuti video in base a diverse granularità. Ad esempio, in base a scene, scatti e fotogrammi chiave, descritti in questo argomento.   

![Scene, scatti e fotogrammi chiave](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)
 
## <a name="scene-detection"></a>Rilevamento della scena  
 
Video Indexer determina quando una scena viene modificata in video in base a segnali visivi. Una scena raffigura un singolo evento ed è costituita da una serie di scatti consecutivi, che sono semanticamente correlati. Un'anteprima della scena è il primo fotogramma chiave dell'immagine sottostante. L'indicizzatore video suddivide un video in scene basate sulla coerenza dei colori in scatti consecutivi e recupera l'ora di inizio e di fine di ogni scena. Il rilevamento della scena è considerato un'attività complessa perché comporta la quantificazione degli aspetti semantici dei video.

> [!NOTE]
> Applicabile ai video contenenti almeno tre scene.

## <a name="shot-detection"></a>Rilevamento inquadratura

Video Indexer determina quando un video viene modificato in base ai segnali visivi, monitorando transizioni improvvise e graduali nella combinazione di colori dei frame adiacenti. I metadati dell'immagine includono un'ora di inizio e di fine, nonché l'elenco dei fotogrammi chiave inclusi in tale scatto. Gli scatti sono i frame consecutivi ricavati dalla stessa fotocamera nello stesso momento.

## <a name="keyframe-detection"></a>Rilevamento di fotogrammi chiave

Seleziona i frame che meglio rappresentano il tentativo. I fotogrammi chiave sono i fotogrammi rappresentativi selezionati dall'intero video in base alle proprietà estetiche, ad esempio contrasto e stabilità. Video Indexer recupera un elenco di ID di fotogrammi chiave come parte dei metadati dell'immagine, in base ai clienti che possono estrarre l'anteprima del fotogramma chiave. 

I fotogrammi chiave sono associati a scatti nel codice JSON di output. 

## <a name="next-steps"></a>Passaggi successivi

[Esaminare l'output del Video Indexer prodotto dall'API](video-indexer-output-json-v2.md#scenes)
