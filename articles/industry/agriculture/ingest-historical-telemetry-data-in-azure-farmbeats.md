---
title: Inserire dati di telemetria cronologici
description: Questo articolo descrive come inserire dati di telemetria cronologici.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.custom: has-adal-ref
ms.openlocfilehash: af1bee00261cd96f61a39389f31a52109f4e64b5
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675814"
---
# <a name="ingest-historical-telemetry-data"></a>Inserire dati di telemetria cronologici

Questo articolo descrive come inserire i dati cronologici dei sensori in FarmBeats di Azure.

L'inserimento di dati cronologici da risorse di Internet delle cose, ad esempio dispositivi e sensori, è uno scenario comune in FarmBeats. Si creano metadati per dispositivi e sensori e quindi si inseriscono i dati cronologici in FarmBeats in un formato canonico.

## <a name="before-you-begin"></a>Prima di iniziare

Prima di procedere con questo articolo, assicurarsi di aver installato FarmBeats e raccolto i dati cronologici dai dispositivi Internet delle cose. È anche necessario abilitare l'accesso ai partner come indicato nei passaggi seguenti.

## <a name="enable-partner-access"></a>Abilitare l'accesso ai partner

È necessario abilitare l'integrazione dei partner nell'istanza di Azure FarmBeats. Questo passaggio crea un client che ha accesso all'istanza di Azure FarmBeats come partner del dispositivo e fornisce i valori seguenti, necessari nei passaggi successivi:

- Endpoint API: si tratta dell'URL datahub, ad esempio https:// \<datahub> . azurewebsites.NET
- ID tenant
- ID client
- Segreto client
- Stringa di connessione all’Hub eventi

A tale scopo, seguire questa procedura:

> [!NOTE]
> Per eseguire la procedura seguente, è necessario essere un amministratore.

1. Accedere a https://portal.azure.com/.

2. **Se si utilizza FarmBeats versione 1.2.7 o successiva, ignorare i passaggi a, b e c e andare al passaggio 3.** È possibile controllare la versione di FarmBeats selezionando l'icona **delle impostazioni** nell'angolo superiore destro dell'interfaccia utente di FarmBeatsri.

      a.  Passa a **Azure Active Directory**  >  **registrazioni** per l'app

      b. Selezionare la **registrazione dell'app** creata come parte della distribuzione di FarmBeats. Avrà lo stesso nome di FarmBeats datahub.

      c. Selezionare **esporre un'API** > selezionare **Aggiungi un'applicazione client** e immettere **04b07795-8ddb-461A-BBEE-02f9e1bf7b46** e selezionare **autorizzazione ambito** . In questo modo si concederà l'accesso all'interfaccia della riga di comando di Azure (Cloud Shell) per eseguire i passaggi seguenti:

3. Aprire Cloud Shell. Questa opzione è disponibile sulla barra degli strumenti nell'angolo superiore destro della portale di Azure.

    ![Barra degli strumenti portale di Azure](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

4. Verificare che l'ambiente sia impostato su **PowerShell** . Per impostazione predefinita, è impostato su bash.

    ![Impostazione della barra degli strumenti di PowerShell](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

5. Passare alla Home Directory.

    ```azurepowershell-interactive
    cd
    ```

6. Eseguire il comando seguente. In questo modo si connette un account autenticato da usare per le richieste di Azure AD

    ```azurepowershell-interactive
    Connect-AzureAD
    ```

7. Eseguire il comando seguente. Verrà scaricato uno script nella Home Directory.

    ```azurepowershell-interactive 

    wget –q https://aka.ms/farmbeatspartnerscriptv3 -O ./generatePartnerCredentials.ps1

    ```

8. Eseguire lo script seguente. Lo script richiede l'ID tenant, che può essere ottenuto da **Azure Active Directory**  >  pagina **Panoramica** .

    ```azurepowershell-interactive

    ./generatePartnerCredentials.ps1

    ```

9. Seguire le istruzioni visualizzate per acquisire i valori per l' **endpoint API** , l' **ID tenant** , l' **ID client** , il **segreto client** e la **stringa di connessione EventHub** .


## <a name="create-device-or-sensor-metadata"></a>Crea metadati del dispositivo o del sensore

 Ora che si dispone delle credenziali necessarie, è possibile definire il dispositivo e i sensori. A tale scopo, creare i metadati chiamando le API FarmBeats. Assicurarsi di chiamare le API come app client creata nella sezione precedente.

 FarmBeats datahub include le API seguenti che consentono di creare e gestire i metadati del dispositivo o del sensore.

 > [!NOTE]
 > I partner possono accedere solo per leggere, creare e aggiornare i metadati. **l'opzione Delete è limitata al partner.**

- /**DeviceModel** : DeviceModel corrisponde ai metadati del dispositivo, ad esempio il produttore e il tipo di dispositivo, che può essere un gateway o un nodo.
- /**Device** : Device corrisponde a un dispositivo fisico presente nella farm.
- /**SensorModel** : SensorModel corrisponde ai metadati del sensore, ad esempio il produttore, il tipo di sensore, che può essere analogico o digitale, e la misura del sensore, ad esempio la temperatura e la pressione dell'ambiente.
- /**Sensor** : Sensor corrisponde a un sensore fisico che registra i valori. Un sensore è in genere connesso a un dispositivo con un ID dispositivo.


|        DeviceModel   |  Suggerimenti   |
| ------- | -------             |
|     Type (node, gateway)        |          Tipo di dispositivo-nodo o gateway      |
|          Produttore            |         Nome del produttore    |
|  ProductCode                    |  Codice prodotto del dispositivo o numero o nome del modello. Ad esempio, EnviroMonitor#6800.  |
|            Porte          |     Nome e tipo della porta, che può essere digitale o analoga.
|     Nome                 |  Nome di identificazione della risorsa. Ad esempio, il nome del modello o il nome del prodotto.
      Descrizione     | Inserire una descrizione significativa del modello.
|    Proprietà          |    Proprietà aggiuntive inserite dal produttore.   |
|    **Dispositivo**             |                      |
|   DeviceModelId     |     ID del modello di dispositivo associato.  |
|  HardwareId          | ID univoco per il dispositivo, ad esempio l'indirizzo MAC.
|  ReportingInterval        |   L'intervallo di reporting in secondi.
|  Location            |  Latitudine (tra -90 e +90), longitudine (tra -180 e 180) ed elevazione (in metri) del dispositivo.
|ParentDeviceId       |    ID del dispositivo padre a cui è connesso questo dispositivo. Ad esempio, un nodo connesso a un gateway. Un nodo ha parentDeviceId come gateway.  |
|    Nome            | Nome per identificare la risorsa. I partner del dispositivo devono inviare un nome coerente con il nome del dispositivo sul lato del partner. Se il nome del dispositivo partner è definito dall'utente, lo stesso nome definito dall'utente deve essere propagato a FarmBeats.|
|     Descrizione       |      Specificare una descrizione significativa. |
|     Proprietà    |  Proprietà aggiuntive inserite dal produttore.
|     **SensorModel**        |          |
|       Type (analog, digital)          |      Tipo di sensore, sia analogico che digitale.       |
|          Produttore            |       Produttore del sensore.     |
|     ProductCode| Codice prodotto o numero o nome del modello. Ad esempio, RS-CO2-N01. |
|       SensorMeasures > Name       | Nome della misurazione del sensore. Sono supportati solo caratteri minuscoli. Per le misurazioni di profondità diverse, specificare la profondità. Ad esempio, soil_moisture_15cm. Questo nome deve essere coerente con i dati di telemetria.  |
|          SensorMeasures > DataType       |Tipo di dati di telemetria. Attualmente è supportato il tipo di dati double.|
|    SensorMeasures > Type    |Tipo di misurazione dei dati di telemetria del sensore. I tipi definiti dal sistema sono Temperaturaambiente, CO2, depth, ElectricalConductivity, LeafWetness, length, LiquidLevel, nitrate, O2, PH, fosfato, PointInTime, potassio, Pressure, RainGauge, RelativeHumidity, salinità, SoilMoisture, SoilTemperature, SolarRadiation, state, TimeDuration, UVRadiation, UVIndex, volume, WindDirection, WindRun, WindSpeed, evapotraspirazione, PAR. Per aggiungere altre informazioni, vedere l'API/ExtendedType.|
|        SensorMeasures > Unit              | Unità di dati di telemetria del sensore. Le unità definite dal sistema sono nounit, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, Mercury, PSI, millimetro, centimetro, metro, pollice, piedi, miglio, chilometro, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, Degree, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, percentuale, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, Liter, Millilitr, seconds, UnixTimestamp, MicroMolPerMeterSquaredPerSecond, InchesPerHour per aggiungere altro, fare riferimento all'API/ExtendedType.|
|    SensorMeasures > AggregationType    |  I valori possibili sono None, Average, Maximum, Minimum o StandardDeviation.  |
|          Nome            | Nome per identificare una risorsa. Ad esempio, il nome del modello o il nome del prodotto.  |
|    Descrizione        | Inserire una descrizione significativa del modello.|
|   Proprietà       |  Proprietà aggiuntive inserite dal produttore.|
|    **Sensor**      |          |
| HardwareId          |   ID univoco del sensore impostato dal produttore.|
|  SensorModelId     |    ID del modello di sensore associato.|
| Location          |  Latitudine (tra -90 e +90), longitudine (tra -180 e 180) ed elevazione (in metri) del sensore.|
|   Port > Name        |  Nome e tipo della porta tramite cui il sensore è connesso al dispositivo. Il nome deve corrispondere a quello definito nel modello di dispositivo.|
|    DeviceID  |    ID del dispositivo a cui è connesso il sensore. |
| Nome            |   Nome che identifica la risorsa. Ad esempio, il nome del sensore o il nome del prodotto e il numero di modello o il codice prodotto.|
|    Descrizione      | Specificare una descrizione significativa.|
|    Proprietà        |Proprietà aggiuntive inserite dal produttore.|

Per ulteriori informazioni sugli oggetti, vedere [spavalderia](https://aka.ms/FarmBeatsDatahubSwagger).

### <a name="api-request-to-create-metadata"></a>Richiesta API per la creazione di metadati

Per eseguire una richiesta API, combinare il metodo HTTP (POST), l'URL del servizio API e l'URI di una risorsa per eseguire query, inviare dati a, creare o eliminare una richiesta. Quindi si aggiungono una o più intestazioni della richiesta HTTP. L'URL del servizio API è l'endpoint API, ovvero l'URL datahub (https:// \<yourdatahub> . azurewebsites.NET).

### <a name="authentication"></a>Authentication

FarmBeats datahub usa l'autenticazione della porta, che richiede le credenziali seguenti generate nella sezione precedente:

- ID client
- Segreto client
- ID tenant

Usando queste credenziali, il chiamante può richiedere un token di accesso. Il token deve essere inviato nelle richieste API successive, nella sezione header, come indicato di seguito:

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

Il codice Python di esempio seguente fornisce il token di accesso, che può essere usato per le chiamate API successive a FarmBeats: 

```python
import requests
import json
import msal

# Your service principal App ID
CLIENT_ID = "<CLIENT_ID>"
# Your service principal password
CLIENT_SECRET = "<CLIENT_SECRET>"
# Tenant ID for your Azure subscription
TENANT_ID = "<TENANT_ID>"

AUTHORITY_HOST = 'https://login.microsoftonline.com'
AUTHORITY = AUTHORITY_HOST + '/' + TENANT_ID

ENDPOINT = "https://<yourfarmbeatswebsitename-api>.azurewebsites.net"
SCOPE = ENDPOINT + "/.default"

context = msal.ConfidentialClientApplication(CLIENT_ID, authority=AUTHORITY, client_credential=CLIENT_SECRET)
token_response = context.acquire_token_for_client(SCOPE)
# We should get an access token here
access_token = token_response.get('access_token')
```

**Intestazioni di richiesta HTTP**

Di seguito sono riportate le intestazioni di richiesta più comuni che è necessario specificare quando si effettua una chiamata API a FarmBeats datahub:

- **Content-Type** : Application/JSON
- **Autorizzazione** : portar <Access-Token>
- **Accept** : Application/JSON

### <a name="input-payload-to-create-metadata"></a>Payload di input per la creazione di metadati

DeviceModel


```json
{
  "type": "Node",
  "manufacturer": "string",
  "productCode": "string",
  "ports": [
    {
      "name": "string",
      "type": "Analog"
    }
  ],
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

Dispositivo

```json
{
  "deviceModelId": "string",
  "hardwareId": "string",
  "farmId": "string",
  "reportingInterval": 0,
  "location": {
    "latitude": 0,
    "longitude": 0,
    "elevation": 0
  },
  "parentDeviceId": "string",
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

SensorModel

```json
{
  "type": "Analog",
  "manufacturer": "string",
  "productCode": "string",
  "sensorMeasures": [
    {
      "name": "string",
      "dataType": "Double",
      "type": "string",
      "unit": "string",
      "aggregationType": "None",
      "depth": 0,
      "description": "string"
    }
  ],
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}

```
Sensore

```json
{
  "hardwareId": "string",
  "sensorModelId": "string",
  "location": {
    "latitude": 0,
    "longitude": 0,
    "elevation": 0
  },
  "depth": 0,
  "port": {
    "name": "string",
    "type": "Analog"
  },
  "deviceId": "string",
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

La richiesta di esempio seguente crea un dispositivo. Questa richiesta ha input JSON come payload con il corpo della richiesta.

```bash
curl -X POST "https://<datahub>.azurewebsites.net/Device" -H
"accept: application/json" -H  "Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",
\"reportingInterval\": 900,  \"name\": \"Device123\",
\"description\": \"Test Device 123\"}" *
```

Di seguito è riportato un codice di esempio in Python. Il token di accesso utilizzato in questo esempio è lo stesso che viene ricevuto durante l'autenticazione.

```python
import requests
import json

# Got access token - Calling the Device Model API

headers = {
    "Authorization": "Bearer " + access_token,
    "Content-Type" : "application/json"
    }
payload = '{"type" : "Node", "productCode" : "TestCode", "ports": [{"name": "port1","type": "Analog"}], "name" : "DummyDevice"}'
response = requests.post(ENDPOINT + "/DeviceModel", data=payload, headers=headers)
```

> [!NOTE]
> Le API restituiscono ID univoci per ogni istanza creata. È necessario mantenere gli ID per inviare i messaggi di telemetria corrispondenti.

### <a name="send-telemetry"></a>Inviare dati di telemetria

Ora che sono stati creati i dispositivi e i sensori in FarmBeats, è possibile inviare i messaggi di telemetria associati.

### <a name="create-a-telemetry-client"></a>Creare un client di telemetria

È necessario inviare i dati di telemetria a hub eventi di Azure per l'elaborazione. Hub eventi di Azure è un servizio che consente l’inserimento di dati (di telemetria) in tempo reale da applicazioni e dispositivi connessi. Per inviare i dati di telemetria a FarmBeats, creare un client che invii messaggi a un hub eventi in FarmBeats. Per altre informazioni sull'invio di dati di telemetria, vedere [Hub eventi di Azure](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).

### <a name="send-a-telemetry-message-as-the-client"></a>Inviare un messaggio di telemetria come client

Dopo aver stabilito una connessione come client di hub eventi, è possibile inviare messaggi all'hub eventi come JSON.

Ecco il codice Python di esempio che invia dati di telemetria come client a un hub eventi specificato:

```python
import azure
from azure.eventhub import EventHubClient, Sender, EventData, Receiver, Offset
EVENTHUBCONNECTIONSTRING = "<EventHub Connection String provided by customer>"
EVENTHUBNAME = "<EventHub Name provided by customer>"

write_client = EventHubClient.from_connection_string(EVENTHUBCONNECTIONSTRING, eventhub=EVENTHUBNAME, debug=False)
sender = write_client.add_sender(partition="0")
write_client.run()
for i in range(5):
    telemetry = "<Canonical Telemetry message>"
    print("Sending telemetry: " + telemetry)
    sender.send(EventData(telemetry))
write_client.stop()

```

Convertire il formato dei dati cronologici del sensore in un formato canonico che Azure FarmBeats riconosce. Il formato del messaggio canonico è il seguente:

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```

Dopo aver aggiunto i dispositivi e i sensori corrispondenti, ottenere l'ID del dispositivo e l'ID del sensore nel messaggio di telemetria, come descritto nella sezione precedente.

Ecco un esempio di messaggio di telemetria:


 ```json
{
  "deviceid": "7f9b4b92-ba45-4a1d-a6ae-c6eda3a5bd12",
  "timestamp": "2019-06-22T06:55:02.7279559Z",
  "version": "1",
  "sensors": [
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 15
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 42
        }
      ]
    },
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 20
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 89
        }
      ]
    }
  ]
}
```

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>Non è possibile visualizzare i dati di telemetria dopo l'inserimento dei dati cronologici/di streaming dai sensori

**Sintomo** : i dispositivi o i sensori sono stati distribuiti e sono stati creati in FarmBeats e i dati di telemetria sono stati inseriti in EventHub, ma non è possibile ottenere o visualizzare i dati di telemetria in FarmBeats.

**Azione correttiva** :

1. Assicurarsi di aver eseguito la registrazione del partner appropriata. per verificarlo, passare a datahub spavalderia, passare all'API/partner, eseguire un'operazione get e verificare se il partner è registrato. In caso contrario, seguire questa [procedura](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats) per aggiungere il partner.

2. Assicurarsi di aver creato i metadati (DeviceModel, Device, SensorModel, Sensor) usando le credenziali client del partner.

3. Assicurarsi di aver usato il formato del messaggio di telemetria corretto (come specificato di seguito):

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui dettagli di integrazione basata sull'API REST, vedere [API REST](rest-api-in-azure-farmbeats.md).