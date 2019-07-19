---
title: Valutare un numero elevato di macchine virtuali VMware per la migrazione ad Azure con Azure Migrate | Microsoft Docs
description: Viene descritto come valutare un numero elevato di macchine virtuali VMware per la migrazione ad Azure usando il servizio Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/12/2019
ms.author: hamusa
ms.openlocfilehash: c9c57a07100f2ea6db86408826bf74d05c8df5aa
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2019
ms.locfileid: "67868672"
---
# <a name="assess-large-numbers-of-vmware-vms-for-migration-to-azure"></a>Valutare un numero elevato di macchine virtuali VMware per la migrazione ad Azure


Questo articolo descrive come valutare numeri elevati (1000-35.000) di macchine virtuali VMware locali per la migrazione ad Azure, usando lo strumento di valutazione di Azure Migrate server

[Azure migrate](migrate-services-overview.md) offre un hub di strumenti che consentono di individuare, valutare ed eseguire la migrazione di app, infrastruttura e carichi di lavoro a Microsoft Azure. L'hub comprende Azure Migrate strumenti e offerte di fornitori di software indipendenti (ISV) di terze parti. 

In questo articolo viene spiegato come:
> [!div class="checklist"]
> * Pianificare la valutazione su larga scala.
> * Configurare le autorizzazioni di Azure e preparare VMware per la valutazione.
> * Creare un progetto Azure Migrate e creare una valutazione.
> * Esaminare la valutazione durante la pianificazione della migrazione.


> [!NOTE]
> Se si vuole provare un modello di prova per valutare un paio di macchine virtuali prima di valutare la scalabilità, seguire la serie di [esercitazioni](tutorial-prepare-vmware.md)

## <a name="plan-for-assessment"></a>Pianificare la valutazione

Quando si pianifica la valutazione di un numero elevato di macchine virtuali VMware, è necessario considerare alcuni aspetti:

- **Pianificare Azure migrate progetti**: Scopri come distribuire i progetti Azure Migrate. Se, ad esempio, i Data Center si trovano in aree geografiche diverse oppure è necessario archiviare i metadati relativi a individuazione, valutazione o migrazione in un'altra area geografica, potrebbero essere necessari più progetti. 
- **Pianificare le appliance**: Azure Migrate usa un'appliance Azure Migrate locale, distribuita come macchina virtuale VMware, per individuare continuamente le VM. Il dispositivo monitora le modifiche apportate all'ambiente, ad esempio l'aggiunta di VM, dischi o schede di rete. Invia inoltre i metadati e i dati sulle prestazioni relativi ad Azure. È necessario scoprire quante Appliance è necessario distribuire.
- **Pianificare gli account per l'individuazione**: Il Azure Migrate Appliance usa un account con accesso a server vCenter per individuare le VM per la valutazione e la migrazione. Se si stanno scoprendo più di 10.000 VM, configurare più account.


## <a name="planning-limits"></a>Limiti di pianificazione
 
Usare i limiti riepilogati in questa tabella per la pianificazione.

**Pianificazione** | **Limiti**
--- | --- 
**Progetti Azure Migrate** | Consente di valutare fino a 35.000 VM in un progetto.
**Appliance Azure Migrate** | Un appliance può connettersi solo a una singola server vCenter.<br/><br/> Un appliance può essere associato solo a un singolo progetto Azure Migrate.<br/> Un dispositivo può individuare fino a 10.000 VM in una server vCenter.
**Valutazione Azure Migrate** | È possibile valutare fino a 35.000 VM in un'unica valutazione.

Tenendo conto di questi limiti, di seguito sono riportate alcune distribuzioni di esempio:


**Server vCenter** | **Macchine virtuali nel server** | **Consiglio** | **Azione**
---|---|---
Uno | < 10.000 | Un progetto Azure Migrate.<br/> Un appliance.<br/> Un account vCenter per l'individuazione. | Configurare il dispositivo, connettersi a server vCenter con un account.
Uno | > 10.000 | Un progetto Azure Migrate.<br/> Più appliance.<br/> Più account vCenter. | Configurare l'appliance per ogni VM 10.000.<br/><br/> Configurare gli account vCenter e dividere l'inventario per limitare l'accesso per un account a meno di 10.000 macchine virtuali.<br/> Connettere ogni appliance al server vCenter con un account.<br/> È possibile analizzare le dipendenze tra computer individuati con dispositivi diversi.
Multipli | < 10.000 |  Un progetto Azure Migrate.<br/> Più appliance.<br/> Un account vCenter per l'individuazione. | Configurare le appliance, connettersi a server vCenter con un account.<br/> È possibile analizzare le dipendenze tra computer individuati con dispositivi diversi.
Multipli | > 10.000 | Un progetto Azure Migrate.<br/> Più appliance.<br/> Più account vCenter. | Se server vCenter individuazione < VM 10.000, configurare un'appliance per ogni server vCenter.<br/><br/> Se server vCenter individuazione > VM 10.000, configurare un'appliance per ogni VM 10.000.<br/> Configurare gli account vCenter e dividere l'inventario per limitare l'accesso per un account a meno di 10.000 macchine virtuali.<br/> Connettere ogni appliance al server vCenter con un account.<br/> È possibile analizzare le dipendenze tra computer individuati con dispositivi diversi.


## <a name="plan-discovery-in-a-multi-tenant-environment"></a>Pianificare l'individuazione in un ambiente multi-tenant

Se si sta pianificando un ambiente multi-tenant, è possibile definire l'ambito dell'individuazione nel server vCenter.

- È possibile impostare l'ambito di individuazione dell'appliance su un server vCenter Data Center, cluster o cartella di cluster, host o cartella di host o singole macchine virtuali.
- Se l'ambiente è condiviso tra i tenant e si vuole individuare separatamente ogni tenant, è possibile definire l'ambito di accesso all'account vCenter usato dall'appliance per l'individuazione. 
    - Se i tenant condividono gli host, può essere utile definire l'ambito per cartelle VM. Azure Migrate non riesce a individuare le VM se l'account vCenter ha accesso concesso a livello di cartella della macchina virtuale vCenter. Se si sta cercando di definire l'ambito di individuazione per cartelle VM, è possibile farlo assicurando che all'account vCenter sia assegnato l'accesso in sola lettura a livello di macchina virtuale. Altre informazioni sull'individuazione dell'ambito sono disponibili [qui](tutorial-assess-vmware.md#scoping-discovery).

## <a name="prepare-for-assessment"></a>Prepararsi per la valutazione

Preparare Azure e VMware per la valutazione del server. 

1. Verificare i [requisiti e le limitazioni del supporto VMware](migrate-support-matrix-vmware.md).
2. Configurare le autorizzazioni per l'account di Azure per interagire con Azure Migrate.
3. Preparare VMware per la valutazione.

Per configurare queste impostazioni, seguire le istruzioni riportate in [questa esercitazione](tutorial-prepare-vmware.md) .


## <a name="create-a-project"></a>Creare un progetto

In conformità ai requisiti di pianificazione, eseguire le operazioni seguenti:

1. Creare un progetto Azure Migrate.
2. Aggiungere lo strumento Azure Migrate server assessment ai progetti.

[Altre informazioni](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>Creare ed esaminare una valutazione

1. Creare valutazioni per le macchine virtuali VMware.
1. Esaminare le valutazioni in preparazione per la pianificazione della migrazione.


Per configurare queste impostazioni, seguire le istruzioni riportate in [questa esercitazione](tutorial-assess-vmware.md) .
    

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si apprenderà come:
 
> [!div class="checklist"] 
> * Pianificazione della scalabilità di Azure Migrate valutazioni per le macchine virtuali VMware
> * Azure e VMware preparati per la valutazione
> * Creazione di un progetto Azure Migrate ed esecuzione di valutazioni
> * Revisione delle valutazioni in preparazione alla migrazione.

A questo punto, [informazioni su come](concepts-assessment-calculation.md) vengono calcolate le valutazioni e su come [modificare le valutazioni](how-to-modify-assessment.md).
