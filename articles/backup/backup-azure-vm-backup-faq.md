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
ms.openlocfilehash: f69cbbab19acbc4e71445012d262896275a7d768
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2018
---
# <a name="questions-about-the-azure-vm-backup-service"></a>Domande sul servizio Backup delle macchine virtuali di Azure
Questo articolo contiene risposte a domande comuni che consentiranno di comprendere rapidamente i componenti del servizio Backup delle macchine virtuali di Azure. Alcune risposte includono collegamenti ad articoli con informazioni complete. È anche possibile inserire le domande sul servizio Backup di Azure nel [forum di discussione](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

## <a name="configure-backup"></a>Configurare il backup
### <a name="do-recovery-services-vaults-support-classic-vms-or-resource-manager-based-vms-br"></a>Gli insiemi di credenziali di Servizi di ripristino supportano le macchine virtuali in modalità classica o quelle basate su Resource Manager? <br/>
Gli insiemi di credenziali di Servizi di ripristino supportano entrambi modelli.  È possibile eseguire il backup di una macchina virtuale creata nel portale classico o di una macchina virtuale di Resource Manager creata nel portale di Azure nell'insieme di credenziali di Servizi di ripristino.

### <a name="what-configurations-are-not-supported-by-azure-vm-backup"></a>Quali configurazioni non sono supportate dal servizio di backup delle macchine virtuali di Azure?
Vedere i [sistemi operativi supportati](backup-azure-arm-vms-prepare.md#supported-operating-systems-for-backup) e le [limitazioni del backup di macchine virtuali](backup-azure-arm-vms-prepare.md#limitations-when-backing-up-and-restoring-a-vm).

### <a name="why-cant-i-see-my-vm-in-configure-backup-wizard"></a>Perché non è possibile visualizzare la macchina virtuale nella configurazione guidata del backup?
Nella configurazione guidata del backup, Backup di Azure elenca soltanto le macchine virtuali che:
  * Non sono già protette. Per verificare lo stato del backup di una macchina virtuale, è possibile accedere al pannello della macchina virtuale e controllare lo stato di backup dal menu Impostazioni. Altre informazioni su come [controllare lo stato del backup di una macchina virtuale](backup-azure-vms-first-look-arm.md#configure-the-backup-job-from-the-vm-management-blade).
  * Appartiene alla stessa area della macchina virtuale.

## <a name="backup"></a>Backup
### <a name="will-on-demand-backup-job-follow-same-retention-schedule-as-scheduled-backups"></a>Il processo di backup su richiesta seguirà la stessa pianificazione di conservazione dei backup pianificati?
di serie È necessario specificare l'intervallo di conservazione per un processo di backup su richiesta. Per impostazione predefinita, i backup attivati dal portale vengono conservati per 30 giorni. 

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>Di recente è stata abilitata la Crittografia dischi di Azure in alcune macchine virtuali. I backup continueranno a funzionare?
È necessario concedere le autorizzazioni per permettere al servizio Backup di Azure di accedere a Key Vault. Per concedere tali autorizzazioni in PowerShell, è possibile seguire la procedura descritta nella sezione relativa all'*abilitazione del backup* nella documentazione di [PowerShell](backup-azure-vms-automation.md).

### <a name="i-migrated-disks-of-a-vm-to-managed-disks-will-my-backups-continue-to-work"></a>È stata eseguita la migrazione dei dischi di una macchina virtuale a Managed Disks. I backup continueranno a funzionare?
Sì, i backup vengono eseguiti senza problemi e non è necessario configurare nuovamente il backup. 

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>La macchina virtuale è stata arrestata. Il backup pianificato o su richiesta viene eseguito ugualmente?
Sì. Anche quando un computer è stato arrestato i backup vengono eseguiti e il punto di ripristino viene contrassegnato come coerenza per arresto anomalo del sistema. Per altri dettagli, vedere la sezione relativa alla coerenza dei dati in [questo articolo](backup-azure-vms-introduction.md#how-does-azure-back-up-virtual-machines).

### <a name="can-i-cancel-an-in-progress-backup-job"></a>È possibile annullare un processo di backup in corso?
Sì. È possibile annullare il processo di backup se è in fase di "Creazione dello snapshot". **Se è in corso il trasferimento di dati dallo snapshot non è possibile annullare un processo**. 

### <a name="i-enabled-resource-group-lock-on-my-backed-up-managed-disk-vms-will-my-backups-continue-to-work"></a>È stato abilitato il blocco del gruppo di risorse sulle VM con Managed Disks di cui è stato eseguito il backup. I backup continueranno a funzionare?
Se l'utente blocca il gruppo di risorse, il servizio di backup non è in grado di eliminare i punti di ripristino precedenti. Per questo motivo i nuovi backup non vengono eseguiti, in quanto esiste un limite massimo 18 punti di ripristino imposto dal back-end. Se i backup hanno esito negativo con un errore interno dopo il blocco del gruppo di risorse, seguire questa [procedura per rimuovere la raccolta di punti di ripristino](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#backup-service-does-not-have-permission-to-delete-the-old-restore-points-due-to-resource-group-lock).

## <a name="restore"></a>Restore
### <a name="how-do-i-decide-between-restoring-disks-versus-full-vm-restore"></a>Come scegliere tra il ripristino dei dischi e il ripristino completo della macchina virtuale?
Il ripristino completo di una macchina virtuale di Azure è fondamentalmente un'opzione di creazione rapida. Il ripristino della macchina virtuale modifica i nomi di dischi, contenitori usati dai dischi, indirizzi IP pubblici e i nomi delle interfacce di rete. Tale modifica è necessaria per mantenere l'univocità delle risorse create durante la creazione della macchina virtuale. La macchina virtuale non viene tuttavia aggiunta al set di disponibilità. 

Usare i dischi di ripristino per eseguire queste operazioni:
  * Personalizzare la macchina virtuale che viene creata da una configurazione temporizzata, ad esempio modificando le dimensioni
  * Aggiungere configurazioni non disponibili al momento del backup 
  * Controllare la convenzione di denominazione per le risorse create.
  * Aggiungere la macchina virtuale al set di disponibilità.
  * Per qualsiasi altra configurazione che è possibile ottenere soltanto usando PowerShell o la definizione di un modello dichiarativo
  
### <a name="can-i-use-backups-of-unmanaged-disk-vm-to-restore-after-i-upgrade-my-disks-to-managed-disks"></a>È possibile usare i backup della VM con dischi non gestiti per eseguire il ripristino dopo l'aggiornamento dei dischi personali a dischi gestiti?
Sì, è possibile usare i backup eseguiti prima di eseguire la migrazione dei dischi da non gestiti a gestiti. Per impostazione predefinita, il processo di ripristino della VM creerà una VM con dischi non gestiti. Per ripristinare i dischi e usarli per creare una VM su dischi gestiti, è possibile usare la funzionalità di ripristino dei dischi. 

## <a name="manage-vm-backups"></a>Gestire i backup delle macchine virtuali
### <a name="what-happens-when-i-change-a-backup-policy-on-vms"></a>Cosa accade quando si modificano criteri di backup in una o più macchine virtuali?
Quando vengono applicati nuovi criteri a una o più macchine virtuali, vengono seguite la pianificazione e la conservazione stabilite dai nuovi criteri. Se il periodo di conservazione viene esteso, i punti di ripristino esistenti vengono contrassegnati in modo che vengano mantenuti in base ai nuovi criteri. Se il periodo di conservazione viene ridotto, vengono contrassegnati per l'eliminazione ed eliminati nel successivo processo di pulizia. 

### <a name="how-can-i-move-a-vm-enrolled-in-azure-backup-between-resource-groups"></a>Come si può spostare una VM registrata in Backup di Azure tra gruppi di risorse?
Seguire questa procedura per spostare le VM sottoposte a backup nel gruppo di risorse di destinazione 
1. Interrompere temporaneamente il backup e conservare i dati di backup
2. Spostare la VM nel gruppo di risorse di destinazione
3. Riproteggerla con lo stesso insieme di credenziali o con un nuovo insieme di credenziali

Gli utenti possono eseguire il ripristino dai punti di ripristino disponibili creati prima dell'operazione di spostamento.


