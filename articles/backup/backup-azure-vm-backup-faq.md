---
title: Domande frequenti - Backup delle macchine virtuali di AzureFAQ - Backing up Azure VMs
description: In questo articolo vengono fornite le risposte alle domande comuni sul backup delle macchine virtuali di Azure con il servizio Backup di Azure.In this article, discover answers to common questions about backing up Azure VMs with the Azure Backup service.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: accfc57055f70254814c889de875f5360878bcd9
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81757463"
---
# <a name="frequently-asked-questions-back-up-azure-vms"></a>Domande frequenti: eseguire il backup delle macchine virtuali di AzureFrequently asked questions-Back up Azure VMs

Questo articolo risponde alle domande comuni sul backup delle macchine virtuali di Azure con il servizio Backup di [Azure.This article](backup-introduction-to-azure-backup.md) answers common questions about backing up Azure VMs with the Azure Backup service.

## <a name="backup"></a>Backup

### <a name="which-vm-images-can-be-enabled-for-backup-when-i-create-them"></a>Quali immagini VM possono essere abilitate per il backup quando vengono create?

Quando si crea una macchina virtuale, è possibile abilitare il backup per le macchine virtuali che eseguono [sistemi operativi supportati.](backup-support-matrix-iaas.md#supported-backup-actions)

### <a name="is-the-backup-cost-included-in-the-vm-cost"></a>Il costo di backup è incluso nel costo della macchina virtuale?

No. I costi di backup sono separati dai costi di una macchina virtuale. Altre informazioni sui [prezzi di Backup di Azure](https://azure.microsoft.com/pricing/details/backup/).Learn more about Azure Backup pricing .

### <a name="which-permissions-are-required-to-enable-backup-for-a-vm"></a>Quali autorizzazioni sono necessarie per abilitare il backup per una macchina virtuale?

Se si è un collaboratore di VM, è possibile abilitare il backup nella macchina virtuale. Se si usa un ruolo personalizzato, sono necessarie le autorizzazioni seguenti per abilitare il backup nella macchina virtuale:If you're using a custom role, you need the following permissions to enable backup on the VM:

- Microsoft.RecoveryServices/Vaults/write
- Microsoft.RecoveryServices/Vaults/read
- Microsoft.RecoveryServices/locations/*
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write
- Microsoft.RecoveryServices/Vaults/backupPolicies/read
- Microsoft.RecoveryServices/Vaults/backupPolicies/write

Se l'insieme di credenziali e la macchina virtuale di Servizi di ripristino dispongono di gruppi di risorse diversi, assicurarsi di disporre delle autorizzazioni di scrittura nel gruppo di risorse per l'insieme di credenziali di Servizi di ripristino.  

### <a name="does-an-on-demand-backup-job-use-the-same-retention-schedule-as-scheduled-backups"></a>Un processo di backup su richiesta si basa sulla stessa pianificazione di conservazione dei backup pianificati?

No. Specificare l'intervallo di conservazione per un processo di backup su richiesta. Per impostazione predefinita, un backup attivato dal portale viene conservato per 30 giorni.

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>Di recente è stata abilitata la Crittografia dischi di Azure in alcune macchine virtuali. I backup continueranno a funzionare?

Fornire le autorizzazioni per Backup di Azure per accedere all'insieme di credenziali delle chiavi. Specificare le autorizzazioni in PowerShell come descritto nella sezione **Attivare un backup** nella documentazione relativa a [PowerShell di Backup di Azure](backup-azure-vms-automation.md).

### <a name="i-migrated-vm-disks-to-managed-disks-will-my-backups-continue-to-work"></a>È stata eseguita la migrazione dei dischi della macchina virtuale in dischi gestiti. I backup continueranno a funzionare?

Sì, i backup continueranno a funzionare senza problemi. Non è necessario riconfigurare alcun elemento.

### <a name="why-cant-i-see-my-vm-in-the-configure-backup-wizard"></a>Perché non è visibile la macchina virtuale nella procedura guidata Configura backup?

La procedura guidata visualizza solo le macchine virtuali che si trovano nella stessa area dell'insieme di credenziali e che non sono già state sottoposte a backup.

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>La macchina virtuale è stata arrestata. Il backup pianificato o su richiesta viene eseguito ugualmente?

Sì. I backup vengono eseguiti quando una macchina virtuale è spenta. Il punto di ripristino viene contrassegnato come coerente con l'arresto anomalo del sistema.

### <a name="can-i-cancel-an-in-progress-backup-job"></a>È possibile annullare un processo di backup in corso?

Sì. È possibile annullare il processo di backup in stato **Acquisizione snapshot.** Non è possibile annullare un processo se è in corso il trasferimento di dati dallo snapshot.

### <a name="i-enabled-a-lock-on-the-resource-group-created-by-azure-backup-service-for-example-azurebackuprg_geo_number-will-my-backups-continue-to-work"></a>Ho abilitato un blocco sul gruppo di risorse creato `AzureBackupRG_<geo>_<number>`dal servizio Backup di Azure, ad esempio ). I backup continueranno a funzionare?

Se si blocca il gruppo di risorse creato dal servizio Backup di Azure, i backup inizieranno a non riuscire in quanto è previsto un limite massimo di 18 punti di ripristino.

Rimuovere il blocco e cancellare la raccolta di punti di ripristino da tale gruppo di risorse per eseguire correttamente i backup futuri. [Seguire questi passaggi](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-azure-portal) per rimuovere la raccolta di punti di ripristino.

### <a name="does-azure-backup-support-standard-ssd-managed-disks"></a>Il backup di Azure supporta dischi gestiti da SSD standard?

Sì, Backup di Azure supporta i [dischi gestiti SSD standard.](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/)

### <a name="can-we-back-up-a-vm-with-a-write-accelerator-wa-enabled-disk"></a>È possibile eseguire il backup di una macchina virtuale con un disco con acceleratore di scrittura?

Non è possibile creare snapshot nel disco con acceleratore di scrittura. Il servizio Backup di Azure, tuttavia, può escludere questo disco dal backup.

### <a name="i-have-a-vm-with-write-accelerator-wa-disks-and-sap-hana-installed-how-do-i-back-up"></a>In una macchina virtuale sono presenti dischi con acceleratore di scrittura ed è installato SAP HANA. In che modo è possibile eseguire il backup?

Backup di Azure non può eseguire il backup del disco con acceleratore di scrittura, ma può escluderlo dal processo. Il backup, tuttavia, non assicurerà la coerenza del database perché non viene eseguito il backup delle informazioni presenti sul disco con acceleratore di scrittura. È possibile eseguire il backup di dischi con questa configurazione per ottenere il backup del disco del sistema operativo e il backup di dischi senza acceleratore di scrittura.

Backup di Azure offre una soluzione di backup in streaming per i database SAP HANA con un RPO di 15 minuti. È certificato Backint da SAP per fornire un supporto di backup nativo sfruttando le API native di SAP HANA. Altre informazioni [sul backup dei database SAP HANA nelle macchine virtuali](https://docs.microsoft.com/azure/backup/sap-hana-db-about)di Azure.

### <a name="what-is-the-maximum-delay-i-can-expect-in-backup-start-time-from-the-scheduled-backup-time-i-have-set-in-my-vm-backup-policy"></a>Qual è il ritardo massimo previsto nell'ora di inizio del backup dal tempo di backup pianificato impostato nei criteri di backup della macchina virtuale?

Il backup pianificato verrà attivato entro 2 ore dall'ora di backup pianificata. Ad esempio, se l'ora di inizio del backup di 100 macchine virtuali è pianificata alle 2:00 AM, entro le 4:00 AM al più tardi tutte le 100 macchine virtuali avranno il processo di backup in corso. Se i backup pianificati sono stati sospesi a causa di un'interruzione e ripreso o ritentato, il backup può iniziare al di fuori di questo intervallo pianificato di due ore.

### <a name="what-is-the-minimum-allowed-retention-range-for-a-daily-backup-point"></a>Qual è l'intervallo di conservazione minimo consentito per un punto di backup giornaliero?

I criteri di backup della macchina virtuale di Azure supportano un intervallo di conservazione minimo da sette giorni fino a 9999 giorni. Qualsiasi modifica a un criterio di backup della macchina virtuale esistente con meno di sette giorni richiederà un aggiornamento per soddisfare l'intervallo di conservazione minimo di sette giorni.

### <a name="what-happens-if-i-change-the-case-of-the-name-of-my-vm-or-my-vm-resource-group"></a>Cosa succede se si modifica la combinazione di maiuscole e minuscole del nome della macchina virtuale o del gruppo di risorse della macchina virtuale?

Se si modifica il caso (in alto o inferiore) del gruppo di risorse della macchina virtuale o della macchina virtuale, il caso del nome dell'elemento di backup non cambierà. Tuttavia, questo è previsto il comportamento di Backup di Azure.However, this is expected Azure Backup behavior. La modifica del caso non verrà visualizzata nell'elemento di backup, ma verrà aggiornata nel back-end.

### <a name="can-i-back-up-or-restore-selective-disks-attached-to-a-vm"></a>È possibile eseguire il backup o il ripristino di dischi selettivi collegati a una macchina virtuale?

Backup di Azure supporta ora il backup e il ripristino selettivi del disco tramite la soluzione di backup della macchina virtuale di Azure.Azure Backup now supports selective disk backup and restore using the Azure Virtual Machine backup solution.

Oggi, Backup di Azure supporta il backup di tutti i dischi (sistema operativo e dati) in una macchina virtuale insieme usando la soluzione di backup della macchina virtuale. Con la funzionalità di dischi esclusi, è possibile eseguire il backup di uno o pochi dei numerosi dischi dati in una macchina virtuale. Ciò offre una soluzione efficiente ed economica per le vostre esigenze di backup e ripristino. Ogni punto di ripristino contiene i dati dei dischi inclusi nell'operazione di backup, che consente di ripristinare un sottoinsieme di dischi dal punto di ripristino specificato durante l'operazione di ripristino. Questo vale per il ripristino sia dallo snapshot che dal vault.

Per iscriverti all'anteprima, scrivici all'utenteAskAzureBackupTeam@microsoft.com

## <a name="restore"></a>Restore

### <a name="how-do-i-decide-whether-to-restore-disks-only-or-a-full-vm"></a>In che modo si decide se è opportuno ripristinare solo i dischi oppure una macchina virtuale completa?

Il ripristino di una macchina virtuale può essere considerato come un'opzione di creazione rapida di una macchina virtuale di Azure. Questa opzione consente di modificare i nomi dei dischi, i contenitori utilizzati dai dischi, gli indirizzi IP pubblici e i nomi delle interfacce di rete. Con la modifica le risorse vengono mantenute univoche quando si crea una macchina virtuale. La macchina virtuale non viene aggiunta a un set di disponibilità.

L'opzione di ripristino di un disco consente di:

- Personalizzare la macchina virtuale che viene creata, Ad esempio, modificare le dimensioni.
- Aggiungere le impostazioni di configurazione che non erano presenti al momento del backup.
- Controllare la convenzione di denominazione delle risorse create.
- Aggiungere la macchina virtuale a un set di disponibilità.
- Aggiungere qualsiasi altra impostazione da configurare usando PowerShell o un modello.

### <a name="can-i-restore-backups-of-unmanaged-vm-disks-after-i-upgrade-to-managed-disks"></a>È possibile ripristinare i backup dei dischi non gestiti della macchina virtuale dopo l'aggiornamento a dischi gestiti?

Sì, è possibile usare i backup eseguiti prima della migrazione dei dischi da non gestiti a gestiti.

### <a name="how-do-i-restore-a-vm-to-a-restore-point-before-the-vm-was-migrated-to-managed-disks"></a>In che modo si ripristina una macchina virtuale in un punto di ripristino prima della migrazione di tale macchina a dischi gestiti?

Il processo di ripristino rimane invariato. Se il punto di ripristino è di un punto nel tempo in cui la macchina virtuale conservere dischi non gestiti, è possibile [ripristinare i dischi come non gestiti.](tutorial-restore-disk.md#unmanaged-disks-restore) Se la macchina virtuale conserver dischi gestiti era in grado di [ripristinare i dischi come dischi gestiti.](tutorial-restore-disk.md#managed-disk-restore) È quindi possibile [creare una macchina virtuale da tali dischi.](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk)

[Altre informazioni](backup-azure-vms-automation.md#restore-an-azure-vm) su come eseguire questa procedura in PowerShell.

### <a name="can-i-restore-the-vm-thats-been-deleted"></a>È possibile ripristinare una macchina virtuale che è stata eliminata?

Sì. Anche se si elimina la macchina virtuale, è possibile passare all'elemento di backup corrispondente nell'insieme di credenziali e ripristinare da un punto di ripristino.

### <a name="how-do-i-restore-a-vm-to-the-same-availability-sets"></a>Come si ripristina una macchina virtuale negli stessi set di disponibilità?

Per le macchine virtuali di Azure su disco gestito, il ripristino nei set di disponibilità viene abilitato fornendo un'opzione nel modello durante il ripristino come dischi gestiti. Il parametro di input di questo modello è denominato **Set di disponibilità**.

### <a name="how-do-we-get-faster-restore-performances"></a>In che modo si ottengono migliori prestazioni di ripristino?

[La](backup-instant-restore-capability.md) funzionalità di ripristino immediato consente di eseguire backup e ripristini istantanei dalle istantanee.

### <a name="what-happens-when-we-change-the-key-vault-settings-for-the-encrypted-vm"></a>Cosa succede quando si modificano le impostazioni dell'insieme di credenziali delle chiavi per la macchina virtuale crittografata?

Dopo aver modificato le impostazioni dell'insieme di credenziali delle chiavi per la macchina virtuale crittografata, i backup continueranno a funzionare con il nuovo set di dettagli. Tuttavia, dopo il ripristino da un punto di ripristino prima della modifica, è necessario ripristinare i segreti in un insieme di credenziali delle chiavi prima di poter creare la macchina virtuale da esso. Per ulteriori informazioni, vedere questo [articolo](https://docs.microsoft.com/azure/backup/backup-azure-restore-key-secret).

Operazioni come secret/key roll-over non richiedono questo passaggio e lo stesso KeyVault può essere utilizzato dopo il ripristino.

### <a name="can-i-access-the-vm-once-restored-due-to-a-vm-having-broken-relationship-with-domain-controller"></a>È possibile accedere alla macchina virtuale una volta ripristinata a causa di una macchina virtuale interrotta con il controller di dominio?

Sì, si accede alla macchina virtuale una volta ripristinata a causa di una macchina virtuale che ha interrotto la relazione con il controller di dominio. Per altre informazioni, vedere questo [articoloFor](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#post-restore-steps) more information, see this article

## <a name="manage-vm-backups"></a>Gestire i backup delle macchine virtuali

### <a name="what-happens-if-i-modify-a-backup-policy"></a>Cosa accade se si modifica un criterio di backup?

Il backup della macchina virtuale viene eseguito usando le impostazioni di conservazione e pianificazione del criterio nuovo o modificato.

- Se il periodo di conservazione viene esteso, i punti di ripristino esistenti vengono contrassegnati per essere mantenuti conformi al nuovo criterio.
- Se il periodo di conservazione viene ridotto, i punti di ripristino vengono contrassegnati per l'eliminazione ed eliminati nel successivo processo di pulizia.

### <a name="how-do-i-move-a-vm-backed-up-by-azure-backup-to-a-different-resource-group"></a>In che modo è possibile spostare una macchina virtuale sottoposta a backup tramite Backup di Azure in un gruppo di risorse diverso?

1. Interrompere temporaneamente il backup e conservare i dati di backup.
2. Spostare la macchina virtuale nel gruppo di risorse di destinazione.
3. Riattivare il backup nello stesso vault o nel nuovo insieme di credenziali.

È possibile ripristinare la macchina virtuale dai punti di ripristino disponibili creati prima dell'operazione di spostamento.

### <a name="is-there-a-limit-on-number-of-vms-that-can-beassociated-with-the-same-backup-policy"></a>Esiste un limite al numero di macchine virtuali che possono essere associate agli stessi criteri di backup?

Sì, esiste un limite di 100 macchine virtuali che possono essere associate agli stessi criteri di backup dal portale. È consigliabile che per più di 100 macchine virtuali crei più criteri di backup con la stessa pianificazione o una pianificazione diversa.
