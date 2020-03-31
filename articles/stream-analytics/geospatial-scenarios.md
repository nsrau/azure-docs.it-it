---
title: Geofencing and geospatial aggregation with Azure Stream Analytics
description: Questo articolo descrive come usare Analisi di flusso di Azure per la geofencing e l'aggregazione geospaziale.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 5a3aa3786469c3df37b53cb82bdd396871689297
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443648"
---
# <a name="geofencing-and-geospatial-aggregation-scenarios-with-azure-stream-analytics"></a>Geofencing and geospatial aggregation scenarios with Azure Stream Analytics

Con le funzioni geospaziali integrate, puoi usare Analisi di flusso di Azure per creare applicazioni per scenari come la gestione della flotta, la condivisione delle corse, le auto connesse e il monitoraggio degli asset.

## <a name="geofencing"></a>Geofencing

Analisi di flusso di Azure supporta calcoli di geofencing in tempo reale a bassa latenza nel cloud e nel runtime di IoT Edge.Azure Stream Analytics supports low latency real-time geofencing computations in the cloud and on the IoT Edge runtime.

### <a name="geofencing-scenario"></a>Scenario di geofencing

Un'azienda manifatturiera deve tenere traccia delle risorse sui propri edifici. Hanno equipaggiato ogni dispositivo con un GPS e vogliono ricevere notifiche se un dispositivo lascia una certa area.

I dati di riferimento utilizzati in questo esempio contraddittori e sono disponibili per gli edifici e i dispositivi consentiti in ciascuno degli edifici. Tenere presente che i dati di riferimento potrebbero essere statici o a modifica lenta. Per questo scenario vengono utilizzati dati di riferimento statici. Un flusso di dati emette continuamente l'ID del dispositivo e la posizione corrente.

### <a name="define-geofences-in-reference-data"></a>Definire i recinti geografici nei dati di riferimentoDefine geofences in reference data

Un geofence può essere definito utilizzando un oggetto GeoJSON. Per i lavori con compatibilità versione 1.2 e successive, i recinti `NVARCHAR(MAX)`geografici possono essere definiti anche utilizzando il testo noto (WKT) come . WKT è uno standard OGC (Open Geospatial Consortium) utilizzato per rappresentare i dati spaziali in un formato testuale.

Le funzioni geospaziali integrate possono utilizzare georeghi definiti per scoprire se un elemento si trova all'esterno o all'esterno di uno specifico poligono di geofence.

La tabella seguente è un esempio di dati di riferimento geofence che possono essere archiviati nell'archiviazione BLOB di Azure o in una tabella SQL di Azure.The following table is an example of geofence reference data that could be stored in Azure blob storage or an Azure SQL table. Ogni sito è rappresentato da un poligono geospaziale e ogni dispositivo è associato a un ID sito consentito.

|Siteid|SiteName|Geofence|AllowedDeviceID|
|------|--------|--------|---------------|
|1|"Edificio di Redmond 41"|"POLYGON((-122.1337357922017 47.63782998329432,-122.13373042778369 47.637634793257305,-1222.1334675713002347.6376262622622622622622622626262 022530954,-122.13348902897235 47.637508280806806,-122.1336177750506 47.637508280806806,-122.133612410580 47.63732393354484,-122.13265754417773 47.63730947490855,-122.13266290859576 47.637519124743164,-122.13302232460376 47.637515510097955,-122.13301696018573 47.63764925180358,-122.13272728161212 47.63764925180358,-122.13274873928424 47.63784082716388,-122.13373579220172 47.63782998329432))"|"B"|
|2|"Redmond Building 40"|"POLYGON((-122.1336154507967 47.6366745947009,-122.13361008637867 47.636483015064535,-122.13349206918201 47.636649 400347675,-122.13349743360004 47.63636372927573,-122.13372810357532 47.63636372927573,-122.1337334679933535353333377933533337793353333337793353337793353533333779335333377933337793333379933333779333377933337793333 47.63617576323771,-122.13263912671528 47.63616491902258,-122.13264985555134 47.63635649982525,-122.13304682248554 47.636367344000604,-122.13305218690357 47.63650831807564,-122.13276250832996 47.636497473929516,-122.13277323716602 47.63668543881025,-122.1336154507967 47.6366745947009))"|"A"|
|3|"Edificio Redmond 22"|"POLYGON((122.13611660248233 47.6375854698554,122.13635263677564 47.6374083293018,-122.13622389084293333333333333333333333333 603619712,-122.13622389084293 47.63717699101473,-122.13581619507266 47.63692757827657,-122.1355962533344444444444444444444444444444444444444444444444444 47.637046862778135,-122.13569281345798 47.637144458985965,-122.13570890671207 47.637314348246214,-122.13611660248233 47.63758544698554))"|"C"|

### <a name="generate-alerts-with-geofence"></a>Genera avvisi con geofence

I dispositivi possono emettere il loro `DeviceStreamInput`ID e la posizione ogni minuto attraverso un flusso chiamato . La tabella seguente è un flusso di input.

|DeviceID|Geoposition|
|--------|-----------|
|"A"|"PUNTO(-122.13292341559497 47.636318374032726)"|
|"B"|"PUNTO(-122.13338475554553 47.63743531308874)"|
|"C"|"PUNTO(-122.13354001095752 47.63627622505007)"|

È possibile scrivere una query che unisce il flusso del dispositivo con i dati di riferimento geofence e genera un avviso ogni volta che un dispositivo si trova all'esterno di una compilazione consentita.

```SQL
SELECT DeviceStreamInput.DeviceID, SiteReferenceInput.SiteID, SiteReferenceInput.SiteName 
INTO Output
FROM DeviceStreamInput 
JOIN SiteReferenceInput
ON st_within(DeviceStreamInput.GeoPosition, SiteReferenceInput.Geofence) = 0
WHERE DeviceStreamInput.DeviceID = SiteReferenceInput.AllowedDeviceID
```

L'immagine seguente rappresenta i recinti geografici. Puoi vedere dove si trovano i dispositivi in base all'input dei dati del flusso.

![Costruzione di recinti geografici](./media/geospatial-scenarios/building-geofences.png)

Il dispositivo "C" si trova all'interno dell'ID edificio 2, che non è consentito in base ai dati di riferimento. Questo dispositivo deve trovarsi all'interno dell'ID edificio 3. L'esecuzione di questo processo genererà un avviso per questa violazione specifica.

### <a name="site-with-multiple-allowed-devices"></a>Sito con più dispositivi consentiti

Se un sito consente più dispositivi, è possibile definire `AllowedDeviceID` un array di ID dispositivo `WHERE` e utilizzare una funzione definita dall'utente nella clausola per verificare se l'ID del dispositivo di flusso corrisponde a qualsiasi ID dispositivo in tale elenco. Per altre informazioni, vedere l'esercitazione [sulla funzione definita dall'uso di Javascript](stream-analytics-javascript-user-defined-functions.md) per i processi cloud e l'esercitazione sulla funzione definita dall'utente di [C'è](stream-analytics-edge-csharp-udf.md) per i processi perimetrali.

## <a name="geospatial-aggregation"></a>Aggregazione geospaziale

Analisi di flusso di Azure supporta l'aggregazione geospaziale in tempo reale a bassa latenza nel cloud e nel runtime di IoT Edge.Azure Stream Analytics supports low latency real-time geospatial aggregation in the cloud and on the IoT Edge runtime.

### <a name="geospatial-aggregation-scenario"></a>Scenario di aggregazione geospaziale

Un'azienda di taxi vuole costruire un'applicazione in tempo reale per guidare i loro tassisti in cerca di un giro verso le aree delle città che attualmente stanno vivendo una domanda più elevata.

L'azienda memorizza le regioni logiche della città come dati di riferimento. Ogni area è definita da un RegionID, RegionName e Geofence.Each region is defined by a RegionID, RegionName, and Geofence.

### <a name="define-the-geofences"></a>Definire i recinti geografici

La tabella seguente è un esempio di dati di riferimento geofence che possono essere archiviati nell'archiviazione BLOB di Azure o in una tabella SQL di Azure.The following table is an example of geofence reference data that could be stored in Azure blob storage or an Azure SQL table. Ogni area è rappresentata da un poligono geospaziale, che viene usato per correlare con le richieste provenienti dai dati di streaming.

Questi poligoni sono solo per riferimento e non rappresentano le separazioni logiche o fisiche effettive della città.

|RegionID|RegionName|Geofence|
|--------|----------|--------|
|1|"SoHo"|"POLYGON((-74.00279525078275 40.72833625216264,-74.0054774597977 65 40.721929158663244,-74.00125029839018 40.718936802189 94,-73.9957785919998 40.72521409075776,-73.9972377137039 40.72557184584898,-74.00279525078275 40.72833625216264) )"|
|2|"Chinatown"|"POLYGON((-73.997123671114876 40.71281582267133,-73.9901070123658 40.71336881907936,-73.9902375839851 40.7145233 5908633,-73.98976368961189 40.715548230789444,-73.99551434573982 40.7173372467837355,-73.99480624255989 40.718491949759304,-73.99652285632942 40.719109951574,-73.99776740131233 40.7168005470334,-73.99903340396736 40.71727219249899,-74.00193018970344 40.71938642421256,-74.00409741458748 40.71688186545551,-74.00051398334358 40.71517415773184,-74.0004281526551 40.714377212470005,-73.99849696216438 40.713450141693166,-73.99748845157478 40.71405192594819,-73.99712367114876 40.71281582267133))"|
|3|"Tribeca"|"POLYGON((-74.01091641815208 40.72583120006787,-74.01338405044578 40.71436586362705,-74.01370591552757 40.71361 7702123415,-74.0086204723533 40.711308107057235,-74.00194711120628 40.7194238654018,-74.01091641815208 40.72583120006787))"|

### <a name="aggregate-data-over-a-window-of-time"></a>Aggregare i dati in un intervallo di tempo

La tabella seguente contiene i dati in streaming di "ride".

|UserID|FromLocation|ToLocation|TripRequestedTime (Ora di viaggio)|
|------|------------|----------|-----------------|
|"A"|"PUNTO(-74.00726861389182 40.71610611981975)"|"PUNTO(-73.98615095917779 40.703107386025835)"|"2019-03-12T07:00:00"|
|"B"|"PUNTO(-74.00249841021645 40.723827238895666)"|"PUNTO(-74.01160699942085 40.71378884930115)"|"2019-03-12T07:01:00"|
|"C"|"PUNTO(-73.99680120565864 40.716439898624024)"|"PUNTO(-73.98289663412544 40.72582343969828)"|"2019-03-12T07:02:00"|
|"D"|"PUNTO(-74.00741090068288 40.7161562675086)"|"PUNTO(-73.97999843120539 40.73477895807408)"|"2019-03-12T07:03:00"|

La query seguente unisce il flusso del dispositivo con i dati di riferimento dell'oggetto geofence e calcola il numero di richieste per area in un intervallo di tempo di 15 minuti ogni minuto.

```SQL
SELECT count(*) as NumberOfRequests, RegionsRefDataInput.RegionName 
FROM UserRequestStreamDataInput
JOIN RegionsRefDataInput 
ON st_within(UserRequestStreamDataInput.FromLocation, RegionsRefDataInput.Geofence) = 1
GROUP BY RegionsRefDataInput.RegionName, hoppingwindow(minute, 1, 15)
```

Questa query restituisce un conteggio di richieste ogni minuto per gli ultimi 15 minuti per ogni area all'interno della città. Queste informazioni possono essere visualizzate facilmente dal dashboard di Power BI o possono essere trasmesse a tutti i driver come SMS tramite l'integrazione con servizi come le funzioni di Azure.This information can be displayed easily by Power BI dashboard, or can be broadcasted to all drivers as SMS text messages through integration with services like Azure functions.

L'immagine seguente illustra l'output della query nel dashboard di Power BI. 

![Output dei risultati nel dashboard di Power BIResult output on Power BI dashboard](./media/geospatial-scenarios/power-bi-output.png)


## <a name="next-steps"></a>Passaggi successivi

* [Introduzione alle funzioni geospaziali di Analisi di flusso](stream-analytics-geospatial-functions.md)
* [Funzioni geospaziali (Azure Stream Analytics)GeoSpatial Functions (Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/geospatial-functions)
