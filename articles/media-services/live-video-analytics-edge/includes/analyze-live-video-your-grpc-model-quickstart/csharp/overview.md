---
ms.openlocfilehash: e96975bce5e5d99db4ea78c80c027a95d4b662e3
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92038557"
---
![Panoramica](../../../media/quickstarts/gRPC-extension.svg)

Il diagramma mostra il flusso dei segnali in questo argomento di avvio rapido. Un [modulo Edge](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simula una videocamera IP che ospita un server RTSP (Real-Time Streaming Protocol). Un nodo di [origine RTSP](../../../media-graph-concept.md#rtsp-source) estrae il feed video da questo server e invia i fotogrammi video al nodo del [processore di rilevamento del movimento](../../../media-graph-concept.md#motion-detection-processor). Questo processore rileverà il movimento e in caso di rilevamento effettuerà il push dei fotogrammi video al nodo del [processore di estensioni gRPC](../../../media-graph-concept.md#grpc-extension-processor).

Il nodo del processore di estensioni gRPC svolge il ruolo di proxy. Converte i fotogrammi video nel tipo di immagine specificato. Inoltra quindi l'immagine tramite gRPC a un altro modulo Edge che esegue un modello di intelligenza artificiale protetto da un endpoint gRPC tramite una [memoria condivisa](https://en.wikipedia.org/wiki/Shared_memory). In questo esempio, il modulo Edge viene creato usando il modello [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx), che è in grado di rilevare molti tipi di oggetti. Il nodo del processore di estensioni gRPC raccoglie i risultati del rilevamento e pubblica gli eventi nel nodo del [sink dell'hub IoT](../../../media-graph-concept.md#iot-hub-message-sink). Il nodo invia quindi gli eventi all'[hub di IoT Edge](../../../../../iot-edge/iot-edge-glossary.md#iot-edge-hub).

Contenuto dell'avvio rapido:

1. Creare e distribuire il grafico multimediale.
1. Interpretare i risultati.
1. Pulire le risorse.