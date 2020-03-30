---
title: Stiamo ritirando le macchine virtuali classiche di Azure il 1 marzo 2023
description: Articolo fornisce una panoramica generale del pensionamento classico delle macchine virtuali
services: virtual-machines
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: 764567bffd2a08ebb5beb17e3063998848b3f110
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127329"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>Eseguire la migrazione delle risorse di IaaS in Azure Resource Manager entro il 1 marzo 2023Migrate your IaaS resources to Azure Resource Manager by March 1, 2023 

Nel 2014 abbiamo lanciato IaaS in Azure Resource Manager e da allora abbiamo migliorato le funzionalità. Poiché [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) dispone ora di funzionalità IaaS complete e di altri miglioramenti, la gestione delle macchine virtuali IaaS tramite Azure Service Manager è stata completamente ritirata il 1 marzo 2023. 

Oggi, circa il 90% delle macchine virtuali IaaS usa Azure Resource Manager.Today, circa 90% delle macchine virtuali IaaS utilizzano Azure Resource Manager.Today, circa 90% delle macchine virtuali IaaS utilizzano Azure Resource Manager.Today, circa 90 Se si usano risorse IaaS tramite Azure Service Manager (ASM), iniziare a pianificare la migrazione ora e completarla entro il 1 marzo 2023 per sfruttare I vantaggi di [Azure Resource Manager.](https://docs.microsoft.com/azure/azure-resource-manager/management/)

Le macchine virtuali classiche seguiranno i [criteri moderni relativi](https://support.microsoft.com/help/30881/modern-lifecycle-policy) al ciclo di vita per il pensionamento.

## <a name="how-does-this-affect-me"></a>Quali sono le conseguenze di questa modifica? 

1) A partire dal 28 febbraio 2020, i clienti che non hanno utilizzato le macchine virtuali IaaS tramite Azure Service Manager (ASM) nel mese di febbraio 2020 non saranno più in grado di creare macchine virtuali classiche. 
2) Il 1 marzo 2023 i clienti non saranno più in grado di avviare le macchine virtuali IaaS usando Azure Service Manager e tutte le macchine che sono ancora in esecuzione o allocate verranno arrestate e deallocate. 
2) Il 1 marzo 2023, le sottoscrizioni che non hanno eseguito la migrazione a Azure Resource Manager verranno informate sulle sequenze temporali per l'eliminazione delle macchine virtuali classiche rimanenti.  

I servizi e le funzionalità di Azure seguenti NON saranno interessati da questo ritiro:The following Azure services and functionality **NOT** be impacted by this retirement: 
- Servizi cloud 
- Account di archiviazione non usati dalle macchine virtuali classicheStorage accounts **not** used by classic VMs 
- Reti virtuali (VNet) **non** usate dalle macchine virtuali classiche. 
- Altre risorse classiche

## <a name="what-actions-should-i-take"></a>Quali azioni devo intraprendere? 

- Inizia subito a pianificare la migrazione ad Azure Resource Manager. 

- Altre informazioni sulla migrazione delle macchine virtuali Linux e Windows classiche a Azure Resource Manager.Learn [more](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview) about migrating your classic [Linux](./linux/migration-classic-resource-manager-plan.md) and [Windows](./windows/migration-classic-resource-manager-plan.md) VMs to Azure Resource Manager.

- Per altre informazioni, vedere le [domande frequenti sulla migrazione classica a Azure Resource ManagerFor](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq) more information, refer to the Frequently asked asked questions about classic to Azure Resource Manager migration

- Per domande e problemi tecnici, [contattare il supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)tecnico .

- Per altre domande che non fanno parte di FAQ e feedback, commenta di seguito.
