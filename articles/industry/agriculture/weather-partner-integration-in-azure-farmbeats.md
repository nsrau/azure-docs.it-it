---
title: Integrazione di partner per il meteo
description: Questo articolo descrive come un provider di dati meteorologici può integrarsi con FarmBeats
author: sunasing
ms.topic: article
ms.date: 07/09/2020
ms.author: sunasing
ms.openlocfilehash: a2677b5343b2d65a39e7c9f6d5006db599c1ac73
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86496996"
---
# <a name="weather-partner-integration"></a>Integrazione di partner per il meteo

Questo articolo fornisce informazioni sul componente Docker di Azure FarmBeats **Connector** che i provider di dati meteorologici possono sviluppare per l'integrazione con FarmBeats sfruttando le API e inviano i dati meteo a FarmBeats. Quando i dati sono disponibili in FarmBeats, è possibile usarli per la fusione dei dati e per la creazione di modelli di Machine Learning/intelligenza artificiale.

 > [!NOTE]
 > Ai fini di questa documentazione, si userà un'implementazione di riferimento compilata usando NOAA da Azure Open DataSets ed è disponibile all'indirizzo [https://github.com/azurefarmbeats/noaa_docker](https://github.com/azurefarmbeats/noaa_docker) .
 > L'immagine Docker corrispondente è disponibile all'indirizzo[https://hub.docker.com/r/azurefarmbeats/farmbeats-noaa](https://hub.docker.com/r/azurefarmbeats/farmbeats-noaa)

Un partner Meteo dovrà fornire un'immagine o un programma Docker (con le specifiche indicate di seguito) e ospitare l'immagine Docker in un registro contenitori accessibile ai clienti. Il partner meteorologico dovrà fornire le informazioni seguenti ai clienti:

- URL dell'immagine Docker
- Tag immagine Docker
- Chiavi/credenziali per accedere all'immagine Docker
- Chiavi/credenziali API specifiche del cliente per accedere ai dati dal sistema del partner Meteo
- Dettagli dello SKU della VM (i partner possono fornire questo problema nel caso in cui Docker disponga di requisiti specifici per le macchine virtuali, altrimenti i clienti possono scegliere tra SKU di VM supportate in Azure)

Usando le informazioni di Docker sopra indicate, il cliente registrerà un partner meteorologico nell'istanza di FarmBeats. Per altre informazioni su come i clienti possono usare Docker per inserire i dati meteorologici in FarmBeats, vedere la guida per [ottenere i dati meteo](https://docs.microsoft.com/azure/industry/agriculture/get-weather-data-from-weather-partner)

## <a name="connector-docker-development"></a>Sviluppo del connettore Docker

**Integrazione basata sull'API REST**

Le API FarmBeats contengono la documentazione tecnica di spavalderia. Per informazioni su tutte le API e le relative richieste o risposte corrispondenti, vedere [FarmBeats spavalderia](https://aka.ms/farmbeatsswagger). 

Se è stato installato FarmBeats, è possibile accedere al FarmBeats spavalderia`https://yourfarmbeatswebsitename-api.azurewebsites.net/swagger`

Si noti che "-API" viene aggiunto al nome del sito Web FarmBeats.
L'endpoint API sarà:`https://yourfarmbeatswebsitename-api.azurewebsites.net`

### <a name="datahub-lib"></a>Lib datahub

FarmBeats fornirà un lib che può essere usato da Weather partner. Lib è attualmente disponibile come parte [dell'implementazione di](https://github.com/azurefarmbeats/noaa_docker/tree/master/datahub_lib)riferimento. In futuro, lo stesso sarà disponibile come SDK per più lingue.

### <a name="authentication"></a>Authentication

**Autenticazione con API FarmBeats**

FarmBeats usa l'autenticazione di connessione e le API sono accessibili fornendo un token di accesso nella sezione di intestazione della richiesta come riportato di seguito:

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

Il token di accesso può essere richiesto da una funzione di Azure in esecuzione nell'istanza di FarmBeats del cliente. L'URL della funzione di Azure verrà fornito al programma Docker come argomento e il token di accesso può essere ottenuto effettuando una richiesta GET sull'URL. La risposta dall'URL conterrà il token di accesso. Datahub lib fornisce funzioni helper per consentire ai partner di ottenere il token di accesso. Ulteriori dettagli [qui](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_auth_helper.py).

Il token di accesso è valido solo per alcune ore e deve essere richiesto di nuovo al termine della scadenza.

**Autenticazione con API lato partner**

Per consentire ai clienti di eseguire l'autenticazione con le API lato partner durante l'esecuzione di Docker, i clienti devono fornire le credenziali durante la registrazione del partner, come indicato di seguito:

```json
{
 "partnerCredentials": {
   "key1": "value1",
   "key2": "value2"
   }
}
```
Il servizio API Serializza questo dict e lo archivia [in un insieme di credenziali](https://docs.microsoft.com/azure/key-vault/basic-concepts)delle credenziali.

[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) viene usato per orchestrare i processi meteorologici e per avviare le risorse per l'esecuzione del codice docker. Fornisce anche un meccanismo per eseguire il push dei dati in modo sicuro alla macchina virtuale in cui viene eseguito il processo docker. Le credenziali dell'API, ora archiviate in modo protetto nell'insieme di credenziali delle credenziali, vengono lette come stringhe protette dall'insieme di credenziali delle credenziali e rese disponibili come proprietà estese nella directory di lavoro del contenitore Docker come activity.json (il percorso del file è "nella working_dir/activity.json") il codice Docker può leggere le credenziali da questo file durante la fase di esecuzione per accedere alle API lato partner per conto del cliente. Le credenziali saranno disponibili nel file come indicato di seguito:

```json
{ 
   "typeProperties" : {
      "extendedProperties" : { 
         "partnerCredentials": "" } 
   } 
}
```
Si noti che il "partnerCredentials" sarà disponibile esattamente come fornito dal cliente durante la registrazione del partner

FarmBeats lib fornisce funzioni helper per consentire ai partner di leggere le credenziali dalle proprietà dell'attività. Ulteriori dettagli [qui](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_adf_helper.py).

La durata del file è solo durante l'esecuzione del codice Docker e verrà eliminata al termine dell'esecuzione di Docker.

Per ulteriori informazioni sul funzionamento delle pipeline e delle attività di ADF, vedere [https://docs.microsoft.com/azure/data-factory/copy-activity-schema-and-type-mapping](https://docs.microsoft.com/azure/data-factory/copy-activity-schema-and-type-mapping) .

**Intestazioni di richiesta HTTP**

Di seguito sono riportate le intestazioni di richiesta più comuni che è necessario specificare quando si effettua una chiamata API a FarmBeats.

**Intestazione** | **Descrizione ed esempio**
--- | ---
Content-Type | Formato della richiesta (Content-Type: application/<format>). Il formato per l’API FarmBeats Datahub è JSON. Content-Type: application/json
Autorizzazione | Specifica il token di accesso necessario per effettuare una chiamata API. Authorization: Bearer <Access-Token>
Accept | Formato della risposta. Il formato per l’API FarmBeats Datahub è JSON. Accept: application/json

## <a name="data-format"></a>Formato dati

JSON è un formato di dati comune indipendente dal linguaggio che offre una rappresentazione testuale semplice di strutture di dati arbitrarie. Per altre informazioni, vedere [json.org](http://json.org).

## <a name="docker-specifications"></a>Specifiche di Docker

Il programma Docker deve avere due componenti: **bootstrap** e **Jobs**. Può essere presente più di un processo.

### <a name="bootstrap"></a>Bootstrap

Questo componente deve essere eseguito quando il cliente avvia la registrazione di Docker in FarmBeats. Gli argomenti (arg1, arg2) che verranno passati al programma sono:

- Endpoint API FarmBeats: endpoint API FarmBeats per richieste API: questo è l'endpoint per effettuare chiamate API alla distribuzione FarmBeats.
- URL della funzione di Azure: si tratta dell'endpoint personale che fornirà il token di accesso per le API FarmBeats. Semplicemente chiamando un'operazione GET su questo URL, il token di accesso verrà recuperato nella risposta.

La responsabilità del bootstrap consiste nel creare i metadati necessari in modo che gli utenti possano eseguire i processi per ottenere i dati meteorologici. Per informazioni sull'implementazione di riferimento, vedere [qui](https://github.com/azurefarmbeats/noaa_docker). È possibile aggiornare il bootstrap_manifest.jssu file in base alle proprie esigenze e il programma bootstrap di riferimento creerà automaticamente i metadati necessari.

I metadati seguenti vengono creati come parte di questo processo. 

 > [!NOTE]
 > Si **noti** che se si aggiorna il bootstrap_manifest.jsnel file come indicato nell' [implementazione di riferimento](https://github.com/azurefarmbeats/noaa_docker), non è necessario creare i metadati seguenti perché il bootstrap creerà lo stesso in base al file manifesto.

- /**WeatherDataModel**: un WeatherDataModel è un modello per la rappresentazione dei dati meteorologici e corrisponde a set di dati diversi forniti dall'origine. Un DailyForecastSimpleModel, ad esempio, può fornire le informazioni di temperatura, umidità e precipitazione medie una volta al giorno, mentre un DailyForecastAdvancedModel può fornire molte più informazioni a livello di granularità oraria. È possibile creare un numero qualsiasi di WeatherDataModels.
- /**Tipoprocesso**: FarmBeats dispone di un sistema di gestione dei processi estensibile. Come provider di dati meteorologici, sono disponibili diversi set di dati/API, ad esempio GetDailyForecasts, che possono essere abilitati in FarmBeats come Tipoprocesso. Una volta creato un Tipoprocesso, un cliente può attivare processi di quel tipo per ottenere i dati meteorologici per la loro posizione/farm di interesse (vedere Tipoprocesso and Job API in [FarmBeats spavalderia](https://aka.ms/farmbeatsswagger)).

### <a name="jobs"></a>Processi

Questo componente verrà richiamato ogni volta che un utente FarmBeats esegue un processo del/JobType creato nell'ambito del processo di bootstrap. Il comando Docker Run per il processo è definito come parte del **/JobType** creato.
- La responsabilità del processo sarà recuperare i dati dall'origine e inserirli in FarmBeats. I parametri necessari per ottenere i dati devono essere definiti come parte di/JobType nel processo bootstrap.
- Come parte del processo, il programma dovrà creare una **/WeatherDataLocation** in base al/WeatherDataModel creato nell'ambito del processo di bootstrap. Il valore di **/WeatherDataLocation** corrisponde a un percorso (Lat/Long) fornito dall'utente come parametro al processo.

### <a name="details-of-the-objects"></a>Dettagli degli oggetti

  WeatherDataModel | Description |
  --- | ---
  Nome  | Nome del modello di dati meteorologici |
  Descrizione  | Inserire una descrizione significativa del modello. |
  Proprietà  | Proprietà aggiuntive definite dal provider di dati. |
  Nome > weatherMeasures  | Nome della misura Meteo. Ad esempio humidity_max |
  Tipo di dati > weatherMeasures  | Double o enum. Se enum, measureEnumDefinition è obbligatorio |
  weatherMeasures > measureEnumDefinition  | Obbligatorio solo se DataType è enum. Ad esempio {"NoRain": 0, "snow": 1, "pioggerelle": 2, "pioggia": 3} |
  Tipo > weatherMeasures  | tipo di dati di telemetria Meteo. Ad esempio "RelativeHumidity". Di seguito sono riportati i tipi definiti dal sistema: Temperaturaambiente, nounit, CO2, depth, ElectricalConductivity, LeafWetness, length, LiquidLevel, nitrate, O2, PH, fosfato, PointInTime, potassio, Pressure, RainGauge, RelativeHumidity, salinità, SoilMoisture, SoilTemperature, SolarRadiation, state, TimeDuration, UVRadiation, UVIndex, volume, WindDirection, WindRun, WindSpeed, evapotraspirazione, PAR. Per aggiungere altre informazioni, vedere l'API/ExtendedType o nella [sezione aggiungere tipi e unità](weather-partner-integration-in-azure-farmbeats.md#add-extendedtype) di seguito
  Unità > weatherMeasures | Unità di dati di telemetria Meteo. Di seguito sono riportate le unità definite dal sistema: NoUnit, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, Mercury, PSI, MilliMeter, CentiMeter, Meter, Inch, Feet, Mile, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, Degree, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, Percentage, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, Liter, MilliLiter, Seconds, UnixTimestamp, MicroMolPerMeterSquaredPerSecond, and InchesPerHour. Per aggiungere altre informazioni, vedere l'API/ExtendedType o nella [sezione aggiungere tipi e unità](weather-partner-integration-in-azure-farmbeats.md#add-extendedtype) riportata di seguito.
  weatherMeasures > AggregationType  | Nessuno, medio, massimo, minimo, StandardDeviation, somma, totale
  Profondità > weatherMeasures  | Profondità del sensore espressa in centimetri. Ad esempio, la misurazione dell'umidità a 10 cm sotto la superficie.
  Descrizione > weatherMeasures  | Inserire una descrizione significativa della misurazione. |
  **JobType** | **Descrizione** |
  Nome  | nome del processo, ad esempio Get_Daily_Forecast; il processo che il cliente eseguirà per ottenere i dati meteo|
  pipelineDetails parametri di > > nome  | nome del parametro |
  tipo di > pipelineDetails > Parameters | una stringa, int, float, bool, array |
  parametri > pipelineDetails > obbligatorio | Boolean true se il parametro è obbligatorio; in caso contrario, false. il valore predefinito è true |
  parametri > pipelineDetails > defaultValue | Valore predefinito del parametro |
  Descrizione > pipelineDetails > Parameters | Descrizione del parametro |
  Proprietà  | Proprietà aggiuntive inserite dal produttore.
  Proprietà > **programRunCommand** | comando Docker Run: questo comando verrà eseguito quando il cliente esegue il processo Weather. |
  **WeatherDataLocation** | **Descrizione** |
  weatherDataModelId  | ID del WeatherDataModel corrispondente creato durante il bootstrap|
  posizione  | rappresenta la latitudine, la longitudine e l'elevazione |
  Nome | Nome dell'oggetto |
  Descrizione | Descrizione |
  farmId | **facoltativo** ID della farm fornita dal cliente come parte del parametro Job |
  Proprietà  | Proprietà aggiuntive inserite dal produttore.

 Per informazioni su ciascun oggetto e sulle relative proprietà, vedere [Swagger](https://aka.ms/FarmBeatsSwagger).

 > [!NOTE]
 > Le API restituiscono ID univoci per ogni istanza creata. Questi ID devono essere conservati da Translator per la gestione dei dispositivi e la sincronizzazione dei metadati.

**Sincronizzazione dei metadata**

Il connettore Docker deve essere in grado di inviare aggiornamenti sui metadati. Esempi di scenari di aggiornamento sono: l'aggiunta di nuovi parametri meteorologici nel set di dati del provider meteorologico, l'aggiunta di funzionalità, ad esempio Aggiunta di previsioni di 30 giorni)

> [!NOTE]
> L'eliminazione non è supportata per i metadati, ad esempio. modello di dati meteorologici.
>
> Per aggiornare i metadati, è obbligatorio chiamare/Get/{ID} sul modello di dati meteorologici, aggiornare le proprietà modificate, quindi eseguire un/Put/{ID} in modo che le proprietà impostate dall'utente non vadano perse.

## <a name="weather-data-telemetry-specifications"></a>Specifiche di dati meteorologici (telemetria)

Viene eseguito il mapping dei dati meteorologici a un messaggio canonico di cui viene effettuato il push in un hub eventi di Azure per l'elaborazione. Azure EventHub è un servizio che consente l'inserimento di dati in tempo reale (telemetria) da applicazioni e dispositivi connessi. Per inviare i dati meteo a FarmBeats, è necessario creare un client che invii messaggi a un hub eventi in FarmBeats. Per altre informazioni sull'invio di dati di telemetria, vedere [invio di dati di telemetria a un hub eventi](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send)

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
   "weatherstations": [
   {
   "id": "id of the WeatherDataLocation",
   "weatherdata": [
   {
     "timestamp": "timestamp of the data. For historical, this is the time for which the observations are sent. For forecast this is the time for which data is forecasted. Format is ISO 8601. Default time-zone is UTC",
     "predictiontimestamp": "timestamp on which the forecast data is predicted i.e time of prediction. Required only for forecast data. Format is ISO 8601. Default timezone is UTC ",
     "weathermeasurename1": <value>,
     "weathermeasurename2": <value>
     }
     ]
    }
   ]
}
```

Ecco ad esempio un messaggio di dati di telemetria:

```json
{
   "weatherstations": [
   {
     "id": "5e4b34e7-bf9e-4f39-xxxx-f3c06d0366ea",
     "weatherdata": [
     {
      "timestamp": "2019-07-10T00:00:00Z",
      "predictiontimestamp": "2019-07-05T00:00:00Z",
      "PrecipitationTotalLiquidEquivalent": 0,
      "AvgPressure": 0,
      "AvgRelativeHumidity": 72
     }
    ] 
  }
 ]
}

```

## <a name="troubleshooting-and-error-management"></a>Risoluzione dei problemi e gestione degli errori

**Registrazione degli errori**

Poiché il processo partner verrà eseguito nel Framework dei processi esistente, gli errori vengono registrati nello stesso modo degli errori relativi ad altri processi preesistenti in FarmBeats, ad esempio GetFarmData, SensorPlacement e così via. L'attività di ADF in esecuzione nella pipeline di ADF registra sia STDERR che STDOUT. Entrambi i file sono disponibili nell'account di archiviazione "datahublogs-xxx" all'interno del gruppo di risorse FarmBeats.

Datahub lib fornisce funzioni di supporto per abilitare la registrazione nell'ambito dei log datahub complessivi. Ulteriori dettagli [qui](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/framework/logger.py).

**Risoluzione dei problemi o supporto**

Nel caso in cui il cliente non sia in grado di ricevere dati meteorologici nell'istanza di FarmBeats specificata, il partner meteorologico deve fornire supporto e un meccanismo per la risoluzione dei problemi.

## <a name="add-extendedtype"></a>Aggiungi ExtendedType

FarmBeats supporta l'aggiunta di nuovi tipi e unità di misura del sensore. Si noti che un partner meteorologico può aggiungere nuove unità/tipi aggiornando il bootstrap_manifest.jssul file nell'implementazione di riferimento [qui](https://github.com/azurefarmbeats/noaa_docker)

Per aggiungere un nuovo tipo di WeatherMeasure, ad esempio "PrecipitationDepth", seguire questa procedura.

1. Eseguire una richiesta GET su/ExtendedType con il filtro di query-Key = WeatherMeasureType
2. Prendere nota dell'ID dell'oggetto restituito.
3. Aggiungere il nuovo tipo all'elenco nell'oggetto restituito ed effettuare una richiesta PUT in/ExtendedType{ID} con il nuovo elenco seguente. Il payload di input deve corrispondere alla risposta ricevuta sopra e alla nuova unità aggiunta alla fine dell'elenco di valori.

Per altre informazioni sull’API /ExtendedType, vedere [Swagger](https://aka.ms/FarmBeatsSwagger).

## <a name="next-steps"></a>Passaggi successivi

A questo punto si dispone di un connettore Docker che si integra con FarmBeats. A questo punto è possibile vedere come ottenere i dati meteorologici usando Docker in FarmBeats. Vedere [ottenere i dati meteorologici](get-weather-data-from-weather-partner.md).
