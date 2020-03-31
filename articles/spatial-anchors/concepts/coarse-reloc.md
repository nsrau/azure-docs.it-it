---
title: Rilocalizzazione grossolana
description: Scopri come usare la rilocalizzazione grossolana per trovare le ancore vicino a te.
author: bucurb
manager: dacoghl
services: azure-spatial-anchors
ms.author: bobuc
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 4c1604eaad1ebdedf6a360a647fe5b9f95c829c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844395"
---
# <a name="coarse-relocalization"></a>Rilocalizzazione grossolana

Rilocalizzazione grossolana è una caratteristica che fornisce una risposta iniziale alla domanda: *Dove è il mio dispositivo ora / Quale contenuto devo osservare?* La risposta non è precisa, ma invece è nella forma: *sei vicino a questi ancoraggi; prova a localizzarne uno.*

La rilocalizzazione grossolana funziona associando varie letture del sensore sul dispositivo sia alla creazione che all'interrogazione degli ancoraggi. Per gli scenari esterni, i dati del sensore sono in genere la posizione GPS (Global Positioning System) del dispositivo. Quando il GPS non è disponibile o inaffidabile (ad esempio in ambienti interni), i dati del sensore sono costituiti dai punti di accesso WiFi e dai beacon Bluetooth nel raggio d'azione. Tutti i dati dei sensori raccolti contribuiscono a mantenere un indice spaziale, usati dagli ancoraggi spaziali di Azure per determinare rapidamente gli ancoraggi che si trovano entro circa 100 metri dal dispositivo.

La rapida ricerca di ancore abilitate da una rilocalizzazione grossolana semplifica lo sviluppo di applicazioni supportate da collezioni su scala mondiale di (diciamo, milioni di ancoraggi geo-distribuiti). La complessità della gestione dell'ancora è tutto nascosto, consentendo di concentrarsi di più sulla logica dell'applicazione impressionante. Tutto l'ancoraggio di sollevamento pesante viene eseguito per l'utente dietro le quinte da Azure Spatial Anchors.All the anchor heavy-lifting is done for you by you by you by Azure Spatial Anchors.

## <a name="collected-sensor-data"></a>Dati dei sensori raccolti

I dati del sensore che è possibile inviare al servizio di ancoraggio sono i seguenti:

* Posizione GPS: latitudine, longitudine, altitudine.
* Potenza di segnale dei punti di accesso WiFi nel raggio d'azione.
* Potenza del segnale dei fari Bluetooth in gamma.

In generale, l'applicazione dovrà acquisire autorizzazioni specifiche del dispositivo per accedere ai dati GPS, WiFi o BLE. Inoltre, alcuni dei dati del sensore di cui sopra non sono disponibili in base alla progettazione su alcune piattaforme. Per tenere conto di queste situazioni, la raccolta dei dati del sensore è facoltativa ed è disattivata per impostazione predefinita.

## <a name="set-up-the-sensor-data-collection"></a>Configurare la raccolta dei dati del sensore

Iniziamo creando un provider di impronte digitali del sensore e rendendo la sessione consapevole di esso:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
// Create the sensor fingerprint provider
sensorProvider = new PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = new CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.LocationProvider = sensorProvider;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
// Create the sensor fingerprint provider
ASAPlatformLocationProvider *sensorProvider;
sensorProvider = [[ASAPlatformLocationProvider alloc] init];

// Create and configure the session
cloudSpatialAnchorSession = [[ASACloudSpatialAnchorSession alloc] init];

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.locationProvider = sensorProvider;
```

# <a name="swift"></a>[Repentino](#tab/swift)

```swift
// Create the sensor fingerprint provider
var sensorProvider: ASAPlatformLocationProvider?
sensorProvider = ASAPlatformLocationProvider()

// Create and configure the session
cloudSpatialAnchorSession = ASACloudSpatialAnchorSession()

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession!.locationProvider = sensorProvider
```

# <a name="java"></a>[Java](#tab/java)

```java
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = new PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = new CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.setLocationProvider(sensorProvider);
```

# <a name="c-ndk"></a>[NDK di C](#tab/cpp)

```cpp
// Create the sensor fingerprint provider
std::shared_ptr<PlatformLocationProvider> sensorProvider;
sensorProvider = std::make_shared<PlatformLocationProvider>();

// Create and configure the session
cloudSpatialAnchorSession = std::make_shared<CloudSpatialAnchorSession>();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession->LocationProvider(sensorProvider);
```

# <a name="c-winrt"></a>[WinRT in C](#tab/cppwinrt)
```cpp
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.LocationProvider(sensorProvider);
```
---

Successivamente, è necessario decidere quali sensori si desidera utilizzare per la rilocalizzazione grossolana. Questa decisione è specifica per l'applicazione che si sta sviluppando, ma i consigli nella tabella seguente dovrebbero fornire un buon punto di partenza:


|             | All' interno | All' aperto |
|-------------|---------|----------|
| Gps         | Disattivato | Attivato |
| WiFi        | Attivato | Attivato (opzionale) |
| Fari BLE | Attivato (opzionale con avvertenze, vedere di seguito) | Disattivato |


### <a name="enabling-gps"></a>Abilitazione del GPS

Supponendo che l'applicazione disponga già dell'autorizzazione per accedere alla posizione GPS del dispositivo, è possibile configurare gli ancoraggi spaziali di Azure per usarlo:Assuming your application already have permission to access the device's GPS position, you can configure Azure Spatial Anchors to use it:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
sensorProvider.Sensors.GeoLocationEnabled = true;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.geoLocationEnabled = true;
```

# <a name="swift"></a>[Repentino](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.geoLocationEnabled = true
```

# <a name="java"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setGeoLocationEnabled(true);
```

# <a name="c-ndk"></a>[NDK di C](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->GeoLocationEnabled(true);
```

# <a name="c-winrt"></a>[WinRT in C](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.GeoLocationEnabled(true);
```

---

Quando si utilizza il GPS nell'applicazione, tenere presente che le letture fornite dall'hardware sono in genere:

* frequenza asincrona e bassa (meno di 1 Hz).
* inaffidabile / rumoroso (in media deviazione standard di 7 m).

In generale, sia il sistema operativo del dispositivo che gli ancoraggi spaziali di Azure eseguiranno alcuni filtri ed estrapolazioni sul segnale GPS non elaborato nel tentativo di ridurre questi problemi. Questa elaborazione aggiuntiva richiede più tempo per la convergenza, pertanto per ottenere risultati ottimali è consigliabile provare a:

* creare un sensore di impronte digitali il più presto possibile nella vostra applicazione
* mantenere attivo il provider di impronte digitali del sensore tra più sessioni
* condividere il provider di impronte digitali del sensore tra più sessioni

Se si prevede di utilizzare il provider di impronte digitali del sensore al di fuori di una sessione di ancoraggio, assicurarsi di avviarlo prima di richiedere le stime del sensore. Ad esempio, il codice seguente si occuperà dell'aggiornamento della posizione del dispositivo sulla mappa in tempo reale:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
// Game about to start, start tracking the sensors
sensorProvider.Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.GetLocationEstimate();

    // Paint it on the map
    drawCircle(
        x: geoPose.Longitude,
        y: geoPose.Latitude,
        radius: geoPose.HorizontalError);
}

// Game ended, no need to track the sensors anymore
sensorProvider.Stop();
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
// Game about to start, start tracking the sensors
[sensorProvider start];

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    ASAGeoLocation *geoPose = [sensorProvider getLocationEstimate];

    // Paint it on the map
    drawCircle(geoPose.longitude, geoPose.latitude, geoPose.horizontalError);
}

// Game ended, no need to track the sensors anymore
[sensorProvider stop];
```

# <a name="swift"></a>[Repentino](#tab/swift)

```swift
// Game about to start, start tracking the sensors
sensorProvider?.start()

// Game loop
while m_isRunning
{
    // Get the GPS estimate
    var geoPose: ASAGeoLocation?
    geoPose = sensorProvider?.getLocationEstimate()

    // Paint it on the map
    drawCircle(geoPose.longitude, geoPose.latitude, geoPose.horizontalError)
}

// Game ended, no need to track the sensors anymore
sensorProvider?.stop()
```

# <a name="java"></a>[Java](#tab/java)

```java
// Game about to start, start tracking the sensors
sensorProvider.start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.getLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose.getLongitude(), geoPose.getLatitude(), geoPose.getHorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider.stop();
```

# <a name="c-ndk"></a>[NDK di C](#tab/cpp)

```cpp
// Game about to start, start tracking the sensors
sensorProvider->Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    std::shared_ptr<GeoLocation> geoPose = sensorProvider->GetLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose->Longitude(), geoPose->Latitude(), geoPose->HorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider->Stop();
```

# <a name="c-winrt"></a>[WinRT in C](#tab/cppwinrt)

```cpp
// Game about to start, start tracking the sensors
sensorProvider.Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.GetLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose.Longitude(), geoPose.Latitude(), geoPose.HorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider.Stop();
```

---

### <a name="enabling-wifi"></a>Abilitazione del WiFi

Supponendo che l'applicazione disponga già dell'autorizzazione per accedere allo stato WiFi del dispositivo, è possibile configurare gli ancoraggi spaziali di Azure per usarlo:Assuming your application already have permission to access the device's WiFi state, you can configure Azure Spatial Anchors to use it:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
sensorProvider.Sensors.WifiEnabled = true;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.wifiEnabled = true;
```

# <a name="swift"></a>[Repentino](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.wifiEnabled = true
```

# <a name="java"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setWifiEnabled(true);
```

# <a name="c-ndk"></a>[NDK di C](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->WifiEnabled(true);
```

# <a name="c-winrt"></a>[WinRT in C](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.WifiEnabled(true);
```

---

Quando si utilizza il WiFi nell'applicazione, tenere presente che le letture fornite dall'hardware sono in genere:

* frequenza asincrona e bassa (meno di 0,1 Hz).
* potenzialmente limitato a livello di sistema operativo.
* inaffidabile / rumoroso (in media 3-dBBm deviazione standard).

Gli ancoraggi spaziali di Azure tenteranno di creare una mappa della potenza del segnale WiFi filtrata durante una sessione nel tentativo di ridurre questi problemi. Per ottenere i migliori risultati si dovrebbe cercare di:

* creare bene la sessione prima di posizionare il primo ancoraggio.
* mantenere attiva la sessione il più a lungo possibile (ovvero, creare tutti gli ancoraggi e interrogare in una sessione).

### <a name="enabling-bluetooth-beacons"></a>Abilitazione dei beacon Bluetooth

Supponendo che l'applicazione disponga già dell'autorizzazione per accedere allo stato Bluetooth del dispositivo, è possibile configurare gli ancoraggi spaziali di Azure per usarlo:Assuming your application already have permission to access the device's Bluetooth state, you can configure Azure Spatial Anchors to use it:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
sensorProvider.Sensors.BluetoothEnabled = true;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.bluetoothEnabled = true;
```

# <a name="swift"></a>[Repentino](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.bluetoothEnabled = true
```

# <a name="java"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setBluetoothEnabled(true);
```

# <a name="c-ndk"></a>[NDK di C](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->BluetoothEnabled(true);
```

# <a name="c-winrt"></a>[WinRT in C](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors();
sensors.BluetoothEnabled(true);
```

---

I beacon sono in genere dispositivi versatili, in cui è possibile configurare tutto, inclusi gli UUID e gli indirizzi MAC. Questa flessibilità può essere problematica per gli ancoraggi spaziali di Azure in quanto considera i beacon come identificati in modo univoco dai relativi UUID. Non riuscire a garantire questa unicità molto probabilmente causerà wormhole spaziali. Per ottenere i migliori risultati è necessario:

* assegnare UUID univoci ai beacon.
* distribuirli, in genere in un modello regolare, ad esempio una griglia.
* passare l'elenco degli UUID dei beacon univoci al provider di impronte digitali del sensore:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
sensorProvider.Sensors.KnownBeaconProximityUuids = new[]
{
    "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1",
    "a63819b9-8b7b-436d-88ec-ea5d8db2acb0",
    . . .
};
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
NSArray *uuids = @[@"22e38f1a-c1b3-452b-b5ce-fdb0f39535c1", @"a63819b9-8b7b-436d-88ec-ea5d8db2acb0"];

ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.knownBeaconProximityUuids = uuids;
```

# <a name="swift"></a>[Repentino](#tab/swift)

```swift
let uuids = [String]()
uuids.append("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1")
uuids.append("a63819b9-8b7b-436d-88ec-ea5d8db2acb0")

let sensors = locationProvider?.sensors
sensors.knownBeaconProximityUuids = uuids
```

# <a name="java"></a>[Java](#tab/java)

```java
String uuids[] = new String[2];
uuids[0] = "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1";
uuids[1] = "a63819b9-8b7b-436d-88ec-ea5d8db2acb0";

SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setKnownBeaconProximityUuids(uuids);
```

# <a name="c-ndk"></a>[NDK di C](#tab/cpp)

```cpp
std::vector<std::string> uuids;
uuids.push_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.push_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->KnownBeaconProximityUuids(uuids);
```

# <a name="c-winrt"></a>[WinRT in C](#tab/cppwinrt)

```cpp
std::vector<winrt::hstring> uuids;
uuids.emplace_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.emplace_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

SensorCapabilities sensors = sensorProvider.Sensors();
sensors.KnownBeaconProximityUuids(uuids);
```

---

Gli ancoraggi spaziali di Azure terranno traccia solo dei beacon Bluetooth presenti nell'elenco degli UUID di prossimità dei beacon noti. I fari dannosi programmati per avere UUID consentiti possono comunque avere un impatto negativo sulla qualità del servizio. Per questo motivo, è consigliabile utilizzare i beacon solo in spazi curati in cui è possibile controllare la loro distribuzione.

## <a name="querying-with-sensor-data"></a>Esecuzione di query con i dati del sensore

Dopo aver creato gli ancoraggi con i dati del sensore associati, è possibile iniziare a recuperarli utilizzando le letture del sensore segnalate dal dispositivo. Non sei più tenuto a fornire al servizio un elenco di ancoraggi noti che ti aspetti di trovare, invece di far sapere al servizio la posizione del tuo dispositivo come riportato dai suoi sensori di bordo. Gli ancoraggi spaziali di Azure scopriranno quindi il set di ancoraggi vicino al dispositivo e tenteranno di abbinarli visivamente.

Per fare in modo che le query utilizzino i dati del sensore, iniziare creando criteri "vicino al dispositivo":

# <a name="c"></a>[C #](#tab/csharp)

```csharp
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.DistanceInMeters = 5;

// Cap the number of anchors returned
nearDeviceCriteria.MaxResultCount = 25;

anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.NearDevice = nearDeviceCriteria;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
ASANearDeviceCriteria *nearDeviceCriteria = [[ASANearDeviceCriteria alloc] init];

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.distanceInMeters = 5.0f;

// Cap the number of anchors returned
nearDeviceCriteria.maxResultCount = 25;

ASAAnchorLocateCriteria *anchorLocateCriteria = [[ASAAnchorLocateCriteria alloc] init];
anchorLocateCriteria.nearDevice = nearDeviceCriteria;
```

# <a name="swift"></a>[Repentino](#tab/swift)

```swift
let nearDeviceCriteria = ASANearDeviceCriteria()!

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.distanceInMeters = 5.0

// Cap the number of anchors returned
nearDeviceCriteria.maxResultCount = 25

let anchorLocateCriteria = ASAAnchorLocateCriteria()!
anchorLocateCriteria.nearDevice = nearDeviceCriteria
```

# <a name="java"></a>[Java](#tab/java)

```java
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.setDistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria.setMaxResultCount(25);

AnchorLocateCriteria anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.setNearDevice(nearDeviceCriteria);
```

# <a name="c-ndk"></a>[NDK di C](#tab/cpp)

```cpp
auto nearDeviceCriteria = std::make_shared<NearDeviceCriteria>();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria->DistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria->MaxResultCount(25);

auto anchorLocateCriteria = std::make_shared<AnchorLocateCriteria>();
anchorLocateCriteria->NearDevice(nearDeviceCriteria);
```

# <a name="c-winrt"></a>[WinRT in C](#tab/cppwinrt)

```cpp
NearDeviceCriteria nearDeviceCriteria = NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.DistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria.MaxResultCount(25);

// Set the session's locate criteria
anchorLocateCriteria = AnchorLocateCriteria();
anchorLocateCriteria.NearDevice(nearDeviceCriteria);
```

---

Il `DistanceInMeters` parametro controlla la distanza di esplorazione del grafico di ancoraggio per recuperare il contenuto. Si supponga, ad esempio, di aver popolato dello spazio con ancoraggi a densità costante di 2 ogni metro. Inoltre, la fotocamera del dispositivo sta osservando una singola ancora e il servizio l'ha individuata con successo. Molto probabilmente sei interessato a recuperare tutti gli ancoraggi che hai posizionato nelle vicinanze piuttosto che il singolo ancoraggio che stai osservando. Supponendo che gli ancoraggi che hai posizionato siano collegati in un grafico, il servizio può recuperare tutti gli ancoraggi vicini seguendo i bordi nel grafico. La quantità di attraversamento del `DistanceInMeters`grafico fatto è controllato da ; ti verranno date tutte le ancore collegate a quella che `DistanceInMeters`hai localizzato, che sono più vicine di .

Tenere presente che i `MaxResultCount` valori di grandi dimensioni per possono influire negativamente sulle prestazioni. Impostarlo su un valore ragionevole per l'applicazione.

Infine, è necessario indicare alla sessione di utilizzare la ricerca basata su sensori:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
[cloudSpatialAnchorSession createWatcher:anchorLocateCriteria];
```

# <a name="swift"></a>[Repentino](#tab/swift)

```swift
cloudSpatialAnchorSession!.createWatcher(anchorLocateCriteria)
```

# <a name="java"></a>[Java](#tab/java)

```java
cloudSpatialAnchorSession.createWatcher(anchorLocateCriteria);
```

# <a name="c-ndk"></a>[NDK di C](#tab/cpp)

```cpp
cloudSpatialAnchorSession->CreateWatcher(anchorLocateCriteria);
```

# <a name="c-winrt"></a>[WinRT in C](#tab/cppwinrt)

```cpp
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

---

## <a name="expected-results"></a>Risultati previsti

I dispositivi GPS di qualità consumer sono in genere piuttosto imprecisi. Uno studio condotto da [Sandenbergen e Barbeau (2011)][6] riporta la precisione mediana dei telefoni cellulari con GPS assistito (A-GPS) per essere di circa 7 metri - un valore piuttosto grande da ignorare! Per tenere conto di questi errori di misurazione, il servizio considera gli ancoraggi come distribuzioni di probabilità nello spazio GPS. Come tale, un'ancora è ora la regione dello spazio che più probabilmente (cioè, con più del 95% di confidenza) contiene la sua vera posizione GPS sconosciuta.

Lo stesso ragionamento viene applicato quando si esegue una query con GPS. Il dispositivo è rappresentato come un'altra regione di confidenza spaziale intorno alla sua posizione GPS vera e sconosciuta. Scoprire le ancore vicine si traduce in una semplice ricerca delle ancore con regioni di confidenza *abbastanza vicine* alla regione di confidenza del dispositivo, come illustrato nell'immagine seguente:

![Selezione dei candidati di ancoraggio con GPS](media/coarse-reloc-gps-separation-distance.png)

La precisione del segnale GPS, sia sulla creazione di ancoraggio che durante le query, ha una grande influenza sul set di ancoraggi restituiti. Al contrario, le query basate su WiFi / beacon prenderanno in considerazione tutti gli ancoraggi che hanno almeno un punto di accesso / beacon in comune con la query. In questo senso, il risultato di una query basata su WiFi / fari è per lo più determinato dalla gamma fisica dei punti di accesso / fari, e ostacoli ambientali.

La tabella seguente stima lo spazio di ricerca previsto per ogni tipo di sensore:

| Sensore      | Raggio dello spazio di ricerca (approssimativo) | Dettagli |
|-------------|:-------:|---------|
| Gps         | 20 m - 30 m | Determinato dall'incertezza GPS tra gli altri fattori. I numeri riportati sono stimati per la precisione GPS mediana dei telefoni cellulari con A-GPS, cioè 7 metri. |
| WiFi        | 50 m - 100 m | Determinato dalla gamma dei punti di accesso wireless. Dipende dalla frequenza, dalla forza del trasmettitore, dagli ostacoli fisici, dalle interferenze e così via. |
| Fari BLE |  70 m | Determinato dalla gamma del faro. Dipende dalla frequenza, dalla forza di trasmissione, dagli ostacoli fisici, dalle interferenze e così via. |

## <a name="per-platform-support"></a>Supporto per piattaforma

La tabella seguente riepiloga i dati dei sensori raccolti su ciascuna delle piattaforme supportate, insieme a tutte le avvertenze specifiche della piattaforma:


|             | HoloLens | Android | iOS |
|-------------|----------|---------|-----|
| Gps         | N/D | Supportato tramite le API [LocationManager][3] (sia GPS che NETWORK) | Supportato tramite le API [CLLocationManagerSupported through CLLocationManager][4] APIs |
| WiFi        | Supportato a una velocità di circa una scansione ogni 3 secondi | Supportato. A partire dal livello API 28, le scansioni WiFi vengono limitate a 4 chiamate ogni 2 minuti. Da Android 10, la limitazione delle richieste può essere disabilitata dal menu Impostazioni sviluppatore. Per ulteriori informazioni, vedere la [documentazione di Android][5]. | N/D - nessuna API pubblica |
| Fari BLE | Limitato a [Eddystone][1] e [iBeacon][2] | Limitato a [Eddystone][1] e [iBeacon][2] | Limitato a [Eddystone][1] e [iBeacon][2] |

## <a name="next-steps"></a>Passaggi successivi

Usa la rilocalizzazione grossolana in un'app.

> [!div class="nextstepaction"]
> [Unità](../how-tos/set-up-coarse-reloc-unity.md)

> [!div class="nextstepaction"]
> [Objective-C](../how-tos/set-up-coarse-reloc-objc.md)

> [!div class="nextstepaction"]
> [Repentino](../how-tos/set-up-coarse-reloc-swift.md)

> [!div class="nextstepaction"]
> [Java](../how-tos/set-up-coarse-reloc-java.md)

> [!div class="nextstepaction"]
> [C++/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md)

> [!div class="nextstepaction"]
> [C++/WinRT](../how-tos/set-up-coarse-reloc-cpp-winrt.md)

<!-- Reference links in article -->
[1]: https://developers.google.com/beacons/eddystone
[2]: https://developer.apple.com/ibeacon/
[3]: https://developer.android.com/reference/android/location/LocationManager
[4]: https://developer.apple.com/documentation/corelocation/cllocationmanager?language=objc
[5]: https://developer.android.com/guide/topics/connectivity/wifi-scan
[6]: https://www.cambridge.org/core/journals/journal-of-navigation/article/positional-accuracy-of-assisted-gps-data-from-highsensitivity-gpsenabled-mobile-phones/E1EE20CD1A301C537BEE8EC66766B0A9
