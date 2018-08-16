---
title: Esportare i dati in Azure IoT Central | Microsoft Docs
description: Come esportare dati dall'applicazione Azure IoT Central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/3/2018
ms.topic: article
ms.prod: azure-iot-central
manager: peterpr
ms.openlocfilehash: 3ca2bc56c03e5bbabbd9b2f17edc621bdd94b02f
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2018
ms.locfileid: "39622484"
---
# <a name="export-your-data-in-azure-iot-central"></a>Esportare i dati in Azure IoT Central

Questo articolo descrive come usare la funzionalità di esportazione continua dei dati in Azure IoT Central per esportare periodicamente i dati nell'account di archiviazione BLOB di Azure. È possibile scegliere di esportare **misurazioni**, **dispositivi** e **modelli di dispositivo** in file nel formato [Apache AVRO](https://avro.apache.org/docs/current/index.html). I dati esportati possono essere usati per eseguire analisi di percorsi non critici, ad esempio modelli di training in Azure Machine Learning o analisi delle tendenze a lungo termine in Microsoft Power BI.

> [!Note]
> Quando si attiva l'esportazione continua dei dati, si recuperano solo i dati a partire dal momento dell'attivazione. Attualmente non è possibile recuperare i dati relativi a un periodo in cui l'esportazione continua era disattivata. Per conservare una maggiore quantità di dati cronologici, attivare presto l'esportazione continua dei dati.

## <a name="prerequisites"></a>Prerequisiti

- Un'applicazione IoT Central con versione di valutazione di 30 giorni o un'applicazione a pagamento.
- Un account Azure con una sottoscrizione di Azure.
- Lo stesso account Azure è un amministratore nell'applicazione IoT Central.
- Lo stesso account Azure dispone delle autorizzazioni per creare un account di archiviazione o per accedere a un account di archiviazione esistente nella stessa sottoscrizione di Azure.

## <a name="types-of-data-to-export"></a>Tipi di dati da esportare

### <a name="measurements"></a>Misurazioni

Le misurazioni inviate dai dispositivi vengono esportate nell'account di archiviazione una volta al minuto. I dati contengono tutti i nuovi messaggi che IoT Central ha ricevuto da tutti i dispositivi durante tale periodo. I file AVRO esportati sono nello stesso formato dei file dei messaggi esportati dal [routing dei messaggi dell'hub IoT](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-csharp-csharp-process-d2c) nell'archiviazione BLOB.

> [!NOTE]
> I dispositivi che inviano le misurazioni sono rappresentati da ID dispositivo (vedere le sezioni seguenti). Per ottenere i nomi dei dispositivi, esportare i relativi snapshot. Correlare ogni record di messaggio usando il valore **connectionDeviceId** corrispondente all'ID del dispositivo.

L'esempio seguente mostra un record in un file AVRO decodificato:

```json
{
    "EnqueuedTimeUtc": "2018-06-11T00:00:08.2250000Z",
    "Properties": {},
    "SystemProperties": {
        "connectionDeviceId": "2383d8ba-c98c-403a-b4d5-8963859643bb",
        "connectionAuthMethod": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
        "connectionDeviceGenerationId": "636614021491644195",
        "enqueuedTime": "2018-06-11T00:00:08.2250000Z"
    },
    "Body": "{\"humidity\":80.59100954598546,\"magnetometerX\":0.29451796907056726,\"magnetometerY\":0.5550332126050068,\"magnetometerZ\":-0.04116681874733441,\"connectivity\":\"connected\",\"opened\":\"triggered\"}"
}
```

### <a name="devices"></a>Dispositivi

Quando si attiva l'esportazione continua dei dati per la prima volta, viene esportato un singolo snapshot con tutti i dispositivi. Lo snapshot include:
- ID dei dispositivi
- Nomi dei dispositivi
- ID dei modelli di dispositivo
- Valori delle proprietà
- Valori delle impostazioni

Viene creato un nuovo snapshot una volta al minuto. Lo snapshot include:

- I nuovi dispositivi aggiunti dall'ultimo snapshot
- I dispositivi i cui valori di proprietà e impostazioni sono stati modificati dall'ultimo snapshot

> [!NOTE]
> I dispositivi eliminati dall'ultimo snapshot non vengono esportati. Gli snapshot non hanno attualmente indicatori per i dispositivi eliminati.
>
> Il modello di dispositivo a cui appartiene ogni dispositivo è rappresentato da un ID del modello di dispositivo. Per ottenere il nome del modello di dispositivo, esportare gli snapshot dei modelli di dispositivo.

Ogni record del file AVRO decodificato è simile al seguente:

```json
{
    "id": "2383d8ba-c98c-403a-b4d5-8963859643bb",
    "name": "Refrigerator 2",
    "simulated": true,
    "deviceTemplate": {
        "id": "c318d580-39fc-4aca-b995-843719821049",
        "version": "1.0.0"
    },
    "properties": {
        "cloud": {
            "location": "New York",
            "maintCon": true,
            "tempThresh": 20
        },
        "device": {
            "lastReboot": "2018-02-09T22:22:47.156Z"
        }
    },
    "settings": {
        "device": {
            "fanSpeed": 0
        }
    }
}
```

### <a name="device-templates"></a>Modelli di dispositivo

Quando si attiva l'esportazione continua dei dati per la prima volta, viene esportato un singolo snapshot con tutti i modelli di dispositivo. Lo snapshot include: 
- ID dei modelli di dispositivo
- Tipi di dati delle misurazioni e valori min/max
- Tipi di dati delle proprietà e valori predefiniti
- Tipi di dati delle impostazioni e valori predefiniti

Viene creato un nuovo snapshot una volta al minuto. Lo snapshot include:

- I nuovi modelli di dispositivo aggiunti dall'ultimo snapshot
- I modelli di dispositivo le cui misurazioni, proprietà e impostazioni sono state modificate dall'ultimo snapshot

> [!NOTE]
> I modelli di dispositivo eliminati dall'ultimo snapshot non vengono esportati. Gli snapshot non hanno attualmente indicatori per i modelli di dispositivo eliminati.

Ogni record del file AVRO decodificato è simile al seguente:

```json
{
    "id": "c318d580-39fc-4aca-b995-843719821049",
    "name": "Refrigerated Vending Machine",
    "version": "1.0.0",
    "measurements": {
        "telemetry": {
            "humidity": {
                "dataType": "double",
                "name": "Humidity"
            },
            "magnetometerX": {
                "dataType": "double",
                "name": "Magnetometer X"
            },
            "magnetometerY": {
                "dataType": "double",
                "name": "Magnetometer Y"
            },
            "magnetometerZ": {
                "dataType": "double",
                "name": "Magnetometer Z"
            }
        },
        "states": {
            "connectivity": {
                "dataType": "enum",
                "name": "Connectivity"
            }
        },
        "events": {
            "opened": {
                "name": "Door Opened",
                "category": "informational"
            }
        }
    },
    "settings": {
        "device": {
            "fanSpeed": {
                "dataType": "double",
                "name": "Fan Speed",
                "initialValue": 0
            }
        }
    },
    "properties": {
        "cloud": {
            "location": {
                "dataType": "string",
                "name": "Location",
                "initialValue": "Seattle"
            },
            "maintCon": {
                "dataType": "boolean",
                "name": "Maintenance Contract",
                "initialValue": true
            },
            "tempThresh": {
                "dataType": "double",
                "name": "Temperature Alert Threshold",
                "initialValue": 30
            }
        },
        "device": {
            "lastReboot": {
                "dataType": "dateTime",
                "name": "Last Reboot"
            }
        }
    }
}
```

## <a name="set-up-continuous-data-export"></a>Configurare l'esportazione continua dei dati

1. Se non si ha un account di archiviazione di Azure, [crearne uno nuovo](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) nel portale di Azure. Creare l'account di archiviazione **nella sottoscrizione di Azure con l'applicazione IoT Central**.
    - Come tipo di account scegliere **Utilizzo generico** o **Archiviazione BLOB**.
    - Selezionare la sottoscrizione in cui si trova l'applicazione IoT Central. Se la sottoscrizione non viene visualizzata, può essere necessario accedere a un altro account Azure o richiedere l'accesso alla sottoscrizione.
    - Scegliere un gruppo di risorse esistente oppure crearne uno nuovo. Vedere le istruzioni per [creare un nuovo account di archiviazione](https://aka.ms/blobdocscreatestorageaccount).

2. Creare un contenitore nell'account di archiviazione in cui esportare i dati di IoT Central. Passare all'account di archiviazione. In **Servizio BLOB** selezionare **Esplora BLOB**. Selezionare **Contenitore** per creare un nuovo contenitore.

   ![Creare un contenitore](media/howto-export-data/createcontainer.png)

3. Accedere all'applicazione IoT Central usando lo stesso account Azure.

4. In **Amministrazione** selezionare **Esporta dati**.

   ![Configurare l'esportazione continua dei dati](media/howto-export-data/continuousdataexport.PNG)

5. Nell'elenco a discesa **Account di archiviazione** selezionare il proprio account di archiviazione. Nell'elenco a discesa **Contenitore** selezionare il contenitore. In **Data to export** (Dati da esportare) specificare ogni tipo di dati da esportare impostando il tipo su **On** (Attivato).

6. Per attivare l'esportazione continua dei dati, impostare **Data export** (Esportazione dati) su **On** (Attivato). Selezionare **Salva**.

7. Dopo alcuni minuti, i dati vengono visualizzati nell'account di archiviazione. Passare all'account di archiviazione. Selezionare **Esplora BLOB** e quindi il contenitore. Verranno visualizzate tre cartelle per i dati di esportazione. I percorsi predefiniti per i file AVRO con i dati di esportazione sono i seguenti:
    - Messaggi: {contenitore}/measurements/{nomehub}/{AAAA}/{MM}/{gg}/{hh}/{mm}/00.avro
    - Dispositivi: {contenitore}/devices/{nomehub}/{AAAA}/{MM}/{gg}/{hh}/{mm}/00.avro
    - Modelli di dispositivo: {contenitore}/deviceTemplates/{nomehub}/{AAAA}/{MM}/{gg}/{hh}/{mm}/00.avro

## <a name="read-exported-avro-files"></a>Leggere i file AVRO esportati

AVRO è un formato binario, pertanto i file non possono essere letti nello stato non elaborato. I file possono essere decodificati in formato JSON. Gli esempi seguenti illustrano come analizzare i file AVRO con le misurazioni, i dispositivi e i modelli di dispositivo. Gli esempi corrispondono a quelli descritti nella sezione precedente.

### <a name="read-avro-files-by-using-python"></a>Leggere i file AVRO usando Python

#### <a name="install-pandas-and-the-pandavro-package"></a>Installare pandas e il pacchetto pandavro

```python
pip install pandas
pip install pandavro
```

#### <a name="parse-a-measurements-avro-file"></a>Analizzare un file AVRO con le misurazioni

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the AVRO file into a pandas DataFrame
    # where each record is a single row.
    measurements = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The SystemProperties column contains a dictionary
    # with the device ID located under the connectionDeviceId key.
    transformed["device_id"] = measurements["SystemProperties"].apply(lambda x: x["connectionDeviceId"])

    # The Body column is a series of UTF-8 bytes that is stringified
    # and parsed as JSON. This example pulls the humidity property
    # from each column to get the humidity field.
    transformed["humidity"] = measurements["Body"].apply(lambda x: json.loads(bytes(x).decode('utf-8'))["humidity"])

    # Finally, print the new DataFrame with our device IDs and humidities.
    print(transformed)

```

#### <a name="parse-a-devices-avro-file"></a>Analizzare un file AVRO con i dispositivi

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the AVRO file into a pandas DataFrame
    # where each record is a single row.
    devices = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The device ID is available in the id column.
    transformed["device_id"] = devices["id"]

    # The template ID and version are present in a dictionary under
    # the deviceTemplate column.
    transformed["template_id"] = devices["deviceTemplate"].apply(lambda x: x["id"])
    transformed["template_version"] = devices["deviceTemplate"].apply(lambda x: x["version"])

    # The fanSpeed setting value is located in a nested dictionary
    # under the settings column.
    transformed["fan_speed"] = devices["settings"].apply(lambda x: x["device"]["fanSpeed"])

    # Finally, print the new DataFrame with our device and template
    # information, along with the value of the fan speed.
    print(transformed)

```

#### <a name="parse-a-device-templates-avro-file"></a>Analizzare un file AVRO con i modelli di dispositivo

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the AVRO file into a pandas DataFrame
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
    transformed["fan_speed"] = templates["settings"].apply(lambda x: x["device"]["fanSpeed"])

    # Finally, print the new DataFrame with our device and template
    # information, along with the value of the fan speed.
    print(transformed)
```

### <a name="read-avro-files-by-using-c"></a>Leggere i file AVRO usando C#

#### <a name="install-the-microsofthadoopavro-package"></a>Installare il pacchetto Microsoft.Hadoop.Avro

```csharp
Install-Package Microsoft.Hadoop.Avro -Version 1.5.6
```

#### <a name="parse-a-measurements-avro-file"></a>Analizzare un file AVRO con le misurazioni

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
            // For one AVRO container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the AVRO records in the block and extract the fields.
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

#### <a name="parse-a-devices-avro-file"></a>Analizzare un file AVRO con i dispositivi

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one AVRO container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the AVRO records in the block and extract the fields.
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // records and make the function IEnumerable<AvroRecord>.
                    var deviceId = record.GetField<string>("id");

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
                        "ID: {0}, Template ID: {1}, Template Version: {2}, Fan Speed: {3}",
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

#### <a name="parse-a-device-templates-avro-file"></a>Analizzare un file AVRO con i modelli di dispositivo

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one AVRO container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the AVRO records in the block and extract the fields.
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

#### <a name="parse-a-measurements-avro-file"></a>Analizzare un file AVRO con le misurazioni

```javascript
const avro = require('avsc');

// Read the AVRO file. Parse the device ID and humidity from each record.
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

#### <a name="parse-a-devices-avro-file"></a>Analizzare un file AVRO con i dispositivi

```javascript
const avro = require('avsc');

// Read the AVRO file. Parse the device and template identification
// information and the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device ID from the id property.
        const deviceId = record.id;

        // Fetch the template ID and version from the deviceTemplate property.
        const deviceTemplateId = record.deviceTemplate.id;
        const deviceTemplateVersion = record.deviceTemplate.version;

        // Get the fanSpeed from the nested device settings property.
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the retrieved device ID and humidity.
        console.log(`ID: ${deviceId}, Template ID: ${deviceTemplateId}, Template Version: ${deviceTemplateVersion}, Fan Speed: ${fanSpeed}`);
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

#### <a name="parse-a-device-templates-avro-file"></a>Analizzare un file AVRO con i modelli di dispositivo

```javascript
const avro = require('avsc');

// Read the AVRO file. Parse the device and template identification
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
