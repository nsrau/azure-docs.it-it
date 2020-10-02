---
title: "Esercitazione: Eseguire la migrazione di un'app Android | Mappe di Microsoft Azure"
description: Come eseguire la migrazione di un'app Android da Google Maps a Mappe di Microsoft Azure.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: fe67364ef51248d04cbc6095eb691ffe255fa02c
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90085868"
---
# <a name="migrate-an-android-app-from-google-maps"></a>Eseguire la migrazione di un'app Android da Google Maps

Azure Maps Android SDK offre un'interfaccia API molto simile a quella di Web SDK. Se il codice è stato sviluppato con uno di questi SDK, valgono gli stessi concetti, procedure consigliate e architetture

Azure Maps Android SDK supporta una versione Android minima dell'API 21: Android 5.0.0 (Lollipop).

Tutti gli esempi sono forniti in Java, ma è anche possibile usare Kotlin con Azure Maps Android SDK.

Per altre informazioni su come sviluppare con Android SDK di Mappe di Azure, vedere anche le [guide pratiche per Azure Maps Android SDK](how-to-use-android-map-control-library.md).

## <a name="load-a-map"></a>Caricare una mappa

La procedura di caricamento in un'app Android con Google Maps prevede molti passaggi simili a quella di Mappe di Azure. Quando si usa uno degli SDK è necessario:

- Ottenere un'API o una chiave di sottoscrizione per accedere a una delle piattaforme.
- Aggiungere del codice XML a un'attività per specificare la posizione del rendering e la disposizione della mappa.
- Eseguire l'override dei metodi del ciclo di vita dall'attività contenente la visualizzazione della mappa con quelli corrispondenti nella classe map. In particolare, è necessario eseguire l'override dei metodi seguenti:
    - `onCreate(Bundle)`
    - `onStart()`
    - `onResume()`
    - `onPause()`
    - `onStop()`
    - `onDestroy()`
    - `onSaveInstanceState(Bundle)`
    - `onLowMemory()`
- Attendere che la mappa sia pronta prima di provare ad accedervi e a programmarla.

**Prima: Google Maps**

Per visualizzare una mappa con Google Maps SDK per Android, occorre seguire questa procedura:

1.  Verificare che Google Play Services sia installato.
2.  Aggiungere una dipendenza per il servizio Google Maps al file **gradle.build** del modulo: 

    `implementation 'com.google.android.gms:play-services-maps:17.0.0'`

1.  Aggiungere una chiave API di Google Maps all'interno della sezione application del file **google\_maps\_api.xml**:
    
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

1.  Nel file **MainActivity.java** è necessario importare Google Maps SDK. Trasferire tutti i metodi del ciclo di vita dall'attività contenente la visualizzazione della mappa a quelli corrispondenti nella classe della mappa. Recuperare un'istanza di `MapView` dal frammento della mappa usando il metodo `getMapAsync(OnMapReadyCallback)`. `MapView` inizializza automaticamente il sistema delle mappe e la visualizzazione. Modificare il file **MainActivity.java** come segue:

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

Quando si esegue un'applicazione, il controllo mappa viene caricato come illustrato nell'immagine seguente.

<center>

![Mappa semplice di Google Maps](media/migrate-google-maps-android-app/simple-google-maps.png)</center>

**Dopo: Mappe di Azure**

Per visualizzare una mappa con Azure Maps SDK per Android, occorre seguire questa procedura:

1. Aprire il file di primo livello **build.gradle** e aggiungere il codice seguente alla sezione del blocco **all projects**:

    ```JAVA
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Aggiornare **app/build.gradle** e aggiungervi il codice seguente:
    
    1. Verificare che il valore di **minSdkVersion** del progetto sia API 21 o versione successiva.

    2. Aggiungere il codice seguente alla sezione Android:

        ```java
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. Aggiornare il blocco delle dipendenze. Aggiungere una nuova riga di dipendenza implementation per la versione più recente di Azure Maps Android SDK:

        ```java
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```

        > [!Note]
        > Azure Maps Android SDK viene regolarmente aggiornato e migliorato. Vedere [Introduzione al controllo mappa di Android](how-to-use-android-map-control-library.md) per recuperare il numero di versione più recente di Mappe di Azure. È anche possibile impostare il numero di versione da "0.2" a "0+" affinché il codice punti sempre alla versione più recente.
    
    4. Passare a **File** sulla barra degli strumenti e fare clic su **Sync Project with Gradle Files** (Sincronizza progetto con file Gradle).
3. Aggiungere un frammento di mappa all'attività principale (resources \> layout \> activity\_main.xml):
    
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

4. Nel file **MainActivity.java** è necessario:
    
    * Importare Azure Maps SDK
    * Impostare le informazioni di autenticazione di Mappe di Azure
    * Ottenere l'istanza del controllo mappa nel metodo **onCreate**

     Impostare le informazioni di autenticazione nella classe `AzureMaps` usando i metodi `setSubscriptionKey` o `setAadProperties`. Questo aggiornamento globale garantisce che le informazioni di autenticazione vengano aggiunte a ogni visualizzazione.

    Il controllo mappa contiene i propri metodi del ciclo di vita per la gestione del ciclo di vita OpenGL di Android. Questi metodi devono essere chiamati direttamente dall'attività che li contiene. Per chiamare correttamente i metodi del ciclo di vita del controllo mappa, occorre eseguire l'override dei metodi del ciclo di vita seguenti nell'attività che contiene il controllo mappa e chiamare il rispettivo metodo del controllo mappa.

    * `onCreate(Bundle)` 
    * `onStart()` 
    * `onResume()` 
    * `onPause()` 
    * `onStop()` 
    * `onDestroy()` 
    * `onSaveInstanceState(Bundle)` 
    * `onLowMemory()`

    Modificare il file **MainActivity.java** come segue:
    
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

Se si esegue l'applicazione, il controllo mappa viene caricato come illustrato nell'immagine seguente.

<center>

![Mappa semplice di Mappe di Azure](media/migrate-google-maps-android-app/simple-azure-maps.png)</center>

Si noti che il controllo Mappe di Azure supporta maggiormente lo zoom indietro e offre una più ampia visualizzazione del mondo.

> [!TIP]
> Se si usa un emulatore Android in un computer Windows, potrebbe essere impossibile eseguire il rendering della mappa a causa di conflitti con il rendering della grafica OpenGL e con accelerazione software. In alcuni casi, la soluzione seguente ha consentito di risolvere il problema. aprire AVD Manager e selezionare il dispositivo virtuale da modificare. Scorrere verso il basso nel pannello **Verify Configuration** (Verifica configurazione). Nella sezione **Emulated Performance** (Prestazioni emulate) impostare l'opzione **Graphics** (Grafica) su **Hardware**.

## <a name="localizing-the-map"></a>Localizzare la mappa

Se i destinatari sono distribuiti in più paesi/aree geografiche o parlano lingue diverse, la localizzazione è importante.

**Prima: Google Maps**

Aggiungere il codice seguente al metodo `onCreate` per impostare la lingua della mappa prima di impostare la visualizzazione del contesto della mappa. Il codice lingua "fr" limita la lingua al francese.

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

![Localizzazione di Google Maps](media/migrate-google-maps-android-app/google-maps-localization.png)</center>

**Dopo: Mappe di Azure**

Mappe di Azure offre tre modi diversi per impostare la lingua e la visualizzazione a livello di area della mappa. La prima opzione consiste nel passare le informazioni sulla lingua e sulla visualizzazione a livello di area nella classe `AzureMaps`. Questa opzione usa i metodi statici `setLanguage` e `setView` a livello globale. In questo modo la lingua e la visualizzazione a livello di area predefinite vengono impostate in tutti i controlli di Mappe di Azure caricati nell'app. Questo esempio imposta la lingua francese usando il codice lingua "fr-FR".

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

La seconda opzione consiste nel passare le informazioni sulla lingua e sulla visualizzazione nel codice XML del controllo mappa.

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

La terza opzione consiste nell'impostare a livello di codice la lingua e la visualizzazione a livello di area della mappa usando il metodo `setStyle` della mappa. Questa opzione consente di aggiornare la lingua e la visualizzazione a livello di area ogni volta che viene eseguito il codice.

```java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

Di seguito è riportato un esempio di Mappe di Azure con la lingua impostata su "fr-FR".

<center>

![Localizzazione di Mappe di Azure](media/migrate-google-maps-android-app/azure-maps-localization.png)</center>

Vedere l'elenco completo delle [lingue supportate](supported-languages.md).

## <a name="setting-the-map-view"></a>Impostare la visualizzazione mappa

Le mappe dinamiche in Mappe di Azure e Google Maps possono essere spostate a livello di codice in nuove aree geografiche chiamando i metodi appropriati. Ora verrà illustrato come visualizzare le immagini aeree satellitari, centrare la mappa su una posizione con le coordinate e modificare il livello di zoom. In questo esempio verranno usati i valori seguenti: latitudine: 35.0272, longitudine: -111.0225 e livello di zoom 15.

**Prima: Google Maps**

La fotocamera del controllo mappa di Google Maps può essere spostata a livello di codice usando il metodo `moveCamera`. Il metodo `moveCamera` consente di specificare il centro della mappa e un livello di zoom. Il metodo `setMapType` modifica il tipo di mappa da visualizzare.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.moveCamera(CameraUpdateFactory.newLatLngZoom(new LatLng(35.0272, -111.0225), 15));
    mapView.setMapType(GoogleMap.MAP_TYPE_SATELLITE);
}
```

<center>

![Impostazione visualizzazione di Google Maps](media/migrate-google-maps-android-app/google-maps-set-view.png)</center>

> [!NOTE]
> Google Maps usa tessere con dimensioni di 256 pixel, mentre Mappe di Azure usa tessere più grandi, da 512 pixel. In questo modo si riduce il numero di richieste di rete necessarie a Mappe di Azure per caricare la stessa area mappa di Google Maps. Per ottenere la stessa area visualizzabile di una mappa in Google Maps, in Mappe di Azure è necessario ridurre il livello di zoom usato in Google Maps di uno. 

**Dopo: Mappe di Azure**

Come indicato in precedenza, per ottenere la stessa area visualizzabile in Mappe di Azure, occorre ridurre di uno il livello di zoom usato in Google Maps, in questo caso usando un livello di zoom pari a 14.

La visualizzazione della mappa iniziale può essere impostata negli attributi XML del controllo mappa.

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

La visualizzazione della mappa può essere programmata usando i metodi `setCamera` e `setStyle` della mappa.

```java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(35.0272, -111.0225), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![Impostazione visualizzazione di Mappe di Azure](media/migrate-google-maps-android-app/azure-maps-set-view.png)</center>

**Risorse aggiuntive:**

- [Stili di mappa supportati](supported-map-styles.md)

## <a name="adding-a-marker"></a>Aggiungere un marcatore

Il rendering dei dati dei punti viene spesso eseguito tramite un'immagine sulla mappa. In genere queste immagini sono denominate indicatori, puntine da disegno, segnaposto o simboli. Negli esempi seguenti viene eseguito il rendering dei dati dei punti come indicatori sulla mappa alle coordinate seguenti: latitudine: 51.5, longitudine: -0.2.

**Prima: Google Maps**

Con Google Maps i marcatori vengono aggiunti tramite il metodo `addMarker` della mappa.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33)));
}
```

<center>

![Marcatore di Google Maps](media/migrate-google-maps-android-app/google-maps-marker.png)</center>

**Dopo: Mappe di Azure**

In Mappe di Azure è possibile eseguire il rendering dei dati dei punti sulla mappa aggiungendo prima i dati a un'origine dati e quindi connettendo l'origine dati a un livello simbolo. L'origine dati ottimizza la gestione dei dati spaziali nel controllo mappa. Il livello simbolo specifica come eseguire il rendering dei dati dei punti usando un'immagine o un testo.

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

![Marcatore di Mappe di Azure](media/migrate-google-maps-android-app/azure-maps-marker.png)</center>

## <a name="adding-a-custom-marker"></a>Aggiungere un marcatore personalizzato

È possibile usare immagini personalizzate per rappresentare i punti su una mappa. La mappa negli esempi seguenti usa un'immagine personalizzata per visualizzare un punto sulla mappa. Il punto si trova alla latitudine: 51.5 e longitudine: -0.2. L'ancoraggio esegue l'offset della posizione dell'indicatore affinché il punto dell'icona a forma di puntina da disegno sia allineato alla posizione corretta sulla mappa.

<center>

![immagine puntina da disegno gialla](media/migrate-google-maps-web-app/yellow-pushpin.png)<br/>
yellow-pushpin.png</center>

In entrambi gli esempi l'immagine precedente viene aggiunta alla cartella di risorse drawable delle risorse dell'app.

**Prima: Google Maps**

Con Google Maps è possibile usare immagini personalizzate per gli indicatori. Caricare le immagini personalizzate usando l'opzione `icon` dell'indicatore. Per allineare il punto dell'immagine alla coordinata, usare l'opzione `anchor`. L'ancoraggio è relativo alle dimensioni dell'immagine. In questo caso, l'ancoraggio è di 0,2 unità di larghezza e 1 unità di altezza.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33))
    .icon(BitmapDescriptorFactory.fromResource(R.drawable.yellow-pushpin))
    .anchor(0.2f, 1f));
}
```

<center>

![Marcatore personalizzato di Google Maps](media/migrate-google-maps-android-app/google-maps-custom-marker.png)</center>

**Dopo: Mappe di Azure**

I livelli simbolo in Mappe di Azure supportano anche le immagini personalizzate, ma è prima necessario caricare l'immagine nelle risorse della mappa e assegnarle un ID univoco. Il livello simbolo deve quindi fare riferimento a questo ID. Scostare il simbolo affinché sia allineato al punto corretto sull'immagine usando l'opzione `iconOffset`. L'offset dell'icona è espresso in pixel. Per impostazione predefinita, l'offset è relativo alla parte centrale inferiore dell'immagine, ma può essere regolato usando l'opzione `iconAnchor`. Questo esempio imposta l'opzione `iconAnchor` su `"center"` e usa un offset dell'icona per spostare l'immagine di cinque pixel a destra e 15 pixel in alto per allinearla al punto dell'immagine della puntina da disegno.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create a point feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Load the custom image icon into the map resources.
    map.images.add("my-yellow-pin", R.drawable.yellow_pushpin);

    //Create a symbol that uses the custom image icon and add it to the map.
    map.layers.add(new SymbolLayer(dataSource,
        iconImage("my-yellow-pin"),
        iconAnchor(AnchorType.CENTER
        iconOffset(new Float[]{5f, -15f})));
});
```

<center>

![Marcatore personalizzato di Mappe di Azure](media/migrate-google-maps-android-app/azure-maps-custom-marker.png)</center>

## <a name="adding-a-polyline"></a>Aggiungere una polilinea

Le polilinee vengono usate per rappresentare una linea o un percorso sulla mappa. Negli esempi seguenti viene illustrato come creare una polilinea tratteggiata sulla mappa.

**Prima: Google Maps**

Con Google Maps è possibile eseguire il rendering di una polilinea usando la classe `PolylineOptions`. Aggiungere la polilinea alla mappa usando il metodo `addPolyline`. Impostare il colore del tratto usando l'opzione `color`. Impostare lo spessore del tratto usando l'opzione `width`. Aggiungere una matrice tratteggiata del tratto usando l'opzione `pattern`.

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

![Polilinea di Google Maps](media/migrate-google-maps-android-app/google-maps-polyline.png)</center>

**Dopo: Mappe di Azure**

In Mappe di Azure le polilinee sono denominate come oggetti `LineString` o `MultiLineString`. Aggiungere questi oggetti a un'origine dati ed eseguirne il rendering usando un livello linea. Impostare lo spessore del tratto usando l'opzione `strokeWidth`. Aggiungere una matrice tratteggiata del tratto usando l'opzione `strokeDashArray`.

Lo spessore del tratto e le unità in "pixel" della matrice tratteggiata in Azure Maps Web SDK sono equivalenti a quelle del servizio Google Maps. Entrambi accettano gli stessi valori per generare gli stessi risultati.

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

![Polilinea di Mappe di Azure](media/migrate-google-maps-android-app/azure-maps-polyline.png)</center>

## <a name="adding-a-polygon"></a>Aggiungere un poligono

I poligoni vengono usati per rappresentare un'area sulla mappa. Negli esempi successivi viene illustrato come creare un poligono. Questo poligono forma un triangolo basato sulla coordinata centrale della mappa.

**Prima: Google Maps**

Con Google Maps è possibile eseguire il rendering di un poligono usando la classe `PolygonOptions`. Aggiungere il poligono alla mappa usando il metodo `addPolygon`. Impostare i colori di riempimento e del tratto usando rispettivamente le opzioni `fillColor` e `strokeColor`. Impostare lo spessore del tratto usando l'opzione `strokeWidth`.

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

![Poligono di Google Maps](media/migrate-google-maps-android-app/google-maps-polygon.png)</center>

**Dopo: Mappe di Azure**

In Mappe di Azure aggiungere gli oggetti `Polygon` e `MultiPolygon` a un'origine dati ed eseguirne il rendering sulla mappa usando i livelli. Eseguire il rendering dell'area di un poligono usando un livello poligono. Eseguire il rendering del contorno di un poligono usando un livello linea. Impostare il colore e lo spessore del tratto usando le opzioni `strokeColor` e `strokeWidth`.

Lo spessore del tratto e le unità in "pixel" della matrice tratteggiata in Azure Maps Web SDK sono allineati alle rispettive unità in Google Maps. Entrambi accettano gli stessi valori e generano gli stessi risultati.

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

![Poligono di Mappe di Azure](media/migrate-google-maps-android-app/azure-maps-polygon.png)</center>

## <a name="overlay-a-tile-layer"></a>Sovrapporre un livello tessera

 Usare i livelli tessera per sovrapporre immagini del livello che sono state scomposte in immagini a tessere più piccole allineate al sistema a tessere delle mappe. Questo approccio è un modo comune per sovrapporre immagini di livello o set di dati di grandi dimensioni. I livelli tessera sono anche noti anche come Overlay immagine in Google Maps.

Gli esempi seguenti mostrano la sovrapposizione di un livello tessera di radar meteo dall'Iowa Environmental Mesonet dell'Iowa State University. Le dimensioni delle tessere sono di 256 pixel.

**Prima: Google Maps**

Con Google Maps è possibile sovrapporre un livello tessera sulla mappa. Usare la classe `TileOverlayOptions`. Aggiungere il livello tessera alla mappa usando il metodo `addTileLauer`. Per rendere le tessere semi-trasparenti, l'opzione `transparency` è impostata su 0,2 o su una trasparenza del 20%.

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

![Livello tessera di Google Maps](media/migrate-google-maps-android-app/google-maps-tile-layer.png)</center>

**Dopo: Mappe di Azure**

È possibile aggiungere un livello tessera alla mappa con la stessa procedura usata per qualsiasi altro livello. Viene usato un URL formattato con i segnaposto x, y e zoom. `{x}`, `{y}`, `{z}` indicano rispettivamente al livello dove accedere alle tessere. Inoltre, i livelli tessera in Mappe di Azure supportano anche i segnaposto `{quadkey}`, `{bbox-epsg-3857}` e `{subdomain}`. Per rendere semi-trasparente il livello tessera, viene usato un valore di opacità pari a 0,8. L'opacità e la trasparenza, benché simili, usano valori invertiti. Per eseguire la conversione tra entrambe le opzioni, è sufficiente sottrarre il valore dal numero uno.

> [!TIP]
> Mappe di Azure consente di eseguire il rendering dei livelli sotto altri livelli, inclusi i livelli mappa di base. Inoltre, è spesso consigliabile eseguire il rendering dei livelli tessera sotto le etichette della mappa in modo che siano facili da leggere. Il metodo `map.layers.add` accetta un secondo parametro che è l'ID del livello sotto il quale inserire il nuovo livello. Per inserire un livello tessera sotto le etichette della mappa, è possibile usare il codice seguente: `map.layers.add(myTileLayer, "labels");`

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

![Livello tessera di Mappe di Azure](media/migrate-google-maps-android-app/azure-maps-tile-layer.png)</center>

## <a name="show-traffic"></a>Visualizzare il traffico

In Mappe di Azure e in Google Maps sono disponibili opzioni per la sovrapposizione dei dati sul traffico.

**Prima: Google Maps**

Con Google Maps, i dati del flusso di traffico possono essere sovrapposti sopra la mappa passando il valore true al metodo `setTrafficEnabled` della mappa.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.setTrafficEnabled(true);
}
```

<center>

![Traffico di Google Maps](media/migrate-google-maps-android-app/google-maps-traffic.png)</center>

**Dopo: Mappe di Azure**

Mappe di Azure offre numerose opzioni per la visualizzazione del traffico. Gli eventi del traffico, come strade chiuse e incidenti stradali, possono essere visualizzati come icone sulla mappa. Il flusso del traffico e le strade con codifica a colori possono essere sovrapposti sulla mappa. È possibile modificare i colori affinché vengano visualizzati in relazione al limite di velocità indicato, al normale ritardo previsto o al ritardo assoluto. I dati relativi agli incidenti in Mappe di Azure vengono aggiornati ogni minuto e i dati del flusso di traffico ogni due minuti.

```java
mapControl.onReady(map -> {
    map.setTraffic(
        incidents(true),
        flow(TrafficFlow.RELATIVE));
});
```

<center>

![Traffico in Mappe di Azure](media/migrate-google-maps-android-app/azure-maps-traffic.png)</center>

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su Azure Maps Android SDK.

> [!div class="nextstepaction"]
> [Come usare il controllo mappa di Android](how-to-use-android-map-control-library.md)
