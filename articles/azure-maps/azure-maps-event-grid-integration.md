---
title: Rispondere agli eventi di Mappe di Azure con Griglia di eventi
description: Informazioni su come reagire agli eventi di Maps di Azure che coinvolgono le reti geografiche. Vedere come ascoltare gli eventi del mapping e come usare griglia di eventi per reindirizzare gli eventi ai gestori eventi.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/16/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 701c94237ef5348e11b5d7fbc85d4da1f20136ee
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88036811"
---
# <a name="react-to-azure-maps-events-by-using-event-grid"></a>Rispondere agli eventi di Mappe di Azure con Griglia di eventi

Azure Maps si integra con griglia di eventi di Azure, in modo che gli utenti possano inviare notifiche degli eventi ad altri servizi e attivare processi downstream. Lo scopo di questo articolo è semplificare la configurazione delle applicazioni aziendali per l'ascolto degli eventi di Azure maps. Ciò consente agli utenti di rispondere a eventi critici in modo affidabile, scalabile e sicuro. Ad esempio, gli utenti possono compilare un'applicazione per aggiornare un database, creare un ticket e recapitare una notifica di posta elettronica, ogni volta che un dispositivo immette una rete perimetrale.

Griglia di eventi di Azure è un servizio di routing di eventi completamente gestito che usa un modello di pubblicazione-sottoscrizione. Griglia di eventi include il supporto incorporato per i servizi di Azure, come [funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-overview) e app per la [logica di Azure](https://docs.microsoft.com/azure/azure-functions/functions-overview). Può inviare avvisi di eventi a servizi non di Azure usando i webhook. Per un elenco completo dei gestori di eventi supportati da Griglia di eventi, vedere [Introduzione a Griglia di eventi di Azure](https://docs.microsoft.com/azure/event-grid/overview).


![Modello funzionale di Griglia di eventi di Azure](./media/azure-maps-event-grid-integration/azure-event-grid-functional-model.png)


## <a name="azure-maps-events-types"></a>Tipi di eventi di Mappe di Azure

Griglia di eventi usa le [sottoscrizioni di eventi](https://docs.microsoft.com/azure/event-grid/concepts#event-subscriptions) per instradare i messaggi di evento ai sottoscrittori. Un account di Mappe di Azure genera i tipi di eventi seguenti: 

| Tipo di evento | Descrizione |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | Generato quando le coordinate ricevute sono state spostate dall'esterno di una data Fence specificata all'interno di |
| Microsoft.Maps.GeofenceExited | Generato quando le coordinate ricevute sono state spostate dall'interno di una determinata georecinzione all'esterno |
| Microsoft.Maps.GeofenceResult | Generato ogni volta che una query di geofencing restituisce un risultato, indipendentemente dallo stato |

## <a name="event-schema"></a>Schema di eventi

Nell'esempio seguente viene illustrato lo schema per GeofenceResult:

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

* Configurare più sottoscrizioni per indirizzare gli eventi allo stesso gestore eventi. È importante non presupporre che gli eventi provengano da un'origine in particolare. Controllare sempre l'argomento del messaggio per verificare che il messaggio provenga dall'origine prevista.
* Usare il `X-Correlation-id` campo nell'intestazione della risposta per capire se le informazioni sugli oggetti sono aggiornate. I messaggi possono arrivare non in ordine o dopo un ritardo.
* Quando una richiesta GET o POST nell'API Geofence viene chiamata con il parametro mode impostato su `EnterAndExit` , viene generato un evento Enter o Exit per ogni geometria nel Geofence per cui lo stato è stato modificato rispetto alla precedente chiamata API di Geofence.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come usare il geofencing per il controllo di operazioni in un cantiere, vedere:

> [!div class="nextstepaction"] 
> [Configurare un recinto virtuale con Mappe di Azure](tutorial-geofence.md)
