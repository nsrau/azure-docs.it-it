---
title: Integrazione di partner di immagini
description: Questo articolo descrive l'integrazione dei partner di immagini.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 62e5b363f8008380a61e24c0549573a30ecaeb73
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77131862"
---
# <a name="imagery-partner-integration"></a>Integrazione di partner di immagini

Questo articolo descrive come usare il componente Azure FarmBeats Translator per inviare dati di immagini a FarmBeats.This article describes how to use the Azure FarmBeats Translator component to send imagery data to FarmBeats. I dati delle immagini agricole possono essere generati da varie fonti, come telecamere multispettrali, satelliti e droni. I partner di immagini agricole possono integrarsi con FarmBeats per fornire ai clienti mappe generate su misura per le loro aziende agricole.

I dati, una volta disponibili, possono essere visualizzati tramite FarmBeats Accelerator e potenzialmente utilizzati per la fusione dei dati e la creazione di modelli di machine learning/intelligenza artificiale (ML/AI) da parte di aziende agricole o integratori di sistemi clienti.

FarmBeats offre la possibilità di:

- Definire tipi di immagine personalizzati, origine e formato di file utilizzando le API /ExtendedType.Define custom image types, source, and file format by using /ExtendedType APIs.
- Inserimento di dati di immagini da varie origini tramite le API /Scene e /SceneFile.

Le seguenti informazioni si concentrano sull'ottenimento di qualsiasi forma di immagine nel sistema FarmBeats.

Quando si seleziona la sezione **Immagini drone,** si apre un pop-up per mostrare un'immagine ad alta risoluzione dell'ortomosaico del drone. È possibile accedere al software partner, che aiuta a pianificare i voli drone e ottenere dati grezzi. Continuerai a utilizzare il software del partner per la pianificazione del percorso e l'cucitura ortomosaica delle immagini.

I partner per i droni devono consentire ai clienti di collegare il proprio account cliente all'istanza FarmBeats in Azure.Drone partners need to enable customers to link their customer account with their FarmBeats instance on Azure.

Per collegare FarmBeats, è necessario utilizzare le credenziali seguenti:

- Endpoint API
- ID tenant
- ID client
- Segreto client

## <a name="api-development"></a>Sviluppo di API

Le API contengono la documentazione tecnica di Swagger. Per informazioni sulle API e sulle richieste o risposte corrispondenti, vedere [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

## <a name="authentication"></a>Authentication

FarmBeats usa Microsoft Azure [Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization) (Azure AD). Il servizio app di Azure offre il supporto incorporato per l'autenticazione e l'autorizzazione. 

Per altre informazioni su Azure AD, vedere [Azure Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization).   

FarmBeats Datahub utilizza l'autenticazione di connessione, che richiede le credenziali seguenti:

- ID client
- Segreto client
- ID tenant

Utilizzando le credenziali precedenti, il chiamante può richiedere un token di accesso, che deve essere inviato nelle richieste API successive, nella sezione dell'intestazione, come indicato di seguito:Using the previous credentials, the caller can request an access token, which needs to be sent in the subsequent API requests, in the header section, as follows:

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

L'esempio di codice Python seguente recupera il token di accesso. È quindi possibile utilizzare il token per le chiamate API successive a FarmBeats.You can then use the token for subsequent API calls to FarmBeats.

```python
from azure.common.credentials import ServicePrincipalCredentials 
import adal 
#FarmBeats API Endpoint 
ENDPOINT = "https://<yourdatahub>.azurewebsites.net"   
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

## <a name="http-request-headers"></a>Intestazioni di richiesta HTTP

Ecco le intestazioni di richiesta più comuni che devono essere specificate quando si effettua una chiamata API a FarmBeats Datahub.

**Intestazione** | **Descrizione ed esempio**
--- | ---
Content-Type  | Il formato della richiesta (Content-Type: application/<format>). Per le API FarmBeats Datahub, il formato è JSON. Content-Type: application/json
Autorizzazione | Specifica il token di accesso necessario per effettuare una chiamata API. Autorizzazione:> T-token di connessione <BearerAuthorization: Bearer Access-Token>
Accept  | Formato della risposta. Per le API FarmBeats Datahub, il formato è JSON. Accetta: applicazione/json


## <a name="api-requests"></a>Richieste API

Per effettuare una richiesta di API REST, combinare:To make a REST API request, you combine:

- Il metodo HTTP (GET, POST e PUT).
- URL del servizio API.
- URI della risorsa (per eseguire query, inviare dati, aggiornare o eliminare).
- Una o più intestazioni di richiesta HTTP.

Facoltativamente, è possibile includere parametri di query nelle chiamate GET per filtrare, limitare le dimensioni e ordinare i dati nelle risposte.

La richiesta di esempio seguente consiste nell'ottenere l'elenco dei dispositivi:The following sample request is to get the list of devices:

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H
"Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>”
```

La maggior parte delle chiamate GET, POST e PUT richiede un corpo della richiesta JSON.

La richiesta di esempio seguente consiste nel creare un dispositivo. Questo esempio include un JSON di input con il corpo della richiesta.


```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  
"accept: application/json" -H  
"Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d
"{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  
\"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>Formato dati

JSON è un formato di dati comune indipendente dal linguaggio che fornisce una rappresentazione testuale semplice di strutture di dati arbitrari. Per altre informazioni, vedere [JSON org](https://JSON.org).

## <a name="ingest-imagery-into-farmbeats"></a>Ingeri le immagini in FarmBeats

Dopo che il partner dispone delle credenziali per connettersi a FarmBeats Datahub, il partner esegue i passaggi seguenti nel componente Translator.

1.  Creare un nuovo tipo esteso per i seguenti campi, in base al tipo di immagini da caricare:

    - **Origine scena**: Ad esempio, drone_partner_name
    - **Tipo di scena**: Ad esempio, drone
    - **Tipo di file scena**: Ad esempio, indice clorofilla
    - **Tipo di contenuto del file di scena**: Ad esempio, immagine/tiff

2.  Chiamare l'API /Farms per ottenere l'elenco delle farm dall'interno del sistema FarmBeats di Azure.Call the /Farms API to get the list of farms from within the Azure FarmBeats system.
3.  Offrire al cliente la possibilità di scegliere una singola farm dall'elenco delle farm.

    Il sistema partner deve mostrare la farm all'interno del software partner per eseguire la pianificazione del percorso e la raccolta di voli e immagini del drone.

4.  Chiamare l'API /Scene e fornire i dettagli necessari per creare una nuova scena con un ID scena univoco.
5.  Ricevere un URL di archiviazione BLOB per caricare le immagini necessarie in FarmBeats Datahub, nel contesto della farm scelta, nel sistema FarmBeats.

Ecco un flusso dettagliato sulle chiamate API.

### <a name="step-1-extendedtype"></a>Passaggio 1: ExtendedTypeStep 1: ExtendedType

Controllare l'API /ExtendedType per verificare se il tipo e l'origine file sono disponibili in FarmBeats. A tale scopo, chiamare un GET sull'API /ExtendedType.

Di seguito sono riportati i valori definiti dal sistema:

```json
{
  "items": [
    {
      "id": "ae2aa527-7d27-42e2-8fcf-841937c651d7",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:47:42.5910344Z",
      "key": "SceneFileType",
      "value": [
        "evi",
        "ndvi",
        "ndwi",
        "tci",
        "soil-moisture",
        "sensor-placement",
        "sentinel-b01",
        "sentinel-b02",
        "sentinel-b03",
        "sentinel-b04",
        "sentinel-b05",
        "sentinel-b06",
        "sentinel-b07",
        "sentinel-b08",
        "sentinel-b8a",
        "sentinel-b09",
        "sentinel-b10",
        "sentinel-b11",
        "sentinel-b12",
        "cloud-mask",
        "farm-mask",
        "image/tiff",
        "orthomosaic"
      ],
      "description": "List of scene file types available in system. User can add more values."
    },
    {
      "id": "b7824d2e-3e43-46d3-8fd4-ec7ba8918d84",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:44.8914691Z",
      "key": "SceneFileContentType",
      "value": [
        "image/tiff",
        "image/png",
        "image/jpeg",
        "text/csv",
        "text/plain",
        "text/tab-separated-values",
        "application/json",
        "application/octet-stream",
        "orthomosaic"
      ],
      "description": "List of scene file content types available in system. User can add more values."
    },
    {
      "id": "21d2924d-8b3e-4ce4-a7f8-318e85c0f7f2",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:37.5096445Z",
      "key": "SceneSource",
      "value": [
        "sentinel-l1c",
        "sentinel-l2a",
        "farmbeats-model",
        "dji",
        "SlantRange-3P",
        "DJI"
      ],
      "description": "List of scene sources available in system. User can add more values."
    },
    {
      "id": "6e09ca69-cacf-4b53-b63a-53c659aae4a4",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:34.7483899Z",
      "key": "SceneType",
      "value": [
        "base-bands",
        "sensor-placement",
        "soil-moisture",
        "evi",
        "ndwi",
        "ndvi",
        "drone",
        "orthomosaic-drone"
      ],
      "description": "List of scene types available in system. User can add more values."
    },
    {
      "id": "55d8436b-3a86-4bea-87ab-e1b51226525f",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-06-17T07:32:30.1135716Z",
      "key": "SensorMeasureType",
      "value": [
        "AmbientTemperature",
        "CO2",
        "Depth",
        "ElectricalConductivity",
        "LeafWetness",
        "Length",
        "LiquidLevel",
        "Nitrate",
        "O2",
        "PH",
        "Phosphate",
        "PointInTime",
        "Potassium",
        "Pressure",
        "RainGauge",
        "RelativeHumidity",
        "Salinity",
        "SoilMoisture",
        "SoilTemperature",
        "SolarRadiation",
        "State",
        "TimeDuration",
        "UVRadiation",
        "UVIndex",
        "Volume",
        "WindDirection",
        "WindRun",
        "WindSpeed",
        "Evapotranspiration",
        "PAR"
      ],
      "description": "List of sensor measure types available in system. User can add more values."
    },
    {
      "id": "0dfd6e6b-df58-428f-9ab8-a0674bfdcbe5",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-06-17T07:32:30.1135716Z",
      "key": "SensorMeasureUnit",
      "value": [
        "NoUnit",
        "Celsius",
        "Fahrenheit",
        "Kelvin",
        "Rankine",
        "Pascal",
        "Mercury",
        "PSI",
        "MilliMeter",
        "CentiMeter",
        "Meter",
        "Inch",
        "Feet",
        "Mile",
        "KiloMeter",
        "MilesPerHour",
        "MilesPerSecond",
        "KMPerHour",
        "KMPerSecond",
        "MetersPerHour",
        "MetersPerSecond",
        "Degree",
        "WattsPerSquareMeter",
        "KiloWattsPerSquareMeter",
        "MilliWattsPerSquareCentiMeter",
        "MilliJoulesPerSquareCentiMeter",
        "VolumetricWaterContent",
        "Percentage",
        "PartsPerMillion",
        "MicroMol",
        "MicroMolesPerLiter",
        "SiemensPerSquareMeterPerMole",
        "MilliSiemensPerCentiMeter",
        "Centibar",
        "DeciSiemensPerMeter",
        "KiloPascal",
        "VolumetricIonContent",
        "Liter",
        "MilliLiter",
        "Seconds",
        "UnixTimestamp",
        "MicroMolPerMeterSquaredPerSecond",
        "InchesPerHour"
      ],
      "description": "List of sensor measure units available in system. User can add more values."
    }
  ]
}
```

Questo passaggio è una configurazione una tantera. L'ambito di questo nuovo tipo di scena è limitato alla sottoscrizione in cui è installato Azure FarmBeats.The scope of this new scene type is limited to the subscription in which the Azure FarmBeats is installed.

Ad esempio, per aggiungere SceneSource: "SlantRange", si fa un PUT sull'ID dell'API /ExtendedType con la chiave "SceneSource" payload di input.

```json
{

      "key": "SceneSource",
      "value": [
        "sentinel-l1c",
        "sentinel-l2a",
        "farmbeats-model",
        "dji",
        "SlantRange-3P"
      ],
      "description": "List of scene sources available in system. User can add more values."
}

```

Il campo verde è la nuova aggiunta ai valori di origine della scena definita dal sistema.

### <a name="step-2-get-farm-details"></a>Passaggio 2: Ottenere i dettagli della farmStep 2: Get farm details

Le scene (file con estensione tiff o csv) si trovano nel contesto di una farm. È necessario ottenere i dettagli della farm eseguendo un GET nell'API /Farm. L'API restituisce l'elenco delle farm disponibili in FarmBeats. È possibile selezionare la farm per cui si desidera inserire i dati.

GET /Risposta farm:

```json
{
  "id": "07f3e09c-89aa-4619-8d50-e57fb083d8f9",
  "createdAt": "2019-11-01T13:55:41.8804663Z",
  "lastModifiedAt": "2019-11-01T13:55:41.8804663Z",
  "geometry": {
    "type": "Polygon",
    "coordinates": [
      [
        [
          78.34252251428694,
          17.402556352862675
        ],
        [
          78.34278000635095,
          17.407920852463022
        ],
        [
          78.34883106989963,
          17.40878079576528
        ],
        [
          78.3498181228054,
          17.403539173730607
        ],
        [
          78.34805859369493,
          17.39977166504127
        ],
        [
          78.34252251428694,
          17.402556352862675
        ]
      ]
    ]
  },
  "name": "Samplefarm",
  "properties": {
    "crops": "Corn",
    "address": "Sample street"
  }
}
 ```

### <a name="step-3-create-a-scene-id-post-call"></a>Passaggio 3: Creare un ID scena (chiamata POST)

Creare una nuova scena (file con estensione tiff o csv) con le informazioni fornite, che fornisce la data, la sequenza e l'ID della farm a cui è associata la scena. I metadati associati alla scena possono essere definiti in proprietà, che includono la durata e il tipo di misura.

La creazione di una nuova scena crea un nuovo ID scena, associato alla farm. Dopo aver creato l'ID scena, l'utente può utilizzare lo stesso per creare un nuovo file (.tiff o .csv) e memorizzare il contenuto del file.

Esempio di payload di input per la chiamata POST sull'API /Scene:Example input payload for the POST call on the /Scene API:

```json
{
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

Risposta API:

```json
{
  "id": "a0505928-c480-491b-ba31-d38285a28c1d",
  "createdAt": "2019-10-04T16:19:12.4838584Z",
  "lastModifiedAt": "2019-10-04T16:19:12.4838584Z",
  "type": "new type",
  "source": "SlantRange-3P",
  "farmId": "d41a33e7-b73e-480e-9279-0fcb3207332b",
  "date": "2019-10-04T16:13:39.064Z",
  "sequence": 5,
  "name": "test scene",
  "description": "test scene description",
  "properties": {}
}

```

**Creare un file di scena**

L'ID scena restituito nel passaggio 3 è l'input per il file di scena. Il file di scena restituisce un token URL di firma di accesso condiviso, valido per 24 ore.

Se l'utente richiede un modo a livello di codice per caricare un flusso di immagini, l'SDK di archiviazione BLOB può essere usato per definire un metodo usando l'ID, il percorso e l'URL del file di scena.

Esempio di payload di input per la chiamata POST sull'API /SceneFile:Example input payload for the POST call on the /SceneFile API:

```json
{
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```
Risposta API:

```json
{
  "uploadSASUrl": "https://storagej2lho.blob.core.windows.net/farm-scene/2019/a0505928-c480-491b-ba31-d38285a28c1d/e91139a7-4ebd-4e2f-b17c-c677822dc840?sv=2018-03-28&sr=b&sig=%2F1426JkDcIFE5g3d%2BjOevCVMIn%2FJo9YKwBn3La5zL8Y%3D&se=2019-10-05T16%3A23%3A57Z&sp=w",
  "id": "e91139a7-4ebd-4e2f-b17c-c677822dc840",
  "createdAt": "2019-10-04T16:23:57.1192916Z",
  "lastModifiedAt": "2019-10-04T16:23:57.1192916Z",
  "blobUrl": "https://storagej2lho.blob.core.windows.net/farm-scene/2019/a0505928-c480-491b-ba31-d38285a28c1d/e91139a7-4ebd-4e2f-b17c-c677822dc840",
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {}
}


```

La chiamata POST all'API /SceneFile restituisce un URL di caricamento SAS, che può essere usato per caricare il file CSV o tiff usando la libreria o il client di archiviazione BLOB di Azure.The POST call to the /SceneFile API returns an SAS upload URL, which can be used to upload the .csv or .tiff file by using the Azure Blob storage client or library.


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui dettagli di integrazione basata su API REST, vedere [API REST.](rest-api-in-azure-farmbeats.md)
