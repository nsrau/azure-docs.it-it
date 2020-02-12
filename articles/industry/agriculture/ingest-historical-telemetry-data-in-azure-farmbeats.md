---
title: Inserire dati di telemetria cronologici
description: Questo articolo descrive come inserire dati di telemetria cronologici.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 0d220d1d88d9d761d9f0eba6187abefb372681be
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77131891"
---
# <a name="ingest-historical-telemetry-data"></a>Inserire dati di telemetria cronologici

Questo articolo descrive come inserire i dati cronologici dei sensori in FarmBeats di Azure.

L'inserimento di dati cronologici da risorse di Internet delle cose, ad esempio dispositivi e sensori, è uno scenario comune in FarmBeats. Si creano metadati per dispositivi e sensori e quindi si inseriscono i dati cronologici in FarmBeats in un formato canonico.

## <a name="before-you-begin"></a>Prima di iniziare

Prima di procedere con questo articolo, assicurarsi di aver installato FarmBeats e raccolto i dati cronologici dai dispositivi Internet delle cose.
È anche necessario abilitare l'accesso ai partner come indicato nei passaggi seguenti.

## <a name="enable-partner-access"></a>Abilitare l'accesso ai partner

È necessario abilitare l'integrazione dei partner nell'istanza di Azure FarmBeats. Questo passaggio crea un client che ha accesso all'istanza di Azure FarmBeats come partner del dispositivo e fornisce i valori seguenti, necessari nei passaggi successivi:

- Endpoint API: si tratta dell'URL datahub, ad esempio, https://\<datahub >. azurewebsites. NET.
- ID tenant
- ID client
- Segreto client
- Stringa di connessione EventHub

Eseguire la procedura seguente.

>[!NOTE]
> Per eseguire la procedura seguente, è necessario essere un amministratore.

1. Scaricare il [file zip](https://aka.ms/farmbeatspartnerscriptv2)ed estrarlo nell'unità locale. Sarà presente un file all'interno del file zip.
2. Accedere a https://portal.azure.com/ e passare a Azure Active Directory-> registrazioni per l'app

3. Fare clic sulla registrazione dell'app creata come parte della distribuzione di FarmBeats. Avrà lo stesso nome di FarmBeats datahub.

4. Fare clic su "esporre un'API"-> fare clic su "Aggiungi applicazione client" e immettere **04b07795-8ddb-461A-BBEE-02f9e1bf7b46** e selezionare "autorizzazione ambito". In questo modo si concederà l'accesso all'interfaccia della riga di comando di Azure (Cloud Shell) per eseguire i passaggi seguenti.

5. Aprire Cloud Shell. Questa opzione è disponibile sulla barra degli strumenti nell'angolo superiore destro della portale di Azure.

    ![Barra degli strumenti portale di Azure](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

6. Verificare che l'ambiente sia impostato su **PowerShell**. Per impostazione predefinita, è impostato su bash.

    ![Impostazione della barra degli strumenti di PowerShell](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

7. Caricare il file dal passaggio 1 nell'istanza di Cloud Shell.

    ![Pulsante carica barra degli strumenti](./media/get-sensor-data-from-sensor-partner/power-shell-two-1.png)

8. Passare alla directory in cui è stato caricato il file. Per impostazione predefinita, i file vengono caricati nella home directory sotto il nome utente.

9. Eseguire lo script seguente. Lo script richiede l'ID tenant che può essere ottenuto dalla pagina Panoramica di Azure Active Directory >.

    ```azurepowershell-interactive 

    ./generatePartnerCredentials.ps1   

    ```

10. Seguire le istruzioni visualizzate per acquisire i valori per l' **endpoint API**, l' **ID tenant**, l' **ID client**, il **segreto client**e la **stringa di connessione EventHub**.
## <a name="create-device-or-sensor-metadata"></a>Crea metadati del dispositivo o del sensore

 Ora che si dispone delle credenziali necessarie, è possibile definire il dispositivo e i sensori. A tale scopo, creare i metadati chiamando le API FarmBeats. Si noti che sarà necessario chiamare le API come app client creata nella sezione precedente

 FarmBeats datahub include le API seguenti che consentono di creare e gestire i metadati del dispositivo o del sensore. Si noti che, come partner, è possibile accedere solo a leggere, creare e aggiornare i metadati. **L'eliminazione non è consentita da un partner.**

- /**DeviceModel**: DeviceModel corrisponde ai metadati del dispositivo, ad esempio il produttore e il tipo di dispositivo, che può essere un gateway o un nodo.
- /**dispositivo**: il dispositivo corrisponde a un dispositivo fisico presente nella farm.
- /**SensorModel**: SensorModel corrisponde ai metadati del sensore, ad esempio il produttore, il tipo di sensore, che può essere analogico o digitale, e la misura del sensore, ad esempio la temperatura e la pressione dell'ambiente.
- **sensore**/: il sensore corrisponde a un sensore fisico che registra i valori. Un sensore è in genere connesso a un dispositivo con un ID dispositivo.  


|        DeviceModel   |  Suggerimenti   |
| ------- | -------             |
|     Tipo (nodo, gateway)        |          Tipo di dispositivo-nodo o gateway      |
|          Produttore            |         Nome del produttore    |
|  ProductCode                    |  Codice prodotto del dispositivo o numero o nome del modello. Ad esempio, EnviroMonitor # 6800.  |
|            Porte          |     Nome e tipo della porta, che è digitale o analogo.
|     Name                 |  Nome per identificare la risorsa. Ad esempio, il nome del modello o il nome del prodotto.
      Descrizione     | Fornire una descrizione significativa del modello.
|    Proprietà          |    Proprietà aggiuntive del produttore.   |
|    **Dispositivo**             |                      |
|   DeviceModelId     |     ID del modello di dispositivo associato.  |
|  HardwareId          | ID univoco per il dispositivo, ad esempio l'indirizzo MAC.
|  ReportingInterval        |   Intervallo di Reporting in secondi.
|  Location            |  Latitudine del dispositivo (da-90 a + 90), Longitudine (-180 a 180) ed elevazione (in metri).   
|ParentDeviceId       |    ID del dispositivo padre a cui è connesso il dispositivo. Ad esempio, un nodo connesso a un gateway. Un nodo ha parentDeviceId come gateway.  |
|    Name            | Nome per identificare la risorsa. I partner del dispositivo devono inviare un nome coerente con il nome del dispositivo sul lato del partner. Se il nome del dispositivo partner è definito dall'utente, lo stesso nome definito dall'utente deve essere propagato a FarmBeats.|
|     Descrizione       |      Fornire una descrizione significativa. |
|     Proprietà    |  Proprietà aggiuntive del produttore.
|     **SensorModel**        |          |
|       Tipo (analogico, digitale)          |      Tipo di sensore, sia analogico che digitale.       |
|          Produttore            |       Produttore del sensore.     |
|     ProductCode| Codice prodotto o nome modello o numero. Ad esempio, RS-CO2-N01. |
|       Nome > SensorMeasures       | Nome della misura del sensore. Sono supportati solo caratteri minuscoli. Per le misurazioni di profondità diverse, specificare la profondità. Ad esempio, soil_moisture_15cm. Questo nome deve essere coerente con i dati di telemetria.  |
|          Tipo di dati > SensorMeasures       |Tipo di dati di telemetria. Attualmente, il valore Double è supportato.|
|    Tipo > SensorMeasures    |Tipo di misura dei dati di telemetria del sensore. I tipi definiti dal sistema sono Temperaturaambiente, CO2, depth, ElectricalConductivity, LeafWetness, length, LiquidLevel, nitrate, O2, PH, fosfato, PointInTime, potassio, Pressure, RainGauge, RelativeHumidity, salinità, SoilMoisture, SoilTemperature, SolarRadiation, state, TimeDuration, UVRadiation, UVIndex, volume, WindDirection, WindRun, WindSpeed, evapotraspirazione, PAR. Per aggiungere altre informazioni, vedere l'API/ExtendedType.|
|        Unità > SensorMeasures              | Unità di dati di telemetria del sensore. Le unità definite dal sistema sono nounit, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, Mercury, PSI, millimetro, centimetro, metro, pollice, feet, Mile, chilometro, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, Degree, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, percentuale, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, litro, millilitro, seconds, UnixTimestamp, MicroMolPerMeterSquaredPerSecond, InchesPerHour per aggiungere altro, fare riferimento all'API/ExtendedType.|
|    SensorMeasures > AggregationType    |  I valori possibili sono None, Average, Maximum, Minimum o StandardDeviation.  |
|          Name            | Nome per identificare una risorsa. Ad esempio, il nome del modello o il nome del prodotto.  |
|    Descrizione        | Fornire una descrizione significativa del modello.  |
|   Proprietà       |  Proprietà aggiuntive del produttore.  |
|    **Sensore**      |          |
| HardwareId          |   ID univoco per il sensore impostato dal produttore. |
|  SensorModelId     |    ID del modello di sensore associato.   |
| Location          |  Latitudine del sensore (da-90 a + 90), Longitudine (-180 a 180) ed elevazione (in metri).|
|   Nome > porta        |  Nome e tipo della porta a cui il sensore è connesso nel dispositivo. Il nome deve corrispondere a quello definito nel modello di dispositivo. |
|    ID dispositivo  |    ID del dispositivo a cui è connesso il sensore.     |
| Name            |   Nome per identificare la risorsa. Ad esempio, il nome del sensore o il nome del prodotto e il numero di modello o il codice prodotto.|
|    Descrizione      | Fornire una descrizione significativa. |
|    Proprietà        |Proprietà aggiuntive del produttore. |

Per ulteriori informazioni sugli oggetti, vedere [spavalderia](https://aka.ms/FarmBeatsDatahubSwagger).

### <a name="api-request-to-create-metadata"></a>Richiesta API per la creazione di metadati

Per eseguire una richiesta API, combinare il metodo HTTP (POST), l'URL del servizio API e l'URI di una risorsa per eseguire query, inviare dati a, creare o eliminare una richiesta. Quindi si aggiungono una o più intestazioni della richiesta HTTP. L'URL del servizio API è l'endpoint API, ovvero l'URL datahub (https://\<yourdatahub >. azurewebsites. NET).  

### <a name="authentication"></a>Autenticazione

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

**Sintomo**: i dispositivi o i sensori sono stati distribuiti e sono stati creati i dispositivi/sensori in FarmBeats e i dati di telemetria inseriti nel EventHub, ma non è possibile ottenere o visualizzare i dati di telemetria in FarmBeats.

**Azione correttiva**:

1. Assicurarsi che la registrazione del partner sia stata eseguita correttamente. è possibile verificarla selezionando datahub spavalderia, passare all'API/partner, eseguire un'operazione get e verificare se il partner è registrato. In caso contrario, seguire la [procedura descritta qui](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats) per aggiungere il partner.
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
