---
title: Selezionare un'opzione di migrazione di VMware con Azure Migrate Server Migration . Documenti Microsoft
description: Fornisce una panoramica delle opzioni per la migrazione delle macchine virtuali VMware ad Azure con la migrazione del server di Azure Migrate
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 52e7103ea3ebcd83369a866cc3f75b0bf0e889a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76028726"
---
# <a name="select-a-vmware-migration-option"></a>Selezionare un'opzione di migrazione VMware

È possibile eseguire la migrazione di macchine virtuali VMware in Azure usando lo strumento Migrazione server di Azure Migrate. Questo strumento offre due opzioni per la migrazione di VM VMware:

- Migrazione con replica senza agente. Consente di eseguire la migrazione delle macchine virtuali senza che siano necessarie installazioni nelle VM stesse.
- Migrazione con un agente per la replica. Installare un agente nella VM per la replica.




## <a name="compare-migration-methods"></a>Confrontare i metodi di migrazione

Utilizzare questi confronti selezionati per decidere quale metodo utilizzare. È inoltre possibile esaminare i requisiti di supporto completi per la migrazione [senza agenti](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) e [basata su agente.](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers)

**Impostazione** | **Senza agente** | **Basato su agente**
--- | --- | ---
**Autorizzazioni di Azure** | Sono necessarie le autorizzazioni per creare un progetto di Azure Migrate e registrare le app di Azure AD create quando si distribuisce l'appliance di Azure Migrate.You need permissions to create an Azure Migrate project, and to register Azure AD apps created when you deploy the Azure Migrate appliance. | Sono necessarie le autorizzazioni di collaboratore per la sottoscrizione di Azure.You need Contributor permissions on the Azure subscription. 
**Replica simultanea** | È possibile replicare contemporaneamente un massimo di 100 macchine virtuali da un server vCenter.<br/> Se si dispone di più di 50 macchine virtuali per la migrazione, creare più batch di macchine virtuali.<br/> La replica di più in una sola volta influirà sulle prestazioni. | ND
**Distribuzione dell'appliance** | [L'appliance di Azure Migrate](migrate-appliance.md) viene distribuita in locale. | [L'appliance di replica di Azure Migrate](migrate-replication-appliance.md) viene distribuita in locale.
**Compatibilità di Site Recovery** | Compatibile. | Non è possibile eseguire la replica con Azure Migrate Server Migration se è stata configurata la replica per un computer tramite Site Recovery.
**Disco di destinazione** | Dischi gestiti | Dischi gestiti
**Limiti del disco** | Disco del sistema operativo: 2 TB<br/><br/> Disco dati: 4 TB<br/><br/> Dischi massimi: 60 | Disco del sistema operativo: 2 TB<br/><br/> Disco dati: 8 TB<br/><br/> Dischi massimi: 63
**Dischi pass-through** | Non supportate | Supportato
**Stivale UEFI** | Non supportate | La macchina virtuale migrata in Azure verrà convertita automaticamente in una macchina virtuale di avvio del BIOS.<br/><br/> Il disco del sistema operativo deve avere fino a quattro partizioni e i volumi devono essere formattati con NTFS.


## <a name="deployment-steps-comparison"></a>Confronto dei passaggi di distribuzione

Dopo aver esaminato le limitazioni, la comprensione dei passaggi necessari per la distribuzione di ogni soluzione può essere utile decidere quale opzione scegliere.

**Attività** | **Dettagli** |**Senza agente** | **Basato su agente**
--- | --- | --- | ---
**Valutazione** | Valutare i server prima della migrazione.  La valutazione è facoltativa. Ti consigliamo di valutare i computer prima di eseguirne la migrazione, ma non è necessario. <br/><br/> Per la valutazione, Azure Migrate configura un'appliance leggera per individuare e valutare le macchine virtuali. | Se si esegue una migrazione senza agente dopo la valutazione, per la migrazione senza agente viene usata la stessa appliance di Azure Migrate configurata per la valutazione.  |  Se si esegue una migrazione basata su agente dopo la valutazione, l'appliance configurata per la valutazione non viene utilizzata durante la migrazione senza agente. È possibile lasciare l'appliance sul posto o rimuoverlo se non si desidera eseguire ulteriori operazioni di individuazione e valutazione.
**Preparare server VMware e macchine virtuali per la migrazione** | Configurare una serie di impostazioni nei server VMware e nelle macchine virtuali. | Obbligatoria | Obbligatoria
**Aggiungere lo strumento di migrazione server** | Aggiungere lo strumento di migrazione del server di Azure Migrate nel progetto Azure Migrate.Add the Azure Migrate Server Migration tool in the Azure Migrate project. | Obbligatoria | Obbligatoria
**Distribuire l'appliance di Azure MigrateDeploy the Azure Migrate appliance** | Configurare un'appliance leggera in una macchina virtuale VMware per l'individuazione e la valutazione delle macchine virtuali. | Obbligatoria | Non obbligatorio.
**Installare il servizio Mobility nelle macchine virtualiInstall the Mobility service on VMs** | Installare il servizio Mobility in ogni macchina virtuale da replicare | Facoltativo | Obbligatoria
**Distribuire l'appliance di replica di Migrazione del server di Azure MigrateDeploy the Azure Migrate Server Migration replication appliance** | Configurare un'appliance in una macchina virtuale VMware per individuare le macchine virtuali e creare un collegamento tra il servizio Mobility in esecuzione nelle macchine virtuali e la migrazione del server di migrazione di Azure Migrate | Facoltativo | Obbligatoria
**Replicare le macchine virtuali**. Abilitare la replica delle macchine virtuali. | Configurare le impostazioni di replica e selezionare le macchine virtuali da replicare | Obbligatoria | Obbligatoria
**Eseguire una migrazione di test** | Eseguire una migrazione di test per verificare che tutti gli elementi funzionino come previsto. | Obbligatoria | Obbligatoria
**Eseguire una migrazione completa** | Eseguire la migrazione delle macchine virtuali. | Obbligatoria | Obbligatoria




## <a name="next-steps"></a>Passaggi successivi

[Eseguire la migrazione di macchine virtuali VMware](tutorial-migrate-vmware.md) con migrazione senza agente.



