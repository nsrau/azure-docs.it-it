---
title: Operazioni di analisi spaziale
titleSuffix: Azure Cognitive Services
description: Operazioni di analisi spaziale.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: aahi
ms.openlocfilehash: 5955bc35acfaf8f877e68db083871c353a3ce326
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90984931"
---
# <a name="spatial-analysis-operations"></a>Operazioni di analisi spaziale

L'analisi spaziale consente di analizzare il video in streaming in tempo reale dai dispositivi della fotocamera. Per ogni dispositivo della fotocamera configurato, le operazioni per l'analisi spaziale genereranno un flusso di output di messaggi JSON inviati all'istanza dell'hub Azure. 

Il contenitore di analisi spaziale implementa le operazioni seguenti:

| Identificatore dell'operazione| Descrizione|
|---------|---------|
| cognitiveservices. Vision. spatialanalysis-PersonCount | Conta le persone in una zona designata nel campo di visualizzazione della fotocamera. <br> Genera un evento _personCountEvent_ iniziale e quindi _personCountEvent_ gli eventi quando il conteggio viene modificato.  |
| cognitiveservices. Vision. spatialanalysis-personcrossingline | Tiene traccia del momento in cui un utente incrocia una riga designata nel campo di visualizzazione della fotocamera. <br>Genera un evento _personLineEvent_ quando la persona attraversa la riga e fornisce informazioni direzionali. 
| cognitiveservices. Vision. spatialanalysis-personcrossingpolygon | Tiene traccia del momento in cui un utente incrocia una riga designata nel campo di visualizzazione della fotocamera. <br> Genera un evento _personLineEvent_ quando la persona attraversa la zona e fornisce informazioni direzionali. |
| cognitiveservices. Vision. spatialanalysis-persondistance | Tiene traccia del momento in cui le persone violano una regola di distanza. <br> Emette periodicamente un _personDistanceEvent_ con la posizione di ogni violazione della distanza. |

Tutte le operazioni sopra elencate sono disponibili anche nella `.debug` versione, che hanno la possibilità di visualizzare i fotogrammi video durante l'elaborazione. Sarà necessario eseguire `xhost +` nel computer host per abilitare la visualizzazione di fotogrammi video ed eventi.

| Identificatore dell'operazione| Descrizione|
|---------|---------|
| cognitiveservices. Vision. spatialanalysis-PersonCount. debug | Conta le persone in una zona designata nel campo di visualizzazione della fotocamera. <br> Genera un evento _personCountEvent_ iniziale e quindi _personCountEvent_ gli eventi quando il conteggio viene modificato.  |
| cognitiveservices. Vision. spatialanalysis-personcrossingline. debug | Tiene traccia del momento in cui un utente incrocia una riga designata nel campo di visualizzazione della fotocamera. <br>Genera un evento _personLineEvent_ quando la persona attraversa la riga e fornisce informazioni direzionali. 
| cognitiveservices. Vision. spatialanalysis-personcrossingpolygon. debug | Tiene traccia del momento in cui un utente incrocia una riga designata nel campo di visualizzazione della fotocamera. <br> Genera un evento _personLineEvent_ quando la persona attraversa la zona e fornisce informazioni direzionali. |
| cognitiveservices. Vision. spatialanalysis-persondistance. debug | Tiene traccia del momento in cui le persone violano una regola di distanza. <br> Emette periodicamente un _personDistanceEvent_ con la posizione di ogni violazione della distanza. |

L'analisi spaziale può essere eseguita anche con analisi [video in tempo reale](https://azure.microsoft.com/services/media-services/live-video-analytics/) come modulo di intelligenza artificiale video. 

<!--more details on the setup can be found in the [LVA Setup page](LVA-Setup.md). Below is the list of the operations supported with Live Video Analytics. -->

| Identificatore dell'operazione| Descrizione|
|---------|---------|
| cognitiveservices. Vision. spatialanalysis-PersonCount. livevideoanalytics | Conta le persone in una zona designata nel campo di visualizzazione della fotocamera. <br> Genera un evento _personCountEvent_ iniziale e quindi _personCountEvent_ gli eventi quando il conteggio viene modificato.  |
| cognitiveservices. Vision. spatialanalysis-personcrossingline. livevideoanalytics | Tiene traccia del momento in cui un utente incrocia una riga designata nel campo di visualizzazione della fotocamera. <br>Genera un evento _personLineEvent_ quando la persona attraversa la riga e fornisce informazioni direzionali. 
| cognitiveservices. Vision. spatialanalysis-personcrossingpolygon. livevideoanalytics | Tiene traccia del momento in cui un utente incrocia una riga designata nel campo di visualizzazione della fotocamera. <br> Genera un evento _personLineEvent_ quando la persona attraversa la zona e fornisce informazioni direzionali. |
| cognitiveservices. Vision. spatialanalysis-persondistance. livevideoanalytics | Tiene traccia del momento in cui le persone violano una regola di distanza. <br> Emette periodicamente un _personDistanceEvent_ con la posizione di ogni violazione della distanza. |

Le operazioni di analisi video in tempo reale sono disponibili anche nella `.debug` versione (ad esempio, cognitiveservices. Vision. spatialanalysis-PersonCount. livevideoanalytics. debug), che è in grado di visualizzare i fotogrammi video come elaborati. Sarà necessario eseguire `xhost +` nel computer host per abilitare la visualizzazione dei fotogrammi video e degli eventi

> [!IMPORTANT]
> I modelli di intelligenza artificiale per la visione artificiale rilevano e individuano la presenza umana nel metraggio video e nell'output usando un rettangolo di delimitazione intorno a un corpo umano. I modelli di intelligenza artificiale non tentano di rilevare visi o individuare le identità o i dati demografici degli individui.

Questi sono i parametri richiesti da ognuna di queste operazioni di analisi spaziale.

| Parametri delle operazioni| Descrizione|
|---------|---------|
| ID operazione | Identificatore dell'operazione della tabella precedente.|
| Enabled | Booleano: true o false|
| VIDEO_URL| URL RTSP per il dispositivo della fotocamera (esempio: `rtsp://username:password@url` ). L'analisi spaziale supporta il flusso con codifica H. 264 tramite RTSP, http o MP4 |
| VIDEO_SOURCE_ID | Nome descrittivo per il dispositivo della fotocamera o il flusso video. Questa operazione verrà restituita con l'output JSON dell'evento.|
| VIDEO_IS_LIVE| True per i dispositivi della fotocamera; false per i video registrati.|
| VIDEO_DECODE_GPU_INDEX| GPU da cui decodificare il fotogramma video. Per impostazione predefinita, è 0. Deve corrispondere `gpu_index` a in altre configurazioni del nodo, ad esempio `VICA_NODE_CONFIG` `DETECTOR_NODE_CONFIG` .|
| DETECTOR_NODE_CONFIG | JSON che indica la GPU su cui eseguire il nodo del rilevamento. Deve avere il formato seguente: `"{ \"gpu_index\": 0 }",`|
| SPACEANALYTICS_CONFIG | Configurazione JSON per la zona e la linea, come descritto di seguito.|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcount"></a>Configurazione della zona per cognitiveservices. Vision. spatialanalysis-PersonCount

 Questo è un esempio di input JSON per il parametro SPACEANALYTICS_CONFIG che configura una zona. Per questa operazione è possibile configurare più zone.

```json
{
"zones":[{
    "name": "lobbycamera"
    "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
    "threshold": 50.00,
    "events":[{
        "type": "count",
        "config":{
            "trigger": "event",
            "output_frequency": 1
      }
    }]
}
```

| Nome | Tipo| Descrizione|
|---------|---------|---------|
| `zones` | list| Elenco di zone. |
| `name` | string| Nome descrittivo per questa zona.|
| `polygon` | list| Ogni coppia valore rappresenta la x, y per i vertici di un poligono. Il poligono rappresenta le aree in cui gli utenti vengono rilevati o conteggiati e i punti poligono sono basati su coordinate normalizzate (0-1), in cui l'angolo superiore sinistro è (0,0, 0,0) e l'angolo inferiore destro è (1,0, 1,0).   
| `threshold` | float| Gli eventi vengono uscita quando la confidenza dei modelli di intelligenza artificiale è maggiore o uguale a questo valore. |
| `type` | string| Per **cognitiveservices. Vision. spatialanalysis-PersonCount** deve essere `count` .|
| `trigger` | string| Tipo di trigger per l'invio di un evento. I valori supportati sono `event` per l'invio di eventi quando il conteggio cambia o `interval` per l'invio periodico di eventi, indipendentemente dal fatto che il conteggio sia stato modificato o meno.
| `interval` | string| Tempo in secondi durante il quale il conteggio delle persone viene aggregato prima che venga generato un evento. L'operazione continuerà ad analizzare la scena a una velocità costante e restituisce il conteggio più comune rispetto a tale intervallo. L'intervallo di aggregazione è applicabile sia a `event` che a `interval` .|

### <a name="line-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingline"></a>Configurazione della linea per cognitiveservices. Vision. spatialanalysis-personcrossingline

Questo è un esempio di input JSON per il parametro SPACEANALYTICS_CONFIG che configura una riga. È possibile configurare più righe di incrocio per questa operazione.

```json
{
"lines":[{
    "name": "doorcamera" 
    "line": {
        "start": {"x": 0, "y": 0.5},
        "end": {"x": 1, "y": 0.5}
            },
    "threshold": 50.00,
    "events":[{
        "type": "linecrossing",
        "config":{
            "trigger": "event"
            }
        }]
    }]
}
```

| Nome | Tipo| Descrizione|
|---------|---------|---------|
| `lines` | list| Elenco di righe.|
| `name` | string| Nome descrittivo per questa riga.|
| `line` | list| Definizione della riga. Si tratta di una linea direzionale che consente di comprendere "entry" e "Exit".|
| `start` | coppia valore| coordinate x, y per il punto iniziale della linea. I valori float rappresentano la posizione del vertice rispetto all'angolo superiore sinistro. Per calcolare i valori x assoluti, si moltiplicano questi valori con le dimensioni del frame. |
| `end` | coppia valore| coordinate x, y per il punto finale della linea. I valori float rappresentano la posizione del vertice rispetto all'angolo superiore sinistro. Per calcolare i valori x assoluti, si moltiplicano questi valori con le dimensioni del frame. |
| `threshold` | float| Gli eventi vengono uscita quando la confidenza dei modelli di intelligenza artificiale è maggiore o uguale a questo valore. |
| `type` | string| Per **cognitiveservices. Vision. spatialanalysis-personcrossingline** deve essere `linecrossing` .|
|`trigger`|string|Tipo di trigger per l'invio di un evento.<br>Valori supportati: "Event": viene attivato quando un utente incrocia la riga.|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon"></a>Configurazione della zona per cognitiveservices. Vision. spatialanalysis-personcrossingpolygon

Questo è un esempio di input JSON per il parametro SPACEANALYTICS_CONFIG che configura una zona. Per questa operazione è possibile configurare più zone.

 ```json
{
"zones":[{
    "name": "queuecamera"
    "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
    "threshold": 50.00,
    "events":[{
        "type": "zone_crossing",
        "config":{
            "trigger": "event"
            }
        }]
    }]
}
```

| Nome | Tipo| Descrizione|
|---------|---------|---------|
| `zones` | list| Elenco di zone. |
| `name` | string| Nome descrittivo per questa zona.|
| `polygon` | list| Ogni coppia valore rappresenta la x, y per i vertici del poligono. Il poligono rappresenta le aree in cui gli utenti vengono rilevati o conteggiati. I valori float rappresentano la posizione del vertice rispetto all'angolo superiore sinistro. Per calcolare i valori x assoluti, si moltiplicano questi valori con le dimensioni del frame. 
| `threshold` | float| Gli eventi vengono uscita quando la confidenza dei modelli di intelligenza artificiale è maggiore o uguale a questo valore. |
| `type` | string| Per **cognitiveservices. Vision. spatialanalysis-personcrossingpolygon** deve essere `enter` o `exit` .|
| `trigger`|string|Tipo di trigger per l'invio di un evento<br>Valori supportati: "Event": viene attivato quando un utente immette o esce dalla zona.|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-persondistance"></a>Configurazione della zona per cognitiveservices. Vision. spatialanalysis-persondistance

Questo è un esempio di input JSON per il parametro SPACEANALYTICS_CONFIG che configura una zona per **cognitiveservices. Vision. spatialanalysis-persondistance**. Per questa operazione è possibile configurare più zone.

```json
{
"zones":[{
    "name": "lobbycamera",
    "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
    "threshold": 35.00,
    "events":[{
        "type": "persondistance",
        "config":{
            "trigger": "event",
            "output_frequency":1,
            "minimum_distance_threshold":6.0,
            "maximum_distance_threshold":35.0
            }
        }]
    }]
}
```

| Nome | Tipo| Descrizione|
|---------|---------|---------|
| `zones` | list| Elenco di zone. |
| `name` | string| Nome descrittivo per questa zona.|
| `polygon` | list| Ogni coppia valore rappresenta la x, y per i vertici del poligono. Il poligono rappresenta le aree in cui vengono conteggiate le persone e viene misurata la distanza tra le persone. I valori float rappresentano la posizione del vertice rispetto all'angolo superiore sinistro. Per calcolare i valori x assoluti, si moltiplicano questi valori con le dimensioni del frame. 
| `threshold` | float| Gli eventi vengono uscita quando la confidenza dei modelli di intelligenza artificiale è maggiore o uguale a questo valore. |
| `type` | string| Per **cognitiveservices. Vision. spatialanalysis-persondistance** deve essere `people_distance` .|
| `trigger` | string| Tipo di trigger per l'invio di un evento. I valori supportati sono `event` per l'invio di eventi quando il conteggio cambia o `interval` per l'invio periodico di eventi, indipendentemente dal fatto che il conteggio sia stato modificato o meno.
| `interval` | string | Tempo in secondi durante il quale le violazioni verranno aggregate prima che venga generato un evento. L'intervallo di aggregazione è applicabile sia a `event` che a `interval` .|
| `output_frequency` | INT | Frequenza con cui gli eventi vengono uscita. Quando `output_frequency` = x, ogni evento X è uscita, ad esempio. `output_frequency` = 2 indica che ogni altro evento viene restituito. Il output_frequency è applicabile sia a `event` che a `interval` .|
| `minimum_distance_threshold` | float| Distanza in metri che attiverà un evento "TooClose" quando le persone sono inferiori a tale distanza.|
| `maximum_distance_threshold` | float| Distanza in metri che attiverà un evento "TooFar" quando le persone sono più grandi della distanza.|

Questo è un esempio di input JSON per il parametro DETECTOR_NODE_CONFIG che configura una zona **cognitiveservices. Vision. spatialanalysis-persondistance** .

```json
{ 
"gpu_index": 0, 
"do_calibration": true
}
```

| Nome | Tipo| Descrizione|
|---------|---------|---------|
| `gpu_index` | string| Indice GPU in cui verrà eseguita l'operazione.|
| `do_calibration` | string | Indica che la taratura è attivata. `do_calibration` deve essere true per il corretto funzionamento di **cognitiveservices. Vision. spatialanalysis-persondistance** .|

Vedere le linee guida per la [selezione host](spatial-analysis-camera-placement.md)  per informazioni sulle configurazioni di zone e linee.

## <a name="spatial-analysis-operation-output"></a>Output dell'operazione di analisi spaziale

Gli eventi di ogni operazione sono uscita nell'hub Azure Internet in formato JSON.

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcount-ai-insights"></a>Formato JSON per cognitiveservices. Vision. spatialanalysis-PersonCount AI Insights

Esempio di JSON per l'output di un evento da questa operazione.

```json
{
    "events": [
        {
            "id": "b013c2059577418caa826844223bb50b",
            "type": "personCountEvent",
            "detectionIds": [
                "bc796b0fc2534bc59f13138af3dd7027",
                "60add228e5274158897c135905b5a019"
            ],
            "properties": {
                "personCount": 2
            },
            "zone": "lobbycamera",
            "trigger": "event"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:06:57.224Z",
        "width": 608,
        "height": 342,
        "frameId": "1400",
        "cameraCalibrationInfo": {
            "status": "Complete",
            "cameraHeight": 10.306597709655762,
            "focalLength": 385.3199462890625,
            "tiltupAngle": 1.0969393253326416
        },
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "bc796b0fc2534bc59f13138af3dd7027",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.612683747944079,
                        "y": 0.25340268765276636
                    },
                    {
                        "x": 0.7185954043739721,
                        "y": 0.6425260577285499
                    }
                ]
            },
            "confidence": 0.9559211134910583,
            "centerGroundPoint": {
                "x": 0.0,
                "y": 0.0
            },
            "metadataType": ""
        },
        {
            "type": "person",
            "id": "60add228e5274158897c135905b5a019",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.22326200886776573,
                        "y": 0.17830915618361087
                    },
                    {
                        "x": 0.34922296122500773,
                        "y": 0.6297955429344847
                    }
                ]
            },
            "confidence": 0.9389744400978088,
            "centerGroundPoint": {
                "x": 0.0,
                "y": 0.0
            },
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| Nome campo evento | Tipo| Descrizione|
|---------|---------|---------|
| `id` | string| ID evento|
| `type` | string| Tipo di evento|
| `detectionsId` | array| Matrice di dimensioni 1 dell'identificatore univoco del rilevamento della persona che ha attivato l'evento|
| `properties` | collection| Raccolta di valori|
| `trackinId` | string| Identificatore univoco della persona rilevata|
| `status` | string| ' Enter ' o ' Exit '|
| `side` | INT| Il numero di lato del poligono attraversato dall'utente|
| `zone` | string | Il campo "Name" del poligono che rappresenta la zona attraversata|
| `trigger` | string| Il tipo di trigger è' Event ' o ' Interval ' a seconda del valore di `trigger` in SPACEANALYTICS_CONFIG|

| Nome campo rilevamento | Tipo| Descrizione|
|---------|---------|---------|
| `id` | string| ID rilevamento|
| `type` | string| Tipo di rilevamento|
| `region` | collection| Raccolta di valori|
| `type` | string| Tipo di area|
| `points` | collection| Punti in alto a sinistra e in basso a destra quando il tipo di area è rettangolo |
| `confidence` | float| Confidenza degli algoritmi|

| Nome campo SourceInfo | Tipo| Descrizione|
|---------|---------|---------|
| `id` | string| Camera ID|
| `timestamp` | Data| Data UTC di creazione del payload JSON|
| `width` | INT | Larghezza fotogramma video|
| `height` | INT | Altezza del fotogramma video|
| `frameId` | INT | Identificatore frame|
| `cameraCallibrationInfo` | collection | Raccolta di valori|
| `status` | string | Indica se la taratura della fotocamera sul piano di fondo è "completata"|
| `cameraHeight` | float | Altezza della fotocamera sopra la superficie in piedi. Questa operazione viene dedotta dalla calibrazione automatica. |
| `focalLength` | float | Lunghezza focale della fotocamera in pixel. Questa operazione viene dedotta dalla calibrazione automatica. |
| `tiltUpAngle` | float | Angolo di inclinazione della fotocamera verticale. Questa operazione viene dedotta dalla calibrazione automatica.|

| Nome campo SourceInfo | Tipo| Descrizione|
|---------|---------|---------|
| `id` | string| Camera ID|
| `timestamp` | Data| Data UTC di creazione del payload JSON|
| `width` | INT | Larghezza fotogramma video|
| `height` | INT | Altezza del fotogramma video|
| `frameId` | INT | Identificatore frame|


### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingline-ai-insights"></a>Formato JSON per cognitiveservices. Vision. spatialanalysis-personcrossingline AI Insights

Codice JSON di esempio per i rilevamenti restituiti da questa operazione.

```json
{
    "events": [
        {
            "id": "3733eb36935e4d73800a9cf36185d5a2",
            "type": "personLineEvent",
            "detectionIds": [
                "90d55bfc64c54bfd98226697ad8445ca"
            ],
            "properties": {
                "trackingId": "90d55bfc64c54bfd98226697ad8445ca",
                "status": "CrossLeft"
            },
            "zone": "doorcamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:06:53.261Z",
        "width": 608,
        "height": 342,
        "frameId": "1340",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "90d55bfc64c54bfd98226697ad8445ca",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.491627341822574,
                        "y": 0.2385801348769874
                    },
                    {
                        "x": 0.588894994635331,
                        "y": 0.6395559924387793
                    }
                ]
            },
            "confidence": 0.9005028605461121,
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```
| Nome campo evento | Tipo| Descrizione|
|---------|---------|---------|
| `id` | string| ID evento|
| `type` | string| Tipo di evento|
| `detectionsId` | array| Matrice di dimensioni 1 dell'identificatore univoco del rilevamento della persona che ha attivato l'evento|
| `properties` | collection| Raccolta di valori|
| `trackinId` | string| Identificatore univoco della persona rilevata|
| `status` | string| Direzione delle incrociate di riga, ovverò CrossLeft ' o ' CrossRight '|
| `zone` | string | Il campo "nome" della riga che è stata superata|

| Nome campo rilevamento | Tipo| Descrizione|
|---------|---------|---------|
| `id` | string| ID rilevamento|
| `type` | string| Tipo di rilevamento|
| `region` | collection| Raccolta di valori|
| `type` | string| Tipo di area|
| `points` | collection| Punti in alto a sinistra e in basso a destra quando il tipo di area è rettangolo |
| `confidence` | float| Confidenza degli algoritmi|

| Nome campo SourceInfo | Tipo| Descrizione|
|---------|---------|---------|
| `id` | string| Camera ID|
| `timestamp` | Data| Data UTC di creazione del payload JSON|
| `width` | INT | Larghezza fotogramma video|
| `height` | INT | Altezza del fotogramma video|
| `frameId` | INT | Identificatore frame|


> [!IMPORTANT]
> Il modello di intelligenza artificiale rileva una persona indipendentemente dal fatto che la persona si trovi verso o fuori dalla fotocamera. Il modello di intelligenza artificiale non esegue il rilevamento del viso o il riconoscimento e non emette informazioni biometriche. 

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon-ai-insights"></a>Formato JSON per cognitiveservices. Vision. spatialanalysis-personcrossingpolygon AI Insights

Codice JSON di esempio per i rilevamenti restituiti da questa operazione.

```json
{
    "events": [
        {
            "id": "f095d6fe8cfb4ffaa8c934882fb257a5",
            "type": "personZoneEvent",
            "detectionIds": [
                "afcc2e2a32a6480288e24381f9c5d00e"
            ],
            "properties": {
                "trackingId": "afcc2e2a32a6480288e24381f9c5d00e",
                "status": "Enter",
                "side": ""
            },
            "zone": "queuecamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:15:09.680Z",
        "width": 608,
        "height": 342,
        "frameId": "428",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "afcc2e2a32a6480288e24381f9c5d00e",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.8135572734631991,
                        "y": 0.6653949670624315
                    },
                    {
                        "x": 0.9937645761590255,
                        "y": 0.9925406829655519
                    }
                ]
            },
            "confidence": 0.6267998814582825,
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| Nome campo evento | Tipo| Descrizione|
|---------|---------|---------|
| `id` | string| ID evento|
| `type` | string| Tipo di evento|
| `detectionsId` | array| Matrice di dimensioni 1 dell'identificatore univoco del rilevamento della persona che ha attivato l'evento|
| `properties` | collection| Raccolta di valori|
| `trackinId` | string| Identificatore univoco della persona rilevata|
| `status` | string| Direzione degli incroci del poligono, ovverò Enter ' o ' Exit '|
| `zone` | string | Il campo "Name" del poligono che rappresenta la zona attraversata|

| Nome campo rilevamento | Tipo| Descrizione|
|---------|---------|---------|
| `id` | string| ID rilevamento|
| `type` | string| Tipo di rilevamento|
| `region` | collection| Raccolta di valori|
| `type` | string| Tipo di area|
| `points` | collection| Punti in alto a sinistra e in basso a destra quando il tipo di area è rettangolo |
| `confidence` | float| Confidenza degli algoritmi|

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-persondistance-ai-insights"></a>Formato JSON per cognitiveservices. Vision. spatialanalysis-persondistance AI Insights

Codice JSON di esempio per i rilevamenti restituiti da questa operazione.

```json
{
    "events": [
        {
            "id": "9c15619926ef417aa93c1faf00717d36",
            "type": "personDistanceEvent",
            "detectionIds": [
                "9037c65fa3b74070869ee5110fcd23ca",
                "7ad7f43fd1a64971ae1a30dbeeffc38a"
            ],
            "properties": {
                "personCount": 5,
                "averageDistance": 20.807043981552123,
                "minimumDistanceThreshold": 6.0,
                "maximumDistanceThreshold": "Infinity",
                "eventName": "TooClose",
                "distanceViolationPersonCount": 2
            },
            "zone": "lobbycamera",
            "trigger": "event"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:17:25.309Z",
        "width": 608,
        "height": 342,
        "frameId": "1199",
        "cameraCalibrationInfo": {
            "status": "Complete",
            "cameraHeight": 12.9940824508667,
            "focalLength": 401.2800598144531,
            "tiltupAngle": 1.057669997215271
        },
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "9037c65fa3b74070869ee5110fcd23ca",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.39988183975219727,
                        "y": 0.2719132942065858
                    },
                    {
                        "x": 0.5051516984638414,
                        "y": 0.6488402517218339
                    }
                ]
            },
            "confidence": 0.948630690574646,
            "centerGroundPoint": {
                "x": -1.4638760089874268,
                "y": 18.29732322692871
            },
            "metadataType": ""
        },
        {
            "type": "person",
            "id": "7ad7f43fd1a64971ae1a30dbeeffc38a",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.5200299714740954,
                        "y": 0.2875368218672903
                    },
                    {
                        "x": 0.6457497446160567,
                        "y": 0.6183311060855263
                    }
                ]
            },
            "confidence": 0.8235412240028381,
            "centerGroundPoint": {
                "x": 2.6310102939605713,
                "y": 18.635927200317383
            },
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| Nome campo evento | Tipo| Descrizione|
|---------|---------|---------|
| `id` | string| ID evento|
| `type` | string| Tipo di evento|
| `detectionsId` | array| Matrice di dimensioni 1 dell'identificatore univoco del rilevamento della persona che ha attivato l'evento|
| `properties` | collection| Raccolta di valori|
| `personCount` | INT| Numero di persone rilevate quando è stato emesso l'evento|
| `averageDistance` | float| Distanza media tra tutti gli utenti rilevati in piedi|
| `minimumDistanceThreshold` | float| Distanza in metri che attiverà un evento "TooClose" quando le persone sono inferiori a tale distanza.|
| `maximumDistanceThreshold` | float| Distanza in metri che attiverà un evento "TooFar" quando gli utenti sono più grandi della distanza.|
| `eventName` | string| `TooClose`Il nome dell'evento è `minimumDistanceThreshold` violato, `TooFar` quando `maximumDistanceThreshold` viene violato o quando la `unknown` calibrazione automatica non è stata completata|
| `distanceViolationPersonCount` | INT| Numero di persone rilevate in violazione di `minimumDistanceThreshold` o `maximumDistanceThreshold`|
| `zone` | string | Il campo "Name" del poligono che rappresenta la zona monitorata per l'allontanamento tra le persone|
| `trigger` | string| Il tipo di trigger è' Event ' o ' Interval ' a seconda del valore di `trigger` in SPACEANALYTICS_CONFIG|

| Nome campo rilevamento | Tipo| Descrizione|
|---------|---------|---------|
| `id` | string| ID rilevamento|
| `type` | string| Tipo di rilevamento|
| `region` | collection| Raccolta di valori|
| `type` | string| Tipo di area|
| `points` | collection| Punti in alto a sinistra e in basso a destra quando il tipo di area è rettangolo |
| `confidence` | float| Confidenza degli algoritmi|
| `centerGroundPoint` | 2 valori float| `x`, `y` i valori con le coordinate della posizione dedotta della persona sulla superficie in piedi. `x` distanza dalla fotocamera perpendicolare al piano dell'immagine della fotocamera proiettato sulla superficie in piedi. `y` distanza tra la fotocamera e il piano dell'immagine proiettata sulla superficie in piedi.|

| Nome campo SourceInfo | Tipo| Descrizione|
|---------|---------|---------|
| `id` | string| Camera ID|
| `timestamp` | Data| Data UTC di creazione del payload JSON|
| `width` | INT | Larghezza fotogramma video|
| `height` | INT | Altezza del fotogramma video|
| `frameId` | INT | Identificatore frame|
| `cameraCallibrationInfo` | collection | Raccolta di valori|
| `status` | string | Indica se la taratura della fotocamera sul piano di fondo è "completata"|
| `cameraHeight` | float | Altezza della fotocamera sopra la superficie in piedi. Questa operazione viene dedotta dalla calibrazione automatica. |
| `focalLength` | float | Lunghezza focale della fotocamera in pixel. Questa operazione viene dedotta dalla calibrazione automatica. |
| `tiltUpAngle` | float | Angolo di inclinazione della fotocamera verticale. Questa operazione viene dedotta dalla calibrazione automatica.|


## <a name="use-the-output-generated-by-the-container"></a>Usare l'output generato dal contenitore

Potrebbe essere necessario integrare il rilevamento o gli eventi di analisi spaziale nell'applicazione. Ecco alcuni approcci da considerare: 

* Usare l'SDK di hub eventi di Azure per il linguaggio di programmazione scelto per connettersi all'endpoint dell'hub Azure Internet e ricevere gli eventi. Per altre informazioni, vedere [leggere i messaggi da dispositivo a cloud dall'endpoint predefinito](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-read-builtin) . 
* Configurare il **routing dei messaggi** nell'hub Azure Internet per inviare gli eventi ad altri endpoint o salvare gli eventi nell'archivio dati. Per ulteriori informazioni, vedere [routing dei messaggi dell'hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c) Internet. 
* Configurare un processo di analisi di flusso di Azure per elaborare gli eventi in tempo reale quando arrivano e creare visualizzazioni. 

## <a name="deploying-spatial-analysis-operations-at-scale-multiple-cameras"></a>Distribuzione di operazioni di analisi spaziale su larga scala (più fotocamere)

Per ottenere prestazioni e utilizzo ottimali delle GPU, è possibile distribuire qualsiasi operazione di analisi spaziale su più fotocamere usando le istanze di Graph. Di seguito è riportato un esempio per l'esecuzione dell'operazione cognitiveservices. Vision. spatialanalysis-PersonCount su cinque (5) fotocamere.

```json
 "properties.desired": {
      "globalSettings": {
          "PlatformTelemetryEnabled": false,
          "CustomerTelemetryEnabled": true
      },
      "graphs": {
          "personcount": {
              "operationId": "cognitiveservices.vision.spatialanalysis-personcount",
              "version": 1,
              "enabled": true,
              "sharedNodes": {
                  "shared_detector1": {
                      "node": "PersonCountGraph.detector",
                      "parameters": {
                          "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0, \"batch_size\": 5}",
                      }
                  }
              },
              "parameters": {
                  "VIDEO_DECODE_GPU_INDEX": 0,
                  "VIDEO_IS_LIVE": true
              },
              "instances": {
                  "1": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 1>",
                          "VIDEO_SOURCE_ID": "camera 1",
                          "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"zone5\",\"polygon\":[[0,0],[1,0],[0,1],[1,1],[0,0]],\"threshold\":50.0, \"events\":[{\"type\":\"count\", \"output_frequency\": 1}]}]}"
                      }
                  },
                  "2": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 2>",
                          "VIDEO_SOURCE_ID": "camera 2",
                          "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                      }
                  },
                  "3": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 3>",
                          "VIDEO_SOURCE_ID": "camera 3",
                          "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                      }
                  },
                  "4": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 4>",
                          "VIDEO_SOURCE_ID": "camera 4",
                          "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                      }
                  },
                  "5": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 5>",
                          "VIDEO_SOURCE_ID": "camera 5",
                          "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                      }
                  }
              }
          }
      }
  }
  ```
| Nome | Tipo| Descrizione|
|---------|---------|---------|
| `batch_size` | INT | Indica il numero di fotocamere che verranno utilizzate in tale operazione.|

## <a name="next-steps"></a>Passaggi successivi

* [Distribuire un'applicazione Web per il conteggio delle persone](spatial-analysis-web-app.md)
* [Registrazione e risoluzione dei problemi](spatial-analysis-logging.md)
* [Guida al posizionamento della fotocamera](spatial-analysis-camera-placement.md)
* [Guida al posizionamento di zone e linee](spatial-analysis-zone-line-placement.md)
