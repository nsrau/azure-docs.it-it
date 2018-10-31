---
title: Connettività dei dispositivi e dati di telemetria in ingresso con Gemelli digitali di Azure | Microsoft Docs
description: Panoramica dell'onboarding di un dispositivo con il servizio Gemelli digitali di Azure
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: alinast
ms.openlocfilehash: 61d05a7e9936a0edd17c5528ce4f55233b6e7e0e
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323799"
---
# <a name="device-connectivity-and-telemetry-ingress"></a>Connettività dei dispositivi e dati di telemetria in ingresso

I dati di telemetria inviati da dispositivi e sensori formano la struttura portante di qualsiasi soluzione IoT. Di conseguenza, rappresentare queste risorse disparate e gestirle nel contesto di una posizione costituiscono una delle maggiori preoccupazioni nello sviluppo di app IoT. Il servizio Gemelli digitali di Azure semplifica lo sviluppo di soluzioni IoT combinando dispositivi e sensori con un grafico di intelligenza spaziale.

Per iniziare, è necessario creare una risorsa `IoTHub` alla radice del grafico spaziale, in modo da consentire a tutti i dispositivi sotto lo spazio della radice di inviare messaggi. Dopo aver creato l'hub IoT e aver registrato i dispositivi con i sensori nell'istanza del servizio Gemelli digitali, i dispositivi possono iniziare a inviare dati al servizio Gemelli digitali tramite [Azure IoT SDK per dispositivi](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks#azure-iot-device-sdks).

Una guida dettagliata per l'onboarding dei dispositivi è disponibile in [Esercitazione: Distribuire Gemelli digitali di Azure e configurare un grafico spaziale](tutorial-facilities-setup.md). A grandi linee, i passaggi sono i seguenti:

- Distribuire un'istanza di Gemelli digitali di Azure dal [portale di Azure](https://portal.azure.com)
- Creare spazi nel grafico
- Creare una risorsa `IoTHub` e assegnarla a uno spazio nel grafico
- Creare dispositivi e sensori nel grafico e assegnarli agli spazi creati nei passaggi precedenti
- Creare un matcher per filtrare i messaggi di telemetria in base a condizioni
- Creare una [**funzione definita dall'utente**](concepts-user-defined-functions.md) e assegnarla a uno spazio nel grafico per l'elaborazione personalizzata dei messaggi di telemetria
- Assegnare un ruolo per consentire alla funzione definita dall'utente di accedere ai dati del grafico
- Ottenere la stringa di connessione del dispositivo hub IoT dalle API di gestione di Gemelli digitali di Azure
- Configurare la stringa di connessione del dispositivo nel dispositivo con Azure IoT SDK per dispositivi

Di seguito viene illustrato come ottenere la stringa di connessione del dispositivo hub IoT dall'API di gestione di Gemelli digitali di Azure e come usare il formato dei messaggi di telemetria dell'hub IoT per inviare dati di telemetria basati su sensore. Il servizio Gemelli digitali richiede che ogni dato di telemetria che riceve sia associato a un sensore nel grafico spaziale per assicurare che i dati vengano elaborati e instradati nel contesto spaziale appropriato.

## <a name="get-the-iot-hub-device-connection-string-from-the-management-api"></a>Ottenere la stringa di connessione del dispositivo hub IoT dall'API di gestione

Eseguire una chiamata GET sull'API del dispositivo con un parametro `includes=ConnectionString` per ottenere la stringa di connessione del dispositivo hub IoT. Per trovare il dispositivo è possibile filtrare per GUID dispositivo o ID hardware:

```plaintext
https://yourManagementApiUrl/api/v1.0/devices/yourDeviceGuid?includes=ConnectionString
```

```plaintext
https://yourManagementApiUrl/api/v1.0/devices?hardwareIds=yourDeviceHardwareId&includes=ConnectionString
```

| Nome attributo personalizzato | Sostituire con |
| --- | --- |
| `yourManagementApiUrl` | Percorso URL completo per l'API Gestione |
| `yourDeviceGuid` | ID dispositivo |
| `yourDeviceHardwareId` | ID hardware del dispositivo |

Nel payload della risposta copiare la proprietà `connectionString` del dispositivo, che verrà usata nella chiamata di Azure IoT SDK per dispositivi per inviare dati a Gemelli digitali di Azure.

## <a name="device-to-cloud-message"></a>Messaggio da dispositivo a cloud

È possibile personalizzare il payload e il formato dei messaggii del dispositivo in base alle esigenze della soluzione. Si può usare qualsiasi contratto di dati che possa essere serializzato in una matrice di byte o in un flusso supportato dalla [classe dei messaggi del client del dispositivo Azure IoT, Message(byte[] byteArray)](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.-ctor?view=azure-dotnet#Microsoft_Azure_Devices_Client_Message__ctor_System_Byte___). Il messaggio può avere un formato binario personalizzato a scelta, purché il contratto di dati venga decodificato in una funzione definita dall'utente corrispondente. L'unico requisito per un messaggio da dispositivo a cloud è mantenere un set di proprietà (vedere di seguito) per assicurare il corretto routing del messaggio al motore di elaborazione.

### <a name="telemetry-properties"></a>Proprietà di telemetria

Massime il contenuto del payload di un `Message` può essere costituito da dati arbitrari con dimensioni massime di 256 KB, esistono alcuni requisiti per le [Message.Properties](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.properties?view=azure-dotnet) previste. I passaggi illustrati di seguito indicano le proprietà obbligatorie e facoltative supportate dal sistema:

| Nome proprietà | Valore | Obbligatoria | Descrizione |
|---|---|---|---|
| DigitalTwins-Telemetry | 1.0 | Sì | Valore costante che identifica un messaggio al sistema |
| DigitalTwins-SensorHardwareId | `string(72)` | Sì | Identificatore univoco del sensore che invia il `Message`. Questo valore deve corrispondere alla proprietà `HardwareId` di un oggetto affinché il sistema possa elaborarlo. Ad esempio: `00FF0643BE88-CO2` |
| CreationTimeUtc | `string` | no | Stringa di data in formato [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) che identifica l'ora del campionamento del payload. Ad esempio: `2018-09-20T07:35:00.8587882-07:00` |
| CorrelationId | `string` | no | `uuid` che può essere usato per tracciare gli eventi nel sistema. Ad esempio: `cec16751-ab27-405d-8fe6-c68e1412ce1f`

### <a name="sending-your-message-to-digital-twins"></a>Invio del messaggio a Gemelli digitali

Usare la chiamata DeviceClient [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventasync?view=azure-dotnet) o [SendEventBatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventbatchasync?view=azure-dotnet) per inviare il messaggio al servizio Gemelli digitali.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sulle funzionalità di elaborazione dati e delle funzioni definite dall'utente del servizio Gemelli digitali di Azure, vedere [Elaborazione dati e funzioni definite dall'utente](concepts-user-defined-functions.md).

