---
title: Integrazione di partner di sensori
description: Questo articolo descrive l'integrazione del partner di sensori.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 1570e85d93e7d82b5a842697a7755603247375b0
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896537"
---
# <a name="sensor-partner-integration"></a>Integrazione di partner di sensori

Questo articolo fornisce informazioni sul componente Azure FarmBeats translator, che consente l'integrazione del partner di sensori.

Grazie a questo componente, i partner possono sviluppare sensori che si integrano con FarmBeats per usare l'API e inviare dati del dispositivo e telemetria del cliente a FarmBeats datahub. I dati vengono visualizzati tramite l'acceleratore FarmBeats. I dati possono essere usati per la fusione dei dati e per la creazione di modelli di Machine Learning/intelligenza artificiale.

## <a name="link-a-farmbeats-account"></a>Collega un account FarmBeats

Dopo aver acquistato e distribuito i dispositivi o i sensori, è possibile accedere ai dati del dispositivo e ai dati di telemetria nel portale di Software as a Service (SaaS) del partner dispositivo. I partner di dispositivi consentono di collegare l'account all'istanza di FarmBeats in Azure. Le credenziali seguenti devono essere compilate dall'utente o dall'integratore del sistema:

   - Nome visualizzato (campo facoltativo che consente agli utenti di definire un nome per l'integrazione)
   - Endpoint API
   - ID tenant
   - ID client
   - Segreto client
   - Stringa di connessione EventHub
   - Data di inizio

   > [!NOTE]
   > La data di inizio Abilita il feed di dati cronologici, ovvero i dati della data specificata dall'utente.

## <a name="unlink-farmbeats"></a>Scollega FarmBeats

È possibile scollegare un'integrazione FarmBeats esistente. Lo scollegamento di FarmBeats non deve eliminare i metadati del dispositivo o del sensore creati nell'hub dati. Lo scollegamento esegue le operazioni seguenti:

   - Arresta il flusso di telemetria.
   - Elimina e cancella le credenziali di integrazione nel partner del dispositivo.

## <a name="edit-farmbeats-integration"></a>Modifica integrazione FarmBeats

È possibile modificare le impostazioni di integrazione FarmBeats se il segreto client o la stringa di connessione viene modificata. In questo caso, è possibile modificare solo i campi seguenti:

   - Nome visualizzato (se applicabile)
   - Segreto client (dovrebbe essere visualizzato nel formato "2x8 * * * * * * * * * * *" o nella funzionalità Mostra/Nascondi anziché in testo non crittografato)
   - Stringa di connessione (dovrebbe essere visualizzata in "2x8 * * * * * * * * * * *" formato o Mostra/Nascondi funzionalità anziché testo non crittografato)

## <a name="view-the-last-telemetry-sent"></a>Visualizza l'ultimo telemetria inviato

È possibile visualizzare il timestamp dell'ultimo telemetria inviato, disponibile in **telemetria inviato**. Si tratta dell'ora in cui la telemetria più recente è stata inviata correttamente a FarmBeats.

## <a name="translator-development"></a>Sviluppo di convertitori

**Integrazione basata sull'API REST**

Le funzionalità di integrazione dei dati dei sensori di FarmBeats vengono esposte tramite l'API REST. Le funzionalità includono la definizione dei metadati, il provisioning del dispositivo e del sensore e la gestione dei dispositivi e dei sensori.

**Inserimento di dati di telemetria**

Viene eseguito il mapping dei dati di telemetria a un messaggio canonico pubblicato negli hub eventi di Azure per l'elaborazione. Hub eventi di Azure è un servizio che consente l'inserimento di dati in tempo reale (telemetria) da applicazioni e dispositivi connessi.

**Sviluppo di API**

Le API contengono la documentazione tecnica di spavalderia. Per altre informazioni sulle API e sulle richieste o risposte corrispondenti, vedere [spavalderia](https://aka.ms/FarmBeatsDatahubSwagger).

**Autenticazione**

FarmBeats usa l'autenticazione Microsoft Azure Active Directory. App Azure servizio fornisce supporto predefinito per l'autenticazione e l'autorizzazione.

Per altre informazioni, vedere [Azure Active Directory - Documentazione](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization).

FarmBeats datahub usa l'autenticazione della porta, che richiede le credenziali seguenti:
   - ID client
   - Segreto client
   - ID tenant

Usando queste credenziali, il chiamante può richiedere un token di accesso. Il token deve essere inviato nelle richieste API successive, nella sezione header, come indicato di seguito:

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

Il codice Python di esempio seguente fornisce il token di accesso, che può essere usato per le chiamate API successive a FarmBeats.

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

Di seguito sono riportate le intestazioni di richiesta più comuni che è necessario specificare quando si effettua una chiamata API a FarmBeats datahub.


**Intestazione** | **Descrizione ed esempio**
--- | ---
Content-Type | Formato della richiesta (Content-Type: Application/<format>). Per le API datahub di FarmBeats, il formato è JSON. Content-Type: application/json
Authorization | Specifica il token di accesso necessario per effettuare una chiamata API. Autorizzazione: Bearer < Access-token >
Accept | Formato della risposta. Per le API datahub di FarmBeats, il formato è JSON. Accept: Application/JSON

**Richieste API**

Per eseguire una richiesta API REST, combinare il metodo HTTP (GET, POST o PUT), l'URL del servizio API, il Uniform Resource Identifier (URI) a una risorsa per eseguire query, inviare dati a, aggiornare o eliminare e una o più intestazioni della richiesta HTTP. L'URL del servizio API è l'endpoint API fornito. Di seguito è riportato un esempio: https://\<yourdatahub-website-name >. azurewebsites. NET

Facoltativamente, è possibile includere i parametri di query sulle chiamate GET da filtrare, limitare le dimensioni e ordinare i dati nelle risposte.

La richiesta di esempio seguente consente di ottenere l'elenco dei dispositivi.

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>"
```
Per la maggior parte delle chiamate GET, POST e PUT è necessario un corpo della richiesta JSON.

La richiesta di esempio seguente consiste nel creare un dispositivo. Questo esempio include un JSON di input con il corpo della richiesta.

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>Formato dati

JSON è un formato di dati comune indipendente dal linguaggio che fornisce una semplice rappresentazione testuale di strutture di dati arbitrarie. Per ulteriori informazioni, vedere [JSON.org](http://json.org).

## <a name="metadata-specifications"></a>Specifiche dei metadati

FarmBeats datahub include le API seguenti che consentono ai partner di dispositivi di creare e gestire i metadati del dispositivo o del sensore. 

- /**DeviceModel**: DeviceModel corrisponde ai metadati del dispositivo, ad esempio il produttore e il tipo di dispositivo, ovvero gateway o nodo. 
- /**dispositivo**: il dispositivo corrisponde a un dispositivo fisico presente nella farm.
- /**SensorModel**: SensorModel corrisponde ai metadati del sensore, ad esempio il produttore, il tipo di sensore, che può essere analogico o digitale, e la misura del sensore, ad esempio la temperatura e la pressione dell'ambiente.
- **sensore**/: il sensore corrisponde a un sensore fisico che registra i valori. Un sensore è in genere connesso a un dispositivo con un ID dispositivo.
  
  **DeviceModel** |  |
  --- | ---
  Tipo (nodo, gateway)  | 1 stella |
  Produttore  | 2 stelle |
  ProductCode  | Codice prodotto del dispositivo o numero o nome del modello. Ad esempio, EnviroMonitor # 6800. |
  Porte  | Nome e tipo della porta, che è digitale o analogo.  |
  name  | Nome per identificare la risorsa. Ad esempio il nome del modello o il nome del prodotto. |
  Description  | Fornire una descrizione significativa del modello. |
  properties  | Proprietà aggiuntive del produttore. |
  **Dispositivo** |  |
  DeviceModelId  |ID del modello di dispositivo associato. |
  hardwareId   |ID univoco per il dispositivo, ad esempio un indirizzo MAC.  |
  ReportingInterval |Intervallo di Reporting in secondi. |
  Località    |Latitudine del dispositivo (da-90 a + 90), Longitudine (-180 a 180) ed elevazione (in metri). |
  ParentDeviceId | ID del dispositivo padre a cui è connesso il dispositivo. Ad esempio, se un nodo è connesso a un gateway, il nodo avrà parentDeviceID come gateway. |
  name  | Nome per identificare la risorsa. I partner del dispositivo devono inviare un nome coerente con il nome del dispositivo sul lato del partner del dispositivo. Se il nome del dispositivo è definito da utente sul lato del partner del dispositivo, lo stesso nome definito dall'utente deve essere propagato a FarmBeats.  |
  Description  | Fornire una descrizione significativa.  |
  properties  |Proprietà aggiuntive del produttore.  |
  **SensorModel** |  |
  Tipo (analogico, digitale)  |Menzione sensore analogico o digitale.|
  Produttore  | Nome del produttore. |
  ProductCode  | Codice prodotto o nome modello o numero. Ad esempio, RS-CO2-N01.  |
  Nome > SensorMeasures  | Nome della misura del sensore. Sono supportati solo caratteri minuscoli. Per le misurazioni di profondità diverse, specificare la profondità. Ad esempio, soil_moisture_15cm. Questo nome deve essere coerente con i dati di telemetria. |
  Tipo di dati > SensorMeasures  | Tipo di dati di telemetria. Attualmente, il valore Double è supportato. |
  Tipo > sensorMeasures  | Tipo di misura dei dati di telemetria del sensore. Di seguito sono riportati i tipi definiti dal sistema: Temperaturaambiente, CO2, depth, ElectricalConductivity, LeafWetness, length, LiquidLevel, nitrate, O2, PH, fosfato, PointInTime, potassio, Pressure, RainGauge, RelativeHumidity, salinità, SoilMoisture, SoilTemperature, SolarRadiation, state, TimeDuration, UVRadiation, UVIndex, volume, WindDirection, WindRun, WindSpeed, evapotraspirazione, PAR. Per aggiungere altre informazioni, vedere l'API/ExtendedType.
  Unità > SensorMeasures | Unità di dati di telemetria del sensore. Di seguito sono riportate le unità definite dal sistema: nounit, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, Mercury, PSI, millimetro, centimetro, Meter, inch, feet, Mile, kilometr, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, Degree, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, percentual, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, Liter, Millilitr, seconds, UnixTimestamp, MicroMolPerMeterSquaredPerSecond e InchesPerHour. Per aggiungere altre informazioni, vedere l'API/ExtendedType.
  SensorMeasures > AggregationType  | None, Average, Maximum, Minimum o StandardDeviation.
  Profondità > SensorMeasures  | Profondità del sensore in centimetri. Ad esempio, la misurazione dell'umidità di 10 cm sotto la superficie.
  Descrizione > SensorMeasures  | Fornire una descrizione significativa della misura.
  name  | Nome per identificare la risorsa. Ad esempio, il nome del modello o il nome del prodotto.
  Description  | Fornire una descrizione significativa del modello.
  properties  | Proprietà aggiuntive del produttore.
  **Sensore**  |  |
  hardwareId  | ID univoco per il sensore impostato dal produttore.
  SensorModelId  | ID del modello di sensore associato.
  Località  | Latitudine del sensore (da-90 a + 90), Longitudine (-180 a 180) ed elevazione (in metri).
  Nome > porta  |Nome e tipo della porta a cui il sensore è connesso nel dispositivo. Deve corrispondere al nome definito nel modello di dispositivo.
  deviceId  | ID del dispositivo a cui è connesso il sensore.
  name  | Nome per identificare la risorsa. Ad esempio, il nome del sensore o il nome del prodotto e il numero di modello o il codice prodotto.
  Description  | Fornire una descrizione significativa.
  properties  | Proprietà aggiuntive del produttore.

 Per informazioni su ogni oggetto e sulle relative proprietà, vedere [spavalderia](https://aka.ms/FarmBeatsDatahubSwagger).

 > [!NOTE]
 > Le API restituiscono ID univoci per ogni istanza creata. Questo ID deve essere mantenuto dal convertitore per la gestione dei dispositivi e la sincronizzazione dei metadati.


**Sincronizzazione dei metadati**

Il convertitore deve inviare aggiornamenti sui metadati. Gli scenari di aggiornamento, ad esempio, cambiano il nome del dispositivo o del sensore e la modifica del percorso del dispositivo o del sensore.

Il convertitore deve avere la possibilità di aggiungere nuovi dispositivi o sensori installati dall'utente post linking di FarmBeats. Analogamente, se un dispositivo o un sensore è stato aggiornato dall'utente, lo stesso dovrebbe essere aggiornato in FarmBeats per il dispositivo o il sensore corrispondente. Gli scenari tipici che richiedono l'aggiornamento di un dispositivo o di un sensore sono una modifica in un percorso del dispositivo o l'aggiunta di sensori in un nodo.


> [!NOTE]
> L'eliminazione non è supportata per i metadati del dispositivo o del sensore.
>
> Per aggiornare i metadati, è obbligatorio chiamare/Get/{ID} sul dispositivo o sul sensore, aggiornare le proprietà modificate, quindi eseguire un/put/{ID} in modo che le proprietà impostate dall'utente non vadano perse.

### <a name="add-new-types-and-units"></a>Aggiungere nuovi tipi e unità

FarmBeats supporta l'aggiunta di nuovi tipi e unità di misura del sensore. Per altre informazioni sull'API/ExtendedType, vedere [spavalderia](https://aka.ms/FarmBeatsDatahubSwagger).

## <a name="telemetry-specifications"></a>Specifiche di telemetria

Viene eseguito il mapping dei dati di telemetria a un messaggio canonico pubblicato negli hub eventi di Azure per l'elaborazione. Hub eventi di Azure è un servizio che consente l'inserimento di dati in tempo reale (telemetria) da applicazioni e dispositivi connessi.

## <a name="send-telemetry-data-to-farmbeats"></a>Inviare dati di telemetria a FarmBeats

Per inviare i dati di telemetria a FarmBeats, creare un client che invii messaggi a un hub eventi in FarmBeats. Per altre informazioni sui dati di telemetria, vedere [invio di dati di telemetria a un hub eventi](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send).

Ecco un codice Python di esempio che invia dati di telemetria come client a un hub eventi specificato.

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

Il formato del messaggio canonico è il seguente:

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
Tutti i nomi di chiave nel codice JSON di telemetria devono essere minuscoli. Esempi sono DeviceID e SensorData.

Ad esempio, ecco un messaggio di telemetria:


```json
{
  "deviceid": "7f9b4b92-ba45-4a1d-a6ae-c6eda3a5bd12",
  "timestamp": "2019-06-22T06:55:02.7279559Z",
  "version" : "1",
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

## <a name="troubleshooting-and-error-management"></a>Risoluzione dei problemi e gestione degli errori

**Risolvere i problemi relativi all'opzione o al supporto**

Se non si è in grado di ricevere dati del dispositivo o dati di telemetria nell'istanza di FarmBeats specificata, il partner del dispositivo deve fornire supporto e un meccanismo per la risoluzione dei problemi.

**Conservazione dei dati di telemetria**

I dati di telemetria devono essere conservati anche per un periodo di tempo predefinito, in modo che possa essere utile per eseguire il debug o inviare nuovamente i dati di telemetria in caso di errore o perdita di dati.

**Gestione degli errori o notifica degli errori**

Se un errore influisce sui metadati del dispositivo o del sensore o del flusso di dati di integrazione dei dati o di telemetria nel sistema di partner dei dispositivi, è necessario ricevere una notifica. Inoltre, è necessario progettare e implementare un meccanismo per risolvere eventuali errori.

**Elenco di controllo per la connessione**

I produttori di dispositivi o i partner possono utilizzare l'elenco di controllo seguente per assicurarsi che le credenziali fornite dal cliente siano accurate:

   - Verificare se è stato ricevuto un token di accesso con le credenziali fornite.
   - Verificare se una chiamata API ha esito positivo con il token di accesso ricevuto.
   - Verificare se è stata stabilita la connessione del client EventHub.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'API REST, vedere [API REST](references-for-farmbeats.md#rest-api).
