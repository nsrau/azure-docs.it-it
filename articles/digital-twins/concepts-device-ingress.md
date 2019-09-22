---
title: Connettività dei dispositivi e dati di telemetria in ingresso con Gemelli digitali di Azure | Microsoft Docs
description: Panoramica dell'onboarding di un dispositivo con Gemelli digitali di Azure
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: alinast
ms.openlocfilehash: 22ae7aeeff4542bee764e131f58eb115026a4fb3
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2019
ms.locfileid: "71177114"
---
# <a name="device-connectivity-and-telemetry-ingress"></a>Connettività dei dispositivi e dati di telemetria in ingresso

I dati di telemetria inviati da dispositivi e sensori formano la struttura portante di qualsiasi soluzione IoT. Rappresentare queste risorse disparate e gestirle nel contesto di una posizione rappresentano gli aspetti più problematici nello sviluppo di app IoT. Gemelli digitali di Azure semplifica il processo di sviluppo di soluzioni IoT combinando dispositivi e sensori con un grafico di intelligenza spaziale.

Per iniziare, creare una risorsa hub IoT di Azure alla radice del grafico spaziale. La risorsa hub IoT consente a tutti i dispositivi sotto lo spazio radice di inviare messaggi. Dopo la creazione dell'hub IoT, registrare i dispositivi con sensori all'interno dell'istanza di Gemelli digitali. I dispositivi possono inviare dati a un servizio Gemelli digitali tramite [Azure IoT SDK per dispositivi](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks).

Per una guida dettagliata all'onboarding dei dispositivi, vedere [Esercitazione: Distribuire Gemelli digitali di Azure e configurare un grafico spaziale](tutorial-facilities-setup.md). A grandi linee, i passaggi sono i seguenti:

- Distribuire un'istanza di Gemelli digitali di Azure dal [portale di Azure](https://portal.azure.com).
- Creare spazi nel grafico.
- Creare una risorsa hub IoT e assegnarla a uno spazio nel grafico.
- Creare dispositivi e sensori nel grafico e assegnarli agli spazi creati nei passaggi precedenti.
- Creare un matcher per filtrare i messaggi di telemetria in base a condizioni.
- Creare una [funzione definita dall'utente](concepts-user-defined-functions.md) e assegnarla a uno spazio nel grafico per l'elaborazione personalizzata dei messaggi di telemetria.
- Assegnare un ruolo per consentire alla funzione definita dall'utente di accedere ai dati del grafico.
- Ottenere la stringa di connessione del dispositivo hub IoT dalle API di gestione di Gemelli digitali.
- Configurare la stringa di connessione del dispositivo nel dispositivo con Azure IoT SDK per dispositivi.

Nelle sezioni seguenti viene descritto come ottenere la stringa di connessione del dispositivo hub IoT dall'API di gestione di Gemelli digitali. Viene illustrato anche come usare il formato dei messaggi di telemetria dell'hub IoT per inviare dati di telemetria basati su sensori. Il servizio Gemelli digitali richiede che ogni dato di telemetria che riceve sia associato a un sensore nel grafico spaziale. Ciò assicura che i dati vengano elaborati e instradati nel contesto spaziale appropriato.

## <a name="get-the-iot-hub-device-connection-string-from-the-management-api"></a>Ottenere la stringa di connessione del dispositivo hub IoT dall'API di gestione

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Eseguire una chiamata GET sull'API del dispositivo con un parametro `includes=ConnectionString` per ottenere la stringa di connessione del dispositivo hub IoT. Per trovare il dispositivo specifico, filtrare in base a GUID dispositivo o ID hardware.

```plaintext
YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_GUID?includes=ConnectionString
```

| Parametro | Sostituire con |
| --- | --- |
| *YOUR_DEVICE_GUID* | ID dispositivo |

```plaintext
YOUR_MANAGEMENT_API_URL/devices?HardwareIds=YOUR_DEVICE_HARDWARE_ID&includes=ConnectionString
```

| Valore del parametro | Sostituire con |
| --- | --- |
| *YOUR_DEVICE_HARDWARE_ID* | ID hardware del dispositivo |

Nel payload di risposta, copiare la proprietà **connectionString** del dispositivo. Questo valore verrà usato quando si chiama Azure IoT SDK per dispositivi per inviare dati a Gemelli digitali.

## <a name="device-to-cloud-message"></a>Messaggio da dispositivo a cloud

È possibile personalizzare il payload e il formato dei messaggii del dispositivo in base alle esigenze della soluzione. Usare qualsiasi contratto di dati che possa essere serializzato in una matrice di byte o in un flusso supportato dalla [classe dei messaggi del client del dispositivo Azure IoT, Message(byte[] byteArray)](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.-ctor?view=azure-dotnet#Microsoft_Azure_Devices_Client_Message__ctor_System_Byte___). Il messaggio può avere un formato binario personalizzato a scelta, a condizione che il contratto di dati venga decodificato in una funzione definita dall'utente corrispondente. Per un messaggio da dispositivo a cloud c'è un solo requisito. Mantenere un set di proprietà per assicurarsi che il messaggio venga indirizzato in modo appropriato al motore di elaborazione.

### <a name="telemetry-properties"></a>Proprietà di telemetria

 Il contenuto del payload di un **messaggio** può essere costituito da dati arbitrari con dimensioni massime di 256 KB. Per le proprietà del tipo [`Message.Properties`](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.properties?view=azure-dotnet) sono previsti alcuni requisiti. La tabella mostra le proprietà obbligatorie e facoltative supportate dal sistema.

| Nome proprietà | Value | Richiesto | Descrizione |
|---|---|---|---|
| **DigitalTwins-Telemetry** | 1.0 | Yes | Valore costante che identifica un messaggio al sistema. |
| **DigitalTwins-SensorHardwareId** | `string(72)` | Yes | Identificatore univoco del sensore che invia il **messaggio**. Questo valore deve corrispondere alla proprietà **HardwareId** di un oggetto affinché il sistema possa elaborarlo. Ad esempio `00FF0643BE88-CO2`. |
| **CreationTimeUtc** | `string` | No | Stringa di data in formato [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) che identifica l'ora di campionamento del payload. Ad esempio `2018-09-20T07:35:00.8587882-07:00`. |
| **CorrelationId** | `string` | No | UUID usato per tracciare gli eventi nel sistema. Ad esempio `cec16751-ab27-405d-8fe6-c68e1412ce1f`.

### <a name="send-your-message-to-digital-twins"></a>Inviare il messaggio a Gemelli digitali

Usare la chiamata [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventasync?view=azure-dotnet) o [SendEventBatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventbatchasync?view=azure-dotnet) DeviceClient per inviare il messaggio a Gemelli digitali.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulle funzionalità di elaborazione dati e sulle funzioni definite dall'utente del servizio Gemelli digitali di Azure, vedere [Elaborazione dati e funzioni definite dall'utente di Gemelli digitali di Azure](concepts-user-defined-functions.md).
