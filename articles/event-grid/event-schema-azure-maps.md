---
title: Schema di eventi di Mappe di Azure di Griglia di eventi di Azure
description: Descrive le proprietà e lo schema disponibili per gli eventi di Mappe di Azure con Griglia di eventi di Azure
services: event-grid
author: walsehgal
ms.service: event-grid
ms.topic: reference
ms.date: 02/08/2019
ms.author: v-musehg
ms.openlocfilehash: 74a3674e632f8dc3f0755bc2ad48376708c7966f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60861855"
---
# <a name="azure-event-grid-event-schema-for-azure-maps"></a>Schema di eventi di Griglia di eventi di Azure per Mappe di Azure

Questo articolo illustra le proprietà e lo schema per gli eventi di Mappe di Azure. Per un'introduzione agli schemi di eventi, vedere [Schema di eventi di Griglia di eventi di Azure](https://docs.microsoft.com/azure/event-grid/event-schema).

## <a name="available-event-types"></a>Tipi di evento disponibili

Un account di Mappe di Azure genera i tipi di eventi seguenti:

| Tipo evento | DESCRIZIONE |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | Generato quando le coordinate ricevute sono state spostate dall'esterno all'interno di un determinato recinto virtuale |
| Microsoft.Maps.GeofenceExited | Generato quando le coordinate ricevute sono state spostate dall'interno all'esterno di un determinato recinto virtuale |
| Microsoft.Maps.GeofenceResult | Generato ogni volta che una query di geofencing restituisce un risultato, indipendentemente dallo stato |

## <a name="event-examples"></a>Esempi di eventi

L'esempio seguente illustra lo schema di un evento **GeofenceEntered**

```JSON
{   
   "id":"7f8446e2-1ac7-4234-8425-303726ea3981", 
   "topic":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
   "subject":"/spatial/geofence/udid/{udid}/id/{eventId}", 
   "data":{   
      "geometries":[   
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":-999.0, 
            "nearestLat":47.618786, 
            "nearestLon":-122.132151 
         } 
      ], 
      "expiredGeofenceGeometryId":[   
      ], 
      "invalidPeriodGeofenceGeometryId":[   
      ] 
   }, 
   "eventType":"Microsoft.Maps.GeofenceEntered", 
   "eventTime":"2018-11-08T00:54:17.6408601Z", 
   "metadataVersion":"1", 
   "dataVersion":"1.0" 
}
```

L'esempio seguente illustra lo schema di **GeofenceResult** 

```JSON
{   
   "id":"451675de-a67d-4929-876c-5c2bf0b2c000", 
   "topic":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
   "subject":"/spatial/geofence/udid/{udid}/id/{eventId}", 
   "data":{   
      "geometries":[   
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"1", 
            "distance":999.0, 
            "nearestLat":47.609833, 
            "nearestLon":-122.148274 
         }, 
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":999.0, 
            "nearestLat":47.621954, 
            "nearestLon":-122.131841 
         } 
      ], 
      "expiredGeofenceGeometryId":[   
      ], 
      "invalidPeriodGeofenceGeometryId":[   
      ] 
   }, 
   "eventType":"Microsoft.Maps.GeofenceResult", 
   "eventTime":"2018-11-08T00:52:08.0954283Z", 
   "metadataVersion":"1", 
   "dataVersion":"1.0" 
}
```

## <a name="event-properties"></a>Proprietà degli eventi

Un evento presenta i seguenti dati di primo livello:

| Proprietà | Type | DESCRIZIONE |
| -------- | ---- | ----------- |
| argomento | string | Percorso risorsa completo dell'origine evento. Questo campo non è scrivibile. Questo valore viene fornito da Griglia di eventi. |
| subject | string | Percorso dell'oggetto dell'evento definito dall'autore. |
| eventType | string | Uno dei tipi di evento registrati per l'origine evento. |
| eventTime | string | Ora di generazione dell'evento in base all'ora UTC del provider. |
| id | string | Identificatore univoco dell'evento. |
| data | object | Dati dell'evento di geofencing. |
| dataVersion | string | Versione dello schema dell'oggetto dati. La versione dello schema è definita dall'editore. |
| metadataVersion | string | Versione dello schema dei metadati dell'evento. Lo schema delle proprietà di primo livello è definito da Griglia di eventi. Questo valore viene fornito da Griglia di eventi. |

Di seguito sono elencate le proprietà dell'oggetto dati:

| Proprietà | Type | DESCRIZIONE |
| -------- | ---- | ----------- |
| apiCategory | string | Categoria API dell'evento. |
| apiName | string | Nome API dell'evento. |
| issues | object | Elenca i problemi riscontrati durante l'elaborazione. Se vengono restituiti problemi, la risposta non includerà geometrie. |
| responseCode | number | Codice di risposta HTTP |
| geometries | object | Elenca le geometrie del recinto che contengono la posizione della coordinata o si sovrappongono a searchBuffer intorno alla posizione. |

L'oggetto error viene restituito quando si verifica un errore nell'API Mappe. L'oggetto error ha le proprietà seguenti:

| Proprietà | Type | DESCRIZIONE |
| -------- | ---- | ----------- |
| error | ErrorDetails |Questo oggetto viene restituito quando si verifica un errore nell'API Mappe  |

L'oggetto ErrorDetails viene restituito quando si verifica un errore nell'API Mappe. L'oggetto ErrorDetails ha le proprietà seguenti:

| Proprietà | Type | DESCRIZIONE |
| -------- | ---- | ----------- |
| code | string | Codice di stato HTTP. |
| message | string | Se disponibile, una descrizione leggibile dell'errore. |
| innererror | InnerError | Se disponibile, un oggetto contenente informazioni specifiche del servizio relative all'errore. |

InnerError è un oggetto contenente informazioni specifiche del servizio relative all'errore. L'oggetto InnerError ha le proprietà seguenti: 

| Proprietà | Type | DESCRIZIONE |
| -------- | ---- | ----------- |
| code | string | Messaggio di errore. |

L'oggetto geometries elenca gli ID delle geometrie dei recinti virtuali che sono scaduti rispetto all'ora utente nella richiesta. L'oggetto geometries include elementi di geometria con le proprietà seguenti: 

| Proprietà | Type | DESCRIZIONE |
|:-------- |:---- |:----------- |
| deviceid | string | ID del dispositivo. |
| distance | string | <p>Distanza dalla coordinata al bordo più vicino del recinto virtuale. Un valore positivo indica che la coordinata è all'esterno del recinto virtuale. Se la coordinata è all'esterno del recinto virtuale, ma più lontana del valore di searchBuffer dal bordo del recinto virtuale più vicino, il valore è 999. Un valore negativo indica che la coordinata è all'interno del recinto virtuale. Se la coordinata è all'interno del poligono, ma più lontana del valore di searchBuffer dal bordo del recinto virtuale più vicino, il valore è -999. Un valore pari a 999 indica che è molto probabile che la coordinata sia all'esterno del recinto virtuale. Un valore pari a -999 indica che è molto probabile che la coordinata sia all'interno del recinto virtuale.<p> |
| geometryid |string | ID univoco che identifica la geometria del recinto virtuale. |
| nearestlat | number | Latitudine del punto più vicino della geometria. |
| nearestlon | number | Longitudine del punto più vicino della geometria. |
| udId | string | ID univoco restituito dal servizio di caricamento utente durante il caricamento di un recinto virtuale. Non verrà incluso nell'API POST di geofencing. |

Di seguito sono elencate le proprietà dell'oggetto dati:

| Proprietà | Type | DESCRIZIONE |
| -------- | ---- | ----------- |
| expiredGeofenceGeometryId | string[] | Elenca gli ID delle geometrie del recinto virtuale che sono scaduti rispetto all'ora utente nella richiesta. |
| geometries | geometries[] |Elenca le geometrie del recinto che contengono la posizione della coordinata o si sovrappongono a searchBuffer intorno alla posizione. |
| invalidPeriodGeofenceGeometryId | string[]  | Elenca gli ID delle geometrie del recinto virtuale che sono inclusi in un periodo non valido rispetto all'ora utente nella richiesta. |
| isEventPublished | boolean | True se almeno un evento viene pubblicato nel sottoscrittore di eventi di Mappe di Azure, false non viene pubblicato alcun evento nel sottoscrittore di eventi di Mappe di Azure. |

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a Griglia di eventi di Azure, vedere [Informazioni su Griglia di eventi](overview.md)
* Per altre informazioni sulla creazione di una sottoscrizione di Griglia di eventi di Azure, vedere [Schema di sottoscrizione per Griglia di eventi](subscription-creation-schema.md).