---
title: Selezionare un'opzione di migrazione VMware con Azure Migrate migrazione del server | Microsoft Docs
description: Viene fornita una panoramica delle opzioni per la migrazione di macchine virtuali VMware in Azure con Azure Migrate migrazione del server
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 52e7103ea3ebcd83369a866cc3f75b0bf0e889a2
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028726"
---
# <a name="select-a-vmware-migration-option"></a>Selezionare un'opzione di migrazione VMware

È possibile eseguire la migrazione di macchine virtuali VMware in Azure usando lo strumento Migrazione server di Azure Migrate. Questo strumento offre due opzioni per la migrazione di VM VMware:

- Migrazione con replica senza agente. Consente di eseguire la migrazione delle macchine virtuali senza che siano necessarie installazioni nelle VM stesse.
- Migrazione con un agente per la replica. Installare un agente nella VM per la replica.




## <a name="compare-migration-methods"></a>Confrontare i metodi di migrazione

Usare i confronti selezionati per decidere quale metodo usare. È anche possibile esaminare i requisiti di supporto completo per la migrazione senza [agenti](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) e [basata su agenti](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) .

**Impostazione** | **Senza agenti** | **Basata su agenti**
--- | --- | ---
**Autorizzazioni di Azure** | Sono necessarie le autorizzazioni per creare un progetto Azure Migrate e per registrare le app Azure AD create quando si distribuisce il dispositivo Azure Migrate. | Sono necessarie autorizzazioni di collaboratore per la sottoscrizione di Azure. 
**Replica simultanea** | È possibile replicare simultaneamente un massimo di 100 VM da un server vCenter.<br/> Se sono presenti più di 50 VM per la migrazione, creare più batch di macchine virtuali.<br/> La replica di un maggior numero di volte avrà un effetto sulle prestazioni. | ND
**Distribuzione dell'appliance** | Il [Azure migrate Appliance](migrate-appliance.md) viene distribuito in locale. | Il [Azure migrate appliance di replica](migrate-replication-appliance.md) viene distribuito in locale.
**Compatibilità Site Recovery** | Compatibile. | Non è possibile eseguire la replica con Azure Migrate migrazione del server se è stata configurata la replica per un computer con Site Recovery.
**Disco di destinazione** | Managed Disks | Managed Disks
**Limiti del disco** | Disco del sistema operativo: 2 TB<br/><br/> Disco dati: 4 TB<br/><br/> Numero massimo di dischi: 60 | Disco del sistema operativo: 2 TB<br/><br/> Disco dati: 8 TB<br/><br/> Numero massimo di dischi: 63
**Dischi pass-through** | Supporto non disponibile | Supportato
**Avvio UEFI** | Supporto non disponibile | La macchina virtuale di cui è stata eseguita la migrazione in Azure verrà automaticamente convertita in una macchina virtuale di avvio BIOS.<br/><br/> Il disco del sistema operativo deve avere fino a quattro partizioni e i volumi devono essere formattati con NTFS.


## <a name="deployment-steps-comparison"></a>Confronto tra passaggi di distribuzione

Dopo aver esaminato le limitazioni, la comprensione dei passaggi necessari per la distribuzione di ogni soluzione può essere utile per decidere quale opzione scegliere.

**Attività** | **Dettagli** |**Senza agenti** | **Basata su agenti**
--- | --- | --- | ---
**Valutazione** | Valutare i server prima della migrazione.  La valutazione è facoltativa. Prima di eseguire la migrazione, è consigliabile valutare i computer, ma non è necessario. <br/><br/> Per la valutazione, Azure Migrate configura un appliance leggero per individuare e valutare le macchine virtuali. | Se si esegue una migrazione senza agenti dopo la valutazione, lo stesso Azure Migrate Appliance configurato per la valutazione viene usato per la migrazione senza agenti.  |  Se si esegue una migrazione basata su agenti dopo la valutazione, l'appliance configurato per la valutazione non viene usato durante la migrazione senza agenti. È possibile lasciare l'appliance sul posto o rimuoverlo se non si desidera eseguire ulteriori operazioni di individuazione e valutazione.
**Preparare i server e le VM VMware per la migrazione** | Configurare una serie di impostazioni in server e macchine virtuali VMware. | Obbligatorio | Obbligatorio
**Aggiungere lo strumento di migrazione server** | Aggiungere lo strumento di migrazione Azure Migrate server nel progetto Azure Migrate. | Obbligatorio | Obbligatorio
**Distribuire il dispositivo Azure Migrate** | Configurare un'appliance Lightweight in una macchina virtuale VMware per l'individuazione e la valutazione delle macchine virtuali. | Obbligatorio | Non obbligatorio.
**Installare il servizio Mobility nelle macchine virtuali** | Installare il servizio Mobility in ogni macchina virtuale che si vuole replicare | Non obbligatorio | Obbligatorio
**Distribuire il dispositivo di replica della migrazione di Azure Migrate server** | Configurare un'appliance in una macchina virtuale VMware per individuare le macchine virtuali e il Bridge tra il servizio Mobility in esecuzione nelle macchine virtuali e la migrazione di Azure Migrate server | Non obbligatorio | Obbligatorio
**Replicare le macchine virtuali**. Abilitare la replica della macchina virtuale. | Configurare le impostazioni di replica e selezionare le macchine virtuali da replicare | Obbligatorio | Obbligatorio
**Eseguire una migrazione di test** | Eseguire una migrazione di test per verificare che tutti gli elementi funzionino come previsto. | Obbligatorio | Obbligatorio
**Eseguire una migrazione completa** | Eseguire la migrazione delle macchine virtuali. | Obbligatorio | Obbligatorio




## <a name="next-steps"></a>Passaggi successivi

Eseguire la migrazione di [macchine virtuali VMware](tutorial-migrate-vmware.md) con migrazione senza agenti.



