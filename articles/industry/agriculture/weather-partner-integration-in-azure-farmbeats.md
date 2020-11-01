---
title: Integrazione di partner per il meteo
description: Informazioni su come un provider di dati meteorologici può integrarsi con FarmBeats.
author: sunasing
ms.topic: article
ms.date: 07/09/2020
ms.author: sunasing
ms.openlocfilehash: f0fbd93e2a5f4e92089e10e75dc17e304ff80bf6
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2020
ms.locfileid: "93147080"
---
# <a name="weather-partner-integration-with-farmbeats"></a>Integrazione di Weather partner con FarmBeats

Questo articolo fornisce informazioni sul componente Docker di Azure FarmBeats Connector. Come provider di dati meteorologici, è possibile usare il connettore Docker per l'integrazione con FarmBeats. Usare le relative API per inviare i dati meteo a FarmBeats. In FarmBeats i dati possono essere usati per la fusione dei dati e per la creazione di modelli di apprendimento automatico o modelli di intelligenza artificiale.

 > [!NOTE]
 > In questo articolo viene usata un' [implementazione di riferimento](https://github.com/azurefarmbeats/noaa_docker) compilata usando i set di dati aperti di Azure e i dati meteorologici di National Oceanic and atmosferical Administration (NOAA). Viene anche usata l' [immagine Docker](https://hub.docker.com/r/azurefarmbeats/farmbeats-noaa)corrispondente.

È necessario fornire un' [immagine Docker o un programma appropriato](#docker-specifications) e ospitare l'immagine Docker in un registro contenitori a cui i clienti possono accedere. Fornire le informazioni seguenti ai clienti:

- URL dell'immagine Docker
- Tag immagine Docker
- Chiavi o credenziali per accedere all'immagine Docker
- Chiavi API o credenziali specifiche del cliente per accedere ai dati dal sistema
- Dettagli dello SKU della VM (fornire questi dettagli se l'immagine Docker presenta requisiti specifici per le macchine virtuali. In caso contrario, i clienti possono scegliere tra gli SKU di VM supportati in Azure.

I clienti usano queste informazioni Docker per registrare un partner Meteo nell'istanza di FarmBeats. Per altre informazioni su come i clienti possono usare Docker per inserire i dati meteorologici in FarmBeats, vedere [ottenere dati da partner meteorologici](./get-weather-data-from-weather-partner.md).

## <a name="connector-docker-development"></a>Sviluppo del connettore Docker

**Integrazione basata sull'API REST**

Le API FarmBeats contengono la documentazione tecnica di spavalderia. Per altre informazioni sulle API e sulle richieste o risposte corrispondenti, vedere il [FarmBeats spavalderia](https://aka.ms/farmbeatsswagger). 

Se è già stato installato FarmBeats, accedere al FarmBeats spavalderia `https://yourfarmbeatswebsitename-api.azurewebsites.net/swagger`

Si noti che l' *API* viene aggiunta al nome del sito Web FarmBeats. L'endpoint API è `https://yourfarmbeatswebsitename-api.azurewebsites.net`

### <a name="datahub-lib"></a>Lib datahub

FarmBeats fornisce un lib che è possibile usare. Lib è attualmente disponibile come [parte dell'implementazione di riferimento](https://github.com/azurefarmbeats/noaa_docker/tree/master/datahub_lib). In seguito sarà disponibile come SDK per più linguaggi.

### <a name="authentication"></a>Authentication

**Autenticazione con API FarmBeats**

FarmBeats usa l'autenticazione di portar. È possibile accedere alle API fornendo un token di accesso nella sezione dell'intestazione della richiesta. Ecco un esempio:

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

È possibile richiedere il token di accesso da un'app funzioni di Azure in esecuzione nell'istanza di FarmBeats del cliente. L'URL di funzioni di Azure viene fornito al programma Docker come argomento. È possibile ottenere il token di accesso effettuando una `GET` richiesta sull'URL. La risposta dall'URL contiene il token di accesso. 

Usare le funzioni di supporto in lib datahub per ottenere il token di accesso. Per altre informazioni, vedere la [pagina di GitHub per l'immagine Docker NOAA](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_auth_helper.py).

Il token di accesso è valido solo per alcune ore. Alla scadenza, è necessario richiederlo nuovamente.

**Autenticazione con API lato partner**

Per eseguire l'autenticazione con le API lato partner mentre è in esecuzione il processo Docker, i clienti devono fornire le credenziali durante la registrazione del partner. Ecco un esempio:

```json
{
 "partnerCredentials": {
   "key1": "value1",
   "key2": "value2"
   }
}
```
Il servizio API Serializza questo dict e lo archivia in un insieme di credenziali delle [chiavi](../../key-vault/general/basic-concepts.md).

[Azure Data Factory](../../data-factory/introduction.md) viene usato per orchestrare i processi meteorologici. Avvia le risorse per eseguire il codice docker. Data Factory fornisce anche un meccanismo per eseguire il push dei dati in modo sicuro alla macchina virtuale in cui viene eseguito il processo docker. Le credenziali API vengono quindi archiviate in modo sicuro nell'insieme di credenziali delle chiavi. 

Le credenziali vengono lette come stringhe protette dall'insieme di credenziali delle chiavi. Vengono fornite come proprietà estese nella directory di lavoro del contenitore docker. Il percorso del file è *nella working_dir/activity.json* . 

Il codice Docker può leggere le credenziali da *activity.jsin* fase di esecuzione per accedere alle API lato partner per il cliente. Nel file JSON le credenziali hanno un aspetto simile a questo esempio di codice:

```json
{ 
   "typeProperties" : {
      "extendedProperties" : { 
         "partnerCredentials": "" } 
   } 
}
```
La `partnerCredentials` credenziale è disponibile nel modo in cui il cliente lo ha fornito durante la registrazione del partner.

FarmBeats lib fornisce funzioni helper. Utilizzare queste funzioni per leggere le credenziali dalle proprietà dell'attività. Per altre informazioni, vedere la [pagina di GitHub per l'immagine Docker NOAA](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_adf_helper.py).

Il file viene usato solo quando il codice Docker è in esecuzione. Al termine del codice, il file viene eliminato.

Per ulteriori informazioni sul funzionamento di Data Factory pipeline e attività, vedere [mapping di tipi di dati e schema](../../data-factory/copy-activity-schema-and-type-mapping.md).

**Intestazioni di richiesta HTTP**

La tabella seguente illustra le intestazioni di richiesta più comuni che è necessario specificare quando si effettua una chiamata API a FarmBeats.

Intestazione | Descrizione ed esempio
--- | ---
Content-Type | Formato della richiesta. Esempio: `Content-Type: application/<format>` <br/>Il formato per l’API FarmBeats Datahub è JSON. Esempio: ` Content-Type: application/json`
Autorizzazione | Il token di accesso necessario per effettuare una chiamata API. Esempio: `Authorization: Bearer <Access-Token>`
Accept | Formato della risposta. Il formato per l’API FarmBeats Datahub è JSON. Esempio: `Accept: application/json`

## <a name="data-format"></a>Formato dati

JSON è un formato di dati comune indipendente dal linguaggio che offre una rappresentazione testuale semplice di strutture di dati arbitrarie. Per ulteriori informazioni, vedere [JSON.org](https://json.org).

## <a name="docker-specifications"></a>Specifiche di Docker

Il programma Docker richiede due componenti: il bootstrap e il processo. Il programma può avere più di un processo.

### <a name="bootstrap"></a>Bootstrap

Il componente bootstrap deve essere eseguito quando il cliente avvia la registrazione di Docker in FarmBeats. Gli argomenti ( `arg1` e `arg2` ) seguenti vengono passati al programma:

- **Endpoint API FarmBeats** : endpoint API FarmBeats per le richieste API. Questo endpoint effettua chiamate API alla distribuzione di FarmBeats.
- **URL di funzioni di Azure** : endpoint personalizzato. Questo URL fornisce il token di accesso per le API FarmBeats. È possibile chiamare `GET` su questo URL per recuperare il token di accesso.

Il bootstrap crea i metadati necessari agli utenti per eseguire i processi per ottenere i dati meteorologici. Per ulteriori informazioni, vedere l' [implementazione di riferimento](https://github.com/azurefarmbeats/noaa_docker). 

Se si Personalizza il *bootstrap_manifest.jsnel* file, il programma bootstrap di riferimento creerà automaticamente i metadati necessari. Il programma bootstrap crea i metadati seguenti: 

 > [!NOTE]
 > Se si aggiorna il *bootstrap_manifest.jsnel* file come descritto nell' [implementazione di riferimento](https://github.com/azurefarmbeats/noaa_docker) , non è necessario creare i metadati seguenti. Il programma bootstrap utilizzerà il file manifesto per creare i metadati necessari.

- /**WeatherDataModel** : i metadati WeatherDataModel rappresentano i dati meteorologici. Corrisponde ai set di dati forniti dall'origine. Ad esempio, un DailyForecastSimpleModel potrebbe fornire informazioni sulla temperatura, l'umidità e le precipitazioni medie una volta al giorno. Al contrario, un DailyForecastAdvancedModel può fornire molte più informazioni a livello di granularità oraria. È possibile creare un numero qualsiasi di modelli di dati meteorologici.
- /**Tipoprocesso** : FarmBeats dispone di un sistema di gestione dei processi estensibile. Come provider di dati meteorologici, sono disponibili vari set di dati e API (ad esempio, GetDailyForecasts). È possibile abilitare questi set di impostazioni e API in FarmBeats usando Tipoprocesso. Dopo la creazione di un tipo di processo, un cliente può attivare processi di quel tipo per ottenere i dati meteorologici per la loro posizione o la loro farm di interesse. Per altre informazioni, vedere Tipoprocesso e le API di processo in [FarmBeats spavalderia](https://aka.ms/farmbeatsswagger).

### <a name="jobs"></a>Processi

Il componente processi viene richiamato ogni volta che un utente FarmBeats esegue un processo del/JobType creato come parte del processo di bootstrap. Il comando Docker Run per il processo è definito come parte del/JobType creato.

Il processo recupera i dati dall'origine e li inserisce in FarmBeats. Durante il processo di bootstrap, i parametri necessari per ottenere i dati devono essere definiti come parte di/JobType.

Come parte del processo, il programma deve creare una/WeatherDataLocation in base al/WeatherDataModel creato durante il processo di bootstrap. Il valore di/WeatherDataLocation corrisponde a un percorso (coordinate di latitudine e longitudine) fornito dall'utente come parametro per il processo.

### <a name="object-details"></a>Dettagli oggetto

WeatherDataModel | Descrizione |
--- | ---
Nome  | Nome del modello di dati meteorologici. |
Descrizione  | Descrizione significativa del modello. |
Proprietà  | Proprietà aggiuntive definite dal provider di dati. |
Nome > weatherMeasures  | Nome della misura Meteo. Ad esempio, humidity_max. |
Tipo di dati > weatherMeasures  | Double o enum. Se enum, measureEnumDefinition è obbligatorio. |
weatherMeasures > measureEnumDefinition  | Obbligatorio solo se DataType è enum. Ad esempio, usare `{ "NoRain": 0, "Snow": 1, "Drizzle": 2, "Rain": 3 }` |
Tipo > weatherMeasures  | Tipo di dati di telemetria Meteo. Ad esempio, RelativeHumidity. I tipi definiti dal sistema sono Temperaturaambiente, nounit, CO2, depth, ElectricalConductivity, LeafWetness, length, LiquidLevel, nitrate, O2, PH, fosfato, PointInTime, potassio, Pressure, RainGauge, RelativeHumidity, salinità, SoilMoisture, SoilTemperature, SolarRadiation, state, TimeDuration, UVRadiation, UVIndex, volume, WindDirection, WindRun, WindSpeed, evapotraspirazione e PAR. Per aggiungere altri tipi, vedere la sezione [Add ExtendedType](#add-extendedtype) in questo articolo.
Unità > weatherMeasures | Unità di dati di telemetria Meteo. Le unità definite dal sistema sono nounit, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, mercurio, PSI, millimetro, centimetro, misuratore, pollice, piedi, miglio, chilometro, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, Degree, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, percentuale, PartsPerMillion, micromole, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, litro, millilitro, secondi, UnixTimestamp, MicroMolePerMeterSquaredPerSecond e InchesPerHour Per aggiungere altre unità, vedere la sezione [Add ExtendedType](#add-extendedtype) in questo articolo.
weatherMeasures > AggregationType  | Tipo di aggregazione. I valori possibili sono None, Average, Maximum, Minimum, StandardDeviation, Sum e Total.
Profondità > weatherMeasures  | Profondità del sensore espressa in centimetri. Ad esempio, la misurazione dell'umidità a 10 cm sotto la superficie.
Descrizione > weatherMeasures  | Descrizione significativa della misura. 

JobType | Descrizione |
--- | ---
Nome  | Nome del processo. Ad esempio, Get_Daily_Forecast. Il cliente eseguirà questo processo per ottenere i dati meteorologici.|
pipelineDetails parametri di > > nome  | Nome del parametro. |
tipo di > pipelineDetails > Parameters | Tipo di parametro. I valori possibili sono String, int, float, bool e Array. |
parametri > pipelineDetails > obbligatorio | Valore Boolean del parametro. Il valore è true se il parametro è obbligatorio. In caso contrario, il valore è false. Il valore predefinito è true. |
parametri > pipelineDetails > defaultValue | Valore predefinito del parametro. |
Descrizione > pipelineDetails > Parameters | Descrizione del parametro. |
Proprietà  | Proprietà aggiuntive inserite dal produttore.
Proprietà > programRunCommand | Comando Docker Run. Questo comando viene eseguito quando il cliente esegue il processo Weather. |

WeatherDataLocation | Descrizione |
--- | ---
weatherDataModelId  | ID del WeatherDataModel corrispondente creato durante il processo di bootstrap.|
posizione  | Latitudine, longitudine ed elevazione dei privilegi. |
Nome | Nome dell'oggetto. |
Descrizione | Descrizione della posizione dei dati meteorologici. |
farmId | Opzionale ID della farm. Il cliente fornisce questo ID come parte del parametro Job. |
Proprietà  | Proprietà aggiuntive inserite dal produttore.

Per ulteriori informazioni sugli oggetti e sulle relative proprietà, vedere [FarmBeats spavalderia](https://aka.ms/FarmBeatsSwagger).

> [!NOTE]
> Le API restituiscono ID univoci per ogni istanza creata. Il convertitore per la gestione dei dispositivi e la sincronizzazione dei metadati deve conservare questo ID.

**Sincronizzazione dei metadata**

Il componente docker del connettore deve essere in grado di inviare aggiornamenti sui metadati. Ad esempio, deve inviare aggiornamenti quando il provider Weather aggiunge nuovi parametri a un set di dati o quando viene aggiunta una nuova funzionalità, ad esempio una nuova previsione di 30 giorni.

> [!NOTE]
> L'eliminazione non è supportata per i metadati nel modello di dati meteorologici.
>
> Per aggiornare i metadati, è necessario chiamare `/Get/{ID}` sul modello di dati meteorologici. Aggiornare le proprietà modificate, quindi eseguire un'operazione `/Put/{ID}` per mantenere le proprietà impostate dall'utente.

## <a name="weather-data-telemetry-specifications"></a>Specifiche di dati meteorologici (telemetria)

Viene eseguito il mapping dei dati meteorologici a un messaggio canonico che viene inserito in un hub eventi di Azure per l'elaborazione. Hub eventi di Azure è un servizio che consente l’inserimento di dati (di telemetria) in tempo reale da applicazioni e dispositivi connessi. 

Per inviare i dati meteo a FarmBeats, creare un client che invii messaggi a un hub eventi in FarmBeats. Per altre informazioni, vedere [invio di dati di telemetria a un hub eventi](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).

Il codice Python di esempio seguente invia dati di telemetria come client a un hub eventi specificato.

```python
import azure
from azure.eventhub import EventHubClient, Sender, EventData, Receiver, Offset
EVENTHUBCONNECTIONSTRING = "<EventHub connection string provided by customer>"
EVENTHUBNAME = "<EventHub name provided by customer>"

write_client = EventHubClient.from_connection_string(EVENTHUBCONNECTIONSTRING, eventhub=EVENTHUBNAME, debug=False)
sender = write_client.add_sender(partition="0")
write_client.run()
for i in range(5):
    telemetry = "<Canonical telemetry message>"
    print("Sending telemetry: " + telemetry)
    sender.send(EventData(telemetry))
write_client.stop()

```

Ecco il formato del messaggio canonico:

```json
{
   "weatherstations": [
   {
   "id": "ID of the WeatherDataLocation.",
   "weatherdata": [
   {
     "timestamp": "Timestamp of the data. For historical purposes, this is the time for which the observations are sent. For forecast, this is the time for which data is forecasted. Format is ISO 8601. Default time zone is UTC.",
     "predictiontimestamp": "Timestamp on which the forecast data is predicted. I.e., the time of prediction. Required only for forecast data. Format is ISO 8601. Default time zone is UTC. ",
     "weathermeasurename1": <value>,
     "weathermeasurename2": <value>
     }
     ]
    }
   ]
}
```

Ecco un esempio di messaggio di telemetria:

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

### <a name="error-logging"></a>Registrazione degli errori

Il processo partner viene eseguito nel Framework dei processi esistente. Gli errori vengono quindi registrati nello stesso modo degli errori per altri processi FarmBeats preesistenti, ad esempio GetFarmData e SensorPlacement. L'attività Data Factory eseguita all'interno della pipeline Data Factory registra sia `STDERR` che `STDOUT` . Entrambi i file sono disponibili nell' `datahublogs-xxx` account di archiviazione all'interno del gruppo di risorse FarmBeats.

Datahub lib fornisce funzioni di supporto per abilitare la registrazione nell'ambito dei log datahub complessivi. Per altre informazioni, vedere la [pagina di GitHub per l'immagine Docker NOAA](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/framework/logger.py).

### <a name="troubleshooting-and-support"></a>Risoluzione dei problemi e supporto

Se il cliente non può ricevere dati meteorologici nell'istanza di FarmBeats, fornire supporto e un meccanismo per la risoluzione del problema.

## <a name="add-extendedtype"></a>Aggiungi ExtendedType

FarmBeats supporta l'aggiunta di nuovi tipi e unità di misura del sensore. È possibile aggiungere nuove unità o tipi aggiornando il *bootstrap_manifest.js* nel file nell' [implementazione di riferimento](https://github.com/azurefarmbeats/noaa_docker).

Attenersi alla seguente procedura per aggiungere un nuovo tipo di WeatherMeasure, ad esempio PrecipitationDepth.

1. Eseguire una `GET` richiesta su/ExtendedType usando la query `filter - key = WeatherMeasureType` .
2. Prendere nota dell'ID dell'oggetto restituito.
3. Aggiungere il nuovo tipo all'elenco nell'oggetto restituito. Eseguire una `PUT` richiesta in/ExtendedType{ID} con il nuovo elenco seguente. Il payload di input deve corrispondere alla risposta ricevuta in precedenza. La nuova unità deve essere aggiunta alla fine dell'elenco di valori.

Per altre informazioni sull'API/ExtendedType, vedere [FarmBeats spavalderia](https://aka.ms/FarmBeatsSwagger).

## <a name="next-steps"></a>Passaggi successivi

A questo punto si dispone di un componente docker del connettore che si integra con FarmBeats. Quindi, scoprire come [ottenere i dati meteorologici](get-weather-data-from-weather-partner.md) usando l'immagine Docker in FarmBeats. 
