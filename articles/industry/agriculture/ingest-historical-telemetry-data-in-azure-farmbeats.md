---
title: Inserire dati di telemetria cronologici
description: In questo articolo viene descritto come ingest dati di telemetria cronologici.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: b0b9d62e8761cfb67d0642d8e5a97e7d1f05af12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064445"
---
# <a name="ingest-historical-telemetry-data"></a>Inserire dati di telemetria cronologici

Questo articolo descrive come inserire dati cronologici del sensore in Azure FarmBeats.This article describes how to ingest historical sensor data into Azure FarmBeats.

L'inserimento di dati cronologici da risorse Internet of Things (IoT), ad esempio dispositivi e sensori, è uno scenario comune in FarmBeats. Creare metadati per dispositivi e sensori e quindi immettere i dati cronologici in FarmBeats in un formato canonico.

## <a name="before-you-begin"></a>Prima di iniziare

Prima di procedere con questo articolo, assicurati di aver installato FarmBeats e di aver raccolto dati cronologici dai tuoi dispositivi IoT. È inoltre necessario abilitare l'accesso partner come indicato nei passaggi seguenti.

## <a name="enable-partner-access"></a>Abilitare l'accesso ai partnerEnable partner access

È necessario abilitare l'integrazione dei partner nell'istanza di Azure FarmBeats.You need to enable partner integration to your Azure FarmBeats instance. Questo passaggio crea un client che ha accesso all'istanza di Azure FarmBeats come partner del dispositivo e fornisce i valori seguenti necessari nei passaggi successivi:This step creates a client that has access to your Azure FarmBeats instance as your device partner and provides you with the following values that are the following values that are the following steps:

- Endpoint API: si tratta dell'URL di\<Datahub, ad esempio, https:// datahub>.azurewebsites.net
- ID tenant
- ID client
- Segreto client
- Stringa di connessione EventHub

A tale scopo, seguire questa procedura:

> [!NOTE]
> Per eseguire la procedura seguente, è necessario essere un amministratore.

1. Scaricare il [file zip](https://aka.ms/farmbeatspartnerscriptv2)ed estrarlo nell'unità locale. Ci sarà un file all'interno del file zip.

2. Accedere https://portal.azure.com/ e passare a**Registrazioni app** **di Azure Active Directory** > .

3. Selezionare la **registrazione dell'app** creata come parte della distribuzione di FarmBeats. Avrà lo stesso nome del tuo Hub FarmBeats.

4. Selezionare **Esporre un'API** > selezionare **Selezionare Aggiungi un'applicazione client** e immettere **04b07795-8ddb-461a-bbee-02f9e1bf7b46** e **selezionare Authorize Scope**. In questo modo sarà possibile accedere all'interfaccia della riga di comando di Azure (Cloud Shell) per eseguire i passaggi seguenti:This will give access to the Azure CLI (Cloud Shell) to perform the following steps:

5. Aprire Cloud Shell. Questa opzione è disponibile sulla barra degli strumenti nell'angolo superiore destro del portale di Azure.This option is available on the toolbar in the upper-right corner of the Azure portal.

    ![Barra degli strumenti del portale di AzureAzure portal toolbar](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

6. Verificare che l'ambiente sia impostato su **PowerShell**. Per impostazione predefinita, è impostato su Bash.By default, it's set to Bash.

    ![Impostazione della barra degli strumenti di PowerShellPowerShell toolbar setting](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

7. Caricare il file dal passaggio 1 nell'istanza di Cloud Shell.

    ![Pulsante Carica barra degli strumenti](./media/get-sensor-data-from-sensor-partner/power-shell-two-1.png)

8. Passare alla directory in cui è stato caricato il file. Per impostazione predefinita, i file vengono caricati nella home directory con il nome utente.

9. Eseguire lo script seguente. Lo script richiede l'ID tenant, che può essere ottenuto dalla**pagina Panoramica**di **Azure Active Directory.** > 

    ```azurepowershell-interactive

    ./generatePartnerCredentials.ps1   

    ```

10. Seguire le istruzioni visualizzate per acquisire i valori per **Endpoint API**, **ID tenant**, ID **client**, **Segreto client**e Stringa di **connessione EventHub**.

## <a name="create-device-or-sensor-metadata"></a>Creare metadati del dispositivo o del sensoreCreate device or sensor metadata

 Ora che si dispone delle credenziali necessarie, è possibile definire il dispositivo e i sensori. A tale scopo, creare i metadati chiamando le API FarmBeats.To do this, create the metadata by calling FarmBeats APIs. Assicurati di chiamare le API come app client che hai creato nella sezione precedente.

 FarmBeats Datahub dispone delle API seguenti che consentono la creazione e la gestione dei metadati del dispositivo o del sensore.

 > [!NOTE]
 > In qualità di partner hai accesso solo per leggere, creare e aggiornare i metadati; **l'opzione di eliminazione è limitata al partner.**

- /**DeviceModel**: DeviceModel corrisponde ai metadati del dispositivo, ad esempio il produttore e il tipo di dispositivo, ovvero un gateway o un nodo.
- /**Dispositivo:** il dispositivo corrisponde a un dispositivo fisico presente nella farm.
- /**SensorModel**: SensorModel corrisponde ai metadati del sensore, come il produttore, il tipo di sensore, analogico o digitale, e la misurazione del sensore, come la temperatura ambiente e la pressione.
- /**Sensore**: Sensore corrisponde a un sensore fisico che registra i valori. Un sensore è in genere collegato a un dispositivo con un ID dispositivo.  


|        DeviceModel   |  Suggerimenti   |
| ------- | -------             |
|     Tipo (nodo, gateway)        |          Tipo di dispositivo - nodo o gateway      |
|          Produttore            |         Nome del produttore    |
|  Productcode                    |  Codice prodotto del dispositivo o nome o numero del modello. Ad esempio, EnviroMonitor 6800.  |
|            Porte          |     Nome e tipo della porta, che è digitale o analogico.
|     Nome                 |  Nome per identificare la risorsa. Ad esempio, il nome del modello o del prodotto.
      Descrizione     | Fornire una descrizione significativa del modello.
|    Proprietà          |    Proprietà aggiuntive del produttore.   |
|    **Dispositivo**             |                      |
|   DeviceModelId     |     ID del modello di dispositivo associato.  |
|  HardwareId (ID hardware)          | ID univoco per il dispositivo, ad esempio l'indirizzo MAC.
|  ReportingInterval        |   Intervallo di segnalazione in secondi.
|  Location            |  Latitudine del dispositivo (da -90 a 90 dollari), longitudine (-180 a 180) e elevazione (in metri).   
|ParentDeviceId       |    ID del dispositivo padre a cui è connesso il dispositivo. Ad esempio, un nodo connesso a un gateway. Un nodo ha parentDeviceId come gateway.  |
|    Nome            | Nome per l'identificazione della risorsa. I partner dispositivo devono inviare un nome coerente con il nome del dispositivo sul lato partner. Se il nome del dispositivo partner è definito dall'utente, lo stesso nome definito dall'utente deve essere propagato a FarmBeats.If the partner device name is user defined, then the same user-defined name should be propagated to FarmBeats.|
|     Descrizione       |      Fornire una descrizione significativa. |
|     Proprietà    |  Proprietà aggiuntive del produttore.
|     **SensorEModello**        |          |
|       Tipo (analogico, digitale)          |      Il tipo di sensore, sia analogico che digitale.       |
|          Produttore            |       Produttore del sensore.     |
|     Productcode| Codice prodotto o nome o numero del modello. Ad esempio, RS-CO2-N01. |
|       Nome > SensorMeasures       | Nome della misura del sensore. È supportato solo lettere minuscole. Per le misurazioni da profondità diverse, specificare la profondità. Ad esempio, soil_moisture_15cm. Questo nome deve essere coerente con i dati di telemetria.  |
|          SensorMeasures > DataType       |Tipo di dati di telemetria. Attualmente è supportato double.|
|    Tipo di > SensorMeasures    |Tipo di misurazione dei dati di telemetria del sensore. I tipi definiti dal sistema sono AmbientTemperature, CO2, Depth, ElectricalConductivity, LeafWetness, Length, LiquidLevel, Nitrate, O2, PH, Phosphate, PointInTime, Potassium, Pressure, RainGauge, Relativeity, Salinity, SoilMoisture, SoilTemperature, SolarRadiation, Stato, TimeDuration, UVRadiation, UVIndex, Volume, WindDirection, WindRun, WindSpeed, Evapotranspiration, PAR. Per aggiungerne altre, fare riferimento all'API /ExtendedType.|
|        Unità > SensorMeasures              | Unità di dati di telemetria del sensore. Le unità definite dal sistema sono NoUnit, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, Mercury, PSI, MilliMeter, CentiMeter, Meter, Inch, Piedi, Mile, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, Grado, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, Percentage, PartsPerMillion, MicroMol, MicroMolesPerLiTer, SiemensPerSquareMeter MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, Liter, MilliLiter, Seconds, UnixTimestamp, MicroMolPerMeterSquaredPerSecond, InchesPerHour Per aggiungerne altri, fare riferimento all'API /ExtendedType.|
|    SensorMeasures > AggregationType    |  I valori possono essere nessuno, media, massimo, minimo o Deviazione standard.  |
|          Nome            | Nome per identificare una risorsa. Ad esempio, il nome del modello o del prodotto.  |
|    Descrizione        | Fornire una descrizione significativa del modello.|
|   Proprietà       |  Proprietà aggiuntive del produttore.|
|    **Sensore**      |          |
| HardwareId (ID hardware)          |   ID univoco per il sensore impostato dal produttore.|
|  SensorModelId (IdModello)     |    ID del modello di sensore associato.|
| Location          |  Latitudine del sensore (da -90 a 90 dollari), longitudine (-180 a 180) e elevazione (in metri).|
|   Nome > portuale        |  Nome e tipo della porta a cui è collegato il sensore sul dispositivo. Deve essere lo stesso nome definito nel modello di dispositivo.|
|    DeviceID  |    ID del dispositivo a cui è collegato il sensore. |
| Nome            |   Nome per identificare la risorsa. Ad esempio, il nome del sensore o il nome del prodotto e il numero di modello o il codice prodotto.|
|    Descrizione      | Fornire una descrizione significativa.|
|    Proprietà        |Proprietà aggiuntive del produttore.|

Per ulteriori informazioni sugli oggetti, vedere [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

### <a name="api-request-to-create-metadata"></a>Richiesta API per creare metadati

Per effettuare una richiesta API, combinare il metodo HTTP (POST), l'URL del servizio API e l'URI per una risorsa per eseguire una query, inviare dati a, creare o eliminare una richiesta. Aggiungere quindi una o più intestazioni di richiesta HTTP. L'URL del servizio API è l'endpoint API, ovvero\<l'URL di Datahub (https:// yourdatahub>.azurewebsites.net).  

### <a name="authentication"></a>Authentication

FarmBeats Datahub utilizza l'autenticazione di connessione, che richiede le credenziali seguenti generate nella sezione precedente:

- ID client
- Segreto client
- ID tenant

Utilizzando queste credenziali, il chiamante può richiedere un token di accesso. Il token deve essere inviato nelle richieste API successive, nella sezione dell'intestazione, come segue:

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

The following sample Python code gives the access token, which can be used for subsequent API calls to FarmBeats: 

```python
import azure 

from azure.common.credentials import ServicePrincipalCredentials 
import adal 
#FarmBeats API Endpoint 
ENDPOINT = "https://<yourdatahub>.azurewebsites.net" [Azure website](https://<yourdatahub>.azurewebsites.net)
CLIENT_ID = "<Your Client ID>"   
CLIENT_SECRET = "<Your Client Secret>"   
TENANT_ID = "<Your Tenant ID>" 
AUTHORITY_HOST = 'https://login.microsoftonline.com' 
AUTHORITY = AUTHORITY_HOST + '/' + TENANT_ID 
#Authenticating with the credentials 
context = adal.AuthenticationContext(AUTHORITY) 
token_response = context.acquire_token_with_client_credentials(ENDPOINT, CLIENT_ID, CLIENT_SECRET) 
#Should get an access token here 
access_token = token_response.get('accessToken') 
```

**Intestazioni di richiesta HTTP**

Ecco le intestazioni di richiesta più comuni che devono essere specificate quando si effettua una chiamata API a FarmBeats Datahub:Here are the most common request headers that must be specified when you make an API call to FarmBeats Datahub:

- **Content-Type**: application/json
- **Autorizzazione**:> Ton-token di connessione <tramite connessione
- **Accetta**: applicazione/json

### <a name="input-payload-to-create-metadata"></a>Payload di input per creare metadatiInput payload to create metadata

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

SensorEModello

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

Di seguito è riportato un codice di esempio in Python.Below is a sample code in Python. Il token di accesso usato in questo esempio è lo stesso ricevuto durante l'autenticazione.

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

Dopo aver creato i dispositivi e i sensori in FarmBeats, è possibile inviare i messaggi di telemetria associati.

### <a name="create-a-telemetry-client"></a>Creare un client di telemetriaCreate a telemetry client

È necessario inviare i dati di telemetria agli hub eventi di Azure per l'elaborazione. Hub eventi di Azure è un servizio che consente l'inserimento di dati in tempo reale (telemetria) da applicazioni e dispositivi connessi. To send telemetry data to FarmBeats, create a client that sends messages to an event hub in FarmBeats. Per altre informazioni sull'invio di dati di telemetria, vedere Hub eventi di Azure.For more information about sending telemetry, see [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send).

### <a name="send-a-telemetry-message-as-the-client"></a>Inviare un messaggio di telemetria come clientSend a telemetry message as the client

Dopo aver stabilito una connessione come client Hub eventi, è possibile inviare messaggi all'hub eventi come JSON.

Ecco il codice Python di esempio che invia dati di telemetria come client a un hub eventi specificato:Here's sample Python code that sends telemetry as a client to a specified event hub:

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

Convertire il formato dei dati dei sensori cronologici in un formato canonico comprensibile a Azure FarmBeats.Convert the historical sensor data format to a canonical format that Azure FarmBeats understands. Il formato del messaggio canonico è il seguente:The canonical message format is as follows:

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
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": "<values>"
        }
      ]
    }
 ]
}
```

Dopo aver aggiunto i dispositivi e i sensori corrispondenti, ottenere l'ID del dispositivo e l'ID del sensore nel messaggio di telemetria, come descritto nella sezione precedente.

Ecco un esempio di messaggio di telemetria:Here's an example of a telemetry message:


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

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>Impossibile visualizzare i dati di telemetria dopo l'inserimento di dati cronologici/in streaming dai sensori

**Sintomo:** i dispositivi o i sensori vengono distribuiti e i dispositivi/sensori sono stati creati in FarmBeats e sono stati ingeriti dati di telemetria in EventHub, ma non è possibile ottenere o visualizzare i dati di telemetria in FarmBeats.

**Azione correttiva**:

1. Assicurati di aver fatto la registrazione del partner appropriata - puoi controllarlo andando al tuo datahub swagger, passare a /Partner API, Do a Get e controllare se il partner è registrato. In caso contrario, seguire i [passaggi qui](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats) per aggiungere partner.

2. Assicurarsi di aver creato i metadati (DeviceModel, Device, SensorModel, Sensor) utilizzando le credenziali del client partner.

3. Assicurarsi di aver utilizzato il formato del messaggio di telemetria corretto (come specificato di seguito):

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
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        }
      ]
    }
 ]
}
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui dettagli di integrazione basata sull'API REST, vedere [API REST.](rest-api-in-azure-farmbeats.md)
