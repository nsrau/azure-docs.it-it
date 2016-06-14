<properties
	pageTitle="Usare un dispositivo reale con Gateway SDK | Microsoft Azure"
	description="Procedura dettagliata di Azure IoT Hub Gateway SDK che prevede l'uso di un dispositivo SensorTag di Texas Instruments per inviare dati all'hub IoT tramite un gateway in esecuzione su un modulo di calcolo Intel Edison"
	services="iot-hub"
	documentationCenter=""
	authors="chipalost"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="05/31/2016"
     ms.author="cstreet"/>


# IoT Gateway SDK (beta): inviare messaggi da dispositivo a cloud con un dispositivo reale usando Linux

Questa procedura dettagliata relativa all'[esempio Bluetooth Low Energy][lnk-ble-samplecode] illustra come usare [Microsoft Azure IoT Gateway SDK][lnk-sdk] per inoltrare all'hub IoT i dati di telemetria da dispositivo a cloud da un dispositivo fisico e come instradare i comandi dall'hub IoT a un dispositivo fisico.

In questa procedura dettagliata verranno trattati i seguenti argomenti:

* **Architettura**: informazioni importanti sull'architettura relative all'esempio Bluetooth Low Energy.

* **Compilazione ed esecuzione**: i passaggi richiesti per compilare ed eseguire l'esempio.

## Architettura

La procedura dettagliata illustra come compilare ed eseguire un gateway IoT in un modulo di calcolo Intel Edison che esegue Linux. Il gateway viene creato mediante IoT Gateway SDK. L'esempio usa un dispositivo SensorTag di Texas Instruments basato su tecnologia Bluetooth Low Energy (BLE) per raccogliere i dati sulla temperatura.

Quando viene eseguito, il gateway:

- Si connette a un dispositivo SensorTag usando il protocollo BLE.
- Si connette all'hub IoT usando il protocollo AMQP.
- Inoltra i dati di telemetria dal dispositivo SensorTag all'hub IoT.
- Instrada i comandi dall'hub IoT al dispositivo SensorTag.

Il gateway contiene i moduli seguenti:

- Un *modulo BLE* che si interfaccia con un dispositivo BLE per ricevere i dati sulla temperatura dal dispositivo e inviare comandi al dispositivo.
- Un *modulo logger* che genera i dati di diagnostica del bus di messaggi.
- Un *modulo di mapping delle identità* che esegue la conversione tra gli indirizzi MAC del dispositivo BLE e le identità dispositivo dell'hub IoT di Azure.
- Un *modulo HTTP dell'hub IoT* che carica i dati di telemetria in un hub IoT e riceve i comandi del dispositivo da un hub IoT.
- Un *modulo di stampa BLE* che interpreta i dati di telemetria dal dispositivo BLE e stampa i dati formattati sulla console per consentire la risoluzione dei problemi e il debug.

### Come avviene il flusso di dati attraverso il gateway

Il diagramma a blocchi seguente illustra la pipeline del flusso di caricamento dei dati di telemetria:

![](media/iot-hub-gateway-sdk-physical-device/gateway_ble_upload_data_flow.png)

Il passaggio di un elemento di telemetria da un dispositivo BLE all'hub IoT prevede le fasi seguenti:

1. Il dispositivo BLE genera un esempio relativo alla temperatura e lo invia tramite Bluetooth al modulo BLE nel gateway.
2. Il modulo BLE riceve l'esempio e lo pubblica nel bus di messaggi insieme all'indirizzo MAC del dispositivo.
3. Il modulo di mapping delle identità preleva il messaggio dal bus di messaggi e usa una tabella interna per convertire l'indirizzo MAC del dispositivo in un'identità dispositivo (ID e chiave del dispositivo) dell'hub IoT, quindi pubblica nel bus di messaggi un nuovo messaggio contenente i dati dell'esempio relativo alla temperatura, nonché l'indirizzo MAC, l'ID e la chiave del dispositivo.
4. Il modulo HTTP dell'hub IoT riceve questo nuovo messaggio (generato dal modulo di mapping delle identità) dal bus di messaggi e lo pubblica nell'hub IoT.
5. Il modulo logger registra tutti i messaggi provenienti dal bus di messaggi in un file su disco.

Il diagramma a blocchi seguente illustra la pipeline del flusso di invio dei comandi del dispositivo:

![](media/iot-hub-gateway-sdk-physical-device/gateway_ble_command_data_flow.png)

1. Il modulo HTTP dell'hub IoT esegue periodicamente il polling dell'hub IoT per rilevare la presenza di nuovi messaggi di comando.
2. Quando il modulo HTTP dell'hub IoT riceve un nuovo messaggio di comando, lo pubblica nel bus di messaggi.
3. Il modulo di mapping delle identità preleva il messaggio di comando dal bus di messaggi e usa una tabella interna per convertire l'ID dispositivo dell'hub IoT in un indirizzo MAC di dispositivo, quindi pubblica nel bus di messaggi un nuovo messaggio che include l'indirizzo MAC del dispositivo di destinazione nella mappa delle proprietà.
4. Il modulo BLE preleva il messaggio ed esegue l'istruzione di I/O comunicando con il dispositivo BLE.
5. Il modulo logger registra tutti i messaggi provenienti dal bus di messaggi in un file su disco.

## Preparare l'hardware

Questa esercitazione presuppone che si usi un dispositivo [SensorTag di Texas Instruments](http://www.ti.com/ww/en/wireless_connectivity/sensortag2015/index.html) connesso a una scheda Intel Edison.

### Configurare la scheda Edison

Prima di iniziare, verificare che sia possibile connettere il dispositivo Edison alla rete wireless. Per configurare il dispositivo Edison, è necessario connetterlo a un computer host. Sono disponibili guide introduttive di Intel per i sistemi operativi seguenti:

- [Get Started with the Intel Edison Development Board on Windows 64-bit][lnk-setup-win64] (Introduzione alla scheda di sviluppo Intel Edison su Windows a 64 bit)
- [Get Started with the Intel Edison Development Board on Windows 32-bit][lnk-setup-win32] (Introduzione alla scheda di sviluppo Intel Edison su Windows a 32 bit)
- [Get Started with the Intel Edison Development Board on Mac OS X][lnk-setup-osx] (Introduzione alla scheda di sviluppo Intel Edison su Mac OS X)
- [Getting Started with the Intel® Edison Board on Linux][lnk-setup-linux] (Introduzione alla scheda Intel® Edison su Linux)

Per configurare il dispositivo Edison e acquisire familiarità con tale dispositivo, è consigliabile eseguire tutti i passaggi descritti in questi articoli introduttivi tranne l'ultimo, relativo alla scelta dell'IDE, che non è necessario ai fini dell'esercitazione corrente. Al termine della procedura di configurazione del dispositivo Edison, l'utente avrà:

- Aggiornato il dispositivo Edison con il firmware più recente.
- Stabilito una connessione seriale dall'host al dispositivo Edison.
- Eseguito lo script **configure\_edison** per impostare una password e abilitare la connettività Wi-Fi sul dispositivo Edison.

### Abilitare la connettività al dispositivo SensorTag dalla scheda Edison

Prima di eseguire l'esempio, è necessario verificare che la scheda Edison sia in grado di connettersi al dispositivo SensorTag.

È necessario innanzitutto aggiornare la versione del software BlueZ sulla scheda Edison. Anche se è già installata la versione 5.37, è necessario eseguire i passaggi seguenti per assicurarsi che l'installazione sia completata:

1. Arrestare il daemon bluetooth attualmente in esecuzione.
    
    ```
    systemctl stop bluetooth
    ```

2. Scaricare ed estrarre il [codice sorgente](http://www.kernel.org/pub/linux/bluetooth/bluez-5.37.tar.xz) per la versione 5.37 di BlueZ.
    
    ```
    wget http://www.kernel.org/pub/linux/bluetooth/bluez-5.37.tar.xz
    tar -xvf bluez-5.37.tar.xz
    cd bluez-5.37
    ```

3. Compilare e installare BlueZ.
    
    ```
    ./configure --disable-udev --disable-systemd --enable-experimental
    make
    make install
    ```

4. Cambiare la configurazione del servizio *systemd* per bluetooth in modo che punti al nuovo daemon bluetooth modificando il file **/lib/systemd/system/bluetooth.service**. Sostituire il valore dell'attributo **ExecStart** in modo che risulti simile al seguente:
    
    ```
    ExecStart=/usr/local/libexec/bluetooth/bluetoothd -E
    ```

5. Riavviare la scheda Edison.

A questo punto è necessario verificare che la scheda Edison riesca a connettersi al dispositivo SensorTag.

1. Sbloccare bluetooth sulla scheda Edison e verificare che il numero di versione sia **5.37**.
    
    ```
    rfkill unblock bluetooth
    bluetoothctl --version
    ```

2. Eseguire il comando **bluetoothctl**. Verrà visualizzato un output simile al seguente:
    
    ```
    [NEW] Controller 98:4F:EE:04:1F:DF edison [default]
    ```

3. A questo punto sarà disponibile una shell di bluetooth interattiva. Immettere il comando **scan on** per eseguire la scansione di dispositivi Bluetooth. Verrà visualizzato un output simile al seguente:
    
    ```
    Discovery started
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: yes
    ```

4. Rendere individuabile il dispositivo SensorTag premendo il pulsante piccolo (il LED verde dovrebbe lampeggiare). La scheda Edison dovrebbe individuare il dispositivo SensorTag:
    
    ```
    [NEW] Device A0:E6:F8:B5:F6:00 CC2650 SensorTag
    [CHG] Device A0:E6:F8:B5:F6:00 TxPower: 0
    [CHG] Device A0:E6:F8:B5:F6:00 RSSI: -43
    ```
    
    In questo esempio è possibile notare che l'indirizzo MAC del dispositivo SensorTag è **A0:E6:F8:B5:F6:00**.

5. Disattivare la scansione immettendo il comando **scan off**.
    
    ```
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: no
    Discovery stopped
    ```

6. Connettersi al dispositivo SensorTag mediante il relativo indirizzo MAC immettendo **connect <MAC address>**. Si noti che l'output di esempio seguente è abbreviato:
    
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
    
    Nota: è possibile elencare di nuovo le caratteristiche GATT del dispositivo usando il comando **list-attributes**.

7. È ora possibile disconnettersi dal dispositivo usando il comando **disconnect** e quindi uscire dalla shell di bluetooth mediante il comando **quit**:
    
    ```
    Attempting to disconnect from A0:E6:F8:B5:F6:00
    Successful disconnected
    [CHG] Device A0:E6:F8:B5:F6:00 Connected: no
    ```

A questo punto si è pronti a eseguire l'esempio del gateway BLE sul dispositivo Edison.

## Eseguire l'esempio del gateway BLE

Per eseguire l'esempio BLE sul dispositivo Edison, è necessario completare tre attività:

- Configurare due dispositivi di esempio nell'hub IoT.
- Compilare Gateway SDK sul dispositivo Edison.
- Configurare ed eseguire l'esempio BLE sul dispositivo Edison.

Al momento della redazione di questo documento, Gateway SDK supporta solo i gateway che usano moduli BLE su Linux.

### Configurare due dispositivi di esempio nell'hub IoT

- [Creare un hub IoT][lnk-create-hub] nella sottoscrizione di Azure. Per completare questa procedura è necessario conoscere il nome dell'hub. Se non si ha ancora una sottoscrizione di Azure, è possibile creare un [account gratuito][lnk-free-trial].
- Aggiungere un dispositivo chiamato **SensorTag\_01** nell'hub IoT e prendere nota del relativo ID e della chiave del dispositivo. È possibile usare lo strumento [Device Explorer o iothub-explorer][lnk-explorer-tools] per aggiungere il dispositivo all'hub IoT creato nel passaggio precedente e recuperarne la chiave. Questo dispositivo verrà mappato al dispositivo SensorTag al momento della configurazione del gateway.

### Compilare Gateway SDK sul dispositivo Edison

La versione di **git** sul dispositivo Edison non supporta moduli secondari. Per scaricare il codice sorgente completo per Gateway SDK sul dispositivo Edison sono disponibili due opzioni:

- Opzione 1: clonare il repository di [Microsoft Azure IoT Gateway SDK][lnk-sdk] e quindi clonare manualmente il repository per ogni modulo secondario.
- Opzione 2: clonare il repository di [Microsoft Azure IoT Gateway SDK][lnk-sdk] su un dispositivo desktop dove **git** supporta moduli secondari e quindi copiare il repository completo con i moduli secondari sul dispositivo Edison.

Se si sceglie l'opzione 2, usare i comandi **git** seguenti per clonare Gateway SDK e tutti i relativi moduli secondari:

```
git clone --recursive https://github.com/Azure/azure-iot-gateway-sdk.git 
git submodule update --init --recursive
```

Si dovrà quindi comprimere l'intero repository locale in un unico file di archivio prima di copiarlo nel dispositivo Edison. Per copiare il file di archivio nel dispositivo Edison, è possibile usare un'utilità, ad esempio **pscp** inclusa in **Putty**. Ad esempio:

```
pscp .\gatewaysdk.zip root@192.168.0.45:/home/root
```

Quando si ha una copia completa del repository di Gateway SDK sul dispositivo Edison, è possibile compilarla usando il comando seguente dalla cartella contenente l'SDK:

```
./tools/build.sh
```

### Configurare ed eseguire l'esempio BLE sul dispositivo Edison

Per avviare ed eseguire l'esempio, è necessario configurare ogni modulo coinvolto nell'attività del gateway. Questa configurazione è definita in un file JSON ed è necessario configurare tutti e cinque i moduli coinvolti. Nel repository chiamato **gateway\_sample.json** è presente un file JSON di esempio che è possibile usare come base per creare il file di configurazione. Il file si trova nella cartella **samples/ble\_gateway\_hl/src** della copia locale del repository di SDK Gateway.

Le sezioni seguenti descrivono come modificare il file di configurazione per l'esempio BLE, supponendo che il repository di Gateway SDK si trovi nella cartella **/home/root/azure-iot-gateway-sdk/** sul dispositivo Edison. Se il repository si trova in un'altra posizione, è necessario modificare i percorsi di conseguenza:


#### Configurazione del modulo logger

Se il repository del gateway si trova nella cartella **/home/root/azure-iot-gateway-sdk/**, configurare il modulo logger nel modo seguente:

```json
{
    "module name": "logger",
    "module path": "/home/root/azure-iot-gateway-sdk/build/modules/logger/liblogger_hl.so",
    "args":
    {
        "filename":"/home/root/gw_logger.log"
    }
}
```

#### Configurazione del modulo BLE

La configurazione di esempio per il dispositivo BLE presuppone che venga usato un dispositivo SensorTag di Texas Instruments. Dovrebbe essere possibile usare qualsiasi dispositivo BLE standard in grado di funzionare come periferica GATT, ma in tal caso sarà necessario aggiornare i dati e gli ID delle caratteristiche GATT (per le istruzioni di scrittura). Aggiungere l'indirizzo MAC del dispositivo SensorTag:

```json
{
  "module name": "SensorTag",
  "module path": "/home/root/azure-iot-gateway-sdk/build/modules/ble/libble_hl.so",
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

#### Modulo HTTP dell'hub IoT

Aggiungere il nome dell'hub IoT. Il valore del suffisso è in genere **azure-devices.net**:

```json
{
  "module name": "IoTHub",
  "module path": "/home/root/azure-iot-gateway-sdk/build/modules/iothubhttp/libiothubhttp_hl.so",
  "args": {
    "IoTHubName": "<<Azure IoT Hub Name>>",
    "IoTHubSuffix": "<<Azure IoT Hub Suffix>>"
  }
}
```

#### Configurazione del modulo di mapping delle identità

Aggiungere l'indirizzo MAC del dispositivo SensorTag, nonché l'ID e la chiave del dispositivo **SensorTag\_01** che è stato aggiunto all'hub IoT:

```json
{
  "module name": "mapping",
  "module path": "/home/root/azure-iot-gateway-sdk/build/modules/identitymap/libidentity_map_hl.so",
  "args": [
    {
      "macAddress": "<<AA:BB:CC:DD:EE:FF>>",
      "deviceId": "<<Azure IoT Hub Device ID>>",
      "deviceKey": "<<Azure IoT Hub Device Key>>"
    }
  ]
}
```

#### Configurazione del modulo di stampa BLE

```json
{
    "module name": "BLE Printer",
    "module path": "/home/root/azure-iot-gateway-sdk/build/samples/ble_gateway_hl/ble_printer/libble_printer.so",
    "args": null
}
```

Per eseguire l'esempio, eseguire il codice binario di **ble\_gateway\_hl** passando il percorso al file di configurazione JSON. Se si è usato il file **gateway\_sample.json**, il comando per l'esecuzione avrà un aspetto simile al seguente:

```
./build/samples/ble_gateway_hl/ble_gateway_hl ./samples/ble_gateway_hl/src/gateway_sample.json
```

Prima di eseguire l'esempio, potrebbe essere necessario premere il pulsante piccolo sul dispositivo SensorTag per renderlo individuabile.

Durante l'esecuzione dell'esempio, è possibile usare lo strumento [Device Explorer o iothub-explorer][lnk-explorer-tools] per monitorare i messaggi che il gateway inoltra dal dispositivo SensorTag.

## Inviare messaggi da cloud a dispositivo

Il modulo BLE supporta anche l'invio di istruzioni dall'hub IoT di Azure al dispositivo. È possibile usare [Azure IoT Hub Device Explorer](https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md) o [IoT Hub Explorer] https://github.com/Azure/azure-iot-sdks/tree/master/tools/iothub-explorer) per inviare i messaggi JSON che il modulo BLE del gateway passa al dispositivo BLE. Se ad esempio si usa il dispositivo SensorTag di Texas Instruments, è possibile inviare al dispositivo i messaggi JSON seguenti dall'hub IoT.

- Reimpostare tutti i LED e il segnalatore acustico (spegnerli)

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AA=="
    }
    ```

- Configurare l'I/O come "remoto"

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA66-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```

- Accendere il LED rosso

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```

- Accendere il LED verde

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "Ag=="
    }
    ```

- Accendere il segnalatore acustico

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "BA=="
    }
    ```

Il comportamento predefinito per un dispositivo che usa il protocollo HTTP per connettersi all'hub IoT consiste nel controllare ogni 25 minuti se è stato inviato un nuovo comando. Pertanto, se si inviano più comandi separati, è necessario attendere 25 minuti affinché il dispositivo riceva ogni comando.

> [AZURE.NOTE] Il gateway verifica la presenza di nuovi comandi anche ogni volta che viene avviato. Di conseguenza, per imporre l'elaborazione di un comando è possibile arrestare e avviare il gateway.

## Passaggi successivi

Per altre informazioni, vedere [Azure IoT Gateway SDK][lnk-sdk].

<!-- Links -->
[lnk-ble-samplecode]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/samples/ble_gateway_hl
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md
[lnk-create-hub]: iot-hub-manage-through-portal.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/
[lnk-setup-win64]: https://software.intel.com/get-started-edison-windows
[lnk-setup-win32]: https://software.intel.com/get-started-edison-windows-32
[lnk-setup-osx]: https://software.intel.com/get-started-edison-osx
[lnk-setup-linux]: https://software.intel.com/get-started-edison-linux
[lnk-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/

<!---HONumber=AcomDC_0601_2016-->