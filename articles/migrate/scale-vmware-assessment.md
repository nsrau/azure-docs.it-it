---
title: Valutare un elevato numero di macchine virtuali VMware per la migrazione ad Azure con Azure Migrate | Microsoft Docs
description: Viene descritto come valutare un elevato numero di macchine virtuali VMware per la migrazione ad Azure tramite il servizio Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: 6102a1c59be3627b95dc1e0cb1d1d712d5832d2f
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811336"
---
# <a name="assess-large-numbers-of-vmware-vms-for-migration-to-azure"></a>Valutare un elevato numero di macchine virtuali VMware per la migrazione ad Azure


Questo articolo descrive come valutare un numero elevato (1000 35000) delle macchine virtuali VMware locali per la migrazione ad Azure usando lo strumento di valutazione di Azure Migrate Server

[Azure Migrate](migrate-services-overview.md) fornisce un hub di strumenti che consentono di individuare, valutare e la migrazione di App, infrastruttura e i carichi di lavoro in Microsoft Azure. L'hub include strumenti di Azure Migrate e le offerte di fornitori di software indipendenti di terze parti. 

In questo articolo viene spiegato come:
> [!div class="checklist"]
> * Piano per la valutazione su larga scala.
> * Configurare le autorizzazioni di Azure e preparare VMware per la valutazione.
> * Creare un progetto Azure Migrate e creare una valutazione.
> * Quando si pianifica la migrazione, rivedere la valutazione.


> [!NOTE]
> Se si vuole provare un proof-of-concept per valutare un paio di macchine virtuali di prima fase di valutazione su larga scala, seguire la [serie di esercitazioni](tutorial-prepare-vmware.md)

## <a name="plan-for-assessment"></a>Piano per la valutazione di

Durante la pianificazione per la valutazione di un numero elevato di macchine virtuali VMware, esistono un paio di aspetti da considerare:

- **Pianificare i progetti Azure Migrate**: Scoprire come distribuire i progetti Azure Migrate. Ad esempio, se è necessario archiviare il rilevamento, valutazione o i metadati correlati alla migrazione in una diversa area geografica dei data center sono in aree geografiche diverse oppure potrebbe essere più progetti. 
- **Pianificare Appliance**: Azure Migrate Usa una Azure Migrate appliance locale, distribuita come VM VMware, per individuare continuamente le macchine virtuali. L'appliance consente di monitorare le modifiche dell'ambiente, ad esempio l'aggiunta di macchine virtuali, dischi o schede di rete. Anche invia i dati dei metadati e le prestazioni riguardo ad Azure. È necessario stabilire quanti dispositivi è necessario distribuire.
- **Pianificare gli account per l'individuazione**: L'appliance Azure Migrate Usa un account con accesso al Server vCenter per individuare le macchine virtuali per la valutazione e migrazione. Se si sta individuando più di 10.000 macchine virtuali, configurare più account.


## <a name="planning-limits"></a>Pianificazione di limiti
 
Usare i limiti riepilogati nella tabella seguente per la pianificazione.

**Pianificazione** | **Limiti**
--- | --- 
**Progetti di Azure Migrate** | Consente di valutare fino a 35.000 macchine virtuali in un progetto.
**Appliance Azure Migrate** | Un dispositivo può connettersi solo a un singolo server vCenter Server.<br/><br/> Può essere associata a un singolo progetto Azure Migrate solo un'appliance.<br/> Un'appliance può individuare fino a 10.000 macchine virtuali in un Server vCenter.
**Valutazione di Azure Migrate** | È possibile valutare fino a 35.000 macchine virtuali in una singola valutazione.

Con questi limiti mente, ecco alcuni esempi di distribuzione:


**Server vCenter** | **Macchine virtuali nel server** | **Consiglio** | **Azione**
---|---|---
Uno | < 10,000 | Un progetto Azure Migrate.<br/> Un'appliance.<br/> Un account vCenter per l'individuazione. | Configurare l'appliance, connettersi al Server vCenter con un account.
Uno | > 10,000 | Un progetto Azure Migrate.<br/> Più dispositivi.<br/> Più account vCenter. | Configurare l'appliance per ogni 10.000 VM.<br/><br/> Configurare gli account vCenter e dividere inventario per limitare l'accesso per un account per le macchine virtuali meno di 10.000.<br/> Connettere ogni appliance per il server vCenter con un account.<br/> È possibile analizzare le dipendenze tra computer in cui vengono individuati con i dispositivi diversi.
Multipli | < 10,000 |  Un progetto Azure Migrate.<br/> Più dispositivi.<br/> Un account vCenter per l'individuazione. | Configurare gli accessori, connettersi al Server vCenter con un account.<br/> È possibile analizzare le dipendenze tra computer in cui vengono individuati con i dispositivi diversi.
Multipli | > 10,000 | Un progetto Azure Migrate.<br/> Più dispositivi.<br/> Più account vCenter. | Se individuazione di vCenter Server le macchine virtuali < 10.000, configurare un'appliance per ognuno dei Server vCenter.<br/><br/> Se individuazione del Server vCenter > 10.000 macchine virtuali, configurare un dispositivo per ogni 10.000 macchine virtuali.<br/> Configurare gli account vCenter e dividere inventario per limitare l'accesso per un account per le macchine virtuali meno di 10.000.<br/> Connettere ogni appliance per il server vCenter con un account.<br/> È possibile analizzare le dipendenze tra computer in cui vengono individuati con i dispositivi diversi.


## <a name="plan-discovery-in-a-multi-tenant-environment"></a>Pianificare l'individuazione in un ambiente multi-tenant

Se si intende per un ambiente multi-tenant, è possibile definire l'ambito di individuazione nel Server vCenter.

- È possibile impostare l'ambito dell'individuazione dispositivo a un vCenter Server datacenter, cluster o una cartella della cartella di host o alle singole macchine virtuali, host o cluster.
- Se l'ambiente viene condiviso tra i tenant e non si vuole individuare separatamente ogni tenant, è possibile definire l'ambito di accesso all'account vCenter usato per l'individuazione dell'appliance. 
    - Se i tenant condividono gli host, creare credenziali con accesso in sola lettura per le macchine virtuali che appartengono al tenant specifico. 
    - Usare queste credenziali per l'individuazione di appliance Azure Migrate.
    - Valutazione di Azure Migrate non è possibile individuare le macchine virtuali se vCenter ha accesso concesso a livello di cartella vCenter della macchina virtuale. Le cartelle di host e cluster sono supportate. 

## <a name="prepare-for-assessment"></a>Preparare per la valutazione di

Preparare per la valutazione di server di Azure e VMware. 

1. Verificare [VMware supportano i requisiti e limitazioni](migrate-support-matrix-vmware.md).
2. Impostare le autorizzazioni per l'account di Azure interagire con Azure Migrate.
3. Preparare VMware per la valutazione.


Seguire le istruzioni in [in questa esercitazione](tutorial-prepare-vmware.md) configurare queste impostazioni.


## <a name="create-a-project"></a>Creare un progetto

In base alle necessità di pianificazione, eseguire le operazioni seguenti:

1. Creare progetti di Azure Migrate.
2. Aggiungere lo strumento di valutazione di Azure Migrate Server per i progetti.

[Altre informazioni](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>Creare ed esaminare una valutazione

1. Creare le valutazioni per le macchine virtuali VMware.
1. Esaminare le valutazioni in preparazione per la pianificazione della migrazione.


Seguire le istruzioni in [in questa esercitazione](tutorial-assess-vmware.md) configurare queste impostazioni.
    

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si apprenderà come:
 
> [!div class="checklist"] 
> * Pianificato per la scalabilità di Azure Migrate valutazioni per le macchine virtuali VMware
> * Preparati Azure e VMware per la valutazione di
> * Creazione di un progetto Azure Migrate ed esecuzione delle valutazioni
> * Rivedere le valutazioni in preparazione per la migrazione.

A questo punto [informazioni su come](concepts-assessment-calculation.md) vengono calcolate le valutazioni e come [modificare valutazioni](how-to-modify-assessment.md).
