---
title: Eseguire la migrazione di un'app Android | Microsoft Docs
description: Esercitazione su come eseguire la migrazione di un'app Android da Google Maps a mappe Microsoft Azure.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 60d8fcc9879e89276aad80bbaf3a0edf244a45b8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75481399"
---
# <a name="migrate-an-android-app-from-google-maps"></a>Eseguire la migrazione di un'app Android da Google Maps

Azure Maps Android SDK dispone di un'interfaccia API molto simile a Web SDK. Se si è sviluppato con uno di questi SDK, si applicano molti degli stessi concetti, procedure consigliate e architetture e si dovrebbe essere in grado di trasferire facilmente le proprie conoscenze da una all'altra.

Azure Maps Android SDK supporta una versione minima di Android dell'API 21: Android 5.0.0 (Lollipop).

Tutti gli esempi forniti in Java, tuttavia Kotlin possono essere usati anche con le mappe di Azure Android SDK.

Per altre informazioni sullo sviluppo con questo SDK, vedere anche le [guide alle procedure per le mappe di Azure Android SDK](how-to-use-android-map-control-library.md) .

## <a name="load-a-map"></a>Caricare una mappa

Il caricamento di una mappa in un'app Android con Google o Azure Maps è costituito da molti degli stessi passaggi. Quando si usa uno degli SDK, è necessario:

- Ottenere un'API o una chiave di sottoscrizione per accedere a una delle piattaforme.
- Aggiungere codice XML a un'attività per specificare la posizione in cui deve essere eseguito il rendering della mappa e il modo in cui deve essere disposta.
- Consente di eseguire il provisioning di tutti i metodi del ciclo di vita dall'attività contenente la vista mappa a quelli corrispondenti nella classe map. In particolare, è necessario procedere con i metodi seguenti:
    - `onCreate(Bundle)`
    - `onStart()`
    - `onResume()`
    - `onPause()`
    - `onStop()`
    - `onDestroy()`
    - `onSaveInstanceState(Bundle)`
    - `onLowMemory()`
- Attendere che la mappa sia pronta prima di tentare di accedervi a livello di codice.

**Prima: Google Maps**

Per visualizzare una mappa con Google Maps SDK per Android, è necessario eseguire le operazioni seguenti:

1.  Verificare che Google Play servizi sia installato.
2.  Aggiungere una dipendenza per il servizio Google Maps al file **Gradle. Build** del modulo: 

    `implementation 'com.google.android.gms:play-services-maps:17.0.0'`

1.  Aggiungere una chiave API di Google Maps nella sezione dell'applicazione del file di **google\_maps\_API. XML** :
    
    ```xml
    <meta-data android:name="com.google.android.geo.API_KEY" android:value="YOUR_GOOGLE_MAPS_KEY"/>
    ```

1.  Aggiungere un frammento di mappa all'attività principale:

    ```xml
    <com.google.android.gms.maps.MapView
            android:id="@+id/myMap"
            android:layout_width="match_parent"
            android:layout_height="match_parent"/>
    ```

1.  Nel file **MainActivity. Java** è necessario aggiungere le importazioni per Google Maps SDK. Consente di eseguire il provisioning di tutti i metodi del ciclo di vita dall'attività contenente la vista mappa a quelli corrispondenti nella classe map. È possibile recuperare un'istanza di `MapView` dal frammento della mappa utilizzando il metodo `getMapAsync(OnMapReadyCallback)`. Il `MapView` Inizializza automaticamente il sistema Maps e la vista. Modificare il file **MainActivity. Java** come indicato di seguito:

    ```java
    import com.google.android.gms.maps.GoogleMap;
    import com.google.android.gms.maps.MapView;
    import com.google.android.gms.maps.OnMapReadyCallback;
    
    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    
    public class MainActivity extends AppCompatActivity implements OnMapReadyCallback {
    
        MapView mapView;
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mapView = findViewById(R.id.myMap);
    
            mapView.onCreate(savedInstanceState);
            mapView.getMapAsync(this);
        }
    
        @Override
    
        public void onMapReady(GoogleMap map) {
            //Add your post map load code here.
        }
    
        @Override
        public void onResume() {
            super.onResume();
            mapView.onResume();
        }
    
        @Override
        protected void onStart(){
            super.onStart();
            mapView.onStart();
        }
    
        @Override
        public void onPause() {
            super.onPause();
            mapView.onPause();
        }
    
        @Override
        public void onStop() {
            super.onStop();
            mapView.onStop();
        }
    
        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapView.onLowMemory();
        }
    
        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapView.onDestroy();
        }
    
        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapView.onSaveInstanceState(outState);
        }
    }
    ```

Quando viene eseguito in un'applicazione, il controllo mappa viene caricato come indicato di seguito.

<center>

![semplici](media/migrate-google-maps-android-app/simple-google-maps.png)</center> Google Maps

**Dopo: mappe di Azure**

Per visualizzare una mappa con Azure Maps SDK per Android, è necessario eseguire i passaggi seguenti:

1. Aprire il file **Build. Gradle** di primo livello e aggiungere il codice seguente alla sezione **all projects**, **repository** Block:

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Aggiornare l' **app/Build. Gradle** e aggiungere il codice seguente:
    
    1. Verificare che il **minSdkVersion** del progetto si trovi nell'API 21 o versione successiva.

    2. Aggiungere il codice seguente alla sezione Android:

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. Aggiornare il blocco delle dipendenze e aggiungere una nuova linea di dipendenza di implementazione per la versione più recente di Azure Maps Android SDK:

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```

        > [!Note]
        > Il Android SDK mappe di Azure viene regolarmente aggiornato e migliorato. Per ottenere il numero di versione più recente dell'implementazione di mappe di Azure, è possibile vedere la documentazione introduttiva [di controllo mappa di Android](how-to-use-android-map-control-library.md) . Inoltre, è possibile impostare il numero di versione da "0,2" a "0 +" per fare in modo che punti sempre alla versione più recente.
    
    4. Passare a **file** sulla barra degli strumenti e quindi fare clic su **Sincronizza progetto con file Gradle**.
3. Aggiungere un frammento di mappa all'attività principale (res \> layout \> attività\_Main. Xml):
    
    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <FrameLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        >

        <com.microsoft.azure.maps.mapcontrol.MapControl
            android:id="@+id/mapcontrol"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            />
    </FrameLayout>
    ```

4. Nel file **MainActivity. Java** è necessario:
    
    * aggiungere le importazioni per Azure Maps SDK
    * impostare le informazioni di autenticazione di Azure Maps
    * ottenere l'istanza del controllo Map nel metodo **OnCreate**

    L'impostazione delle informazioni di autenticazione nella classe `AzureMaps` a livello globale mediante i metodi `setSubscriptionKey` o `setAadProperties` lo rende necessario, pertanto non sarà necessario aggiungere le informazioni di autenticazione a ogni visualizzazione. 

    Il controllo mappa contiene i propri metodi del ciclo di vita per la gestione del ciclo di vita OpenGL di Android, che deve essere chiamato direttamente dall'attività contenitore. Affinché l'applicazione venga eseguita correttamente, chiamare i metodi del ciclo di vita del controllo mappa, è necessario eseguire l'override dei seguenti metodi del ciclo di vita nell'attività che contiene il controllo mappa e chiamare il rispettivo metodo di controllo map. 

    * `onCreate(Bundle)` 
    * `onStart()` 
    * `onResume()` 
    * `onPause()` 
    * `onStop()` 
    * `onDestroy()` 
    * `onSaveInstanceState(Bundle)` 
    * `onLowMemory()`

    Modificare il file **MainActivity. Java** come indicato di seguito:
    
    ```java
    package com.example.myapplication;

    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.options.MapStyle;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;

    public class MainActivity extends AppCompatActivity {
        
        static {
            AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
        }

        MapControl mapControl;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            mapControl = findViewById(R.id.mapcontrol);

            mapControl.onCreate(savedInstanceState);
    
            //Wait until the map resources are ready.
            mapControl.onReady(map -> {
                //Add your post map load code here.
    
            });
        }

        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
        }

        @Override
        protected void onStart(){
            super.onStart();
            mapControl.onStart();
        }

        @Override
        public void onPause() {
            super.onPause();
            mapControl.onPause();
        }

        @Override
        public void onStop() {
            super.onStop();
            mapControl.onStop();
        }

        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapControl.onLowMemory();
        }

        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapControl.onDestroy();
        }

        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapControl.onSaveInstanceState(outState);
        }
    }
    ```

Se si esegue l'applicazione, il controllo mappa viene caricato come indicato di seguito.

<center>

![semplici mappe di Azure](media/migrate-google-maps-android-app/simple-azure-maps.png)</center>

Si noti che il controllo mappe di Azure supporta lo zoom avanti e fornisce un maggior numero di visualizzazioni internazionali.

> [!TIP]
> Se si usa un emulatore Android in Windows, non è possibile eseguire il rendering della mappa a causa di conflitti con il rendering delle immagini OpenGL e software Accelerated. Per risolvere il problema, è stato usato il codice seguente. Aprire AVD Manager e selezionare il dispositivo virtuale da modificare. Nella sezione **prestazioni emulata** , impostare l'opzione **grafica** su **hardware**.

## <a name="localizing-the-map"></a>Localizzazione della mappa

Se i destinatari sono distribuiti in più paesi o parlano lingue diverse, la localizzazione è importante.

**Prima: Google Maps**

Il linguaggio della mappa può essere impostato nel metodo `onCreate` dell'attività principale aggiungendo il codice seguente prima di impostare la visualizzazione del contesto della mappa. Il codice seguente limita la lingua al francese usando il codice lingua "fr".

```java
String languageToLoad = "fr";
Locale locale = new Locale(languageToLoad);
Locale.setDefault(locale);
Configuration config = new Configuration();
config.locale = locale;
getBaseContext().getResources().updateConfiguration(config,
        getBaseContext().getResources().getDisplayMetrics());
```

Di seguito è riportato un esempio di Google Maps con la lingua impostata su "fr".

<center>

![](media/migrate-google-maps-android-app/google-maps-localization.png)</center> di localizzazione di Google Maps

**Dopo: mappe di Azure**

Azure Maps offre tre diverse modalità di impostazione della lingua e della visualizzazione a livello di area della mappa. La prima opzione consiste nel passare le informazioni sulla lingua e sulla visualizzazione regionale nella classe `AzureMaps` usando i metodi statici `setLanguage` e `setView` a livello globale. La lingua predefinita e la visualizzazione a livello di area vengono impostate in tutti i controlli di Azure Maps caricati nell'app. Il codice seguente limita la lingua al francese usando il codice lingua "fr-FR".

```java
static {
    //Set your Azure Maps Key.
    AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

    //Set the language to be used by Azure Maps.
    AzureMaps.setLanguage("fr-FR");

    //Set the regional view to be used by Azure Maps.
    AzureMaps.setView("auto");
}
```

La seconda opzione consiste nel passare la lingua e visualizzare le informazioni nel codice XML del controllo mappa.

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

La terza opzione consiste nel impostare a livello di codice la lingua e la visualizzazione regionale della mappa utilizzando il metodo Maps `setStyle`. Questa operazione può essere eseguita in qualsiasi momento per modificare la lingua e la visualizzazione a livello di area della mappa.

```java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

Di seguito è riportato un esempio di mappe di Azure con la lingua impostata su "fr-FR".

<center>

![di localizzazione di Azure Maps](media/migrate-google-maps-android-app/azure-maps-localization.png)</center>

Un elenco completo delle lingue supportate e delle viste internazionali è documentato [qui](supported-languages.md).

## <a name="setting-the-map-view"></a>Impostazione della vista mappa

Le mappe dinamiche in Azure e Google Maps possono essere spostate a livello di codice in nuove posizioni geografiche chiamando i metodi appropriati. Gli esempi seguenti illustrano come fare in modo che la mappa visualizzi le immagini aeree satellite, centrare la mappa su una posizione con coordinate (Latitudine: 35,0272, Longitudine:-111,0225) e impostare il livello di zoom su 15 in Google Maps.

> [!NOTE]
> Google Maps usa riquadri di dimensioni di 256 pixel mentre Maps di Azure usa un riquadro di 512 pixel di dimensioni maggiori. In questo modo si riduce il numero di richieste di rete richieste da mappe di Azure per caricare la stessa area mappa di Google Maps. Tuttavia, a causa del modo in cui le piramidi dei riquadri funzionano nei controlli mappa, i riquadri più grandi in Maps di Azure consentono di ottenere la stessa area visualizzabile di una mappa in Google Maps. è necessario sottrarre il livello di zoom usato in Google Maps per 1 quando si usa mappe di Azure.

**Prima: Google Maps**

La fotocamera del controllo mappa di Google Maps può essere spostata a livello di codice usando il metodo `moveCamera`, che consente di specificare il centro della mappa e un livello di zoom. Il metodo `setMapType` può essere utilizzato per modificare il tipo di mappa visualizzato.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.moveCamera(CameraUpdateFactory.newLatLngZoom(new LatLng(35.0272, -111.0225), 15));
    mapView.setMapType(GoogleMap.MAP_TYPE_SATELLITE);
}
```

<center>

![visualizzazione set di Google Maps](media/migrate-google-maps-android-app/google-maps-set-view.png)</center>

**Dopo: mappe di Azure**

Come indicato in precedenza, per ottenere la stessa area visualizzabile in mappe di Azure, sottrarre il livello di zoom usato in Google Maps per uno, in questo caso usare un livello di zoom pari a 14.

La vista mappa iniziale può essere impostata negli attributi XML nel controllo mappa.

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_cameraLat="35.0272"
    app:mapcontrol_cameraLng="-111.0225"
    app:mapcontrol_zoom="14"
    app:mapcontrol_style="satellite"
    />
```

La vista mappa può essere aggiornata a livello di codice usando i metodi Maps `setCamera` e `setStyle`.

```java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(35.0272, -111.0225), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![visualizzazione set di mappe di Azure](media/migrate-google-maps-android-app/azure-maps-set-view.png)</center>

**Risorse aggiuntive:**

- [Stili mappa supportati](supported-map-styles.md)

## <a name="adding-a-marker"></a>Aggiunta di un marcatore

Il rendering dei dati del punto viene spesso eseguito sulla mappa utilizzando un'immagine sulla mappa. Queste immagini sono spesso denominate marcatori, puntine da disegno, pin o simboli. Gli esempi seguenti eseguono il rendering dei dati punto come marcatori sulla mappa in corrispondenza di (Latitudine: 51,5, Longitudine:-0,2).

**Prima: Google Maps**

Con Google Maps, i marcatori vengono aggiunti usando il metodo Maps `addMarker`.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33)));
}
```

<center>

![marcatore di Google Maps](media/migrate-google-maps-android-app/google-maps-marker.png)</center>

**Dopo: mappe di Azure**

Nei dati del punto di Azure Maps è possibile eseguire il rendering della mappa aggiungendo prima i dati a un'origine dati e quindi connettendo tale origine dati a un livello di simboli. L'origine dati consente di ottimizzare la gestione dei dati spaziali nel controllo della mappa e il livello dei simboli specifica come eseguire il rendering dei dati del punto utilizzando come immagine e/o testo.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create a point feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Create a symbol layer and add it to the map.
    map.layers.add(new SymbolLayer(dataSource));
});
```

<center>

![marcatore mappe di Azure](media/migrate-google-maps-android-app/azure-maps-marker.png)</center>

## <a name="adding-a-custom-marker"></a>Aggiunta di un marcatore personalizzato

Le immagini personalizzate possono essere usate per rappresentare punti su una mappa. Negli esempi seguenti viene usata l'immagine seguente: usare un'immagine personalizzata per visualizzare un punto sulla mappa in (Latitudine: 51,5, Longitudine:-0,2) e sfalsare la posizione del marcatore in modo che il punto dell'icona a forma di puntina da disegno sia allineato alla posizione corretta sulla mappa.

<center>

![immagine con puntina da disegno giallo](media/migrate-google-maps-web-app/ylw_pushpin.png)<br/>
YLW\_la puntina da disegno. png</center>

In entrambi gli esempi, l'immagine precedente viene aggiunta alla cartella di cui è possibile creare le risorse delle app.

**Prima: Google Maps**

Con Google Maps, le immagini personalizzate possono essere usate per i marcatori caricando tali immagini tramite l'opzione `icon` del marcatore. Per allineare il punto dell'immagine alla coordinata, è possibile usare l'opzione `anchor`. L'ancoraggio è relativo alle dimensioni dell'immagine, in questo caso 0,2 unità di larghezza e 1 unità di altezza.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33))
    .icon(BitmapDescriptorFactory.fromResource(R.drawable.ylw_pushpin))
    .anchor(0.2f, 1f));
}
```

<center>

![marcatore personalizzato di Google Maps](media/migrate-google-maps-android-app/google-maps-custom-marker.png)</center>

**Dopo: mappe di Azure**

I livelli dei simboli nelle mappe di Azure supportano anche immagini personalizzate, ma è necessario caricare prima l'immagine nelle risorse della mappa e assegnare un ID univoco. Il livello dei simboli può quindi fare riferimento a questo ID. Il simbolo può essere offset per essere allineato al punto corretto sull'immagine usando l'opzione `iconOffset`. Si noti che l'offset dell'icona è espresso in pixel. Per impostazione predefinita, l'offset è relativo al centro inferiore dell'immagine, ma può essere modificato usando l'opzione `iconAnchor`. Questo esempio imposta l'opzione `iconAnchor` su `"center"` e usa un offset dell'icona per spostare l'immagine di cinque pixel a destra e 15 pixel per allinearla con il punto dell'immagine della puntina da disegno.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create a point feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Load the custom image icon into the map resources.
    map.images.add("my-yellow-pin", R.drawable.ylw_pushpin);

    //Create a symbol that uses the custom image icon and add it to the map.
    map.layers.add(new SymbolLayer(dataSource,
        iconImage("my-yellow-pin"),
        iconAnchor(AnchorType.CENTER
        iconOffset(new Float[]{5f, -15f})));
});
```

<center>

![di Azure Maps Custom marker](media/migrate-google-maps-android-app/azure-maps-custom-marker.png)</center>

## <a name="adding-a-polyline"></a>Aggiunta di una polilinea

Le polilinee vengono usate per rappresentare una linea o un tracciato sulla mappa. Negli esempi seguenti viene illustrato come creare una polilinea tratteggiata sulla mappa.

**Prima: Google Maps**

Con Google Maps è possibile creare una polilinea usando la classe `PolylineOptions` e aggiunta alla mappa usando il metodo `addPolyline`. Il colore del tratto può essere impostato utilizzando l'opzione `color`, la larghezza del tratto viene impostata utilizzando l'opzione width ed è possibile impostare una matrice Dash tratto utilizzando l'opzione `pattern`.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polyline.
    PolylineOptions lineOptions = new PolylineOptions()
            .add(new LatLng(46, -123))
            .add(new LatLng(49, -122))
            .add(new LatLng(46, -121))
            .color(Color.RED)
            .width(10f)
            .pattern(Arrays.<PatternItem>asList(
                    new Dash(30f), new Gap(30f)));

    //Add the polyline to the map.
    Polyline polyline = mapView.addPolyline(lineOptions);
}
```

<center>

![linea di Google Maps](media/migrate-google-maps-android-app/google-maps-polyline.png)</center>

**Dopo: mappe di Azure**

Nelle mappe di Azure le polilinee sono denominate oggetti LineString o MultiLineString. Questi oggetti possono essere aggiunti a un'origine dati e sottoposti a rendering utilizzando un livello linea. Si noti che le unità "pixel" della larghezza del tratto e della matrice Dash si allineano con Azure Maps Web SDK, in quanto l'uso degli stessi valori in entrambi gli SDK produce gli stessi risultati.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create an array of points.
    List<Point> points = Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46));

    //Create a LineString feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(LineString.fromLngLats(points)));

    //Create a line layer and add it to the map.
    map.layers.add(new LineLayer(dataSource,
        strokeColor("red"),
        strokeWidth(4f),
        strokeDashArray(new Float[]{3f, 3f})));
});
```

<center>

![](media/migrate-google-maps-android-app/azure-maps-polyline.png)</center> polilinea di Azure Maps

## <a name="adding-a-polygon"></a>Aggiunta di un poligono

I poligoni vengono usati per rappresentare un'area sulla mappa. Gli esempi seguenti illustrano come creare un poligono che formi un triangolo basato sulla coordinata centrale della mappa.

**Prima: Google Maps**

Con Google Maps è possibile creare un poligono usando la classe `PolygonOptions` e aggiungerlo alla mappa usando il metodo `addPolygon`. È possibile impostare i colori di riempimento e tratto utilizzando l'opzione `fillColor` e `strokeColor`, la larghezza del tratto viene impostata utilizzando l'opzione `strokeWidth`.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polygon.
    PolygonOptions polygonOptions = new PolygonOptions()
            .add(new LatLng(46, -123))
            .add(new LatLng(49, -122))
            .add(new LatLng(46, -121))
            .add(new LatLng(46, -123))  //Close the polygon.
            .fillColor(Color.argb(128, 0, 128, 0))
            .strokeColor(Color.RED)
            .strokeWidth(5f);

    //Add the polygon to the map.
    Polygon polygon = mapView.addPolygon(polygonOptions);
}
```

<center>

![](media/migrate-google-maps-android-app/google-maps-polygon.png)</center> poligono di Google Maps

**Dopo: mappe di Azure**

Nelle mappe di Azure, gli oggetti Polygon e MultiPolygon possono essere aggiunti a un'origine dati e sottoposti a rendering sulla mappa usando i livelli. L'area di un poligono può essere sottoposta a rendering in un livello poligono. Il rendering del contorno di un poligono può essere eseguito usando un livello linea. Si noti che le unità "pixel" della larghezza del tratto e della matrice Dash si allineano con Azure Maps Web SDK, in quanto l'uso degli stessi valori in entrambi gli SDK produce gli stessi risultati.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create an array of point arrays to create polygon rings.
    List<List<Point>> rings = Arrays.asList(Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46),
        Point.fromLngLat(-123, 46)));

    //Create a Polygon feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Polygon.fromLngLats(rings)));

    //Add a polygon layer for rendering the polygon area.
    map.layers.add(new PolygonLayer(dataSource,
        fillColor("green"),
        fillOpacity(0.5f)));

    //Add a line layer for rendering the polygon outline.
    map.layers.add(new LineLayer(dataSource,
        strokeColor("red"),
        strokeWidth(2f)));
});
```

<center>

![poligono di Azure Maps](media/migrate-google-maps-android-app/azure-maps-polygon.png)</center>

## <a name="overlay-a-tile-layer"></a>Sovrapposizione di un livello sezione

I livelli affiancati, noti anche come sovrimpressioni delle immagini in Google Maps, consentono di sovrapporre le immagini del livello che sono state suddivise in immagini affiancate più piccole allineate al sistema di affiancamento delle mappe. Si tratta di un modo comune per sovrapporre immagini del livello o set di dati di grandi dimensioni.

Gli esempi seguenti sovrappongono un livello sezione del radar meteorologico da Iowa Environmental Mesont of Iowa State University. Le dimensioni dei riquadri sono di 256 pixel.

**Prima: Google Maps**

Con Google Maps, un livello sezione può essere sovrapposto sopra la mappa usare la classe `TileOverlayOptions` e aggiungerla alla mappa usando il metodo `addTileLauer`. Per rendere semi-trasparenti i riquadri, l'opzione `transparency` è impostata su 0,2 o su un 20% trasparente.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the tile layer.
    TileOverlayOptions tileLayer = new TileOverlayOptions()
        .tileProvider(new UrlTileProvider(256, 256) {
            @Override
            public URL getTileUrl(int x, int y, int zoom) {

                try {
                    //Define the URL pattern for the tile images.
                    return new URL(String.format("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/%d/%d/%d.png", zoom, x, y));
                }catch (MalformedURLException e) {
                    throw new AssertionError(e);
                }
            }
        }).transparency(0.2f);

    //Add the tile layer to the map.
    mapView.addTileOverlay(tileLayer);
}
```

<center>

![livello sezione di Google Maps](media/migrate-google-maps-android-app/google-maps-tile-layer.png)</center>

**Dopo: mappe di Azure**

Nelle mappe di Azure è possibile aggiungere un livello sezione alla mappa in modo analogo a qualsiasi altro livello. URL formattato con segnaposto x, y e zoom; `{x}`, `{y}`, `{z}` viene usato rispettivamente per indicare al livello dove accedere ai riquadri. I livelli affiancati in Azure Maps supportano anche `{quadkey}`, `{bbox-epsg-3857}` e `{subdomain}` segnaposto. Per rendere il livello sezione semi-trasparente, viene utilizzato un valore di opacità pari a 0,8. Si noti che l'opacità e la trasparenza, sebbene simili, usano valori invertiti. Per eseguire la conversione, è sufficiente sottrarre il proprio valore dal numero uno.

> [!TIP]
> Nei livelli mappe di Azure è possibile eseguire facilmente il rendering sotto altri livelli, inclusi i livelli mappa di base. Spesso è consigliabile eseguire il rendering dei livelli affiancati sotto le etichette della mappa in modo che siano facili da leggere. Il metodo `map.layers.add` accetta un secondo parametro che è l'ID del livello in cui inserire il nuovo livello sotto. Per inserire un livello sezione sotto le etichette della mappa, è possibile usare il codice seguente: `map.layers.add(myTileLayer, "labels");`

```java
mapControl.onReady(map -> {
    //Add a tile layer to the map, below the map labels.
    map.layers.add(new TileLayer(
        tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
        opacity(0.8f),
        tileSize(256)
    ), "labels");
});
```

<center>

![livello tessera di Azure Maps](media/migrate-google-maps-android-app/azure-maps-tile-layer.png)</center>

## <a name="show-traffic"></a>Mostra il traffico

I dati relativi al traffico possono essere sovrapposti ad Azure e a Google Maps.

**Prima: Google Maps**

Con Google Maps, i dati del flusso di traffico possono essere sovrapposti sopra la mappa passando true al metodo `setTrafficEnabled` della mappa.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.setTrafficEnabled(true);
}
```

<center>

![il traffico di Google Maps](media/migrate-google-maps-android-app/google-maps-traffic.png)</center>

**Dopo: mappe di Azure**

Azure Maps offre diverse opzioni per la visualizzazione del traffico. Gli eventi imprevisti del traffico, come le chiusure e gli incidenti stradali, possono essere visualizzati come icone sulla mappa. Il flusso del traffico, le strade con codifica a colori, possono essere sovrapposti sulla mappa e i colori possono essere modificati in base al limite di velocità inviato, in relazione al ritardo previsto normale o al ritardo assoluto. I dati relativi agli eventi imprevisti nelle mappe di Azure vengono aggiornati ogni minuto e i dati di flusso ogni due minuti.

```java
mapControl.onReady(map -> {
    map.setTraffic(
        incidents(true),
        flow(TrafficFlow.RELATIVE));
});
```

<center>

![Azure Maps Traffic](media/migrate-google-maps-android-app/azure-maps-traffic.png)</center>

## <a name="next-steps"></a>Passaggi successivi

Scopri di più sulle mappe di Azure Android SDK.

> [!div class="nextstepaction"]
> [Come usare il controllo mappa di Android](how-to-use-android-map-control-library.md)
