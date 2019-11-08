---
title: Integrazione del partner sensore
description: Descrive l'integrazione del partner sensori
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: e7de815b7254fb071b3094f9ae636b712b38684b
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73797686"
---
# <a name="sensor-partner-integration"></a>Integrazione del partner sensore
Questo articolo fornisce informazioni sul componente Azure FarmBeats **Translator** .

Grazie a questo componente, i partner possono sviluppare sensori che si integrano con FarmBeats, sfruttando l'API e inviando dati di telemetria e dati del dispositivo all'hub dati di FarmBeats. I dati vengono visualizzati usando l'acceleratore FarmBeats. I dati possono essere usati per la fusione dei dati e per la creazione di modelli di linguaggio del computer/intelligenza artificiale.

## <a name="link-farmbeats-account"></a>Collega account FarmBeats

Una volta che i clienti hanno acquistato e distribuito dispositivi/sensori, possono accedere ai dati del dispositivo e ai dati di telemetria nel portale SaaS del partner dispositivi (software come servizio). I partner dispositivo devono consentire ai clienti di collegare il proprio account all'istanza di FarmBeats in Azure. Le credenziali seguenti sono necessarie per compilare da Customer/SI:

   - Nome visualizzato (campo facoltativo per l'utente che definisce un nome per l'integrazione)
   - Endpoint API
   - ID tenant
   - ID client
   - Segreto client
   - Stringa di connessione EventHub
   - Data di inizio

   > [!NOTE]
   > Data di inizio Abilita il feed di dati cronologici, ad esempio i dati della data specificata dall'utente.

## <a name="unlink-farmbeats"></a>Scollega FarmBeats

I clienti hanno la possibilità di scollegare un'integrazione FarmBeats esistente. Lo scollegamento di FarmBeats non deve eliminare i metadati del dispositivo/sensore creati nell'hub dati del cliente. Lo scollegamento esegue le operazioni seguenti:

   - Arresta il flusso di telemetria.
   - Elimina e cancella le credenziali di integrazione nel partner dispositivo.

## <a name="edit-farmbeats-integration"></a>Modifica integrazione FarmBeats

Il cliente può modificare l'integrazione FarmBeats. Lo scenario principale per la modifica è quando il segreto client o la stringa di connessione viene modificata a causa della scadenza, in questo caso il cliente può solo modificare i campi seguenti.

   - Nome visualizzato (se applicabile)
   - Segreto client (dovrebbe essere visualizzato in "2x8 * * * * * * * * * * *" formato o Mostra/Nascondi funzionalità anziché testo non crittografato)
   - Stringa di connessione (dovrebbe essere visualizzata in "2x8 * * * * * * * * * * *" formato o Mostra/Nascondi funzionalità anziché testo non crittografato)

   > [!NOTE]
   > La modifica non deve interrompere la creazione di oggetti di metadati.

## <a name="view-last-telemetry-sent"></a>Visualizza l'ultimo telemetria inviato

È possibile visualizzare il timestamp degli ultimi **dati di telemetria inviati**. Si tratta dell'ora in cui la telemetria più recente è stata inviata correttamente a FarmBeats.

## <a name="translator-development"></a>Sviluppo di convertitori

**Integrazione basata sull'API REST**

Le funzionalità di integrazione dei dati dei sensori di FarmBeats vengono esposte tramite l'API REST. Le funzionalità includono la definizione dei metadati, il provisioning del dispositivo/sensore, il dispositivo e la gestione dei sensori.

**Inserimento di dati di telemetria**

Viene eseguito il mapping dei dati di telemetria a un messaggio canonico pubblicato nell'hub eventi di Azure per l'elaborazione. Azure EventHub è un servizio che consente l'inserimento di dati in tempo reale (telemetria) da applicazioni e dispositivi connessi.

**Sviluppo di API**

Le API contengono la documentazione tecnica di spavalderia. Per altre informazioni sulle API e sulle richieste/risposte corrispondenti, vedere [spavalderia](https://aka.ms/FarmBeatsDatahubSwagger)

**Autenticazione**

FarmBeats utilizza l'autenticazione Active Directory di Microsoft Azure. App Azure servizio fornisce supporto predefinito per l'autenticazione e l'autorizzazione.

Per altre informazioni, vedere [Azure Active Directory - Documentazione](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization).

FarmBeats Data Hub USA l'autenticazione della porta, che richiede le credenziali seguenti:
   - ID client
   - Client Secret
   - ID tenant

Usando le credenziali sopra riportate, il chiamante può richiedere un token di accesso, che deve essere inviato nelle richieste API successive nella sezione dell'intestazione, come indicato di seguito:

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

Di seguito è riportato un codice Python di esempio che fornisce il token di accesso, che può essere usato per le chiamate API successive a FarmBeats: 

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

Di seguito sono riportate le intestazioni di richiesta più comuni che è necessario specificare quando si effettua una chiamata API a FarmBeats Data Hub:


**Intestazione** | **Descrizione ed esempio**
--- | ---
Content-Type | Il formato della richiesta (Content-Type: Application/<format>) per il formato API dell'hub dati FarmBeats è JSON. Content-Type: Application/JSON
Autorizzazione | Specifica il token di accesso necessario per eseguire un'autorizzazione chiamata API: Bearer < Access-token >
Accept | Formato della risposta. Per le API di FarmBeats Data Hub, il formato è JSON Accept: Application/JSON

**Richieste API**

Per eseguire una richiesta API REST (Representational State Transfer), combinare il metodo HTTP (GET, POST o PUT), l'URL del servizio API, l'URI (Uniform Resource Identifier) a una risorsa per eseguire query, inviare dati a, aggiornare o eliminare e una o più richieste HTTP intestazioni. L'URL del servizio API è l'endpoint API fornito dal cliente. Ecco un esempio: https://\<yourdatahub-website-name >. azurewebsites. NET

Facoltativamente, è possibile includere i parametri di query sulle chiamate GET da filtrare, limitare le dimensioni e ordinare i dati nelle risposte.

La richiesta di esempio seguente consente di ottenere l'elenco dei dispositivi:

```
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>”
```
Per la maggior parte delle chiamate GET, POST e PUT è necessario un corpo della richiesta JSON.

La richiesta di esempio seguente consiste nel creare un dispositivo (questo esempio ha un JSON di input con il corpo della richiesta).

```
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>Formato dati

JSON (JavaScript Object Notation) è un formato di dati comune indipendente dal linguaggio che fornisce una semplice rappresentazione testuale di strutture di dati arbitrarie. Per ulteriori informazioni, vedere [JSON.org](http://json.org).

## <a name="metadata-specifications"></a>Specifiche dei metadati

L'hub dati FarmBeats include le API seguenti che consentono ai partner di dispositivi di creare e gestire i metadati del dispositivo/sensore.  

- /modello **DeviceModel** -device corrisponde ai metadati del dispositivo, ad esempio il produttore, il tipo del dispositivo gateway o node.  
- /**dispositivo-dispositivo corrisponde** a un dispositivo fisico presente nella farm.
- /modello **SensorModel** -Sensor corrisponde ai metadati del sensore, ad esempio il produttore, il tipo di sensore analogico o digitale, misura sensore come temperatura ambiente, pressione e così via.
- /**sensore-sensore corrisponde** a un sensore fisico che registra i valori. Un sensore è in genere connesso a un dispositivo con un ID dispositivo.

  Modello di dispositivo| DeviceModel corrisponde ai metadati del dispositivo, ad esempio il produttore, il tipo del dispositivo gateway o node.
  --- | ---
  Tipo (nodo, gateway)  | 1 stella |
  Produttore  | 2 stelle |
  ProductCode  | Codice prodotto del dispositivo o nome/numero del modello. Ad esempio, EnviroMonitor # 6800 |
  Porte  | Nome e tipo di porta (digitale/analogico)  |
  Name  | Nome per identificare la risorsa. Ad esempio nome modello/nome prodotto |
  Descrizione  | Fornire una descrizione significativa del modello |
  Proprietà  | Proprietà aggiuntive del produttore |
  **Dispositivo** | **Il dispositivo corrisponde a un dispositivo fisico presente nella farm. Ogni dispositivo ha un ID dispositivo univoco** |
DeviceModelId  |ID del modello di dispositivo associato. |
hardwareId   |ID univoco per il dispositivo, ad esempio l'indirizzo MAC e così via.  |
reportingInterval |Intervallo di Reporting in secondi |
Percorso    |Latitudine del dispositivo (da-90 a + 90)/Longitude (-180 a 180)/Elevation (in metri) |
ParentDeviceId | ID del dispositivo padre a cui è connesso il dispositivo. Ad esempio: Un nodo connesso a un gateway; il nodo avrà parentDeviceID come gateway |
  Name  | Nome per identificare la risorsa.  I partner del dispositivo dovranno inviare un nome coerente con il nome del dispositivo sul lato del partner del dispositivo. Se il nome del dispositivo è definito dall'utente sul lato Device partner, lo stesso nome definito dall'utente deve essere propagato a FarmBeats  |
  Descrizione  | Fornire una descrizione significativa  |
  Proprietà  |Proprietà aggiuntive del produttore  |
  **Modello di sensore** | SensorModel corrisponde ai metadati del sensore, ad esempio il produttore, il tipo di sensore analogico o digitale, misura sensore come temperatura ambiente, pressione e così via. |
  Tipo (analogico, digitale)  |Menzione sensore analogico o digitale|
  manufacturer  | nome del produttore |
  ProductCode  | Codice prodotto o nome modello/numero. Ad esempio, RS-CO2-N01  |
  Nome > SensorMeasures  | Nome della misura del sensore. Sono supportate solo lettere minuscole. Per la misura da profondità diverse, specificare la profondità. Ad esempio, soil_moisture_15cm questo nome deve essere coerente con i dati di telemetria. |
  Tipo di dati > SensorMeasures  | Tipo di dati di telemetria. Attualmente il doppio è supportato  |
  Tipo > sensorMeasures  | Tipo di misura dei dati di telemetria del sensore. Di seguito sono riportati i tipi definiti dal sistema: Temperaturaambiente, CO2, depth, ElectricalConductivity, LeafWetness, length, LiquidLevel, nitrate, O2, PH, fosfato, PointInTime, potassio, Pressure, RainGauge, RelativeHumidity, salinità, SoilMoisture, SoilTemperature, SolarRadiation, state, TimeDuration, UVRadiation, UVIndex, volume, WindDirection, WindRun, WindSpeed, evapotraspirazione, PAR. Per aggiungere altre informazioni, vedere l'API/ExtendedType
  Unità > SensorMeasures | Unità di dati di telemetria del sensore. Di seguito sono riportate le unità definite dal sistema: nounit, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, Mercury, PSI, millimetro, centimetro, Meter, inch, feet, Mile, kilometr, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, Degree, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, percentual, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, Liter, Millilitr, seconds, UnixTimestamp, MicroMolPerMeterSquaredPerSecond, InchesPerHour per aggiungere altro, vedere/ API ExtendedType
  SensorMeasures > aggregationType  | None, Average, Maximum, Minimum, StandardDeviation
  Profondità > SensorMeasures  | Profondità del sensore in centimetri, ad esempio misura dell'umidità di 10 cm sotto la superficie.
  Descrizione > sensorMeasures  | Fornire una descrizione significativa della misura
  name  | Nome per identificare la risorsa. Ad esempio nome modello/nome prodotto
  description  | Fornire una descrizione significativa del modello
  properties  | Proprietà aggiuntive del produttore
  **Sensore**  |
  hardwareId  | ID univoco per il sensore impostato dal produttore
  SensorModelId  | ID del modello di sensore associato.
  location  | Latitudine del sensore (da-90 a + 90)/Longitude (-180 a 180)/Elevation (in metri)
  nome > porta  |Nome e tipo della porta a cui il sensore è connesso nel dispositivo. Il nome deve corrispondere a quello definito nel modello di dispositivo
  deviceId  | ID del dispositivo a cui è connesso il sensore
  name  | Nome per identificare la risorsa. Ad esempio il nome del sensore, il nome del prodotto e il numero di modello/codice del prodotto.
  description  | Fornire una descrizione significativa
  properties  | Proprietà aggiuntive del produttore

 Per informazioni su ogni oggetto e sulle relative proprietà, vedere [spavalderia](httpa://aka.ms/FarmBeatsDatahubSwagger).

 > [!NOTE]
 > Le API restituiscono ID univoci per ogni istanza creata. Questo ID deve essere mantenuto dal convertitore per la gestione dei dispositivi e la sincronizzazione dei metadati.


**Sincronizzazione dei metadati**

Il convertitore deve inviare aggiornamenti sui metadati. Ad esempio, gli scenari di aggiornamento sono: modifica del nome del dispositivo/sensore, modifica della posizione del dispositivo/sensore.

Il convertitore deve avere la possibilità di aggiungere nuovi dispositivi e/o sensori installati dal collegamento post-utente di FarmBeats. Analogamente, se un dispositivo/sensore è stato aggiornato dall'utente, lo stesso dovrebbe essere aggiornato in FarmBeats per il dispositivo/sensore corrispondente. Gli scenari tipici per l'aggiornamento del dispositivo/sensore potrebbero essere: modificare la posizione del dispositivo, aggiungere i sensori in un nodo e così via.


> [!NOTE]
> L'eliminazione non è supportata per i metadati del dispositivo/sensore.
>
> Per aggiornare i metadati, è obbligatorio chiamare/Get/{ID} sul dispositivo/sensore, aggiornare le proprietà modificate e quindi eseguire un/put/{ID} in modo che le proprietà impostate dall'utente non vadano perse.

### <a name="adding-new-typesunit"></a>Aggiunta di nuovi tipi/unità

FarmBeats supporta l'aggiunta di nuovi tipi e unità di misura del sensore. Per altre informazioni sull'API/ExtendedType, [spavalderia](https://aka.ms/FarmBeatsDatahubSwagger).

## <a name="telemetry-specifications"></a>Specifiche di telemetria

Viene eseguito il mapping dei dati di telemetria a un messaggio canonico pubblicato nell'hub eventi di Azure per l'elaborazione. Azure EventHub è un servizio che consente l'inserimento di dati in tempo reale (telemetria) da applicazioni e dispositivi connessi.

## <a name="send-telemetry-data-to-farmbeats"></a>Inviare dati di telemetria a FarmBeats

Per inviare i dati di telemetria a FarmBeats, è necessario creare un client che invii messaggi a un hub eventi in FarmBeats. Per altre informazioni sui dati di telemetria, vedere [invio di dati di telemetria a hub eventi](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send).

Di seguito è riportato un codice Python di esempio che invia dati di telemetria come client a un hub eventi specificato:

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

```
{
“deviceid”: “<id of the Device created>”,
 "timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>”
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>": value
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": value
        }
      ]
    }
}

```

Tutti i nomi di chiave nel codice JSON di telemetria devono essere in lettere minuscole, ad esempio DeviceID, SensorData e così via.

Ad esempio, messaggio di telemetria:


```
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

## <a name="troubleshooterror-management"></a>Risoluzione dei problemi/gestione degli errori

**Risoluzione dei problemi opzione/supporto**

Nel caso in cui il cliente non sia in grado di ricevere dati del dispositivo e/o dati di telemetria nell'istanza di FarmBeats specificata, il partner del dispositivo deve fornire supporto e un meccanismo per la risoluzione dei problemi.

**Conservazione dei dati di telemetria**

I dati di telemetria devono essere conservati anche per un periodo di tempo predefinito in modo che lo stesso possa essere utile per eseguire il debug o inviare nuovamente i dati di telemetria in caso di errore o perdita di dati.

**Gestione errori/notifica di errore**

In caso di errore che influisce sui metadati del dispositivo/sensore/sull'integrazione dei dati o sul flusso di dati di telemetria nel sistema del partner del dispositivo, lo stesso deve essere notificato al cliente. È anche possibile progettare e implementare un meccanismo per la risoluzione degli errori.

**Elenco di controllo per la connessione**

Per assicurarsi che le credenziali fornite dal cliente siano accurate, i produttori e i partner del dispositivo possono avere il test e l'elenco di controllo di integrità seguenti.

   - Controllare se è stato ricevuto un token di accesso con le credenziali fornite
   - Controllare se una chiamata API ha esito positivo con il token di accesso ricevuto
   - Controllare se è stata stabilita la connessione client EventHub

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'API REST, vedere [API REST](references-for-farmbeats.md#rest-api).
