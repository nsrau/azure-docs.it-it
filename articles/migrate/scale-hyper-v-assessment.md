---
title: Valutare un numero elevato di macchine virtuali Hyper-V per la migrazione ad Azure con Azure Migrate. Documenti Microsoft
description: Descrive come valutare un numero elevato di macchine virtuali Hyper-V per la migrazione ad Azure usando il servizio Azure Migrate.Describes how to assess large numbers of Hyper-V VMs for migration to Azure using the Azure Migrate service.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: c1ae3a9ed8a775161aaf85ab2c91b1e43113d2e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70279446"
---
# <a name="assess-large-numbers-of-hyper-v-vms-for-migration-to-azure"></a>Valutare un numero elevato di macchine virtuali Hyper-V per la migrazione ad AzureAssess large numbers of Hyper-V VMs for migration to Azure

Questo articolo descrive come valutare un numero elevato di macchine virtuali Hyper-V locali per la migrazione ad Azure usando lo strumento di valutazione del server di azure migrate.This article describes how to assess large numbers of on-premises Hyper-V VMs for migration to Azure, using the Azure Migrate Server Assessment tool.

[Azure Migrate](migrate-services-overview.md) offre un hub di strumenti che consentono di individuare, valutare ed eseguire la migrazione di app, infrastruttura e carichi di lavoro a Microsoft Azure. L'hub include gli strumenti di Azure Migrate e offerte di fornitori di software indipendenti (ISV) di terze parti. 


In questo articolo vengono illustrate le operazioni seguenti:
> [!div class="checklist"]
> * Pianificare la valutazione su larga scala.
> * Configurare le autorizzazioni di Azure e preparare Hyper-V per la valutazione.
> * Creare un progetto Azure Migrate e creare una valutazione.
> * Esaminare la valutazione durante la pianificazione della migrazione.


> [!NOTE]
> Se si vuole provare un proof-of-concept per valutare un paio di macchine virtuali prima di valutare su larga scala, seguire la nostra [serie di tutorial](tutorial-prepare-hyper-v.md)

## <a name="plan-for-assessment"></a>Piano per la valutazione

Quando si pianifica la valutazione di un numero elevato di macchine virtuali Hyper-V, è necessario considerare un paio di aspetti:When planning for assessment of large number of Hyper-V V Ms, there are a couple of things to think about:

- **Pianificare**i progetti di Azure Migrate: informazioni su come distribuire progetti di Azure Migrate.Plan Azure Migrate projects : How out how to deploy Azure Migrate projects. Ad esempio, se i data center si trovano in aree geografiche diverse o è necessario archiviare metadati correlati all'individuazione, alla valutazione o alla migrazione in un'area geografica diversa, potrebbero essere necessari più progetti.
- **Pianificare le appliance:** Azure Migrate usa un'appliance di Azure Migrate locale, distribuita come macchina virtuale Hyper-V, per individuare continuamente le macchine virtuali per la valutazione e la migrazione. L'appliance monitora le modifiche dell'ambiente, ad esempio l'aggiunta di macchine virtuali, dischi o schede di rete. Invia inoltre metadati e dati sulle prestazioni su di essi ad Azure.It also sends metadata and performance data about them to Azure. È necessario determinare il numero di appliance da distribuire.


## <a name="planning-limits"></a>Limiti di pianificazione
 
Utilizzare i limiti riepilogati in questa tabella per la pianificazione.

**Pianificazione** | **Limiti**
--- | --- 
**Progetti di azure migrateAzure Migrate projects** | Valutare fino a 35.000 macchine virtuali in un progetto.
**Appliance Azure Migrate** | Un'appliance può individuare fino a 5000 macchine virtuali.<br/> Un'appliance può connettersi a un massimo di 300 host Hyper-V.<br/> Un'appliance può essere associata solo a un singolo progetto di Azure Migrate.An appliance can only be associated with a single Azure Migrate project.<br/> È possibile associare un numero qualsiasi di appliance a un singolo progetto di Azure Migrate.Any number of appliances can be associated with a single Azure Migrate project. <br/><br/> 
**Gruppo** | È possibile aggiungere fino a 35.000 macchine virtuali in un singolo gruppo.
**Valutazione di Azure MigrateAzure Migrate assessment** | È possibile valutare fino a 35.000 macchine virtuali in un'unica valutazione.



## <a name="other-planning-considerations"></a>Altre considerazioni sulla pianificazione

- Per avviare l'individuazione dall'appliance, è necessario selezionare ogni host Hyper-V. 
- Se si esegue un ambiente multi-tenant, non è attualmente possibile individuare solo le macchine virtuali che appartengono a un tenant specifico. 

## <a name="prepare-for-assessment"></a>Prepararsi per la valutazione

Preparare Azure e Hyper-V per la valutazione del server. 

1. Verificare i requisiti e le limitazioni di supporto di [Hyper-V](migrate-support-matrix-hyper-v.md).
2. Configurare le autorizzazioni per l'account Azure per interagire con Azure MigrateSet up permissions for your Azure account to interact with Azure Migrate
3. Preparare gli host e le macchine virtuali Hyper-VPrepare Hyper-V hosts and VMs

Seguire le istruzioni in [questa esercitazione](tutorial-prepare-hyper-v.md) per configurare queste impostazioni.

## <a name="create-a-project"></a>Creare un progetto

In conformità con i requisiti di pianificazione, eseguire le operazioni seguenti:

1. Creare un progetto di azure Migrate.Create an Azure Migrate projects.
2. Aggiungere lo strumento di valutazione del server di Azure Migrate ai progetti.

[Scopri di più](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>Creare e rivedere una valutazione

1. Creare valutazioni per le macchine virtuali Hyper-V.Create assessments for Hyper-V VMs.
1. Esaminare le valutazioni in preparazione per la pianificazione della migrazione.

[Ulteriori informazioni](tutorial-assess-hyper-v.md) sulla creazione e la revisione delle valutazioni.
    

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si apprenderà come:
 
> [!div class="checklist"] 
> * Pianificato per ridimensionare le valutazioni di Azure Migrate per le macchine virtuali Hyper-V
> * Azure preparato e Hyper-V per la valutazione
> * Creazione di un progetto Azure Migrate ed esecuzione di valutazioni
> * Valutazioni riviste in preparazione per la migrazione.

A [questo punto, scopri come](concepts-assessment-calculation.md) vengono calcolate le valutazioni e come [modificare le valutazioni](how-to-modify-assessment.md)
