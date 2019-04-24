---
title: Panoramica di modelli comuni di scalabilità automatica
description: Informazioni su alcuni modelli comuni per la scalabilità automatica delle risorse in Azure.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/07/2017
ms.author: ancav
ms.subservice: autoscale
ms.openlocfilehash: 8356a8c8c31a043197485b4913b4a67d7d719778
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60534223"
---
# <a name="overview-of-common-autoscale-patterns"></a>Panoramica di modelli comuni di scalabilità automatica
Questo articolo descrive alcuni modelli comuni per la scalabilità delle risorse in Azure.

La scalabilità automatica di Monitoraggio di Azure si applica solo a [set di scalabilità di macchine virtuali](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Servizi cloud](https://azure.microsoft.com/services/cloud-services/), [app Web del servizio app](https://azure.microsoft.com/services/app-service/web/) e [servizi di gestione API](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

## <a name="lets-get-started"></a>Introduzione

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

È necessario un front-end web e un livello di API che comunica con il back-end.

- Si vuole ridimensionare il livello API in base agli eventi personalizzati nel front-end. Ad esempio, si intende ridimensionare il processo di completamento della transazione in base al numero di elementi nel carrello).

![Scalabilità in base a metriche personalizzate][5]

<!--Reference-->
[1]: ./autoscale-get-started.md
[2]: ./media/autoscale-common-scale-patterns/scale-based-on-cpu.png
[3]: ./media/autoscale-common-scale-patterns/weekday-weekend-scale.png
[4]: ./media/autoscale-common-scale-patterns/holidays-scale.png
[5]: ./media/autoscale-common-scale-patterns/custom-metric-scale.png

