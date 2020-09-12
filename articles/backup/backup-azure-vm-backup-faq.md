---
title: 'Domande frequenti: backup di macchine virtuali di Azure'
description: In questo articolo vengono fornite le risposte alle domande comuni sul backup di macchine virtuali di Azure con il servizio backup di Azure.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 7206a62e3148c1bbb8d2e3704d991025deeece37
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89377319"
---
# <a name="frequently-asked-questions-back-up-azure-vms"></a>Domande frequenti-eseguire il backup di macchine virtuali di Azure

Questo articolo risponde a domande comuni sul backup di macchine virtuali di Azure con il servizio [backup di Azure](./backup-overview.md) .

## <a name="backup"></a>Backup

### <a name="which-vm-images-can-be-enabled-for-backup-when-i-create-them"></a>Quali immagini di macchina virtuale possono essere abilitate per il backup durante la creazione?

Quando si crea una macchina virtuale, è possibile abilitare il backup per le macchine virtuali che eseguono [sistemi operativi supportati](backup-support-matrix-iaas.md#supported-backup-actions).

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

Fornire le autorizzazioni per il backup di Azure per accedere al Key Vault. Specificare le autorizzazioni in PowerShell come descritto nella sezione **Attivare un backup** nella documentazione relativa a [PowerShell di Backup di Azure](backup-azure-vms-automation.md).

### <a name="i-migrated-vm-disks-to-managed-disks-will-my-backups-continue-to-work"></a>È stata eseguita la migrazione dei dischi della macchina virtuale in dischi gestiti. I backup continueranno a funzionare?

Sì, i backup continueranno a funzionare senza problemi. Non è necessario riconfigurare alcun elemento.

### <a name="why-cant-i-see-my-vm-in-the-configure-backup-wizard"></a>Perché non è visibile la macchina virtuale nella procedura guidata Configura backup?

La procedura guidata visualizza solo le macchine virtuali che si trovano nella stessa area dell'insieme di credenziali e che non sono già state sottoposte a backup.

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>La macchina virtuale è stata arrestata. Il backup pianificato o su richiesta viene eseguito ugualmente?

Sì. I backup vengono eseguiti quando una macchina virtuale è spenta. Il punto di ripristino viene contrassegnato come coerente con l'arresto anomalo del sistema.

### <a name="can-i-cancel-an-in-progress-backup-job"></a>È possibile annullare un processo di backup in corso?

Sì. È possibile annullare il processo di backup in uno stato **snapshot in corso** . Non è possibile annullare un processo se è in corso il trasferimento di dati dallo snapshot.

### <a name="i-enabled-a-lock-on-the-resource-group-created-by-azure-backup-service-for-example-azurebackuprg_geo_number-will-my-backups-continue-to-work"></a>È stato abilitato un blocco sul gruppo di risorse creato dal servizio backup di Azure (ad esempio, `AzureBackupRG_<geo>_<number>` ). I backup continueranno a funzionare?

Se si blocca il gruppo di risorse creato dal servizio backup di Azure, i backup inizieranno ad avere esito negativo perché è previsto un limite massimo di 18 punti di ripristino.

Rimuovere il blocco e deselezionare la raccolta di punti di ripristino dal gruppo di risorse per fare in modo che i backup futuri abbiano esito positivo. Per rimuovere la raccolta di punti di ripristino, [attenersi alla seguente procedura](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-azure-portal) .

### <a name="does-azure-backup-support-standard-ssd-managed-disks"></a>Backup di Azure supporta dischi standard gestiti da unità SSD?

Sì, backup di Azure supporta [dischi gestiti SSD standard](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/).

### <a name="can-we-back-up-a-vm-with-a-write-accelerator-wa-enabled-disk"></a>È possibile eseguire il backup di una macchina virtuale con un disco con acceleratore di scrittura?

Non è possibile creare snapshot nel disco con acceleratore di scrittura. Il servizio Backup di Azure, tuttavia, può escludere questo disco dal backup.

### <a name="i-have-a-vm-with-write-accelerator-wa-disks-and-sap-hana-installed-how-do-i-back-up"></a>In una macchina virtuale sono presenti dischi con acceleratore di scrittura ed è installato SAP HANA. In che modo è possibile eseguire il backup?

Backup di Azure non può eseguire il backup del disco con acceleratore di scrittura, ma può escluderlo dal processo. Il backup, tuttavia, non assicurerà la coerenza del database perché non viene eseguito il backup delle informazioni presenti sul disco con acceleratore di scrittura. È possibile eseguire il backup di dischi con questa configurazione per ottenere il backup del disco del sistema operativo e il backup di dischi senza acceleratore di scrittura.

Backup di Azure offre una soluzione di backup di flusso per database di SAP HANA con un RPO di 15 minuti. Backint è certificata da SAP per offrire un supporto di backup nativo che sfrutta le API native di SAP HANA. Altre informazioni [sul backup di SAP Hana database nelle VM di Azure](./sap-hana-db-about.md).

### <a name="what-is-the-maximum-delay-i-can-expect-in-backup-start-time-from-the-scheduled-backup-time-i-have-set-in-my-vm-backup-policy"></a>Qual è il ritardo massimo che è possibile aspettarsi nell'ora di inizio del backup dall'ora di backup pianificata configurata nel criterio di backup della macchina virtuale?

Il backup pianificato verrà attivato entro 2 ore dall'ora del backup pianificato. Se, ad esempio, le VM 100 hanno un'ora di avvio di backup pianificata alle 2:00 AM, il processo di backup di 4:00 sarà il più recente di tutte le macchine virtuali 100. Se i backup pianificati sono stati sospesi a causa di un'interruzione e ripresi o ritentati, il backup può essere avviato al di fuori di questa finestra di due ore pianificata.

### <a name="what-is-the-minimum-allowed-retention-range-for-a-daily-backup-point"></a>Qual è il periodo di mantenimento dati minimo consentito per un punto di backup giornaliero?

Il criterio di backup della macchina virtuale di Azure supporta un periodo di conservazione minimo compreso tra sette giorni e 9999 giorni. Qualsiasi modifica a un criterio di backup di VM esistente con meno di sette giorni richiederà un aggiornamento per soddisfare il periodo di mantenimento dati minimo di sette giorni.

### <a name="what-happens-if-i-change-the-case-of-the-name-of-my-vm-or-my-vm-resource-group"></a>Che cosa accade se si modifica la combinazione di maiuscole e minuscole del nome della macchina virtuale o del gruppo di risorse VM?

Se si modifica il caso (in alto o in basso) della VM o del gruppo di risorse VM, il caso del nome dell'elemento di backup non verrà modificato. Tuttavia, si tratta di un comportamento previsto di backup di Azure. La modifica del case non verrà visualizzata nell'elemento di backup, ma verrà aggiornata nel back-end.

### <a name="can-i-back-up-or-restore-selective-disks-attached-to-a-vm"></a>È possibile eseguire il backup o il ripristino di dischi selettivi collegati a una macchina virtuale?

Backup di Azure ora supporta il backup e il ripristino selettivi dei dischi tramite la soluzione di backup delle macchine virtuali di Azure. Per altre informazioni, vedere [backup e ripristino di dischi selettivi per macchine virtuali di Azure](selective-disk-backup-restore.md).

### <a name="are-managed-identities-preserved-if-a-tenant-change-occurs-during-backup"></a>Le identità gestite vengono mantenute se si verifica una modifica del tenant durante il backup?

Se si verificano [modifiche al tenant](https://docs.microsoft.com/azure/devops/organizations/accounts/change-azure-ad-connection) , è necessario disabilitare e riabilitare le [identità gestite](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) per eseguire nuovamente il backup.

## <a name="restore"></a>Restore

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

### <a name="how-do-i-restore-a-vm-to-a-restore-point-before-the-vm-was-migrated-to-managed-disks"></a>In che modo si ripristina una macchina virtuale in un punto di ripristino prima della migrazione di tale macchina a dischi gestiti?

Il processo di ripristino rimane invariato. Se il punto di ripristino è temporizzato quando la macchina virtuale dispone di dischi non gestiti, è possibile ripristinare i [dischi come non gestiti](tutorial-restore-disk.md#unmanaged-disks-restore). Se la macchina virtuale aveva dischi gestiti, è possibile [ripristinare i dischi come Managed disks](tutorial-restore-disk.md#managed-disk-restore). Quindi, è possibile [creare una macchina virtuale da tali dischi](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk).

[Altre informazioni](backup-azure-vms-automation.md#restore-an-azure-vm) su come eseguire questa procedura in PowerShell.

### <a name="if-the-restore-fails-to-create-the-vm-what-happens-to-the-disks-included-in-the-restore"></a>Se il ripristino non riesce a creare la macchina virtuale, cosa accade ai dischi inclusi nel ripristino?

In caso di ripristino di una macchina virtuale gestita, anche se la creazione della macchina virtuale ha esito negativo, i dischi verranno comunque ripristinati.

### <a name="can-i-restore-a-vm-thats-been-deleted"></a>È possibile ripristinare una macchina virtuale che è stata eliminata?

Sì. Anche se si elimina la macchina virtuale, è possibile passare all'elemento di backup corrispondente nell'insieme di credenziali ed eseguire il ripristino da un punto di ripristino.

### <a name="how-do-i-restore-a-vm-to-the-same-availability-sets"></a>Ricerca per categorie ripristinare una macchina virtuale negli stessi set di disponibilità?

Per le macchine virtuali di Azure con dischi gestiti, il ripristino dei set di disponibilità viene abilitato fornendo un'opzione nel modello durante il ripristino come Managed Disks. Il parametro di input di questo modello è denominato **Set di disponibilità**.

### <a name="how-do-we-get-faster-restore-performances"></a>In che modo si ottengono migliori prestazioni di ripristino?

La funzionalità di [ripristino istantaneo](backup-instant-restore-capability.md) consente di eseguire backup più veloci e ripristini istantanei dagli snapshot.

### <a name="what-happens-when-we-change-the-key-vault-settings-for-the-encrypted-vm"></a>Cosa accade quando si modificano le impostazioni dell'insieme di credenziali delle chiavi per la VM crittografata?

Dopo aver modificato le impostazioni dell'insieme di credenziali delle chiavi per la VM crittografata, i backup continueranno a funzionare con il nuovo set di dettagli. Tuttavia, dopo il ripristino da un punto di ripristino prima della modifica, sarà necessario ripristinare i segreti in un insieme di credenziali delle chiavi prima di poter creare la macchina virtuale. Per altre informazioni, vedere questo [articolo](./backup-azure-restore-key-secret.md).

Operazioni come il rollup della chiave/segreto non richiedono questo passaggio e lo stesso insieme di credenziali delle chiavi può essere usato dopo il ripristino.

### <a name="can-i-access-the-vm-once-restored-due-to-a-vm-having-broken-relationship-with-domain-controller"></a>È possibile accedere alla VM una volta ripristinato a causa di una relazione tra una macchina virtuale e il controller di dominio?

Sì, si accede alla macchina virtuale dopo il ripristino a causa di una relazione di una macchina virtuale con il controller di dominio. Per altre informazioni, vedere questo [articolo](./backup-azure-arm-restore-vms.md#post-restore-steps)

## <a name="manage-vm-backups"></a>Gestire i backup delle macchine virtuali

### <a name="what-happens-if-i-modify-a-backup-policy"></a>Cosa accade se si modifica un criterio di backup?

Il backup della macchina virtuale viene eseguito usando le impostazioni di conservazione e pianificazione del criterio nuovo o modificato.

- Se il periodo di conservazione viene esteso, i punti di ripristino esistenti vengono contrassegnati per essere mantenuti conformi al nuovo criterio.
- Se il periodo di conservazione viene ridotto, i punti di ripristino vengono contrassegnati per l'eliminazione ed eliminati nel successivo processo di pulizia.

### <a name="how-do-i-move-a-vm-backed-up-by-azure-backup-to-a-different-resource-group"></a>In che modo è possibile spostare una macchina virtuale sottoposta a backup tramite Backup di Azure in un gruppo di risorse diverso?

1. Arrestare temporaneamente il backup e conservare i dati di backup.
2. Per spostare le macchine virtuali configurate con backup di Azure, seguire questa procedura:

   1. Trovare il percorso della macchina virtuale.
   2. Trovare un gruppo di risorse con il modello di denominazione seguente: `AzureBackupRG_<location of your VM>_1` . Ad esempio, *AzureBackupRG_westus2_1*
   3. Nella portale di Azure selezionare **Mostra tipi nascosti**.
   4. Trovare la risorsa con il tipo **Microsoft. Compute/restorePointCollections** con il modello di denominazione `AzureBackup_<name of your VM that you're trying to move>_###########` .
   5. Eliminare la risorsa. Con questa operazione vengono eliminati solo i punti di ripristino istantaneo, non i dati di backup presenti nell'insieme di credenziali.
   6. Al termine dell'operazione di eliminazione, è possibile spostare la macchina virtuale.

3. Spostare la macchina virtuale nel gruppo di risorse di destinazione.
4. Riprendere il backup.

È possibile ripristinare la macchina virtuale dai punti di ripristino disponibili creati prima dell'operazione di spostamento.

### <a name="what-happens-after-i-move-a-vm-to-a-different-resource-group"></a>Cosa accade dopo aver spostato una macchina virtuale in un gruppo di risorse diverso?

Quando una macchina virtuale viene spostata in un gruppo di risorse diverso, si tratta di una nuova macchina virtuale per quanto concerne backup di Azure.

Dopo aver spostato la macchina virtuale in un nuovo gruppo di risorse, è possibile riproteggere la macchina virtuale nello stesso insieme di credenziali o in un insieme di credenziali diverso. Poiché si tratta di una nuova macchina virtuale per backup di Azure, ti verrà addebitata separatamente.

Se necessario, i punti di ripristino della macchina virtuale precedente saranno disponibili per il ripristino. Se questi dati di backup non sono necessari, è possibile arrestare la protezione della macchina virtuale precedente con l'eliminazione dei dati.

### <a name="is-there-a-limit-on-number-of-vms-that-can-beassociated-with-the-same-backup-policy"></a>Esiste un limite al numero di macchine virtuali che possono essere associate allo stesso criterio di backup?

Sì, è previsto un limite di 100 VM che possono essere associate agli stessi criteri di backup dal portale. Per più di 100 macchine virtuali, è consigliabile creare più criteri di backup con la stessa pianificazione o una pianificazione diversa.

### <a name="how-can-i-view-the-retention-settings-for-my-backups"></a>Come è possibile visualizzare le impostazioni di conservazione per i backup?

Attualmente, è possibile visualizzare le impostazioni di conservazione a livello di elemento di backup (VM) in base al criterio di backup assegnato alla macchina virtuale.

Per visualizzare le impostazioni di conservazione per i backup, è possibile passare al [Dashboard](https://docs.microsoft.com/azure/backup/backup-azure-manage-vms#view-vms-on-the-dashboard) dell'elemento di backup per la macchina virtuale, nella portale di Azure. Selezionando il collegamento ai criteri di backup è possibile visualizzare la durata di conservazione di tutti i punti di conservazione giornalieri, settimanali, mensili e annuali associati alla macchina virtuale.

È anche possibile usare [Esplora backup](https://docs.microsoft.com/azure/backup/monitor-azure-backup-with-backup-explorer) per visualizzare le impostazioni di conservazione per tutte le macchine virtuali all'interno di un singolo riquadro di vetro. Passare a backup Explorer da qualsiasi insieme di credenziali di servizi di ripristino, passare alla scheda **elementi di backup** e selezionare la visualizzazione avanzata per visualizzare informazioni dettagliate sulla conservazione per ogni macchina virtuale.
