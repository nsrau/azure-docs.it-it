---
title: Valutare un numero elevato di macchine virtuali VMware per la migrazione ad Azure con Azure Migrate
description: Descrive come valutare un numero elevato di macchine virtuali VMware per la migrazione ad Azure usando il servizio Azure Migrate.e
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: d404583b1bad474a5e24e8c7cf060aeb80d610bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336860"
---
# <a name="assess-large-numbers-of-vmware-vms-for-migration-to-azure"></a>Valutare un numero elevato di macchine virtuali VMware per la migrazione ad AzureAssess large numbers of VMware VMs for migration to Azure


In questo articolo viene descritto come valutare numeri grandi (1000-35.000) di macchine virtuali VMware locali per la migrazione ad Azure, usando lo strumento valutazione del server di Azure Migrate.This article describes how to assess large numbers (1000-35,000) of on-premises VMware VMs for migration to Azure, using the Azure Migrate Server Assessment tool.

[Azure Migrate](migrate-services-overview.md) offre un hub di strumenti che consentono di individuare, valutare ed eseguire la migrazione di app, infrastruttura e carichi di lavoro a Microsoft Azure. L'hub include gli strumenti di Azure Migrate e offerte di fornitori di software indipendenti (ISV) di terze parti. 

In questo articolo vengono illustrate le operazioni seguenti:
> [!div class="checklist"]
> * Pianificare la valutazione su larga scala.
> * Configurare le autorizzazioni di Azure e preparare VMware per la valutazione.
> * Creare un progetto Azure Migrate e creare una valutazione.
> * Esaminare la valutazione durante la pianificazione della migrazione.


> [!NOTE]
> Se si vuole provare un proof-of-concept per valutare un paio di macchine virtuali prima di valutare su larga scala, seguire la nostra [serie di tutorial](tutorial-prepare-vmware.md)

## <a name="plan-for-assessment"></a>Piano per la valutazione

Quando si pianifica la valutazione di un numero elevato di macchine virtuali VMware, è necessario considerare un paio di aspetti:When planning for assessment of large number of VMware VMs, there are a couple of things to think about:

- **Pianificare**i progetti di Azure Migrate: informazioni su come distribuire progetti di Azure Migrate.Plan Azure Migrate projects : How out how to deploy Azure Migrate projects. Ad esempio, se i data center si trovano in aree geografiche diverse o è necessario archiviare metadati correlati all'individuazione, alla valutazione o alla migrazione in un'area geografica diversa, potrebbero essere necessari più progetti. 
- **Pianificare le appliance:** Azure Migrate usa un'appliance di Azure Migrate locale, distribuita come macchina virtuale VMware, per individuare continuamente le macchine virtuali. L'appliance monitora le modifiche dell'ambiente, ad esempio l'aggiunta di macchine virtuali, dischi o schede di rete. Invia inoltre metadati e dati sulle prestazioni su di essi ad Azure.It also sends metadata and performance data about them to Azure. È necessario determinare il numero di appliance da distribuire.
- **Pianificare gli account per l'individuazione:** l'appliance di Azure Migrate usa un account con accesso a vCenter Server per individuare le macchine virtuali per la valutazione e la migrazione. Se si stanno scoprendo più di 10.000 macchine virtuali, configurare più account.


## <a name="planning-limits"></a>Limiti di pianificazione
 
Utilizzare i limiti riepilogati in questa tabella per la pianificazione.

**Pianificazione** | **Limiti**
--- | --- 
**Progetti di azure migrateAzure Migrate projects** | Valutare fino a 35.000 macchine virtuali in un progetto.
**Appliance Azure Migrate** | Un'appliance può individuare fino a 10.000 macchine virtuali in un server vCenter.<br/> Un'appliance può connettersi a un solo server vCenter.<br/> Un'appliance può essere associata solo a un singolo progetto di Azure Migrate.An appliance can only be associated with a single Azure Migrate project.<br/>  È possibile associare un numero qualsiasi di appliance a un singolo progetto di Azure Migrate.Any number of appliances can be associated with a single Azure Migrate project. <br/><br/> 
**Gruppo** | È possibile aggiungere fino a 35.000 macchine virtuali in un singolo gruppo.
**Valutazione di Azure MigrateAzure Migrate assessment** | È possibile valutare fino a 35.000 macchine virtuali in un'unica valutazione.

Tenendo presenti questi limiti, ecco alcune distribuzioni di esempio:With these limits in mind, here are some example deployments:


**Server vCenter** | **Macchine virtuali nel server** | **Raccomandazione** | **Azione**
---|---|---
Uno | < 10.000 | Un progetto di azure Migrate.<br/> Un apparecchio.<br/> Un account vCenter per l'individuazione. | Configurare l'appliance, connettersi a vCenter Server con un account.
Uno | > 10.000 | Un progetto di azure Migrate.<br/> Elettrodomestici multipli.<br/> Più account vCenter. | Configurare l'appliance per ogni 10.000 macchine virtuali.<br/><br/> Configurare gli account vCenter e dividere le scorte per limitare l'accesso di un account a meno di 10.000 macchine virtuali.<br/> Connettere ogni appliance al server vCenter con un account.<br/> È possibile analizzare le dipendenze tra computer individuati con appliance diverse.
Multipli | < 10.000 |  Un progetto di azure Migrate.<br/> Elettrodomestici multipli.<br/> Un account vCenter per l'individuazione. | Configurare le appliance, connettersi a vCenter Server con un account.<br/> È possibile analizzare le dipendenze tra computer individuati con appliance diverse.
Multipli | > 10.000 | Un progetto di azure Migrate.<br/> Elettrodomestici multipli.<br/> Più account vCenter. | Se l'individuazione di vCenter Server < 10.000 macchine virtuali, configurare un'appliance per ogni server vCenter.<br/><br/> Se l'individuazione di vCenter Server > 10.000 macchine virtuali, configurare un'appliance per ogni 10.000 macchine virtuali.<br/> Configurare gli account vCenter e dividere le scorte per limitare l'accesso di un account a meno di 10.000 macchine virtuali.<br/> Connettere ogni appliance al server vCenter con un account.<br/> È possibile analizzare le dipendenze tra computer individuati con appliance diverse.


## <a name="plan-discovery-in-a-multi-tenant-environment"></a>Pianificare l'individuazione in un ambiente multi-tenantPlan discovery in a multi-tenant environment

Se si pianifica un ambiente multi-tenant, è possibile definire l'ambito dell'individuazione nel server vCenter.

- È possibile impostare l'ambito di individuazione dell'appliance su un datacenter di vCenter Server, su cluster o su una cartella di cluster, host o cartelle di host o singole macchine virtuali.
- Se l'ambiente è condiviso tra tenant e si desidera individuare ogni tenant separatamente, è possibile definire l'ambito dell'accesso all'account vCenter utilizzato dall'appliance per l'individuazione. 
    - È possibile definire l'ambito in base alle cartelle della macchina virtuale se i tenant condividono gli host. Azure Migrate non è in grado di individuare le macchine virtuali se l'account vCenter ha accesso concesso a livello di cartella VM vCenter. Se si vuole definire l'ambito dell'individuazione in base alle cartelle di VM, è possibile farlo verificando che all'account vCenter sia assegnato l'accesso di sola lettura a livello di VM. [Scopri di più](set-discovery-scope.md).

## <a name="prepare-for-assessment"></a>Prepararsi per la valutazione

Preparare Azure e VMware per la valutazione del server. 

1. Verificare i requisiti e le limitazioni del [supporto VMware](migrate-support-matrix-vmware.md).
2. Configurare le autorizzazioni per l'account Azure per interagire con Azure Migrate.Set up permissions for your Azure account to interact with Azure Migrate.
3. Preparare VMware per la valutazione.

Seguire le istruzioni in [questa esercitazione](tutorial-prepare-vmware.md) per configurare queste impostazioni.


## <a name="create-a-project"></a>Creare un progetto

In conformità con i requisiti di pianificazione, eseguire le operazioni seguenti:

1. Creare un progetto di azure Migrate.Create an Azure Migrate projects.
2. Aggiungere lo strumento di valutazione del server di Azure Migrate ai progetti.

[Scopri di più](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>Creare e rivedere una valutazione

1. Creare valutazioni per le macchine virtuali VMware.Create assessments for VMware VMs.
1. Esaminare le valutazioni in preparazione per la pianificazione della migrazione.


Seguire le istruzioni in [questa esercitazione](tutorial-assess-vmware.md) per configurare queste impostazioni.
    

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si apprenderà come:
 
> [!div class="checklist"] 
> * Pianificato di ridimensionare le valutazioni di Azure Migrate per le macchine virtuali VMware
> * Azure e VMware preparati per la valutazione
> * Creazione di un progetto Azure Migrate ed esecuzione di valutazioni
> * Valutazioni riviste in preparazione per la migrazione.

A [questo](concepts-assessment-calculation.md) punto, scopri come vengono calcolate le valutazioni e come [modificare le valutazioni.](how-to-modify-assessment.md)
