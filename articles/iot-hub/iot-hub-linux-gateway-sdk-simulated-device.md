---
title: Simulare un dispositivo con Azure IoT Gateway SDK (Linux) | Documentazione Microsoft
description: Come usare Azure IoT Gateway SDK in Linux per creare un dispositivo simulato che invia dati di telemetria attraverso un gateway a un hub IoT.
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: 11e7bf28-ee3d-48d6-a386-eb506c7a31cf
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2016
ms.author: andbuc
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: e2e814559282de3e5409e3215d824e1309debe5a


---
# <a name="use-the-azure-iot-gateway-sdk-to-send-device-to-cloud-messages-with-a-simulated-device-linux"></a>Usare Azure IoT Gateway SDK per inviare messaggi da dispositivo a cloud con un dispositivo simulato (Linux)
[!INCLUDE [iot-hub-gateway-sdk-simulated-selector](../../includes/iot-hub-gateway-sdk-simulated-selector.md)]

## <a name="build-and-run-the-sample"></a>Compilare ed eseguire l'esempio
Prima di iniziare:

* [Configurare l'ambiente di sviluppo][lnk-setupdevbox] per l'uso dell'SDK in Linux.
* [Creare un hub IoT][lnk-create-hub] nella sottoscrizione di Azure. Il nome dell'hub sarà necessario per completare questa procedura. Se non si ha un account, è possibile crearne uno [gratuito][lnk-free-trial] in pochi minuti.
* Aggiungere due dispositivi all'hub IoT e annotare i relativi ID e le chiavi di dispositivo. È possibile usare lo strumento [Esplora dispositivi o iothub-explorer][lnk-explorer-tools] per aggiungere i dispositivi all'hub IoT creato nel passaggio precedente e recuperarne le chiavi.

Per compilare l'esempio:

1. Aprire una shell.
2. Accedere alla directory principale nella copia locale del repository **azure-iot-gateway-sdk** .
3. Eseguire lo script **tools/build.sh --skip-unittests**. Questo script usa l'utilità **cmake** per creare una cartella denominata **build** nella directory principale della copia locale del repository **azure-iot-gateway-sdk** e generare un makefile. Lo script quindi compila la soluzione senza eseguire i test dell'unità. Rimuovere il parametro **--skip-unittests** se si desidare compilare ed eseguire i test dell'unità. 

> [!NOTE]
> Ogni volta che si esegue lo script **build.sh**, la cartella **build** viene eliminata e ricreata nella directory principale della copia locale del repository **azure-iot-gateway-sdk**.
> 
> 

Per eseguire l'esempio:

In un editor di testo aprire il file **samples/simulated_device_cloud_upload/src/simulated_device_cloud_upload_lin.json** nella copia locale del repository **azure-iot-gateway-sdk**. Questo file consente di configurare i moduli nel gateway di esempio:

* Il modulo **IoTHub** si connette all'hub IoT. È necessario configurarlo per l'invio di dati all'hub IoT. In particolare, impostare il valore di **IoTHubName** sul nome dell'hub IoT e impostare il valore di **IoTHubSuffix** su **azure-devices.net**. Impostare il valore **Trasporto** su "HTTP", "AMQP" o "MQTT". Notare che attualmente, solo "HTTP" consente di condividere una connessione TCP per tutti i messaggi del dispositivo. Se si imposta il valore "AMQP" o "MQTT", il gateway manterrà una connessione TCP separata all'hub IoT per ciascun dispositivo.
* Il modulo **mapping** esegue il mapping degli indirizzi MAC dei dispositivi simulati sugli ID dispositivo dell'hub IoT. Assicurarsi che i valori di **deviceId** corrispondano agli ID dei due dispositivi aggiunti all'hub IoT e che i valori di **deviceKey** contengano le chiavi dei due dispositivi.
* I moduli **BLE1** e **BLE2** sono i dispositivi simulati. Si noti come gli indirizzi MAC corrispondono a quelli nel modulo **mapping** .
* Il modulo **Logger** registra l'attività del gateway in un file.
* I valori di **module path** illustrati di seguito presuppongono che l'esempio venga eseguito dalla radice di una copia locale del repository **azure-iot-gateway-sdk**.
* La matrice **links** nella parte inferiore del file JSON connette i moduli **BLE1** e **BLE2** al modulo **mapping** e il modulo **mapping** al modulo **IoTHub**. Inoltre, la matrice garantisce la registrazione di tutti i messaggi da parte del modulo **Logger** .

```
{
    "modules": [
        {
            "name": "IotHub",
          "loader": {
            "name": "native",
            "entrypoint": {
              "module.path": "./modules/iothub/libiothub.so"
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
              "module.path": "./modules/identitymap/libidentity_map.so"
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
              "module.path": "./modules/simulated_device/libsimulated_device.so"
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
              "module.path": "./modules/simulated_device/libsimulated_device.so"
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
              "module.path": "./modules/logger/liblogger.so"
            }
            },
            "args": {
              "filename": "deviceCloudUploadGatewaylog.log"
            }
          }
    ],
    "links": [
        {
            "source": "*",
            "sink": "Logger"
        },
        {
            "source": "BLE1",
            "sink": "mapping"
        },
        {
            "source": "BLE2",
            "sink": "mapping"
        },
        {
            "source": "mapping",
            "sink": "IotHub"
        }
    ]
}
```

Salvare le modifiche apportate al file di configurazione.

Per eseguire l'esempio:

1. Nella shell accedere alla cartella **azure-iot-gateway-sdk/build**.
2. Eseguire il comando seguente:
   
    ```
    ./samples/simulated_device_cloud_upload/simulated_device_cloud_upload_sample ./../samples/simulated_device_cloud_upload/src/simulated_device_cloud_upload_lin.json
    ```
3. È possibile usare lo strumento [Esplora dispositivi o iothub-explorer][lnk-explorer-tools] per monitorare i messaggi che l'hub IoT riceve dal gateway.

## <a name="next-steps"></a>Passaggi successivi
Per ottenere informazioni più avanzate su IOT SDK per gateway e provare alcuni esempi di codice, vedere le seguenti risorse ed esercitazioni per gli sviluppatori:

* [Inviare messaggi da dispositivo a cloud da un dispositivo fisico con IoT Gateway SDK][lnk-physical-device]
* [Azure IoT Gateway SDK][lnk-gateway-sdk]

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Guida per gli sviluppatori dell'hub IoT][lnk-devguide]
* [Proteggere la soluzione IoT sin dall'inizio][lnk-securing]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/

[lnk-physical-device]: iot-hub-gateway-sdk-physical-device.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-securing]: iot-hub-security-ground-up.md
[lnk-create-hub]: iot-hub-create-through-portal.md



<!--HONumber=Dec16_HO2-->


