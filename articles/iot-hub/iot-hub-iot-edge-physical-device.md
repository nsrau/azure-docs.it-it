---
title: Usare un dispositivo fisico con Azure IoT Edge | Microsoft Docs
description: Come usare un dispositivo SensorTag di Texas Instruments per inviare dati a un hub IoT tramite un gateway IoT Edge in esecuzione su un dispositivo Raspberry Pi 3. Il gateway viene creato con Azure IoT Edge.
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
ms.date: 06/12/2017
ms.author: andbuc
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 02962a91c739a53dfcf947bcc736e5c293b9384f
ms.contentlocale: it-it
ms.lasthandoff: 06/26/2017


---
# <a name="use-azure-iot-edge-on-a-raspberry-pi-to-forward-device-to-cloud-messages-to-iot-hub"></a>Usare Azure IoT Edge in Raspberry Pi per inoltrare all'hub IoT messaggi da dispositivo a cloud

Questa procedura dettagliata relativa all'[esempio Bluetooth Low Energy][lnk-ble-samplecode] illustra come usare [Azure IoT Edge][lnk-sdk] per:

* Inoltrare all'hub IoT i dati di telemetria da dispositivo a cloud da un dispositivo fisico.
* Instradare i comandi dall'hub IoT a un dispositivo fisico.

In questa procedura dettagliata verranno trattati i seguenti argomenti:

* **Architettura**: informazioni importanti sull'architettura relative all'esempio Bluetooth Low Energy.
* **Compilazione ed esecuzione**: i passaggi richiesti per compilare ed eseguire l'esempio.

## <a name="architecture"></a>Architettura

La procedura dettagliata illustra come compilare ed eseguire un gateway IoT Edge in un modulo Raspberry Pi 3 che esegue Raspbian Linux. Il gateway viene creato usando IoT Edge. L'esempio usa un dispositivo SensorTag di Texas Instruments basato su tecnologia Bluetooth Low Energy (BLE) per raccogliere i dati sulla temperatura.

Quando viene eseguito, il gateway IoT Edge:

* Si connette a un dispositivo SensorTag usando il protocollo BLE.
* Si connette all'hub IoT usando il protocollo HTTP.
* Inoltra i dati di telemetria dal dispositivo SensorTag all'hub IoT.
* Instrada i comandi dall'hub IoT al dispositivo SensorTag.

Il gateway contiene i moduli IoT Edge seguenti:

* Un *modulo BLE* che si interfaccia con un dispositivo BLE per ricevere i dati sulla temperatura dal dispositivo e inviare comandi al dispositivo.
* Un *modulo BLE da cloud a dispositivo* che converte i messaggi JSON inviati dall'hub IoT in istruzioni BLE per il *modulo BLE*.
* Un *modulo logger* che registra tutti i messaggi del gateway in un file locale.
* Un *modulo di mapping delle identità* che esegue la conversione tra gli indirizzi MAC del dispositivo BLE e le identità dispositivo dell'hub IoT di Azure.
* Un *modulo dell'hub IoT* che carica i dati di telemetria in un hub IoT e riceve i comandi del dispositivo da un hub IoT.
* Un *modulo di stampa BLE* che interpreta i dati di telemetria dal dispositivo BLE e stampa i dati formattati sulla console per consentire la risoluzione dei problemi e il debug.

### <a name="how-data-flows-through-the-gateway"></a>Come avviene il flusso di dati attraverso il gateway

Il diagramma a blocchi seguente illustra la pipeline del flusso di caricamento dei dati di telemetria:

![Pipeline di caricamento dei dati di telemetria attraverso il gateway](media/iot-hub-iot-edge-physical-device/gateway_ble_upload_data_flow.png)

Il passaggio di un elemento di telemetria da un dispositivo BLE all'hub IoT prevede le fasi seguenti:

1. Il dispositivo BLE genera un esempio relativo alla temperatura e lo invia tramite Bluetooth al modulo BLE nel gateway.
1. Il modulo BLE riceve l'esempio e lo pubblica sul broker insieme all'indirizzo MAC del dispositivo.
1. Il modulo di mapping delle identità preleva il messaggio e usa una tabella interna per convertire l'indirizzo MAC del dispositivo in un'identità dispositivo dell'hub IoT. Un'identità dispositivo dell'hub IoT è costituita da un ID di dispositivo e una chiave del dispositivo.
1. Il modulo di mapping delle identità pubblica un nuovo messaggio contenente i dati di esempio relativi alla temperatura, nonché l'indirizzo MAC, l'ID e la chiave del dispositivo.
1. Il modulo dell'hub IoT riceve questo nuovo messaggio (generato dal modulo di mapping delle identità) e lo pubblica nell'hub IoT.
1. Il modulo logger registra tutti i messaggi provenienti dal broker in un file locale.

Il diagramma a blocchi seguente illustra la pipeline del flusso di invio dei comandi del dispositivo:

![Pipeline dell'invio dei comandi del dispositivo attraverso il gateway](media/iot-hub-iot-edge-physical-device/gateway_ble_command_data_flow.png)

1. Il modulo dell'hub IoT esegue periodicamente il polling dell'hub IoT per rilevare la presenza di nuovi messaggi di comando.
1. Quando il modulo dell'hub IoT riceve un nuovo messaggio di comando, lo pubblica sul broker.
1. Il modulo di mapping delle identità preleva il messaggio di comando e usa una tabella interna per convertire l'ID dispositivo dell'hub IoT in un indirizzo MAC di dispositivo, quindi pubblica un nuovo messaggio che include l'indirizzo MAC del dispositivo di destinazione nella mappa delle proprietà.
1. Il modulo BLE da cloud a dispositivo preleva il messaggio e lo converte nell'istruzione BLE appropriata per il modulo BLE. Dopodiché pubblica un nuovo messaggio.
1. Il modulo BLE preleva il messaggio ed esegue l'istruzione di I/O comunicando con il dispositivo BLE.
1. Il modulo logger registra tutti i messaggi provenienti dal broker in un file su disco.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, è necessaria una sottoscrizione di Azure attiva.

> [!NOTE]
> Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][lnk-free-trial].

È necessario un client SSH nel computer desktop per poter accedere in remoto alla riga di comando in Raspberry Pi.

- Windows non include un client SSH. È consigliabile usare [PuTTY](http://www.putty.org/).
- La maggior parte delle distribuzioni Linux e Mac OS includono l'utilità SSH della riga di comando. Per altre informazioni, vedere [SSH Using Linux or Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md) (SSH con Linux o Mac OS).

## <a name="prepare-your-hardware"></a>Preparare l'hardware

Questa esercitazione presuppone che si usi un dispositivo [SensorTag di Texas Instruments](http://www.ti.com/ww/en/wireless_connectivity/sensortag2015/index.html) connesso a un modulo Raspberry Pi 3 che esegue Raspbian.

### <a name="install-raspbian"></a>Installare Raspbian

È possibile usare una delle opzioni seguenti per installare Raspbian nel dispositivo Raspberry Pi 3.

* Per installare la versione più recente di Raspbian, usare l'interfaccia utente grafica [NOOBS][lnk-noobs].
* [Scaricare][lnk-raspbian] manualmente l'immagine più recente del sistema operativo Raspbian e scriverla in una scheda SD.

### <a name="sign-in-and-access-the-terminal"></a>Accedere al terminale

Esistono due opzioni per accedere all'ambiente di un terminale in Raspberry Pi:

* Se a Raspberry Pi sono connessi una tastiera e un monitor, è possibile usare l'interfaccia utente grafica di Raspbian per accedere a una finestra del terminale.

* Accedere alla riga di comando in Raspberry Pi usando SSH dal computer desktop.

#### <a name="use-a-terminal-window-in-the-gui"></a>Usare una finestra del terminale nell'interfaccia utente grafica

Le credenziali predefinite per Raspbian sono il nome utente **pi** e la password **raspberry**. Nella barra della applicazioni dell'interfaccia utente grafica è possibile avviare l'utilità **Terminal** (Terminale) usando l'icona del monitor.

#### <a name="sign-in-with-ssh"></a>Accedere con SSH

È possibile usare la riga di comando SSH per accedere a Raspberry Pi. L'articolo [SSH (Secure Shell)][lnk-pi-ssh] (SSH - Secure Shell) descrive come configurare SSH in Raspberry Pi e come connettersi da [Windows][lnk-ssh-windows] o [Linux e Mac OS][lnk-ssh-linux].

Accedere con il nome utente **pi** e la password **raspberry**.

### <a name="install-bluez-537"></a>Installare BlueZ 5.37

I moduli BLE comunicano con l'hardware Bluetooth tramite lo stack BlueZ. Per il corretto funzionamento dei moduli è necessaria la versione 5.37 di BlueZ. Queste istruzioni consentono di verificare che sia installata la versione corretta di BlueZ.

1. Arrestare il daemon Bluetooth corrente:

    ```sh
    sudo systemctl stop bluetooth
    ```

1. Installare le dipendenze BlueZ:

    ```sh
    sudo apt-get update
    sudo apt-get install bluetooth bluez-tools build-essential autoconf glib2.0 libglib2.0-dev libdbus-1-dev libudev-dev libical-dev libreadline-dev
    ```

1. Scaricare il codice sorgente BlueZ da bluez.org:

    ```sh
    wget http://www.kernel.org/pub/linux/bluetooth/bluez-5.37.tar.xz
    ```

1. Decomprimere il codice sorgente:

    ```sh
    tar -xvf bluez-5.37.tar.xz
    ```

1. Passare alla cartella appena creata:

    ```sh
    cd bluez-5.37
    ```

1. Configurare il codice BlueZ da compilare:

    ```sh
    ./configure --disable-udev --disable-systemd --enable-experimental
    ```

1. Compilare BlueZ:

    ```sh
    make
    ```

1. Al termine, installare BlueZ:

    ```sh
    sudo make install
    ```

1. Cambiare la configurazione del servizio systemd per bluetooth in modo che punti al nuovo daemon Bluetooth nel file `/lib/systemd/system/bluetooth.service`. Sostituire la riga 'ExecStart' con il testo seguente:

    ```conf
    ExecStart=/usr/local/libexec/bluetooth/bluetoothd -E
    ```

### <a name="enable-connectivity-to-the-sensortag-device-from-your-raspberry-pi-3-device"></a>Abilitare la connettività al dispositivo SensorTag dal dispositivo Raspberry Pi 3

Prima di eseguire l'esempio è necessario verificare che il dispositivo Raspberry Pi 3 possa connettersi al dispositivo SensorTag.

1. Assicurarsi che l'utilità `rfkill` sia installata:

    ```sh
    sudo apt-get install rfkill
    ```

1. Sbloccare Bluetooth sul dispositivo Raspberry Pi 3 e verificare che il numero di versione sia **5.37**:

    ```sh
    sudo rfkill unblock bluetooth
    bluetoothctl --version
    ```

1. Per accedere a una shell del Bluetooth interattiva, avviare il servizio Bluetooth ed eseguire il comando **bluetoothctl**:

    ```sh
    sudo systemctl start bluetooth
    bluetoothctl
    ```

1. Immettere il comando **power on** per accendere il controller bluetooth. L'output restituito dal comando è simile al seguente:

    ```sh
    [NEW] Controller 98:4F:EE:04:1F:DF C3 raspberrypi [default]
    ```

1. Nella shell Bluetooth interattiva immettere il comando **scan on** per cercare i dispositivi Bluetooth. L'output restituito dal comando è simile al seguente:

    ```sh
    Discovery started
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: yes
    ```

1. Rendere individuabile il dispositivo SensorTag premendo il pulsante piccolo (il LED verde dovrebbe lampeggiare). Il modulo Raspberry Pi 3 dovrebbe individuare il dispositivo SensorTag:

    ```sh
    [NEW] Device A0:E6:F8:B5:F6:00 CC2650 SensorTag
    [CHG] Device A0:E6:F8:B5:F6:00 TxPower: 0
    [CHG] Device A0:E6:F8:B5:F6:00 RSSI: -43
    ```

    In questo esempio è possibile notare che l'indirizzo MAC del dispositivo SensorTag è **A0:E6:F8:B5:F6:00**.

1. Disattivare la scansione immettendo il comando **scan off**:

    ```sh
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: no
    Discovery stopped
    ```

1. Connettersi al dispositivo SensorTag tramite il relativo indirizzo MAC immettendo**connect \<indirizzo MAC\>**. L'output di esempio seguente è abbreviato per maggiore chiarezza:

    ```sh
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

    > È possibile elencare di nuovo le caratteristiche GATT del dispositivo usando il comando **list-attributes**.

1. È ora possibile disconnettersi dal dispositivo usando il comando **disconnect** e quindi uscire dalla shell Bluetooth con il comando **quit**:

    ```sh
    Attempting to disconnect from A0:E6:F8:B5:F6:00
    Successful disconnected
    [CHG] Device A0:E6:F8:B5:F6:00 Connected: no
    ```

A questo punto si è pronti a eseguire l'esempio di IoT Edge BLE nel dispositivo Raspberry Pi 3.

## <a name="run-the-iot-edge-ble-sample"></a>Eseguire l'esempio di IoT Edge BLE

Per eseguire l'esempio IoT Edge BLE è necessario completare tre attività:

* Configurare due dispositivi di esempio nell'hub IoT.
* Compilare IoT Edge sul dispositivo Raspberry Pi 3.
* Configurare ed eseguire l'esempio BLE sul dispositivo Raspberry Pi 3.

Al momento della redazione di questo documento, IoT Edge supporta solo moduli BLE in gateway che vengono eseguiti in Linux.

### <a name="configure-two-sample-devices-in-your-iot-hub"></a>Configurare due dispositivi di esempio nell'hub IoT

* [Creare un hub IoT][lnk-create-hub] nella sottoscrizione di Azure. Per completare questa procedura, è necessario disporre del nome dell'hub. Se non si ha un account, è possibile crearne uno [gratuito][lnk-free-trial] in pochi minuti.
* Aggiungere un dispositivo chiamato **SensorTag_01** all'hub IoT e prendere nota del relativo ID e della chiave del dispositivo. È possibile usare lo strumento per [Esplora dispositivi o iothub-explorer][lnk-explorer-tools] per aggiungere il dispositivo all'hub IoT creato nel passaggio precedente e recuperarne la chiave. Il mapping del dispositivo al SensorTag avviene quando si configura il gateway.

### <a name="build-azure-iot-edge-on-your-raspberry-pi-3"></a>Compilare Azure IoT Edge su Raspberry Pi 3

Installare le dipendenze per Azure IoT Edge:

```sh
sudo apt-get install cmake uuid-dev curl libcurl4-openssl-dev libssl-dev
```

Usare i comandi seguenti per clonare IoT Edge e tutti i relativi moduli secondari nella directory home:

```sh
cd ~
git clone https://github.com/Azure/iot-edge.git
```

Quando si ha una copia completa del repository di IoT Edge nel dispositivo Raspberry Pi 3, è possibile procedere alla compilazione usando il comando seguente dalla cartella contenente l'SDK:

```sh
cd ~/iot-edge
./tools/build.sh  --disable-native-remote-modules
```

### <a name="configure-and-run-the-ble-sample-on-your-raspberry-pi-3"></a>Configurare ed eseguire l'esempio BLE sul dispositivo Raspberry Pi 3

Per avviare ed eseguire l'esempio, è necessario configurare ogni modulo IoT Edge coinvolto nell'attività del gateway. La configurazione viene definita in un file JSON ed è necessario configurare tutti e cinque i moduli IoT Edge coinvolti. Nell'archivio chiamato **gateway\_sample.json** è presente un file JSON di esempio che è possibile usare come base per creare il file di configurazione. Il file si trova nella cartella **samples/ble_gateway_hl/src** della copia locale del repository IoT Edge.

Le sezioni seguenti descrivono come modificare il file di configurazione per l'esempio BLE, supponendo che il repository IoT Edge si trovi nella cartella **/home/pi/iot-edge/** del dispositivo Raspberry Pi 3. Se l'archivio si trova in un'altra posizione, modificare i percorsi di conseguenza.

#### <a name="logger-configuration"></a>Configurazione del modulo logger

Se l'archivio del gateway si trova nella cartella **/home/pi/iot-edge/**, configurare il modulo logger nel modo seguente:

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

La configurazione di esempio per il dispositivo BLE presuppone che venga usato un dispositivo SensorTag di Texas Instruments. Dovrebbe essere possibile usare qualsiasi dispositivo BLE standard in grado di funzionare come periferica GATT, ma in tal caso sarà necessario aggiornare i dati e gli ID delle caratteristiche GATT. Aggiungere l'indirizzo MAC del dispositivo SensorTag:

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

Se non si usa un dispositivo SensorTag, esaminare la documentazione del dispositivo BLE per determinare se è necessario aggiornare i dati e gli ID delle caratteristiche GATT.

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
      "macAddress": "<<AA:BB:CC:DD:EE:FF>>",
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

La configurazione seguente garantisce che i moduli IoT Edge:

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

Per eseguire l'esempio, passare il percorso del file di configurazione JSON come parametro al codice binario **ble\_gateway**. Il comando seguente assume che si stia usano il file di configurazione **gateway_sample.json**. Eseguire questo comando dalla cartella **iot-edge** in Raspberry Pi:

```sh
./build/samples/ble_gateway/ble_gateway ./samples/ble_gateway/src/gateway_sample.json
```

Prima di eseguire l'esempio, potrebbe essere necessario premere il pulsante piccolo sul dispositivo SensorTag per renderlo individuabile.

Durante l'esecuzione dell'esempio è possibile usare lo strumento [Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) o [iothub-explorer](https://github.com/Azure/iothub-explorer) per monitorare i messaggi che il gateway IoT Edge inoltra dal dispositivo SensorTag. Ad esempio, tramite iothub-explorer è possibile monitorare i messaggi da dispositivo a cloud usando il comando seguente:

```sh
iothub-explorer monitor-events --login "HostName={Your iot hub name}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={Your IoT Hub key}"
```

## <a name="send-cloud-to-device-messages"></a>Inviare messaggi da cloud a dispositivo

Il modulo BLE supporta anche l'invio di comandi dall'hub IoT al dispositivo. È possibile usare lo strumento [Esplora dispositivi](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) o [iothub-explorer](https://github.com/Azure/iothub-explorer) per inviare i messaggi JSON che il modulo BLE del gateway inoltra al dispositivo BLE.

Se si usa il dispositivo SensorTag di Texas Instruments, è possibile attivare il LED rosso, il LED verde o il segnalatore acustico inviando comandi dall'hub IoT. Prima dell'invio dei comandi dall'hub IoT, occorre tuttavia inviare i due messaggi JSON seguenti nell'ordine indicato. Sarà quindi possibile inviare i comandi per attivare i LED o il segnalatore acustico.

1. Reimpostare tutti i LED e il segnalatore acustico (spegnerli):

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AA=="
    }
    ```

1. Configurare l'I/O come "remote":

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA66-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```

È possibile a questo punto inviare i comandi seguenti per attivare i LED o il segnalatore acustico sul dispositivo SensorTag:

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

Per ottenere informazioni più avanzate su IoT Edge e provare alcuni esempi di codice, vedere le seguenti risorse ed esercitazioni per gli sviluppatori:

* [Azure IoT Edge][lnk-sdk]

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Guida per gli sviluppatori dell'hub IoT][lnk-devguide]

<!-- Links -->
[lnk-ble-samplecode]: https://github.com/Azure/iot-edge/tree/master/samples/ble_gateway
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-sdk]: https://github.com/Azure/iot-edge/
[lnk-noobs]: https://www.raspberrypi.org/documentation/installation/noobs.md
[lnk-raspbian]: https://www.raspberrypi.org/downloads/raspbian/
[lnk-devguide]: iot-hub-devguide.md
[lnk-create-hub]: iot-hub-create-through-portal.md 
[lnk-pi-ssh]: https://www.raspberrypi.org/documentation/remote-access/ssh/README.md
[lnk-ssh-windows]: https://www.raspberrypi.org/documentation/remote-access/ssh/windows.md
[lnk-ssh-linux]: https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md

