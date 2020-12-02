---
title: Analizzare un video live con un modello HTTP personalizzato - Azure
description: Questa guida di avvio rapido illustra come applicare la visione artificiale all'analisi del feed video live proveniente da una videocamera IP simulata con un modello HTTP personalizzato.
ms.topic: quickstart
ms.date: 04/27/2020
zone_pivot_groups: ams-lva-edge-programming-languages
ms.openlocfilehash: 8e3f0a5cb6a97ce55d848c2d3311ebab5151e186
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96494988"
---
# <a name="quickstart-analyze-live-video-by-using-your-own-http-model"></a>Avvio rapido: Analizzare un video live con un modello HTTP personalizzato

Questo avvio rapido illustra come usare Analisi video live in IoT Edge per analizzare un feed video live proveniente da una videocamera IP simulata. Si vedrà come applicare un modello di visione artificiale per rilevare oggetti. Viene inviato un sottoinsieme di fotogrammi del feed video live a un servizio di inferenza. I risultati vengono inviati all'hub di IoT Edge. 

In questo argomento di avvio rapido si usano una VM di Azure come dispositivo IoT Edge e uno streaming di video live simulato. Si basa sul codice di esempio scritto in C# e si basa sull'argomento di avvio rapido [Rilevare il movimento e generare eventi](detect-motion-emit-events-quickstart.md). 

::: zone pivot="programming-language-csharp"
[!INCLUDE [header](includes/analyze-live-video-your-http-model-quickstart/csharp/header.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [header](includes/analyze-live-video-your-http-model-quickstart/python/header.md)]
::: zone-end

## <a name="prerequisites"></a>Prerequisiti

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/analyze-live-video-your-http-model-quickstart/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/analyze-live-video-your-http-model-quickstart/python/prerequisites.md)]
::: zone-end

## <a name="review-the-sample-video"></a>Esaminare il video di esempio

::: zone pivot="programming-language-csharp"
[!INCLUDE [review-sample-video](includes/analyze-live-video-your-http-model-quickstart/csharp/review-sample-video.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [review-sample-video](includes/analyze-live-video-your-http-model-quickstart/python/review-sample-video.md)]
::: zone-end

## <a name="overview"></a>Panoramica

::: zone pivot="programming-language-csharp"
[!INCLUDE [overview](includes/analyze-live-video-your-http-model-quickstart/csharp/overview.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [overview](includes/analyze-live-video-your-http-model-quickstart/python/overview.md)]
::: zone-end

## <a name="create-and-deploy-the-media-graph"></a>Creare e distribuire il grafico multimediale

::: zone pivot="programming-language-csharp"
[!INCLUDE [create-deploy-media-graph](includes/analyze-live-video-your-http-model-quickstart/csharp/create-deploy-media-graph.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [create-deploy-media-graph](includes/analyze-live-video-your-http-model-quickstart/python/create-deploy-media-graph.md)]
::: zone-end

## <a name="interpret-results"></a>Interpretare i risultati

::: zone pivot="programming-language-csharp"
[!INCLUDE [interpret-results](includes/analyze-live-video-your-http-model-quickstart/csharp/interpret-results.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [interpret-results](includes/analyze-live-video-your-http-model-quickstart/python/interpret-results.md)]
::: zone-end

## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende provare altri argomenti di avvio rapido, mantenere le risorse create. In caso contrario, passare al portale di Azure, passare ai gruppi di risorse, selezionare il gruppo di risorse in cui è stato eseguito l'argomento di avvio rapido ed eliminare tutte le risorse.

## <a name="next-steps"></a>Passaggi successivi

* Provare a eseguire una [versione protetta del modello YoloV3](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/tls-yolov3-onnx/readme.md) e distribuirla nel dispositivo di IoT Edge. 

Esaminare altri problemi per utenti avanzati:

* Usare una [videocamera IP](https://en.wikipedia.org/wiki/IP_camera) con supporto per RTSP invece del simulatore RTSP. È possibile cercare videocamere IP che supportano RTSP nella pagina dei prodotti [conformi a ONVIF](https://www.onvif.org/conformant-products/). Cercare dispositivi conformi ai profili G, S o T.
* Usare un dispositivo Linux AMD64 o x64 invece di una VM Linux di Azure. Il dispositivo deve trovarsi nella stessa rete della videocamera IP. È possibile seguire le istruzioni riportate in [Installare il runtime Azure IoT Edge in Linux](../../iot-edge/how-to-install-iot-edge.md). Quindi seguire il dispositivo con l'hub IoT di Azure seguendo le istruzioni riportate in [Distribuire il primo modulo IoT Edge in un dispositivo Linux virtuale](../../iot-edge/quickstart-linux.md).