---
title: 'Domande frequenti: backup di macchine virtuali di Azure'
description: In questo articolo vengono fornite le risposte alle domande comuni sul backup di macchine virtuali di Azure con il servizio backup di Azure.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 30036d6cf241e1ac840b2be67ca78fbda6c60061
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172570"
---
# <a name="frequently-asked-questions-back-up-azure-vms"></a>Domande frequenti-eseguire il backup di macchine virtuali di Azure

Questo articolo risponde a domande comuni sul backup di macchine virtuali di Azure con il servizio [backup di Azure](backup-introduction-to-azure-backup.md) .

## <a name="backup"></a>Backup

### <a name="which-vm-images-can-be-enabled-for-backup-when-i-create-them"></a>Quali immagini di macchina virtuale possono essere abilitate per il backup durante la creazione?

Quando si crea una macchina virtuale, è possibile abilitare il backup per le macchine virtuali che eseguono [sistemi operativi supportati](backup-support-matrix-iaas.md#supported-backup-actions)

### <a name="is-the-backup-cost-included-in-the-vm-cost"></a>Il costo del backup è incluso nel costo della macchina virtuale?

No. I costi di backup sono separati dai costi della macchina virtuale. Scopri di più sui [prezzi di backup di Azure](https://azure.microsoft.com/pricing/details/backup/).

### <a name="which-permissions-are-required-to-enable-backup-for-a-vm"></a>Quali autorizzazioni sono necessarie per abilitare il backup per una macchina virtuale?

Se si è un collaboratore della macchina virtuale, è possibile abilitare il backup nella macchina virtuale. Se si usa un ruolo personalizzato, sono necessarie le autorizzazioni seguenti per abilitare il backup nella macchina virtuale:

- Microsoft.RecoveryServices/Vaults/write
- Microsoft.RecoveryServices/Vaults/read
- Microsoft.RecoveryServices/locations/*
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write
- Microsoft.RecoveryServices/Vaults/backupPolicies/read
- Microsoft.RecoveryServices/Vaults/backupPolicies/write

Se l'insieme di credenziali di servizi di ripristino e la VM hanno gruppi di risorse diversi, verificare di avere le autorizzazioni di scrittura nel gruppo di risorse per l'insieme di credenziali di servizi di ripristino.  

### <a name="does-an-on-demand-backup-job-use-the-same-retention-schedule-as-scheduled-backups"></a>Un processo di backup su richiesta si basa sulla stessa pianificazione di conservazione dei backup pianificati?

No. Specificare il periodo di mantenimento dati per un processo di backup su richiesta. Per impostazione predefinita, un backup attivato dal portale viene conservato per 30 giorni.

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>Di recente è stata abilitata la Crittografia dischi di Azure in alcune macchine virtuali. I backup continueranno a funzionare?

Fornire le autorizzazioni per il backup di Azure per accedere a Key Vault. Specificare le autorizzazioni in PowerShell come descritto nella sezione **Attivare un backup** nella documentazione relativa a [PowerShell di Backup di Azure](backup-azure-vms-automation.md).

### <a name="i-migrated-vm-disks-to-managed-disks-will-my-backups-continue-to-work"></a>È stata eseguita la migrazione dei dischi della macchina virtuale in dischi gestiti. I backup continueranno a funzionare?

Sì, i backup continueranno a funzionare senza problemi. Non è necessario riconfigurare alcun elemento.

### <a name="why-cant-i-see-my-vm-in-the-configure-backup-wizard"></a>Perché non è visibile la macchina virtuale nella procedura guidata Configura backup?

La procedura guidata visualizza solo le macchine virtuali che si trovano nella stessa area dell'insieme di credenziali e che non sono già state sottoposte a backup.

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>La macchina virtuale è stata arrestata. Il backup pianificato o su richiesta viene eseguito ugualmente?

Sì. I backup vengono eseguiti quando una macchina virtuale è spenta. Il punto di ripristino viene contrassegnato come coerente con l'arresto anomalo del sistema.

### <a name="can-i-cancel-an-in-progress-backup-job"></a>È possibile annullare un processo di backup in corso?

Sì. È possibile annullare un processo di backup nello stato **Creazione dello snapshot**. Non è possibile annullare un processo se è in corso il trasferimento di dati dallo snapshot.

### <a name="i-enabled-lock-on-resource-group-created-by-azure-backup-service-ie-azurebackuprg_geo_number-will-my-backups-continue-to-work"></a>È stato abilitato il blocco sul gruppo di risorse creato dal servizio backup di Azure (ad esempio `AzureBackupRG_<geo>_<number>`), i backup continueranno a funzionare?

Se si blocca il gruppo di risorse creato dal servizio backup di Azure, i backup inizieranno ad avere esito negativo perché è previsto un limite massimo di 18 punti di ripristino.

Per fare in modo che i backup futuri abbiano esito positivo, è necessario rimuovere il blocco e deselezionare la raccolta di punti di ripristino da tale gruppo di risorse, [attenersi alla procedura seguente](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-azure-portal) per rimuovere la raccolta di punti di ripristino.

### <a name="does-azure-backup-support-standard-ssd-managed-disks"></a>Backup di Azure supporta dischi standard gestiti da unità SSD?

Sì, backup di Azure supporta [dischi gestiti SSD standard](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/).

### <a name="can-we-back-up-a-vm-with-a-write-accelerator-wa-enabled-disk"></a>È possibile eseguire il backup di una macchina virtuale con un disco con acceleratore di scrittura?

Non è possibile creare snapshot nel disco con acceleratore di scrittura. Il servizio Backup di Azure, tuttavia, può escludere questo disco dal backup.

### <a name="i-have-a-vm-with-write-accelerator-wa-disks-and-sap-hana-installed-how-do-i-back-up"></a>In una macchina virtuale sono presenti dischi con acceleratore di scrittura ed è installato SAP HANA. In che modo è possibile eseguire il backup?

Backup di Azure non può eseguire il backup del disco con acceleratore di scrittura, ma può escluderlo dal processo. Il backup, tuttavia, non assicurerà la coerenza del database perché non viene eseguito il backup delle informazioni presenti sul disco con acceleratore di scrittura. È possibile eseguire il backup di dischi con questa configurazione per ottenere il backup del disco del sistema operativo e il backup di dischi senza acceleratore di scrittura.

È in esecuzione un'anteprima privata per un backup di SAP HANA con un RPO di 15 minuti. È stata creata in modo simile al backup del database SQL e usa l'interfaccia backInt per soluzioni di terze parti certificate da SAP HANA. Se si è interessati, inviare un messaggio di posta elettronica all'`AskAzureBackupTeam@microsoft.com` con l' **iscrizione all'anteprima privata per il backup di SAP Hana in macchine virtuali di Azure**.

### <a name="what-is-the-maximum-delay-i-can-expect-in-backup-start-time-from-the-scheduled-backup-time-i-have-set-in-my-vm-backup-policy"></a>Qual è il ritardo massimo che è possibile aspettarsi nell'ora di inizio del backup dall'ora di backup pianificata configurata nel criterio di backup della macchina virtuale?

Il backup pianificato verrà attivato entro 2 ore dall'ora del backup pianificato. Se ad esempio le VM 100 hanno un'ora di inizio di backup pianificata alle 2:00 AM, il processo di backup sarà in corso per il numero massimo di 4:00 macchine virtuali 100. Se i backup pianificati sono stati sospesi a causa di un'interruzione e ripresa/nuovo tentativo, il backup può essere avviato al di fuori di questa finestra di due ore pianificata.

### <a name="what-is-the-minimum-allowed-retention-range-for-daily-backup-point"></a>Qual è il periodo di mantenimento dati minimo consentito per il punto di backup giornaliero?

Il criterio di backup della macchina virtuale di Azure supporta un periodo di conservazione minimo di sette giorni fino a 9999 giorni. Qualsiasi modifica a un criterio di backup di VM esistente con meno di sette giorni richiederà un aggiornamento per soddisfare il periodo di mantenimento dati minimo di sette giorni.

## <a name="restore"></a>Ripristino

### <a name="how-do-i-decide-whether-to-restore-disks-only-or-a-full-vm"></a>In che modo si decide se è opportuno ripristinare solo i dischi oppure una macchina virtuale completa?

Il ripristino di una macchina virtuale può essere considerato come un'opzione di creazione rapida di una macchina virtuale di Azure. Questa opzione modifica i nomi dei dischi, i contenitori usati dai dischi, gli indirizzi IP pubblici e i nomi delle interfacce di rete. Con la modifica le risorse vengono mantenute univoche quando si crea una macchina virtuale. La macchina virtuale non viene aggiunta a un set di disponibilità.

L'opzione di ripristino di un disco consente di:

- Personalizzare la macchina virtuale che viene creata, Modificare ad esempio le dimensioni.
- Aggiungere le impostazioni di configurazione che non erano disponibili al momento del backup.
- Controllare la convenzione di denominazione delle risorse create.
- Aggiungere la macchina virtuale a un set di disponibilità.
- Aggiungere qualsiasi altra impostazione da configurare usando PowerShell o un modello.

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

La funzionalità di [ripristino istantaneo](backup-instant-restore-capability.md) consente di eseguire backup più veloci e ripristini istantanei dagli snapshot.

### <a name="what-happens-when-we-change-the-key-vault-settings-for-the-encrypted-vm"></a>Cosa accade quando si modificano le impostazioni dell'insieme di credenziali delle chiavi per la VM crittografata?

Dopo aver modificato le impostazioni dell'insieme di credenziali delle credenziali per la macchina virtuale crittografata, i backup continueranno a funzionare con il nuovo set di dettagli. Tuttavia, dopo il ripristino da un punto di ripristino prima della modifica, sarà necessario ripristinare i segreti in un insieme di credenziali delle chiavi prima di poter creare la macchina virtuale. Per altre informazioni, vedere questo [articolo](https://docs.microsoft.com/azure/backup/backup-azure-restore-key-secret)

Operazioni come il rollup della chiave o del segreto non richiedono questo passaggio e lo stesso insieme di credenziali delle chiavi può essere usato dopo il ripristino.

### <a name="can-i-access-the-vm-once-restored-due-to-a-vm-having-broken-relationship-with-domain-controller"></a>È possibile accedere alla VM una volta ripristinato a causa di una relazione tra una macchina virtuale e il controller di dominio?

Sì, si accede alla macchina virtuale dopo il ripristino a causa di una relazione di una macchina virtuale con il controller di dominio. Per altre informazioni, vedere questo [articolo](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#post-restore-steps)

## <a name="manage-vm-backups"></a>Gestire i backup delle macchine virtuali

### <a name="what-happens-if-i-modify-a-backup-policy"></a>Cosa accade se si modifica un criterio di backup?

Il backup della macchina virtuale viene eseguito usando le impostazioni di conservazione e pianificazione del criterio nuovo o modificato.

- Se il periodo di conservazione viene esteso, i punti di ripristino esistenti vengono contrassegnati per essere mantenuti conformi al nuovo criterio.
- Se il periodo di conservazione viene ridotto, i punti di ripristino vengono contrassegnati per l'eliminazione ed eliminati nel successivo processo di pulizia.

### <a name="how-do-i-move-a-vm-backed-up-by-azure-backup-to-a-different-resource-group"></a>In che modo è possibile spostare una macchina virtuale sottoposta a backup tramite Backup di Azure in un gruppo di risorse diverso?

1. Interrompere temporaneamente il backup e conservare i dati di backup.
2. Spostare la macchina virtuale nel gruppo di risorse di destinazione.
3. Riabilitazione del backup nello stesso o in un nuovo insieme di credenziali.

È possibile ripristinare la macchina virtuale dai punti di ripristino disponibili creati prima dell'operazione di spostamento.

### <a name="is-there-a-limit-on-number-of-vms-that-can-beassociated-with-a-same-backup-policy"></a>Esiste un limite al numero di macchine virtuali che possono essere associate a uno stesso criterio di backup?

Sì, è previsto un limite di 100 VM che possono essere associate agli stessi criteri di backup dal portale. Per più di 100 macchine virtuali, è consigliabile creare più criteri di backup con la stessa pianificazione o una pianificazione diversa.
