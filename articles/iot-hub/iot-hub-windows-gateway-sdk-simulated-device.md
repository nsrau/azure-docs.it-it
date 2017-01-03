---
title: Simulare un dispositivo con IoT Gateway SDK | Documentazione Microsoft
description: Procedura dettagliata dell&quot;Azure IoT SDK per gateway con Windows che illustra l&quot;invio di dati di telemetria da un dispositivo simulato con Azure IoT SDK per gateway.
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
ms.date: 08/29/2016
ms.author: andbuc
translationtype: Human Translation
ms.sourcegitcommit: 00746fa67292fa6858980e364c88921d60b29460
ms.openlocfilehash: f5eeea933dbd5b63c6a8f2bd5b065a13286a4bae


---
# <a name="azure-iot-gateway-sdk--send-device-to-cloud-messages-with-a-simulated-device-app-using-windows"></a>Azure IoT Gateway SDK: inviare messaggi da dispositivo a cloud con un dispositivo simulato usando Windows
[!INCLUDE [iot-hub-gateway-sdk-simulated-selector](../../includes/iot-hub-gateway-sdk-simulated-selector.md)]

## <a name="build-and-run-the-sample"></a>Compilare ed eseguire l'esempio
Prima di iniziare:

* [Configurare l'ambiente di sviluppo][lnk-setupdevbox] per l'uso dell'SDK in Windows.
* [Creare un hub IoT][lnk-create-hub] nella sottoscrizione di Azure. Il nome dell'hub sarà necessario per completare questa procedura. Se non si ha un account, è possibile crearne uno [gratuito][lnk-free-trial] in pochi minuti.
* Aggiungere due dispositivi all'hub IoT e annotare i relativi ID e le chiavi di dispositivo. È possibile usare lo strumento [Esplora dispositivi o iothub-explorer][lnk-explorer-tools] per aggiungere i dispositivi all'hub IoT creato nel passaggio precedente e recuperarne le chiavi relative.

Per compilare l'esempio:

1. Aprire un **prompt dei comandi per gli sviluppatori per VS2015** .
2. Accedere alla directory principale nella copia locale del repository **azure-iot-gateway-sdk** .
3. Eseguire lo script **tools\\build.cmd**. Questo script crea un file di soluzione Visual Studio, compila la soluzione ed esegue i test. È possibile trovare la soluzione di Visual Studio nella cartella **build** nella copia locale del repository **azure-iot-gateway-sdk**.

Per eseguire l'esempio:

In un editor di testo aprire il file **samples\\simulated_device_cloud_upload\\src\\simulated_device_cloud_upload_win.json** nella copia locale del repository **azure-iot-gateway-sdk**. Questo file consente di configurare i moduli nel gateway di esempio:

* Il modulo **IoTHub** si connette all'hub IoT. È necessario configurarlo per l'invio di dati all'hub IoT. In particolare, impostare il valore di **IoTHubName** sul nome dell'hub IoT e impostare il valore di **IoTHubSuffix** su **azure-devices.net**. Impostare il valore **Trasporto** su "HTTP", "AMQP" o "MQTT". Notare che attualmente, solo "HTTP" consente di condividere una connessione TCP per tutti i messaggi del dispositivo. Se si imposta il valore "AMQP" o "MQTT", il gateway manterrà una connessione TCP separata all'hub IoT per ciascun dispositivo.
* Il modulo **mapping** esegue il mapping degli indirizzi MAC dei dispositivi simulati sugli ID dispositivo dell'hub IoT. Assicurarsi che i valori di **deviceId** corrispondano agli ID dei due dispositivi aggiunti all'hub IoT e che i valori di **deviceKey** contengano le chiavi dei due dispositivi.
* I moduli **BLE1** e **BLE2** sono i dispositivi simulati. Si noti come gli indirizzi MAC corrispondono a quelli nel modulo **mapping** .
* Il modulo **Logger** registra l'attività del gateway in un file.
* I valori di **module path** illustrati di seguito presuppongono che sia stato clonato il repository SDK per gateway IoT nella directory principale dell'unità **C:**. Se il repository è stato scaricato in un altro percorso, è necessario rettificare di conseguenza i valori di **module path** .
* La matrice **links** nella parte inferiore del file JSON connette i moduli **BLE1** e **BLE2** al modulo **mapping** e il modulo **mapping** al modulo **IoTHub**. Inoltre, la matrice garantisce la registrazione di tutti i messaggi da parte del modulo **Logger** .

```
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
            "macAddress": "01:01:01:01:01:01"
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
            "macAddress": "02:02:02:02:02:02"
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

1. Al prompt dei comandi, accedere alla directory principale della copia locale del repository **azure-iot-gateway-sdk** .
2. Eseguire il comando seguente:
   
    ```
    build\samples\simulated_device_cloud_upload\Debug\simulated_device_cloud_upload_sample.exe samples\simulated_device_cloud_upload\src\simulated_device_cloud_upload_win.json
    ```
3. È possibile usare lo strumento [Esplora dispositivi o iothub-explorer][lnk-explorer-tools] per monitorare i messaggi che l'hub IoT riceve dal gateway.

## <a name="next-steps"></a>Passaggi successivi
Per ottenere informazioni più avanzate su IOT SDK per gateway e provare alcuni esempi di codice, vedere le seguenti risorse ed esercitazioni per gli sviluppatori:

* [Inviare messaggi da dispositivo a cloud da un dispositivo fisico con IoT Gateway SDK][lnk-physical-device]
* [Azure IoT Gateway SDK][lnk-gateway-sdk]

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Guida per sviluppatori][lnk-devguide]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/

[lnk-physical-device]: iot-hub-gateway-sdk-physical-device.md

[lnk-devguide]: ./iot-hub-devguide.md
[lnk-create-hub]: iot-hub-create-through-portal.md 



<!--HONumber=Nov16_HO5-->


