---
title: Strutture di dati del servizio Mobility nelle mappe di Azure | Microsoft Docs
description: Strutture di dati nel servizio di mobilità delle mappe di Azure
author: walsehgal
ms.author: v-musehg
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 29e8a9d7555ca836b6266879f3b3c1e32ffd3980
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66735557"
---
# <a name="data-structures-in-azure-maps-mobility-service"></a>Strutture di dati nel servizio di mobilità delle mappe di Azure

Questo articolo introduce il concetto di Area metropolitana [il servizio Mobility di Azure esegue il mapping](https://aka.ms/AzureMapsMobilityService) e alcuni dei campi comuni restituite tramite i servizi, quando vengono richieste per trasporto pubblico viene interrotta e righe. È consigliabile consultare questo articolo prima di iniziare con le API del servizio Mobility. Vengono illustrati questi campi comuni seguenti.

## <a name="metro-area"></a>Area tracciato

I dati del servizio Mobility sono suddiviso in aree metropolitane supportate. Aree metropolitane non seguono i limiti di città, un'area metropolitana può contenere più città, ad esempio città densamente popolate e le città circostante; e un paese/area geografica può essere un'area tracciata. 

Il `metroID` è l'ID dell'area metropolitana che può essere usato per chiamare il [Metro Area Info API Get](https://aka.ms/AzureMapsMobilityMetroAreaInfo) ai tipi di transito richiesta supportati e altri dettagli per l'area metropolitana, ad esempio gli avvisi attivi e agenzie di transito. È possibile utilizzare l'API di Metro ottenere mappe di Azure per richiedere le aree supportate della metropolitana e metroIDs. ID area metropolitana sono soggette a modifiche.

**metroID:** 522 **nome:** Seattle-Tacoma-Bellevue

![Area di Seattle metro](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>Arrestare gli ID

Transito si arresta possa farvi riferimento.{0}{0}i due tipi di ID, il [generale transito Feed specifica (GFTS)](https://gtfs.org/) ID (detto stopKey) e le mappe di Azure arresta ID (detto stopId). Quando si fa riferimento a viene arrestata nel tempo, è consigliabile usare l'ID di arrestare le mappe di Azure, come questo ID è più stabile e non verrà probabilmente modificato finché esiste l'arresto fisico. L'ID arresto GTFS viene aggiornato più spesso, ad esempio, nel caso in cui il provider GTFS dovrà modificarla o nuova versione GTFS viene rilasciato, anche se il punto di arresto non era alcuna modifica.

Per iniziare, è possibile richiedere transito nelle vicinanze interrompe utilizzando [API Get nelle vicinanze transito](https://aka.ms/AzureMapsMobilityNearbyTransit).

## <a name="line-groups-and-lines"></a>I gruppi di riga e linee

Il servizio Mobility Usa un modello di dati paralleli per le righe e gruppi di riga per gestire meglio le modifiche alla ereditata da [GTFS](https://gtfs.org/) modello di dati di route e corse.


### <a name="line-groups"></a>Gruppi di riga

Un gruppo di linee è un'entità, che raggruppa tutte le righe che fanno logicamente parte dello stesso gruppo. In genere un gruppo di linee conterrà due righe, uno passando da punto A B, la restituzione da punto B ad A, entrambi appartenente all'agenzia di trasporti pubblici stesso e con lo stesso numero di riga. Tuttavia, potrebbero esserci casi in cui un gruppo di linee con più di due righe o solo una singola riga all'interno di esso.


### <a name="lines"></a>Righe

Come illustrato in precedenza, ogni gruppo di righe è composto da un set di righe. Spesso ciascuna riga descrive una direzione e ogni riga del gruppo è costituito da due righe. Tuttavia ci sono casi in cui più righe costituiscono un gruppo di linee, ad esempio non esiste una riga che talvolta devia tramite un determinato nodo vicino e a volte non esiste e viene gestito in entrambi i casi con lo stesso numero di riga e altri casi in cui una riga g ruppa è costituito da una singola riga, ad esempio una linea circolare con un'unica direzione.

Per iniziare, è possibile richiedere i gruppi di riga usando la [ottenere transito riga API](https://aka.ms/AzureMapsMobilityTransitLine) e versioni successive drill-down righe.


## <a name="next-steps"></a>Passaggi successivi

Informazioni su come richiedere i dati di transito tramite il servizio Mobility:

> [!div class="nextstepaction"]
> [Come richiedere i dati di transito](how-to-request-transit-data.md)

Informazioni su come richiedere i dati in tempo reale usando il servizio Mobility:

> [!div class="nextstepaction"]
> [Come richiedere i dati in tempo reale](how-to-request-real-time-data.md)

Esplorare la documentazione dell'API servizio Mobility mappe di Azure

> [!div class="nextstepaction"]
> [Documentazione dell'API del servizio Mobility](https://aka.ms/AzureMapsMobilityService)
