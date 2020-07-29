---
title: Verranno ritirate le VM di Azure classico il 1 ° marzo 2023
description: Questo articolo fornisce una panoramica generale del ritiro delle VM classiche
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: 6ddc9299f72e8a0b25e8b1f85968933bd6868af8
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87288537"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>Eseguire la migrazione delle risorse IaaS a Azure Resource Manager entro il 1 ° marzo 2023 

In 2014 è stata avviata la IaaS su Azure Resource Manager e sono state migliorate le funzionalità da sempre. Poiché [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) ora ha funzionalità complete di IaaS e altri miglioramenti, è stata deprecata la gestione delle macchine virtuali IaaS tramite Azure Service Manager il 28 febbraio 2020 e questa funzionalità verrà completamente ritirata il 1 ° marzo 2023. 

Attualmente, circa il 90% delle VM IaaS USA Azure Resource Manager. Se si usano le risorse di IaaS tramite Azure Service Manager (ASM), iniziare a pianificare la migrazione adesso e completarla entro il 1 ° marzo 2023 per sfruttare i vantaggi offerti da [Azure Resource Manager](../azure-resource-manager/management/index.yml).

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

- [Scopri di più](./windows/migration-classic-resource-manager-overview.md) sulla migrazione di macchine virtuali [Linux](./linux/migration-classic-resource-manager-plan.md) e [Windows](./windows/migration-classic-resource-manager-plan.md) classiche a Azure Resource Manager.

- Per ulteriori informazioni, vedere le [domande frequenti sulla migrazione dal modello classico al Azure Resource Manager](./windows/migration-classic-resource-manager-faq.md)

- Per domande tecniche, problemi e aggiunta di sottoscrizioni all'elenco Consenti, [contattare il supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)tecnico.

- Per altre domande che non fanno parte di domande frequenti e commenti e suggerimenti, commentare qui di seguito.
