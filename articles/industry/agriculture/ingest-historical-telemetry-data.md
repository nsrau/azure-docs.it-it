---
title: Inserire dati di telemetria cronologici
description: Questo articolo descrive come inserire dati di telemetria cronologici.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: a3e4f543be2f01e0c649d5f9bcc9287dedc275f1
ms.sourcegitcommit: b5ff5abd7a82eaf3a1df883c4247e11cdfe38c19
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74941645"
---
# <a name="ingest-historical-telemetry-data"></a>Inserire dati di telemetria cronologici

Questo articolo descrive come inserire i dati cronologici dei sensori in FarmBeats di Azure.

L'inserimento di dati cronologici da risorse di Internet delle cose, ad esempio dispositivi e sensori, è uno scenario comune in FarmBeats. Si creano metadati per dispositivi e sensori e quindi si inseriscono i dati cronologici in FarmBeats in un formato canonico.

## <a name="before-you-begin"></a>Prima di iniziare

Prima di procedere con questo articolo, assicurarsi di avere installato FarmBeats e di avere raccolto i dati cronologici.
È anche necessario abilitare l'accesso ai partner come indicato nei passaggi seguenti.

## <a name="enable-partner-access"></a>Abilitare l'accesso ai partner

È necessario abilitare l'integrazione dei partner nell'istanza di Azure FarmBeats. Questo passaggio crea un client che ha accesso all'istanza di Azure FarmBeats come partner del dispositivo e fornisce i valori seguenti, necessari nei passaggi successivi:

- Endpoint API: si tratta dell'URL datahub, ad esempio, https://\<datahub >. azurewebsites. NET.
- ID tenant
- ID client
- Segreto client
- Stringa di connessione EventHub

Seguire questa procedura.

>[!NOTE]
> Per eseguire la procedura seguente, è necessario essere un amministratore.

1. Scaricare questo [script](https://aka.ms/farmbeatspartnerscript)ed estrarlo nell'unità locale. Due file sono inclusi nel file zip.
2. Accedere al [portale di Azure](https://portal.azure.com/) e aprire Azure Cloud Shell. Questa opzione è disponibile sulla barra degli strumenti nell'angolo superiore destro del portale.

    ![Barra degli strumenti portale di Azure](./media/for-tutorials/navigation-bar-1.png)

3. Verificare che l'ambiente sia impostato su **PowerShell**.

    ![Impostazione di PowerShell](./media/for-tutorials/power-shell-new-1.png)

4. Caricare i due file scaricati al passaggio 1 nell'istanza di Cloud Shell.

    ![Pulsante carica sulla barra degli strumenti](./media/for-tutorials/power-shell-two-1.png)

5. Passare alla directory in cui sono stati caricati i file.

   >[!NOTE]
   > Per impostazione predefinita, i file vengono caricati nella home directory/home/username.
6. Eseguire lo script usando il comando seguente:

    ```azurepowershell-interactive
    ./generateCredentials.ps1
    ```

7. Seguire le istruzioni visualizzate per completare la procedura.

## <a name="create-device-or-sensor-metadata"></a>Crea metadati del dispositivo o del sensore

 Ora che si dispone delle credenziali necessarie, è possibile definire il dispositivo e i sensori. A tale scopo, creare i metadati usando le API FarmBeats.

 FarmBeats datahub include le API seguenti che consentono di creare e gestire i metadati del dispositivo o del sensore.

- /**DeviceModel**: DeviceModel corrisponde ai metadati del dispositivo, ad esempio il produttore e il tipo di dispositivo, che può essere un gateway o un nodo.
- /**dispositivo**: il dispositivo corrisponde a un dispositivo fisico presente nella farm.
- /**SensorModel**: SensorModel corrisponde ai metadati del sensore, ad esempio il produttore, il tipo di sensore, che può essere analogico o digitale, e la misura del sensore, ad esempio la temperatura e la pressione dell'ambiente.
- **sensore**/: il sensore corrisponde a un sensore fisico che registra i valori. Un sensore è in genere connesso a un dispositivo con un ID dispositivo.  


|        DeviceModel   |  Suggerimenti   |
| ------- | -------             |
|     Tipo (nodo, gateway)        |          1 stella      |
|          Produttore            |         2 stelle     |
|  ProductCode                    |  Codice prodotto del dispositivo o numero o nome del modello. Ad esempio, EnviroMonitor # 6800.  |
|            Porte          |     Nome e tipo della porta, che è digitale o analogo.
|     name                 |  Nome per identificare la risorsa. Ad esempio, il nome del modello o il nome del prodotto.
      Description     | Fornire una descrizione significativa del modello.
|    properties          |    Proprietà aggiuntive del produttore.   |
|    **Dispositivo**             |                      |
|   DeviceModelId     |     ID del modello di dispositivo associato.  |
|  HardwareId          | ID univoco per il dispositivo, ad esempio l'indirizzo MAC.
|  ReportingInterval        |   Intervallo di Reporting in secondi.
|  Località            |  Latitudine del dispositivo (da-90 a + 90), Longitudine (-180 a 180) ed elevazione (in metri).   
|ParentDeviceId       |    ID del dispositivo padre a cui è connesso il dispositivo. Ad esempio, un nodo connesso a un gateway. Un nodo ha parentDeviceId come gateway.  |
|    name            | Nome per identificare la risorsa. I partner del dispositivo devono inviare un nome coerente con il nome del dispositivo sul lato del partner. Se il nome del dispositivo partner è definito dall'utente, lo stesso nome definito dall'utente deve essere propagato a FarmBeats.|
|     Description       |      Fornire una descrizione significativa. |
|     properties    |  Proprietà aggiuntive del produttore.
|     **SensorModel**        |          |
|       Tipo (analogico, digitale)          |      Tipo di sensore, sia analogico che digitale.       |
|          Produttore            |       Produttore del sensore.     |
|     ProductCode| Codice prodotto o nome modello o numero. Ad esempio, RS-CO2-N01. |
|       Nome > SensorMeasures       | Nome della misura del sensore. Sono supportati solo caratteri minuscoli. Per le misurazioni di profondità diverse, specificare la profondità. Ad esempio, soil_moisture_15cm. Questo nome deve essere coerente con i dati di telemetria.  |
|          Tipo di dati > SensorMeasures       |Tipo di dati di telemetria. Attualmente, il valore Double è supportato.|
|    Tipo > SensorMeasures    |Tipo di misura dei dati di telemetria del sensore. I tipi definiti dal sistema sono Temperaturaambiente, CO2, depth, ElectricalConductivity, LeafWetness, length, LiquidLevel, nitrate, O2, PH, fosfato, PointInTime, potassio, Pressure, RainGauge, RelativeHumidity, salinità, SoilMoisture, SoilTemperature, SolarRadiation, state, TimeDuration, UVRadiation, UVIndex, volume, WindDirection, WindRun, WindSpeed, evapotraspirazione, PAR. Per aggiungere altre informazioni, vedere l'API/ExtendedType.|
|        Unità > SensorMeasures              | Unità di dati di telemetria del sensore. Le unità definite dal sistema sono nounit, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, Mercury, PSI, millimetro, centimetro, metro, pollice, feet, Mile, chilometro, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, Degree, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, percentuale, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, litro, millilitro, seconds, UnixTimestamp, MicroMolPerMeterSquaredPerSecond, InchesPerHour per aggiungere altro, fare riferimento all'API/ExtendedType.|
|    SensorMeasures > AggregationType    |  I valori possibili sono None, Average, Maximum, Minimum o StandardDeviation.  |
|          name            | Nome per identificare una risorsa. Ad esempio, il nome del modello o il nome del prodotto.  |
|    Description        | Fornire una descrizione significativa del modello.  |
|   properties       |  Proprietà aggiuntive del produttore.  |
|    **Sensore**      |          |
| HardwareId          |   ID univoco per il sensore impostato dal produttore. |
|  SensorModelId     |    ID del modello di sensore associato.   |
| Località          |  Latitudine del sensore (da-90 a + 90), Longitudine (-180 a 180) ed elevazione (in metri).|
|   Nome > porta        |  Nome e tipo della porta a cui il sensore è connesso nel dispositivo. Il nome deve corrispondere a quello definito nel modello di dispositivo. |
|    DeviceID  |    ID del dispositivo a cui è connesso il sensore.     |
| name            |   Nome per identificare la risorsa. Ad esempio, il nome del sensore o il nome del prodotto e il numero di modello o il codice prodotto.|
|    Description      | Fornire una descrizione significativa. |
|    properties        |Proprietà aggiuntive del produttore. |

Per ulteriori informazioni sugli oggetti, vedere [spavalderia](https://aka.ms/FarmBeatsDatahubSwagger).

### <a name="api-request-to-create-metadata"></a>Richiesta API per la creazione di metadati

Per eseguire una richiesta API, combinare il metodo HTTP (POST), l'URL del servizio API e l'URI di una risorsa per eseguire query, inviare dati a, creare o eliminare una richiesta. Quindi si aggiungono una o più intestazioni della richiesta HTTP. L'URL del servizio API è l'endpoint API, ovvero l'URL datahub (https://\<yourdatahub >. azurewebsites. NET).  

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

Di seguito sono riportate le intestazioni di richiesta più comuni che è necessario specificare quando si effettua una chiamata API a FarmBeats datahub:

- **Content-Type**: Application/JSON
- **Autorizzazione**: bearer < Access-token >
- **Accept**: Application/JSON

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

> [!NOTE]
> Le API restituiscono ID univoci per ogni istanza creata. È necessario mantenere gli ID per inviare i messaggi di telemetria corrispondenti.

### <a name="send-telemetry"></a>Inviare dati di telemetria

Ora che sono stati creati i dispositivi e i sensori in FarmBeats, è possibile inviare i messaggi di telemetria associati.

### <a name="create-a-telemetry-client"></a>Creare un client di telemetria

È necessario inviare i dati di telemetria a hub eventi di Azure per l'elaborazione. Hub eventi di Azure è un servizio che consente l'inserimento di dati in tempo reale (telemetria) da applicazioni e dispositivi connessi. Per inviare i dati di telemetria a FarmBeats, creare un client che invii messaggi a un hub eventi in FarmBeats. Per altre informazioni sull'invio di dati di telemetria, vedere [Hub eventi di Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send).

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


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui dettagli di integrazione basata sull'API REST, vedere [API REST](references-for-farmbeats.md#rest-api).
