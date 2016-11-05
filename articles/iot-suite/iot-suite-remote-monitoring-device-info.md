---
title: Metadati di informazioni sul dispositivo nella soluzione per il monitoraggio remoto | Microsoft Docs
description: Descrizione della soluzione preconfigurata per il monitoraggio remoto di Azure IoT e relativa architettura.
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''

ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2016
ms.author: dobett

---
# Metadati di informazioni sul dispositivo nella soluzione preconfigurata per il monitoraggio remoto
La soluzione preconfigurata per il monitoraggio remoto di Azure IoT Suite dimostra un approccio per la gestione dei metadati del dispositivo. Questo articolo delinea l'approccio adottato da questa soluzione per illustrare:

* Quali metadati del dispositivo vengono archiviati dalla soluzione.
* Come la soluzione gestisce i metadati del dispositivo.

## Context
La soluzione preconfigurata per il monitoraggio remoto usa l'[hub IoT Azure][lnk-iot-hub] per consentire ai dispositivi di inviare dati al cloud. L'hub IoT include un [registro delle identità dei dispositivi][lnk-identity-registry] per controllare l'accesso all'hub IoT. Il registro delle identità dei dispositivi dell'hub IoT è separato dal *registro dei dispositivi* specifico della soluzione per il monitoraggio remoto che archivia i metadati di informazioni sul dispositivo. La soluzione per il monitoraggio remoto usa un database [DocumentDB][lnk-docdb] per implementare il registro dei dispositivi per l'archiviazione dei metadati di informazioni sul dispositivo. L'[architettura di riferimento di Microsoft Azure IoT][lnk-ref-arch] descrive il ruolo del registro dei dispositivi in una tipica soluzione IoT.

> [!NOTE]
> La soluzione preconfigurata per il monitoraggio remoto mantiene il registro delle identità dei dispositivi sincronizzato con il registro dei dispositivi. Entrambi usano lo stesso ID dispositivo per identificare in modo univoco ogni dispositivo connesso all'hub IoT.
> 
> 

L'[anteprima di Gestione dei dispositivi dell'hub IoT][lnk-dm-preview] aggiunge all'hub IoT funzionalità simili a quelle per la gestione delle informazioni sul dispositivo descritta in questo articolo. Attualmente, la soluzione per il monitoraggio remoto usa solo le funzionalità disponibili a livello generale nell'hub IoT.

## Metadati di informazioni sul dispositivo
Un documento JSON di metadati di informazioni sul dispositivo archiviato nel database DocumentDB del registro dei dispositivi ha la struttura seguente:

```
{
  "DeviceProperties": {
    "DeviceID": "deviceid1",
    "HubEnabledState": null,
    "CreatedTime": "2016-04-25T23:54:01.313802Z",
    "DeviceState": "normal",
    "UpdatedTime": null
    },
  "SystemProperties": {
    "ICCID": null
  },
  "Commands": [],
  "CommandHistory": [],
  "IsSimulatedDevice": false,
  "id": "fe81a81c-bcbc-4970-81f4-7f12f2d8bda8"
}
```

* **DeviceProperties**: è il dispositivo stesso che scrive queste proprietà ed è l'autorità per i dati. Altre proprietà del dispositivo di esempio includono il produttore, il numero di modello e il numero di serie.
* **DeviceID**: ID dispositivo univoco. Questo valore è lo stesso nel registro delle identità dei dispositivi dell'hub IoT.
* **HubEnabledState**: stato del dispositivo nell'hub IoT. Questo valore è inizialmente impostato su **null** fino alla prima connessione del dispositivo. Nel portale della soluzione un valore **null** è rappresentato come dispositivo "registrato ma non presente".
* **CreatedTime**: data e ora di creazione del dispositivo.
* **DeviceState**: stato segnalato dal dispositivo.
* **UpdatedTime**: data e ora dell'ultimo aggiornamento dei dispositivo nel portale della soluzione.
* **SystemProperties**: il portale della soluzione scrive le proprietà di sistema di cui il dispositivo non è a conoscenza. Una proprietà di sistema di esempio è **ICCID** se la soluzione viene autorizzata e connessa a un servizio che gestisce dispositivi abilitati per la SIM.
* **Commands**: un elenco dei comandi supportati dal dispositivo. Il dispositivo fornisce queste informazioni alla soluzione.
* **CommandHistory**: un elenco dei comandi inviati dalla soluzione per il monitoraggio remoto al dispositivo e lo stato di tali comandi.
* **IsSimulatedDevice**: un flag che identifica un dispositivo come simulato.
* **id**: l'identificatore DocumentDB univoco per questo documento sul dispositivo.

> [!NOTE]
> Le informazioni sul dispositivo possono includere anche metadati per descrivere la telemetria inviata dal dispositivo all'hub IoT. La soluzione per il monitoraggio remoto usa questi metadati di telemetria per personalizzare la visualizzazione della [telemetria dinamica][lnk-dynamic-telemetry] nel dashboard.
> 
> 

## Ciclo di vita
Quando si crea per la prima volta un dispositivo nel portale della soluzione, la soluzione crea una voce nel registro dei dispositivi come illustrato in precedenza. La maggior parte delle informazioni viene inizialmente rimossa e **HubEnabledState** viene impostato su **null**. A questo punto la soluzione crea anche una voce per il dispositivo nel registro delle identità dei dispositivi che genera le chiavi usate dal dispositivo per l'autenticazione con l'hub IoT.

Quando un dispositivo si connette per la prima volta alla soluzione, invia un messaggio informativo sul dispositivo che include le proprietà del dispositivo, ad esempio il produttore del dispositivo, il numero di modello e il numero di serie. Un messaggio informativo sul dispositivo include anche un elenco dei comandi supportati dal dispositivo, incluse le informazioni sui parametri dei comandi. Quando la soluzione riceve il messaggio, aggiorna i metadati di informazioni sul dispositivo nel registro dei dispositivi.

### Visualizzare e modificare informazioni sul dispositivo nel portale della soluzione
L'elenco di dispositivi nel portale della soluzione visualizza le proprietà del dispositivo seguenti come colonne: **Stato**, **ID dispositivo**, **Produttore**, **Numero modello**, **Numero di serie**, **Firmware**, **Piattaforma**, **Processore** e **RAM installata**. Le proprietà **Latitudine** e **Longitudine** del dispositivo determinano la posizione nella mappa di Bing nel dashboard.

![Elenco dei dispositivi][img-device-list]

Se si fa clic su **Modifica** nel riquadro **Dettagli dispositivo** nel portale della soluzione, è possibile modificare tutte queste proprietà. Modificando queste proprietà, viene aggiornato il record per il dispositivo nel database di DocumentDB. Tuttavia, se un dispositivo invia un messaggio informativo sul dispositivo aggiornato, sovrascrive eventuali modifiche apportate nel portale della soluzione. Non è possibile modificare le proprietà **DeviceId**, **Hostname**, **HubEnabledState**, **CreatedTime**, **DeviceState** e **UpdatedTime** nel portale della soluzione perché solo il dispositivo ha autorità su queste proprietà.

![Modifica del dispositivo][img-device-edit]

È possibile usare il portale della soluzione per rimuovere un dispositivo dalla soluzione. Quando si rimuove un dispositivo, la soluzione rimuove i metadati di informazioni sul dispositivo dal registro dei dispositivi della soluzione e rimuove la voce del dispositivo nel registro delle identità dei dispositivi dell'hub IoT. Prima di poter rimuovere un dispositivo, è necessario disabilitarlo.

![Rimozione del dispositivo][img-device-remove]

## Elaborazione dei messaggi informativi sul dispositivo
I messaggi informativi sul dispositivo inviati da un dispositivo sono diversi dai messaggi di telemetria. I messaggi informativi sul dispositivo includono informazioni come le proprietà del dispositivo, i comandi a cui un dispositivo può rispondere e l'eventuale cronologia dei comandi. L'hub IoT non conosce i metadati contenuti in un messaggio informativo sul dispositivo ed elabora il messaggio come qualsiasi altro messaggio inviato dal dispositivo al cloud. Nella soluzione per il monitoraggio remoto, un processo di [Analisi di flusso di Azure][lnk-stream-analytics] legge il messaggio dall'hub IoT. Il processo di analisi di flusso **DeviceInfo** filtra i messaggi contenenti **"ObjectType": "DeviceInfo"** e li inoltra all'istanza dell'host **EventProcessorHost** in esecuzione in un processo Web. La logica nell'istanza di **EventProcessorHost** usa l'ID dispositivo per trovare il record di DocumentDB per il dispositivo specifico e aggiorna il record. Il record del registro dei dispositivi ora include informazioni come le proprietà del dispositivo, i comandi e la cronologia dei comandi.

> [!NOTE]
> Un messaggio informativo sul dispositivo è un messaggio standard inviato dal dispositivo al cloud. La soluzione distingue i messaggi informativi sul dispositivo dai messaggi di telemetria usando le query di Analisi di flusso di Azure.
> 
> 

## Record di informazioni sul dispositivo di esempio
La soluzione preconfigurata per il monitoraggio remoto usa due tipi di record di informazioni sul dispositivo: record per i dispositivi simulati distribuiti con la soluzione e record per i dispositivi personalizzati connessi alla soluzione.

### Dispositivo simulato
L'esempio seguente illustra il record di informazioni sul dispositivo JSON per un dispositivo simulato. Questo record ha un valore impostato per **UpdatedTime** che indica che il dispositivo ha inviato un messaggio **DeviceInfo** all'hub IoT. Il record include alcune comuni proprietà del dispositivo, definisce i sei comandi supportati dai dispositivi simulati e ha il flag **IsSimulatedDevice** impostato su **1**.

```
{
  "DeviceProperties": {
    "DeviceID": "SampleDevice001_455",
    "HubEnabledState": true,
    "CreatedTime": "2016-01-26T19:02:01.4550695Z",
    "DeviceState": "normal",
    "UpdatedTime": "2016-06-01T15:28:41.8105157Z",
    "Manufacturer": "Contoso Inc.",
    "ModelNumber": "MD-369",
    "SerialNumber": "SER9009",
    "FirmwareVersion": "1.39",
    "Platform": "Plat-34",
    "Processor": "i3-2191",
    "InstalledRAM": "3 MB",
    "Latitude": 47.583582,
    "Longitude": -122.130622
  },
  "Commands": [
    {
      "Name": "PingDevice",
      "Parameters": null
    },
    {
      "Name": "StartTelemetry",
      "Parameters": null
    },
    {
      "Name": "StopTelemetry",
      "Parameters": null
    },
    {
      "Name": "ChangeSetPointTemp",
      "Parameters": [
        {
          "Name": "SetPointTemp",
          "Type": "double"
        }
      ]
    },
    {
      "Name": "DiagnosticTelemetry",
      "Parameters": [
        {
          "Name": "Active",
          "Type": "boolean"
        }
      ]
    },
    {
      "Name": "ChangeDeviceState",
      "Parameters": [
        {
          "Name": "DeviceState",
          "Type": "string"
        }
      ]
    }
  ],
  "CommandHistory": [],
  "IsSimulatedDevice": 1,
  "Version": "1.0",
  "ObjectType": "DeviceInfo",
  "IoTHub": {
    "MessageId": null,
    "CorrelationId": null,
    "ConnectionDeviceId": "SampleDevice001_455",
    "ConnectionDeviceGenerationId": "635894317219942540",
    "EnqueuedTime": "0001-01-01T00:00:00",
    "StreamId": null
  },
  "SystemProperties": {
    "ICCID": null
  },
  "id": "7101c002-085f-4954-b9aa-7466980a2aaf"
}
```

### Dispositivo personalizzato
L'esempio seguente illustra il record di informazioni sul dispositivo JSON per un dispositivo personalizzato e ha il flag **IsSimulatedDevice** impostato su **0**. È possibile notare che questo dispositivo personalizzato supporta due comandi e che il portale della soluzione ha inviato un comando **SetTemperature** al dispositivo:

```
{
  "DeviceProperties": {
    "DeviceID": "mydevice01",
    "HubEnabledState": true,
    "CreatedTime": "2016-03-28T21:05:06.6061104Z",
    "DeviceState": "normal",
    "UpdatedTime": "2016-06-07T22:05:34.2802549Z"
  },
  "SystemProperties": {
    "ICCID": null
  },
  "Commands": [
    {
      "Name": "SetHumidity",
      "Parameters": [
        {
          "Name": "humidity",
          "Type": "int"
        }
      ]
    },
    {
      "Name": "SetTemperature",
      "Parameters": [
        {
          "Name": "temperature",
          "Type": "int"
        }
      ]
    }
  ],
  "CommandHistory": [
    {
      "Name": "SetTemperature",
      "MessageId": "2a0cec61-5eca-4de7-92dc-9c0bc4211c46",
      "CreatedTime": "2016-06-07T21:05:18.140796Z",
      "Parameters": {
        "temperature": 20
      },
      "UpdatedTime": "2016-06-07T21:05:18.716076Z",
      "Result": "Expired"
    }
  ],
  "IsSimulatedDevice": 0,
  "id": "6184ae0f-2d94-4fbd-91cd-4b193aecc9d1",
  "ObjectType": "DeviceInfo",
  "Version": "1.0",
  "IoTHub": {
    "MessageId": null,
    "CorrelationId": null,
    "ConnectionDeviceId": "SampleCustom",
    "ConnectionDeviceGenerationId": "635947959068246845",
    "EnqueuedTime": "0001-01-01T00:00:00",
    "StreamId": null
  }
}
```

Il codice seguente illustra il messaggio **DeviceInfo** JSON inviato dal dispositivo per aggiornare i metadati di informazioni sul dispositivo:

```
{ "ObjectType":"DeviceInfo",
  "Version":"1.0",
  "IsSimulatedDevice":false,
  "DeviceProperties": { "DeviceID":"mydevice01", "HubEnabledState":true },
  "Commands": [
    {"Name":"SetHumidity", "Parameters":[{"Name":"humidity","Type":"double"}]},
    {"Name":"SetTemperature", "Parameters":[{"Name":"temperature","Type":"double"}]}
  ]
}
```

## Passaggi successivi
Dopo aver illustrato come personalizzare le soluzioni preconfigurate, è possibile esplorare alcune altre funzionalità e soluzioni preconfigurate di Suite IoT:

* [Panoramica della soluzione preconfigurata di manutenzione predittiva][lnk-predictive-overview]
* [Domande frequenti su IoT Suite][lnk-faq]
* [Sicurezza della soluzione IoT sin dall'inizio][lnk-security-groundup]

<!-- Images and links -->
[img-device-list]: media/iot-suite-remote-monitoring-device-info/image1.png
[img-device-edit]: media/iot-suite-remote-monitoring-device-info/image2.png
[img-device-remove]: media/iot-suite-remote-monitoring-device-info/image3.png

[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide.md#device-identity-registry
[lnk-docdb]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-ref-arch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-dm-preview]: ../iot-hub/iot-hub-device-management-overview.md
[lnk-dynamic-telemetry]: iot-suite-dynamic-telemetry.md

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md

<!---HONumber=AcomDC_0914_2016-->