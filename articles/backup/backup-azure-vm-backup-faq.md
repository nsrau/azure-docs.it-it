---
title: Domande frequenti sul servizio Backup delle macchine virtuali di Azure | Microsoft Docs
description: Risposte alle domande frequenti sul funzionamento del backup delle macchine virtuali di Azure, sulle relative limitazioni e su cosa accade quando si modificano i criteri
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
keywords: backup di macchine virtuali di Azure, ripristino di macchine virtuali di Azure, criteri di backup
ms.assetid: c4cd7ff6-8206-45a3-adf5-787f64dbd7e1
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/18/2017
ms.author: trinadhk;pullabhk;
ms.openlocfilehash: 1372a9e05cb47f6c68240bffccd46b0fbebb5464
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="questions-about-the-azure-vm-backup-service"></a>Domande sul servizio Backup delle macchine virtuali di Azure
Questo articolo contiene risposte a domande comuni che consentiranno di comprendere rapidamente i componenti del servizio Backup delle macchine virtuali di Azure. Alcune risposte includono collegamenti ad articoli con informazioni complete. È anche possibile inserire le domande sul servizio Backup di Azure nel [forum di discussione](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

## <a name="configure-backup"></a>Configurare il backup
### <a name="do-recovery-services-vaults-support-classic-vms-or-resource-manager-based-vms-br"></a>Gli insiemi di credenziali di Servizi di ripristino supportano le macchine virtuali in modalità classica o quelle basate su Resource Manager? <br/>
Gli insiemi di credenziali di Servizi di ripristino supportano entrambi modelli.  È possibile eseguire il backup di una macchina virtuale creata nel portale classico o di una macchina virtuale di Resource Manager creata nel portale di Azure nell'insieme di credenziali di Servizi di ripristino.

### <a name="what-configurations-are-not-supported-by-azure-vm-backup-"></a>Quali configurazioni non sono supportate da servizio Backup delle macchine virtuali di Azure?
Vedere i [sistemi operativi supportati](backup-azure-arm-vms-prepare.md#supported-operating-system-for-backup) e le [limitazioni del backup di macchine virtuali](backup-azure-arm-vms-prepare.md#limitations-when-backing-up-and-restoring-a-vm).

### <a name="why-cant-i-see-my-vm-in-configure-backup-wizard"></a>Perché non è possibile visualizzare la macchina virtuale nella configurazione guidata del backup?
Nella configurazione guidata del backup, Backup di Azure elenca soltanto le macchine virtuali che:
* Non sono già protette. Per verificare lo stato del backup di una macchina virtuale è possibile passare al pannello della macchina virtuale e controllare lo stato di backup dal menu Impostazioni del pannello. Altre informazioni su come [controllare lo stato del backup di una macchina virtuale](backup-azure-vms-first-look-arm.md#configure-the-backup-job-from-the-vm-management-blade).
* Appartiene alla stessa area della macchina virtuale.

## <a name="backup"></a>Backup
### <a name="will-on-demand-backup-job-follow-same-retention-schedule-as-scheduled-backups"></a>Il processo di backup su richiesta seguirà la stessa pianificazione di conservazione dei backup pianificati?
No. È necessario specificare l'intervallo di conservazione per un processo di backup su richiesta. Per impostazione predefinita, i backup attivati dal portale vengono conservati per 30 giorni. 

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>Di recente è stata abilitata la Crittografia dischi di Azure in alcune macchine virtuali. I backup continueranno a funzionare?
È necessario concedere le autorizzazioni per permettere al servizio Backup di Azure di accedere a Key Vault. Per concedere tali autorizzazioni in PowerShell, è possibile seguire la procedura descritta nella sezione relativa all'*abilitazione del backup* nella documentazione di [PowerShell](backup-azure-vms-automation.md).

### <a name="i-migrated-disks-of-a-vm-to-managed-disks-will-my-backups-continue-to-work"></a>È stata eseguita la migrazione dei dischi di una macchina virtuale a Managed Disks. I backup continueranno a funzionare?
Sì, i backup funzionano correttamente e non è necessario configurare nuovamente il backup. 

## <a name="restore"></a>Ripristino
### <a name="how-do-i-decide-between-restoring-disks-versus-full-vm-restore"></a>Come scegliere tra il ripristino dei dischi e il ripristino completo della macchina virtuale?
Il ripristino completo di una macchina virtuale di Azure è un modo per avere un'opzione di creazione rapida per la macchina virtuale ripristinata. L'opzione di ripristino della macchina virtuale modifica i nomi dei dischi e dei contenitori usati dai dischi, gli indirizzi IP pubblici e i nomi di interfaccia di rete per l'univocità delle risorse create come parte della creazione della macchina virtuale. Non aggiunge la macchina virtuale al set di disponibilità. 

Usare i dischi di ripristino per eseguire queste operazioni:
* Personalizzare la macchina virtuale che viene creata da una configurazione temporizzata, ad esempio modificando le dimensioni dalla configurazione di backup.
* Aggiungere configurazioni non disponibili al momento del backup. 
* Controllare la convenzione di denominazione per le risorse create.
* Aggiungere la macchina virtuale al set di disponibilità.
* Avere una configurazione che è possibile ottenere soltanto usando PowerShell o la definizione di un modello dichiarativo.

## <a name="manage-vm-backups"></a>Gestire i backup delle macchine virtuali
### <a name="what-happens-when-i-change-a-backup-policy-on-vms"></a>Cosa accade quando si modificano criteri di backup in una o più macchine virtuali?
Quando vengono applicati nuovi criteri a una o più macchine virtuali, viene seguita la pianificazione e la conservazione stabilita dai nuovi criteri. Se il periodo di conservazione viene esteso, i punti di ripristino esistenti vengono contrassegnati in modo che vengano mantenuti in base ai nuovi criteri. Se il periodo di conservazione viene ridotto, vengono contrassegnati per l'eliminazione nel processo di pulizia successivo e verranno eliminati. 
