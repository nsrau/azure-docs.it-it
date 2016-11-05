---
title: Usare un dispositivo reale con Gateway SDK | Microsoft Docs
description: Procedura dettagliata di Azure IoT Hub Gateway SDK che prevede l'uso di un dispositivo SensorTag di Texas Instruments per inviare dati all'hub IoT tramite un gateway in esecuzione su un modulo di calcolo Intel Edison
services: iot-hub
documentationcenter: ''
author: chipalost
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2016
ms.author: andbuc

---
# <a name="iot-gateway-sdk-(beta)-–-send-device-to-cloud-messages-with-a-real-device-using-linux"></a>IoT Gateway SDK (beta): inviare messaggi da dispositivo a cloud con un dispositivo reale usando Linux
Questa procedura dettagliata relativa all'esempio [Bluetooth Low Energy][lnk-ble-samplecode] illustra come usare [Microsoft Azure IoT Gateway SDK][lnk-sdk] per inoltrare all'hub IoT i dati di telemetria da dispositivo a cloud da un dispositivo fisico e come instradare i comandi dall'hub IoT a un dispositivo fisico.

In questa procedura dettagliata verranno trattati i seguenti argomenti:

* **Architettura**: informazioni importanti sull'architettura relative all'esempio Bluetooth Low Energy.
* **Compilazione ed esecuzione**: i passaggi richiesti per compilare ed eseguire l'esempio.

## <a name="architecture"></a>Architettura
La procedura dettagliata illustra come compilare ed eseguire un gateway IoT in un modulo di calcolo Intel Edison che esegue Linux. Il gateway viene creato mediante IoT Gateway SDK. L'esempio usa un dispositivo SensorTag di Texas Instruments basato su tecnologia Bluetooth Low Energy (BLE) per raccogliere i dati sulla temperatura.

Quando viene eseguito, il gateway:

* Si connette a un dispositivo SensorTag usando il protocollo BLE.
* Si connette all'hub IoT usando il protocollo AMQP.
* Inoltra i dati di telemetria dal dispositivo SensorTag all'hub IoT.
* Instrada i comandi dall'hub IoT al dispositivo SensorTag.

Il gateway contiene i moduli seguenti:

* Un *modulo BLE* che si interfaccia con un dispositivo BLE per ricevere i dati sulla temperatura dal dispositivo e inviare comandi al dispositivo.
* Un *modulo BLE da cloud a dispositivo* che converte i messaggi JSON provenienti dal cloud in istruzioni BLE per il *modulo BLE*.
* Un *modulo logger* che registra tutti i messaggi del gateway.
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
5. Il modulo logger registra tutti i messaggi provenienti dal broker in un file su disco.

Il diagramma a blocchi seguente illustra la pipeline del flusso di invio dei comandi del dispositivo:

![](media/iot-hub-gateway-sdk-physical-device/gateway_ble_command_data_flow.png)

1. Il modulo dell'hub IoT esegue periodicamente il polling dell'hub IoT per rilevare la presenza di nuovi messaggi di comando.
2. Quando il modulo dell'hub IoT riceve un nuovo messaggio di comando, lo pubblica sul broker.
3. Il modulo di mapping delle identità preleva il messaggio di comando e usa una tabella interna per convertire l'ID dispositivo dell'hub IoT in un indirizzo MAC di dispositivo, quindi pubblica un nuovo messaggio che include l'indirizzo MAC del dispositivo di destinazione nella mappa delle proprietà.
4. Il modulo BLE da cloud a dispositivo preleva il messaggio e lo converte nell'istruzione BLE appropriata per il modulo BLE. Dopodiché pubblica un nuovo messaggio.
5. Il modulo BLE preleva il messaggio ed esegue l'istruzione di I/O comunicando con il dispositivo BLE.
6. Il modulo logger registra tutti i messaggi provenienti dal broker in un file su disco.

## <a name="prepare-your-hardware"></a>Preparare l'hardware
Questa esercitazione presuppone che si usi un dispositivo [SensorTag di Texas Instruments](http://www.ti.com/ww/en/wireless_connectivity/sensortag2015/index.html) connesso a una scheda Intel Edison.

### <a name="set-up-the-edison-board"></a>Configurare la scheda Edison
Prima di iniziare, verificare che sia possibile connettere il dispositivo Edison alla rete wireless. Per configurare il dispositivo Edison, è necessario connetterlo a un computer host. Sono disponibili guide introduttive di Intel per i sistemi operativi seguenti:

* [Get Started with the Intel Edison Development Board on Windows 64-bit][lnk-setup-win64] (Introduzione alla scheda di sviluppo Intel Edison su Windows a 64 bit).
* [Get Started with the Intel Edison Development Board on Windows 32-bit][lnk-setup-win32] (Introduzione alla scheda di sviluppo Intel Edison su Windows a 32 bit).
* [Get Started with the Intel Edison Development Board on Mac OS X][lnk-setup-osx] (Introduzione alla scheda di sviluppo Intel Edison su Mac OS X).
* [Getting Started with the Intel® Edison Board on Linux][lnk-setup-linux] (Introduzione alla scheda Intel® Edison su Linux).

Per configurare il dispositivo Edison e acquisire familiarità con tale dispositivo, è consigliabile eseguire tutti i passaggi descritti in questi articoli introduttivi tranne l'ultimo, relativo alla scelta dell'IDE, che non è necessario ai fini dell'esercitazione corrente. Al termine della procedura di configurazione del dispositivo Edison, l'utente avrà:

* Aggiornato il dispositivo Edison con il firmware più recente.
* Stabilito una connessione seriale dall'host al dispositivo Edison.
* Eseguito lo script **configure_edison** per impostare una password e abilitare la connettività Wi-Fi sul dispositivo Edison.

### <a name="enable-connectivity-to-the-sensortag-device-from-your-edison-board"></a>Abilitare la connettività al dispositivo SensorTag dalla scheda Edison
Prima di eseguire l'esempio, è necessario verificare che la scheda Edison sia in grado di connettersi al dispositivo SensorTag.

Innanzitutto è necessario verificare che la scheda Edison riesca a connettersi al dispositivo SensorTag.

1. Sbloccare bluetooth sulla scheda Edison e verificare che il numero di versione sia **5.37**.
   
    ```
    rfkill unblock bluetooth
    bluetoothctl --version
    ```
2. Eseguire il comando **bluetoothctl** . A questo punto sarà disponibile una shell di bluetooth interattiva. 
3. Immettere il comando **power on** per accendere il controller bluetooth. Verrà visualizzato un output simile al seguente:
   
    ```
    [NEW] Controller 98:4F:EE:04:1F:DF edison [default]
    ```
4. Sempre nella shell interattiva bluetooth, immettere il comando **scan on** per cercare i dispositivi bluetooth. Verrà visualizzato un output simile al seguente:
   
    ```
    Discovery started
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: yes
    ```
5. Rendere individuabile il dispositivo SensorTag premendo il pulsante piccolo (il LED verde dovrebbe lampeggiare). La scheda Edison dovrebbe individuare il dispositivo SensorTag:
   
    ```
    [NEW] Device A0:E6:F8:B5:F6:00 CC2650 SensorTag
    [CHG] Device A0:E6:F8:B5:F6:00 TxPower: 0
    [CHG] Device A0:E6:F8:B5:F6:00 RSSI: -43
    ```
   
    In questo esempio è possibile notare che l'indirizzo MAC del dispositivo SensorTag è **A0:E6:F8:B5:F6:00**.
6. Disattivare la scansione immettendo il comando **scan off** .
   
    ```
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: no
    Discovery stopped
    ```
7. Connettersi al dispositivo SensorTag con l'indirizzo MAC immettendo **connect <MAC address>**. Si noti che l'output di esempio seguente è abbreviato:
   
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
   
    Nota: è possibile elencare di nuovo le caratteristiche GATT del dispositivo usando il comando **list-attributes** .
8. È ora possibile disconnettersi dal dispositivo usando il comando **disconnect** e quindi uscire dalla shell Bluetooth con il comando **quit**:
   
    ```
    Attempting to disconnect from A0:E6:F8:B5:F6:00
    Successful disconnected
    [CHG] Device A0:E6:F8:B5:F6:00 Connected: no
    ```

A questo punto si è pronti a eseguire l'esempio del gateway BLE sul dispositivo Edison.

## <a name="run-the-ble-gateway-sample"></a>Eseguire l'esempio del gateway BLE
Per eseguire l'esempio BLE sul dispositivo Edison, è necessario completare tre attività:

* Configurare due dispositivi di esempio nell'hub IoT.
* Compilare Gateway SDK sul dispositivo Edison.
* Configurare ed eseguire l'esempio BLE sul dispositivo Edison.

Al momento della redazione di questo documento, Gateway SDK supporta solo i gateway che usano moduli BLE su Linux.

### <a name="configure-two-sample-devices-in-your-iot-hub"></a>Configurare due dispositivi di esempio nell'hub IoT
* [Creare un hub IoT][lnk-create-hub] nella sottoscrizione di Azure. Il nome dell'hub sarà necessario per completare questa procedura. Se non si ha ancora una sottoscrizione di Azure, è possibile [creare un account gratuito][lnk-free-trial].
* Aggiungere un dispositivo chiamato **SensorTag_01** all'hub IoT e prendere nota del relativo ID e della chiave del dispositivo. È possibile usare lo strumento [Device Explorer o iothub-explorer][lnk-explorer-tools] per aggiungere il dispositivo all'hub IoT creato nel passaggio precedente e recuperarne la chiave. Questo dispositivo verrà mappato al dispositivo SensorTag al momento della configurazione del gateway.

### <a name="build-the-gateway-sdk-on-your-edison-device"></a>Compilare Gateway SDK sul dispositivo Edison
La versione di **git** nel dispositivo Edison non supporta moduli secondari. Per scaricare il codice sorgente completo per Gateway SDK sul dispositivo Edison sono disponibili due opzioni:

* Opzione 1: Clonare il repository di [Microsoft Azure IoT Gateway SDK][lnk-sdk] e quindi clonare manualmente il repository per ogni modulo secondario.
* Opzione 2: Clonare il repository di [Microsoft Azure IoT Gateway SDK][lnk-sdk] in un dispositivo desktop dove **git** supporta moduli secondari e quindi copiare il repository completo con i moduli secondari nel dispositivo Edison.

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

### <a name="configure-and-run-the-ble-sample-on-your-edison-device"></a>Configurare ed eseguire l'esempio BLE sul dispositivo Edison
Per avviare ed eseguire l'esempio, è necessario configurare ogni modulo coinvolto nell'attività del gateway. Questa configurazione è definita in un file JSON ed è necessario configurare tutti e cinque i moduli coinvolti. Nel repository chiamato **gateway_sample.json** è presente un file JSON di esempio che è possibile usare come base per creare il file di configurazione. Il file si trova nella cartella **samples/ble_gateway_hl/src** della copia locale del repository Gateway SDK.

Le sezioni seguenti descrivono come modificare il file di configurazione per l'esempio BLE, supponendo che il repository Gateway SDK si trovi nella cartella **/home/root/azure-iot-gateway-sdk/** nel dispositivo Edison. Se il repository si trova in un'altra posizione, è necessario modificare i percorsi di conseguenza:

#### <a name="logger-configuration"></a>Configurazione del modulo logger
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

#### <a name="ble-module-configuration"></a>Configurazione del modulo BLE
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

#### <a name="iot-hub-module"></a>modulo dell'hub IoT
Aggiungere il nome dell'hub IoT. Il valore del suffisso è in genere **azure-devices.net**:

```json
{
  "module name": "IoTHub",
  "module path": "/home/root/azure-iot-gateway-sdk/build/modules/iothub/libiothub_hl.so",
  "args": {
    "IoTHubName": "<<Azure IoT Hub Name>>",
    "IoTHubSuffix": "<<Azure IoT Hub Suffix>>",
    "Transport": "HTTP"
  }
}
```

#### <a name="identity-mapping-module-configuration"></a>Configurazione del modulo di mapping delle identità
Aggiungere l'indirizzo MAC del dispositivo SensorTag, nonché l'ID e la chiave del dispositivo **SensorTag_01** che è stato aggiunto all'hub IoT:

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

#### <a name="ble-printer-module-configuration"></a>Configurazione del modulo di stampa BLE
```json
{
    "module name": "BLE Printer",
    "module path": "/home/root/azure-iot-gateway-sdk/build/samples/ble_gateway_hl/ble_printer/libble_printer.so",
    "args": null
}
```

#### <a name="routing-configuration"></a>Configurazione di routing
La configurazione seguente garantisce che:

* Il modulo **Logger** riceva e registri tutti i messaggi.
* Il modulo **SensorTag** invii i messaggi a entrambi i moduli **mapping** e **BLE Printer**.
* Il modulo **mapping** invii i messaggi al modulo **IoTHub** per farli recapitare all'hub IoT.
* Il modulo **IoTHub** invii di nuovo i messaggi al modulo **mapping**.
* Il modulo **mapping** invii di nuovo i messaggi al modulo **SensorTag**.

```json
"links" : [
    {"source" : "*", "sink" : "Logger" },
    {"source" : "SensorTag", "sink" : "mapping" },
    {"source" : "SensorTag", "sink" : "BLE Printer" },
    {"source" : "mapping", "sink" : "IoTHub" },
    {"source" : "IoTHub", "sink" : "mapping" },
    {"source" : "mapping", "sink" : "SensorTag" }
  ]
```

Per eseguire l'esempio, eseguire il codice binario **ble_gateway_hl** passando il percorso al file di configurazione JSON. Se si è usato il file **gateway_sample.json**, il comando per l'esecuzione avrà un aspetto simile al seguente:

```
./build/samples/ble_gateway_hl/ble_gateway_hl ./samples/ble_gateway_hl/src/gateway_sample.json
```

Prima di eseguire l'esempio, potrebbe essere necessario premere il pulsante piccolo sul dispositivo SensorTag per renderlo individuabile.

Durante l'esecuzione dell'esempio è possibile usare lo strumento [Device Explorer o iothub-explorer][lnk-explorer-tools] per monitorare i messaggi che il gateway inoltra dal dispositivo SensorTag.

## <a name="send-cloud-to-device-messages"></a>Inviare messaggi da cloud a dispositivo
Il modulo BLE supporta anche l'invio di istruzioni dall'hub IoT di Azure al dispositivo. È possibile usare [Device Explorer per l'hub IoT di Azure](https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md) o [IoT Hub Explorer](https://github.com/Azure/azure-iot-sdks/tree/master/tools/iothub-explorer) per inviare i messaggi JSON che il modulo BLE del gateway passa al dispositivo BLE. Se ad esempio si usa il dispositivo SensorTag di Texas Instruments, è possibile inviare al dispositivo i messaggi JSON seguenti dall'hub IoT.

* Reimpostare tutti i LED e il segnalatore acustico (spegnerli)
  
    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AA=="
    }
    ```
* Configurare l'I/O come "remoto"
  
    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA66-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```
* Accendere il LED rosso
  
    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```
* Accendere il LED verde
  
    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "Ag=="
    }
    ```
* Accendere il segnalatore acustico
  
    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "BA=="
    }
    ```

Il comportamento predefinito per un dispositivo che usa il protocollo HTTP per connettersi all'hub IoT consiste nel controllare ogni 25 minuti se è stato inviato un nuovo comando. Pertanto, se si inviano più comandi separati, è necessario attendere 25 minuti affinché il dispositivo riceva ogni comando.

> [!NOTE]
> Il gateway verifica la presenza di nuovi comandi anche ogni volta che viene avviato. Di conseguenza, per imporre l'elaborazione di un comando è possibile arrestare e avviare il gateway.
> 
> 

## <a name="next-steps"></a>Passaggi successivi
Per ottenere informazioni più avanzate sul Gateway SDK e provare alcuni esempi di codice, vedere le seguenti risorse ed esercitazioni per gli sviluppatori:

* [Azure IoT Gateway SDK][lnk-sdk]

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Guida per gli sviluppatori][lnk-devguide]

<!-- Links -->
[lnk-ble-samplecode]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/samples/ble_gateway_hl
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-setup-win64]: https://software.intel.com/get-started-edison-windows
[lnk-setup-win32]: https://software.intel.com/get-started-edison-windows-32
[lnk-setup-osx]: https://software.intel.com/get-started-edison-osx
[lnk-setup-linux]: https://software.intel.com/get-started-edison-linux
[lnk-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/


[lnk-devguide]: iot-hub-devguide.md
[lnk-create-hub]: iot-hub-create-through-portal.md 


<!--HONumber=Oct16_HO2-->


