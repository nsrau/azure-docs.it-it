---
title: Esportare i dati in Archiviazione BLOB di Azure | Microsoft Docs
description: Come esportare dati dall'applicazione Azure IoT Central in Archiviazione BLOB di Azure
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 09/26/2019
ms.topic: conceptual
ms.service: iot-central
manager: corywink
ms.openlocfilehash: b6a437af33e3e85b8dc07de223b6e1fd7ce6cb03
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176242"
---
# <a name="export-your-data-to-azure-blob-storage"></a>Esportare i dati in Archiviazione BLOB di Azure

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

*Questo argomento riguarda gli amministratori.*

Questo articolo descrive come usare la funzionalità di esportazione continua dei dati in Azure IoT Central per esportare periodicamente i dati nell' **account di archiviazione BLOB di Azure** o **Azure Data Lake storage Gen2 account di archiviazione**. È possibile esportare **misure**, **dispositivi**e **modelli di dispositivo** in file in formato JSON o Apache avro. I dati esportati possono essere usati per eseguire analisi di percorsi non critici, ad esempio modelli di training in Azure Machine Learning o analisi delle tendenze a lungo termine in Microsoft Power BI.

> [!Note]
> Quando si attiva l'esportazione continua dei dati, si recuperano solo i dati a partire dal momento dell'attivazione. Attualmente non è possibile recuperare i dati relativi a un periodo in cui l'esportazione continua era disattivata. Per conservare una maggiore quantità di dati cronologici, attivare presto l'esportazione continua dei dati.


## <a name="prerequisites"></a>Prerequisiti

- È necessario essere amministratore nell'applicazione IoT Central


## <a name="set-up-export-destination"></a>Configurare la destinazione di esportazione

Se non si dispone di una risorsa di archiviazione esistente in cui eseguire l'esportazione, attenersi alla seguente procedura:

1. Creare un [nuovo account di archiviazione nel portale di Azure](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). È possibile ottenere altre informazioni sulla creazione di nuovi [account di archiviazione BLOB di Azure](https://aka.ms/blobdocscreatestorageaccount) o [Azure Data Lake storage V2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-account).

    > [!Note] 
    > Se si sceglie di esportare i dati in un account di archiviazione ADLS V2, è necessario scegliere il **tipo di account** come **BlobStorage**. 

    > [!Note] 
    > È possibile esportare i dati negli account di archiviazione in sottoscrizioni diverse da quello per l'applicazione IoT Central con pagamento in base al consumo. In questo caso ci si connetterà con una stringa di connessione.

2. Creare un contenitore nell'account di archiviazione. Passare all'account di archiviazione. In **Servizio BLOB** selezionare **Esplora BLOB**. Selezionare **+ Contenitore** in alto per creare un nuovo contenitore.

## <a name="set-up-continuous-data-export"></a>Configurare l'esportazione continua dei dati

Ora che si dispone di una destinazione di archiviazione in cui esportare i dati, attenersi alla procedura seguente per configurare l'esportazione continua dei dati. 

1. Accedere all'applicazione IoT Central.

2. Nel riquadro sinistro selezionare **esportazione dati**.

    > [!Note]
    > Se non viene visualizzata l'esportazione dei dati nel riquadro sinistro, l'utente non è un amministratore nell'app. Chiedere a un amministratore di configurare l'esportazione dei dati.

3. Selezionare il pulsante **+ nuovo** in alto a destra. Scegliere **archiviazione BLOB di Azure** come destinazione dell'esportazione. 

    > [!NOTE] 
    > Il numero massimo di esportazioni per app è cinque. 

    ![Creare una nuova esportazione continua dei dati](media/howto-export-data/export-new2.png)

4. Nella casella di riepilogo a discesa selezionare lo **spazio dei nomi dell'account di archiviazione**. È anche possibile selezionare l'ultima opzione dell'elenco, ossia **Immettere una stringa di connessione**. 

    > [!NOTE] 
    > Vengono visualizzati solo gli spazi dei nomi degli account di archiviazione nella **stessa sottoscrizione dell'app IOT Central**. Se si vogliono esportare i dati in una destinazione esterna a questa sottoscrizione, scegliere **Immettere una stringa di connessione** e vedere il passaggio 5.

    > [!NOTE] 
    > Per le app in versione di valutazione per 7 giorni l'unico modo per configurare l'esportazione continua dei dati è tramite una stringa di connessione. A queste app non è infatti associata una sottoscrizione di Azure.

    ![Crea nuova esportazione in BLOB](media/howto-export-data/export-create-blob2.png)

5. (Facoltativo) Se si è selezionata l'opzione **Immettere una stringa di connessione**, viene visualizzata una nuova casella in cui incollare la stringa di connessione. Per ottenere la stringa di connessione per l'account di archiviazione, passare all'account di archiviazione nella portale di Azure: in **Impostazioni**selezionare **chiavi di accesso** -copiare la stringa di connessione Key1 o la stringa di connessione Key2
 
6. Scegliere un contenitore dalla casella di riepilogo a discesa. Se non si dispone di un contenitore, passare all'account di archiviazione nel portale di Azure:
    - In **servizio BLOB**selezionare **BLOB**. Fare clic su **+ contenitore** e assegnare un nome al contenitore. Scegliere un livello di accesso pubblico per i dati (qualsiasi funzionerà con l'esportazione continua dei dati). Altre informazioni sono disponibili nella [documentazione di archiviazione di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container).

7. Scegliere il **formato di dati** preferito: JSON o il formato [Apache avro](https://avro.apache.org/docs/current/index.html) .

8. In **Data to export** (Dati da esportare) specificare ogni tipo di dati da esportare impostando il tipo su **On** (Attivato).

9. Per attivare l'esportazione continua dei dati, assicurarsi che l'interruttore **Esporta dati** sia **attivato**. Selezionare **Salva**.

   ![Configurare l'esportazione continua dei dati](media/howto-export-data/export-list-blob2.png)

10. Dopo alcuni minuti, i dati verranno visualizzati nell'account di archiviazione.


## <a name="path-structure"></a>Struttura Path

I dati di misurazioni, dispositivi e modelli di dispositivo vengono esportati nell'account di archiviazione ogni minuto e ogni file contiene il batch delle modifiche apportate dall'ultimo file esportato. I dati esportati vengono inseriti in tre cartelle in formato JSON o avro. I percorsi predefiniti nell'account di archiviazione sono:
- Messaggi: {container}/measurements/{hubname}/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}
- Dispositivi: {container}/devices/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}
- Modelli di dispositivo: {container}/deviceTemplates/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}

È possibile esplorare i file esportati nel portale di Azure passando al file e scegliendo la scheda **modifica BLOB** .

## <a name="data-format"></a>Formato dati 

### <a name="measurements"></a>Measurements (Misure)

I dati delle misurazioni esportati contengono tutti i nuovi messaggi che IoT Central ha ricevuto da tutti i dispositivi durante tale periodo. I file esportati sono nello stesso formato dei file dei messaggi esportati dal [routing dei messaggi dell'hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-csharp-csharp-process-d2c) nell'archiviazione BLOB.

> [!NOTE]
> Assicurarsi che i dispositivi inviino messaggi con `contentType: application/JSON` e `contentEncoding:utf-8` (o `utf-16``utf-32`). Per un esempio, vedere la [documentazione sull'hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#message-routing-query-based-on-message-body) .

> [!NOTE]
> I dispositivi che inviano le misurazioni sono rappresentati da ID dispositivo (vedere le sezioni seguenti). Per ottenere i nomi dei dispositivi, esportare i relativi snapshot. Correlare ogni record di messaggio usando il valore **connectionDeviceId** corrispondente a **deviceId** del dispositivo del record di dispositivo.

L'esempio seguente mostra un record in un file AVRO decodificato:

```json
{ 
  "EnqueuedTimeUtc":"2019-06-11T00:00:08.2250000Z",
  "Properties":{},
  "SystemProperties":{ 
    "connectionDeviceId":"<deviceId>",
    "connectionAuthMethod":"{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "connectionDeviceGenerationId":"<generationId>",
    "enqueuedTime":"2019-06-11T00:00:08.2250000Z"
  },
  "Body":"{\"humidity\":80.59100954598546,\"magnetometerX\":0.29451796907056726,\"magnetometerY\":0.5550332126050068,\"magnetometerZ\":-0.04116681874733441,\"connectivity\":\"connected\",\"opened\":\"triggered\"}"
}
```

### <a name="devices"></a>Dispositivi

Quando si attiva l'esportazione continua dei dati per la prima volta, viene esportato un singolo snapshot con tutti i dispositivi. Ogni dispositivo include:
- `id` del dispositivo in IoT Central
- `name` ID del dispositivo
- `deviceId` dal [Servizio di provisioning di dispositivi](/azure/iot-central/core/howto-connect-nodejs)
- Informazioni sul modello di dispositivo
- Valori delle proprietà
- Valori delle impostazioni

Viene creato un nuovo snapshot una volta al minuto. Lo snapshot include:

- I nuovi dispositivi aggiunti dall'ultimo snapshot
- I dispositivi i cui valori di proprietà e impostazioni sono stati modificati dall'ultimo snapshot

> [!NOTE]
> I dispositivi eliminati dall'ultimo snapshot non vengono esportati. Gli snapshot non hanno attualmente indicatori per i dispositivi eliminati.
>
> Il modello di dispositivo a cui appartiene ogni dispositivo è rappresentato da un ID del modello di dispositivo. Per ottenere il nome del modello di dispositivo, esportare gli snapshot dei modelli di dispositivo.

I file esportati contengono una sola riga per ogni record. L'esempio seguente mostra un record in formato Avro, decodificato:

```json
{ 
  "id":"<id>",
  "name":"Refrigerator 2",
  "simulated":true,
  "deviceId":"<deviceId>",
  "deviceTemplate":{ 
    "id":"<template id>",
    "version":"1.0.0"
  },
  "properties":{ 
    "cloud":{ 
      "location":"New York",
      "maintCon":true,
      "tempThresh":20
    },
    "device":{ 
      "lastReboot":"2018-02-09T22:22:47.156Z"
    }
  },
  "settings":{ 
    "device":{ 
      "fanSpeed":0
    }
  }
}
```

### <a name="device-templates"></a>Modelli di dispositivo

Quando si attiva l'esportazione continua dei dati per la prima volta, viene esportato un singolo snapshot con tutti i modelli di dispositivo. Ogni modello di dispositivo include:
- `id` del modello del dispositivo
- `name` del modello del dispositivo
- `version` del modello del dispositivo
- Tipi di dati delle misurazioni e valori min/max
- Tipi di dati delle proprietà e valori predefiniti
- Tipi di dati delle impostazioni e valori predefiniti

Viene creato un nuovo snapshot una volta al minuto. Lo snapshot include:

- I nuovi modelli di dispositivo aggiunti dall'ultimo snapshot
- I modelli di dispositivo le cui misurazioni, proprietà e impostazioni sono state modificate dall'ultimo snapshot

> [!NOTE]
> I modelli di dispositivo eliminati dall'ultimo snapshot non vengono esportati. Gli snapshot non hanno attualmente indicatori per i modelli di dispositivo eliminati.

I file esportati contengono una sola riga per ogni record. L'esempio seguente mostra un record in formato Avro, decodificato:

```json
{ 
  "id":"<id>",
  "name":"Refrigerated Vending Machine",
  "version":"1.0.0",
  "measurements":{ 
    "telemetry":{ 
      "humidity":{ 
        "dataType":"double",
        "name":"Humidity"
      },
      "magnetometerX":{ 
        "dataType":"double",
        "name":"Magnetometer X"
      },
      "magnetometerY":{ 
        "dataType":"double",
        "name":"Magnetometer Y"
      },
      "magnetometerZ":{ 
        "dataType":"double",
        "name":"Magnetometer Z"
      }
    },
    "states":{ 
      "connectivity":{ 
        "dataType":"enum",
        "name":"Connectivity"
      }
    },
    "events":{ 
      "opened":{ 
        "name":"Door Opened",
        "category":"informational"
      }
    }
  },
  "settings":{ 
    "device":{ 
      "fanSpeed":{ 
        "dataType":"double",
        "name":"Fan Speed",
        "initialValue":0
      }
    }
  },
  "properties":{ 
    "cloud":{ 
      "location":{ 
        "dataType":"string",
        "name":"Location",
        "initialValue":"Seattle"
      },
      "maintCon":{ 
        "dataType":"boolean",
        "name":"Maintenance Contract",
        "initialValue":true
      },
      "tempThresh":{ 
        "dataType":"double",
        "name":"Temperature Alert Threshold",
        "initialValue":30
      }
    },
    "device":{ 
      "lastReboot":{ 
        "dataType":"dateTime",
        "name":"Last Reboot"
      }
    }
  }
}
```

## <a name="read-exported-avro-files"></a>Leggere i file AVRO esportati

AVRO è un formato binario, pertanto i file non possono essere letti nel loro stato non elaborato. I file possono essere decodificati in formato JSON. Gli esempi seguenti illustrano come analizzare i file AVRO delle misurazioni, dei dispositivi e dei modelli di dispositivo. Gli esempi corrispondono a quelli descritti nella sezione precedente.

### <a name="read-avro-files-by-using-python"></a>Leggere i file AVRO usando Python

#### <a name="install-pandas-and-the-pandavro-package"></a>Installare pandas e il pacchetto pandavro

```python
pip install pandas
pip install pandavro
```

#### <a name="parse-a-measurements-avro-file"></a>Analizzare un file AVRO delle misurazioni

```python
import json
import pandavro as pdx
import pandas as pd


def parse(filePath):
    # Pandavro loads the Avro file into a pandas DataFrame
    # where each record is a single row.
    measurements = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The SystemProperties column contains a dictionary
    # with the device ID located under the connectionDeviceId key.
    transformed["device_id"] = measurements["SystemProperties"].apply(
        lambda x: x["connectionDeviceId"])

    # The Body column is a series of UTF-8 bytes that is stringified
    # and parsed as JSON. This example pulls the humidity property
    # from each column to get the humidity field.
    transformed["humidity"] = measurements["Body"].apply(
        lambda x: json.loads(bytes(x).decode('utf-8'))["humidity"])

    # Finally, print the new DataFrame with our device IDs and humidities.
    print(transformed)
```

#### <a name="parse-a-devices-avro-file"></a>Analizzare un file AVRO dei dispositivi

```python
import json
import pandavro as pdx
import pandas as pd


def parse(filePath):
    # Pandavro loads the Avro file into a pandas DataFrame
    # where each record is a single row.
    devices = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The device ID is available in the id column.
    transformed["device_id"] = devices["deviceId"]

    # The template ID and version are present in a dictionary under
    # the deviceTemplate column.
    transformed["template_id"] = devices["deviceTemplate"].apply(
        lambda x: x["id"])
    transformed["template_version"] = devices["deviceTemplate"].apply(
        lambda x: x["version"])

    # The fanSpeed setting value is located in a nested dictionary
    # under the settings column.
    transformed["fan_speed"] = devices["settings"].apply(
        lambda x: x["device"]["fanSpeed"])

    # Finally, print the new DataFrame with our device and template
    # information, along with the value of the fan speed.
    print(transformed)
```

#### <a name="parse-a-device-templates-avro-file"></a>Analizzare un file AVRO dei modelli di dispositivo

```python
import json
import pandavro as pdx
import pandas as pd


def parse(filePath):
    # Pandavro loads the Avro file into a pandas DataFrame
    # where each record is a single row.
    templates = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The template and version are available in the id and version columns.
    transformed["template_id"] = templates["id"]
    transformed["template_version"] = templates["version"]

    # The fanSpeed setting value is located in a nested dictionary
    # under the settings column.
    transformed["fan_speed"] = templates["settings"].apply(
        lambda x: x["device"]["fanSpeed"])

    # Finally, print the new DataFrame with our device and template
    # information, along with the value of the fan speed.
    print(transformed)
```

### <a name="read-avro-files-by-using-c"></a>Leggere i file AVRO usando C#

#### <a name="install-the-microsofthadoopavro-package"></a>Installare il pacchetto Microsoft.Hadoop.Avro

```csharp
Install-Package Microsoft.Hadoop.Avro -Version 1.5.6
```

#### <a name="parse-a-measurements-avro-file"></a>Analizzare un file AVRO delle misurazioni

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;
using Newtonsoft.Json;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the Avro records in the block and extract the fields.
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    var systemProperties = record.GetField<IDictionary<string, object>>("SystemProperties");
                    var deviceId = systemProperties["connectionDeviceId"] as string;
                    Console.WriteLine("Device ID: {0}", deviceId);

                    using (var stream = new MemoryStream(record.GetField<byte[]>("Body")))
                    {
                        using (var streamReader = new StreamReader(stream, Encoding.UTF8))
                        {
                            var body = JsonSerializer.Create().Deserialize(streamReader, typeof(IDictionary<string, dynamic>)) as IDictionary<string, dynamic>;
                            var humidity = body["humidity"];
                            Console.WriteLine("Humidity: {0}", humidity);
                        }
                    }
                }
            }
        }
    }
}
```

#### <a name="parse-a-devices-avro-file"></a>Analizzare un file AVRO dei dispositivi

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the Avro records in the block and extract the fields.
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // records and make the function IEnumerable<AvroRecord>.
                    var deviceId = record.GetField<string>("deviceId");

                    // The device template information is stored in a sub-record
                    // under the deviceTemplate field.
                    var deviceTemplateRecord = record.GetField<AvroRecord>("deviceTemplate");
                    var templateId = deviceTemplateRecord.GetField<string>("id");
                    var templateVersion = deviceTemplateRecord.GetField<string>("version");

                    // The settings and properties are nested two levels deep.
                    // The first level indicates settings or properties.
                    // The second level indicates the type of setting or property.
                    var settingsRecord = record.GetField<AvroRecord>("settings");
                    var deviceSettingsRecord = settingsRecord.GetField<IDictionary<string, dynamic>>("device");
                    var fanSpeed = deviceSettingsRecord["fanSpeed"];
                    
                    Console.WriteLine(
                        "Device ID: {0}, Template ID: {1}, Template Version: {2}, Fan Speed: {3}",
                        deviceId,
                        templateId,
                        templateVersion,
                        fanSpeed
                    );
                }
            }
        }
    }
}

```

#### <a name="parse-a-device-templates-avro-file"></a>Analizzare un file AVRO dei modelli di dispositivo

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the Avro records in the block and extract the fields.
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // records and make the function IEnumerable<AvroRecord>.
                    var id = record.GetField<string>("id");
                    var version = record.GetField<string>("version");

                    // The settings and properties are nested two levels deep.
                    // The first level indicates settings or properties.
                    // The second level indicates the type of setting or property.
                    var settingsRecord = record.GetField<AvroRecord>("settings");
                    var deviceSettingsRecord = settingsRecord.GetField<IDictionary<string, dynamic>>("device");
                    var fanSpeed = deviceSettingsRecord["fanSpeed"];
                    
                    Console.WriteLine(
                        "ID: {1}, Version: {2}, Fan Speed: {3}",
                        id,
                        version,
                        fanSpeed
                    );
                }
            }
        }
    }
}
```

### <a name="read-avro-files-by-using-javascript"></a>Leggere i file AVRO usando JavaScript

#### <a name="install-the-avsc-package"></a>Installare il pacchetto avsc

```javascript
npm install avsc
```

#### <a name="parse-a-measurements-avro-file"></a>Analizzare un file AVRO delle misurazioni

```javascript
const avro = require('avsc');

// Read the Avro file. Parse the device ID and humidity from each record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device ID from the system properties.
        const deviceId = record.SystemProperties.connectionDeviceId;

        // Convert the body from a buffer to a string and parse it.
        const body = JSON.parse(record.Body.toString());

        // Get the humidty property from the body.
        const humidity = body.humidity;

        // Log the retrieved device ID and humidity.
        console.log(`Device ID: ${deviceId}`);
        console.log(`Humidity: ${humidity}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream.
        // Collect the records into an array and return them at the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

#### <a name="parse-a-devices-avro-file"></a>Analizzare un file AVRO dei dispositivi

```javascript
const avro = require('avsc');

// Read the Avro file. Parse the device and template identification
// information and the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device ID from the deviceId property.
        const deviceId = record.deviceId;

        // Fetch the template ID and version from the deviceTemplate property.
        const deviceTemplateId = record.deviceTemplate.id;
        const deviceTemplateVersion = record.deviceTemplate.version;

        // Get the fanSpeed from the nested device settings property.
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the retrieved device ID and humidity.
        console.log(`deviceID: ${deviceId}, Template ID: ${deviceTemplateId}, Template Version: ${deviceTemplateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream.
        // Collect the records into an array and return them at the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

#### <a name="parse-a-device-templates-avro-file"></a>Analizzare un file AVRO dei modelli di dispositivo

```javascript
const avro = require('avsc');

// Read the Avro file. Parse the device and template identification
// information and the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the template ID and version from the id and verison properties.
        const templateId = record.id;
        const templateVersion = record.version;

        // Get the fanSpeed from the nested device settings property.
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the retrieved device id and humidity.
        console.log(`Template ID: ${templateId}, Template Version: ${templateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream.
        // Collect the records into an array and return them at the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

## <a name="next-steps"></a>Passaggi successivi

Dopo avere appreso come esportare i dati, continuare con il passaggio successivo:

> [!div class="nextstepaction"]
> [Come visualizzare i dati in Power BI](howto-connect-powerbi.md)
