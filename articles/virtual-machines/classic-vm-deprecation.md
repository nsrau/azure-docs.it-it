---
title: Verranno ritirate le VM di Azure classico il 1 ° marzo 2023
description: Questo articolo fornisce una panoramica generale del ritiro delle VM classiche
services: virtual-machines
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: e56aa5ec073aadc2a16d53c266d33255a34077cb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80668798"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>Eseguire la migrazione delle risorse IaaS a Azure Resource Manager entro il 1 ° marzo 2023 

In 2014 è stata avviata la IaaS su Azure Resource Manager e sono state migliorate le funzionalità da sempre. Poiché [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) ora ha funzionalità complete di IaaS e altri miglioramenti, è stata deprecata la gestione delle macchine virtuali IaaS tramite Azure Service Manager il 28 febbraio 2020 e questa funzionalità verrà completamente ritirata il 1 ° marzo 2023. 

Attualmente, circa il 90% delle VM IaaS USA Azure Resource Manager. Se si usano le risorse di IaaS tramite Azure Service Manager (ASM), iniziare a pianificare la migrazione adesso e completarla entro il 1 ° marzo 2023 per sfruttare i vantaggi offerti da [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/).

Le macchine virtuali classiche seguiranno i [criteri del ciclo](https://support.microsoft.com/help/30881/modern-lifecycle-policy) di vita moderni per il ritiro.

## <a name="how-does-this-affect-me"></a>Quali sono le conseguenze di questa modifica? 

1) A partire dal 28 febbraio 2020, i clienti che non hanno usato le macchine virtuali IaaS tramite Azure Service Manager (ASM) nel mese di febbraio 2020 non saranno più in grado di creare VM classiche. 
2) A partire dal 1 ° marzo 2023 i clienti non saranno più in grado di avviare macchine virtuali IaaS con Azure Service Manager e le eventuali ancora in esecuzione o allocate verranno arrestate e deallocate. 
2) Il 1 ° marzo 2023, le sottoscrizioni che non hanno eseguito la migrazione a Azure Resource Manager verranno informati sulle sequenze temporali per l'eliminazione di tutte le macchine virtuali classiche rimanenti.  

I servizi e le funzionalità di Azure seguenti **non** saranno interessati dal ritiro: 
- Servizi cloud 
- Account di archiviazione **non** usati dalle VM classiche 
- Reti virtuali (reti virtuali) **non** usate dalle VM classiche. 
- Altre risorse classiche

## <a name="what-actions-should-i-take"></a>Quali azioni è necessario eseguire? 

- Inizia a pianificare la migrazione a Azure Resource Manager oggi stesso. 

- [Scopri di più](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview) sulla migrazione di macchine virtuali [Linux](./linux/migration-classic-resource-manager-plan.md) e [Windows](./windows/migration-classic-resource-manager-plan.md) classiche a Azure Resource Manager.

- Per ulteriori informazioni, vedere le [domande frequenti sulla migrazione dal modello classico al Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq)

- Per domande tecniche, problemi e supporto per i [contatti](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)per la sottoscrizione.

- Per altre domande che non fanno parte di domande frequenti e commenti e suggerimenti, commentare qui di seguito.
