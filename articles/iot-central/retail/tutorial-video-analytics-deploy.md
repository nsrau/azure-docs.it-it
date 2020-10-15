---
title: Come distribuire il modello di applicazione Azure IoT Central per il rilevamento di oggetti e movimenti di analisi video
description: Questa guida riepiloga i passaggi per distribuire un'applicazione IoT Central di Azure usando il modello di applicazione video Analytics-oggetto e rilevamento movimento.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: how-to
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: decfa7020be7778e8ca64a9fb0cb4aac1657da27
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91873337"
---
# <a name="how-to-deploy-an-iot-central-application-using-the-video-analytics---object-and-motion-detection-application-template"></a>Come distribuire un'applicazione IoT Central usando il modello di applicazione video Analytics-oggetto e rilevamento movimento

Per una panoramica dei componenti principali dell'applicazione *analisi dei movimenti di oggetti e di rilevamento del movimento* , vedere l'articolo relativo all' [architettura delle applicazioni di analisi video di rilevamento di oggetti e movimento](architecture-video-analytics.md).

Il video seguente offre una procedura dettagliata su come usare il _modello applicazione video Analytics-oggetto e rilevamento movimento_ per distribuire una soluzione IOT Central:

> [!VIDEO https://www.youtube.com/embed/Bo3FziU9bSA]

## <a name="deploy-the-application"></a>Distribuire l'applicazione

Completare i passaggi seguenti per distribuire un'applicazione IoT Central usando il modello applicazione di analisi video:

1. Completare l'esercitazione [creare un'applicazione di analisi video in azure IOT Central (Yolo V3)](tutorial-video-analytics-create-app-yolo-v3.md) o [creare un'analisi video in Azure IOT Central (OpenVINO &trade; )](tutorial-video-analytics-create-app-openvino.md) per:
    - Creare un account di Servizi multimediali di Azure.
    - Creare l'applicazione IoT Central dal modello di applicazione video Analytics-oggetto e rilevamento movimento.
    - Configurare un dispositivo gateway nell'applicazione IoT Central. Il gateway consente ai dispositivi della fotocamera di connettersi all'applicazione.

1. Completare l'esercitazione [creare un'istanza di IOT Edge per l'analisi video (macchina virtuale Linux)](tutorial-video-analytics-iot-edge-vm.md) o l' [esercitazione: creare un'istanza di IOT Edge per analisi video (Intel NUC)](tutorial-video-analytics-iot-edge-nuc.md) per:
    - Creare una macchina virtuale di Azure con il runtime di Azure IoT Edge installato.-preparare l'installazione del IoT Edge per ospitare il modulo di analisi video.
    - Connettere il dispositivo IoT Edge all'applicazione IoT Central.

1. Completare l'esercitazione [monitorare e gestire un'applicazione di analisi video](tutorial-video-analytics-manage.md) per:
    - Aggiungere le fotocamere di rilevamento di oggetti e movimenti al gateway nell'applicazione IoT Central.
    - Avviare l'elaborazione della fotocamera.
    - Installare un lettore multimediale locale per visualizzare il video acquisito in AMS.
    - Visualizza il video acquisito che Mostra gli oggetti rilevati.
    - Riordina.

## <a name="next-steps"></a>Passaggi successivi

A questo punto è disponibile una panoramica dei passaggi per distribuire e usare il modello di applicazione di analisi video, vedere [creare un'applicazione di analisi video in azure IOT Central (Yolo V3)](tutorial-video-analytics-create-app-yolo-v3.md) o [creare un'analisi video in Azure IOT Central (OpenVINO &trade; )](tutorial-video-analytics-create-app-openvino.md) per iniziare.
