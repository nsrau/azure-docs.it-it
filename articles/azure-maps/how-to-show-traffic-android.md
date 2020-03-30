---
title: Mostrare i dati sul traffico sulla mappa androide Mappe di Microsoft Azure
description: In questo articolo si apprenderà come visualizzare i dati sul traffico su una mappa usando Microsoft Azure Maps Android SDK.
author: philmea
ms.author: philmea
ms.date: 02/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: e5611eeb08ac370e12cf452d57a87e449fbd80da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335387"
---
# <a name="show-traffic-data-on-the-map-using-azure-maps-android-sdk"></a>Mostrare i dati sul traffico sulla mappa usando Android SDK di Azure MapsShow traffic data on the map using Azure Maps Android SDK

I dati di flusso e i dati relativi agli eventi imprevisti sono i due tipi di dati sul traffico che possono essere visualizzati sulla mappa. Questa guida mostra come visualizzare entrambi i tipi di dati sul traffico. I dati sugli incidenti sono costituiti da dati basati su punti e linee per operazioni quali costruzioni, chiusure stradali e incidenti. I dati di flusso mostrano le metriche sul flusso di traffico sulla strada.

## <a name="prerequisites"></a>Prerequisiti

Prima di poter visualizzare il traffico sulla mappa, è necessario [creare un account Azure](quick-demo-map-app.md#create-an-account-with-azure-maps)e ottenere una chiave di [sottoscrizione.](quick-demo-map-app.md#get-the-primary-key-for-your-account) Quindi, è necessario installare [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) e caricare una mappa.

## <a name="incidents-traffic-data"></a>Dati sul traffico degli incidenti 

È necessario importare le seguenti librerie per chiamare `setTraffic` e: `incidents`

```java
import static com.microsoft.com.azure.maps.mapcontrol.options.TrafficOptions.incidents;
```

 Il frammento di codice seguente illustra come visualizzare i dati sul traffico sulla mappa. Passiamo un valore `incidents` booleano al metodo `setTraffic` e lo passiamo al metodo. 

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map - > {
        map.setTraffic(incidents(true));
    }
}
```

## <a name="flow-traffic-data"></a>Dati sul traffico di flusso

È innanzitutto necessario importare le seguenti `setTraffic` `flow`librerie da chiamare e:

```java
import com.microsoft.azure.maps.mapcontrol.options.TrafficFlow;
import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.flow;
```

Usare il frammento di codice seguente per impostare i dati del flusso di traffico. Analogamente al codice nella sezione precedente, si `flow` passa `setTraffic` il valore restituito del metodo al metodo. Esistono quattro valori che possono `flow`essere passati `flow` a , e ogni valore verrà attivato per restituire il rispettivo valore. Il valore `flow` restituito di verrà quindi `setTraffic`passato come argomento a . Vedere la tabella seguente per questi quattro valori:See the table below for these four values:

| | |
| :-- | :-- |
| Flusso di traffico.NONE | Non visualizza i dati sul traffico sulla mappa |
| Flusso di traffico.RELATIVE | Mostra i dati sul traffico relativi alla velocità di flusso libera della strada |
| Flusso di traffico.RELATIVE_DELAY | Visualizza le aree più lente del ritardo medio previsto |
| Flusso di traffico.ABSOLUTE | Mostra la velocità assoluta di tutti i veicoli sulla strada |

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map -> {
        map.setTraffic(flow(TrafficFlow.RELATIVE)));
    }
}
```

## <a name="show-incident-traffic-data-by-clicking-a-feature"></a>Mostrare i dati sul traffico degli eventi imprevisti facendo clic su una funzionalità

Per ottenere gli eventi imprevisti per una funzionalità specifica, è possibile utilizzare il codice seguente. Quando si fa clic su una funzionalità, la logica del codice verifica la presenza di eventi imprevisti e compila un messaggio sull'evento imprevisto. Nella parte inferiore dello schermo viene visualizzato un messaggio con i dettagli.

1. In primo luogo, è necessario modificare **il layout > res > activity_main.xml**, in modo che sia simile a quello riportato di seguito. È possibile `mapcontrol_centerLat`sostituire `mapcontrol_centerLng`il `mapcontrol_zoom` , , e con i valori desiderati. Ricordiamo che il livello di zoom è un valore compreso tra 0 e 22. Al livello di zoom 0, l'intero mondo si adatta a una singola tessera.

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
           app:mapcontrol_centerLat="47.6050"
           app:mapcontrol_centerLng="-122.3344"
           app:mapcontrol_zoom="12"
           />

   </FrameLayout>
   ```

2. Aggiungere il codice seguente al file **MainActivity.java.** Il pacchetto è incluso per impostazione predefinita, quindi assicurati di mantenere il pacchetto in alto.

   ```java
   package <yourpackagename>;
   import androidx.appcompat.app.AppCompatActivity;

   import android.os.Bundle;
   import android.widget.Toast;

   import com.microsoft.azure.maps.mapcontrol.AzureMaps;
   import com.microsoft.azure.maps.mapcontrol.MapControl;
   import com.mapbox.geojson.Feature;
   import com.microsoft.azure.maps.mapcontrol.events.OnFeatureClick;

   import com.microsoft.azure.maps.mapcontrol.options.TrafficFlow;
   import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.flow;
   import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.incidents;

   public class MainActivity extends AppCompatActivity {

       static {
           AzureMaps.setSubscriptionKey("Your Azure Maps Subscription Key");
       }

       MapControl mapControl;

       @Override
       protected void onCreate(Bundle savedInstanceState) {
           super.onCreate(savedInstanceState);
           setContentView(R.layout.activity_main);

           mapControl = findViewById(R.id.mapcontrol);

           mapControl.onCreate(savedInstanceState);

           //Wait until the map resources are ready.
           mapControl.getMapAsync(map -> {

               map.setTraffic(flow(TrafficFlow.RELATIVE));
               map.setTraffic(incidents(true));

               map.events.add((OnFeatureClick) (features) -> {

                   if (features != null && features.size() > 0) {
                       Feature incident = features.get(0);
                       if (incident.properties() != null) {


                           StringBuilder sb = new StringBuilder();
                           String incidentType = incident.getStringProperty("incidentType");
                           if (incidentType != null) {
                               sb.append(incidentType);
                           }
                           if (sb.length() > 0) sb.append("\n");
                           if ("Road Closed".equals(incidentType)) {
                               sb.append(incident.getStringProperty("from"));
                           } else {
                               String description = incident.getStringProperty("description");
                               if (description != null) {
                                   for (String word : description.split(" ")) {
                                       if (word.length() > 0) {
                                           sb.append(word.substring(0, 1).toUpperCase());
                                           if (word.length() > 1) {
                                               sb.append(word.substring(1));
                                           }
                                           sb.append(" ");
                                       }
                                   }
                               }
                           }
                           String message = sb.toString();

                           if (message.length() > 0) {
                               Toast.makeText(this,message,Toast.LENGTH_LONG).show();
                           }
                       }
                   }
               });
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

3. Dopo aver incorporato il codice precedente nell'applicazione, sarà possibile fare clic su una funzionalità e visualizzare i dettagli degli eventi imprevisti di traffico. A seconda della latitudine, della longitudine e dei valori del livello di zoom utilizzati nel file **activity_main.xml,** verranno visualizzati risultati simili all'immagine seguente:

   <center>

   ![Incident-traffic-on-the-map](./media/how-to-show-traffic-android/android-traffic.png)

   </center>

## <a name="next-steps"></a>Passaggi successivi

Visualizza le guide seguenti per scoprire come aggiungere altri dati alla mappa:

> [!div class="nextstepaction"]
> [Aggiungere un livello simbolo](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per i riquadri](how-to-add-tile-layer-android-map.md)

> [!div class="nextstepaction"]
> [Aggiungere forme alla mappa di Android](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Visualizzare informazioni sulle funzionalità](display-feature-information-android.md)
