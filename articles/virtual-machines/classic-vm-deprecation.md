---
title: Il ritiro delle macchine virtuali di Azure (versione classica) è il 1 ° marzo 2023
description: Questo articolo fornisce una panoramica generale del ritiro delle macchine virtuali create usando il modello di distribuzione classica.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: 7f2db507176d65e7794607e83db8605b2f892c1c
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89646628"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>Eseguire la migrazione delle risorse IaaS a Azure Resource Manager entro il 1 ° marzo 2023 

In 2014 è stata avviata l'infrastruttura distribuita come servizio (IaaS) in [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). Sono state migliorate le funzionalità da sempre. Poiché Azure Resource Manager ora ha funzionalità complete di IaaS e altri miglioramenti, è stata deprecata la gestione di macchine virtuali (VM) IaaS tramite [Azure Service Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq#what-is-azure-service-manager-and-what-does-it-mean-by-classic) (ASM) il 28 febbraio 2020. Questa funzionalità verrà completamente ritirata il 1 ° marzo 2023. 

Attualmente, circa il 90% delle VM IaaS USA Azure Resource Manager. Se si usano le risorse di IaaS tramite ASM, iniziare a pianificare la migrazione adesso. Completarlo entro il 1 ° marzo 2023 per sfruttare i vantaggi offerti da [Azure Resource Manager](../azure-resource-manager/management/index.yml).

Le macchine virtuali create con il modello di distribuzione classica seguiranno i [criteri del ciclo](https://support.microsoft.com/help/30881/modern-lifecycle-policy) di vita moderni per il ritiro.

## <a name="how-does-this-affect-me"></a>Quali sono le conseguenze di questa modifica? 

- A partire dal 28 febbraio 2020, i clienti che non hanno usato le macchine virtuali IaaS tramite ASM nel mese di febbraio 2020 non possono più creare macchine virtuali (versione classica). 
- Il 1 ° marzo 2023 i clienti non saranno più in grado di avviare macchine virtuali IaaS con ASM. Gli eventuali ancora in esecuzione o allocati verranno arrestati e deallocati. 
- Il 1 ° marzo 2023, le sottoscrizioni di cui non viene eseguita la migrazione a Azure Resource Manager verranno informati sulle sequenze temporali per l'eliminazione di eventuali VM rimanenti (classiche).  

Questo *ritiro non influisce sui* servizi e le funzionalità di Azure seguenti: 
- Servizi cloud di Azure 
- Account di archiviazione *non* usati dalle macchine virtuali (versione classica) 
- Reti virtuali *non* usate dalle macchine virtuali (versione classica) 
- Altre risorse classiche

## <a name="what-actions-should-i-take"></a>Quali azioni è necessario eseguire? 

Inizia a pianificare la migrazione a Azure Resource Manager oggi stesso. 

1. Creare un elenco di tutte le macchine virtuali interessate: 

   - Le VM di tipo **macchine virtuali (versione classica)** nel [riquadro VM portale di Azure](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ClassicCompute%2FVirtualMachines) sono tutte le VM interessate all'interno della sottoscrizione. 
   - È anche possibile eseguire query su Azure Resource Graph usando il [portale](https://portal.azure.com/#blade/HubsExtension/ArgQueryBlade/query/resources%0A%7C%20where%20type%20%3D%3D%20%22microsoft.classiccompute%2Fvirtualmachines%22) o [PowerShell](https://docs.microsoft.com/azure/governance/resource-graph/concepts/work-with-data) per visualizzare l'elenco di tutte le VM contrassegnate (classiche) e le informazioni correlate per le sottoscrizioni selezionate. 
   - L'8 febbraio e il 2 settembre 2020 abbiamo inviato i messaggi di posta elettronica ai proprietari della sottoscrizione con un elenco di tutte le sottoscrizioni che contengono queste macchine virtuali (versione classica). Per compilare questo elenco, usare questi elementi. 

1. [Scopri di più](./windows/migration-classic-resource-manager-overview.md) sulla migrazione di macchine virtuali [Linux](./linux/migration-classic-resource-manager-plan.md) e [Windows](./windows/migration-classic-resource-manager-plan.md) (classiche) a Azure Resource Manager. Per ulteriori informazioni, vedere le [domande frequenti sulla migrazione dal modello classico al Azure Resource Manager](./migration-classic-resource-manager-faq.md).

1. È consigliabile iniziare la pianificazione usando lo [strumento di migrazione del supporto della piattaforma](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview) per eseguire la migrazione delle macchine virtuali esistenti con tre semplici passaggi: convalida, preparazione e commit. Lo strumento è progettato per eseguire la migrazione delle macchine virtuali in modo minimo senza tempi di inattività. 

   1. Il primo passaggio, Validate, non ha alcun effetto sulla distribuzione esistente e fornisce un elenco di tutti gli scenari non supportati per la migrazione. 
   1. Esaminare l' [elenco delle soluzioni alternative](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview#unsupported-features-and-configurations) per correggere la distribuzione e prepararla per la migrazione. 
   1. Idealmente, dopo la correzione di tutti gli errori di convalida, non si verificheranno problemi durante i passaggi di preparazione e commit. Al termine del commit, viene eseguita la migrazione in tempo reale della distribuzione a Azure Resource Manager e può quindi essere gestita tramite le nuove API esposte da Azure Resource Manager. 

   Se lo strumento di migrazione non è adatto alla migrazione, è possibile esplorare [altre offerte di calcolo](https://docs.microsoft.com/azure/architecture/guide/technology-choices/compute-decision-tree) per la migrazione. Poiché esistono molte offerte di calcolo di Azure e sono diverse l'una dall'altra, non è possibile fornire un percorso di migrazione supportato dalla piattaforma.  

1. Per domande tecniche, problemi e supporto per l'aggiunta di sottoscrizioni all'elenco Consenti, [contattare il supporto](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"8a82f77d-c3ab-7b08-d915-776b4ff64ff4"})tecnico.

1. Completare la migrazione appena possibile per evitare l'effetto aziendale e sfruttare le prestazioni, la sicurezza e le nuove funzionalità migliorate del Azure Resource Manager. 

## <a name="what-resources-are-available-for-this-migration"></a>Quali risorse sono disponibili per la migrazione?

- [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-virtual-machines-migration.html): supporto Microsoft e della community per la migrazione.

- [Supporto](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"1135e3d0-20e2-aec5-4ef0-55fd3dae2d58"})per la migrazione di Azure: team di supporto dedicato per assistenza tecnica durante la migrazione.

- [Microsoft Fast Track](https://www.microsoft.com/fasttrack): team che può fornire assistenza tecnica durante la migrazione ai clienti idonei. 

Se la società o l'organizzazione ha collaborato con Microsoft o collabora con i rappresentanti Microsoft (ad esempio Cloud Solution Architects (CSAs) o Technical Account Manager (TAM), collaborare con loro per risorse aggiuntive per la migrazione. 

