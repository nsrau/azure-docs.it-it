---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/13/2020
ms.author: dacoulte
ms.openlocfilehash: 315d0f54d1f8fb875576f685cd0197003975c8eb
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/15/2020
ms.locfileid: "77371746"
---
|Nome |Descrizione |Criteri |Versione |
|---|---|---|---|
|[Abilita Monitoraggio di Azure per i set di scalabilità di macchine virtuali](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Abilitare Monitoraggio di Azure per i set di scalabilità di macchine virtuali nell'ambito specificato (gruppo di gestione, sottoscrizione o gruppo di risorse). Accetta l'area di lavoro Log Analytics come parametro. Nota: se la proprietà upgradePolicy del set di scalabilità è impostata su Manuale, è necessario applicare l'estensione a tutte le macchine virtuali nel set chiamando l'aggiornamento in tali VM. Nell'interfaccia della riga di comando il comando da usare è az vmss update-instances. |6 |1.0.0-preview |
|[Abilita Monitoraggio di Azure per le macchine virtuali](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Abilitare Monitoraggio di Azure per le macchine virtuali nell'ambito specificato (gruppo di gestione, sottoscrizione o gruppo di risorse). Accetta l'area di lavoro Log Analytics come parametro. |6 |1.0.0-preview |
