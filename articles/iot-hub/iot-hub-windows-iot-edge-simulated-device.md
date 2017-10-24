---
title: Simulare un dispositivo con Azure IoT Edge (Windows) | Microsoft Docs
description: Come usare Azure IoT Edge in Windows per creare un dispositivo simulato che invia dati di telemetria attraverso un gateway Azure IoT Edge a un hub IoT.
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: 6a2aeda0-696a-4732-90e1-595d2e2fadc6
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2017
ms.author: andbuc
ms.openlocfilehash: 0aa1836ee1445894022b95fefc2338ef53698240
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-iot-edge-to-send-device-to-cloud-messages-with-a-simulated-device-windows"></a>Usare Azure IoT Edge per inviare messaggi da dispositivo a cloud con un dispositivo simulato (Windows)

[!INCLUDE [iot-hub-iot-edge-simulated-selector](../../includes/iot-hub-iot-edge-simulated-selector.md)]

[!INCLUDE [iot-hub-iot-edge-install-build-windows](../../includes/iot-hub-iot-edge-install-build-windows.md)]

## <a name="run-the-sample"></a>Eseguire l'esempio

Lo script **build.cmd** genera l'output nella cartella **build** nella copia locale dell'archivio **iot-edge**. Questo output include i quattro moduli di IoT Edge usati in questo esempio.

Lo script di compilazione crea i file seguenti:

* **logger.dll** nella cartella **build\\modules\\logger\\Debug**.
* **iothub.dll** nella cartella **build\\modules\\iothub\\Debug**.
* **identity\_map.dll** nella cartella **build\\modules\\identitymap\\Debug**.
* **simulated\_device.dll** nella cartella **build\\modules\\simulated\_device\\Debug**.

Usare questi percorsi per i valori **module path** come illustrato nel file di impostazioni JSON \_device\_cloud\_upload\_win.

Il processo simulated\_device\_cloud\_upload sample accetta il percorso di un file di configurazione JSON come argomento della riga di comando. Il file JSON di esempio seguente è disponibile nell'archivio SDK in **samples\\simulated\_device\_cloud\_upload\_sample\\src\\simulated\_device\_cloud\_upload\_win.json**. Questo file di configurazione funziona così com'è, a meno che non si modifichi lo script di compilazione per inserire moduli di IoT Edge o file eseguibili di esempio in percorsi non predefiniti.

> [!NOTE]
> I percorsi dei moduli sono relativi alla directory in cui si trova il file simulated\_device\_cloud\_upload\_sample.exe. Per impostazione predefinita, il file di configurazione JSON di esempio prevede la scrittura del file "deviceCloudUploadGatewaylog.log" nella directory di lavoro corrente.

In un editor di testo aprire il file **samples\\simulated\_device\_cloud\_upload\\src\\simulated\_device\_cloud\_upload\_win.json** nella copia locale dell'archivio **iot-edge**. Questo file consente di configurare i moduli IoT Edge nel gateway di esempio:

* Il modulo **IoTHub** si connette all'hub IoT. È necessario configurarlo per l'invio di dati all'hub IoT. In particolare, impostare il valore di **IoTHubName** sul nome dell'hub IoT e impostare il valore di **IoTHubSuffix** su **azure-devices.net**. Impostare il valore **Transport** su **HTTP**, **AMQP** o **MQTT**. Attualmente, solo **HTTP** condivide una connessione TCP per tutti i messaggi del dispositivo. Se si imposta il valore su **AMQP** o **MQTT**, il gateway mantiene una connessione TCP separata all'hub IoT per ogni dispositivo.
* Il modulo **mapping** esegue il mapping degli indirizzi MAC dei dispositivi simulati sugli ID dispositivo dell'hub IoT. Impostare i valori **deviceId** sugli ID dei due dispositivi aggiunti all'hub IoT. Impostare i valori **deviceKey** sulle chiavi dei due dispositivi.
* I moduli **BLE1** e **BLE2** sono i dispositivi simulati. Si noti come gli indirizzi del modulo MAC corrispondono a quelli nel modulo **mapping**.
* Il modulo **Logger** registra l'attività del gateway in un file.
* I valori **module path** illustrati nell'esempio seguente sono relativi alla directory in cui si trova il file simulated\_device\_cloud\_upload\_sample.exe.
* La matrice **links** nella parte inferiore del file JSON connette i moduli **BLE1** e **BLE2** al modulo **mapping** e il modulo **mapping** al modulo **IoTHub**. Inoltre, la matrice garantisce la registrazione di tutti i messaggi da parte del modulo **Logger** .

```json
{
    "modules" :
    [
      {
        "name": "IotHub",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\iothub\\Debug\\iothub.dll"
          }
          },
          "args": {
            "IoTHubName": "<<insert here IoTHubName>>",
            "IoTHubSuffix": "<<insert here IoTHubSuffix>>",
            "Transport": "HTTP"
          }
        },
      {
        "name": "mapping",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\identitymap\\Debug\\identity_map.dll"
          }
          },
          "args": [
            {
              "macAddress": "01:01:01:01:01:01",
              "deviceId": "<<insert here deviceId>>",
              "deviceKey": "<<insert here deviceKey>>"
            },
            {
              "macAddress": "02:02:02:02:02:02",
              "deviceId": "<<insert here deviceId>>",
              "deviceKey": "<<insert here deviceKey>>"
            }
          ]
        },
      {
        "name": "BLE1",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\simulated_device\\Debug\\simulated_device.dll"
          }
          },
          "args": {
            "macAddress": "01:01:01:01:01:01",
            "messagePeriod" : 2000
          }
        },
      {
        "name": "BLE2",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\simulated_device\\Debug\\simulated_device.dll"
          }
          },
          "args": {
            "macAddress": "02:02:02:02:02:02",
            "messagePeriod" : 2000
          }
        },
      {
        "name": "Logger",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\logger\\Debug\\logger.dll"
          }
        },
        "args": {
          "filename": "deviceCloudUploadGatewaylog.log"
        }
      }
    ],
    "links" : [
        { "source" : "*", "sink" : "Logger" },
        { "source" : "BLE1", "sink" : "mapping" },
        { "source" : "BLE2", "sink" : "mapping" },
        { "source" : "mapping", "sink" : "IotHub" }
    ]
}
```

Salvare le modifiche apportate al file di configurazione.

Per eseguire l'esempio:

1. Al prompt dei comandi accedere alla cartella **build** della copia locale dell'archivio **iot-edge**.
2. Eseguire il comando seguente:
   
    ```cmd
    samples\simulated_device_cloud_upload\Debug\simulated_device_cloud_upload_sample.exe ..\samples\simulated_device_cloud_upload\src\simulated_device_cloud_upload_win.json
    ```
3. È possibile usare lo strumento [Esplora dispositivi][lnk-device-explorer] o [iothub-explorer][lnk-iothub-explorer] per monitorare i messaggi che l'hub IoT riceve dal gateway. Ad esempio, tramite iothub-explorer è possibile monitorare i messaggi da dispositivo a cloud usando il comando seguente:

    ```cmd
    iothub-explorer monitor-events --login "HostName={Your iot hub name}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={Your IoT Hub key}"
    ```

## <a name="next-steps"></a>Passaggi successivi

Per ottenere informazioni più avanzate su IoT Edge e provare alcuni esempi di codice, vedere le risorse e le esercitazioni per gli sviluppatori elencate di seguito:

* [Inviare messaggi da dispositivo a cloud da un dispositivo fisico con IoT Edge][lnk-physical-device]
* [Azure IoT Edge][lnk-iot-edge]

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Guida per gli sviluppatori dell'hub IoT][lnk-devguide]
* [Proteggere la soluzione IoT sin dall'inizio][lnk-securing]

<!-- Links -->
[lnk-iot-edge]: https://github.com/Azure/iot-edge/
[lnk-physical-device]: iot-hub-iot-edge-physical-device.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-securing]: iot-hub-security-ground-up.md
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer
[lnk-iothub-explorer]: https://github.com/Azure/iothub-explorer/blob/master/readme.md
