---
title: Risoluzione dei problemi dell'hub Azure Internet Error 429001 ThrottlingException
description: Informazioni su come correggere l'errore 429001 ThrottlingException
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: d2f12a6982886eeaa375151c5b8a73acc573aab9
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545362"
---
# <a name="429001-throttlingexception"></a>429001 ThrottlingException

Questo articolo descrive le cause e le soluzioni per gli errori di **429001 ThrottlingException** .

## <a name="symptoms"></a>Sintomi

Le richieste all'hub Internet non riescono con l'errore **429001 ThrottlingException** .

## <a name="cause"></a>Causa

Sono stati superati i [limiti di limitazione delle richieste](./iot-hub-devguide-quotas-throttling.md) dell'hub per l'operazione richiesta.

## <a name="solution"></a>Soluzione

Controllare se si sta raggiungendo il limite di limitazione confrontando la metrica dei *tentativi di invio del messaggio di telemetria* rispetto ai limiti specificati sopra. È anche possibile controllare il *numero di metriche degli errori di limitazione* . Per informazioni su queste metriche, vedere [metriche di telemetria del dispositivo](monitor-iot-hub-reference.md#device-telemetry-metrics). Per informazioni su come usare le metriche per monitorare l'hub Internet delle cose, vedere [monitorare l'hub](monitor-iot-hub.md).

L'hub tutto restituisce 429 ThrottlingException solo dopo che il limite è stato violato per un periodo troppo lungo. Questa operazione viene eseguita in modo che i messaggi non vengano eliminati se l'hub Internet delle cose raggiunge il traffico di espansione. Nel frattempo, l'hub IoT elabora i messaggi alla velocità di limitazione dell'operazione, che potrebbe essere lenta se è presente una quantità eccessiva di traffico nel backlog. Per altre informazioni, vedere [Traffic shaping dell'hub IoT](./iot-hub-devguide-quotas-throttling.md#traffic-shaping).

## <a name="next-steps"></a>Passaggi successivi

Prendere in considerazione [la scalabilità verticale dell'hub](./iot-hub-scaling.md) Internet delle cose in caso di limiti di quota o di limitazione delle richieste.