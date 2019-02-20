---
title: Reagire agli eventi di Mappe di Azure con Griglia di eventi | Microsoft Docs
description: Informazioni su come rispondere agli eventi di Mappe di Azure con Griglia di eventi.
author: walsehgal
ms.author: v-musehg
ms.date: 02/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: a70011b934398ac4e7f74bb67013e93bb5e86e4e
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2019
ms.locfileid: "56007839"
---
# <a name="react-to-azure-maps-events-by-using-event-grid"></a>Rispondere agli eventi di Mappe di Azure con Griglia di eventi 

Mappe di Azure si integra con Griglia di eventi di Azure per poter inviare le notifiche degli eventi agli altri servizi e attivare processi downstream. Lo scopo di questo articolo è fornire assistenza per la configurazione delle applicazioni aziendali per l'ascolto degli eventi di Mappe di Azure in modo che sia possibile rispondere agli eventi critici in modo sicuro, scalabile e affidabile. Compilare, ad esempio, un'applicazione per eseguire più azioni, come l'aggiornamento di un database, la creazione di un ticket e il recapito di una notifica di posta elettronica, ogni volta che un dispositivo entra in un recinto virtuale.

Griglia di eventi di Azure è un servizio di routing di eventi completamente gestito che usa un modello di pubblicazione-sottoscrizione. Griglia di eventi include il supporto predefinito per i servizi di Azure, ad esempio [Funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-overview) e [App per la logica di Azure](https://docs.microsoft.com/azure/azure-functions/functions-overview), e può recapitare gli avvisi relativi agli eventi ai servizi non di Azure usando i webhook. Per un elenco completo dei gestori di eventi supportati da Griglia di eventi, vedere [Introduzione a Griglia di eventi di Azure](https://docs.microsoft.com/azure/event-grid/overview).


![Modello funzionale di Griglia di eventi di Azure](./media/azure-maps-event-grid-integration/azure-event-grid-functional-model.png)


## <a name="azure-maps-events-types"></a>Tipi di eventi di Mappe di Azure

Griglia di eventi usa le [sottoscrizioni di eventi](https://docs.microsoft.com/azure/event-grid/concepts#event-subscriptions) per instradare i messaggi di evento ai sottoscrittori. Un account di Mappe di Azure genera i tipi di eventi seguenti: 

| Tipo evento | DESCRIZIONE |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | Generato quando le coordinate ricevute sono state spostate dall'esterno all'interno di un determinato recinto virtuale |
| Microsoft.Maps.GeofenceExited | Generato quando le coordinate ricevute sono state spostate dall'interno all'esterno di un determinato recinto virtuale |
| Microsoft.Maps.GeofenceResult | Generato ogni volta che una query di geofencing restituisce un risultato, indipendentemente dallo stato |

## <a name="event-schema"></a>Schema di eventi

L'esempio seguente illustra lo schema di GeofenceResult

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

## <a name="tips-for-consuming-events"></a>Suggerimenti per l'utilizzo di eventi

Per le applicazioni che gestiscono gli eventi di un recinto virtuale di Mappe di Azure è consigliabile seguire alcune procedure:

* È possibile configurare più sottoscrizioni per instradare gli eventi allo stesso gestore dell'evento. È importante non presupporre che gli eventi provengano da un'origine in particolare. Controllare sempre l'argomento del messaggio per assicurarsi che provengano dall'origine prevista.
* I messaggi possono arrivare senza ordine o dopo un ritardo. Usare il campo `X-Correlation-id` nell'intestazione della risposta per determinare se le informazioni sugli oggetti sono aggiornate.
* Quando vengono chiamate le API Geofence Get e POST con il parametro di modalità impostato su `EnterAndExit`, viene generato un evento di invio o di uscita per ogni geometria nel recinto virtuale per il quale lo stato è cambiato rispetto alla precedente chiamata API Geofence.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come usare il geofencing per il controllo di operazioni in un cantiere, vedere:

> [!div class="nextstepaction"] 
> [Set up a geofence by using Azure Maps](tutorial-geofence.md) (Configurare un recinto virtuale tramite Mappe di Azure)