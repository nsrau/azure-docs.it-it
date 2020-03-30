---
title: Scegli il piano tariffario giusto. Mappe di Microsoft Azure
description: In this article, you will learn about pricing tiers offered by Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: a8bf7ff9bacd4fe84ee5b64d0aed5cb271ce06f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335672"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Scegliere il piano tariffario corretto in Mappe di Azure

Azure Maps offre due piani tariffari, S0 e S1. Lo scopo di questo articolo è aiutare l'utente a scegliere il piano tariffario corretto secondo le proprie esigenze. Per scegliere il piano tariffario corretto, poniti le due domande seguenti.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>Quanti utenti simultanei si prevede di supportare? 
I piani tariffari S0 e S1 possono gestire diverse quantità di velocità effettive dei dati. Il piano tariffario S0 gestisce fino a **50 query al secondo**. Mentre il livello S1 gestisce **più di 50 query al secondo.**

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>Quali funzionalità geospaziali si prevede di usare?
Il piano tariffario S0 è adatto alle tue esigenze, se le API geospaziali di base soddisfano i requisiti del servizio. Se si desiderano funzionalità più avanzate per l'applicazione, prendere in considerazione il piano tariffario S1. Le funzionalità avanzate includono: immagini aeree e ibride, recupero dell'intervallo di route e geocodifica batch. Esaminare la tabella **delle funzionalità del piano tariffario** per selezionare il piano tariffario più adatto per l'applicazione.

### <a name="pricing-tier-capabilities"></a>Funzionalità dei piani tariffari

| Funzionalità                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| Rendering mappa                              | ✓                   | ✓       |
| Satellitari                       |                     | ✓        |
| Ricerca                                  | ✓                    | ✓        |
| Ricerca batch                            |                     | ✓        |
| Route                                   | ✓                    |✓        |
| Batch Routing                            |                    | ✓        |
| Instradamento matrice                          |                     | ✓        |
| Intervallo di instradamento (Isochrones)                |                     | ✓        |
| Traffico                                |✓                    |✓        |
| Fuso orario                               |✓                    |✓        |
| Geolocalizzazione (anteprima)Geolocation (Preview)                    |✓                   |✓        |
| Operazioni spaziali                        |                    |✓        |
| Geofencing                                |                    |✓        |
| Dati di Mappe di Azure (anteprima)Azure Maps Data (Preview)                |                     | ✓        |
| Mobilità (anteprima)                       |                     | ✓        |
| Meteo (anteprima)                        |✓                    |✓        |

Considerare questi punti aggiuntivi:Consider these additional points:
* Che tipo di impresa hai?
* Quanto è critica la tua applicazione?

### <a name="pricing-tier-targeted-customers"></a>Clienti di destinazione del piano tariffario

Vedere la tabella contenente **Clienti di destinazione del piano tariffario** per un'idea più accurata dei piani tariffari S0 e S1. Per altre informazioni, vedere [Prezzi per Mappe di Azure](https://azure.microsoft.com/pricing/details/azure-maps/). 

| Piano tariffario  |     Clienti di destinazione                                                                |
|-----------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>Il piano tariffario S0 funziona per le applicazioni in tutte le fasi della produzione: dallo sviluppo proof-of-concept e test in fase iniziale, alla produzione e distribuzione delle applicazioni. Tuttavia, questo livello è progettato per lo sviluppo su piccola scala, o i clienti con utenti simultanei bassi o entrambi. <p>|
| S1            |    <p>Il piano tariffario S1 è destinato ai clienti con applicazioni aziendali su larga scala, applicazioni mission-critical o volumi elevati di utenti simultanei. È l'ideale anche per clienti che richiedono servizi geospaziali avanzati.</p>|

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come visualizzare e modificare i piani tariffari:

> [!div class="nextstepaction"] 
> [Gestire un piano tariffario](how-to-manage-pricing-tier.md)
