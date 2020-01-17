---
title: Scegliere il piano tariffario giusto | Mappe Microsoft Azure
description: In questo articolo vengono illustrati i piani tariffari offerti dalle mappe Microsoft Azure.
author: walsehgal
ms.author: v-musehg
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 8764e9161f952118ca7ae28343dcd16477cf1eee
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76155758"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Scegliere il piano tariffario corretto in Mappe di Azure

Azure Maps offre due piani tariffari, S0 e S1. Lo scopo di questo articolo è aiutare l'utente a scegliere il piano tariffario corretto secondo le proprie esigenze. Per scegliere il piano tariffario corretto, è opportuno porsi le due domande seguenti.

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>Quali funzionalità geospaziali si prevede di usare?
Se si ritiene che i requisiti di servizio siano stati soddisfatti per le API core geospaziali, allora il piano tariffario corretto è S0. Se si desiderano funzionalità più avanzate per l'applicazione, prendere in considerazione il piano tariffario S1. Esempio di funzionalità avanzate: aerea e immagini ibride, acquisizione dell'intervallo di route e geocodifica batch. La tabella delle funzionalità del piano **tariffario** può essere utile per scegliere un piano tariffario più adatto all'applicazione.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>Quanti utenti simultanei si prevede di supportare? 
I piani tariffari S0 e S1 possono gestire diverse quantità di velocità effettive dei dati. Il piano tariffario s0 gestisce fino a **50 query al secondo**, mentre il livello S1 gestisce **più di 50 query al secondo**.

### <a name="pricing-tier-capabilities"></a>Funzionalità dei piani tariffari

| Funzionalità                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| Rendering mappa                              | ✓                   | ✓       |
| Immagini satellite                       |                     | ✓        |
| Cerca                                  | ✓                    | ✓        |
| Ricerca batch                            |                     | ✓        |
| Route                                   | ✓                    |✓        |
| Routing di batch                            |                    | ✓        |
| Routing di matrici                          |                     | ✓        |
| Intervallo percorso (isocrone)                |                     | ✓        |
| Traffico                                |✓                    |✓        |
| Fuso orario                               |✓                    |✓        |
| Georilevazione (anteprima)                    |✓                   |✓        |
| Operazioni spaziali                        |                    |✓        |
| Geofencing                                |                    |✓        |
| Dati di Maps di Azure (anteprima)                |                     | ✓        |
| Mobilità (anteprima)                       |                     | ✓        |
| Meteo (anteprima)                        |✓                    |✓        |

Risulta opportuno prendere in considerazione i seguenti punti dati aggiuntivi:
* Quale tipo di azienda si ha?
* Quanto è importante l'applicazione?

### <a name="pricing-tier-targeted-customers"></a>Clienti di destinazione del piano tariffario

Vedere la tabella contenente **Clienti di destinazione del piano tariffario** per un'idea più accurata dei piani tariffari S0 e S1. Per altre informazioni, vedere [Prezzi per Mappe di Azure](https://azure.microsoft.com/pricing/details/azure-maps/). 

| Piano tariffario  |     Clienti di destinazione                                                                |
|-----------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>Il piano tariffario s0 funziona per le applicazioni in tutte le fasi di produzione: dallo sviluppo di modelli di prova e dalla fase iniziale di test per la produzione e la distribuzione di applicazioni. Tuttavia, questo livello è progettato per lo sviluppo su scala ridotta o per i clienti con utenti simultanei di basso livello o entrambi. <p>|
| S1            |    <p>Il piano tariffario S1 è destinato ai clienti che necessitano di supporto per aziende di grandi dimensioni, applicazioni cruciali o volumi elevati di utenti simultanei. È l'ideale anche per clienti che richiedono servizi geospaziali avanzati.</p>|

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come visualizzare e modificare i piani tariffari:

> [!div class="nextstepaction"] 
> [Gestire un piano tariffario](how-to-manage-pricing-tier.md)
