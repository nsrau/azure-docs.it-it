---
title: "Panoramica di modelli comuni di scalabilità automatica | Microsoft Docs"
description: "Informazioni su alcuni modelli comuni per la scalabilità automatica delle risorse in Azure."
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: d37d3fda-8ef1-477c-a360-a855b418de84
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: ancav
ms.openlocfilehash: fce51546e041c8989d813c3935e058c52b38ba77
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2017
---
# <a name="overview-of-common-autoscale-patterns"></a>Panoramica di modelli comuni di scalabilità automatica
Questo articolo descrive alcuni modelli comuni per la scalabilità delle risorse in Azure.

La scalabilità automatica di Monitoraggio di Azure si applica solo a set di scalabilità di macchine virtuali (VMSS), servizi cloud, piani di servizio app e ambienti di servizio app. 

# <a name="lets-get-started"></a>Introduzione

Questo articolo presuppone che l'utente abbia familiarità con la scalabilità automatica. È disponibile un'[introduzione alla scalabilità della risorsa][1]. Di seguito sono indicati alcuni modelli comuni di scalabilità.

## <a name="scale-based-on-cpu"></a>Scalabilità in base alla CPU

È presente un'app Web (VMSS/ruolo del servizio cloud) e 

- Si vuole aumentare/ridurre il numero di istanze in base alla CPU.
- Si vuole anche verificare che sia presente un numero minimo di istanze. 
- Si vuole anche impostare un limite massimo per il numero di istanze che è possibile aggiungere.

![Scalabilità in base alla CPU][2]

## <a name="scale-differently-on-weekdays-vs-weekends"></a>Scalabilità diversa per giorni feriali e fine settimana

È presente un'app Web (VMSS/ruolo del servizio cloud) e

- Si vogliono 3 istanze per impostazione predefinita nei giorni feriali
- Non si prevede traffico nei fine settimana e quindi si vuole ridurre il numero di istanze a 1 nei fine settimana.

![Scalabilità diversa per giorni feriali e fine settimana][3]

## <a name="scale-differently-during-holidays"></a>Scalabilità diversa durante le festività

È presente un'app Web (VMSS/ruolo del servizio cloud) e 

- Si vogliono aumentare/ridurre le prestazioni in base all'utilizzo della CPU per impostazione predefinita
- Durante le festività o in giorni specifici importanti per l'azienda si vogliono tuttavia ignorare le impostazioni predefinite e avere a disposizione maggiore capacità.

![Scalabilità diversa nelle festività][4]

## <a name="scale-based-on-custom-metric"></a>Scalabilità in base a metriche personalizzate

Sono disponibili un front-end web e un livello API che comunica con il back-end. 

- Si vuole ridimensionare il livello API in base a eventi personalizzati nel front-end, ad esempio si vuole ridimensionare il processo di completamento della transazione in base al numero di articoli contenuti nel carrello acquisti.

![Scalabilità in base a metriche personalizzate][5]

<!--Reference-->
[1]: ./monitoring-autoscale-get-started.md
[2]: ./media/monitoring-autoscale-common-scale-patterns/scale-based-on-cpu.png
[3]: ./media/monitoring-autoscale-common-scale-patterns/weekday-weekend-scale.png
[4]: ./media/monitoring-autoscale-common-scale-patterns/holidays-scale.png
[5]: ./media/monitoring-autoscale-common-scale-patterns/custom-metric-scale.png