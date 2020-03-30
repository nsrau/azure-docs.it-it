---
title: Scene, riprese e fotogrammi chiave dell'indicizzatore video
titleSuffix: Azure Media Services
description: Questo argomento offre una panoramica delle scene, delle riprese e dei fotogrammi chiave dell'indicizzatore video.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: a833fd808049cfce95b182910e50e38d3c39f4e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245941"
---
# <a name="scenes-shots-and-keyframes"></a>Scene, scatti e fotogrammi chiave

Video Indexer supporta la segmentazione di video in unità temporali basate su proprietà strutturali e semantiche. Questa funzionalità consente ai clienti di sfogliare, gestire e modificare facilmente i propri contenuti video in base a diverse granularità. Ad esempio, in base a scene, riprese e fotogrammi chiave, descritti in questo argomento.   

![Scene, scatti e fotogrammi chiave](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)
 
## <a name="scene-detection"></a>Rilevamento scena  
 
L'indicizzatore video determina quando una scena cambia nel video in base a segnali visivi. Una scena raffigura un singolo evento ed è composta da una serie di scatti consecutivi, che sono semanticamente correlati. L'anteprima di una scena è il primo fotogramma chiave della ripresa sottostante. L'indicizzatore video segmenta un video in scene in base alla coerenza dei colori tra riprese consecutive e recupera l'ora di inizio e di fine di ogni scena. Il rilevamento delle scene è considerato un compito impegnativo in quanto comporta la quantificazione degli aspetti semantici dei video.

> [!NOTE]
> Applicabile ai video che contengono almeno 3 scene.

## <a name="shot-detection"></a>Rilevamento inquadratura

L'indicizzatore video determina quando una ripresa cambia nel video in base a segnali visivi, tracciando transizioni improvvise e graduali nella combinazione di colori dei fotogrammi adiacenti. I metadati della ripresa includono un'ora di inizio e di fine, nonché l'elenco dei fotogrammi chiave inclusi in quella ripresa. Le riprese sono fotogrammi consecutivi presi dalla stessa fotocamera contemporaneamente.

## <a name="keyframe-detection"></a>Rilevamento dei fotogrammi chiave

L'indicizzatore video seleziona i fotogrammi che meglio rappresentano ogni ripresa. I fotogrammi chiave sono i fotogrammi rappresentativi selezionati dall'intero video in base alle proprietà estetiche (ad esempio, contrasto e stabilità). L'indicizzatore video recupera un elenco di ID fotogrammi chiave come parte dei metadati della ripresa, in base ai quali i clienti possono estrarre il fotogramma chiave come immagine ad alta risoluzione.  

### <a name="extracting-keyframes"></a>Estrazione di fotogrammi chiave

Per estrarre fotogrammi chiave ad alta risoluzione per il video, dovete prima caricare e indicizzare il video.

![Fotogrammi chiave](./media/scenes-shots-keyframes/extracting-keyframes.png)

#### <a name="with-the-video-indexer-website"></a>Con il sito Web Video Indexer

Per estrarre i fotogrammi chiave utilizzando il sito Web Indicizzatore video, caricate e indicizzate il video. Una volta completato il processo di indicizzazione, fare clic sul pulsante **Download** e selezionare **Elementi (CAP)**. La cartella degli elementi verrà scaricata nel computer. 

![Fotogrammi chiave](./media/scenes-shots-keyframes/extracting-keyframes2.png)
 
Decomprimere e aprire la cartella. Nella *cartella _KeyframeThumbnail,* troverete tutti i fotogrammi chiave estratti dal video. 

#### <a name="with-the-video-indexer-api"></a>Con l'API Dell'indicizzatore video

Per ottenere i fotogrammi chiave utilizzando l'API Dell'indicizzatore video, carica e indicizza il video tramite la chiamata [Carica video.](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Upload-Video?) Una volta completato il processo di indicizzazione, chiamare [Get Video Index](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Index?). In questo modo verranno dettagliate l'indicizzatore video estratto dal contenuto in un file JSON.  

Otterrete un elenco di ID fotogrammi chiave come parte dei metadati di ogni ripresa. 

```json
"shots":[  
    {  
      "id":0,
      "keyFrames":[  
          {  
            "id":0,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:00.209",
                  "end":"0:00:00.251",
                  "duration":"0:00:00.042"
                }
            ]
          },
          {  
            "id":1,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:04.755",
                  "end":"0:00:04.797",
                  "duration":"0:00:00.042"
                }
            ]
          }
      ],
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },

]
```

A questo punto è necessario eseguire ognuno di questi ID dei fotogrammi chiave nella chiamata [Ottieni miniature.](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Thumbnail?) Questo scaricherà ciascuna delle immagini dei fotogrammi chiave sul tuo computer. 

## <a name="editorial-shot-type-detection"></a>Rilevamento del tipo di ripresa editoriale

I fotogrammi chiave sono associati alle schermate nel codice JSON di output. 

Il tipo di ripresa associato a una singola ripresa nelle informazioni JSON rappresenta il tipo editoriale. Queste caratteristiche del tipo di ripresa possono risultare utili quando si modificano video in clip, trailer o quando si cerca uno stile specifico di fotogramma chiave per scopi artistici. I diversi tipi vengono determinati in base all'analisi del primo fotogramma chiave di ogni ripresa. Le riprese sono identificate dalla scala, dalle dimensioni e dalla posizione delle facce visualizzate nel primo fotogramma chiave. 

Le dimensioni e la scala dello scatto sono determinate in base alla distanza tra la fotocamera e le facce che appaiono nel fotogramma. Utilizzando queste proprietà, Video Indexer rileva i seguenti tipi di ripresa:

* Ampio: mostra il corpo di un'intera persona.
* Medio: mostra la parte superiore del corpo e il viso di una persona.
* Da vicino: mostra principalmente il volto di una persona.
* Primo piano estremo: mostra il volto di una persona che riempie lo schermo. 

I tipi di ripresa possono anche essere determinati dalla posizione dei caratteri soggetto rispetto al centro della cornice. Questa proprietà definisce i seguenti tipi di inquadrature nell'indicizzatore video:

* Faccia sinistra: una persona appare sul lato sinistro della cornice.
* Faccia centrale: una persona viene visualizzata nell'area centrale della cornice.
* Faccia destra: una persona appare nella parte destra del telaio.
* All'aperto: una persona appare in un ambiente esterno.
* Indoor: una persona appare in un ambiente interno.

Caratteristiche aggiuntive:

* Due scatti: mostra volti di due persone di medie dimensioni.
* Facce multiple: più di due persone.


## <a name="next-steps"></a>Passaggi successivi

[Esaminare l'output dell'indicizzatore video prodotto dall'API](video-indexer-output-json-v2.md#scenes)
