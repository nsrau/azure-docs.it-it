---
title: Visualizzare i dati sul traffico nella mappa Android | Mappe Microsoft Azure
description: In questo articolo si apprenderà come visualizzare i dati sul traffico su una mappa usando il Microsoft Azure Maps Android SDK.
author: farah-alyasari
ms.author: v-faalya
ms.date: 02/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 73f490ec069ff44929ca70f4ecf2ab3aca52934d
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209350"
---
# <a name="show-traffic-data-on-the-map-using-azure-maps-android-sdk"></a>Mostra i dati sul traffico sulla mappa usando le mappe di Azure Android SDK

I dati di flusso e gli eventi imprevisti sono i due tipi di dati di traffico che possono essere visualizzati sulla mappa. Questa guida illustra come visualizzare entrambi i dati sul traffico. I dati degli eventi imprevisti sono costituiti da dati basati su punti e righe per operazioni quali costruzioni, chiusure stradali e incidenti. I dati di Flow mostrano le metriche relative al flusso di traffico in viaggio.

## <a name="prerequisites"></a>Prerequisites

Prima di poter visualizzare il traffico sulla mappa, è necessario installare [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) e caricare una mappa.

## <a name="incidents-traffic-data"></a>Dati traffico eventi imprevisti 

È necessario importare le librerie seguenti per chiamare `setTraffic` e `incidents`:

```java
import static com.microsoft.com.azure.maps.mapcontrol.options.TrafficOptions.incidents;
```

 Il frammento di codice seguente illustra come visualizzare i dati sul traffico sulla mappa. Viene passato un valore booleano al metodo `incidents` e passato al metodo `setTraffic`. 

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map - > {
        map.setTraffic(incidents(true));
}
}
```

## <a name="flow-traffic-data"></a>Flussi di dati sul traffico

È necessario innanzitutto importare le librerie seguenti per chiamare `setTraffic` e `flow`:

```java
import com.microsoft.azure.maps.mapcontrol.options.TrafficFlow;
import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.flow;
```

Usare il frammento di codice seguente per impostare i dati del flusso di traffico. Analogamente al codice della sezione precedente, viene passato il valore restituito del metodo `flow` al metodo `setTraffic`. Sono disponibili quattro valori che possono essere passati a `flow`e ogni valore attiverà `flow` per restituire il rispettivo valore. Il valore restituito di `flow` verrà quindi passato come argomento a `setTraffic`. Vedere la tabella seguente per questi quattro valori:

| | |
| :-- | :-- |
| TrafficFlow. NONE | Non Visualizza i dati sul traffico sulla mappa |
| TrafficFlow. relativa | Mostra i dati sul traffico relativi alla velocità del flusso libero della strada |
| TrafficFlow. RELATIVE_DELAY | Visualizza le aree più lente rispetto al ritardo medio previsto |
| TrafficFlow. ABSOLUTe | Mostra la velocità assoluta di tutti i veicoli in viaggio |

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map -> 
        map.setTraffic(flow(TrafficFlow.RELATIVE)));
}
```

## <a name="show-incident-traffic-data-by-clicking-a-feature"></a>Mostra i dati sul traffico degli eventi imprevisti facendo clic su una funzionalità

Per ottenere gli eventi imprevisti per una funzionalità specifica, è possibile usare il codice seguente. Quando si fa clic su una funzionalità, la logica del codice verifica la presenza di eventi imprevisti e compila un messaggio sull'evento imprevisto. Viene visualizzato un messaggio nella parte inferiore della schermata con i dettagli.

1. In primo luogo, è necessario modificare il **layout res > > activity_main. XML**, in modo che appaia come quello riportato di seguito. È possibile sostituire il `mapcontrol_centerLat`, `mapcontrol_centerLng`e `mapcontrol_zoom` con i valori desiderati. Si ricordi che il livello di zoom è un valore compreso tra 0 e 22. Al livello di zoom 0, l'intero mondo si adatta a un singolo riquadro.

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

2. Aggiungere il codice seguente al file **MainActivity. Java** . Il pacchetto è incluso per impostazione predefinita, quindi assicurarsi di mantenerlo nella parte superiore.

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

3. Una volta incorporato il codice precedente nell'applicazione, sarà possibile fare clic su una funzionalità e visualizzare i dettagli relativi agli eventi imprevisti del traffico. A seconda della latitudine, della longitudine e dei valori del livello di zoom usati nel file **activity_main. XML** , verranno visualizzati risultati simili all'immagine seguente:

   <center>

   ![Evento imprevisto-traffico-mappa](./media/how-to-show-traffic-android/android-traffic.png)

   </center>

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come aggiungere altri dati alla mappa, vedere le guide seguenti:

> [!div class="nextstepaction"]
> [Aggiungere un livello per i simboli](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per i riquadri](how-to-add-tile-layer-android-map.md)

> [!div class="nextstepaction"]
> [Aggiungere forme alla mappa Android](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Visualizzare le informazioni sulle funzionalità](display-feature-information-android.md)
