---
title: Gestione area nello Stack di Azure | Documenti Microsoft
description: Panoramica di gestione di area nello Stack di Azure.
services: azure-stack
documentationcenter: 
author: efemmano
manager: dsavage
editor: 
ms.assetid: e94775d5-d473-4c03-9f4e-ae2eada67c6c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: efemmano
ms.openlocfilehash: d1310f0cb9a820366ab8712a782785e955a24134
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="region-management-in-azure-stack"></a>Gestione area nello Stack di Azure

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Stack di Azure è il concetto di aree, ovvero sono entità logiche costituita da risorse hardware che costituiscono l'infrastruttura di Azure Stack. All'interno di gestione di area, è possibile trovare tutte le risorse che sono necessari per utilizzare correttamente il ciclo di vita di infrastruttura di Azure Stack.

Una distribuzione del sistema di integrazione (detto un *cloud Azure Stack*) costituiscono una singola area. Ogni Kit di sviluppo dello Stack di Azure è un'area, denominata **locale**. Se si distribuisce un sistema Azure Stack integrato secondo, o l'installazione di un'altra istanza del kit di sviluppo su computer separati, questo cloud di Azure Stack è un'area diversa.

## <a name="information-available-through-the-region-management-tile"></a>Informazioni disponibili tramite il riquadro di gestione di area
Stack di Azure dispone di un set di funzionalità di gestione di area disponibili nel **Gestione area** riquadro. Questo riquadro è disponibile a un operatore di Stack di Azure nel dashboard predefinito nel portale di amministrazione. Tramite questo riquadro, è possibile monitorare e aggiornare l'area dello Stack di Azure e i relativi componenti che sono specifiche dell'area.

 ![Riquadro area di gestione](media/azure-stack-manage-region/image1.png)

 Se si fa clic su un'area del riquadro area di gestione, è possibile accedere le informazioni seguenti:

  ![Descrizione dei riquadri nel pannello area Gestione](media/azure-stack-manage-region/image2.png)

1. **Il menu di risorsa**. In questo caso, è possibile accedere aree di gestione specifiche di infrastruttura, visualizzare e gestire le risorse utente, ad esempio gli account di archiviazione e le reti virtuali.

2. **Avvisi**. Questo riquadro Elenca gli avvisi relativi a livello di sistema e fornisce dettagli su ognuna di tali avvisi.

3. **Aggiornamenti**. In questo riquadro, è possibile visualizzare la versione corrente dell'infrastruttura di Azure Stack.

4. **I provider di risorse**. I provider di risorse è possibile gestire la funzionalità di tenant fornita con i componenti necessari per l'esecuzione dello Stack di Azure. Ogni provider di risorse viene fornito con un'esperienza di amministrazione. Questa esperienza può includere avvisi per il provider specifico, metriche e altre funzionalità di gestione specifiche del provider di risorse.
 
5. **Ruoli di infrastruttura**. Ruoli di infrastruttura sono i componenti necessari per l'esecuzione dello Stack di Azure. Vengono elencati solo i ruoli di infrastruttura che segnalano gli avvisi. Fare clic su un ruolo, è possibile visualizzare gli avvisi associati a un ruolo specifico e le istanze del ruolo in cui questo ruolo è in esecuzione. Anche se non esiste la possibilità di avviare, riavviare o arrestare un'istanza del ruolo di infrastruttura, effettuare **non** eseguire questa operazione in un ambiente del kit di sviluppo. Queste opzioni sono progettate solo per un ambiente a più nodi, in cui è presente più di un'istanza di ruolo per ogni ruolo di infrastruttura. Il riavvio di un'istanza del ruolo (in particolare AzS Xrp01) nel kit di sviluppo causa l'instabilità del sistema.

## <a name="next-steps"></a>Passaggi successivi
[Monitoraggio dell'integrità e avvisi nello Stack di Azure](azure-stack-monitor-health.md)

[Gestire gli aggiornamenti in Azure Stack](azure-stack-updates.md)






