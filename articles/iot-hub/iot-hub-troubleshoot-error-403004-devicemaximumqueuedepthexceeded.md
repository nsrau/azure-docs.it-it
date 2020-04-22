---
title: Risoluzione dei problemi dell'errore dell'hub IoT 403004 DeviceMaximumQueueDepthExceededTroubleshooting Azure IoT Hub error 403004 DeviceMaximumQueueDepthExceeded
description: Comprendere come risolvere l'errore 403004 DeviceMaximumQueueDepthExceeded
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 5cc8bae0f0245f5c4b45ca0cd446582b04788c21
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758762"
---
# <a name="403004-devicemaximumqueuedepthexceeded"></a>403004 DeviceMaximumQueueDepthExceeded

In questo articolo vengono descritte le cause e le soluzioni per gli errori **403004 DeviceMaximumQueueDepthExceeded.**

## <a name="symptoms"></a>Sintomi

Quando si tenta di inviare un messaggio cloud-to-device, la richiesta ha esito negativo con l'errore **403004** o **DeviceMaximumQueueDepthExceeded**.

## <a name="cause"></a>Causa

La causa sottostante è che il numero di messaggi accodati per il dispositivo supera il limite della [coda (50)](./iot-hub-devguide-quotas-throttling.md#other-limits).

Il motivo più probabile per cui si sta eseguendo questo limite è perché si utilizza `ReceiveAsync`HTTPS per ricevere il messaggio, che porta al polling continuo tramite , con conseguente limitazione della richiesta da parte dell'hub IoT.

## <a name="solution"></a>Soluzione

Il modello supportato per i messaggi da cloud a dispositivo con HTTPS è rappresentato da dispositivi collegati in modo intermittente che controllano raramente la presenza di messaggi (meno di ogni 25 minuti). Per ridurre la probabilità di incorrere nel limite della coda, passare a AMQP o MQTT per i messaggi da cloud a dispositivo.

In alternativa, migliorare la logica sul lato dispositivo per completare, rifiutare o abbandonare rapidamente i messaggi in coda, ridurre il tempo di permanità o considerare l'invio di un numero inferiore di messaggi. Vedere [Durata dei messaggi C2D](./iot-hub-devguide-messages-c2d.md#message-expiration-time-to-live).

Infine, è consigliabile utilizzare [l'API Purge Queue](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/purgecommandqueue) per pulire periodicamente i messaggi in sospeso prima che venga raggiunto il limite.