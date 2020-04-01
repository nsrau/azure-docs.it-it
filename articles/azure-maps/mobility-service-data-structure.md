---
title: Strutture di dati del servizio Mobility in Azure Maps Mappe di Microsoft Azure
description: In this article, you will learn about common fields and data structures returned via the Microsoft Azure Maps Mobility Services.
author: philmea
ms.author: philmea
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 4dfc6793bba473c4046863937baa292dde7bf421
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478696"
---
# <a name="data-structures-in-azure-maps-mobility-service"></a>Strutture di dati nel servizio per dispositivi mobili di Azure MapsData structures in Azure Maps Mobility Service

Questo articolo introduce il concetto di area metropolitana nel servizio [di mobilità di Mappe](https://aka.ms/AzureMapsMobilityService)di Azure. Vengono illustrati alcuni dei campi comuni restituiti quando viene eseguita una query su questo servizio per le soste e le linee di transito pubblico. È consigliabile leggere questo articolo prima di eseguire lo sviluppo con le API del servizio Mobility.

## <a name="metro-area"></a>Area metropolitana

I dati del servizio di mobilità sono raggruppati in base alle aree metropolitane supportate. Le aree metropolitane non seguono i confini della città. Un'area metropolitana può contenere più città, città densamente popolate e città circostanti. Infatti, un paese /regione può essere un'area metropolitana. 

Il `metroID` è l'ID di un'area metropolitana che può essere utilizzato per chiamare [l'API Get Metro Area Info](https://aka.ms/AzureMapsMobilityMetroAreaInfo). Usa l'API "Ottieni Metro" di Azure Maps per richiedere tipi di transito, agenzie di transito, avvisi attivi e dettagli aggiuntivi per la metropolitana scelta. È inoltre possibile richiedere le aree metropolitane supportate e metroIDe. Gli ID dell'area metropolitana sono soggetti a modifiche.

**metroID:** 522 **Nome:** Seattle-Tacoma-Bellevue

![Seattle-area metropolitana](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>Arresta GLI IMPONitori

Le soste di transito possono essere indicate da due tipi di ID, l'ID [GFTS (General Transit Feed Specification)](http://gtfs.org/) e l'ID arresto di Azure Maps. L'ID GFTS viene definito stopKey e l'ID arresto di Azure Maps viene definito stopID. Quando si fa spesso riferimento alle soste di transito, si consiglia di usare l'ID di arresto di Azure Maps.When frequently refer to transit stops, you're encouraged to use the Azure Maps stop ID. stopID è più stabile e probabilmente rimarrà lo stesso finché esiste l'arresto fisico. L'ID di arresto GTFS viene aggiornato più spesso. Ad esempio, l'ID di arresto GTFS può essere aggiornato in base alla richiesta del provider GTFS o quando viene rilasciata una nuova versione GTFS. Anche se l'arresto fisico non ha subito modifiche, l'ID di arresto GTFS potrebbe cambiare.

Per iniziare, è possibile richiedere le interruzioni di transito nelle vicinanze utilizzando [Get Nearby Transit API](https://aka.ms/AzureMapsMobilityNearbyTransit).

## <a name="line-groups-and-lines"></a>Gruppi di linee e linee

Mobility Service usa un modello di dati parallelo per linee e gruppi di linee. Questo modello viene utilizzato per gestire meglio le modifiche ereditate dai percorsi [GTFS](http://gtfs.org/) e dai dati dei viaggi.


### <a name="line-groups"></a>Gruppi di linee

Un gruppo di linee è un'entità che raggruppa tutte le righe che fanno logicamente parte dello stesso gruppo. In genere, un gruppo di linee contiene due linee, una dal punto A al punto B e l'altra che ritorna dal punto B ad A. Entrambe le righe apparterrebbero alla stessa agenzia di trasporto pubblico e avrebbero lo stesso numero di riga. Tuttavia, ci possono essere casi in cui un gruppo di linee ha più di due righe o solo una singola riga al suo interno.


### <a name="lines"></a>Linee

Come illustrato in precedenza, ogni gruppo di linee è composto da un insieme di righe. Ogni gruppo di linee è composto da due righe e ogni linea descrive una direzione.  Tuttavia, ci sono casi in cui più linee compongono un gruppo di linee. Per esempio, c'è una linea che a volte scorre attraverso un certo quartiere e a volte no. In entrambi i casi, opera sotto lo stesso numero di riga. Anche un gruppo di linee può essere composto da una singola riga. Una linea circolare con una singola direzione è un gruppo di ling con una linea.

Per iniziare, è possibile richiedere gruppi di righe utilizzando [l'API Get Transit Line](https://aka.ms/AzureMapsMobilityTransitLine).


## <a name="next-steps"></a>Passaggi successivi

Informazioni su come richiedere dati di transito utilizzando il servizio Mobility:Learn how to request transit data using Mobility Service:

> [!div class="nextstepaction"]
> [Come richiedere i dati di transito](how-to-request-transit-data.md)

Scopri come richiedere dati in tempo reale utilizzando il servizio Mobility:

> [!div class="nextstepaction"]
> [Come richiedere dati in tempo reale](how-to-request-real-time-data.md)

Esplorare la documentazione dell'API del servizio per dispositivi mobili di Azure MapsExplore the Azure Maps Mobility Service API documentation

> [!div class="nextstepaction"]
> [Documentazione dell'API del servizio Mobility](https://aka.ms/AzureMapsMobilityService)
