---
ms.openlocfilehash: fa0c2f5bb00122b40fb4f4ea06b7cf55c0248904
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "91025309"
---

![Panoramica](../../../media/quickstarts/gRPC-extension.svg)

Il diagramma mostra il flusso dei segnali in questo argomento di avvio rapido. Un [modulo Edge](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simula una videocamera IP che ospita un server RTSP (Real-Time Streaming Protocol). Un nodo di [origine RTSP](../../../media-graph-concept.md#rtsp-source) estrae il feed video da questo server e invia i fotogrammi video al nodo del [processore di rilevamento del movimento](../../../media-graph-concept.md#motion-detection-processor). Questo processore rileverà il movimento e in caso di rilevamento effettuerà il push dei fotogrammi video al nodo del [processore di estensioni gRPC](../../../media-graph-concept.md#grpc-extension-processor).

Il nodo del processore di estensioni gRPC svolge il ruolo di proxy. Converte i fotogrammi video nel tipo di immagine specificato. Inoltra quindi l'immagine tramite gRPC a un altro modulo Edge che esegue un modello di intelligenza artificiale protetto da un endpoint gRPC tramite una [memoria condivisa](https://en.wikipedia.org/wiki/Shared_memory). In questo esempio, il modulo Edge viene creato usando il modello [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx), che è in grado di rilevare molti tipi di oggetti. Il nodo del processore di estensioni gRPC raccoglie i risultati del rilevamento e pubblica gli eventi nel nodo del [sink dell'hub IoT](https://docs.microsoft.com/azure/media-services/live-video-analytics-edge/media-graph-concept#iot-hub-message-sink). Il nodo invia quindi gli eventi all'[hub di IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-glossary#iot-edge-hub).

Contenuto dell'avvio rapido:

1. Creare e distribuire il grafico multimediale.
1. Interpretare i risultati.
1. Pulire le risorse.
