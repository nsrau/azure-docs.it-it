---
title: Risoluzione dei problemi dell'hub IoT di Azure 404104 DeviceConnectionClosedRemotelyTroubleshooting Azure IoT Hub error 404104 DeviceConnectionClosedRemotely
description: Comprendere come risolvere l'errore 404104 DeviceConnectionClosedRemotely
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 686946b65b4b7540f404a291c87c5ad9c7b7a0ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960802"
---
# <a name="404104-deviceconnectionclosedremotely"></a>404104 DeviceConnectionClosedRemotely

In questo articolo vengono descritte le cause e le soluzioni per gli errori **404104 DeviceConnectionClosedRemotely.**

## <a name="symptoms"></a>Sintomi

### <a name="symptom-1"></a>Sintomo 1

I dispositivi si disconnettono a intervalli regolari (ogni 65 minuti, ad esempio) e viene visualizzato 404104 DeviceConnectionClosedRemotely nei log di diagnostica dell'hub IoT.Devices disconnect at a regular interval (every 65 minutes, for example) and you see **404104 DeviceConnectionClosedRemotely in** IoT Hub diagnostic logs. A volte, viene visualizzato anche **401003 IoTHubUnauthorized** e un evento di connessione del dispositivo riuscito meno di un minuto dopo.

### <a name="symptom-2"></a>Sintomo 2

I dispositivi si disconnettono in modo casuale e viene visualizzato **404104 DeviceConnectionClosedRemotely** nei log di diagnostica dell'hub IoT.

### <a name="symptom-3"></a>Sintomo 3

Molti dispositivi si disconnettono contemporaneamente, si vede un tuffo nella [metrica dei dispositivi connessi](iot-hub-metrics.md)e ci sono più **404104 DeviceConnectionClosedRemotely** e [500xxx Errori interni](iot-hub-troubleshoot-error-500xxx-internal-errors.md) nei log di diagnostica del solito.

## <a name="causes"></a>Cause

### <a name="cause-1"></a>Causa 1

Il [token di firma di accesso condiviso usato per connettersi all'hub IoT è scaduto,](iot-hub-devguide-security.md#security-tokens) pertanto l'hub IoT disconnette il dispositivo. La connessione viene ristabilita quando il token viene aggiornato dal dispositivo. Ad esempio, il token di firma di [accesso condiviso scade ogni ora per impostazione predefinita per C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-authentication), che può causare disconnessioni regolari.

Per ulteriori informazioni, vedere [401003 IoTHubUnauthorized cause](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#cause-1).

### <a name="cause-2"></a>Causa 2

Alcune possibilità includono:

- Il dispositivo ha perso la connettività di rete sottostante più a lungo del [keep-alive MQTT](iot-hub-mqtt-support.md#default-keep-alive-timeout), con conseguente timeout di inattività remota. L'impostazione keep-alive MQTT può essere diversa per dispositivo.

- Il dispositivo ha inviato un reset a livello di TCP/IP ma non ha inviato un'applicazione a livello `MQTT DISCONNECT`di applicazione. Fondamentalmente, il dispositivo ha chiuso bruscamente la connessione socket sottostante. In alcuni verità, questo problema è causato da bug nelle versioni precedenti di Azure IoT SDK.

- L'applicazione lato dispositivo si è arrestata in modo anomalo.

### <a name="cause-3"></a>Causa 3

L'hub IoT potrebbe riscontrare un problema temporaneo. Vedere [Errore del server interno dell'hub IoT causa la causa](iot-hub-troubleshoot-error-500xxx-internal-errors.md#cause).

## <a name="solutions"></a>Soluzioni

### <a name="solution-1"></a>Soluzione 1

Vedere [la soluzione 401003 IoTHubUnauthorized 1](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#solution-1)

### <a name="solution-2"></a>Soluzione 2

- Assicurarsi che il dispositivo disponga di una buona connettività all'hub IoT [testando la connessione.](tutorial-connectivity.md) Se la rete è inaffidabile o intermittente, non è consigliabile aumentare il valore keep-alive perché potrebbe causare l'esecuzione di un rilevamento (tramite avvisi di Monitoraggio di Azure), ad esempio). 

- Utilizzare le versioni più recenti degli [SDK IoT](iot-hub-devguide-sdks.md).

### <a name="solution-3"></a>Soluzione 3

Visualizzare le soluzioni agli errori interni del [server dell'hub IoT.](iot-hub-troubleshoot-error-500xxx-internal-errors.md#solution)

## <a name="next-steps"></a>Passaggi successivi

È consigliabile usare gli SDK per i dispositivi IoT di Azure per gestire le connessioni in modo affidabile. Per altre informazioni, vedere Gestire la connettività e la messaggistica affidabile usando gli SDK dei dispositivi hub IoT di AzureTo learn more, see [Manage connectivity and reliable messaging by using Azure IoT Hub device SDKs](iot-hub-reliability-features-in-sdks.md)