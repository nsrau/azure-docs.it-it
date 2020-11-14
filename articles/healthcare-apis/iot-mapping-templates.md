---
title: "Concetti: mapping dei modelli nel connettore Azure Internet per FHIR (anteprima) funzionalità dell'API di Azure per FHIR"
description: Informazioni su come creare due tipi di modelli di mapping nel connettore Azure per FHIR (anteprima). Il modello di mapping del dispositivo trasforma i dati del dispositivo in uno schema normalizzato. Il modello di mapping FHIR trasforma un messaggio normalizzato in una risorsa di osservazione basata su FHIR.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: punagpal
ms.openlocfilehash: 63484361a6d5a331fd9dc646c53627918ce8b246
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2020
ms.locfileid: "94630550"
---
# <a name="azure-iot-connector-for-fhir-preview-mapping-templates"></a>Modelli di mapping del connettore Azure IoT per FHIR (anteprima)
Questo articolo illustra come configurare il connettore Azure per le risorse di interoperabilità sanitaria veloci (FHIR&#174;) * usando i modelli di mapping.

Il connettore Azure per FHIR richiede due tipi di modelli di mapping basati su JSON. Il primo tipo, ovvero il **mapping dei dispositivi** , è responsabile del mapping dei payload del dispositivo inviati all' `devicedata` endpoint dell'hub eventi di Azure. Estrae i tipi, gli identificatori di dispositivo, la data e l'ora di misurazione e i valori di misurazione. Il secondo tipo, **FHIR mapping** , controlla il mapping per la risorsa FHIR. Consente la configurazione della lunghezza del periodo di osservazione, il tipo di dati FHIR usato per archiviare i valori e i codici terminologici. 

I modelli di mapping sono composti in un documento JSON basato sul tipo. Questi documenti JSON vengono quindi aggiunti al connettore Azure per FHIR tramite il portale di Azure. Il documento di mapping dei dispositivi viene aggiunto tramite la pagina **Configura mapping dei dispositivi** e il documento di mapping di FHIR tramite la pagina **Configura mapping FHIR** .

> [!NOTE]
> I modelli di mapping vengono archiviati in un archivio BLOB sottostante e caricati dal BLOB per ogni esecuzione di calcolo. Una volta aggiornati, i risultati dovrebbero essere applicati immediatamente. 

## <a name="device-mapping"></a>Mapping dispositivo
Il mapping dei dispositivi fornisce funzionalità di mapping per estrarre il contenuto del dispositivo in un formato comune per un'ulteriore valutazione. Ogni messaggio ricevuto viene valutato rispetto a tutti i modelli. Questo approccio consente di proiettare un singolo messaggio in ingresso in più messaggi in uscita, che in seguito vengono mappati a osservazioni diverse in FHIR. Il risultato è un oggetto dati normalizzato che rappresenta il valore o i valori analizzati dai modelli. Il modello di dati normalizzato ha alcune proprietà obbligatorie che devono essere trovate ed estratte:

| Proprietà | Descrizione |
| - | - |
|**Tipo**|Nome/tipo per classificare la misura. Questo valore viene usato per eseguire l'associazione al modello di mapping FHIR obbligatorio.  Più modelli possono restituire lo stesso tipo, consentendo di eseguire il mapping di rappresentazioni diverse tra più dispositivi a un singolo output comune.|
|**OccurenceTimeUtc**|Data e ora in cui si è verificata la misurazione.|
|**DeviceId**|Identificatore del dispositivo. Questo valore deve corrispondere a un identificatore nella risorsa del dispositivo esistente nel server FHIR di destinazione.|
 |**Proprietà**|Estrarre almeno una proprietà in modo che il valore possa essere salvato nella risorsa di osservazione creata.  Le proprietà sono una raccolta di coppie chiave-valore estratte durante la normalizzazione.|

Di seguito è riportato un esempio concettuale di ciò che accade durante la normalizzazione.

![Esempio di normalizzazione](media/concepts-iot-mapping-templates/normalization-example.png)

Il payload del contenuto è un messaggio di hub eventi di Azure, composto da tre parti: Body, Properties e SystemProperties. `Body`È una matrice di byte che rappresenta una stringa con codifica UTF-8. Durante la valutazione del modello, la matrice di byte viene convertita automaticamente nel valore stringa. `Properties` è una raccolta di valori chiave che può essere utilizzata dal creatore del messaggio. `SystemProperties` è anche una raccolta di valori chiave riservata dal framework di hub eventi di Azure con le voci popolate automaticamente.

```json
{
    "Body": {
        "content": "value"
    },
    "Properties": {
        "key1": "value1",
        "key2": "value2"
    },
    "SystemProperties": {
        "x-opt-sequence-number": 1,
        "x-opt-enqueued-time": "2019-02-01T22:46:01.8750000Z",
        "x-opt-offset": 1,
        "x-opt-partition-key": "1"
    }
}
```

### <a name="mapping-with-json-path"></a>Mapping con percorso JSON
I due tipi di modelli di contenuto del dispositivo supportati attualmente si basano sul percorso JSON in modo che corrispondano al modello richiesto e ai valori estratti. Altre informazioni sul percorso JSON sono disponibili [qui](https://goessner.net/articles/JsonPath/). Entrambi i tipi di modello usano l' [implementazione .NET JSON](https://www.newtonsoft.com/json/help/html/QueryJsonSelectTokenJsonPath.htm) per la risoluzione delle espressioni di percorso JSON.

#### <a name="jsonpathcontenttemplate"></a>JsonPathContentTemplate
Il JsonPathContentTemplate consente la corrispondenza e l'estrazione di valori da un messaggio di hub eventi usando il percorso JSON.

| Proprietà | Descrizione |<div style="width:150px">Esempio</div>
| --- | --- | --- 
|**TypeName**|Tipo da associare alle misurazioni corrispondenti al modello.|`heartrate`
|**TypeMatchExpression**|Espressione di percorso JSON valutata rispetto al payload dell'hub eventi. Se viene trovato un JToken corrispondente, il modello viene considerato una corrispondenza. Tutte le espressioni successive vengono valutate in base ai JToken estratti corrispondenti.|`$..[?(@heartRate)]`
|**TimestampExpression**|Espressione del percorso JSON per estrarre il valore di timestamp per il OccurenceTimeUtc della misura.|`$.endDate`
|**DeviceIdExpression**|Espressione del percorso JSON per estrarre l'identificatore del dispositivo.|`$.deviceId`
|**PatientIdExpression**|*Facoltativo* : espressione del percorso JSON per estrarre l'identificatore del paziente.|`$.patientId`
|**EncounterIdExpression**|*Facoltativo* : espressione del percorso JSON per estrarre l'identificatore di verifica.|`$.encounterId`
|**Valori []. ValueName**|Nome da associare al valore estratto dall'espressione successiva. Utilizzato per associare il valore o il componente richiesto nel modello di mapping FHIR. |`hr`
|**Valori []. ValueExpression**|Espressione del percorso JSON per estrarre il valore richiesto.|`$.heartRate`
|**Valori []. Obbligatorio**|Richiede la presenza del valore nel payload.  Se non viene trovato, una misura non verrà generata e verrà generata un'eccezione InvalidOperationException.|`true`

##### <a name="examples"></a>Esempio
---
**Frequenza cardiaca**

*Message*
```json
{
    "Body": {
        "heartRate": "78",
        "endDate": "2019-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*Modello*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
---
**Pressione del sangue**

*Message*
```json
{
    "Body": {
        "systolic": "123",
        "diastolic" : "87",
        "endDate": "2019-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*Modello*
```json
{
    "typeName": "bloodpressure",
    "typeMatchExpression": "$..[?(@systolic && @diastolic)]",
    "deviceIdExpression": "$.deviceid",
    "timestampExpression": "$.endDate",
    "values": [
        {
            "required": "true",
            "valueExpression": "$.systolic",
            "valueName": "systolic"
        },
        {
            "required": "true",
            "valueExpression": "$.diastolic",
            "valueName": "diastolic"
        }
    ]
}
```
---

**Proiettare più misurazioni da un singolo messaggio**

*Message*
```json
{
    "Body": {
        "heartRate": "78",
        "steps": "2",
        "endDate": "2019-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*Modello 1*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
*Modello 2*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "stepcount",
        "typeMatchExpression": "$..[?(@steps)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.steps",
                "valueName": "steps"
            }
        ]
    }
}
```
---

**Proietta più misurazioni da array nel messaggio**

*Message*
```json
{
    "Body": [
        {
            "heartRate": "78",
            "endDate": "2019-02-01T22:46:01.8750000Z",
            "deviceId": "device123"
        },
        {
            "heartRate": "81",
            "endDate": "2019-02-01T23:46:01.8750000Z",
            "deviceId": "device123"
        },
        {
            "heartRate": "72",
            "endDate": "2019-02-01T24:46:01.8750000Z",
            "deviceId": "device123"
        }
    ],
    "Properties": {},
    "SystemProperties": {}
}
```
*Modello*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
#### <a name="iotjsonpathcontenttemplate"></a>IotJsonPathContentTemplate
IotJsonPathContentTemplate è simile a JsonPathContentTemplate, ad eccezione di DeviceIdExpression e TimestampExpression non sono obbligatori.

Quando si usa questo modello, il presupposto è che i messaggi valutati siano stati inviati usando gli [SDK per dispositivi dell'hub Azure](../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks). Quando si usano questi SDK, l'identità del dispositivo (presupponendo che l'identificatore del dispositivo dall'hub Azure per le risorse di Azure sia registrato come identificatore per una risorsa del dispositivo nel server FHIR di destinazione) e che il timestamp del messaggio sia noto. Se si usano gli SDK per dispositivi dell'hub Azure, ma si usano proprietà personalizzate nel corpo del messaggio per l'identità del dispositivo o il timestamp di misurazione, è comunque possibile usare JsonPathContentTemplate.

*Nota: quando si usa IotJsonPathContentTemplate, TypeMatchExpression deve essere risolto nell'intero messaggio come JToken. Vedere gli esempi seguenti.* 
##### <a name="examples"></a>Esempio
---
**Frequenza cardiaca**

*Message*
```json
{
    "Body": {
        "heartRate": "78"        
    },
    "Properties": {
        "iothub-creation-time-utc" : "2019-02-01T22:46:01.8750000Z"
    },
    "SystemProperties": {
        "iothub-connection-device-id" : "device123"
    }
}
```
*Modello*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@Body.heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.Body.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
---
**Pressione del sangue**

*Message*
```json
{
    "Body": {
        "systolic": "123",
        "diastolic" : "87"
    },
    "Properties": {
        "iothub-creation-time-utc" : "2019-02-01T22:46:01.8750000Z"
    },
    "SystemProperties": {
        "iothub-connection-device-id" : "device123"
    }
}
```
*Modello*
```json
{
    "typeName": "bloodpressure",
    "typeMatchExpression": "$..[?(@Body.systolic && @Body.diastolic)]",
    "values": [
        {
            "required": "true",
            "valueExpression": "$.Body.systolic",
            "valueName": "systolic"
        },
        {
            "required": "true",
            "valueExpression": "$.Body.diastolic",
            "valueName": "diastolic"
        }
    ]
}
```

## <a name="fhir-mapping"></a>Mapping FHIR
Una volta che il contenuto del dispositivo è stato estratto in un modello normalizzato, i dati vengono raccolti e raggruppati in base all'identificatore del dispositivo, al tipo di misurazione e al periodo di tempo. L'output di questo raggruppamento viene inviato per la conversione in una risorsa FHIR ([osservazione](https://www.hl7.org/fhir/observation.html) attualmente). Il modello di mapping di FHIR controlla il modo in cui viene eseguito il mapping dei dati in un'osservazione di FHIR. È necessario creare un'osservazione per un momento specifico o per un periodo di un'ora? Quali codici devono essere aggiunti all'osservazione? Il valore deve essere rappresentato come [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData) o [Quantity](https://www.hl7.org/fhir/datatypes.html#Quantity)? Questi tipi di dati sono tutte opzioni dei controlli di configurazione del mapping di FHIR.

### <a name="codevaluefhirtemplate"></a>CodeValueFhirTemplate
Il CodeValueFhirTemplate è attualmente l'unico modello supportato nel mapping di FHIR in questo momento.  Consente di definire i codici, il periodo effettivo e il valore dell'osservazione. Sono supportati più tipi di valore: [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData), [CodeableConcept](https://www.hl7.org/fhir/datatypes.html#CodeableConcept)e [Quantity](https://www.hl7.org/fhir/datatypes.html#Quantity). Insieme a questi valori configurabili, l'identificatore per la risorsa di osservazione e il collegamento al dispositivo e alle risorse del paziente appropriati vengono gestiti automaticamente.

| Proprietà | Descrizione 
| --- | ---
|**TypeName**| Tipo di misura a cui questo modello deve essere associato. Deve essere presente almeno un modello di mapping dei dispositivi che restituisce questo tipo.
|**PeriodInterval**|Periodo di tempo durante il quale l'osservazione creata deve rappresentare. I valori supportati sono 0 (un'istanza), 60 (un'ora), 1440 (al giorno).
|**Categoria**|Un numero qualsiasi di [CodeableConcepts](http://hl7.org/fhir/datatypes-definitions.html#codeableconcept) per classificare il tipo di osservazione creato.
|**Codici**|Una o più [codifiche](http://hl7.org/fhir/datatypes-definitions.html#coding) da applicare all'osservazione creata.
|**Codici []. Codice**|Codice per la [codifica](http://hl7.org/fhir/datatypes-definitions.html#coding).
|**Codici []. Sistema**|Sistema per la [codifica](http://hl7.org/fhir/datatypes-definitions.html#coding).
|**Codici []. Visualizzare**|Visualizzazione per la [codifica](http://hl7.org/fhir/datatypes-definitions.html#coding).
|**Valore**|Valore da estrarre e rappresentare nell'osservazione. Per altre informazioni, vedere [modelli di tipi di valore](#valuetypes).
|**Componenti**|*Facoltativo:* Uno o più componenti da creare sull'osservazione.
|**Componenti []. Codici**|Una o più [codifiche](http://hl7.org/fhir/datatypes-definitions.html#coding) da applicare al componente.
|**Componenti []. Valore**|Valore da estrarre e rappresentare nel componente. Per altre informazioni, vedere [modelli di tipi di valore](#valuetypes).

### <a name="value-type-templates"></a>Modelli di tipo valore <a name="valuetypes"></a>
Di seguito sono riportati i modelli di tipo valore attualmente supportati. In futuro, è possibile aggiungere altri modelli.
#### <a name="sampleddata"></a>SampledData
Rappresenta il tipo di dati FHIR di [SampledData](http://hl7.org/fhir/datatypes.html#SampledData) . Le misurazioni di osservazione vengono scritte in un flusso di valori a partire da un punto nel tempo e vengono incrementate in base al periodo definito. Se non è presente alcun valore, `E` verrà scritto un oggetto nel flusso di dati. Se il periodo è tale che altri due valori occupano la stessa posizione nel flusso di dati, viene usato il valore più recente. La stessa logica viene applicata quando viene aggiornata un'osservazione che usa SampledData.

| Proprietà | Descrizione 
| --- | ---
|**DefaultPeriod**|Periodo predefinito in millisecondi da utilizzare. 
|**Unità**|Unità da impostare sull'origine del SampledData. 

#### <a name="quantity"></a>Quantity
Rappresenta il tipo di dati FHIR della [quantità](http://hl7.org/fhir/datatypes.html#Quantity) . Se nel raggruppamento è presente più di un valore, viene utilizzato solo il primo valore. Quando arriva un nuovo valore che esegue il mapping alla stessa osservazione, sovrascriverà il valore precedente.

| Proprietà | Descrizione 
| --- | --- 
|**Unità**| Rappresentazione unità.
|**Codice**| Formato codificato dell'unità.
|**Sistema**| Sistema che definisce il form dell'unità codificata.

### <a name="codeableconcept"></a>CodeableConcept
Rappresenta il tipo di dati FHIR di [CodeableConcept](http://hl7.org/fhir/datatypes.html#CodeableConcept) . Il valore effettivo non viene utilizzato.

| Proprietà | Descrizione 
| --- | --- 
|**Text**|Rappresentazione in testo normale. 
|**Codici**|Una o più [codifiche](http://hl7.org/fhir/datatypes-definitions.html#coding) da applicare all'osservazione creata.
|**Codici []. Codice**|Codice per la [codifica](http://hl7.org/fhir/datatypes-definitions.html#coding).
|**Codici []. Sistema**|Sistema per la [codifica](http://hl7.org/fhir/datatypes-definitions.html#coding).
|**Codici []. Visualizzare**|Visualizzazione per la [codifica](http://hl7.org/fhir/datatypes-definitions.html#coding).

### <a name="examples"></a>Esempio
**Frequenza cardiaca-SampledData**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "8867-4",
                "system": "http://loinc.org",
                "display": "Heart rate"
            }
        ],
        "periodInterval": 60,
        "typeName": "heartrate",
        "value": {
            "defaultPeriod": 5000,
            "unit": "count/min",
            "valueName": "hr",
            "valueType": "SampledData"
        }
    }
}
```
---
**Passaggi-SampledData**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "55423-8",
                "system": "http://loinc.org",
                "display": "Number of steps"
            }
        ],        
        "periodInterval": 60,
        "typeName": "stepsCount",
        "value": {
            "defaultPeriod": 5000,
            "unit": "",
            "valueName": "steps",
            "valueType": "SampledData"
        }
    }
}
```
---
**Pressione del sangue-SampledData**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "85354-9",
                "display": "Blood pressure panel with all children optional",
                "system": "http://loinc.org"
            }
        ],
        "periodInterval": 60,
        "typeName": "bloodpressure",
        "components": [
            {
                "codes": [
                    {
                        "code": "8867-4",
                        "display": "Diastolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "defaultPeriod": 5000,
                    "unit": "mmHg",
                    "valueName": "diastolic",
                    "valueType": "SampledData"
                }
            },
            {
                "codes": [
                    {
                        "code": "8480-6",
                        "display": "Systolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "defaultPeriod": 5000,
                    "unit": "mmHg",
                    "valueName": "systolic",
                    "valueType": "SampledData"
                }
            }
        ]
    }
}
```
---
**Pressione del sangue-quantità**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "85354-9",
                "display": "Blood pressure panel with all children optional",
                "system": "http://loinc.org"
            }
        ],
        "periodInterval": 0,
        "typeName": "bloodpressure",
        "components": [
            {
                "codes": [
                    {
                        "code": "8867-4",
                        "display": "Diastolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "unit": "mmHg",
                    "valueName": "diastolic",
                    "valueType": "Quantity"
                }
            },
            {
                "codes": [
                    {
                        "code": "8480-6",
                        "display": "Systolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "unit": "mmHg",
                    "valueName": "systolic",
                    "valueType": "Quantity"
                }
            }
        ]
    }
}
```
---
**Dispositivo rimosso-CodeableConcept**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "deviceEvent",
                "system": "https://www.mydevice.com/v1",
                "display": "Device Event"
            }
        ],
        "periodInterval": 0,
        "typeName": "deviceRemoved",
        "value": {
            "text": "Device Removed",
            "codes": [
                {
                    "code": "deviceRemoved",
                    "system": "https://www.mydevice.com/v1",
                    "display": "Device Removed"
                }
            ],
            "valueName": "deviceRemoved",
            "valueType": "CodeableConcept"
        }
    }
}
```
---

## <a name="next-steps"></a>Passaggi successivi

Vedere le domande frequenti sul connettore Azure per FHIR (anteprima).

>[!div class="nextstepaction"]
>[Connettore Azure per le domande frequenti su FHIR](fhir-faq.md)

* Nel portale di Azure, il connettore Azure Internet per FHIR viene indicato come connettore Internet (anteprima). FHIR è un marchio registrato di HL7 e viene usato con l'autorizzazione di HL7.