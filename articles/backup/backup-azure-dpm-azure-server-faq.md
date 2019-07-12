---
title: Azure Backup Server e DPM-domande frequenti
description: 'Risposte alle domande comuni su: Il Server di Backup di Azure e Data Protection Manager.'
services: backup
author: srinathvasireddy
manager: sivan
ms.service: backup
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: srinathv
ms.openlocfilehash: 7a598038ee435b67b9ad8f06bdec2490bc1c53c3
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705095"
---
# <a name="azure-backup-server-and-dpm---faq"></a>Server di Backup di Azure e Data Protection Manager - domande frequenti
Questo articolo risponde alle domande frequenti sui Server di Backup di Azure e Data Protection Manager.

### <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-bmr-backup-for-a-physical-server-br"></a>È possibile usare il server di Backup di Azure per creare un backup di ripristino bare metal per un server fisico? <br/>
Sì.

### <a name="can-i-register-the-server-to-multiple-vaults"></a>È possibile registrare un server in più insiemi di credenziali?
No. Un server di Backup di Azure o un server DPM può essere registrato in un solo insieme di credenziali.


### <a name="can-i-use-dpm-to-back-up-apps-in-azure-stack"></a>È possibile usare DPM per eseguire il backup delle App in Azure Stack?
No. È possibile usare Backup di Azure per proteggere Azure Stack. Backup di Azure non supporta l'uso di DPM per eseguire il backup delle app in Azure Stack.

### <a name="if-ive-installed-azure-backup-agent-to-protect-my-files-and-folders-can-i-install-system-center-dpm-to-back-up-on-premises-workloads-to-azure"></a>Se ho installato l'agente Backup di Azure per proteggere i file e le cartelle, posso installare System Center DPM per eseguire il backup dei carichi di lavoro locali in Azure?
Sì. Ma è consigliabile configurare prima DPM e poi installare l'agente di Backup di Azure.  L'installazione dei componenti in questo ordine assicura che l'agente di Backup di Azure funzioni con DPM. L'installazione dell'agente prima di installare DPM non è consigliabile o supportata.

### <a name="why-cant-i-add-an-external-dpm-server-after-installing-ur7-and-latest-azure-backup-agent"></a>Perché non è possibile aggiungere un server DPM esterno dopo l'installazione di UR7 e dell'agente Azure Backup più recente?
Per i server DPM con le origini dati protette nel cloud (utilizzando un aggiornamento cumulativo precedente al 7), è necessario attendere almeno un giorno dopo l'installazione di UR7 e dell'agente Azure Backup più recente per avviare **Aggiungi server DPM esterno**. Questo periodo di un giorno è necessario per caricare i metadati dei gruppi protezione DPM in Azure. I metadati dei gruppi protezione dati vengono caricati la prima volta attraverso un processo notturno.

## <a name="vmware-and-hyper-v-backup"></a>Backup di VMware e Hyper-V

### <a name="can-i-back-up-vmware-vcenter-servers-to-azure"></a>È possibile eseguire il backup dei server VMware vCenter in Azure?
Sì. È possibile usare il server di Backup di Azure per eseguire il backup degli host del server VMware vCenter ed ESXi in Azure.

- [Altre informazioni](backup-mabs-protection-matrix.md) sulle versioni supportate.
- [Attenersi a questa procedura](backup-azure-backup-server-vmware.md) per eseguire il backup di un server VMware.

### <a name="do-i-need-a-separate-license-to-recover-a-full-on-premises-vmwarehyper-v-cluster"></a>Occorre una licenza separata per il ripristino di un cluster VMware/Hyper-V completa in locale?
Per la protezione di VMware/Hyper-V non è necessaria alcuna licenza separata.

- Se si è cliente di System Center, usare System Center Data Protection Manager, ovvero DPM, per proteggere le macchine virtuali VMware.
- Se non si è clienti System Center, per proteggere le macchine virtuali VMware è possibile usare il server di Backup di Azure (con pagamento in base al consumo).


## <a name="sharepoint"></a>SharePoint

### <a name="can-i-recover-a-sharepoint-item-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson-with-protection-on-disk"></a>È possibile ripristinare un elemento di SharePoint nel percorso originale se SharePoint è configurato con SQL AlwaysOn (con protezione su disco)?
Sì, l'elemento può essere ripristinato nel sito originale di SharePoint.

### <a name="can-i-recover-a-sharepoint-database-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson"></a>È possibile ripristinare un database di SharePoint nel percorso originale se SharePoint è configurato con SQL AlwaysOn?
Poiché i database di SharePoint sono configurati in SQL AlwaysOn, non possono essere modificati se non si rimuove il gruppo di disponibilità. Di conseguenza DPM non può ripristinare il database nel percorso originale. È possibile ripristinare un database di SQL Server in un'altra istanza di SQL Server.

## <a name="next-steps"></a>Passaggi successivi

Leggere le altre domande frequenti:

- [Altre informazioni](backup-support-matrix-mabs-dpm.md) matrice di supporto di DPM e sui Server di Backup di Azure.
- [Altre informazioni](backup-azure-mabs-troubleshoot.md) risoluzione dei problemi le linee guida sui Server di Backup di Azure e Data Protection Manager.
