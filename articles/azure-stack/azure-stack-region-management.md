---
title: Gestione delle aree in Azure Stack | Microsoft Docs
description: Panoramica di gestione delle aree in Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: e94775d5-d473-4c03-9f4e-ae2eada67c6c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2018
ms.author: sethm
ms.reviewer: efemmano
ms.openlocfilehash: 401b81ceb7ab71528a4ad11bc7d8944b4d732933
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078866"
---
# <a name="region-management-in-azure-stack"></a>Gestione delle aree in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Stack di Azure Usa il concetto di aree, in cui sono entità logiche comprende le risorse hardware che costituiscono l'infrastruttura di Azure Stack. All'interno di gestione delle aree, è possibile trovare tutte le risorse che sono necessarie per svolgere correttamente l'infrastruttura di Azure Stack.

Una integrate nella distribuzione del sistema (definito come un *cloud Azure Stack*) costituisce una singola area. Ogni database di Azure Stack Development Kit è un'area, denominata **locale**. Se si distribuisce un sistema integrato Azure Stack secondo, o l'installazione di un'altra istanza del kit di sviluppo in hardware separato, questo cloud di Azure Stack è un'area diversa.

## <a name="information-available-through-the-region-management-tile"></a>Informazioni disponibili tramite il riquadro di gestione di area

Azure Stack offre un set di funzionalità di gestione di area disponibili nel **gestione delle aree** riquadro. Questo riquadro è disponibile a un operatore di Azure Stack nel dashboard predefinito nel portale di amministrazione. Tramite questo riquadro, è possibile monitorare e aggiornare l'area di Azure Stack e i relativi componenti, che sono specifici dell'area.

 ![Il riquadro di gestione di area](media/azure-stack-manage-region/image1.png)

 Se si fa clic su un'area nel riquadro di gestione dell'area, è possibile accedere le informazioni seguenti:

  ![Descrizione dei riquadri nel Pannello di gestione dell'area](media/azure-stack-manage-region/image2.png)

1. **Menu delle risorse**. In questo caso, è possibile accedere aree di gestione specifiche dell'infrastruttura, visualizzare e gestire le risorse utente, ad esempio gli account di archiviazione e reti virtuali.

2. **Avvisi**. Questo Elenca gli avvisi a livello di sistema e fornisce informazioni dettagliate su ognuno di tali avvisi.

3. **Gli aggiornamenti**. Qui è possibile visualizzare la versione corrente dell'infrastruttura di Azure Stack, gli aggiornamenti disponibili e la cronologia di aggiornamento. È anche possibile aggiornare il sistema integrato.

4. **I provider di risorse**. I provider di risorse è la posizione per gestire la funzionalità utente fornita con i componenti necessari per eseguire Azure Stack. Ogni provider di risorse viene fornito con un'esperienza amministrativa. Questa esperienza può includere avvisi per il provider specifico, metriche e altre funzionalità di gestione specifiche per il provider di risorse.

5. **Ruoli di infrastruttura**. Ruoli di infrastruttura sono i componenti necessari per eseguire Azure Stack. Vengono elencati solo i ruoli di infrastruttura che segnalano gli avvisi. Se si seleziona un ruolo, è possibile visualizzare gli avvisi associati il ruolo e le istanze del ruolo in cui viene eseguito questo ruolo.

## <a name="next-steps"></a>Passaggi successivi

- [Monitorare l'integrità e gli avvisi in Azure Stack](azure-stack-monitor-health.md)
- [Gestire gli aggiornamenti in Azure Stack](azure-stack-updates.md)
