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
ms.openlocfilehash: 0b2b995a6fe4cedd14b2e4ceeddc5747ec2423cf
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/23/2020
ms.locfileid: "88754804"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>Eseguire la migrazione delle risorse IaaS a Azure Resource Manager entro il 1 ° marzo 2023 

In 2014 è stata avviata la IaaS su Azure Resource Manager e sono state migliorate le funzionalità da sempre. Poiché [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) ora ha funzionalità complete di IaaS e altri miglioramenti, è stata deprecata la gestione delle macchine virtuali IaaS tramite [Azure Service Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq#what-is-azure-service-manager-and-what-does-it-mean-by-classic) il 28 febbraio 2020 e questa funzionalità verrà completamente ritirata il 1 ° marzo 2023. 

Attualmente, circa il 90% delle VM IaaS USA Azure Resource Manager. Se si usano le risorse di IaaS tramite Azure Service Manager (ASM), iniziare a pianificare la migrazione adesso e completarla entro il 1 ° marzo 2023 per sfruttare i vantaggi offerti da [Azure Resource Manager](../azure-resource-manager/management/index.yml).

Le macchine virtuali classiche seguiranno i [criteri del ciclo](https://support.microsoft.com/help/30881/modern-lifecycle-policy) di vita moderni per il ritiro.

## <a name="how-does-this-affect-me"></a>Quali sono le conseguenze di questa modifica? 

- A partire dal 28 febbraio 2020, i clienti che non hanno usato le macchine virtuali IaaS tramite Azure Service Manager (ASM) nel mese di febbraio 2020 non saranno più in grado di creare VM classiche. 
- A partire dal 1 ° marzo 2023 i clienti non saranno più in grado di avviare macchine virtuali IaaS con Azure Service Manager e le eventuali ancora in esecuzione o allocate verranno arrestate e deallocate. 
- Il 1 ° marzo 2023, le sottoscrizioni di cui non viene eseguita la migrazione a Azure Resource Manager verranno informati sulle sequenze temporali per l'eliminazione di eventuali VM classiche rimanenti.  

I servizi e le funzionalità di Azure seguenti **non** saranno interessati dal ritiro: 
- Servizi cloud 
- Account di archiviazione **non** usati dalle VM classiche 
- Reti virtuali (reti virtuali) **non** usate dalle VM classiche. 
- Altre risorse classiche

## <a name="what-actions-should-i-take"></a>Quali azioni è necessario eseguire? 

- Inizia a pianificare la migrazione a Azure Resource Manager oggi stesso. 

- Creare un elenco di tutte le macchine virtuali interessate. Il tipo di macchina virtuale "macchine virtuali (classico)" nel pannello della [macchina virtuale del portale di Azure](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ClassicCompute%2FVirtualMachines) sono tutte le VM interessate nella sottoscrizione. 

- [Scopri di più](./windows/migration-classic-resource-manager-overview.md) sulla migrazione di macchine virtuali [Linux](./linux/migration-classic-resource-manager-plan.md) e [Windows](./windows/migration-classic-resource-manager-plan.md) classiche a Azure Resource Manager.

- Per ulteriori informazioni, vedere le [domande frequenti sulla migrazione dal modello classico al Azure Resource Manager](./migration-classic-resource-manager-faq.md)

- Per domande tecniche, problemi e aggiunta di sottoscrizioni all'elenco Consenti, [contattare il supporto](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"8a82f77d-c3ab-7b08-d915-776b4ff64ff4"})tecnico.

- Per altre domande che non fanno parte di domande frequenti e commenti e suggerimenti, commentare qui di seguito.

- Completa la migrazione appena possibile per evitare l'effetto aziendale e sfruttare le migliori prestazioni, la sicurezza & nuove funzionalità fornite da Azure Resource Manager. 

## <a name="what-resources-are-provided-to-me-for-this-migration"></a>Quali risorse sono disponibili per la migrazione?

- [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-virtual-machines-migration.html): supporto Microsoft & community per la migrazione

- [Supporto](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"1135e3d0-20e2-aec5-4ef0-55fd3dae2d58"})per la migrazione di Azure: team di supporto dedicato per l'assistenza tecnica durante la migrazione

- [Microsoft Fast Track](https://www.microsoft.com/fasttrack): il team di Microsoft Fast Track può fornire assistenza tecnica durante la migrazione ai clienti idonei. 

- Se la società o l'organizzazione ha collaborato con Microsoft e/o collabora con Microsoft, ad esempio (Cloud Solution Architect (CSA), Technical Account Manager (TAM), collaborare con loro per risorse aggiuntive per la migrazione. 

