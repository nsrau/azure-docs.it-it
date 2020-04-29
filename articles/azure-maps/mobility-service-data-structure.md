---
title: Strutture dei dati del servizio Mobility nelle mappe di Azure | Mappe Microsoft Azure
description: In questo articolo vengono fornite informazioni sui campi comuni e sulle strutture di dati restituiti tramite il Microsoft Azure Maps Mobility Services.
author: philmea
ms.author: philmea
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 4dfc6793bba473c4046863937baa292dde7bf421
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80478696"
---
# <a name="data-structures-in-azure-maps-mobility-service"></a>Strutture di dati nel servizio Mobility di Azure Maps

Questo articolo presenta il concetto di area metro nel [servizio Mobility di Azure Maps](https://aka.ms/AzureMapsMobilityService). Vengono illustrati alcuni dei campi comuni che vengono restituiti quando viene eseguita una query su questo servizio per le linee e le interruzioni di transito pubbliche. Si consiglia di leggere questo articolo prima di sviluppare con le API del servizio Mobility.

## <a name="metro-area"></a>Area metropolitana

I dati del servizio Mobility sono raggruppati in base alle aree di metro supportate. Le aree metropolitane non seguono i limiti della città. Un'area metro può contenere più città, città densamente popolate e città circostanti. In realtà, un paese/area geografica può essere un'area metropolitana. 

È `metroID` l'ID di un'area metropolitana che può essere usato per chiamare l' [API dell'area di informazioni di Get metro](https://aka.ms/AzureMapsMobilityMetroAreaInfo). Usare l'API di Azure Maps ' "Get metro" per richiedere tipi di transito, agenzie di transito, avvisi attivi e dettagli aggiuntivi per la metropolitana scelta. È anche possibile richiedere le aree e i metroID di metro supportati. Gli ID area metro sono soggetti a modifiche.

**metroID:** 522 **Nome:** Seattle-Tacoma-Bellevue

![Seattle-Metro-area](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>ID di arresto

È possibile fare riferimento a interruzioni di transito con due tipi di ID, l'ID [GFTS (General Transit feed Specification)](http://gtfs.org/) e l'ID di arresto di Azure maps. L'ID GFTS viene indicato come stopKey e l'ID di arresto di Azure Maps è denominato stopID. Quando si fa spesso riferimento a interruzioni di transito, è consigliabile usare l'ID di arresto di Azure maps. stopID è più stabile e probabilmente rimane invariato a condizione che l'arresto fisico esista. L'ID di arresto GTFS viene aggiornato più spesso. Ad esempio, l'ID di arresto GTFS può essere aggiornato in base alla richiesta del provider GTFS o quando viene rilasciata una nuova versione di GTFS. Anche se l'arresto fisico non ha avuto alcuna modifica, l'ID di arresto GTFS potrebbe cambiare.

Per iniziare, è possibile richiedere interruzioni di transito vicine usando l' [API di transito vicina](https://aka.ms/AzureMapsMobilityNearbyTransit).

## <a name="line-groups-and-lines"></a>Gruppi di righe e linee

Il servizio Mobility usa un modello di dati paralleli per linee e gruppi di linea. Questo modello viene usato per gestire meglio le modifiche ereditate dalle route [GTFS](http://gtfs.org/) e dai dati di viaggio.


### <a name="line-groups"></a>Gruppi di righe

Un gruppo di righe è un'entità, che raggruppa tutte le righe che fanno logicamente parte dello stesso gruppo. In genere, un gruppo di righe contiene due righe, una dal punto A a B e l'altra che restituisce dal punto B a un. Entrambe le righe appartengono alla stessa agenzia di trasporto pubblica e hanno lo stesso numero di riga. In alcuni casi, tuttavia, un gruppo di righe dispone di più di due righe o di una sola riga al suo interno.


### <a name="lines"></a>Linee

Come illustrato in precedenza, ogni gruppo di righe è costituito da un set di righe. Ogni gruppo di righe è costituito da due righe e ogni riga descrive una direzione.  Tuttavia, esistono casi in cui più righe costituiscono un gruppo di righe. Ad esempio, c'è una linea che talvolta si delinea in un determinato quartiere e talvolta non lo è. In entrambi i casi, opera con lo stesso numero di riga. Inoltre, un gruppo di righe può essere composto da una sola riga. Una linea circolare con una sola direzione è un gruppo Ling con una riga.

Per iniziare, è possibile richiedere gruppi di righe usando l' [API Get Transit line](https://aka.ms/AzureMapsMobilityTransitLine).


## <a name="next-steps"></a>Passaggi successivi

Informazioni su come richiedere dati di transito tramite il servizio Mobility:

> [!div class="nextstepaction"]
> [Come richiedere dati di transito](how-to-request-transit-data.md)

Informazioni su come richiedere dati in tempo reale tramite il servizio Mobility:

> [!div class="nextstepaction"]
> [Come richiedere dati in tempo reale](how-to-request-real-time-data.md)

Esplora la documentazione dell'API del servizio Mobility di Azure Maps

> [!div class="nextstepaction"]
> [Documentazione dell'API del servizio Mobility](https://aka.ms/AzureMapsMobilityService)
