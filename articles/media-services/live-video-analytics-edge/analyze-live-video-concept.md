---
title: Analisi di video live senza alcuna registrazione-Azure
description: È possibile usare un grafico multimediale per estrarre solo le analisi da un flusso video live, senza doverle registrare sul perimetro o nel cloud. In questo articolo viene illustrato questo concetto.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 29e00b9c04a652771ca150e2a45e980d20f8bc1f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84260184"
---
# <a name="analyzing-live-video-without-any-recording"></a>Analisi di video live senza registrazione

## <a name="suggested-pre-reading"></a>Letture consigliate 

* [Concetti relativi al grafo multimediale](media-graph-concept.md)
* [Registrazione di video basata su eventi](event-based-video-recording-concept.md)

## <a name="overview"></a>Panoramica  

È possibile usare un grafico multimediale per analizzare video in tempo reale, senza registrare parti del video in un file o in un asset. I grafici dei supporti mostrati di seguito sono simili a quelli nell'articolo sulla [registrazione video basata su eventi](event-based-video-recording-concept.md), ma senza un nodo sink di asset o un nodo di sink di file.

### <a name="motion-detection"></a>Rilevamento movimento

Il grafico multimediale riportato di seguito è costituito da un nodo di [origine RTSP](media-graph-concept.md#rtsp-source) , da un nodo del [processore di rilevamento del movimento](media-graph-concept.md#motion-detection-processor) e da un nodo del sink di messaggi dell' [Hub](media-graph-concept.md#iot-hub-message-sink) Internet. La rappresentazione JSON della topologia Graph di tale grafico multimediale è disponibile [qui](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/motion-detection/topology.json). Questo grafico consente di rilevare il movimento nel flusso video live in ingresso e di inoltrare gli eventi di movimento ad altre app e servizi tramite il nodo sink di messaggi dell'hub Internet. Le app o i servizi esterni possono attivare un avviso o inviare una notifica al personale appropriato.

![Analisi di video live basata su un rilevamento del movimento](./media/analyze-live-video/motion-detection.png)

### <a name="analyzing-video-using-a-custom-vision-model"></a>Analisi di video con un modello di visione artificiale personalizzato 

Il grafico multimediale riportato di seguito consente di analizzare un flusso video live usando un modello di visione artificiale personalizzato incluso in un modulo separato. La rappresentazione JSON della topologia Graph di tale grafico multimediale è disponibile [qui](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/topology.json). È possibile vedere alcuni esempi di wrapping di [modelli in IOT Edge](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis) moduli che vengono eseguiti come servizio di inferenza.

![Analisi video in tempo reale basata su un modulo esterno di inferenza](./media/analyze-live-video/external-inferencing-module.png)

In questo grafico multimediale, il nodo processore filtro frequenza frame abbassa la frequenza dei fotogrammi del flusso video live in ingresso prima di inviarlo a un nodo [processore di estensione http](media-graph-concept.md#http-extension-processor) , che invia frame immagine (in formati JPEG, BMP o png) a un servizio di inferenza esterno su REST. I risultati del servizio di inferenza esterno vengono recuperati dal nodo di estensione HTTP e inoltrati all'hub IoT Edge tramite il nodo del sink di messaggi dell'hub Internet. Questo tipo di grafico multimediale può essere usato per creare soluzioni per un'ampia gamma di scenari, ad esempio per comprendere la distribuzione di serie temporali di veicoli in un'intersezione, per comprendere il modello di traffico del cliente in un negozio al dettaglio e così via.

Un miglioramento di questo esempio prevede l'uso di un processore del rilevamento di movimento in avanti rispetto al nodo del processore del filtro della frequenza dei fotogrammi. Questo consente di ridurre il carico sul servizio di inferenza, perché viene usato solo quando è presente un'attività di movimento nel video.

![Analisi dei video in tempo reale basata su frame rilevati in movimento tramite un modulo esterno di inferenza](./media/analyze-live-video/motion-detected-frames.png)

## <a name="next-steps"></a>Passaggi successivi

[Registrazione continua di video](continuous-video-recording-concept.md)
