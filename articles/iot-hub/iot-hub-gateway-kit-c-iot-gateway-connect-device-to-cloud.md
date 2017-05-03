---
title: Usare un gateway IoT per connettere un dispositivo all&quot;hub IoT di Azure | Microsoft Docs
description: Informazioni su come usare Intel NUC come gateway IoT per connettere un SensorTag TI e inviare i dati del sensore all&quot;hub IoT di Azure nel cloud.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: connessione del dispositivo al cloud tramite gateway IoT
ms.assetid: cb851648-018c-4a7e-860f-b62ed3b493a5
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/06/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: e8efc3204512b7213c44a15c25b38287465975df
ms.lasthandoff: 04/13/2017


---
# <a name="use-iot-gateway-to-connect-things-to-the-cloud---sensortag-to-azure-iot-hub"></a>Usare il gateway IoT per connettere oggetti al cloud: SensorTag ad Azure IoT Hub

> [!NOTE]
> Prima di iniziare questa esercitazione, assicurarsi di aver completato [Configurare Intel NUC come gateway IoT di Azure](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md). In [Configurare Intel NUC come gateway IoT di Azure](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md) si configura il dispositivo Intel NUC come gateway IoT.

## <a name="what-you-will-learn"></a>Contenuto dell'esercitazione

Informazioni su come usare un gateway IoT per connettere un SensorTag di Texas Instruments (CC2650STK) ad Azure IoT Hub. Il gateway IoT invia dati di temperatura e umidità raccolti dal SensorTag ad Azure IoT Hub.

## <a name="what-you-will-do"></a>Contenuto dell'esercitazione

- Creare un hub IoT.
- Registrare un dispositivo nell'hub IoT per SensorTag.
- Abilitare la connessione tra il gateway IoT e il SensorTag.
- Eseguire un'applicazione di esempio BLE per inviare i dati del sensore SensorTag all'hub IoT.

## <a name="what-you-need"></a>Elementi necessari

- Completare l'esercitazione [Configurare Intel NUC come gateway IoT di Azure](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md) in cui si configura il dispositivo Intel NUC come gateway IoT.
- Un client SSH in esecuzione nel computer host. Si consiglia l'uso di PuTTY in Windows. Linux e macOS sono già dotati di un client SSH.
- L'indirizzo IP, il nome utente e la password per accedere al gateway dal client SSH.
- Una connessione Internet.

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

> [!NOTE]
> Qui è possibile registrare questo nuovo dispositivo per SensorTag

## <a name="enable-the-connection-between-the-iot-gateway-and-the-sensortag"></a>Abilitare la connessione tra il gateway IoT e SensorTag

In questa sezione vengono eseguite le attività seguenti:

- Ottenere l'indirizzo MAC di SensorTag per la connessione Bluetooth.
- Avviare una connessione Bluetooth dal gateway IoT per SensorTag.

### <a name="get-the-mac-address-of-the-sensortag-for-bluetooth-connection"></a>Ottenere l'indirizzo MAC di SensorTag per la connessione Bluetooth

1. Nel computer host eseguire il client SSH e connettersi al gateway IoT.
1. Sbloccare il Bluetooth eseguendo il comando seguente:

   ```bash
   sudo rfkill unblock bluetooth
   ```

1. Avviare il servizio Bluetooth sul gateway IoT e immettere una shell Bluetooth per configurare il Bluetooth eseguendo i comandi seguenti:

   ```bash
   sudo systemctl start bluetooth
   bluetoothctl
   ```

1. Accendere il controller Bluetooth eseguendo il comando seguente nella shell del Bluetooth:

   ```bash
   power on
   ```

   ![accendere il controller Bluetooth sul gateway IoT con bluetoothctl](./media/iot-hub-iot-gateway-connect-device-to-cloud/8_power-on-bluetooth-controller-at-bluetooth-shell-bluetoothctl.png)

1. Avviare l'analisi per dispositivi Bluetooth circostanti eseguendo il comando seguente:

   ```bash
   scan on
   ```

   ![Analizzare i dispositivi Bluetooth citcostanti con bluetoothctl](./media/iot-hub-iot-gateway-connect-device-to-cloud/9_start-scan-nearby-bluetooth-devices-at-bluetooth-shell-bluetoothctl.png)

1. Premere il pulsante di associazione su SensorTag. Il LED verde su SensorTag lampeggia.
1. Nella shell del Bluetooth viene visualizzato il SensorTag rilevato. Prendere nota dell'indirizzo MAC di SensorTag. In questo esempio l'indirizzo MAC di SensorTag è `24:71:89:C0:7F:82`.
1. Arrestare l'analisi eseguendo il comando seguente:

   ```bash
   scan off
   ```

   ![Interrompere l'analisi dei dispositivi Bluetooth circostanti con bluetoothctl](./media/iot-hub-iot-gateway-connect-device-to-cloud/10_stop-scanning-nearby-bluetooth-devices-at-bluetooth-shell-bluetoothctl.png)

### <a name="initiate-a-bluetooth-connection-from-the-iot-gateway-to-the-sensortag"></a>Avviare una connessione Bluetooth dal gateway IoT per SensorTag

1. Connettersi a SensorTag eseguendo il comando seguente:

   ```bash
   connect <MAC address>
   ```

   ![Connettersi a SensorTag con bluetoothctl](./media/iot-hub-iot-gateway-connect-device-to-cloud/11_connect-to-sensortag-at-bluetooth-shell-bluetoothctl.png)

1. Disconnettersi da SensorTag e chiudere la shell del Bluetooth eseguendo i comandi seguenti:

   ```bash
   disconnect
   exit
   ```

   ![Disconnettersi da SensorTag con bluetoothctl](./media/iot-hub-iot-gateway-connect-device-to-cloud/12_disconnect-from-sensortag-at-bluetooth-shell-bluetoothctl.png)

La connessione tra il gateway IoT e SensorTag è stata abilitata correttamente.

## <a name="run-a-ble-sample-application-to-send-sensortag-data-to-your-iot-hub"></a>Eseguire un'applicazione di esempio BLE per inviare i dati del sensore SensorTag all'hub IoT

L'applicazione di esempio Bluetooth a basso consumo (BLE) viene offerta dall'SDK del gateway IoT di Azure. L'applicazione di esempio raccoglie i dati dalla connessione BLE e li invia all'hub IoT. Per eseguire l'applicazione di esempio è necessario:

1. Configurare l'applicazione di esempio.
1. Eseguire l'applicazione di esempio nel gateway IoT.

### <a name="configure-the-sample-application"></a>Configurare l'applicazione di esempio

1. Passare alla cartella dell'applicazione di esempio eseguendo il comando seguente:

   ```bash
   cd /user/share/azureiotgatewaysdk/samples
   ```

1. Aprire il file di configurazione usando il comando seguente:

   ```bash
   vi ble_gateway.json
   ```

1. Nel file di configurazione, inserire i valori seguenti:

   **IoTHubName**: il nome dell'hub IoT.

   **IoTHubSuffix**: ottenere IoTHubSuffix dalla chiave primaria della stringa di connessione del dispositivo di cui si è preso nota. Assicurarsi di ottenere la chiave primaria della stringa di connessione del dispositivo, non la chiave primaria della stringa di connessione dell'hub IoT. La chiave primaria della stringa di connessione del dispositivo ha il formato `HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY`.

   **Transport**: il valore predefinito è `amqp`. Questo valore mostra il protocollo durante il trasporto. Potrebbe essere `http`, `amqp` o `mqtt`.

   **macAddress**: l'indirizzo MAC di SensorTag di cui si è preso nota.

   **deviceID**: ID del dispositivo creato nell'hub IoT.

   **deviceKey**: la chiave primaria della stringa di connessione del dispositivo.

   ![Completare il file di configurazione dell'applicazione di esempio BLE](./media/iot-hub-iot-gateway-connect-device-to-cloud/13_edit-config-file-of-ble-sample.png)

1. Premere `ESC` e digitare `:wq` per salvare il file.

### <a name="run-the-sample-application"></a>Eseguire l'applicazione di esempio

1. Assicurarsi che SensorTag sia acceso.
1. Eseguire il comando seguente:

   ```bash
   ./ble_gateway ble_gateway.json
   ```

## <a name="next-steps"></a>Passaggi successivi

[Usare il gateway IoT per la trasformazione dei dati del sensore con Azure IoT Gateway SDK](iot-hub-gateway-kit-c-use-iot-gateway-for-data-conversion.md)

