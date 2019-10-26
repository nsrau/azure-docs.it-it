---
title: Georecinzione e aggregazione geospaziale con analisi di flusso di Azure
description: Questo articolo descrive come usare analisi di flusso di Azure per la geoschermatura e l'aggregazione geospaziale.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: d44b2fae677554594f0cc280c1129bbd6effddf2
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935080"
---
# <a name="geofencing-and-geospatial-aggregation-scenarios-with-azure-stream-analytics"></a>Scenari di geoschermatura e aggregazione geospaziale con analisi di flusso di Azure

Con le funzioni geospaziali predefinite, è possibile usare analisi di flusso di Azure per creare applicazioni per scenari quali gestione della flotta, condivisione delle corse, automobili connesse e rilevamento di asset.

## <a name="geofencing"></a>Geofencing

Analisi di flusso di Azure supporta i calcoli di geoschermatura in tempo reale a bassa latenza nel cloud e nel runtime IoT Edge.

### <a name="geofencing-scenario"></a>Scenario di geoschermatura

Una società di produzione deve tenere traccia delle risorse nei loro edifici. Ogni dispositivo è dotato di un GPS e vuole ricevere notifiche se un dispositivo lascia una determinata area.

I dati di riferimento usati in questo esempio hanno le informazioni georecinzioni per gli edifici e i dispositivi consentiti in ogni edificio. Tenere presente che i dati di riferimento possono essere statici o a modifica lenta. Per questo scenario vengono usati i dati di riferimento statici. Un flusso di dati emette continuamente l'ID del dispositivo e la relativa posizione corrente.

### <a name="define-geofences-in-reference-data"></a>Definire geobarriere nei dati di riferimento

Un geofence può essere definito usando un oggetto GeoJSON. Per i processi con compatibilità con la versione 1,2 e successive, è possibile definire Geofence anche usando WKT (Well Known Text) come `NVARCHAR(MAX)`. WKT è uno standard Open Geospatial Consortium (OGC) usato per rappresentare i dati spaziali in un formato testuale.

Le funzioni geospaziali predefinite possono usare i limiti geografici definiti per verificare se un elemento si trova all'interno o all'esterno di un poligono Geofence specifico.

La tabella seguente è un esempio di dati di riferimento Geofence che possono essere archiviati nell'archivio BLOB di Azure o in una tabella SQL di Azure. Ogni sito è rappresentato da un poligono geospaziale e ogni dispositivo è associato a un ID sito consentito.

|SiteID|SiteName|Recinto virtuale|AllowedDeviceID|
|------|--------|--------|---------------|
|1|"Redmond Building 41"|"POLYGON ((-122.1337357922017 47.63782998329432,-122.13373042778369 47.637634793257305,-122.13346757130023 47.637642022530954,-122.13348902897235 47.637508280806806,-122.13361777500506 47.637508280806806,-122.13361241058703 47.63732393354484,-122.13265754417773 47.63730947490855,-122.13266290859576 47.637519124743164,-122.13302232460376 47.637515510097955,-122.13301696018573 47.63764925180358,-122.13272728161212 47.63764925180358,-122.13274873928424 47.63784082716388,-122.13373579220172 47.63782998329432))"|B|
|2|"Redmond Building 40"|"POLYGON ((-122.1336154507967 47.6366745947009,-122.13361008637867 47.636483015064535,-122.13349206918201 47.636479400347675,-122.13349743360004 47.63636372927573,-122.13372810357532 47.63636372927573,-122.13373346799335 47.63617576323771,-122.13263912671528 47.63616491902258,-122.13264985555134 47.63635649982525,-122.13304682248554 47.636367344000604,-122.13305218690357 47.63650831807564,-122.13276250832996 47.636497473929516,-122.13277323716602 47.63668543881025,-122.1336154507967 47.6366745947009))"|Un|
|3|"Redmond building 22"|"POLYGON ((-122.13611660248233 47.63758544698554,-122.13635263687564 47.6374083293018,-122.13622389084293 47.63733603619712,-122.13622389084293 47.63717699101473,-122.13581619507266 47.63692757827657,-122.13559625393344 47.637046862778135,-122.13569281345798 47.637144458985965,-122.13570890671207 47.637314348246214,-122.13611660248233 47.63758544698554))"|C|

### <a name="generate-alerts-with-geofence"></a>Genera avvisi con Geofence

I dispositivi possono emettere l'ID e la posizione ogni minuto tramite un flusso denominato `DeviceStreamInput`. La tabella seguente è un flusso di input.

|DeviceID|GeoPosition|
|--------|-----------|
|Un|"POINT (-122.13292341559497 47.636318374032726)"|
|B|"POINT (-122.13338475554553 47.63743531308874)"|
|C|"POINT (-122.13354001095752 47.63627622505007)"|

È possibile scrivere una query che unisce il flusso del dispositivo con i dati di riferimento geofence e genera un avviso ogni volta che un dispositivo è esterno a un edificio consentito.

```SQL
SELECT DeviceStreamInput.DeviceID, SiteReferenceInput.SiteID, SiteReferenceInput.SiteName 
INTO Output
FROM DeviceStreamInput 
JOIN SiteReferenceInput
ON st_within(DeviceStreamInput.GeoPosition, SiteReferenceInput.Geofence) = 0
WHERE DeviceStreamInput.DeviceID = SiteReferenceInput.AllowedDeviceID
```

L'immagine seguente rappresenta i Geofence. È possibile visualizzare la posizione in cui i dispositivi sono conformi all'input dei dati di flusso.

![Creazione di recinzioni geoschermi](./media/geospatial-scenarios/building-geofences.png)

Il dispositivo "C" si trova all'interno dell'edificio con ID 2, che non è consentito in base ai dati di riferimento. Questo dispositivo deve trovarsi all'interno dell'edificio con ID 3. L'esecuzione di questo processo genererà un avviso per la violazione specifica.

### <a name="site-with-multiple-allowed-devices"></a>Sito con più dispositivi consentiti

Se un sito consente più dispositivi, è possibile definire una matrice di ID dispositivo in `AllowedDeviceID` ed è possibile usare una funzione definita dall'utente nella clausola `WHERE` per verificare se l'ID del dispositivo di flusso corrisponde a qualsiasi ID dispositivo in tale elenco. Per altre informazioni, vedere l'esercitazione relativa a [JavaScript UDF](stream-analytics-javascript-user-defined-functions.md) per i processi cloud e l'esercitazione sulla funzione [ C# definita dall'utente](stream-analytics-edge-csharp-udf.md) per i processi Edge.

## <a name="geospatial-aggregation"></a>Aggregazione geospaziale

Analisi di flusso di Azure supporta un'aggregazione geospaziale in tempo reale a bassa latenza nel cloud e in IoT Edge Runtime.

### <a name="geospatial-aggregation-scenario"></a>Scenario di aggregazione geospaziale

Una società CAB vuole creare un'applicazione in tempo reale per guidare i driver CAB alla ricerca di un percorso verso le aree delle città che attualmente hanno una domanda più elevata.

La società archivia le aree logiche della città come dati di riferimento. Ogni area è definita da un RegionID, un RegionName e un geofence.

### <a name="define-the-geofences"></a>Definire i Geofence

La tabella seguente è un esempio di dati di riferimento Geofence che possono essere archiviati nell'archivio BLOB di Azure o in una tabella SQL di Azure. Ogni area è rappresentata da un poligono geospaziale, che viene usato per la correlazione con le richieste provenienti dai dati in streaming.

Questi poligoni sono solo a scopo di riferimento e non rappresentano le separazioni fisiche o logiche della città effettive.

|RegionID|RegionName|Recinto virtuale|
|--------|----------|--------|
|1|Soho|"POLYGON ((-74.00279525078275 40.72833625216264,-74.00547745979765 40.721929158663244,-74.00125029839018 40.71893680218994,-73.9957785919998 40.72521409075776,-73.9972377137039 40.72557184584898,-74.00279525078275 40.72833625216264) )"|
|2|Chinatown|"POLYGON ((-73.99712367114876 40.71281582267133,-73.9901070123658 40.71336881907936,-73.99023575839851 40.71452359088633,-73.98976368961189 40.71554823078944,-73.99551434573982 40.717337246783735,-73.99480624255989 40.718491949759304,-73.99652285632942 40.719109951574,-73.99776740131233 40.7168005470334,-73.99903340396736 40.71727219249899,-74.00193018970344 40.71938642421256,-74.00409741458748 40.71688186545551,-74.00051398334358 40.71517415773184,-74.0004281526551 40.714377212470005,-73.99849696216438 40.713450141693166,-73.99748845157478 40.71405192594819,-73.99712367114876 40.71281582267133))"|
|3|Tribeca|"POLYGON ((-74.01091641815208 40.72583120006787,-74.01338405044578 40.71436586362705,-74.01370591552757 40.713617702123415,-74.00862044723533 40.711308107057235,-74.00194711120628 40.7194238654018,-74.01091641815208 40.72583120006787))"|

### <a name="aggregate-data-over-a-window-of-time"></a>Aggregare i dati in un intervallo di tempo

La tabella seguente contiene i dati di streaming delle "corse".

|UserID|FromLocation|ToLocation|TripRequestedTime|
|------|------------|----------|-----------------|
|Un|"POINT (-74.00726861389182 40.71610611981975)"|"POINT (-73.98615095917779 40.703107386025835)"|"2019-03-12T07:00:00Z"|
|B|"POINT (-74.00249841021645 40.723827238895666)"|"POINT (-74.01160699942085 40.71378884930115)"|"2019-03-12T07:01:00Z"|
|C|"POINT (-73.99680120565864 40.716439898624024)"|"POINT (-73.98289663412544 40.72582343969828)"|"2019-03-12T07:02:00Z"|
|D|"POINT (-74.00741090068288 40.71615626755086)"|"POINT (-73.97999843120539 40.73477895807408)"|"2019-03-12T07:03:00Z"|

La query seguente unisce il flusso del dispositivo con i dati di riferimento del geofence e calcola il numero di richieste per area in un intervallo di tempo di 15 minuti ogni minuto.

```SQL
SELECT count(*) as NumberOfRequests, RegionsRefDataInput.RegionName 
FROM UserRequestStreamDataInput
JOIN RegionsRefDataInput 
ON st_within(UserRequestStreamDataInput.FromLocation, RegionsRefDataInput.Geofence) = 1
GROUP BY RegionsRefDataInput.RegionName, hoppingwindow(minute, 1, 15)
```

Questa query restituisce un conteggio delle richieste ogni minuto negli ultimi 15 minuti da ogni area all'interno della città. Queste informazioni possono essere visualizzate facilmente dal dashboard Power BI o possono essere trasmesse a tutti i driver come SMS tramite l'integrazione con servizi come funzioni di Azure.

L'immagine seguente illustra l'output della query per Power BI Dashboard. 

![Output risultati nel dashboard Power BI](./media/geospatial-scenarios/power-bi-output.png)


## <a name="next-steps"></a>Passaggi successivi

* [Introduzione alle funzioni geospaziali di analisi di flusso](stream-analytics-geospatial-functions.md)
* [Funzioni geospaziali (analisi di flusso di Azure)](https://docs.microsoft.com/stream-analytics-query/geospatial-functions)
