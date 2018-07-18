---
title: Gestione area nello Stack di Azure | Documenti Microsoft
description: Panoramica di gestione di area nello Stack di Azure.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: e94775d5-d473-4c03-9f4e-ae2eada67c6c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2018
ms.author: brenduns
ms.reviewer: efemmano
ms.openlocfilehash: 0286ed9c7b3fe320b936d33fe3beaddccd6ac0fa
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35247535"
---
# <a name="region-management-in-azure-stack"></a>Gestione area nello Stack di Azure

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Stack di Azure viene utilizzato il concetto di aree, ovvero sono entità logiche costituita da risorse hardware che costituiscono l'infrastruttura di Azure Stack. All'interno di gestione di area, è possibile trovare tutte le risorse che sono necessari per utilizzare correttamente l'infrastruttura di Azure Stack.

Una distribuzione del sistema di integrazione (detto un *cloud Azure Stack*) costituiscono una singola area. Ogni Kit di sviluppo dello Stack di Azure è un'area, denominata **locale**. Se si distribuisce un sistema Azure Stack integrato secondo, o l'installazione di un'altra istanza del kit di sviluppo su computer separati, questo cloud di Azure Stack è un'area diversa.

## <a name="information-available-through-the-region-management-tile"></a>Informazioni disponibili tramite il riquadro di gestione di area

Stack di Azure dispone di un set di funzionalità di gestione di area disponibili nel **Gestione area** riquadro. Questo riquadro è disponibile a un operatore di Stack di Azure nel dashboard predefinito nel portale di amministrazione. Tramite questo riquadro, è possibile monitorare e aggiornare l'area dello Stack di Azure e i relativi componenti che sono specifiche dell'area.

 ![Riquadro area di gestione](media/azure-stack-manage-region/image1.png)

 Se si fa clic su un'area del riquadro area di gestione, è possibile accedere le informazioni seguenti:

  ![Descrizione dei riquadri nel pannello area Gestione](media/azure-stack-manage-region/image2.png)

1. **Il menu di risorsa**. In questo caso, è possibile accedere aree di gestione specifiche di infrastruttura, visualizzare e gestire le risorse utente, ad esempio gli account di archiviazione e le reti virtuali.

2. **Avvisi**. Questo Elenca gli avvisi relativi a livello di sistema e fornisce informazioni dettagliate su ognuno di tali avvisi.

3. **Aggiornamenti**. Qui è possibile visualizzare la versione corrente dell'infrastruttura dello Stack di Azure, gli aggiornamenti disponibili e la cronologia di aggiornamento. È inoltre possibile aggiornare il sistema integrato.

4. **I provider di risorse**. I provider di risorse è possibile gestire la funzionalità di utente fornita con i componenti necessari per l'esecuzione dello Stack di Azure. Ogni provider di risorse viene fornito con un'esperienza di amministrazione. Questa esperienza può includere avvisi per il provider specifico, metriche e altre funzionalità di gestione specifiche del provider di risorse.

5. **Ruoli di infrastruttura**. Ruoli di infrastruttura sono i componenti necessari per l'esecuzione dello Stack di Azure. Vengono elencati solo i ruoli di infrastruttura che segnalano gli avvisi. Se si seleziona un ruolo, è possibile visualizzare gli avvisi associati il ruolo e le istanze del ruolo in cui viene eseguito questo ruolo.

## <a name="next-steps"></a>Passaggi successivi

- [Monitoraggio dell'integrità e avvisi nello Stack di Azure](azure-stack-monitor-health.md)
- [Gestire gli aggiornamenti in Azure Stack](azure-stack-updates.md)
