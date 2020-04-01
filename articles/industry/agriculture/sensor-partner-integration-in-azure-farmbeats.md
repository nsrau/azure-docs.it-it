---
title: Integrazione di partner di sensori
description: Questo articolo descrive l'integrazione dei partner di sensori.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 3431576acbb01a0cc3a5f372460b28be05bf7ce7
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437474"
---
# <a name="sensor-partner-integration"></a>Integrazione di partner di sensori

Questo articolo fornisce informazioni sul componente Azure FarmBeats **Translator,** che consente l'integrazione dei partner del sensore.

Utilizzando questo componente, i partner possono integrarsi con FarmBeats usando le API FarmBeats Datahub e inviare dati e dati di telemetria dei dispositivi dei clienti a FarmBeats Datahub. Una volta che i dati sono disponibili in FarmBeats, vengono visualizzati utilizzando FarmBeats Accelerator e possono essere utilizzati per la fusione dei dati e per la costruzione di modelli di apprendimento automatico / intelligenza artificiale.

## <a name="before-you-start"></a>Prima di iniziare

Per sviluppare il componente Translator, sono necessarie le credenziali seguenti che consentiranno l'accesso alle API FarmBeats.

- Endpoint API
- ID tenant
- ID client
- Client Secret
- Stringa di connessione EventHub

Vedere questa sezione per ottenere le credenziali precedenti: [Abilitare l'integrazione dei dispositiviSee](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats) this section for getting the above credentials: Enable Device Integration

## <a name="translator-development"></a>Sviluppo del traduttore

**Integrazione basata sull'API REST**

Le funzionalità di integrazione dei dati dei sensori di FarmBeats vengono esposte tramite l'API REST. Le funzionalità includono la definizione dei metadati, il provisioning di dispositivi e sensori e la gestione di dispositivi e sensori.

**Inserimento di telemetriaTelemetry ingestion**

I dati di telemetria vengono mappati a un messaggio canonico pubblicato negli hub eventi di Azure per l'elaborazione. Hub eventi di Azure è un servizio che consente l'inserimento di dati in tempo reale (telemetria) da applicazioni e dispositivi connessi.

**Sviluppo di API**

Le API contengono la documentazione tecnica di Swagger. Per ulteriori informazioni sulle API e sulle richieste o risposte corrispondenti, vedere [Swagger](https://aka.ms/FarmBeatsSwagger).

**autenticazione**

FarmBeats utilizza l'autenticazione di Microsoft Azure Active Directory.Il servizio app di Azure offre il supporto incorporato per l'autenticazione e l'autorizzazione.

Per altre informazioni, vedere [Azure Active Directory - Documentazione](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization).

FarmBeats Datahub utilizza l'autenticazione di connessione, che richiede le credenziali seguenti:
   - ID client
   - Segreto client
   - ID tenant

Utilizzando queste credenziali, il chiamante può richiedere un token di accesso. Il token deve essere inviato nelle richieste API successive, nella sezione dell'intestazione, come indicato di seguito:The token needs to be sent in the subsequent API requests, in the header section, as follows:

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

The following sample Python code gives the access token, which can be used for subsequent API calls to FarmBeats.

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

Ecco le intestazioni di richiesta più comuni che devono essere specificate quando si effettua una chiamata API a FarmBeats Datahub.


**Intestazione** | **Descrizione ed esempio**
--- | ---
Content-Type | Il formato della richiesta (Content-Type: application/<format>). Per le API FarmBeats Datahub, il formato è JSON. Content-Type: application/json
Autorizzazione | Specifica il token di accesso necessario per effettuare una chiamata API. Autorizzazione:> T-token di connessione <BearerAuthorization: Bearer Access-Token>
Accept | Formato della risposta. Per le API FarmBeats Datahub, il formato è JSON. Accetta: applicazione/json

**Richieste API**

Per effettuare una richiesta di API REST, combinare il metodo HTTP (GET, POST o PUT), l'URL del servizio API, l'URI (Uniform Resource Identifier) a una risorsa per eseguire query, inviare dati, aggiornare o eliminare e una o più intestazioni di richiesta HTTP. L'URL del servizio API è l'endpoint API fornito. Ecco un esempio:\<https:// yourdatahub-website-name>.azurewebsites.net

Facoltativamente, è possibile includere parametri di query nelle chiamate GET per filtrare, limitare le dimensioni e ordinare i dati nelle risposte.

La richiesta di esempio seguente consiste nell'ottenere l'elenco dei dispositivi.

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>"
```
La maggior parte delle chiamate GET, POST e PUT richiede un corpo della richiesta JSON.

La richiesta di esempio seguente consiste nel creare un dispositivo. Questo esempio include un JSON di input con il corpo della richiesta.

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>Formato dati

JSON è un formato di dati comune indipendente dal linguaggio che fornisce una rappresentazione testuale semplice di strutture di dati arbitrari. Per ulteriori informazioni, vedere [json.org](http://json.org).

## <a name="metadata-specifications"></a>Specifiche dei metadati

FarmBeats Datahub ha le API seguenti che consentono ai partner di dispositivi di creare e gestire i metadati del dispositivo o del sensore.

- /**DeviceModel**: DeviceModel corrisponde ai metadati del dispositivo, ad esempio il produttore e il tipo di dispositivo, ovvero gateway o nodo.
- /**Dispositivo:** il dispositivo corrisponde a un dispositivo fisico presente nella farm.
- /**SensorModel**: SensorModel corrisponde ai metadati del sensore, come il produttore, il tipo di sensore, analogico o digitale, e la misura del sensore, ad esempio la temperatura ambiente e la pressione.
- /**Sensore**: Sensore corrisponde a un sensore fisico che registra i valori. Un sensore è in genere collegato a un dispositivo con un ID dispositivo.

  **Modello Dispositivo** |  |
  --- | ---
  Tipo (nodo, gateway)  | Tipo di dispositivo - Nodo o gateway |
  Produttore  | Nome del produttore |
  Productcode  | Codice prodotto del dispositivo o nome o numero del modello. Ad esempio, EnviroMonitor 6800. |
  Porte  | Nome e tipo della porta, che è digitale o analogico.  |
  Nome  | Nome per identificare la risorsa. Ad esempio, il nome del modello o il nome del prodotto. |
  Descrizione  | Fornire una descrizione significativa del modello. |
  Proprietà  | Proprietà aggiuntive del produttore. |
  **Dispositivo** |  |
  DeviceModelId  |ID del modello di dispositivo associato. |
  HardwareId (ID hardware)   |ID univoco per il dispositivo, ad esempio un indirizzo MAC.  |
  ReportingInterval |Intervallo di segnalazione in secondi. |
  Location    |Latitudine del dispositivo (da -90 a 90 dollari), longitudine (-180 a 180) e elevazione (in metri). |
  ParentDeviceId | ID del dispositivo padre a cui è connesso il dispositivo. Ad esempio, se un nodo è connesso a un gateway, il nodo ha parentDeviceID come gateway. |
  Nome  | Nome per identificare la risorsa. I partner di dispositivi devono inviare un nome coerente con il nome del dispositivo sul lato partner del dispositivo. Se il nome del dispositivo è definito dall'utente sul lato partner del dispositivo, lo stesso nome definito dall'utente deve essere propagato a FarmBeats.If the device name is user-defined on the device partner side, the same user-defined name should be propagated to FarmBeats.  |
  Descrizione  | Fornire una descrizione significativa.  |
  Proprietà  |Proprietà aggiuntive del produttore.  |
  **SensorEModello** |  |
  Tipo (analogico, digitale)  |Menzione sensore analogico o digitale.|
  Produttore  | Nome del produttore. |
  Productcode  | Codice prodotto o nome o numero del modello. Ad esempio, RS-CO2-N01.  |
  Nome > SensorMeasures  | Nome della misura del sensore. È supportato solo lettere minuscole. Per le misurazioni da profondità diverse, specificare la profondità. Ad esempio, soil_moisture_15cm. Questo nome deve essere coerente con i dati di telemetria. |
  SensorMeasures > DataType  | Tipo di dati di telemetria. Attualmente è supportato double. |
  Tipo di > SensorMeasures  | Tipo di misurazione dei dati di telemetria del sensore. Di seguito sono riportati i tipi definiti dal sistema: AmbientTemperature, CO2, Profondità, Conduttività elettrica, LeafWetness, Length, LiquidLevel, Nitrato, O2, PH, Fosfato, PointInTime, Potassium, Pressione, RainGauge, RelativeUmidità, Salinity, SoilMoisture, SoilTemperature, SolarRadiation, Stato, TimeDuration, UVRadiation, UVIndex, Volume, WindDirection, WindRun, WindSpeed, Evapot. Per aggiungerne altre, fare riferimento all'API /ExtendedType.
  Unità > SensorMeasures | Unità di dati di telemetria del sensore. Di seguito sono riportate le unità definite dal sistema: NoUnit, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, Mercury, PSI, MilliMeter, CentiMeter, Meter, Inch, Feet, Mile, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, Degree, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, Percentuale, PartsPerMillion, MicroMol, MicroMolesPerter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSi eemensPerMeter, KiloPascal, VolumetricIonContent, Liter, MilliLiter, Seconds, UnixTimestamp, MicroMolPerMeterSquaredPerSecond e InchesPerHour. Per aggiungerne altre, fare riferimento all'API /ExtendedType.
  SensorMeasures > AggregationType  | Nessuno, media, massimo, minimo o Deviazionestandard.
  SensorMeasures > profondità  | Profondità del sensore in centimetri. Ad esempio, la misurazione dell'umidità 10 cm sotto terra.
  SensorMeasures > Descrizione  | Fornire una descrizione significativa della misurazione.
  Nome  | Nome per identificare la risorsa. Ad esempio, il nome del modello o del prodotto.
  Descrizione  | Fornire una descrizione significativa del modello.
  Proprietà  | Proprietà aggiuntive del produttore.
  **Sensore**  |  |
  HardwareId (ID hardware)  | ID univoco per il sensore impostato dal produttore.
  SensorModelId (IdModello)  | ID del modello di sensore associato.
  Location  | Latitudine del sensore (da -90 a 90 dollari), longitudine (-180 a 180) e elevazione (in metri).
  Nome > portuale  |Nome e tipo della porta a cui è collegato il sensore sul dispositivo. Deve essere lo stesso nome definito nel modello di dispositivo.
  deviceId  | ID del dispositivo a cui è collegato il sensore.
  Nome  | Nome per identificare la risorsa. Ad esempio, il nome del sensore o il nome del prodotto e il numero di modello o il codice prodotto.
  Descrizione  | Fornire una descrizione significativa.
  Proprietà  | Proprietà aggiuntive del produttore.

 Per informazioni su ciascuno degli oggetti e sulle relative proprietà, vedere [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

 > [!NOTE]
 > Le API restituiscono ID univoci per ogni istanza creata. Questo ID deve essere mantenuto dal traduttore per la gestione dei dispositivi e la sincronizzazione dei metadati.


**Sincronizzazione dei metadati**

Il traduttore deve inviare aggiornamenti sui metadati. Ad esempio, gli scenari di aggiornamento sono la modifica del nome del dispositivo o del sensore e la modifica della posizione del dispositivo o del sensore.

Il traduttore dovrebbe avere la possibilità di aggiungere nuovi dispositivi o sensori che sono stati installati dall'utente post link di FarmBeats. Analogamente, se un dispositivo o un sensore è stato aggiornato dall'utente, lo stesso deve essere aggiornato in FarmBeats per il dispositivo o il sensore corrispondente. Scenari tipici che richiedono l'aggiornamento di un dispositivo o di un sensore sono una modifica in una posizione del dispositivo o l'aggiunta di sensori in un nodo.


> [!NOTE]
> Delete non è supportato per i metadati del dispositivo o del sensore.
>
> Per aggiornare i metadati, è obbligatorio chiamare /Get/ , id sul dispositivo o sensore, aggiornare le proprietà modificate e quindi eseguire un /Put / , in modo che tutte le proprietà impostate dall'utente non vengono perse.

### <a name="add-new-types-and-units"></a>Aggiungere nuovi tipi e unità

FarmBeats supporta l'aggiunta di nuovi tipi di misure e unità di sensori. Per ulteriori informazioni sull'API /ExtendedType, vedere [Swagger](https://aka.ms/FarmBeatsSwagger).

## <a name="telemetry-specifications"></a>Specifiche di telemetria

I dati di telemetria vengono mappati a un messaggio canonico pubblicato negli hub eventi di Azure per l'elaborazione. Hub eventi di Azure è un servizio che consente l'inserimento di dati in tempo reale (telemetria) da applicazioni e dispositivi connessi.

## <a name="send-telemetry-data-to-farmbeats"></a>Inviare dati di telemetria a FarmBeatsSend telemetry data to FarmBeats

To send telemetry data to FarmBeats, create a client that sends messages to an event hub in FarmBeats. Per altre informazioni sui dati di telemetria, vedere [Invio di dati di telemetria a un hub eventi.](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send)

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

Il formato del messaggio canonico è il seguente:The canonical message format is as follows:

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
Tutti i nomi delle chiavi nel codice JSON di telemetria devono essere minuscoli. Esempi sono deviceid e sensordata.

Ad esempio, ecco un messaggio di telemetria:For example, here's a telemetry message:


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

> [!NOTE]
> Le sezioni seguenti sono correlate ad altre modifiche (ad es. UI, gestione degli errori, ecc.) a cui il partner del sensore può fare riferimento nello sviluppo del componente Translator.


## <a name="link-a-farmbeats-account"></a>Collegare un account FarmBeats

Dopo che i clienti hanno acquistato e distribuito dispositivi o sensori, possono accedere ai dati e ai dati di telemetria del dispositivo nel software come servizio (SaaS) dei partner dispositivi. I partner dispositivo possono consentire ai clienti di collegare il proprio account all'istanza FarmBeats in Azure fornendo un modo per immettere le credenziali seguenti:Device partners can enable customers to link their account to their FarmBeats instance on Azure by providing a way to input the following credentials:

   - Nome visualizzato (un campo facoltativo che consente agli utenti di definire un nome per questa integrazione)
   - Endpoint API
   - ID tenant
   - ID client
   - Segreto client
   - Stringa di connessione EventHub
   - Data di inizio

   > [!NOTE]
   > La data di inizio abilita il feed di dati cronologici, ovvero i dati della data specificata dall'utente.

## <a name="unlink-farmbeats"></a>Scollegare FarmBeats

I partner di dispositivi possono consentire ai clienti di scollegare un'integrazione FarmBeats esistente. Lo scollegamento di FarmBeats non deve eliminare i metadati del dispositivo o del sensore creati in FarmBeats Datahub. Lo scollegamento consente di eseguire le operazioni seguenti:

   - Interrompe il flusso di telemetria.
   - Elimina e cancella le credenziali di integrazione nel partner di dispositivo.

## <a name="edit-farmbeats-integration"></a>Modifica l'integrazione di FarmBeats

I partner dispositivo possono consentire ai clienti di modificare le impostazioni di integrazione di FarmBeats se il segreto client o la stringa di connessione cambia. In questo caso, solo i seguenti campi sono modificabili:

   - Nome visualizzato (se applicabile)
   - Segreto del client (deve essere visualizzato nel formato "2x8", ovvero "", "" o "Mostra/Nascondi" o con la funzionalità Mostra/Nascondi piuttosto che in testo non crittografato.
   - Stringa di connessione (dovrebbe essere visualizzata in formato "2x8" o in "Nascondi" anziché in testo non crittografato.

## <a name="view-the-last-telemetry-sent"></a>Visualizzare gli ultimi dati di telemetria inviati

I partner dispositivi possono consentire ai clienti di visualizzare il timestamp dell'ultimo errore di telemetria inviato, disponibile in **Accesso di telemetria.** Questo è il momento in cui i dati di telemetria più recenti sono stati inviati correttamente a FarmBeats.This is the corrente in which data was successfully sent to FarmBeats.

## <a name="troubleshooting-and-error-management"></a>Risoluzione dei problemi e gestione degli errori

**Opzione o supporto per la risoluzione dei problemi**

Se il cliente non è in grado di ricevere dati del dispositivo o dati di telemetria nell'istanza farmBeats specificata, il partner del dispositivo deve fornire supporto e un meccanismo per la risoluzione dei problemi.

**Conservazione dei dati di telemetriaTelemetry data retention**

I dati di telemetria devono inoltre essere conservati per un periodo di tempo predefinito in modo che possa essere utile per il debug o il nuovo invio dei dati di telemetria in caso di errore o perdita di dati.

**Gestione degli errori o notifica di errore**

Se un errore influisce sui metadati del dispositivo o del sensore o sull'integrazione dei dati o sul flusso di dati di telemetria nel sistema partner del dispositivo, il cliente deve ricevere una notifica. Dovrebbe inoltre essere progettato e implementato un meccanismo per risolvere eventuali errori.

**Elenco di controllo per la connessione**

I produttori o i partner di dispositivi possono utilizzare il seguente elenco di controllo per garantire che le credenziali fornite dal cliente siano accurate:

   - Verificare se viene ricevuto un token di accesso con le credenziali fornite.
   - Verificare se una chiamata API ha esito positivo con il token di accesso ricevuto.
   - Verificare se la connessione client EventHub è stata stabilita.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'API REST, vedere [API REST.](rest-api-in-azure-farmbeats.md)
