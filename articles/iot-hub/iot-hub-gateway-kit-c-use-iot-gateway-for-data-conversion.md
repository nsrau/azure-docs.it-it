---
title: Conversione di dati nel gateway IoT con Azure IoT Edge | Microsoft Docs
description: Usare il gateway IoT per convertire il formato dei dati del sensore tramite un modulo personalizzato di Azure IoT Edge.
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: conversione dei dati del gateway iot, trasformazione dei dati del gateway iot
ms.assetid: 75f2573d-500b-4405-bff7-61021c4c3500
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/25/2017
ms.author: xshi
ms.openlocfilehash: d5c735a4adbc59e9526ec4fd40720c5ec136d63d
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2017
---
# <a name="use-iot-gateway-for-sensor-data-transformation-with-azure-iot-edge"></a>Usare il gateway IoT per la trasformazione dei dati del sensore con Azure IoT Edge

> [!NOTE]
> Prima di iniziare questa esercitazione, assicurarsi di aver completato le lezioni seguenti in sequenza:
> * [Configurare Intel NUC come gateway IoT](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md)
> * [Use IoT gateway to connect things to the cloud - SensorTag to Azure IoT Hub](iot-hub-gateway-kit-c-iot-gateway-connect-device-to-cloud.md) (Usare il gateway IoT per connettere oggetti al cloud - SensorTag ad Azure IoT Hub)

Uno degli scopi di un gateway IoT consiste nell'elaborare i dati raccolti prima di inviarli al cloud. Azure IoT Edge introduce una serie di moduli che possono essere creati e assemblati per formare il flusso di lavoro dell'elaborazione dei dati. Un modulo riceve un messaggio, esegue un'azione su di esso e quindi lo passa ad altri moduli per l'elaborazione.

## <a name="what-you-learn"></a>Contenuto dell'esercitazione

Informazioni su come creare un modulo per convertire i messaggi del SensorTag in un formato diverso.

## <a name="what-you-do"></a>Operazioni da fare

* Creare un modulo per convertire nel formato JSON un messaggio ricevuto.
* Compilare il modulo.
* Aggiungere il modulo all'applicazione di esempio BLE da Azure IoT Edge.
* Eseguire l'applicazione di esempio.

## <a name="what-you-need"></a>Elementi necessari

* Le esercitazioni seguenti completate in sequenza:
  * [Configurare Intel NUC come gateway IoT](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md)
  * [Use IoT gateway to connect things to the cloud - SensorTag to Azure IoT Hub](iot-hub-gateway-kit-c-iot-gateway-connect-device-to-cloud.md) (Usare il gateway IoT per connettere oggetti al cloud - SensorTag ad Azure IoT Hub)
* Un client SSH in esecuzione nel computer host. Si consiglia l'uso di PuTTY in Windows. Linux e macOS sono già dotati di un client SSH.
* L'indirizzo IP, il nome utente e la password per accedere al gateway dal client SSH.
* Una connessione Internet.

## <a name="create-a-module"></a>Creare un modulo

1. Nel computer host eseguire il client SSH e connettersi al gateway IoT.
1. Clonare i file di origine del modulo di conversione da GitHub nella home directory del gateway IoT eseguendo i comandi seguenti:

   ```bash
   cd ~
   git clone https://github.com/Azure-Samples/iot-hub-c-intel-nuc-gateway-customized-module.git
   ```

   Questo è un modulo di Azure Edge nativo scritto nel linguaggio di programmazione C. Il modulo converte il formato dei messaggi ricevuti nel formato seguente:

   ```json
   {"deviceId": "Intel NUC Gateway", "messageId": 0, "temperature": 0.0}
   ```

## <a name="compile-the-module"></a>Compilare il modulo

Per compilare il modulo, eseguire i comandi seguenti:

```bash
cd iot-hub-c-intel-nuc-gateway-customized-module/my_module
# change the build script runnable
chmod 777 build.sh
# remove the invalid windows character
sed -i -e "s/\r$//" build.sh
# run the build shell script
./build.sh
```

Al termine della compilazione, viene creato un file `libmy_module.so`. Prendere nota del percorso assoluto del file.

## <a name="add-the-module-to-the-ble-sample-application"></a>Aggiungere il modulo all'applicazione di esempio BLE

1. Passare alla cartella degli esempi usando il comando seguente:

   ```bash
   cd /usr/share/azureiotgatewaysdk/samples
   ```

1. Aprire il file di configurazione usando il comando seguente:

   ```bash
   vi ble_gateway.json
   ```

1. Aggiungere un modulo inserendo il codice seguente nella sezione `modules`.

   ```json
   {
       "name": "MyModule",
       "loader": {
           "name": "native",
           "entrypoint":{
               "module.path": "[Your libmy_module.so path]"
               }
            },
        "args": null
    },
    ```

1. Sostituire `[Your libmy_module.so path]` nel codice con il percorso assoluto del file libmy_module.so.
1. Sostituire il codice nella sezione `links` con il codice seguente:

   ```json
   {
       "source": "SensorTag",
       "sink": "MyModule"
   },
   {
       "source": "MyModule",
       "sink": "mapping"
   }
   ```

1. Premere `ESC` e quindi digitare `:wq` per salvare il file.

## <a name="run-the-sample-application"></a>Eseguire l'applicazione di esempio

1. Accendere il SensorTag.
1. Impostare la variabile di ambiente SSL_CERT_FILE usando il comando seguente:

   ```bash
   export SSL_CERT_FILE=/etc/ssl/certs/ca-certificates.crt
   ```

1. Eseguire l'applicazione di esempio con il modulo aggiunto usando il comando seguente:

   ```bash
   ./ble_gateway ble_gateway.json
   ```

## <a name="next-steps"></a>Passaggi successivi

È stato usato il gateway IoT per convertire il messaggio dal SensorTag nel formato JSON.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
