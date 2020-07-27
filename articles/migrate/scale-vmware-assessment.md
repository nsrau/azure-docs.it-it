---
title: Valutare un numero elevato di macchine virtuali VMware per la migrazione ad Azure con Azure Migrate
description: Viene descritto come valutare un numero elevato di macchine virtuali VMware per la migrazione ad Azure usando il servizio Azure Migrate. e
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: 6490a5448bb68dcccd61784d149e9765107400c2
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87171920"
---
# <a name="assess-large-numbers-of-vmware-vms-for-migration-to-azure"></a>Valutare un numero elevato di macchine virtuali VMware per la migrazione ad Azure


Questo articolo descrive come valutare numeri elevati (1000-35.000) di macchine virtuali VMware locali per la migrazione ad Azure, usando lo strumento di valutazione di Azure Migrate server.

[Azure Migrate](migrate-services-overview.md) offre un hub di strumenti che consentono di individuare, valutare ed eseguire la migrazione di app, infrastruttura e carichi di lavoro a Microsoft Azure. L'hub include gli strumenti di Azure Migrate e offerte di fornitori di software indipendenti (ISV) di terze parti. 

In questo articolo vengono illustrate le operazioni seguenti:
> [!div class="checklist"]
> * Pianificare la valutazione su larga scala.
> * Configurare le autorizzazioni di Azure e preparare VMware per la valutazione.
> * Creare un progetto Azure Migrate e creare una valutazione.
> * Esaminare la valutazione durante la pianificazione della migrazione.


> [!NOTE]
> Se si vuole provare un modello di prova per valutare un paio di macchine virtuali prima di valutare la scalabilità, seguire la serie di [esercitazioni](tutorial-prepare-vmware.md)

## <a name="plan-for-assessment"></a>Pianificare la valutazione

Quando si pianifica la valutazione di un numero elevato di macchine virtuali VMware, è necessario considerare alcuni aspetti:

- **Pianificare Azure migrate progetti**: scoprire come distribuire progetti di Azure migrate. Se, ad esempio, i Data Center si trovano in aree geografiche diverse oppure è necessario archiviare i metadati relativi a individuazione, valutazione o migrazione in un'altra area geografica, potrebbero essere necessari più progetti. 
- **Appliance del piano**: Azure migrate usa un'appliance Azure migrate locale, distribuita come macchina virtuale VMware, per individuare continuamente le VM. Il dispositivo monitora le modifiche apportate all'ambiente, ad esempio l'aggiunta di VM, dischi o schede di rete. Invia inoltre i metadati e i dati sulle prestazioni relativi ad Azure. È necessario scoprire quante Appliance è necessario distribuire.
- **Pianificare gli account per l'individuazione**: il Azure migrate Appliance usa un account con accesso a server vCenter per individuare le VM per la valutazione e la migrazione. Se si stanno individuando più di 10.000 VM, configurare più account perché è necessario che non esistano sovrapposizioni tra le macchine virtuali individuate da due appliance di un progetto. 

> [!NOTE]
> Se si configurano più appliance, assicurarsi che non vi sia sovrapposizione tra le macchine virtuali negli account vCenter forniti. Un'individuazione con una sovrapposizione di questo tipo è uno scenario non supportato. Se una macchina virtuale viene individuata da più di un appliance, si verificano duplicati nell'individuazione e nei problemi durante l'abilitazione della replica per la macchina virtuale usando il portale di Azure nella migrazione del server.

## <a name="planning-limits"></a>Limiti di pianificazione
 
Usare i limiti riepilogati in questa tabella per la pianificazione.

**Pianificazione** | **Limiti**
--- | --- 
**Progetti Azure Migrate** | Consente di valutare fino a 35.000 VM in un progetto.
**Appliance Azure Migrate** | Un dispositivo può individuare fino a 10.000 VM in una server vCenter.<br/> Un appliance può connettersi solo a una singola server vCenter.<br/> Un appliance può essere associato solo a un singolo progetto Azure Migrate.<br/>  Un numero qualsiasi di Appliance può essere associato a un singolo progetto di Azure Migrate. <br/><br/> 
**Gruppo** | È possibile aggiungere fino a 35.000 VM in un singolo gruppo.
**Valutazione Azure Migrate** | È anche possibile valutare fino a 35.000 macchine virtuali in una singola valutazione.

Tenendo conto di questi limiti, di seguito sono riportate alcune distribuzioni di esempio:


**server vCenter** | **Macchine virtuali nel server** | **Consiglio** | **Azione**
---|---|---|---
Uno | < 10.000 | Un progetto Azure Migrate.<br/> Un appliance.<br/> Un account vCenter per l'individuazione. | Configurare il dispositivo, connettersi a server vCenter con un account.
Uno | > 10.000 | Un progetto Azure Migrate.<br/> Più appliance.<br/> Più account vCenter. | Configurare l'appliance per ogni VM 10.000.<br/><br/> Configurare gli account vCenter e dividere l'inventario per limitare l'accesso per un account a meno di 10.000 macchine virtuali.<br/> Connettere ogni appliance al server vCenter con un account.<br/> È possibile analizzare le dipendenze tra computer individuati con dispositivi diversi. <br/> <br/> Assicurarsi che non vi sia sovrapposizione tra le macchine virtuali negli account vCenter forniti. Un'individuazione con una sovrapposizione di questo tipo è uno scenario non supportato. Se una macchina virtuale viene individuata da più di un appliance, si verificano duplicati nell'individuazione e nei problemi durante l'abilitazione della replica per la macchina virtuale usando il portale di Azure nella migrazione del server.
Più elementi | < 10.000 |  Un progetto Azure Migrate.<br/> Più appliance.<br/> Un account vCenter per l'individuazione. | Configurare le appliance, connettersi a server vCenter con un account.<br/> È possibile analizzare le dipendenze tra computer individuati con dispositivi diversi.
Più elementi | > 10.000 | Un progetto Azure Migrate.<br/> Più appliance.<br/> Più account vCenter. | Se server vCenter individuazione < VM 10.000, configurare un'appliance per ogni server vCenter.<br/><br/> Se server vCenter individuazione > VM 10.000, configurare un'appliance per ogni VM 10.000.<br/> Configurare gli account vCenter e dividere l'inventario per limitare l'accesso per un account a meno di 10.000 macchine virtuali.<br/> Connettere ogni appliance al server vCenter con un account.<br/> È possibile analizzare le dipendenze tra computer individuati con dispositivi diversi. <br/><br/> Assicurarsi che non vi sia sovrapposizione tra le macchine virtuali negli account vCenter forniti. Un'individuazione con una sovrapposizione di questo tipo è uno scenario non supportato. Se una macchina virtuale viene individuata da più di un appliance, si verificano duplicati nell'individuazione e nei problemi durante l'abilitazione della replica per la macchina virtuale usando il portale di Azure nella migrazione del server.



## <a name="plan-discovery-in-a-multi-tenant-environment"></a>Pianificare l'individuazione in un ambiente multi-tenant

Se si sta pianificando un ambiente multi-tenant, è possibile definire l'ambito dell'individuazione nel server vCenter.

- È possibile impostare l'ambito di individuazione dell'appliance su un server vCenter Data Center, cluster o cartella di cluster, host o cartella di host o singole macchine virtuali.
- Se l'ambiente è condiviso tra i tenant e si vuole individuare separatamente ogni tenant, è possibile definire l'ambito di accesso all'account vCenter usato dall'appliance per l'individuazione. 
    - Se i tenant condividono gli host, può essere utile definire l'ambito per cartelle VM. Azure Migrate non riesce a individuare le VM se l'account vCenter ha accesso concesso a livello di cartella della macchina virtuale vCenter. Se si vuole definire l'ambito dell'individuazione in base alle cartelle di VM, è possibile farlo verificando che all'account vCenter sia assegnato l'accesso di sola lettura a livello di VM. [Altre informazioni](set-discovery-scope.md).

## <a name="prepare-for-assessment"></a>Preparare la valutazione

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
