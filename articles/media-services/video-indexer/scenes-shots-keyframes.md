---
title: Scene indicizzatore video, schermate e fotogrammi chiave - Azure
titlesuffix: Azure Media Services
description: In questo argomento offre una panoramica delle scene indicizzatore Video, scatti e fotogrammi chiave.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: dfa41dc695cf6ab357a9cd4cdbd32454b6dd107d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60560104"
---
# <a name="scenes-shots-and-keyframes"></a>Scene, scatti e fotogrammi chiave

Video Indexer supporta segmentando i video in unità temporali in base alle proprietà strutturale e semantica. Questa funzionalità consente ai clienti di esplorare, gestire e modificare i contenuti video basato su variabile con la granularità. Ad esempio, in base scene, scatti e fotogrammi chiave, descritte in questo argomento. Il **rilevamento di scena** funzionalità è attualmente in anteprima.   

![Scene, scatti e fotogrammi chiave](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)

## <a name="scene-detection-preview"></a>Rilevamento della scena (anteprima)

Video Indexer determina quando viene modificato una scena nel video in base a segnali visivi. Una scena illustra un singolo evento ed è composto da una serie di schermate consecutivi, semanticamente correlate. Un'immagine di anteprima di scena è il primo fotogramma chiave del relativo cattura sottostante. Indicizzatore video i segmenti di un video in base alla coerenza nei colori in riprese scene e recupera l'inizio e ora di fine di ogni scena. Il rilevamento della scena viene considerato un'attività piuttosto complessa quanto comporta la quantificazione aspetti semantici del video.

> [!NOTE]
> È applicabile a video contenenti quinte almeno 3.

## <a name="shot-detection"></a>Rilevamento inquadratura

Video Indexer determina quando viene modificato un scatto nel video base segnali visivi, tenendo traccia delle transizioni improvvise sia graduale nella combinazione di colori di fotogrammi adiacenti. I metadati di cattura includono un inizio e ora di fine, nonché l'elenco dei fotogrammi chiave inclusi in tale cattura. Le riprese sono frame consecutivi impiegati tra la camera stesso nello stesso momento.

## <a name="keyframe-detection"></a>Rilevamento fotogramma chiave

Seleziona i frame che meglio rappresentano la ripresa. I fotogrammi chiave sono i frame rappresentativi selezionati l'intero video in base alle proprietà estetica (ad esempio, contrasto elevato e stableness). Video Indexer recupera un elenco di fotogramma chiave ID come parte dei metadati di ripresa, basato dei clienti che consente di estrarre l'anteprima di fotogramma chiave. 

I fotogrammi chiave sono associati a schermate nell'output JSON. 

## <a name="next-steps"></a>Passaggi successivi

[Esaminare l'output a Video Indexer prodotto dall'API](video-indexer-output-json-v2.md#scenes)