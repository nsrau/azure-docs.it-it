---
title: Risoluzione dei problemi dell'hub Azure Internet Error 404104 DeviceConnectionClosedRemotely
description: Informazioni su come correggere l'errore 404104 DeviceConnectionClosedRemotely
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom: mqtt
ms.openlocfilehash: c8cb91aa0c7ce1610320d4107db282d3c34407ba
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81758719"
---
# <a name="404104-deviceconnectionclosedremotely"></a>404104 DeviceConnectionClosedRemotely

Questo articolo descrive le cause e le soluzioni per gli errori di **404104 DeviceConnectionClosedRemotely** .

## <a name="symptoms"></a>Sintomi

### <a name="symptom-1"></a>Sintomo 1

I dispositivi si disconnettono a intervalli regolari (ad esempio, ogni 65 minuti) e vengono visualizzati **404104 DeviceConnectionClosedRemotely** nei log di diagnostica dell'hub Internet. In alcuni casi, vengono visualizzati anche **401003 IoTHubUnauthorized** e un evento di connessione del dispositivo riuscito meno di un minuto dopo.

### <a name="symptom-2"></a>Sintomo 2

I dispositivi si disconnettono in modo casuale e viene visualizzato **404104 DeviceConnectionClosedRemotely** nei log di diagnostica dell'hub Internet.

### <a name="symptom-3"></a>Sintomo 3

Molti dispositivi si disconnettono contemporaneamente, viene visualizzato un DIP nella [metrica dispositivi connessi](iot-hub-metrics.md)e sono presenti più **404104** [errori interni](iot-hub-troubleshoot-error-500xxx-internal-errors.md) DeviceConnectionClosedRemotely e 500xxx nei log di diagnostica del solito.

## <a name="causes"></a>Cause

### <a name="cause-1"></a>Causa 1

Il token di firma di accesso condiviso [usato per la connessione all'hub](iot-hub-devguide-security.md#security-tokens) delle cose è scaduto, che causa la disconnessione del dispositivo da parte dell'hub. La connessione viene ristabilita quando il token viene aggiornato dal dispositivo. Il token di firma di accesso condiviso, ad esempio, [scade ogni ora per impostazione predefinita per C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-authentication), che può causare disconnessioni regolari.

Per altre informazioni, vedere [401003 IoTHubUnauthorized cause](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#cause-1).

### <a name="cause-2"></a>Causa 2

Alcune possibilità includono:

- Il dispositivo ha perso la connettività di rete sottostante più a lungo del [MQTT Keep-Alive](iot-hub-mqtt-support.md#default-keep-alive-timeout), causando un timeout di inattività remoto. L'impostazione Keep-Alive MQTT può essere diversa per ogni dispositivo.

- Il dispositivo ha inviato una reimpostazione a livello di TCP/IP, ma non ha inviato un livello di applicazione `MQTT DISCONNECT` . Fondamentalmente, il dispositivo ha chiuso improvvisamente la connessione socket sottostante. Questo problema è talvolta causato da bug nelle versioni precedenti dell'SDK di Azure.

- Arresto anomalo dell'applicazione sul lato dispositivo.

### <a name="cause-3"></a>Causa 3

È possibile che si verifichi un problema temporaneo nell'hub Internet. Vedere l' [errore interno del server dell'hub](iot-hub-troubleshoot-error-500xxx-internal-errors.md#cause)Internet.

## <a name="solutions"></a>Soluzioni

### <a name="solution-1"></a>Soluzione 1

Vedere [401003 IoTHubUnauthorized Solution 1](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#solution-1)

### <a name="solution-2"></a>Soluzione 2

- Verificare che il dispositivo disponga di una connettività corretta all'hub di [Internet delle cose verificando la connessione](tutorial-connectivity.md). Se la rete è inaffidabile o intermittente, non è consigliabile aumentare il valore Keep-Alive perché potrebbe causare un rilevamento (tramite gli avvisi di monitoraggio di Azure, ad esempio) che richiede più tempo. 

- Usare le versioni più recenti degli [SDK](iot-hub-devguide-sdks.md)di Internet delle cose.

### <a name="solution-3"></a>Soluzione 3

Vedere le [soluzioni per gli errori interni del server dell'hub](iot-hub-troubleshoot-error-500xxx-internal-errors.md#solution)Internet.

## <a name="next-steps"></a>Passaggi successivi

È consigliabile usare gli SDK per dispositivi Azure per gestire le connessioni in modo affidabile. Per altre informazioni, vedere [gestire la connettività e la messaggistica affidabile usando gli SDK per dispositivi dell'hub Azure](iot-hub-reliability-features-in-sdks.md) .