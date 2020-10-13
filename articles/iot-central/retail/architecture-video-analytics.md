---
title: Oggetto e rilevamento del movimento di Azure IoT Central Video Analytics | Microsoft Docs
description: Informazioni su come creare un'applicazione IoT Central usando il modello applicazione video Analytics-oggetto e rilevamento movimento nel IoT Central. Questo modello USA analisi video in tempo reale e telecamere connesse.
author: KishorIoT
ms.author: nandab
ms.date: 07/27/2020
ms.topic: conceptual
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
ms.openlocfilehash: 916d491c45a2979c59580328a721c11bd79d49c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91372205"
---
# <a name="video-analytics---object-and-motion-detection-application-architecture"></a>Analisi video-architettura delle applicazioni di rilevamento di oggetti e movimenti

Il modello di applicazione **Video Analytics-oggetto e rilevamento movimento** consente di compilare soluzioni di Internet delle cose che includono funzionalità di analisi video in tempo reale.

:::image type="content" source="media/architecture-video-analytics/architecture.png" alt-text="Diagramma dei componenti di analisi video e di rilevamento di movimento.":::

I componenti principali della soluzione di analisi video includono:

## <a name="live-video-analytics-lva"></a>Analisi video in tempo reale (LVA)

LVA offre una piattaforma per la creazione di applicazioni video intelligenti che si estendono sul perimetro e sul cloud. La piattaforma consente di creare applicazioni video intelligenti che si estendono sul perimetro e sul cloud. La piattaforma offre la possibilità di acquisire, registrare e analizzare video live e di pubblicare i risultati, ovvero video o analisi video, nei servizi di Azure. I servizi di Azure possono essere in esecuzione sul cloud o nei dispositivi perimetrali. La piattaforma può essere usata per migliorare le soluzioni IoT grazie all'analisi video.

Per altre informazioni, vedere [analisi dei video in tempo reale](https://github.com/Azure/live-video-analytics) su GitHub.

## <a name="iot-edge-lva-gateway-module"></a>IoT Edge modulo Gateway LVA

Il modulo IoT Edge Gateway LVA crea un'istanza delle fotocamere come nuovi dispositivi e li connette direttamente ai IoT Central usando l'SDK client del dispositivo Internet.

In questa implementazione di riferimento, i dispositivi si connettono alla soluzione usando chiavi simmetriche dal perimetro. Per ulteriori informazioni sulla connettività dei dispositivi, vedere la pagina relativa [alla connessione ad Azure IOT Central](../core/concepts-get-connected.md)

## <a name="media-graph"></a>Grafico multimediale

Il grafico multimediale consente di definire il punto in cui acquisire i file multimediali, come elaborarli e dove recapitare i risultati. Il grafico multimediale viene configurato connettendo i componenti, o nodi, nel modo desiderato. Per altre informazioni, vedere [grafico multimediale](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph) su GitHub.

## <a name="next-steps"></a>Passaggi successivi

Il passaggio successivo suggerito consiste nell'apprendere come [creare un'applicazione di analisi video in Azure IOT Central](tutorial-video-analytics-create-app.md).
