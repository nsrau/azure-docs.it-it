---
title: Integrazione di partner di sensori
description: Questo articolo descrive l’integrazione di partner di sensori.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 37a387b93f1c6b3796b66993405787cf43990bc4
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684010"
---
# <a name="sensor-partner-integration"></a>Integrazione di partner di sensori

L’articolo include informazioni sul componente FarmBeats **Translator** di Azure, che consente l'integrazione di partner di sensori.

Grazie a questo componente, i partner possono integrarsi con FarmBeats usando le API FarmBeats Datahub e inviare i dati di telemetria e i dati del dispositivo a FarmBeats Datahub. I dati disponibili in FarmBeats vengono visualizzati usando FarmBeats Accelerator e possono essere usati per la fusione dei dati e per la creazione di modelli di Machine Learning/intelligenza artificiale.

## <a name="before-you-start"></a>Prima di iniziare

Per sviluppare il componente Translator, sono necessarie le seguenti credenziali che consentono l'accesso alle API FarmBeats.

- Endpoint API
- ID tenant
- ID client
- Client Secret
- Stringa di connessione all’Hub eventi

Per ottenere le credenziali sopraindicate, vedere questa sezione: [Abilitare l’integrazione dei dispositivi](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats)

## <a name="translator-development"></a>Sviluppo di Translator

**Integrazione basata sull'API REST**

Le capacità di integrazione dei dati dei sensori di FarmBeats vengono esposte tramite l'API REST. Le capacità includono la definizione dei metadati, il provisioning e la gestione dei dispositivi e dei sensori.

**Inserimento dei dati di telemetria**

I dati di telemetria vengono mappati a un messaggio canonico che viene pubblicato in Hub eventi di Azure per l’elaborazione. Hub eventi di Azure è un servizio che consente l’inserimento di dati (di telemetria) in tempo reale da applicazioni e dispositivi connessi.

**Sviluppo di API**

Le API contengono la documentazione tecnica di Swagger. Per altre informazioni sulle API e sulle corrispondenti richieste o risposte, vedere [Swagger](https://aka.ms/FarmBeatsSwagger).

**autenticazione**

FarmBeats usa l’autenticazione di Microsoft Azure Active Directory. Il Servizio app di Azure offre supporto di autorizzazione e autenticazione integrato.

Per altre informazioni, vedere [Azure Active Directory - Documentazione](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization).

FarmBeats Datahub usa l'autenticazione con token di connessione, che richiede le credenziali seguenti:
   - ID client
   - Segreto client
   - ID tenant

Usando queste credenziali, il chiamante può richiedere un token di accesso. Il token deve essere inviato nelle richieste API successive, nella sezione dell’intestazione, come indicato di seguito:

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

Il seguente codice Python di esempio fornisce il token di accesso, che può essere usato per le successive chiamate API a FarmBeats.

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

Di seguito sono riportate le intestazioni di richiesta di uso più frequente che devono essere specificate quando si effettua una chiamata API a FarmBeats Datahub.


**Intestazione** | **Descrizione ed esempio**
--- | ---
Content-Type | Formato della richiesta (Content-Type: application/<format>). Il formato per l’API FarmBeats Datahub è JSON. Content-Type: application/json
Autorizzazione | Specifica il token di accesso necessario per effettuare una chiamata API. Authorization: Bearer <Access-Token>
Accept | Formato della risposta. Il formato per l’API FarmBeats Datahub è JSON. Accept: application/json

**Richieste API**

Per eseguire una richiesta API REST, si combina il metodo HTTP (GET, POST o PUT), l'URL del servizio API, lo Uniform Resource Identifier (URI) a una risorsa su cui eseguire una query, a cui inviare i dati, da aggiornare o eliminare e una o più intestazioni di richiesta HTTP. L'URL al servizio API è l'endpoint dell’API specificato. Ecco un esempio: https://\<nome-sitoweb-datahub->.azurewebsites.net

Facoltativamente, è possibile includere parametri di query sulle chiamate GET da filtrare, di cui limitare le dimensioni e ordinare i dati nelle risposte.

La seguente richiesta di esempio consente di ottenere l'elenco dei dispositivi.

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>"
```
La maggior parte delle chiamate GET, POST e PUT richiede un corpo della richiesta in formato JSON.

La seguente richiesta di esempio serve a creare un dispositivo. L’esempio include codice JSON di input con il corpo della richiesta.

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>Formato dati

JSON è un formato di dati comune indipendente dal linguaggio che offre una rappresentazione testuale semplice di strutture di dati arbitrarie. Per altre informazioni, vedere [json.org](http://json.org).

## <a name="metadata-specifications"></a>Specifiche di metadata

FarmBeats Datahub include le API seguenti che consentono ai partner di dispositivi di creare e gestire i metadati dei dispositivi o dei sensori.

- /**DeviceModel**: DeviceModel corrisponde ai metadati del dispositivo, ad esempio il produttore e il tipo di dispositivo, ovvero gateway o nodo.
- /**Device**: Device corrisponde a un dispositivo fisico presente nella farm.
- /**SensorModel**: SensorModel corrisponde ai metadati del sensore, ad esempio il produttore, il tipo di sensore, che può essere analogico o digitale, e la misurazione del sensore, ad esempio la temperatura e la pressione dell'ambiente.
- /**Sensor**: Sensor corrisponde a un sensore fisico che registra i valori. Un sensore è in genere connesso a un dispositivo con un ID dispositivo.

  **DeviceModel** |  |
  --- | ---
  Type (node, gateway)  | Tipo di dispositivo, ovvero nodo o gateway |
  Produttore  | Nome del produttore |
  ProductCode  | Codice prodotto del dispositivo o numero o nome del modello. Ad esempio, EnviroMonitor#6800. |
  Porte  | Nome e tipo della porta, che può essere digitale o analoga.  |
  Nome  | Nome che identifica la risorsa. Ad esempio, il nome del modello o il nome del prodotto. |
  Descrizione  | Inserire una descrizione significativa del modello. |
  Proprietà  | Proprietà aggiuntive inserite dal produttore. |
  **Dispositivo** |  |
  DeviceModelId  |ID del modello di dispositivo associato. |
  HardwareId   |ID univoco per il dispositivo, ad esempio un indirizzo MAC.  |
  ReportingInterval |L'intervallo di reporting in secondi. |
  Location    |Latitudine (tra -90 e +90), longitudine (tra -180 e 180) ed elevazione (in metri) del dispositivo. |
  ParentDeviceId | ID del dispositivo padre a cui è connesso questo dispositivo. Ad esempio, se un nodo è connesso a un gateway, il parentDeviceID del nodo è il gateway. |
  Nome  | Nome di identificazione della risorsa. I partner dei dispositivi devono inviare un nome che sia coerente con il nome del dispositivo sul lato del partner del dispositivo. Se il nome del dispositivo è definito dall'utente sul lato del partner del dispositivo, lo stesso nome definito dall'utente deve essere propagato a FarmBeats.  |
  Descrizione  | Specificare una descrizione significativa.  |
  Proprietà  |Proprietà aggiuntive inserite dal produttore.  |
  **SensorModel** |  |
  Type (analog, digital)  |Indicare se il sensore è analogico o digitale.|
  Produttore  | Nome del produttore. |
  ProductCode  | Codice prodotto o numero o nome del modello. Ad esempio, RS-CO2-N01.  |
  SensorMeasures > Name  | Nome della misurazione del sensore. Sono supportati solo caratteri minuscoli. Per le misurazioni di profondità diverse, specificare la profondità. Ad esempio, soil_moisture_15cm. Questo nome deve essere coerente con i dati di telemetria. |
  SensorMeasures > DataType  | Tipo di dati di telemetria. Attualmente è supportato il tipo di dati double. |
  SensorMeasures > Type  | Tipo di misurazione dei dati di telemetria del sensore. Di seguito sono riportati i tipi definiti dal sistema: AmbientTemperature, CO2, Depth, ElectricalConductivity, LeafWetness, Length, LiquidLevel, Nitrate, O2, PH, Phosphate, PointInTime, Potassium, Pressure, RainGauge, RelativeHumidity, Salinity, SoilMoisture, SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, UVIndex, Volume, WindDirection, WindRun, WindSpeed, Evapotranspiration, PAR. Per aggiungere altre informazioni, vedere l'API/ExtendedType.
  SensorMeasures > Unit | Unità di dati di telemetria del sensore. Di seguito sono riportate le unità definite dal sistema: NoUnit, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, Mercury, PSI, MilliMeter, CentiMeter, Meter, Inch, Feet, Mile, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, Degree, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, Percentage, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, Liter, MilliLiter, Seconds, UnixTimestamp, MicroMolPerMeterSquaredPerSecond, and InchesPerHour. Per aggiungere altre informazioni, vedere l'API/ExtendedType.
  SensorMeasures > AggregationType  | Scegliere tra none, average, maximum, minimum o StandardDeviation.
  SensorMeasures > Depth  | Profondità del sensore espressa in centimetri. Ad esempio, la misurazione dell'umidità a 10 cm sotto la superficie.
  SensorMeasures > Description  | Inserire una descrizione significativa della misurazione.
  Nome  | Nome che identifica la risorsa. Ad esempio, il nome del modello o il nome del prodotto.
  Descrizione  | Inserire una descrizione significativa del modello.
  Proprietà  | Proprietà aggiuntive inserite dal produttore.
  **Sensor**  |  |
  HardwareId  | ID univoco del sensore impostato dal produttore.
  SensorModelId  | ID del modello di sensore associato.
  Location  | Latitudine (tra -90 e +90), longitudine (tra -180 e 180) ed elevazione (in metri) del sensore.
  Port > Name  |Nome e tipo della porta tramite cui il sensore è connesso al dispositivo. Deve corrispondere al nome definito nel modello di dispositivo.
  deviceId  | ID del dispositivo a cui è connesso il sensore.
  Nome  | Nome di identificazione della risorsa. Ad esempio, il nome del sensore o il nome del prodotto e il numero di modello o il codice prodotto del sensore.
  Descrizione  | Specificare una descrizione significativa.
  Proprietà  | Proprietà aggiuntive inserite dal produttore.

 Per informazioni su ciascun oggetto e sulle relative proprietà, vedere [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

 > [!NOTE]
 > Le API restituiscono ID univoci per ogni istanza creata. Questi ID devono essere conservati da Translator per la gestione dei dispositivi e la sincronizzazione dei metadati.


**Sincronizzazione dei metadata**

Il servizio Translator deve inviare gli aggiornamenti sui metadati. Gli aggiornamenti possono riguardare, ad esempio, una modifica al nome del dispositivo o del sensore e una modifica alla posizione del dispositivo o del sensore.

Il servizio Translator deve essere in grado di aggiungere nuovi dispositivi o sensori installati dall'utente dopo il collegamento di FarmBeats. Analogamente, se un dispositivo o un sensore è stato aggiornato dall'utente, lo stesso aggiornamento deve essere eseguito in FarmBeats per il dispositivo o il sensore corrispondente. Le situazioni più comuni che richiedono l'aggiornamento di un dispositivo o di un sensore sono una modifica al percorso del dispositivo o l'aggiunta di sensori in un nodo.


> [!NOTE]
> L'eliminazione non è supportata per i metadati del dispositivo o del sensore.
>
> Per aggiornare i metadati, è obbligatorio chiamare /Get/{id} sul dispositivo o sul sensore, aggiornare le proprietà modificate e quindi eseguire un’operazione /Put/{id} in modo che le proprietà impostate dall’utente non vadano perse.

### <a name="add-new-types-and-units"></a>Aggiungere nuovi tipi e unità

FarmBeats supporta l'aggiunta di nuovi tipi e unità di misura del sensore. Per altre informazioni sull’API /ExtendedType, vedere [Swagger](https://aka.ms/FarmBeatsSwagger).

## <a name="telemetry-specifications"></a>Specifiche dei dati di telemetria

I dati di telemetria vengono mappati a un messaggio canonico che viene pubblicato in Hub eventi di Azure per l’elaborazione. Hub eventi di Azure è un servizio che consente l’inserimento di dati (di telemetria) in tempo reale da applicazioni e dispositivi connessi.

## <a name="send-telemetry-data-to-farmbeats"></a>Inviare i dati di telemetria a FarmBeats

Per inviare i dati di telemetria a FarmBeats, creare un client che invii messaggi a un hub eventi in FarmBeats. Per altre informazioni sui dati di telemetria, vedere [Invio di dati di telemetria a un hub eventi](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send).

Ecco un frammento di codice Python di esempio che invia dati di telemetria come client a un hub eventi specificato.

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
Tutti i nomi chiave nel codice JSON dei dati di telemetria devono essere in minuscolo, ad esempio deviceid e sensordata.

Ecco ad esempio un messaggio di dati di telemetria:


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
> Le sezioni seguenti sono correlate ad altre modifiche (ad esempio, Interfaccia utente, gestione degli errori e così via) a cui il partner di sensori può fare riferimento nello sviluppo del componente di conversione.


## <a name="link-a-farmbeats-account"></a>Collegare un account FarmBeats

Dopo che i clienti hanno acquistato e installato i dispositivi o i sensori, possono accedere ai dati del dispositivo e ai dati di telemetria nel portale SaaS (Software as a Service) dei partner dei dispositivi. I partner dei dispositivi possono consentire ai clienti di collegare il proprio account alla loro istanza di FarmBeats in Azure indicando un modo per inserire le credenziali seguenti:

   - Nome visualizzato (campo facoltativo che consente agli utenti di definire un nome per l'integrazione)
   - Endpoint API
   - ID tenant
   - ID client
   - Segreto client
   - Stringa di connessione all’Hub eventi
   - Data di inizio

   > [!NOTE]
   > La data di inizio abilita il feed di dati cronologici, ovvero i dati dalla data specificata dall'utente.

## <a name="unlink-farmbeats"></a>Scollegare FarmBeats

I partner dei dispositivi possono consentire ai clienti di scollegare un'integrazione di FarmBeats esistente. Questa operazione non deve eliminare i metadati del dispositivo o del sensore creati in FarmBeats Datahub. Lo scollegamento comporta quando segue:

   - Arresto del flusso di dati di telemetria.
   - Eliminazione e cancellazione delle credenziali di integrazione nel partner del dispositivo.

## <a name="edit-farmbeats-integration"></a>Modifica dell’integrazione di FarmBeats

I partner dei dispositivi possono consentire ai clienti di modificare le impostazioni di integrazione di FarmBeats in caso di modifica della stringa di connessione o del segreto client. In questo caso, sono modificabili solo i campi seguenti:

   - Nome visualizzato (se applicabile)
   - Segreto client (deve essere visualizzato nel formato "2x8 * * * * * * * * * * *" o con la funzionalità Mostra/Nascondi anziché in testo non crittografato)
   - Stringa di connessione (deve essere visualizzata nel formato "2x8 * * * * * * * * * * *" o con la funzionalità Mostra/Nascondi anziché in testo non crittografato)

## <a name="view-the-last-telemetry-sent"></a>Visualizzare gli ultimi dati di telemetria inviati

I partner dei dispositivi possono consentire ai clienti di visualizzare il timestamp degli ultimi dati di telemetria inviati, disponibile in **Telemetry Sent** (Dati di telemetria inviati). Si tratta della data e ora in cui i dati di telemetria più recenti sono stati inviati correttamente a FarmBeats.

## <a name="troubleshooting-and-error-management"></a>Risoluzione dei problemi e gestione degli errori

**Risoluzione dei problemi o supporto**

Se il cliente non riesce a ricevere i dati del dispositivo o i dati di telemetria nell'istanza di FarmBeats specificata, il partner del dispositivo deve fornire il supporto e una procedura di risoluzione dei problemi.

**Conservazione dei dati di telemetria**

I dati di telemetria devono anche essere conservati per un periodo di tempo predefinito, in modo che possano essere utili nell’esecuzione del debug o possano essere nuovamente inviati in caso di errore o perdita di dati.

**Gestione degli errori o notifica degli errori**

Se un errore influisce sui metadati del dispositivo o del sensore o sul flusso dei dati di telemetria o di integrazione dei dati nel sistema di partner dei dispositivi, il cliente deve ricevere una notifica. È necessario progettare e implementare una procedura di risoluzione degli errori.

**Elenco di controllo per la connessione**

I produttori o i partner di dispositivi possono utilizzare l'elenco di controllo seguente per assicurarsi che le credenziali fornite dal cliente siano precise:

   - Verificare se è stato ricevuto un token di accesso con le credenziali specificate.
   - Verificare se una chiamata API ha esito positivo con il token di accesso ricevuto.
   - Verificare se è stata stabilita la connessione del client EventHub.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sull'API REST, vedere [API REST](rest-api-in-azure-farmbeats.md).
