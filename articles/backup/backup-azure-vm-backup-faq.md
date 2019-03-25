---
title: Domande frequenti su backup di macchine virtuali di Azure con Backup di Azure
description: Risposte alle domande frequenti sul backup di macchine virtuali di Azure con Backup di Azure.
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: sogup
ms.openlocfilehash: ef46c37fec3e5438aeb4f9309201d45365a96fdc
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58402066"
---
# <a name="frequently-asked-questions-back-up-azure-vms"></a>Back-domande frequenti su backup di macchine virtuali di Azure

Questo articolo risponde alle domande frequenti su backup di macchine virtuali di Azure con il [Backup di Azure](backup-introduction-to-azure-backup.md) servizio.


## <a name="backup"></a>Backup

### <a name="which-vm-images-can-be-enabled-for-backup-when-i-create-them"></a>Quando creano, quali immagini di macchina virtuale possono essere abilitate per il backup?
Quando si crea una macchina virtuale, è possibile abilitare il backup per macchine virtuali che eseguono [sistemi operativi supportati](backup-support-matrix-iaas.md#supported-backup-actions)
 
### <a name="is-the-backup-cost-included-in-the-vm-cost"></a>È il costo del backup è incluso nel costo della macchina virtuale? 

 No. Costi di backup sono separati dai costi della macchina virtuale. Altre informazioni sulle [prezzi di Backup di Azure](https://azure.microsoft.com/pricing/details/backup/).
 
### <a name="which-permissions-are-required-to-enable-backup-for-a-vm"></a>Quali autorizzazioni sono necessarie per abilitare il backup per una macchina virtuale? 

Se si ha un collaboratore macchina virtuale, è possibile abilitare il backup della macchina virtuale. Se si usa un ruolo personalizzato, sono necessarie le autorizzazioni seguenti per abilitare il backup della macchina virtuale: 

- Microsoft.RecoveryServices/Vaults/write 
- Microsoft.RecoveryServices/Vaults/read 
- Microsoft.RecoveryServices/locations/* 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write 
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write 
- Microsoft.RecoveryServices/Vaults/backupPolicies/read 
- Microsoft.RecoveryServices/Vaults/backupPolicies/write 
 
Se l'insieme di credenziali di servizi di ripristino e della macchina virtuale dispone di diversi gruppi di risorse, verificare di che disporre delle autorizzazioni di scrittura nel gruppo di risorse per l'insieme di credenziali di servizi di ripristino.  


### <a name="what-azure-vms-can-you-back-up-using-azure-backup"></a>Quali macchine virtuali di Azure è possibile sottoporre a backup con Backup di Azure?

Rivedere le [matrice di supporto](backup-support-matrix-iaas.md) per dettagli sul supporto e le limitazioni.

### <a name="does-an-on-demand-backup-job-use-the-same-retention-schedule-as-scheduled-backups"></a>Un processo di backup su richiesta si basa sulla stessa pianificazione di conservazione dei backup pianificati?
 No. Specificare l'intervallo di conservazione per un processo di backup su richiesta. Per impostazione predefinita, un backup attivato dal portale viene conservato per 30 giorni.

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>Di recente è stata abilitata la Crittografia dischi di Azure in alcune macchine virtuali. I backup continueranno a funzionare?
Specificare le autorizzazioni per Backup di Azure per accedere a Key Vault. Specificare le autorizzazioni in PowerShell come descritto nella sezione **Attivare un backup** nella documentazione relativa a [PowerShell di Backup di Azure](backup-azure-vms-automation.md).

### <a name="i-migrated-vm-disks-to-managed-disks-will-my-backups-continue-to-work"></a>È stata eseguita la migrazione dei dischi della macchina virtuale in dischi gestiti. I backup continueranno a funzionare?
Sì, i backup continueranno a funzionare senza problemi. Non è necessario riconfigurare alcun elemento.

### <a name="why-cant-i-see-my-vm-in-the-configure-backup-wizard"></a>Perché non è visibile la macchina virtuale nella procedura guidata Configura backup?
La procedura guidata visualizza solo le macchine virtuali che si trovano nella stessa area dell'insieme di credenziali e che non sono già state sottoposte a backup.

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>La macchina virtuale è stata arrestata. Il backup pianificato o su richiesta viene eseguito ugualmente?
Sì. I backup vengono eseguiti quando una macchina virtuale è spenta. Il punto di ripristino viene contrassegnato come coerente con l'arresto anomalo del sistema.

### <a name="can-i-cancel-an-in-progress-backup-job"></a>È possibile annullare un processo di backup in corso?
Sì. È possibile annullare un processo di backup nello stato **Creazione dello snapshot**. Non è possibile annullare un processo se è in corso il trasferimento di dati dallo snapshot.

### <a name="i-enabled-lock-on-resource-group-created-by-azure-backup-service-ie--azurebackuprggeonumber-will-my-backups-continue-to-work"></a>È stato abilitato blocco sul gruppo di risorse creato dal servizio di Backup di Azure (ad es. ` AzureBackupRG_<geo>_<number>`), i backup continueranno a funzionare?
Se si blocca il gruppo di risorse creato dal servizio Backup di Azure, i backup inizieranno ad avere esito negativo in quanto non esiste un limite massimo di 18 punti di ripristino.

È necessario rimuovere il blocco e cancellare la raccolta di punti di ripristino da tale gruppo di risorse per poter raggiungere il risultato, i backup futuri [seguire questa procedura](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-azure-portal) per rimuovere la raccolta di punti di ripristino.

### <a name="does-the-backup-policy-consider-daylight-saving-time-dst"></a>L'ora legale viene presa in considerazione dai criteri di backup?
No. La data e l'ora del computer locale si adeguano all'ora legale corrente applicata. L'ora impostata per i backup pianificati potrebbe essere diversa dall'ora locale dal momento che è in vigore l'ora legale.

### <a name="how-many-data-disks-can-i-attach-to-a-vm-backed-up-by-azure-backup"></a>Quanti dischi dati è possibile collegare a una macchina virtuale sottoposta a backup tramite Backup di Azure?
Backup di Azure è in grado di eseguire il backup di macchine virtuali con un massimo di 16 dischi. Il supporto per 16 dischi è offerto nel [Ripristino istantaneo](backup-instant-restore-capability.md).

### <a name="does-azure-backup-support-standard-ssd-managed-disk"></a>Backup di Azure supporta Managed Disks SSD Standard?
Backup di Azure supporta [Managed Disks SSD Standard](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/), Unità SSD, managed disks offre un nuovo tipo di risorsa di archiviazione durevole per macchine virtuali di Azure. Il supporto per dischi gestiti SSD è offerto nel [Ripristino istantaneo](backup-instant-restore-capability.md).

### <a name="can-we-back-up-a-vm-with-a-write-accelerator-wa-enabled-disk"></a>È possibile eseguire il backup di una macchina virtuale con un disco con acceleratore di scrittura?
Non è possibile creare snapshot nel disco con acceleratore di scrittura. Il servizio Backup di Azure, tuttavia, può escludere questo disco dal backup. L'esclusione del disco per le macchine virtuali con dischi con acceleratore di scrittura è supportata solo per gli abbonamenti aggiornati a Ripristino istantaneo.

### <a name="i-have-a-vm-with-write-accelerator-wa-disks-and-sap-hana-installed-how-do-i-back-up"></a>In una macchina virtuale sono presenti dischi con acceleratore di scrittura ed è installato SAP HANA. In che modo è possibile eseguire il backup?
Backup di Azure non può eseguire il backup del disco con acceleratore di scrittura, ma può escluderlo dal processo. Il backup, tuttavia, non assicurerà la coerenza del database perché non viene eseguito il backup delle informazioni presenti sul disco con acceleratore di scrittura. È possibile eseguire il backup di dischi con questa configurazione per ottenere il backup del disco del sistema operativo e il backup di dischi senza acceleratore di scrittura.

Anteprima privata per un backup di SAP HANA è in esecuzione con un RPO pari a 15 minuti. È stata creata in modo simile al backup del database SQL e usa l'interfaccia backInt per soluzioni di terze parti certificate da SAP HANA. Se si è interessati, scrivici all'indirizzo ` AskAzureBackupTeam@microsoft.com ` con il soggetto **iscriversi per l'anteprima privata per il backup di SAP HANA in macchine virtuali di Azure**.


## <a name="restore"></a>Restore

### <a name="how-do-i-decide-whether-to-restore-disks-only-or-a-full-vm"></a>In che modo si decide se è opportuno ripristinare solo i dischi oppure una macchina virtuale completa?
Il ripristino di una macchina virtuale può essere considerato come un'opzione di creazione rapida di una macchina virtuale di Azure. Questa opzione consente di modificare i nomi dei dischi, contenitori usati dai dischi, gli indirizzi IP pubblici e i nomi di interfaccia di rete. Con la modifica le risorse vengono mantenute univoche quando si crea una macchina virtuale. La macchina virtuale non viene aggiunta a un set di disponibilità.

L'opzione di ripristino di un disco consente di:
  * Personalizzare la macchina virtuale che viene creata, Ad esempio, modificare le dimensioni.
  * Aggiungere le impostazioni di configurazione che non erano presenti al momento del backup.
  * Controllare la convenzione di denominazione delle risorse create.
  * Aggiungere la macchina virtuale a un set di disponibilità.
  * Aggiungere qualsiasi altra impostazione da configurare usando PowerShell o un modello.

### <a name="can-i-restore-backups-of-unmanaged-vm-disks-after-i-upgrade-to-managed-disks"></a>È possibile ripristinare i backup dei dischi non gestiti della macchina virtuale dopo l'aggiornamento a dischi gestiti?
Sì, è possibile usare i backup eseguiti prima della migrazione dei dischi da non gestiti a gestiti.
- Per impostazione predefinita, il processo di ripristino di una macchina virtuale crea una macchina virtuale non gestita.
- È tuttavia possibile ripristinare i dischi e usarli per creare una macchina virtuale gestita.

### <a name="how-do-i-restore-a-vm-to-a-restore-point-before-the-vm-was-migrated-to-managed-disks"></a>In che modo si ripristina una macchina virtuale in un punto di ripristino prima della migrazione di tale macchina a dischi gestiti?
Per impostazione predefinita, il processo di ripristino di una macchina virtuale crea una macchina virtuale con dischi non gestiti. Per creare una macchina virtuale con dischi gestiti:
1. [Eseguire il ripristino della macchina virtuale con dischi non gestiti](tutorial-restore-disk.md#restore-a-vm-disk).
2. [Convertire i dischi ripristinati in dischi gestiti](tutorial-restore-disk.md#convert-the-restored-disk-to-a-managed-disk).
3. [Creare una macchina virtuale con dischi gestiti](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk).

[Altre informazioni](backup-azure-vms-automation.md#restore-an-azure-vm) su come eseguire questa procedura in PowerShell.

### <a name="can-i-restore-the-vm-thats-been-deleted"></a>È possibile ripristinare una macchina virtuale che è stata eliminata?
Sì. Anche se si elimina la macchina virtuale, è possibile accedere all'elemento di backup corrispondente nell'insieme di credenziali ed eseguire il ripristino da un punto di ripristino.

### <a name="how-to-restore-a-vm-to-the-same-availability-sets"></a>In che modo si ripristina una macchina virtuale negli stessi set di disponibilità?
Per una macchina virtuale di Azure con dischi gestiti, il ripristino nei set di disponibilità è abilitato fornendo un'opzione nel modello durante il ripristino come dischi gestiti. Il parametro di input di questo modello è denominato **Set di disponibilità**.

### <a name="how-do-we-get-faster-restore-performances"></a>In che modo si ottengono migliori prestazioni di ripristino?
Per migliorare le prestazioni di ripristino, passare alla funzionalità di [Ripristino istantaneo](backup-instant-restore-capability.md).

## <a name="manage-vm-backups"></a>Gestire i backup delle macchine virtuali

### <a name="what-happens-if-i-modify-a-backup-policy"></a>Cosa accade se si modifica un criterio di backup?
Il backup della macchina virtuale viene eseguito usando le impostazioni di conservazione e pianificazione del criterio nuovo o modificato.

- Se il periodo di conservazione viene esteso, i punti di ripristino esistenti vengono contrassegnati per essere mantenuti conformi al nuovo criterio.
- Se il periodo di conservazione viene ridotto, i punti di ripristino vengono contrassegnati per l'eliminazione ed eliminati nel successivo processo di pulizia.

### <a name="how-do-i-move-a-vm-backed-up-by-azure-backup-to-a-different-resource-group"></a>In che modo è possibile spostare una macchina virtuale sottoposta a backup tramite Backup di Azure in un gruppo di risorse diverso?

1. Interrompere temporaneamente il backup e conservare i dati di backup.
2. Spostare la macchina virtuale nel gruppo di risorse di destinazione.
3. Abilitata di nuovo il backup nell'insieme di credenziali stesso o nuovo.

È possibile ripristinare la macchina virtuale dai punti di ripristino disponibili creati prima dell'operazione di spostamento.
