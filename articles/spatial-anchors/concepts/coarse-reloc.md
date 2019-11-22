---
title: Rilocalizzazione grossolana
description: Guida introduttiva alla rilocalizzazione grossolana.
author: bucurb
manager: dacoghl
services: azure-spatial-anchors
ms.author: bobuc
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: f03d2fba01dadc443da19416871a93a72289c0c6
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74270157"
---
# <a name="coarse-relocalization"></a>Rilocalizzazione grossolana

La rilocalizzazione grossolana è una funzionalità che fornisce una risposta iniziale alla domanda: *dove si trova il dispositivo ora/quale contenuto è opportuno osservare?* La risposta non è precisa, ma è invece nel formato: *si è vicini a questi ancoraggi, provare a individuarne uno*.

La rilocalizzazione grossolana funziona associando varie letture del sensore sul dispositivo con la creazione e l'esecuzione di query degli ancoraggi. Per gli scenari esterni, i dati del sensore sono in genere la posizione GPS (Global Positioning System) del dispositivo. Quando il GPS non è disponibile o non è affidabile (ad esempio, in ingresso), i dati del sensore sono costituiti dai punti di accesso Wi-Fi e dai beacon Bluetooth nell'intervallo. Tutti i dati dei sensori raccolti contribuiscono alla gestione di un indice spaziale. L'indice spaziale viene usato dal servizio di ancoraggio per determinare rapidamente gli ancoraggi che rientrano approssimativamente a 100 metri del dispositivo.

La ricerca rapida di ancoraggi abilitati dalla rilocalizzazione grossolana semplifica lo sviluppo di applicazioni supportate da raccolte su scala mondiale di (ad indicare milioni di ancoraggi con distribuzione geografica). La complessità della gestione degli ancoraggi è completamente nascosta, consentendo di concentrarsi maggiormente sulla logica dell'applicazione. Tutto il lifting di ancoraggio viene eseguito automaticamente dietro le quinte del servizio.

## <a name="collected-sensor-data"></a>Dati dei sensori raccolti

I dati del sensore che è possibile inviare al servizio di ancoraggio sono uno dei seguenti:

* Posizione GPS: Latitudine, Longitudine, altitudine.
* Potenza del segnale dei punti di accesso Wi-Fi nell'intervallo.
* Livello di attendibilità dei segnali Bluetooth nell'intervallo.

In generale, l'applicazione dovrà acquisire autorizzazioni specifiche del dispositivo per accedere ai dati GPS, Wi-Fi o BLE. Inoltre, alcuni dei dati dei sensori precedenti non sono disponibili in base alla progettazione su determinate piattaforme. Per tenere conto di queste situazioni, la raccolta di dati del sensore è facoltativa ed è disattivata per impostazione predefinita.

## <a name="set-up-the-sensor-data-collection"></a>Configurare la raccolta di dati del sensore

Si inizierà con la creazione di un provider di impronte digitali del sensore e la sessione ne sarà a conoscenza:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
// Create the sensor fingerprint provider
sensorProvider = new PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = new CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.LocationProvider = sensorProvider;
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
// Create the sensor fingerprint provider
ASAPlatformLocationProvider *sensorProvider;
sensorProvider = [[ASAPlatformLocationProvider alloc] init];

// Create and configure the session
cloudSpatialAnchorSession = [[ASACloudSpatialAnchorSession alloc] init];

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.locationProvider = sensorProvider;
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
// Create the sensor fingerprint provider
var sensorProvider: ASAPlatformLocationProvider?
sensorProvider = ASAPlatformLocationProvider()

// Create and configure the session
cloudSpatialAnchorSession = ASACloudSpatialAnchorSession()

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession!.locationProvider = sensorProvider
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = new PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = new CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.setLocationProvider(sensorProvider);
```

# <a name="c-ndktabcpp"></a>[C++ NDK](#tab/cpp)

```cpp
// Create the sensor fingerprint provider
std::shared_ptr<PlatformLocationProvider> sensorProvider;
sensorProvider = std::make_shared<PlatformLocationProvider>();

// Create and configure the session
cloudSpatialAnchorSession = std::make_shared<CloudSpatialAnchorSession>();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession->LocationProvider(sensorProvider);
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)
```cpp
// Create the ASA factory
SpatialAnchorsFactory m_asaFactory { nullptr };
// . . .

// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider;
sensorProvider = m_asaFactory.CreatePlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.LocationProvider(sensorProvider);
```
---

Successivamente, è necessario decidere quali sensori si vuole usare per la rilocalizzazione approssimativa. Questa decisione, in generale, è specifica dell'applicazione che si sta sviluppando, ma i consigli indicati nella tabella seguente dovrebbero fornire un valido punto di partenza:


|             | Interni | Autunno |
|-------------|---------|----------|
| GPS         | Off | Attivato |
| WiFi        | Attivato | On (facoltativo) |
| Beacon BLE | On (facoltativo con avvertenze, vedere più avanti) | Off |


### <a name="enabling-gps"></a>Abilitazione di GPS

Supponendo che l'applicazione disponga già dell'autorizzazione per accedere alla posizione GPS del dispositivo, è possibile configurare gli ancoraggi spaziali di Azure per usarla:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.GeoLocationEnabled = true;
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.geoLocationEnabled = true;
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.geoLocationEnabled = true
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setGeoLocationEnabled(true);
```

# <a name="c-ndktabcpp"></a>[C++ NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->GeoLocationEnabled(true);
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.GeoLocationEnabled(true);
```

---

Quando si usa il GPS nell'applicazione, tenere presente che le letture fornite dall'hardware sono in genere:

* asincrona e bassa frequenza (minore di 1 Hz).
* non affidabile/rumoroso (in media deviazione standard 7-m).

In generale, il sistema operativo del dispositivo e gli ancoraggi spaziali di Azure eseguiranno alcune operazioni di filtro e estrapolazione sul segnale GPS non elaborato nel tentativo di attenuare questi problemi. Questa elaborazione aggiuntiva richiede ulteriore tempo per la convergenza, quindi per ottenere risultati ottimali è consigliabile:

* creare il provider di impronte digitali del sensore il prima possibile nell'applicazione
* Mantieni attivo il provider di impronte digitali del sensore e Condividi tra più sessioni

Se si prevede di usare il provider di impronte digitali del sensore al di fuori di una sessione di ancoraggio, assicurarsi di avviarlo prima di richiedere le stime dei sensori. Ad esempio, il codice seguente si occuperà di aggiornare la posizione del dispositivo sulla mappa in tempo reale:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

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

# <a name="swifttabswift"></a>[Swift](#tab/swift)

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

# <a name="javatabjava"></a>[Java](#tab/java)

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

# <a name="c-ndktabcpp"></a>[C++ NDK](#tab/cpp)

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

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

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

### <a name="enabling-wifi"></a>Abilitazione di Wi-Fi

Supponendo che l'applicazione disponga già dell'autorizzazione per accedere allo stato Wi-Fi del dispositivo, è possibile configurare gli ancoraggi spaziali di Azure per l'uso:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.WifiEnabled = true;
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.wifiEnabled = true;
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.wifiEnabled = true
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setWifiEnabled(true);
```

# <a name="c-ndktabcpp"></a>[C++ NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->WifiEnabled(true);
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.WifiEnabled(true);
```

---

Quando si usa il Wi-Fi nell'applicazione, tenere presente che le letture fornite dall'hardware sono in genere:

* asincrona e bassa frequenza (inferiore a 0,1 Hz).
* potenzialmente limitato a livello di sistema operativo.
* non affidabile/rumoroso (in media, la deviazione standard 3-dBm).

Gli ancoraggi spaziali di Azure tenterà di creare una mappa del livello di attendibilità del segnale Wi-Fi filtrato durante una sessione nel tentativo di attenuare questi problemi. Per ottenere risultati ottimali, provare a:

* creare correttamente la sessione prima di posizionare il primo ancoraggio.
* Mantieni la sessione attiva per il periodo di tempo più lungo possibile, ovvero crea tutti gli ancoraggi e le query in una sessione.

### <a name="enabling-bluetooth-beacons"></a>Abilitazione di Beacon Bluetooth

Supponendo che l'applicazione disponga già dell'autorizzazione per accedere allo stato Bluetooth del dispositivo, è possibile configurare gli ancoraggi spaziali di Azure per usarla:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.BluetoothEnabled = true;
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.bluetoothEnabled = true;
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.bluetoothEnabled = true
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setBluetoothEnabled(true);
```

# <a name="c-ndktabcpp"></a>[C++ NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->BluetoothEnabled(true);
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors();
sensors.BluetoothEnabled(true);
```

---

I beacon sono in genere dispositivi versatili, in cui è possibile configurare tutti gli elementi, inclusi UUID e indirizzi MAC. Questa flessibilità può essere problematica per gli ancoraggi spaziali di Azure che considera i beacon identificati in modo univoco dai rispettivi UUID. Il mancato assicurando che questa unicità venga convertita più probabilmente nei wormhole spaziali. Per ottenere risultati ottimali, è necessario:

* assegnare UUID univoci ai beacon.
* distribuirli, in genere in un modello regolare, ad esempio una griglia.
* passare l'elenco degli UUID del Beacon univoco al provider di impronte digitali del sensore:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.KnownBeaconProximityUuids = new[]
{
    "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1",
    "a63819b9-8b7b-436d-88ec-ea5d8db2acb0",
    . . .
};
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
NSArray *uuids = @[@"22e38f1a-c1b3-452b-b5ce-fdb0f39535c1", @"a63819b9-8b7b-436d-88ec-ea5d8db2acb0"];

ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.knownBeaconProximityUuids = uuids;
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
let uuids = [String]()
uuids.append("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1")
uuids.append("a63819b9-8b7b-436d-88ec-ea5d8db2acb0")

let sensors = locationProvider?.sensors
sensors.knownBeaconProximityUuids = uuids
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
String uuids[] = new String[2];
uuids[0] = "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1";
uuids[1] = "a63819b9-8b7b-436d-88ec-ea5d8db2acb0";

SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setKnownBeaconProximityUuids(uuids);
```

# <a name="c-ndktabcpp"></a>[C++ NDK](#tab/cpp)

```cpp
std::vector<std::string> uuids;
uuids.push_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.push_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->KnownBeaconProximityUuids(uuids);
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

```cpp
std::vector<winrt::hstring> uuids;
uuids.emplace_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.emplace_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

SensorCapabilities sensors = sensorProvider.Sensors();
sensors.KnownBeaconProximityUuids(uuids);
```

---

Gli ancoraggi spaziali di Azure rileveranno solo i beacon Bluetooth presenti nell'elenco. I beacon dannosi programmati per avere UUID con elenco bianco possono comunque influire negativamente sulla qualità del servizio. Per questo motivo, è consigliabile usare Beacon solo negli spazi curati in cui è possibile controllare la distribuzione.

## <a name="querying-with-sensor-data"></a>Esecuzione di query con i dati del sensore

Dopo aver creato gli ancoraggi con i dati del sensore associati, è possibile iniziare a recuperarli usando le letture del sensore segnalate dal dispositivo. Non è più necessario fornire al servizio un elenco di ancoraggi noti che si prevede di trovare. al contrario, è sufficiente lasciare che il servizio conosca la posizione del dispositivo come segnalato dai sensori di onboarding. Il servizio di ancoraggio spaziale rileverà quindi il set di ancoraggi vicino al dispositivo e tenterà di trovare una corrispondenza visiva.

Per fare in modo che le query usino i dati del sensore, iniziare creando un criterio di individuazione:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.DistanceInMeters = 5;

// Cap the number of anchors returned
nearDeviceCriteria.MaxResultCount = 25;

anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.NearDevice = nearDeviceCriteria;
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
ASANearDeviceCriteria *nearDeviceCriteria = [[ASANearDeviceCriteria alloc] init];

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.distanceInMeters = 5.0f;

// Cap the number of anchors returned
nearDeviceCriteria.maxResultCount = 25;

ASAAnchorLocateCriteria *anchorLocateCriteria = [[ASAAnchorLocateCriteria alloc] init];
anchorLocateCriteria.nearDevice = nearDeviceCriteria;
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
let nearDeviceCriteria = ASANearDeviceCriteria()!

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.distanceInMeters = 5.0

// Cap the number of anchors returned
nearDeviceCriteria.maxResultCount = 25

let anchorLocateCriteria = ASAAnchorLocateCriteria()!
anchorLocateCriteria.nearDevice = nearDeviceCriteria
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.setDistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria.setMaxResultCount(25);

AnchorLocateCriteria anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.setNearDevice(nearDeviceCriteria);
```

# <a name="c-ndktabcpp"></a>[C++ NDK](#tab/cpp)

```cpp
auto nearDeviceCriteria = std::make_shared<NearDeviceCriteria>();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria->DistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria->MaxResultCount(25);

auto anchorLocateCriteria = std::make_shared<AnchorLocateCriteria>();
anchorLocateCriteria->NearDevice(nearDeviceCriteria);
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

```cpp
NearDeviceCriteria nearDeviceCriteria = m_asaFactory.CreateNearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.DistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria.MaxResultCount(25);

// Set the session's locate criteria
anchorLocateCriteria = m_asaFactory.CreateAnchorLocateCriteria();
anchorLocateCriteria.NearDevice(nearDeviceCriteria);
```

---

Il parametro `DistanceInMeters` controlla il punto in cui si esplorerà il grafico di ancoraggio per recuperare il contenuto. Si supponga, ad esempio, di avere popolato uno spazio con ancoraggi a densità costante pari a 2 ogni contatore. Inoltre, la fotocamera del dispositivo sta osservando un singolo ancoraggio e il servizio lo ha individuato correttamente. È molto probabile che si sia interessati a recuperare tutti gli ancoraggi posizionati nelle vicinanze, anziché il singolo ancoraggio attualmente in osservazione. Supponendo che gli ancoraggi posizionati siano connessi in un grafico, il servizio può recuperare tutti gli ancoraggi adiacenti per l'utente seguendo i bordi nel grafico. La quantità di attraversamento del grafo eseguita è controllata dal `DistanceInMeters`; verranno assegnati tutti gli ancoraggi connessi a quello che si trova, più vicino a `DistanceInMeters`.

Tenere presente che i valori di grandi dimensioni per `MaxResultCount` potrebbero influire negativamente sulle prestazioni. Provare a impostarla su un valore ragionevole che abbia senso per l'applicazione.

Infine, sarà necessario indicare alla sessione di utilizzare la ricerca basata su sensori:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
[cloudSpatialAnchorSession createWatcher:anchorLocateCriteria];
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
cloudSpatialAnchorSession!.createWatcher(anchorLocateCriteria)
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
cloudSpatialAnchorSession.createWatcher(anchorLocateCriteria);
```

# <a name="c-ndktabcpp"></a>[C++ NDK](#tab/cpp)

```cpp
cloudSpatialAnchorSession->CreateWatcher(anchorLocateCriteria);
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

```cpp
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

---

## <a name="expected-results"></a>Risultati previsti

I dispositivi GPS di livello consumer sono in genere piuttosto imprecisi. Uno studio di [Zandenbergen e Barbeau (2011)][6] segnala l'accuratezza mediana dei telefoni cellulari con il GPS assistito (a-GPS) a circa 7 metri, un valore piuttosto elevato da ignorare. Per tenere conto di questi errori di misurazione, il servizio considera gli ancoraggi come distribuzioni di probabilità nello spazio GPS. Di conseguenza, un ancoraggio è ora l'area di spazio che più probabilmente, ovvero con una confidenza superiore al 95%, contiene la relativa posizione GPS vera e sconosciuta.

Lo stesso ragionamento viene applicato quando si esegue una query con il GPS. Il dispositivo è rappresentato come un'altra area di confidenza spaziale attorno alla sua vera e propria posizione GPS sconosciuta. L'individuazione degli ancoraggi vicini si traduce nella semplice ricerca di ancoraggi con aree di confidenza *sufficientemente vicine* all'area di confidenza del dispositivo, come illustrato nell'immagine seguente:

![Selezione di candidati di ancoraggio con GPS](media/coarse-reloc-gps-separation-distance.png)

L'accuratezza del segnale GPS, sia nella creazione di ancoraggio che durante le query, ha un'influenza notevole sul set di ancoraggi restituiti. Al contrario, le query basate su Wi-Fi/Beacon considereranno tutti gli ancoraggi con almeno un punto di accesso/Beacon in comune con la query. In questo senso, il risultato di una query basata su Wi-Fi/Beacon è determinato principalmente dall'intervallo fisico dei punti di accesso/Beacon e dalle ostruzioni ambientali.

Nella tabella seguente viene stimato lo spazio di ricerca previsto per ogni tipo di sensore:

| Sensore      | Raggio dello spazio di ricerca (circa) | Dettagli |
|-------------|:-------:|---------|
| GPS         | 20 m-30 m | Determinato dall'incertezza GPS tra gli altri fattori. I numeri segnalati sono stimati per l'accuratezza GPS mediana dei telefoni cellulari con A-GPS, ovvero 7 contatori. |
| WiFi        | 50 m-100 m | Determinato dall'intervallo dei punti di accesso wireless. Dipende dalla frequenza, dalla potenza del trasmettitore, dalle ostruzioni fisiche, dall'interferenza e così via. |
| Beacon BLE |  70 m | Determinato dall'intervallo del Beacon. Dipende dalla frequenza, dalla potenza di trasmissione, dagli ostacoli fisici, dall'interferenza e così via. |

## <a name="per-platform-support"></a>Supporto per piattaforma

La tabella seguente riepiloga i dati dei sensori raccolti in ognuna delle piattaforme supportate, insieme a eventuali avvertenze specifiche della piattaforma:


|             | HoloLens | Android | iOS |
|-------------|----------|---------|-----|
| GPS         | N/D | Supportato tramite le API [LocationManager][3] (GPS e rete) | Supportato tramite le API di [CLLocationManager][4] |
| WiFi        | Supportato con una frequenza di circa un'analisi ogni 3 secondi | Supportato. A partire dal livello API 28, le analisi Wi-Fi sono limitate a 4 chiamate ogni 2 minuti. Da Android 10, la limitazione può essere disabilitata dal menu impostazioni sviluppatore. Per ulteriori informazioni, vedere la [documentazione di Android][5]. | N/A-nessuna API pubblica |
| Beacon BLE | Limitato a [Eddystone][1] e [Impedisci iBeacon][2] | Limitato a [Eddystone][1] e [Impedisci iBeacon][2] | Limitato a [Eddystone][1] e [Impedisci iBeacon][2] |

## <a name="next-steps"></a>Passaggi successivi

Usare la rilocalizzazione grossolana in un'app.

> [!div class="nextstepaction"]
> [Unity](../how-tos/set-up-coarse-reloc-unity.md)

> [!div class="nextstepaction"]
> [Objective-C](../how-tos/set-up-coarse-reloc-objc.md)

> [!div class="nextstepaction"]
> [Swift](../how-tos/set-up-coarse-reloc-swift.md)

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