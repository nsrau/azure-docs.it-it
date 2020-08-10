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
ms.openlocfilehash: d2ab93708950743eafdb6cf733273b602cfb5a68
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88038218"
---
# <a name="how-to-deploy-an-iot-central-application-using-the-video-analytics---object-and-motion-detection-application-template"></a>Come distribuire un'applicazione IoT Central usando il modello di applicazione video Analytics-oggetto e rilevamento movimento

Per una panoramica dei componenti principali dell'applicazione *analisi dei movimenti di oggetti e di rilevamento del movimento* , vedere l'articolo relativo all' [architettura delle applicazioni di analisi video di rilevamento di oggetti e movimento](architecture-video-analytics.md).

## <a name="deploy-the-application"></a>Distribuire l'applicazione

Completare i passaggi seguenti per distribuire un'applicazione IoT Central usando il modello applicazione di analisi video:

1. Completare l'esercitazione [creare un'applicazione di analisi video in Azure IOT Central](tutorial-video-analytics-create-app.md) per:
    - Creare un account di Servizi multimediali di Azure.
    - Creare l'applicazione IoT Central dal modello di applicazione video Analytics-oggetto e rilevamento movimento.
    - Configurare un dispositivo gateway nell'applicazione IoT Central. Il gateway consente ai dispositivi della fotocamera di connettersi all'applicazione.

1. Completare l'esercitazione [creare un'istanza di IOT Edge per l'analisi video (VM Linux)](tutorial-video-analytics-iot-edge-vm.md) per:
    - Creare una macchina virtuale di Azure con il runtime di Azure IoT Edge installato.-preparare l'installazione del IoT Edge per ospitare il modulo di analisi video.
    - Connettere il dispositivo IoT Edge all'applicazione IoT Central.

1. Completare l'esercitazione [monitorare e gestire un'applicazione di analisi video](tutorial-video-analytics-manage.md) per:
    - Aggiungere le fotocamere di rilevamento di oggetti e movimenti al gateway nell'applicazione IoT Central.
    - Avviare l'elaborazione della fotocamera.
    - Installare un lettore multimediale locale per visualizzare il video acquisito in AMS.
    - Visualizza il video acquisito che Mostra gli oggetti rilevati.
    - Riordina.

## <a name="next-steps"></a>Passaggi successivi

A questo punto è disponibile una panoramica dei passaggi per distribuire e usare il modello di applicazione di analisi video, vedere [creare un'applicazione video Analytics n in Azure IOT Central](tutorial-video-analytics-create-app.md) per iniziare.
