---
title: Risoluzione dei problemi dell'hub Azure Internet Error 409002 LinkCreationConflict
description: Informazioni su come correggere l'errore 409002 LinkCreationConflict
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom: amqp
ms.openlocfilehash: 20d39b1f5a11f20eb5d12f34337787b382c820f6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81758745"
---
# <a name="409002-linkcreationconflict"></a>409002 LinkCreationConflict

Questo articolo descrive le cause e le soluzioni per gli errori di **409002 LinkCreationConflict** .

## <a name="symptoms"></a>Sintomi

Viene visualizzato l'errore **409002 LinkCreationConflict** registrato nei log di diagnostica insieme alla disconnessione del dispositivo o a un errore del messaggio da cloud a dispositivo. 

<!-- When using AMQP? -->

## <a name="cause"></a>Causa

In genere, questo errore si verifica quando l'hub Internet rileva che un client ha più di una connessione. Infatti, quando arriva una nuova richiesta di connessione per un dispositivo con una connessione esistente, l'hub Internet chiude la connessione esistente con questo errore.

### <a name="cause-1"></a>Causa 1

Nel caso più comune, un problema separato, ad esempio [404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md), causa la disconnessione del dispositivo. Il dispositivo tenta di ristabilire immediatamente la connessione, ma l'hub Internet considera ancora il dispositivo connesso. L'hub IoT chiude la connessione precedente e registra questo errore.

### <a name="cause-2"></a>Causa 2

La logica del lato dispositivo non funzionante causa la connessione del dispositivo quando ne è già aperta una.

## <a name="solution"></a>Soluzione

In genere, questo errore si verifica come effetto collaterale di un altro problema temporaneo. Per risolvere il problema, cercare l'errore nei log. In alternativa, assicurarsi di generare una nuova richiesta di connessione solo se la connessione viene interrotta.
