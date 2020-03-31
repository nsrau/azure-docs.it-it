---
title: Risoluzione dei problemi dell'hub Di Azure IoT 429001 ThrottlingExceptionTroubleshooting Azure IoT Hub error 429001 ThrottlingException
description: Comprendere come risolvere l'errore 429001 ThrottlingException
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 3095e398d7e5cfe59085144d5bb4e8dc33618064
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960698"
---
# <a name="429001-throttlingexception"></a>429001 ThrottlingException

In questo articolo vengono descritte le cause e le soluzioni per gli errori **ThrottlingException 429001.**

## <a name="symptoms"></a>Sintomi

Le richieste all'hub IoT hanno esito negativo con l'errore **429001 ThrottlingException**.

## <a name="cause"></a>Causa

Sono stati superati i limiti di [limitazione](./iot-hub-devguide-quotas-throttling.md) dell'hub IoT per l'operazione richiesta.

## <a name="solution"></a>Soluzione

Verificare se si sta raggiungendo il limite di limitazione confrontando la metrica dei tentativi di invio del messaggio di *telemetria* con i limiti specificati in precedenza. È inoltre possibile controllare la metrica *Numero di errori di limitazione.* Per altre informazioni su queste e altre metriche disponibili per l'hub IoT, vedere [Metriche dell'hub IoT e come usarle.](./iot-hub-metrics.md#iot-hub-metrics-and-how-to-use-them)

Hub IoT restituisce 429 ThrottlingException solo dopo che il limite è stato violato per un periodo troppo lungo. Questa operazione viene eseguita in modo che i messaggi non vengano eliminati se l'hub IoT ottiene il traffico burst. Nel frattempo, l'hub IoT elabora i messaggi alla velocità di limitazione dell'operazione, che potrebbe essere lenta se è presente una quantità eccessiva di traffico nel backlog. Per altre informazioni, vedere [Traffic shaping dell'hub IoT](./iot-hub-devguide-quotas-throttling.md#traffic-shaping).

## <a name="next-steps"></a>Passaggi successivi

Valutare la possibilità di [aumentare l'hub IoT](./iot-hub-scaling.md) se si eseguono limiti di quota o di limitazione.