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
ms.openlocfilehash: 028a227eefb319d19c9274082626a007bd9161de
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2019
ms.locfileid: "53993961"
---
# <a name="choosing-the-right-pricing-tier-in-azure-maps"></a>Scegliere il piano tariffario corretto in Mappe di Azure

Mappe di Azure offre due piani tariffari. Lo scopo di questo articolo è aiutare l'utente a scegliere il piano tariffario corretto secondo le proprie esigenze. Per scegliere il piano tariffario corretto, è opportuno porsi due domande:

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>Quali funzionalità geospaziali si prevede di usare?
Se si ritiene che i requisiti di servizio siano stati soddisfatti per le API core geospaziali, allora il piano tariffario corretto è S0. Se si desiderano funzionalità più avanzate per l'applicazione, ad esempio immagini aeree ibride, intervallo percorso, geocodifica di batch e così via, prendere in considerazione il piano tariffario S1. La tabella seguente con le **funzionalità del piano tariffario** offre una panoramica di tutte le esigenze dell'applicazione per aiutare a scegliere un piano tariffario più adatto per l'applicazione.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>Quanti utenti simultanei si prevede di supportare? 
I piani tariffari S0 e S1 possono gestire diverse quantità di velocità effettive dei dati. Prima di scegliere un piano tariffario per Mappe di Azure, è consigliabile chiedersi quanti utenti simultanei si desidera supportare. Il piano tariffario S0 può gestire fino a **50 query al secondo** e il piano tariffario S1 può gestire **più di 50 query al secondo**.

### <a name="pricing-tier-capabilities"></a>Funzionalità dei piani tariffari

| Funzionalità                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| Ricerca                                  |        ✓           |     ✓    |
| Routing.                                 |        ✓           |     ✓    |
| Rendering                                  |        ✓           |     ✓    |
| Traffico                                 |        ✓           |     ✓    |
| Fusi orari                              |        ✓           |     ✓    |
| *Immagini + immagini ibride (anteprima)     |        ✓           |     ✓    |
| *Intervallo di percorso (anteprima)                  |        ✓           |     ✓    |
| *Posizione IP 2 (anteprima)                |        ✓           |     ✓    |
| *Poligoni dalla ricerca (anteprima)         |        ✓           |     ✓    |
| *Geocodifica batch (anteprima)              |        ✓           |     ✓    |
| *Routing batch (anteprima)                |        ✓           |     ✓    |
| *Routing di matrici (anteprima)               |        ✓           |     ✓    |

> [!Note]
> L'accesso a queste funzionalità dal piano tariffario S0 verrà deprecato dopo il 4 febbraio 2019.

Alcuni altri punti dati aggiuntivi da considerare sono: di quale tipo di azienda si dispone o qual è il livello di criticità dell'applicazione in fase di compilazione?

Vedere la tabella contenente **Clienti di destinazione del piano tariffario** per un'idea più accurata dei piani tariffari S0 e S1. Per altre informazioni sui prezzi di Mappe di Azure, vedere [Prezzi di Mappe di Azure](https://azure.microsoft.com/pricing/details/azure-maps/). 

### <a name="pricing-tier-targeted-customers"></a>Clienti di destinazione del piano tariffario

| Piano tariffario  |        Clienti di destinazione                                                                |
|---------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>Il piano tariffario S0 è destinato ai clienti di aziende di piccole o medie dimensioni. È un piano tariffario corretto per utenti che non prevedono volumi elevati di utenti simultanei e i cui requisiti di servizio per le API core geospaziali siano soddisfatti come indicato nella tabella sopra. Questo livello è disponibile a livello generale ed è applicabile per le applicazioni in tutte le fasi di produzione dallo sviluppo del modello di verifica e dalla fase iniziale di test alla produzione e distribuzione dell'applicazione.<p>|
| S1            |    <p>Il piano tariffario S1 è destinato ai clienti che necessitano di supporto per aziende di grandi dimensioni, applicazioni cruciali, volumi elevati di utenti simultanei e/o necessitano di servizi geospaziali avanzati.</p>|

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla visualizzazione e modifica del piano tariffario:

> [!div class="nextstepaction"]
> [Gestire il piano tariffario](how-to-manage-pricing-tier.md)
