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
ms.openlocfilehash: 5b9564dfe40f292d289ee9ed680e816771d0b0ed
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/27/2018
ms.locfileid: "39282875"
---
# <a name="export-your-data-in-azure-iot-central"></a>Esportare i dati in Azure IoT Central

Usare l'esportazione continua dei dati per esportare periodicamente i dati nell'account di Archiviazione BLOB di Azure. È possibile scegliere di esportare **misurazioni**, **dispositivi** e **modelli di dispositivo** in file di formato [Apache AVRO](https://avro.apache.org/docs/current/index.html). Usare i dati esportati per eseguire analisi di percorsi non critici, ad esempio modelli di training in Azure Machine Learning o analisi delle tendenze a lungo termine in Power BI.

> [!Note]
> Quando si attiva l'esportazione continua dei dati, vengono recuperati solo i dati che arrivano a partire da quel momento. Attualmente non è possibile recuperare i dati che risalgono a quando l'esportazione continua dei dati era disattivata. Attivare presto l'esportazione continua dei dati per conservare una maggiore quantità di dati cronologici.

## <a name="prerequisites"></a>Prerequisiti

- Un'app di valutazione di 30 giorni estesa o un'app a pagamento
- Account Azure con sottoscrizione di Azure
- Lo stesso account Azure è un amministratore nell'app IoT Central
- Lo stesso account Azure dispone delle autorizzazioni per creare un account di archiviazione o per accedere a un account di archiviazione esistente nella stessa sottoscrizione di Azure

## <a name="types-of-data-to-export"></a>Tipi di dati da esportare

### <a name="measurements"></a>Misure

Le misurazioni inviate dai dispositivi vengono esportate nel proprio account di archiviazione. I dati delle misurazioni vengono esportati circa una volta al minuto e contengono tutti i nuovi messaggi ricevuti da IoT Central provenienti da tutti i dispositivi entro quella finestra temporale. I file AVRO esportati sono nello stesso formato dei file di messaggi esportati dal [routing dei messaggi con l'IoT Hub](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-csharp-csharp-process-d2c) nell'archivio BLOB.

> [!NOTE]
> I dispositivi che hanno inviato le misurazioni sono rappresentati da ID dispositivo (vedere sotto). Per ottenere i nomi dei dispositivi, è necessario esportare anche gli snapshot dei dispositivi. È possibile correlare ogni record di messaggio utilizzando il valore connectionDeviceId che corrisponde all'ID del dispositivo.

Segue l'esempio di un record nel file AVRO decodificato.

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

Quando si attiva l'esportazione continua dei dati per la prima volta, viene esportato uno snapshot contenente tutti i dispositivi. Sono inclusi:
- ID dei dispositivi
- Nomi dei dispositivi
- ID dei modelli di dispositivo
- Valori delle proprietà
- Valori delle impostazioni

Circa ogni minuto viene scritto un nuovo snapshot contenente:

- I nuovi dispositivi aggiunti dall'ultimo snapshot
- I dispositivi i cui valori di proprietà e impostazioni sono stati modificati dopo l'ultimo snapshot

> [!NOTE]
> I dispositivi che sono stati eliminati dopo l'ultimo snapshot non vengono esportati. Al momento negli snapshot non è presente alcun indicatore per i dispositivi che sono stati eliminati.

> [!NOTE]
> Il modello di dispositivo a cui appartiene ogni dispositivo è rappresentato da un ID del modello di dispositivo. Per ottenere il nome del modello di dispositivo, è necessario esportare anche gli snapshot dei modelli di dispositivo.

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

Quando si attiva l'esportazione continua dei dati per la prima volta, viene esportato uno snapshot contenente tutti i modelli di dispositivo. Sono inclusi: 
- ID dei modelli di dispositivo
- Tipi di dati delle misurazioni e valori min/max
- Tipi di dati delle proprietà e valori predefiniti
- Tipi di dati delle impostazioni e valori predefiniti

Circa ogni minuto viene scritto un nuovo snapshot contenente:

- I nuovi modelli di dispositivo aggiunti dall'ultimo snapshot
- I modelli di dispositivo le cui misurazioni, proprietà e impostazioni sono state modificate dopo l'ultimo snapshot

> [!NOTE]
> I modelli di dispositivo che sono stati eliminati dopo l'ultimo snapshot non vengono esportati. Al momento negli snapshot non è presente alcun indicatore per i modelli di dispositivo che sono stati eliminati.

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

## <a name="how-to-set-up-data-export"></a>Come configurare l'esportazione dei dati

1. Se non si è già provveduto, creare un account di archiviazione di Azure **nella sottoscrizione di Azure in cui risiede l'app**. [Fare clic qui](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) per passare al portale di Azure per creare un nuovo account di archiviazione di Azure.

    - Scegliere il tipo di account *Utilizzo generico* o il tipo di account *Archivio BLOB*
    - Selezionare la sottoscrizione in cui risiede l'app IoT Central. Se la sottoscrizione desiderata non viene visualizzata, potrebbe essere necessario accedere a un altro account di Azure o richiedere l'accesso alla sottoscrizione.
    - Scegliere un gruppo di risorse esistente oppure crearne uno nuovo. Informazioni su come [creare un nuovo account di archiviazione](https://aka.ms/blobdocscreatestorageaccount).

2. Creare un contenitore nell'account di archiviazione in cui esportare i dati di IoT Central. Passare all'account di archiviazione -> Esplora BLOB e creare un nuovo contenitore. ![Creare un'immagine del contenitore](media/howto-export-data/createcontainer.png)

3. Accedere all'applicazione IoT Central usando lo stesso account di Azure.
1. Andare ad Amministrazione -> Continuous Data Export (Esportazione continua dei dati).
![IoT Central CDE](media/howto-export-data/continuousdataexport.PNG)
1. Dagli elenchi a discesa selezionare l'account di archiviazione e il contenitore. Quindi, usare i controlli per attivare o disattivare i diversi tipi di dati da esportare.
1. Infine, attivare l'esportazione continua dei dati usando l'apposito controllo e premere "Save" (Salva).
1. Dopo qualche minuto i dati vengono visualizzati nell'account di archiviazione. Passare all'account di archiviazione, selezionare Esplora BLOB, selezionare il contenitore e si vedranno tre cartelle. I percorsi predefiniti per i file AVRO contenenti i diversi tipi di dati sono:
- Messaggi: **{container}/measurements/{hubname}/{YYYY}/{MM}/{dd}/{hh}/{mm}/00.avro**
- Dispositivi: **{container}/devices/{hubname}/{YYYY}/{MM}/{dd}/{hh}/{mm}/00.avro**
- Modelli di dispositivo: **{container}/deviceTemplates/{hubname}/{YYYY}/{MM}/{dd}/{hh}/{mm}/00.avro**

## <a name="how-to-read-exported-avro-files"></a>Come leggere i file di AVRO esportati

AVRO è un formato binario, pertanto i file non possono essere letti nel loro stato non elaborato. Possono essere tuttavia decodificati in formato JSON. Gli esempi seguenti illustrano come analizzare i file AVRO con le misurazioni, i dispositivi e i modelli di dispositivo usando gli esempi precedenti.

## <a name="python"></a>Python

### <a name="install-pandas-and-the-pandaavro-package"></a>Installare il pacchetto Pandas e PandaAvro:

```python
pip install pandas
pip install pandavro
```
### <a name="parse-measurements-avro-file"></a>Analizzare il file AVRO con le misurazioni

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the avro file into a pandas DataFrame where each record is
    # a single row
    measurements = pdx.from_avro(filePath)

    # In this example, we create a new DataFrame and load a series for each
    # column we map into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The SystemProperties column contains a dictionary with the device id
    # located under the "connectionDeviceId" key.
    transformed["device_id"] = measurements["SystemProperties"].apply(lambda x: x["connectionDeviceId"])

    # The Body column is a series of utf-8 bytes that is stringified and parsed
    # as json. In this example, we pull the "humidity" property off of each one
    # to get the humidity field.
    transformed["humidity"] = measurements["Body"].apply(lambda x: json.loads(bytes(x).decode('utf-8'))["humidity"])

    # Finally, we print the new DataFrame with our device ids and humidities
    print(transformed)

```
### <a name="parse-devices-avro-file"></a>Analizzare il file di AVRO con i dispositivi

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the avro file into a pandas DataFrame where each record is
    # a single row
    devices = pdx.from_avro(filePath)

    # In this example, we create a new DataFrame and load a series for each
    # column we map into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The device id is available directly in the "id" column
    transformed["device_id"] = devices["id"]

    # The template id and version are present in a dictionary under the
    # deviceTemplate column
    transformed["template_id"] = devices["deviceTemplate"].apply(lambda x: x["id"])
    transformed["template_version"] = devices["deviceTemplate"].apply(lambda x: x["version"])

    # The fanSpeed setting value is located in a nested dictionary under the
    # settings column
    transformed["fan_speed"] = devices["settings"].apply(lambda x: x["device"]["fanSpeed"])

    # Finally, we print the new DataFrame with our device and template
    # information, along with the value of the fan speed
    print(transformed)

```

### <a name="parse-device-templates-avro-file"></a>Analizzare il file di AVRO con i modelli di dispositivo

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the avro file into a pandas DataFrame where each record is
    # a single row
    templates = pdx.from_avro(filePath)

    # In this example, we create a new DataFrame and load a series for each
    # column we map into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The template and version are available directly in the "id" and "version"
    # columns, respectively
    transformed["template_id"] = templates["id"]
    transformed["template_version"] = templates["version"]

    # The fanSpeed setting value is located in a nested dictionary under the
    # settings column
    transformed["fan_speed"] = templates["settings"].apply(lambda x: x["device"]["fanSpeed"])

    # Finally, we print the new DataFrame with our device and template
    # information, along with the value of the fan speed
    print(transformed)
```

## <a name="c"></a>C#

### <a name="install-microsofthadoopavro"></a>Installare Microsoft.Hadoop.Avro

```csharp
Install-Package Microsoft.Hadoop.Avro -Version 1.5.6
```

### <a name="parse-measurements-avro-file"></a>Analizzare il file AVRO con le misurazioni

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
            // For one Avro Container, it may contain multiple blocks
            // Loop through each block within the container
            while (reader.MoveNext())
            {
                // Loop through Avro record inside the block and extract the fields
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

### <a name="parse-devices-avro-file"></a>Analizzare il file di AVRO con i dispositivi

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro Container, it may contains multiple blocks
            // Loop through each block within the container
            while (reader.MoveNext())
            {
                // Loop through Avro record inside the block and extract the
                // fields from it
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // record and make the function IEnumerable<AvroRecord>
                    var deviceId = record.GetField<string>("id");

                    // The device template information is stored in a sub-record
                    // under the "deviceTemplate" field.
                    var deviceTemplateRecord = record.GetField<AvroRecord>("deviceTemplate");
                    var templateId = deviceTemplateRecord.GetField<string>("id");
                    var templateVersion = deviceTemplateRecord.GetField<string>("version");

                    // The settings and properties are nested two levels deep,
                    // with the first level indicating settings or properties
                    // and the second indicating the kind of setting/property
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
### <a name="parse-device-templates-avro-file"></a>Analizzare il file di AVRO con i modelli di dispositivo

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro Container, it may contains multiple blocks
            // Loop through each block within the container
            while (reader.MoveNext())
            {
                // Loop through Avro record inside the block and extract the
                // fields from it
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // record and make the function IEnumerable<AvroRecord>
                    var id = record.GetField<string>("id");
                    var version = record.GetField<string>("version");

                    // The settings and properties are nested two levels deep,
                    // with the first level indicating settings or properties
                    // and the second indicating the kind of setting/property
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

## <a name="javascript"></a>JavaScript

### <a name="install-avsc"></a>Installare avsc

```javascript
npm install avsc
```

### <a name="parse-measurements-avro-file"></a>Analizzare il file AVRO con le misurazioni

```javascript
const avro = require('avsc');

// Read the avro file and parse the device id and humidity from each record
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device id from the system properties
        const deviceId = record.SystemProperties.connectionDeviceId;

        // Convert the Body from a Buffer to a string and parse it
        const body = JSON.parse(record.Body.toString());

        // Get the humidty property off of the body
        const humidity = body.humidity;

        // Log the device id and humidity we found
        console.log(`Device ID: ${deviceId}`);
        console.log(`Humidity: ${humidity}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream. We
        // collect them up into an array and return them when we get to the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

### <a name="parse-devices-avro-file"></a>Analizzare il file di AVRO con i dispositivi

```javascript
const avro = require('avsc');

// Read the avro file and parse the device and template identification info as
// well as the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device id from the id property
        const deviceId = record.id;

        // Fetch the tempalte id and version from the deviceTemplate property
        const deviceTemplateId = record.deviceTemplate.id;
        const deviceTemplateVersion = record.deviceTemplate.version;

        // Get the fanSpeed off the nested device settings property
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the device id and humidity we found
        console.log(`ID: ${deviceId}, Template ID: ${deviceTemplateId}, Template Version: ${deviceTemplateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream. We
        // collect them up into an array and return them when we get to the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

### <a name="parse-device-templates-avro-file"></a>Analizzare il file di AVRO con i modelli di dispositivo

```javascript
const avro = require('avsc');

// Read the avro file and parse the device and template identification info as
// well as the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the template id and version from the id and verison properties
        const templateId = record.id;
        const templateVersion = record.version;

        // Get the fanSpeed off the nested device settings property
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the device id and humidity we found
        console.log(`Template ID: ${templateId}, Template Version: ${templateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream. We
        // collect them up into an array and return them when we get to the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo aver appreso come esportare i dati, il prossimo passo suggerito è:

> [!div class="nextstepaction"]
> [Come visualizzare i dati in Power BI](howto-connect-powerbi.md)