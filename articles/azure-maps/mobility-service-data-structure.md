---
title: Strutture dei dati del servizio Mobility nelle mappe di Azure | Mappe Microsoft Azure
description: In questo articolo vengono fornite informazioni sui campi comuni e sulle strutture di dati restituiti tramite il Microsoft Azure Maps Mobility Services.
author: walsehgal
ms.author: v-musehg
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 213910ee2439fa958b9f1d4926883eb8e066ba41
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2020
ms.locfileid: "75910714"
---
# <a name="data-structures-in-azure-maps-mobility-service"></a>Strutture di dati nel servizio Mobility di Azure Maps

Questo articolo presenta il concetto di area metro nel [servizio Mobility di Azure Maps](https://aka.ms/AzureMapsMobilityService) e alcuni dei campi comuni restituiti tramite i servizi, quando viene eseguita una query per le linee e le interruzioni di transito pubbliche. Prima di iniziare a usare le API del servizio Mobility, è consigliabile procedere in questo articolo. Di seguito vengono illustrati questi campi comuni.

## <a name="metro-area"></a>Area metropolitana

I dati del servizio Mobility sono suddivisi in aree metropolitane supportate. Le aree metropolitane non seguono i limiti della città, un'area metro può contenere più città, ad esempio una città densamente popolata e le relative città circostanti; e un paese/area geografica può essere un'area metropolitana. 

Il `metroID` è l'ID di un'area metropolitana che può essere usato per chiamare l' [API Get Metro area info](https://aka.ms/AzureMapsMobilityMetroAreaInfo) per richiedere i tipi di transito supportati e altri dettagli per l'area metro, ad esempio agenzie di transito e avvisi attivi. È possibile usare l'API di Azure Maps Get metro per richiedere le aree e i metroID di metro supportati. Gli ID area metro sono soggetti a modifiche.

**metroID:** 522 **Nome:** Seattle-Tacoma-Bellevue

![Seattle-Metro-area](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>ID di arresto

È possibile fare riferimento a arresti di transito con due tipi di ID, l'ID della [specifica del feed di transito generale (GFTS)](https://gtfs.org/) (noto come stopKey) e l'ID di arresto di Azure Maps (denominato stopId). Quando si fa riferimento a interruzioni nel tempo, si consiglia di usare l'ID di arresto di Maps di Azure, in quanto questo ID è più stabile e non è probabile che si modifichi a condizione che l'arresto fisico esista. L'ID di arresto GTFS viene aggiornato più spesso, ad esempio nel caso in cui il provider GTFS debba modificarlo o venga rilasciata una nuova versione di GTFS, anche se l'arresto fisico non ha avuto alcuna modifica.

Per iniziare, è possibile richiedere arresti di transito nelle vicinanze usando [Get immediate API Transit](https://aka.ms/AzureMapsMobilityNearbyTransit).

## <a name="line-groups-and-lines"></a>Gruppi di righe e linee

Il servizio Mobility usa un modello di dati paralleli per linee e gruppi di linee per gestire meglio le modifiche ereditate dal modello di dati Routes e Trips di [GTFS](https://gtfs.org/) .


### <a name="line-groups"></a>Gruppi di righe

Un gruppo di righe è un'entità, che raggruppa tutte le righe che fanno logicamente parte dello stesso gruppo. In genere, un gruppo di righe conterrà due righe, una dal punto A al B e l'altra dal punto B a un, entrambe appartenenti alla stessa agenzia di trasporto pubblica e con lo stesso numero di riga. In alcuni casi, tuttavia, un gruppo di righe dispone di più di due righe o di una sola riga al suo interno.


### <a name="lines"></a>Linee

Come illustrato in precedenza, ogni gruppo di righe è costituito da un set di righe. Spesso ogni riga descrive una direzione e ogni gruppo di righe è costituito da due righe. Tuttavia, esistono casi in cui un numero maggiore di righe comprende un gruppo di righe, ad esempio esiste una linea che talvolta viene rivisitata in un determinato quartiere, a volte non e viene gestita in entrambi i casi con lo stesso numero di riga e sono presenti altri casi in cui una riga g ruppo è costituito da una singola riga, ad esempio una linea circolare con una sola direzione.

Per iniziare, è possibile richiedere gruppi di righe usando l' [API Get Transit line](https://aka.ms/AzureMapsMobilityTransitLine) e successivamente eseguire il drill-down nelle righe.


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
