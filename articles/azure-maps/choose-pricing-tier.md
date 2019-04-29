---
title: Scegliere il piano tariffario corretto per Mappe di Azure | Microsoft Docs
description: Informazioni sui piani tariffari offerti da Mappe di Azure
author: walsehgal
ms.author: v-musehg
ms.date: 01/02/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 1bf60014b20f4cec29d661db4deb029fea4ab1d5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60796301"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Scegliere il piano tariffario corretto in Mappe di Azure

Mappe di Azure offre due piani tariffari. Lo scopo di questo articolo è aiutare l'utente a scegliere il piano tariffario corretto secondo le proprie esigenze. Per scegliere il piano tariffario corretto, è opportuno porsi le due domande seguenti.

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>Quali funzionalità geospaziali si prevede di usare?
Se si ritiene che i requisiti di servizio siano stati soddisfatti per le API core geospaziali, allora il piano tariffario corretto è S0. Se si desiderano funzionalità più avanzate per l'applicazione, prendere in considerazione il piano tariffario S1. Le funzionalità di esempio sono settoriali più immagini ibride, ottenengono l'intervallo di route e la geocodifica di batch. La tabella delle **funzionalità dei piani tariffari** riportata di seguito offre una panoramica di tutte le esigenze legate all'applicazione. Consente inoltre di scegliere un piano tariffario più adatto all'applicazione.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>Quanti utenti simultanei si prevede di supportare? 
I piani tariffari S0 e S1 possono gestire diverse quantità di velocità effettive dei dati. Prima di scegliere un piano tariffario di Mappe di Azure, è opportuno porsi alcune domande. Ad esempio: "Quanti utenti simultanei si prevede di supportare?" Il piano tariffario S0 gestisce fino a **50 query al secondo**. Il piano tariffario S1 gestisce più di **50 query al secondo**.

### <a name="pricing-tier-capabilities"></a>Funzionalità dei piani tariffari

| Funzionalità                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| Ricerca (fwd/rev la geocodifica, i punti di interesse)  |        ✓           |     ✓    |
| Geocodifica batch (anteprima)              |                   |     ✓    |
| Poligoni dalla ricerca          |                   |     ✓    |
| Routing.                                 |        ✓           |     ✓    |
| Intervallo di route                    |                   |     ✓    |
| Routing batch (anteprima)                |                   |     ✓    |
| Routing di matrici (anteprima)               |                   |     ✓    |
| Rendering                                  |        ✓           |     ✓    |
| Immagini più immagini ibride    |            |     ✓    |
| Traffico                                 |        ✓           |     ✓    |
| Fusi orari                              |        ✓           |     ✓    |
| Georilevazione (anteprima)                |        ✓           |     ✓    |
| Dati (anteprima)               |                   |     ✓    |
| Spaziale (anteprima)               |                   |     ✓    |
| Geofencing (anteprima)               |                   |     ✓    |



Risulta opportuno prendere in considerazione i seguenti punti dati aggiuntivi:
* Qual è il tipo di azienda?
* Quanto è critica l'applicazione in costruzione?

Vedere la tabella contenente **Clienti di destinazione del piano tariffario** per un'idea più accurata dei piani tariffari S0 e S1. Per altre informazioni, vedere [Prezzi per Mappe di Azure](https://azure.microsoft.com/pricing/details/azure-maps/). 

### <a name="pricing-tier-targeted-customers"></a>Clienti di destinazione del piano tariffario

| Piano tariffario  |     Clienti di destinazione                                                                |
|---------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>Il piano tariffario S0 è destinato ai clienti di aziende di piccole o medie dimensioni. Se non si prevedono di volumi elevati di utenti simultanei, allora questo è il piano tariffario corretto. È il piano corretto inoltre, se le API core geospaziali illustrate nella tabella precedente soddisfano i requisiti di servizio. Questo piano tariffario è disponibile a livello generale. ed è attuabile alle applicazioni in tutte le fasi di produzione, dallo sviluppo del modello di verifica e dalla fase iniziale di test alla produzione e distribuzione.<p>|
| S1            |    <p>Il piano tariffario S1 è destinato ai clienti che necessitano di supporto per aziende di grandi dimensioni, applicazioni cruciali o volumi elevati di utenti simultanei. È l'ideale anche per clienti che richiedono servizi geospaziali avanzati.</p>|

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come visualizzare e modificare i piani tariffari:

> [!div class="nextstepaction"] 
> [Gestire un piano tariffario](how-to-manage-pricing-tier.md)
