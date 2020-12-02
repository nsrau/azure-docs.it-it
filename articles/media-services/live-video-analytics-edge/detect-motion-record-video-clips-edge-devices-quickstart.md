---
title: Rilevare il movimento e registrare video nei dispositivi perimetrali - Azure
description: Questo avvio rapido illustra come usare l'analisi di video live in IoT Edge per analizzare il feed video live da una videocamera IP (simulata), rilevare se è presente un movimento e, in tal caso, registrare un clip video MP4 nel file system locale del dispositivo perimetrale.
ms.topic: quickstart
ms.date: 04/27/2020
zone_pivot_groups: ams-lva-edge-programming-languages
ms.openlocfilehash: e4334cdd14242337a2a870f31886606020654685
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498354"
---
# <a name="quickstart-detect-motion-and-record-video-on-edge-devices"></a>Avvio rapido: Rilevare il movimento e registrare video nei dispositivi perimetrali
 
Questo argomento di avvio rapido illustra come usare Analisi video live in IoT Edge per analizzare un feed di video live proveniente da una videocamera IP simulata. Spiega come rilevare eventuali movimenti e, nel caso, registrare un clip video MP4 nel file system locale sul dispositivo perimetrale. Vengono usati una VM di Azure come dispositivo IoT Edge e uno streaming di video live simulato. 

::: zone pivot="programming-language-csharp"
[!INCLUDE [header](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/header.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [header](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/header.md)]
::: zone-end

## <a name="prerequisites"></a>Prerequisiti

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/prerequisites.md)]
::: zone-end

## <a name="review-the-sample-video"></a>Esaminare il video di esempio

::: zone pivot="programming-language-csharp"
[!INCLUDE [review-sample-video](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/review-sample-video.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [review-sample-video](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/review-sample-video.md)]
::: zone-end

## <a name="overview"></a>Panoramica

::: zone pivot="programming-language-csharp"
[!INCLUDE [overview](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/overview.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [overview](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/overview.md)]
::: zone-end

## <a name="examine-and-edit-the-sample-files"></a>Esaminare e modificare i file di esempio

::: zone pivot="programming-language-csharp"
[!INCLUDE [examine-edit-sample-files](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/examine-edit-sample-files.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [examine-edit-sample-files](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/examine-edit-sample-files.md)]
::: zone-end

## <a name="review---check-the-modules-status"></a>Revisione - Controllare lo stato dei moduli

::: zone pivot="programming-language-csharp"
[!INCLUDE [check-modules-status](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/check-modules-status.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [check-modules-status](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/check-modules-status.md)]
::: zone-end

## <a name="review---prepare-for-monitoring-events"></a>Revisione - Operazioni preliminari al monitoraggio degli eventi

::: zone pivot="programming-language-csharp"
[!INCLUDE [prepare-monitoring-events](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/prepare-monitoring-events.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prepare-monitoring-events](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/prepare-monitoring-events.md)]
::: zone-end

## <a name="run-the-sample-program"></a>Eseguire il programma di esempio

::: zone pivot="programming-language-csharp"
[!INCLUDE [run-sample-program](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/run-sample-program.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [run-sample-program](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/run-sample-program.md)]
::: zone-end

## <a name="interpret-results"></a>Interpretare i risultati 

::: zone pivot="programming-language-csharp"
[!INCLUDE [interpret-results](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/interpret-results.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [interpret-results](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/interpret-results.md)]
::: zone-end

## <a name="play-the-mp4-clip"></a>Riprodurre il clip MP4

::: zone pivot="programming-language-csharp"
[!INCLUDE [play-mp4-clip](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/play-mp4-clip.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [play-mp4-clip](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/play-mp4-clip.md)]
::: zone-end

## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende provare gli altri argomenti di avvio rapido, non eliminare le risorse create. In caso contrario, nel portale di Azure passare ai gruppi di risorse, selezionare il gruppo di risorse in cui è stato eseguito questo argomento di avvio rapido e quindi eliminare tutte le risorse.

## <a name="next-steps"></a>Passaggi successivi

* Seguire l'avvio rapido [Eseguire l'analisi di video live con un modello personalizzato](use-your-model-quickstart.md) per applicare l'intelligenza artificiale ai feed video live.
* Esaminare altri problemi per utenti avanzati:

    * Usare una [videocamera IP](https://en.wikipedia.org/wiki/IP_camera) che supporti RTSP invece di usare il simulatore RTSP. È possibile trovare videocamere IP che supportano RTSP nella pagina dei [prodotti conformi a ONVIF](https://www.onvif.org/conformant-products). Cercare dispositivi conformi ai profili G, S o T.
    * Usare un dispositivo Linux AMD64 o x64 invece di una macchina virtuale Linux in Azure. Il dispositivo deve trovarsi nella stessa rete della videocamera IP. Seguire le istruzioni riportate in [Installare il runtime Azure IoT Edge in Linux](../../iot-edge/how-to-install-iot-edge.md). Quindi seguire le istruzioni in [Distribuire il primo modulo IoT Edge in un dispositivo Linux virtuale](../../iot-edge/quickstart-linux.md) per registrare il dispositivo con l'hub IoT di Azure.