---
title: Usare un dispositivo fisico con Azure IoT Gateway SDK | Documentazione Microsoft
description: Come usare un dispositivo SensorTag di Texas Instruments per inviare dati a un hub IoT tramite un gateway in esecuzione su un dispositivo Raspberry Pi 3. Il gateway viene creato con Azure IoT Gateway SDK.
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: 212dacbf-e5e9-48b2-9c8a-1c14d9e7b913
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2016
ms.author: andbuc
translationtype: Human Translation
ms.sourcegitcommit: e1cf5ed3f2434a9e98027afd0225207ad5d2f1b1
ms.openlocfilehash: 199f07ce705036c3d8f9d56115b5df9c8c52dc45


---
# <a name="use-the-azure-iot-gateway-sdk-to-send-device-to-cloud-messages-with-a-physical-device-linux"></a>Usare Azure IoT Gateway SDK per inviare messaggi da dispositivo a cloud con un dispositivo fisico (Linux)
Questa procedura dettagliata relativa all'[esempio Bluetooth Low Energy][lnk-ble-samplecode] illustra come usare [Azure IoT Gateway SDK][lnk-sdk] per inoltrare all'hub IoT i dati di telemetria da dispositivo a cloud da un dispositivo fisico e come instradare i comandi dall'hub IoT a un dispositivo fisico.

In questa procedura dettagliata verranno trattati i seguenti argomenti:

* **Architettura**: informazioni importanti sull'architettura relative all'esempio Bluetooth Low Energy.
* **Compilazione ed esecuzione**: i passaggi richiesti per compilare ed eseguire l'esempio.

## <a name="architecture"></a>Architettura
La procedura dettagliata illustra come compilare ed eseguire un gateway IoT in un modulo Raspberry Pi 3 che esegue Raspbian Linux. Il gateway viene creato mediante IoT Gateway SDK. L'esempio usa un dispositivo SensorTag di Texas Instruments basato su tecnologia Bluetooth Low Energy (BLE) per raccogliere i dati sulla temperatura.

Quando viene eseguito, il gateway:

* Si connette a un dispositivo SensorTag usando il protocollo BLE.
* Si connette all'hub IoT usando il protocollo HTTP.
* Inoltra i dati di telemetria dal dispositivo SensorTag all'hub IoT.
* Instrada i comandi dall'hub IoT al dispositivo SensorTag.

Il gateway contiene i moduli seguenti:

* Un *modulo BLE* che si interfaccia con un dispositivo BLE per ricevere i dati sulla temperatura dal dispositivo e inviare comandi al dispositivo.
* Un *modulo BLE da cloud a dispositivo* che converte i messaggi JSON provenienti dal cloud in istruzioni BLE per il *modulo BLE*.
* Un *modulo logger* che registra tutti i messaggi del gateway in un file locale.
* Un *modulo di mapping delle identità* che esegue la conversione tra gli indirizzi MAC del dispositivo BLE e le identità dispositivo dell'hub IoT di Azure.
* Un *modulo dell'hub IoT* che carica i dati di telemetria in un hub IoT e riceve i comandi del dispositivo da un hub IoT.
* Un *modulo di stampa BLE* che interpreta i dati di telemetria dal dispositivo BLE e stampa i dati formattati sulla console per consentire la risoluzione dei problemi e il debug.

### <a name="how-data-flows-through-the-gateway"></a>Come avviene il flusso di dati attraverso il gateway
Il diagramma a blocchi seguente illustra la pipeline del flusso di caricamento dei dati di telemetria:

![](media/iot-hub-gateway-sdk-physical-device/gateway_ble_upload_data_flow.png)

Il passaggio di un elemento di telemetria da un dispositivo BLE all'hub IoT prevede le fasi seguenti:

1. Il dispositivo BLE genera un esempio relativo alla temperatura e lo invia tramite Bluetooth al modulo BLE nel gateway.
2. Il modulo BLE riceve l'esempio e lo pubblica sul broker insieme all'indirizzo MAC del dispositivo.
3. Il modulo di mapping delle identità preleva il messaggio e usa una tabella interna per convertire l'indirizzo MAC del dispositivo in un'identità dispositivo (ID e chiave del dispositivo) dell'hub IoT, quindi pubblica un nuovo messaggio contenente i dati dell'esempio relativo alla temperatura, nonché l'indirizzo MAC, l'ID e la chiave del dispositivo.
4. Il modulo dell'hub IoT riceve questo nuovo messaggio (generato dal modulo di mapping delle identità) e lo pubblica nell'hub IoT.
5. Il modulo logger registra tutti i messaggi provenienti dal broker in un file locale.

Il diagramma a blocchi seguente illustra la pipeline del flusso di invio dei comandi del dispositivo:

![](media/iot-hub-gateway-sdk-physical-device/gateway_ble_command_data_flow.png)

1. Il modulo dell'hub IoT esegue periodicamente il polling dell'hub IoT per rilevare la presenza di nuovi messaggi di comando.
2. Quando il modulo dell'hub IoT riceve un nuovo messaggio di comando, lo pubblica sul broker.
3. Il modulo di mapping delle identità preleva il messaggio di comando e usa una tabella interna per convertire l'ID dispositivo dell'hub IoT in un indirizzo MAC di dispositivo, quindi pubblica un nuovo messaggio che include l'indirizzo MAC del dispositivo di destinazione nella mappa delle proprietà.
4. Il modulo BLE da cloud a dispositivo preleva il messaggio e lo converte nell'istruzione BLE appropriata per il modulo BLE. Dopodiché pubblica un nuovo messaggio.
5. Il modulo BLE preleva il messaggio ed esegue l'istruzione di I/O comunicando con il dispositivo BLE.
6. Il modulo logger registra tutti i messaggi provenienti dal broker in un file su disco.

## <a name="prepare-your-hardware"></a>Preparare l'hardware
Questa esercitazione presuppone che si usi un dispositivo [SensorTag di Texas Instruments](http://www.ti.com/ww/en/wireless_connectivity/sensortag2015/index.html) connesso a un modulo Raspberry Pi 3 che esegue Raspbian.

### <a name="install-raspbian"></a>Installare Raspbian
È possibile usare una delle opzioni seguenti per installare Raspbian nel dispositivo Raspberry Pi 3. 

* Usare [NOOBS][lnk-noobs], un'interfaccia utente grafica, per installare la versione più recente di Raspbian. 
* [Scaricare][lnk-raspbian] manualmente e scrivere l'immagine più recente del sistema operativo Raspbian in una scheda SD. 

### <a name="install-bluez-537"></a>Installare BlueZ 5.37
I moduli BLE comunicano con l'hardware Bluetooth tramite lo stack BlueZ. Per il corretto funzionamento dei moduli è necessaria la versione 5.37 di BlueZ. Queste istruzioni consentono di verificare che sia installata la versione corretta di BlueZ.

1. Arrestare il daemon Bluetooth corrente:
   
    ```
    sudo systemctl stop bluetooth
    ```
2. Installare le dipendenze BlueZ. 
   
    ```
    sudo apt-get update
    sudo apt-get install bluetooth bluez-tools build-essential autoconf glib2.0 libglib2.0-dev libdbus-1-dev libudev-dev libical-dev libreadline-dev
    ```
3. Scaricare il codice sorgente BlueZ da bluez.org. 
   
    ```
    wget http://www.kernel.org/pub/linux/bluetooth/bluez-5.37.tar.xz
    ```
4. Decomprimere il codice sorgente.
   
    ```
    tar -xvf bluez-5.37.tar.xz
    ```
5. Passare alla cartella appena creata.
   
    ```
    cd bluez-5.37
    ```
6. Configurare il codice BlueZ da compilare.
   
    ```
    ./configure --disable-udev --disable-systemd --enable-experimental
    ```
7. Compilare BlueZ.
   
    ```
    make
    ```
8. Installare BlueZ al termine della compilazione.
   
    ```
    sudo make install
    ```
9. Cambiare la configurazione del servizio systemd per bluetooth in modo che punti al nuovo daemon Bluetooth nel file `/lib/systemd/system/bluetooth.service`. Sostituire la riga 'ExecStart' con il testo seguente: 
    
    ```
    ExecStart=/usr/local/libexec/bluetooth/bluetoothd -E
    ```

### <a name="enable-connectivity-to-the-sensortag-device-from-your-raspberry-pi-3-device"></a>Abilitare la connettività al dispositivo SensorTag dal dispositivo Raspberry Pi 3
Prima di eseguire l'esempio è necessario verificare che il dispositivo Raspberry Pi 3 possa connettersi al dispositivo SensorTag.


1. Assicurarsi che l'utilità `rfkill` sia installata.
   
    ```
    sudo apt-get install rfkill
    ```
2. Sbloccare Bluetooth sul dispositivo Raspberry Pi 3 e verificare che il numero di versione sia **5.37**.
   
    ```
    sudo rfkill unblock bluetooth
    bluetoothctl --version
    ```
3. Avviare il servizio Bluetooth ed eseguire il comando **bluetoothctl** per entrare in una shell Bluetooth interattiva. 
   
    ```
    sudo systemctl start bluetooth
    bluetoothctl
    ```
4. Immettere il comando **power on** per accendere il controller bluetooth. Verrà visualizzato un output simile al seguente:
   
    ```
    [NEW] Controller 98:4F:EE:04:1F:DF C3 raspberrypi [default]
    ```
5. Sempre nella shell interattiva bluetooth, immettere il comando **scan on** per cercare i dispositivi bluetooth. Verrà visualizzato un output simile al seguente:
   
    ```
    Discovery started
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: yes
    ```
6. Rendere individuabile il dispositivo SensorTag premendo il pulsante piccolo (il LED verde dovrebbe lampeggiare). Il modulo Raspberry Pi 3 dovrebbe individuare il dispositivo SensorTag:
   
    ```
    [NEW] Device A0:E6:F8:B5:F6:00 CC2650 SensorTag
    [CHG] Device A0:E6:F8:B5:F6:00 TxPower: 0
    [CHG] Device A0:E6:F8:B5:F6:00 RSSI: -43
    ```
   
    In questo esempio è possibile notare che l'indirizzo MAC del dispositivo SensorTag è **A0:E6:F8:B5:F6:00**.
7. Disattivare la scansione immettendo il comando **scan off** .
   
    ```
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: no
    Discovery stopped
    ```
8. Connettersi al dispositivo SensorTag tramite il relativo indirizzo MAC immettendo **connect \<indirizzo MAC>**. Si noti che l'output di esempio seguente è abbreviato:
   
    ```
    Attempting to connect to A0:E6:F8:B5:F6:00
    [CHG] Device A0:E6:F8:B5:F6:00 Connected: yes
    Connection successful
    [CHG] Device A0:E6:F8:B5:F6:00 UUIDs: 00001800-0000-1000-8000-00805f9b34fb
    ...
    [NEW] Primary Service
            /org/bluez/hci0/dev_A0_E6_F8_B5_F6_00/service000c
            Device Information
    ...
    [CHG] Device A0:E6:F8:B5:F6:00 GattServices: /org/bluez/hci0/dev_A0_E6_F8_B5_F6_00/service000c
    ...
    [CHG] Device A0:E6:F8:B5:F6:00 Name: SensorTag 2.0
    [CHG] Device A0:E6:F8:B5:F6:00 Alias: SensorTag 2.0
    [CHG] Device A0:E6:F8:B5:F6:00 Modalias: bluetooth:v000Dp0000d0110
    ```
   
    > Si noti che è possibile elencare di nuovo le caratteristiche GATT del dispositivo usando il comando **list-attributes**.
9. È ora possibile disconnettersi dal dispositivo usando il comando **disconnect** e quindi uscire dalla shell Bluetooth con il comando **quit**:
   
    ```
    Attempting to disconnect from A0:E6:F8:B5:F6:00
    Successful disconnected
    [CHG] Device A0:E6:F8:B5:F6:00 Connected: no
    ```

A questo punto si è pronti a eseguire l'esempio del gateway BLE nel dispositivo Raspberry Pi 3.

## <a name="run-the-ble-gateway-sample"></a>Eseguire l'esempio del gateway BLE
Per eseguire l'esempio BLE è necessario completare tre attività:

* Configurare due dispositivi di esempio nell'hub IoT.
* Compilare IoT Gateway SDK sul dispositivo Raspberry Pi 3.
* Configurare ed eseguire l'esempio BLE sul dispositivo Raspberry Pi 3.

Al momento della redazione di questo documento, IoT Gateway SDK supporta solo i gateway che usano moduli BLE su Linux.

### <a name="configure-two-sample-devices-in-your-iot-hub"></a>Configurare due dispositivi di esempio nell'hub IoT
* [Creare un hub IoT][lnk-create-hub] nella sottoscrizione di Azure. Il nome dell'hub sarà necessario per completare questa procedura. Se non si ha un account, è possibile crearne uno [gratuito][lnk-free-trial] in pochi minuti.
* Aggiungere un dispositivo chiamato **SensorTag_01** all'hub IoT e prendere nota del relativo ID e della chiave del dispositivo. È possibile usare lo strumento per [Esplora dispositivi o iothub-explorer][lnk-explorer-tools] per aggiungere il dispositivo all'hub IoT creato nel passaggio precedente e recuperarne la chiave. Questo dispositivo verrà mappato al dispositivo SensorTag al momento della configurazione del gateway.

### <a name="build-the-azure-iot-gateway-sdk-on-your-raspberry-pi-3"></a>Compilare Azure IoT Gateway SDK sul dispositivo Raspberry Pi 3

Installare le dipendenze per Azure IoT Gateway SDK.

``` 
sudo apt-get install cmake uuid-dev curl libcurl4-openssl-dev libssl-dev
```
Usare i comandi seguenti per clonare IoT Gateway SDK e tutti i relativi moduli secondari nella home directory:

```
cd ~
git clone --recursive https://github.com/Azure/azure-iot-gateway-sdk.git 
cd azure-iot-gateway-sdk
git submodule update --init --recursive
```

Quando si ha una copia completa del repository di IoT Gateway SDK nel dispositivo Raspberry Pi 3, è possibile procedere alla compilazione usando il comando seguente dalla cartella contenente l'SDK:

```
./tools/build.sh
```

### <a name="configure-and-run-the-ble-sample-on-your-raspberry-pi-3"></a>Configurare ed eseguire l'esempio BLE sul dispositivo Raspberry Pi 3
Per avviare ed eseguire l'esempio, è necessario configurare ogni modulo coinvolto nell'attività del gateway. Questa configurazione è definita in un file JSON ed è necessario configurare tutti e cinque i moduli coinvolti. Nel repository chiamato **gateway_sample.json** è presente un file JSON di esempio che è possibile usare come base per creare il file di configurazione. Il file si trova nella cartella **samples/ble_gateway_hl/src** della copia locale del repository IoT Gateway SDK.

Le sezioni seguenti descrivono come modificare il file di configurazione per l'esempio BLE, supponendo che il repository IoT Gateway SDK si trovi nella cartella **/home/pi/azure-iot-gateway-sdk/** del dispositivo Raspberry Pi 3. Se il repository si trova in un'altra posizione, è necessario modificare i percorsi di conseguenza:

#### <a name="logger-configuration"></a>Configurazione del modulo logger
Se il repository del gateway si trova nella cartella **/home/pi/azure-iot-gateway-sdk/**, configurare il modulo logger nel modo seguente:

```json
{
  "name": "Logger",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path" : "build/modules/logger/liblogger.so"
    }
  },
  "args":
  {
    "filename": "<</path/to/log-file.log>>"
  }
}
```

#### <a name="ble-module-configuration"></a>Configurazione del modulo BLE
La configurazione di esempio per il dispositivo BLE presuppone che venga usato un dispositivo SensorTag di Texas Instruments. Dovrebbe essere possibile usare qualsiasi dispositivo BLE standard in grado di funzionare come periferica GATT, ma in tal caso sarà necessario aggiornare i dati e gli ID delle caratteristiche GATT (per le istruzioni di scrittura). Aggiungere l'indirizzo MAC del dispositivo SensorTag: 

```json
{
  "name": "SensorTag",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/modules/ble/libble.so"
    }
  },
  "args": {
    "controller_index": 0,
    "device_mac_address": "<<AA:BB:CC:DD:EE:FF>>",
    "instructions": [
      {
        "type": "read_once",
        "characteristic_uuid": "00002A24-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A25-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A26-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A27-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A28-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A29-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "write_at_init",
        "characteristic_uuid": "F000AA02-0451-4000-B000-000000000000",
        "data": "AQ=="
      },
      {
        "type": "read_periodic",
        "characteristic_uuid": "F000AA01-0451-4000-B000-000000000000",
        "interval_in_ms": 1000
      },
      {
        "type": "write_at_exit",
        "characteristic_uuid": "F000AA02-0451-4000-B000-000000000000",
        "data": "AA=="
      }
    ]
  }
}
```

#### <a name="iot-hub-module"></a>modulo dell'hub IoT
Aggiungere il nome dell'hub IoT. Il valore del suffisso è in genere **azure-devices.net**:

```json
{
  "name": "IoTHub",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/modules/iothub/libiothub.so"
    }
  },
  "args": {
    "IoTHubName": "<<Azure IoT Hub Name>>",
    "IoTHubSuffix": "<<Azure IoT Hub Suffix>>",
    "Transport" : "amqp"
  }
}
```

#### <a name="identity-mapping-module-configuration"></a>Configurazione del modulo di mapping delle identità
Aggiungere l'indirizzo MAC del dispositivo SensorTag, nonché l'ID e la chiave del dispositivo **SensorTag_01** che è stato aggiunto all'hub IoT:

```json
{
  "name": "mapping",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/modules/identitymap/libidentity_map.so"
    }
  },
  "args": [
    {
      "macAddress": "AA:BB:CC:DD:EE:FF",
      "deviceId": "<<Azure IoT Hub Device ID>>",
      "deviceKey": "<<Azure IoT Hub Device Key>>"
    }
  ]
}
```

#### <a name="ble-printer-module-configuration"></a>Configurazione del modulo di stampa BLE
```json
{
  "name": "BLE Printer",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/samples/ble_gateway/ble_printer/libble_printer.so"
    }
  },
  "args": null
}
```

#### <a name="blec2d-module-configuration"></a>Configurazione del modulo BLEC2D
```json
{
  "name": "BLEC2D",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/modules/ble/libble_c2d.so"
    }
  },
  "args": null
}
```

#### <a name="routing-configuration"></a>Configurazione di routing
La configurazione seguente garantisce che:

* Il modulo **Logger** riceva e registri tutti i messaggi.
* Il modulo **SensorTag** invii i messaggi a entrambi i moduli **mapping** e **BLE Printer**.
* Il modulo **mapping** invii i messaggi al modulo **IoTHub** per farli recapitare all'hub IoT.
* Il modulo **IoTHub** invii di nuovo i messaggi al modulo **mapping**.
* Il modulo **mapping** invii i messaggi al modulo **BLEC2D**.
* Il modulo **BLEC2D** invii di nuovo i messaggi al modulo **SensorTag**.

```json
"links" : [
    {"source" : "*", "sink" : "Logger" },
    {"source" : "SensorTag", "sink" : "mapping" },
    {"source" : "SensorTag", "sink" : "BLE Printer" },
    {"source" : "mapping", "sink" : "IoTHub" },
    {"source" : "IoTHub", "sink" : "mapping" },
    {"source" : "mapping", "sink" : "BLEC2D" },
    {"source" : "BLEC2D", "sink" : "SensorTag"}
 ]
```

Per eseguire l'esempio, passare il percorso del file di configurazione JSON al codice binario **ble_gateway**. Se si è usato il file **gateway_sample.json**, il comando sarà il seguente. Eseguire questo comando dalla directory azure-iot-gateway-sdk

```
./build/samples/ble_gateway/ble_gateway ./samples/ble_gateway/src/gateway_sample.json
```

Prima di eseguire l'esempio, potrebbe essere necessario premere il pulsante piccolo sul dispositivo SensorTag per renderlo individuabile.

Durante l'esecuzione dell'esempio è possibile usare lo strumento [Esplora dispositivi o iothub-explorer][lnk-explorer-tools] per monitorare i messaggi che il gateway inoltra dal dispositivo SensorTag.

## <a name="send-cloud-to-device-messages"></a>Inviare messaggi da cloud a dispositivo
Il modulo BLE supporta anche l'invio di istruzioni dall'hub IoT di Azure al dispositivo. È possibile usare lo strumento [Esplora dispositivi](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) o [iothub-explorer](https://github.com/Azure/iothub-explorer) per inviare i messaggi JSON che il modulo BLE del gateway passa al dispositivo BLE.
Se si usa il dispositivo SensorTag di Texas Instruments, è possibile attivare il LED rosso, il LED verde o il segnalatore acustico inviando comandi dall'hub IoT. A tale scopo è prima necessario inviare i due messaggi JSON seguenti nell'ordine. Sarà quindi possibile inviare i comandi per attivare i LED o il segnalatore acustico.

1. Reimpostare tutti i LED e il segnalatore acustico (spegnerli):

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AA=="
    }
    ```
    
2. Configurare l'I/O come "remote":
  
    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA66-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```
    
Sarà quindi possibile inviare i comandi seguenti per attivare i LED o il segnalatore acustico.

* Accendere il LED rosso:
  
    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```

* Accendere il LED verde:
  
    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "Ag=="
    }
    ```

* Accendere il segnalatore acustico:
  
    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "BA=="
    }
    ```

## <a name="next-steps"></a>Passaggi successivi
Per ottenere informazioni più avanzate su IOT SDK per gateway e provare alcuni esempi di codice, vedere le seguenti risorse ed esercitazioni per gli sviluppatori:

* [Azure IoT Gateway SDK][lnk-sdk]

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Guida per gli sviluppatori dell'hub IoT][lnk-devguide]

<!-- Links -->
[lnk-ble-samplecode]: https://github.com/Azure/azure-iot-gateway-sdk/tree/master/samples/ble_gateway
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/
[lnk-noobs]: https://www.raspberrypi.org/documentation/installation/noobs.md
[lnk-raspbian]: https://www.raspberrypi.org/downloads/raspbian/


[lnk-devguide]: iot-hub-devguide.md
[lnk-create-hub]: iot-hub-create-through-portal.md 



<!--HONumber=Jan17_HO3-->


