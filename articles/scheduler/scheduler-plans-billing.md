---
title: Piani e fatturazione
description: Informazioni sui piani e la fatturazione per l'Utilità di pianificazione di Azure
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: e821036ff4ddb5a9786bc4f4537bb81539ab2c87
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "78898481"
---
# <a name="plans-and-billing-for-azure-scheduler"></a>Piani e fatturazione per l'Utilità di pianificazione di Azure

> [!IMPORTANT]
> [App per la logica di Azure](../logic-apps/logic-apps-overview.md) sostituirà Utilità di pianificazione di Azure di cui è [in corso il ritiro](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Per continuare a usare i processi configurati nell'utilità di pianificazione, [eseguire la migrazione alle app per la logica di Azure](../scheduler/migrate-from-scheduler-to-logic-apps.md) il prima possibile. 
>
> Utilità di pianificazione non è più disponibile nel portale di Azure, ma l'[API REST](/rest/api/scheduler) e i [cmdlet di PowerShell per Utilità di pianificazione di Azure](scheduler-powershell-reference.md) rimangono attualmente disponibili, quindi è possibile gestire processi e raccolte di processi.

## <a name="job-collection-plans"></a>Piani di raccolta di processi

Nell'Utilità di pianificazione di Azure, una raccolta di processi contiene un numero specifico di processi. La raccolta di processi è l'entità fatturabile ed è disponibile nei piani Standard, P10 Premium e P20 Premium, descritti di seguito: 

| Piano di raccolta di processi | Numero massimo di processi per raccolta | Ricorrenza massima | Numero massimo di raccolte di processi per sottoscrizione | Limiti | 
|:--- |:--- |:--- |:--- |:--- |
| **Standard** | 50 processi per raccolta | Una volta al minuto. Non è possibile eseguire i processi più spesso di una volta al minuto. | Ogni sottoscrizione di Azure può avere fino a 100 raccolte di processi Standard. | Accesso al set completo di funzionalità dell'Utilità di pianificazione | 
| **P10 Premium** | 50 processi per raccolta | Una volta al minuto. Non è possibile eseguire i processi più spesso di una volta al minuto. | Ogni sottoscrizione di Azure può avere fino a 10.000 raccolte di processi P10 Premium. Per un numero maggiore di raccolte, <a href="mailto:wapteams@microsoft.com">contattare Microsoft</a>. | Accesso al set completo di funzionalità dell'Utilità di pianificazione |
| **P20 Premium** | 1000 processi per raccolta | Una volta al minuto. Non è possibile eseguire i processi più spesso di una volta al minuto. | Ogni sottoscrizione di Azure può avere fino a 5.000 raccolte di processi P20 Premium. Per un numero maggiore di raccolte, <a href="mailto:wapteams@microsoft.com">contattare Microsoft</a>. | Accesso al set completo di funzionalità dell'Utilità di pianificazione |
|||||| 

## <a name="pricing"></a>Prezzi

Per informazioni sui prezzi, vedere [Utilità di pianificazione prezzi](https://azure.microsoft.com/pricing/details/scheduler/).

## <a name="upgrade-or-downgrade-plans"></a>Passaggio a un piano superiore o inferiore

È possibile passare in qualsiasi momento a un piano superiore o inferiore per le raccolte di processi, scegliendo tra i piani Standard, P10 Premium e P20 Premium.

## <a name="active-status-and-billing"></a>Stato attivo e fatturazione

Le raccolte di processi sono sempre attive, a meno che l'intera sottoscrizione di Azure non entri in uno stato di disabilitazione temporanea a causa di problemi di fatturazione. Anche se è possibile disabilitare tutti i processi all'interno di una raccolta di processi in un'unica operazione, ciò non modifica lo stato di fatturazione della raccolta di processi, che pertanto verrà *ancora* fatturata. Le raccolte di processi vuote vengono considerate attive e vengono fatturate.

Per assicurarsi che una raccolta di processi non venga fatturata, è necessario eliminarla.

## <a name="standard-billable-units"></a>Unità fatturabili standard

Un'unità fatturabile standard può includere fino a 10 raccolte di processi Standard. Dato che una raccolta di processi Standard può avere al massimo 50 processi per raccolta, un'unità di fatturazione standard consente che una sottoscrizione abbia fino a 500 processi, ovvero fino a quasi 22 *milioni* di esecuzioni di processi al mese. Questo elenco illustra come avviene la fatturazione in base a vari numeri di raccolte di processi Standard:

* Se il numero di raccolte di processi Standard è compreso tra 1 e 10, verrà addebitata un'unità standard di fatturazione. 

* Se il numero di raccolte di processi Standard è compreso tra 11 e 20, verranno addebitate due unità standard di fatturazione. 

* Se il numero di raccolte di processi Standard è compreso tra 21 e 30, verranno addebitate tre unità standard di fatturazione.

## <a name="p10-premium-billable-units"></a>Unità fatturabili P10 Premium

Un'unità fatturabile P10 Premium può includere fino a 10.000 raccolte di processi P10 Premium. Dato che una raccolta di processi P10 Premium può avere un massimo di 50 processi per raccolta, un'unità di fatturazione P10 Premium consente che una sottoscrizione di Azure abbia un massimo di 500.000 processi, ovvero fino a quasi 22 *miliardi* di esecuzioni di processi al mese. 

Le raccolte di processi P10 Premium includono le stesse capacità delle raccolte di processi Standard, ma offrono una riduzione di prezzo per le app che richiedono numerose raccolte di processi e offrono maggiore scalabilità. Questo elenco illustra come avviene la fatturazione in base a vari numeri di raccolte di processi P10 Premium:

* Se il numero di raccolte di processi P10 Premium è compreso tra 1 e 10.000, verrà addebitata un'unità di fatturazione P10 Premium. 

* Se il numero di raccolte di processi P10 Premium è compreso tra 10.001 e 20.000, verranno addebitate 2 unità di fatturazione P10 Premium e così via.

## <a name="p20-premium-billable-units"></a>Unità fatturabili P20 Premium

Un'unità fatturabile P20 Premium può includere fino a 5.000 raccolte di processi P20 Premium. Dato che una raccolta di processi P20 Premium può avere un massimo di 1.000 processi per raccolta, un'unità di fatturazione P20 Premium consente che una sottoscrizione di Azure abbia un massimo di 5.000.000 processi, ovvero fino a quasi 220 *miliardi* di esecuzioni di processi al mese.

Le raccolte di processi P20 Premium forniscono le stesse funzionalità delle raccolte di processi P10 Premium, ma supportano anche un maggiore numero di processi per ogni raccolta e un numero totale di processi maggiore rispetto alla raccolta P10 Premium, assicurando una maggiore scalabilità.

## <a name="plan-comparison"></a>Piani a confronto

* Se esistono più di 100 raccolte di processi Standard (10 unità di fatturazione standard), è possibile risparmiare includendo tutte le raccolte di processi in un piano Premium.

* Se sono disponibili una raccolta di processi Standard e una raccolta di processi Premium, verranno fatturate un'unità di fatturazione Standard *e* un'unità di fatturazione Premium.

  Le fatture del servizio Utilità di pianificazione sono basate sul numero di raccolte di processi attive Standard o Premium.

## <a name="next-steps"></a>Passaggi successivi

* [Concetti, terminologia e gerarchia di entità dell'Utilità di pianificazione di Azure](scheduler-concepts-terms.md)
* [Limiti, valori predefiniti e codici di errore dell'Utilità di pianificazione di Azure](scheduler-limits-defaults-errors.md)