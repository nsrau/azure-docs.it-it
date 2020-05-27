---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/13/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: a1e58ebf1ec22bf05b07cbbb95e335b85e3690f4
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83639384"
---
|Nome |Descrizione |Criteri |Versione |
|---|---|---|---|
|[Abilita Monitoraggio di Azure nei set di scalabilità di macchine virtuali](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Abilita Monitoraggio di Azure per i set di scalabilità di macchine virtuali nell'ambito specificato (gruppo di gestione, sottoscrizione o gruppo di risorse). Accetta l'area di lavoro Log Analytics come parametro. Nota: se il set di scalabilità upgradePolicy è impostato su manuale, è necessario applicare l'estensione a tutte le VM del set tramite una chiamata di aggiornamento. Nell'interfaccia della riga di comando è necessario chiamare az vmss update-instances. |6 |1.0.1 |
|[Abilita Monitoraggio di Azure per le macchine virtuali](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Abilitare Monitoraggio di Azure per le macchine virtuali nell'ambito specificato (gruppo di gestione, sottoscrizione o gruppo di risorse). Accetta l'area di lavoro Log Analytics come parametro. |6 |1.0.1 |
