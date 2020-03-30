---
title: Risoluzione dei problemi dell'errore dell'hub IoT 409002 LinkCreationConflictTroubleshooting Azure IoT Hub error 409002 LinkCreationConflict
description: Informazioni su come correggere l'errore 409002 LinkCreationConflict
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 3c7515be573a0b74a39a77a91fbc554862c7f7aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960776"
---
# <a name="409002-linkcreationconflict"></a>409002 LinkCreationConflict

In questo articolo vengono descritte le cause e le soluzioni per gli errori **409002 LinkCreationConflict.**

## <a name="symptoms"></a>Sintomi

Viene visualizzato l'errore **409002 LinkCreationConflict** registrato nei log di diagnostica insieme alla disconnessione del dispositivo o errore del messaggio da cloud a dispositivo. 

<!-- When using AMQP? -->

## <a name="cause"></a>Causa

In genere, questo errore si verifica quando l'hub IoT rileva che un client dispone di più di una connessione. Infatti, quando arriva una nuova richiesta di connessione per un dispositivo con una connessione esistente, l'hub IoT chiude la connessione esistente con questo errore.

### <a name="cause-1"></a>Causa 1

Nel caso più comune, un problema separato (ad esempio [404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)) causa la disconnessione del dispositivo. Il dispositivo tenta di ristabilire immediatamente la connessione, ma l'hub IoT considera comunque il dispositivo connesso. L'hub IoT chiude la connessione precedente e registra questo errore.

### <a name="cause-2"></a>Causa 2

La logica lato dispositivo difettosa causa il dispositivo per stabilire la connessione quando è già aperto.

## <a name="solution"></a>Soluzione

In genere, questo errore si verifica come effetto collaterale di un altro problema temporaneo. Per risolvere il problema, cercare l'errore nei log. In alternativa, assicurarsi di generare una nuova richiesta di connessione solo se la connessione viene interrotta.
