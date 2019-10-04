---
title: Informazioni sulla messaggistica hub IoT di Azure | Microsoft Docs
description: Guida per gli sviluppatori - Messaggistica da dispositivo a cloud e da cloud a dispositivo con l'hub IoT. Include informazioni sui formati dei messaggi e sui protocolli di comunicazione supportati.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: f56332fa7f53c729ffaa28ea375f043d1b4a3678
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60626248"
---
# <a name="send-device-to-cloud-and-cloud-to-device-messages-with-iot-hub"></a>Inviare messaggi da dispositivo a cloud e da cloud a dispositivo con l'hub IoT

L'hub IoT offre comunicazioni bidirezionali sicure con i propri dispositivi. Usare l'hub IoT di messaggistica per comunicare con i propri dispositivi mediante l'invio di messaggi dai propri dispositivi alle soluzioni di back-end e l'invio di comandi dalle soluzioni IoT back-end ai propri dispositivi. Altre informazioni su [formato dei messaggi dell'hub IoT](iot-hub-devguide-messages-construct.md).

## <a name="sending-device-to-cloud-messages-to-iot-hub"></a>Inviare messaggi da dispositivo a cloud all'hub IoT

L'hub IoT dispone di un endpoint di servizio predefinito che può essere usato dai servizi back-end per leggere i messaggi di telemetria dai propri dispositivi. Questo endpoint è compatibile con [Hub eventi](https://docs.microsoft.com/azure/event-hubs/) ed è possibile usare gli SDK dell'hub IoT standard per [la lettura da questo endpoint predefinito](iot-hub-devguide-messages-read-builtin.md).

L'hub IoT supporta inoltre [endpoint personalizzati](iot-hub-devguide-endpoints.md#custom-endpoints) che possono essere definiti dagli utenti per l'invio di eventi e dati di telemetria a servizi di Azure usando [il routing dei messaggi](iot-hub-devguide-messages-d2c.md).

## <a name="sending-cloud-to-device-messages-from-iot-hub"></a>Inviare messaggi da cloud a dispositivo dall’hub IoT

È possibile inviare messaggi [da cloud a dispositivo](iot-hub-devguide-messages-c2d.md) dal back-end della soluzione ai dispositivi.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Le proprietà di base della funzionalità di messaggistica dell'hub IoT sono l'affidabilità e la durabilità dei messaggi. Queste proprietà consentono la resilienza in caso di connettività intermittente sul lato dispositivo e picchi di carico durante l'elaborazione degli eventi sul lato cloud. L'hub IoT implementa *almeno una volta* le garanzie di recapito per la messaggistica da dispositivo a cloud e da cloud a dispositivo.

## <a name="choosing-the-right-type-of-iot-hub-messaging"></a>Scelta del tipo corretto di messaggistica dell'hub IoT

Usare i messaggi da dispositivo a cloud per inviare la telemetria relativa alle serie temporali e gli avvisi dall'app del dispositivo e i messaggi da cloud a dispositivo per notifiche unidirezionali all'app del dispositivo.

* Vedere [Indicazioni sulle comunicazioni da dispositivo a cloud](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-d2c-guidance) per scegliere tra messaggi da dispositivo a cloud, proprietà segnalate o caricamento di file.

* Vedere [Indicazioni sulle comunicazioni da dispositivo a cloud](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-c2d-guidance) per scegliere tra messaggi da cloud a dispositivo, proprietà desiderate o metodi diretto.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sul [routing dei messaggi](iot-hub-devguide-messages-d2c.md) relativo all'hub IoT.

* Informazioni sulla [messaggistica da cloud a dispositivo](iot-hub-devguide-messages-c2d.md) relativa all'hub IoT.