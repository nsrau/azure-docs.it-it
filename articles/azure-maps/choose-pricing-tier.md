---
title: Scegliere il piano tariffario giusto | Mappe Microsoft Azure
description: In questo articolo vengono illustrati i piani tariffari offerti dalle mappe Microsoft Azure.
author: farah-alyasari
ms.author: v-faalya
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 048ecdc9b96edcdebecb4cd7df0630aa991aa4d1
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210073"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Scegliere il piano tariffario corretto in Mappe di Azure

Azure Maps offre due piani tariffari, S0 e S1. Lo scopo di questo articolo è aiutare l'utente a scegliere il piano tariffario corretto secondo le proprie esigenze. Per scegliere il piano tariffario corretto, è possibile porsi le due domande riportate di seguito.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>Quanti utenti simultanei si prevede di supportare? 
I piani tariffari S0 e S1 possono gestire diverse quantità di velocità effettive dei dati. Il piano tariffario S0 gestisce fino a **50 query al secondo**. Mentre il livello S1 gestisce **più di 50 query al secondo**.

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>Quali funzionalità geospaziali si prevede di usare?
Il piano tariffario s0 è adatto all'utente, se le API geospaziali principali soddisfano i requisiti del servizio. Se si desiderano funzionalità più avanzate per l'applicazione, prendere in considerazione il piano tariffario S1. Le funzionalità avanzate includono: Aerial Plus immagini ibride, ottenere l'intervallo di route e la geocodifica batch. Esaminare la tabella delle funzionalità del piano **tariffario** per selezionare il piano tariffario più adatto all'applicazione.

### <a name="pricing-tier-capabilities"></a>Funzionalità dei piani tariffari

| Funzionalità                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| Rendering mappa                              | ✓                   | ✓       |
| Immagini satellite                       |                     | ✓        |
| Ricerca                                  | ✓                    | ✓        |
| Ricerca batch                            |                     | ✓        |
| Route                                   | ✓                    |✓        |
| Routing batch                            |                    | ✓        |
| Routing matrice                          |                     | ✓        |
| Intervallo di route (isocrone)                |                     | ✓        |
| Traffico                                |✓                    |✓        |
| Fuso orario                               |✓                    |✓        |
| Georilevazione (anteprima)                    |✓                   |✓        |
| Operazioni spaziali                        |                    |✓        |
| Geofencing                                |                    |✓        |
| Dati di Maps di Azure (anteprima)                |                     | ✓        |
| Mobilità (anteprima)                       |                     | ✓        |
| Meteo (anteprima)                        |✓                    |✓        |

Prendere in considerazione questi punti aggiuntivi:
* Quale tipo di azienda si ha?
* Quanto è importante l'applicazione?

### <a name="pricing-tier-targeted-customers"></a>Clienti di destinazione del piano tariffario

Vedere la tabella contenente **Clienti di destinazione del piano tariffario** per un'idea più accurata dei piani tariffari S0 e S1. Per altre informazioni, vedere [Prezzi per Mappe di Azure](https://azure.microsoft.com/pricing/details/azure-maps/). 

| Piano tariffario  |     Clienti di destinazione                                                                |
|-----------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>Il piano tariffario s0 funziona per le applicazioni in tutte le fasi di produzione: dallo sviluppo di modelli di prova e dalla fase iniziale di test per la produzione e la distribuzione di applicazioni. Tuttavia, questo livello è progettato per lo sviluppo su scala ridotta o per i clienti con utenti simultanei di basso livello o entrambi. <p>|
| S1            |    <p>Il piano tariffario S1 è per i clienti con applicazioni aziendali su larga scala, applicazioni cruciali o volumi elevati di utenti simultanei. È l'ideale anche per clienti che richiedono servizi geospaziali avanzati.</p>|

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come visualizzare e modificare i piani tariffari:

> [!div class="nextstepaction"] 
> [Gestire un piano tariffario](how-to-manage-pricing-tier.md)
