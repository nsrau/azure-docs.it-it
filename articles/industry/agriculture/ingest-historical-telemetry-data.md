---
title: Inserire dati di telemetria cronologici
description: Viene descritto come ottenere i dati di telemetria cronologici di inserimento
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: e721a7818c5f2fcea23263b296912edf164036b2
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73927807"
---
# <a name="ingest-historical-telemetry-data"></a>Inserire dati di telemetria cronologici

Questo articolo descrive come inserire i dati cronologici dei sensori in FarmBeats di Azure.

L'inserimento di dati cronologici da Internet delle cose per risorse quali dispositivi e sensori è uno scenario comune in FarmBeats. Si creano i metadati per i dispositivi e i sensori e quindi si inseriscono i dati cronologici in FarmBeats in un formato canonico.

## <a name="before-you-begin"></a>Prima di iniziare

Prima di procedere con questo articolo, verificare di aver installato FarmBeats e raccolto i dati cronologici da Internet.

## <a name="enable-partner-access"></a>Abilitare l'accesso ai partner

È necessario abilitare l'integrazione dei partner nell'istanza di Azure FarmBeats. Questo passaggio crea un client che avrà accesso alla FarmBeats di Azure come partner del dispositivo e fornisce i valori seguenti che sono necessari nei passaggi successivi.

- Endpoint API: URL dell'hub dati, ad esempio https://<datahub>. azurewebsites.net
- ID tenant
- ID Client
- Segreto client
- Stringa di connessione EventHub

Attenersi ai passaggi seguenti per generarli:

>[!NOTE]
> Per eseguire la procedura seguente, è necessario essere un amministratore.

1. Scaricare questo [script](https://aka.ms/farmbeatspartnerscript) ed estrarlo nell'unità locale. Sono presenti due file all'interno del file ZIP.
2. Accedere a [portale di Azure](https://portal.azure.com/) e aprire cloud Shell (questa opzione è disponibile nella barra superiore destra del portale).  

    ![Beat Farm progetto](./media/for-tutorials/navigation-bar-1.png)

3. Verificare che l'ambiente sia impostato su **PowerShell**.

    ![Beat Farm progetto](./media/for-tutorials/power-shell-new-1.png)

4. Caricare i due file scaricati (dal passaggio 1 precedente) nell'Cloud Shell.  

    ![Beat Farm progetto](./media/for-tutorials/power-shell-two-1.png)

5. Passare alla directory in cui sono stati caricati i file

   >[!NOTE]
   > Per impostazione predefinita, il file viene caricato nella home directory/home/username/.
6. Eseguire lo script usando il comando:  

    ```azurepowershell-interactive
    ./generateCredentials.ps1
    ```

7. Seguire le istruzioni visualizzate per completare la procedura.

## <a name="create-devicesensor-metadata"></a>Crea metadati del dispositivo/sensore

 Ora che si dispone delle credenziali necessarie, è possibile definire il dispositivo e i sensori creando i metadati usando le API FarmBeats.

 L'hub dati FarmBeats include le API seguenti che consentono di creare e gestire i metadati del dispositivo/sensore.   

- /modello **DeviceModel** -device corrisponde ai metadati del dispositivo, ad esempio il produttore, il tipo del dispositivo gateway o node.  
- /**dispositivo-dispositivo corrisponde** a un dispositivo fisico presente nella farm.  
- /modello **SensorModel** -Sensor corrisponde ai metadati del sensore, ad esempio il produttore, il tipo di sensore analogico o digitale, misura sensore come temperatura ambiente o pressione.
- /**sensore-sensore corrisponde** a un sensore fisico che registra i valori. Un sensore è in genere connesso a un dispositivo con un ID dispositivo.  


|        Modalità dispositivo   |  Suggerimenti:   |
| ------- | -------             |
|     Tipo (nodo, gateway)        |          1 stella      |
|          Produttore            |         2 stelle     |
|  ProductCode                    |  Codice prodotto del dispositivo o nome/numero del modello. Ad esempio, EnviroMonitor # 6800.  |
|            Porte          |     Nome e tipo di porta (digitale/analogico)
|     Nome                 |  Nome per identificare la risorsa. Ad esempio il nome del modello o il nome del prodotto.
      DESCRIZIONE     | Fornire una descrizione significativa del modello
|    Proprietà          |    Proprietà aggiuntive del produttore   |
|    **Dispositivo**             |                      |
|   DeviceModelId     |     ID del modello di dispositivo associato  |
|  hardwareId          | ID univoco per il dispositivo, ad esempio l'indirizzo MAC e così via.
|  reportingInterval        |   Intervallo di Reporting in secondi
|  Location            |  Latitudine del dispositivo (da-90 a + 90)/Longitude (-180 a 180)/Elevation (in metri)   
|ParentDeviceId       |    ID del dispositivo padre a cui è connesso il dispositivo. Ad esempio, un nodo connesso a un gateway. Un nodo avrà parentDeviceId come gateway.  |
|    Nome            | Nome per identificare la risorsa. I partner del dispositivo devono inviare un nome coerente con il nome del dispositivo sul lato del partner. Se il nome del dispositivo partner è definito dall'utente, lo stesso nome definito dall'utente deve essere propagato a FarmBeats.|
|     DESCRIZIONE       |      Fornire una descrizione significativa  |
|     Proprietà    |  Proprietà aggiuntive del produttore
|     **Modello di sensore**        |          |
|       Tipo (analogico, digitale)          |      tipo di sensore che indica se analogico o digitale       |
|          Produttore            |       produttore del sensore     |
|     ProductCode| Codice prodotto o nome modello/numero. Ad esempio, RS-CO2-N01. |
|       Nome > SensorMeasures       | Nome della misura del sensore. Sono supportate solo lettere minuscole. Per la misura da profondità diverse, specificare la profondità. Ad esempio, soil_moisture_15cm. Questo nome deve essere coerente con i dati di telemetria  |
|          Tipo di dati > SensorMeasures       |Tipo di dati di telemetria. Attualmente il doppio è supportato|
|    Tipo > sensorMeasures    |Tipo di misura dei dati di telemetria del sensore. Di seguito sono riportati i tipi definiti dal sistema: Temperaturaambiente, CO2, depth, ElectricalConductivity, LeafWetness, length, LiquidLevel, nitrate, O2, PH, fosfato, PointInTime, potassio, Pressure, RainGauge, RelativeHumidity, salinità, SoilMoisture, SoilTemperature, SolarRadiation, state, TimeDuration, UVRadiation, UVIndex, volume, WindDirection, WindRun, WindSpeed, evapotraspirazione, PAR. Per aggiungere altre informazioni, vedere l'API/ExtendedType.|
|        Unità > SensorMeasures              | Unità di dati di telemetria del sensore. Di seguito sono riportate le unità definite dal sistema: nounit, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, Mercury, PSI, millimetro, centimetro, Meter, inch, feet, Mile, kilometr, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, Degree, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, percentual, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, Liter, Millilitr, seconds, UnixTimestamp, MicroMolPerMeterSquaredPerSecond, InchesPerHour per aggiungere altro, vedere/ API ExtendedType.|
|    SensorMeasures > aggregationType    |  I valori possibili sono None, Average, Maximum, Minimum o StandardDeviation  |
|          Nome            | Nome per identificare la risorsa. Ad esempio il nome del modello o il nome del prodotto.  |
|    DESCRIZIONE        | Fornire una descrizione significativa del modello  |
|   Proprietà       |  Proprietà aggiuntive del produttore  |
|    **Sensore**      |          |
| hardwareId          |   ID univoco per il sensore impostato dal produttore |
|  SensorModelId     |    ID del modello di sensore associato   |
| location          |  Latitudine del sensore (da-90 a + 90)/Longitude (-180 a 180)/Elevation (in metri)|
|   nome > porta        |  Nome e tipo della porta a cui il sensore è connesso nel dispositivo. Il nome deve corrispondere a quello definito nel modello di dispositivo. |
|    DeviceID  |    ID del dispositivo a cui è connesso il sensore     |
| Nome            |   Nome per identificare la risorsa. Ad esempio il nome del sensore, il nome del prodotto e il numero di modello/codice del prodotto.|
|    DESCRIZIONE      | Fornire una descrizione significativa |
|    Proprietà        |Proprietà aggiuntive del produttore |

Per ulteriori informazioni sugli oggetti, vedere [spavalderia](https://aka.ms/FarmBeatsDatahubSwagger).

**Richiesta API per la creazione di metadati**

Per eseguire una richiesta API, combinare il metodo HTTP (POST), l'URL del servizio API, l'URI di una risorsa per eseguire una query, inviare i dati per creare o eliminare una richiesta e aggiungere una o più intestazioni di richiesta HTTP. L'URL del servizio API è l'endpoint dell'API, ad esempio l'URL dell'hub dati (https://<yourdatahub>. azurewebsites.net)  

**Autenticazione**:

FarmBeats Data Hub USA l'autenticazione della porta, che richiede le credenziali seguenti generate nella sezione precedente.

- ID Client
- Segreto client
- ID tenant  

Usando le credenziali sopra riportate, il chiamante può richiedere un token di accesso, che deve essere inviato nelle richieste API successive nella sezione dell'intestazione, come indicato di seguito:

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

**Intestazioni di richiesta http**:

Di seguito sono riportate le intestazioni di richiesta più comuni che è necessario specificare quando si effettua una chiamata API a FarmBeats Data Hub:

- Content-Type: Application/JSON
- Autorizzazione: Bearer < Access-token >
- Accept: Application/JSON

**Payload di input per la creazione dei metadati**:

**DeviceModel**


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
La richiesta di esempio seguente prevede la creazione di un dispositivo, che include un input JSON come payload con il corpo della richiesta.  

```bash
curl -X POST "https://<datahub>.azurewebsites.net/Device" -H  
"accept: application/json" -H  "Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  
\"reportingInterval\": 900,  \"name\": \"Device123\",  
\"description\": \"Test Device 123\"}" *
```

> [!NOTE]
> Le API restituiscono ID univoci per ogni istanza creata. È necessario mantenere gli ID per inviare i messaggi di telemetria corrispondenti.

**Inviare dati di telemetria**

Ora che sono stati creati i dispositivi e i sensori in FarmBeats, è possibile inviare i messaggi di telemetria associati.  

**Creare un client di telemetria**

È necessario inviare i dati di telemetria all'hub eventi di Azure per l'elaborazione. Azure EventHub è un servizio che consente l'inserimento di dati in tempo reale (telemetria) da applicazioni e dispositivi connessi. Per inviare i dati di telemetria a FarmBeats, è necessario creare un client che invii messaggi a un hub eventi in FarmBeats. Per altre informazioni sull'invio di dati di telemetria, vedere [Hub eventi di Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send).

**Inviare un messaggio di telemetria come client**

Una volta stabilita una connessione come client EventHub, è possibile inviare messaggi al EventHub come JSON.  
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

Dopo aver aggiunto i dispositivi e i sensori corrispondenti, ottenere il DeviceID e il sensorid nel messaggio di telemetria, come descritto nella sezione precedente.

Messaggio di telemetria di esempio:


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
