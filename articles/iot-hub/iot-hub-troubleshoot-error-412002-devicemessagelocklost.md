---
title: Risoluzione dei problemi dell'hub Di Azure IoT 412002 DeviceMessageLockLostTroubleshooting Azure IoT Hub error 412002 DeviceMessageLockLost
description: Informazioni su come correggere l'errore 412002 DeviceMessageLockLost
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 66461b23432a3e8b7ae4ad1fdc078fba9ca05646
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960763"
---
# <a name="412002-devicemessagelocklost"></a>412002 DeviceMessageLockLost

In questo articolo vengono descritte le cause e le soluzioni per gli errori **412002 DeviceMessageLockLost.**

## <a name="symptoms"></a>Sintomi

Quando si tenta di inviare un messaggio cloud-to-device, la richiesta ha esito negativo con l'errore **412002 DeviceMessageLockLost**.

## <a name="cause"></a>Causa

Quando un dispositivo riceve un messaggio da cloud a dispositivo [`ReceiveAsync()`](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet)dalla coda (ad esempio, utilizzando ) il messaggio viene bloccato dall'hub IoT per una durata di timeout di blocco di un minuto. Se il dispositivo tenta di completare il messaggio dopo la scadenza del timeout di blocco, l'hub IoT genera questa eccezione.

## <a name="solution"></a>Soluzione

Se l'hub IoT non ottiene la notifica entro la durata del timeout di blocco di un minuto, imposta il messaggio sullo stato *Enqueued.* Il dispositivo può tentare di ricevere nuovamente il messaggio. Per evitare che l'errore si verifichi in futuro, implementare la logica lato dispositivo per completare il messaggio entro un minuto dalla ricezione del messaggio. Questo timeout di un minuto non può essere modificato.