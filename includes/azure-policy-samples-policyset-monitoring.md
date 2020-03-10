---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/02/2020
ms.author: dacoulte
ms.openlocfilehash: e62ad440eb3f9af41f2a2cddd6d882887638e799
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78262401"
---
|Nome |Descrizione |Criteri |Versione |
|---|---|---|---|
|[Abilita Monitoraggio di Azure nei set di scalabilità di macchine virtuali](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Abilita Monitoraggio di Azure per i set di scalabilità di macchine virtuali nell'ambito specificato (gruppo di gestione, sottoscrizione o gruppo di risorse). Accetta l'area di lavoro Log Analytics come parametro. Nota: se la proprietà upgradePolicy del set di scalabilità è impostata su Manuale, è necessario applicare l'estensione a tutte le macchine virtuali nel set chiamando l'aggiornamento in tali VM. Nell'interfaccia della riga di comando il comando da usare è az vmss update-instances. |6 |1.0.0-preview |
|[Abilita Monitoraggio di Azure per le macchine virtuali](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Abilitare Monitoraggio di Azure per le macchine virtuali nell'ambito specificato (gruppo di gestione, sottoscrizione o gruppo di risorse). Accetta l'area di lavoro Log Analytics come parametro. |6 |1.0.0-preview |
