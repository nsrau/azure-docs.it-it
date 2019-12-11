---
title: Video Indexer scene, scatti e fotogrammi chiave
titleSuffix: Azure Media Services
description: In questo argomento viene illustrata una panoramica delle Video Indexer scene, degli scatti e dei fotogrammi chiave.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: a833fd808049cfce95b182910e50e38d3c39f4e5
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976605"
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

Video Indexer seleziona i frame che meglio rappresentano ogni scatto. I fotogrammi chiave sono i fotogrammi rappresentativi selezionati dall'intero video in base alle proprietà estetiche, ad esempio contrasto e stabilità. Video Indexer recupera un elenco di ID di fotogrammi chiave come parte dei metadati dell'immagine, in base ai clienti che possono estrarre il fotogramma chiave come immagine ad alta risoluzione.  

### <a name="extracting-keyframes"></a>Estrazione di fotogrammi chiave

Per estrarre fotogrammi chiave ad alta risoluzione per il video, è necessario innanzitutto caricare e indicizzare il video.

![KeyFrames](./media/scenes-shots-keyframes/extracting-keyframes.png)

#### <a name="with-the-video-indexer-website"></a>Con il sito Web Video Indexer

Per estrarre fotogrammi chiave tramite il sito Web Video Indexer, caricare e indicizzare il video. Al termine del processo di indicizzazione, fare clic sul pulsante **download** e selezionare **elementi (zip)** . In questo modo verrà scaricata la cartella artefatti nel computer. 

![KeyFrames](./media/scenes-shots-keyframes/extracting-keyframes2.png)
 
Decomprimere e aprire la cartella. Nella cartella *_KeyframeThumbnail* , sono disponibili tutti i fotogrammi chiave estratti dal video. 

#### <a name="with-the-video-indexer-api"></a>Con l'API Video Indexer

Per ottenere fotogrammi chiave usando l'API Video Indexer, caricare e indicizzare il video usando la chiamata [upload video](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Upload-Video?) . Al termine del processo di indicizzazione, chiamare [Get video index](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Index?). Ciò consentirà di ottenere tutte le informazioni che Video Indexer estratte dal contenuto in un file JSON.  

Si otterrà un elenco di ID del fotogramma chiave come parte dei metadati di ogni shot. 

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

A questo punto sarà necessario eseguire ognuno di questi ID di fotogrammi chiave nella chiamata [Get anteprime](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Thumbnail?) . Ogni immagine del fotogramma chiave verrà scaricata nel computer. 

## <a name="editorial-shot-type-detection"></a>Rilevamento del tipo di ripresa editoriale

I fotogrammi chiave sono associati a scatti nel codice JSON di output. 

Il tipo di tiro associato a un singolo scatto nel codice JSON di Insights rappresenta il tipo editoriale. Queste caratteristiche di tipo Shot possono risultare utili quando si modificano i video in clip, trailer o quando si cerca uno stile specifico di fotogramma chiave per scopi artistici. I diversi tipi vengono determinati in base all'analisi del primo fotogramma chiave di ogni scatto. Gli scatti sono identificati dalla scala, dalle dimensioni e dalla posizione delle facce visualizzate nel primo fotogramma chiave. 

Le dimensioni e la scala dei colpi vengono determinate in base alla distanza tra la fotocamera e le facce visualizzate nel frame. Utilizzando queste proprietà, Video Indexer rileva i tipi di shot seguenti:

* Wide: Mostra il corpo di un'intera persona.
* Media: Mostra la faccia e il corpo superiore di una persona.
* Close up: Mostra principalmente la faccia di una persona.
* Extreme close-up: Mostra la superficie di una persona che riempie lo schermo. 

I tipi di cattura possono anche essere determinati in base alla posizione dei caratteri soggetto rispetto al centro del frame. Questa proprietà definisce i tipi di cattura seguenti in Video Indexer:

* Left Face: una persona viene visualizzata sul lato sinistro del frame.
* Center Face: viene visualizzata una persona nell'area centrale del frame.
* Right Face: viene visualizzata una persona sul lato destro del frame.
* Outdoor: una persona viene visualizzata in un'impostazione esterna.
* Interno: una persona viene visualizzata in un'impostazione interna.

Caratteristiche aggiuntive:

* Due scatti: Mostra i visi di due persone di dimensioni medie.
* Più visi: più di due persone.


## <a name="next-steps"></a>Passaggi successivi

[Esaminare l'output del Video Indexer prodotto dall'API](video-indexer-output-json-v2.md#scenes)
