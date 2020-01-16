---
title: Funzionalità di sicurezza per proteggere i carichi di lavoro cloud
description: Informazioni su come usare le funzionalità di sicurezza in backup di Azure per rendere più sicuri i backup.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: e4519a342e1be3244b5d4598880e9ad490f50030
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028216"
---
# <a name="security-features-to-help-protect-cloud-workloads-that-use-azure-backup"></a>Funzionalità di sicurezza che consentono di proteggere i carichi di lavoro cloud che usano backup di Azure

Le preoccupazioni riguardo ai problemi di sicurezza, come malware, ransomware e intrusioni, aumentano continuamente. Questi problemi di sicurezza possono essere costosi in termini di denaro e di dati. Per evitare tali attacchi, backup di Azure offre ora funzionalità di sicurezza che consentono di proteggere i dati di backup anche dopo l'eliminazione. Una di queste funzionalità è l'eliminazione temporanea. Con l'eliminazione temporanea, anche se un attore malintenzionato Elimina il backup di una macchina virtuale (o i dati di backup vengono accidentalmente eliminati), i dati di backup vengono conservati per 14 giorni aggiuntivi, consentendo il recupero dell'elemento di backup senza perdita di dati. Questi ulteriori 14 giorni di conservazione dei dati di backup nello stato "eliminazione temporanea" non comportano alcun costo per il cliente. Azure crittografa inoltre tutti i dati di backup inattivi usando [crittografia del servizio di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) per proteggere ulteriormente i dati.

> [!NOTE]
> L'eliminazione temporanea protegge solo i dati di backup eliminati. Se una macchina virtuale viene eliminata senza un backup, la funzionalità di eliminazione temporanea non manterrà i dati. Tutte le risorse devono essere protette con backup di Azure per garantire la resilienza completa.
>

## <a name="soft-delete"></a>Eliminazione temporanea

### <a name="supported-regions"></a>Aree supportate

L'eliminazione temporanea è attualmente supportata negli Stati Uniti centro-occidentali, Asia orientale, Canada centrale, Canada orientale, Francia centrale, Francia meridionale, Corea centrale, Corea meridionale, Regno Unito meridionale, Regno Unito occidentale, Australia orientale, Australia sudorientale, Europa settentrionale, Stati Uniti occidentali, Stati Uniti occidentali, Stati Uniti centro-meridionali Asia orientale, Stati Uniti centro-settentrionali, Stati Uniti centro-meridionali, Giappone orientale, Giappone occidentale, India meridionale, India centrale, India occidentale, Stati Uniti orientali 2, Svizzera settentrionale, Svizzera occidentale e tutte le aree nazionali.

### <a name="soft-delete-for-vms-using-azure-portal"></a>Eliminazione temporanea per le macchine virtuali con portale di Azure

1. Per eliminare i dati di backup di una macchina virtuale, è necessario arrestare il backup. Nel portale di Azure passare all'insieme di credenziali di servizi di ripristino, fare clic con il pulsante destro del mouse sull'elemento di backup e scegliere **Interrompi backup**.

   ![Screenshot degli elementi di backup portale di Azure](./media/backup-azure-security-feature-cloud/backup-stopped.png)

2. Nella finestra seguente verrà offerta la possibilità di eliminare o mantenere i dati di backup. Se si sceglie **Elimina dati di backup** e quindi **Interrompi backup**, il backup della macchina virtuale non verrà eliminato definitivamente. I dati di backup verranno invece conservati per 14 giorni nello stato di eliminazione temporanea. Se si sceglie **Elimina dati di backup** , viene inviato un avviso di eliminazione tramite posta elettronica all'ID di posta elettronica configurato, che informa l'utente che rimane un periodo di conservazione esteso per i dati di backup di 14 giorni. Viene inoltre inviato un avviso di posta elettronica il giorno 12 per informare che ci sono altri due giorni rimasti per resuscitare i dati eliminati. L'eliminazione viene posticipata fino al 15 ° giorno, quando si verifica l'eliminazione permanente e viene inviato un avviso di posta elettronica finale che informa sull'eliminazione permanente dei dati.

   ![Screenshot della schermata portale di Azure, Interrompi backup](./media/backup-azure-security-feature-cloud/delete-backup-data.png)

3. Durante questi 14 giorni, nell'insieme di credenziali di servizi di ripristino, la macchina virtuale eliminata temporaneamente verrà visualizzata con un'icona "soft-delete" rossa accanto.

   ![Screenshot di portale di Azure, VM in stato di eliminazione temporanea](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

   > [!NOTE]
   > Se nell'insieme di credenziali sono presenti elementi di backup eliminati temporaneamente, non è possibile eliminare l'insieme di credenziali in quel momento. Provare l'eliminazione dell'insieme di credenziali dopo che gli elementi di backup sono stati eliminati definitivamente e non è presente alcun elemento nello stato di eliminazione temporanea lasciato nell'insieme di credenziali.

4. Per ripristinare la macchina virtuale eliminata temporaneamente, è prima necessario annullarne l'eliminazione. Per annullare l'eliminazione, scegliere la macchina virtuale eliminata temporaneamente, quindi selezionare l'opzione **Annulla eliminazione**.

   ![Screenshot della portale di Azure, annullamento dell'eliminazione della macchina virtuale](./media/backup-azure-security-feature-cloud/choose-undelete.png)

   Verrà visualizzata una finestra in cui viene visualizzato un avviso che indica che se si sceglie Annulla eliminazione, tutti i punti di ripristino per la macchina virtuale verranno annullati e resi disponibili per l'esecuzione di un'operazione di ripristino. La macchina virtuale verrà mantenuta in uno stato "Arresta protezione dati con Mantieni dati" con i backup sospesi e i dati di backup mantenuti per sempre senza alcun criterio di backup valido.

   ![Screenshot di portale di Azure, confermare l'annullamento dell'eliminazione della macchina virtuale](./media/backup-azure-security-feature-cloud/undelete-vm.png)

   A questo punto, è anche possibile ripristinare la macchina virtuale selezionando **Ripristina macchina virtuale** dal punto di ripristino scelto.  

   ![Screenshot dell'opzione di portale di Azure, ripristino macchina virtuale](./media/backup-azure-security-feature-cloud/restore-vm.png)

   > [!NOTE]
   > Il Garbage Collector viene eseguito e puliti i punti di ripristino scaduti solo dopo che l'utente esegue l'operazione di **ripresa del backup** .

5. Al termine del processo di annullamento dell'eliminazione, lo stato tornerà a "Interrompi backup con Mantieni dati", quindi è possibile scegliere **Riprendi backup**. L'operazione di **ripresa del backup** riporta l'elemento di backup nello stato attivo, associato a un criterio di backup selezionato dall'utente che definisce le pianificazioni di backup e conservazione.

   ![Screenshot del portale di Azure, Riprendi l'opzione backup](./media/backup-azure-security-feature-cloud/resume-backup.png)

Questo diagramma di flusso Mostra i diversi passaggi e Stati di un elemento di backup quando l'eliminazione temporanea è abilitata:

![Ciclo di vita dell'elemento di backup eliminato temporaneamente](./media/backup-azure-security-feature-cloud/lifecycle.png)

Per ulteriori informazioni, vedere la sezione [domande frequenti](backup-azure-security-feature-cloud.md#frequently-asked-questions) più avanti.

### <a name="soft-delete-for-vms-using-azure-powershell"></a>Eliminazione temporanea per le macchine virtuali con Azure PowerShell

> [!IMPORTANT]
> La versione AZ. RecoveryServices necessaria per usare l'eliminazione temporanea con Azure PS è min 2.2.0. Usare ```Install-Module -Name Az.RecoveryServices -Force``` per ottenere la versione più recente.

Come descritto in precedenza per portale di Azure, la sequenza dei passaggi è identica anche quando si usa Azure PowerShell.

#### <a name="delete-the-backup-item-using-azure-powershell"></a>Eliminare l'elemento di backup usando Azure PowerShell

Eliminare l'elemento di backup usando il cmdlet [Disable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

Il ' DeleteState ' dell'elemento di backup cambierà da' NotDeleted ' a' ToBeDeleted '. I dati di backup verranno conservati per 14 giorni. Se si desidera annullare l'operazione di eliminazione, è necessario eseguire l'annullamento dell'eliminazione.

#### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>Esecuzione dell'operazione di eliminazione mediante Azure PowerShell

Per prima cosa, recuperare l'elemento di backup pertinente che si trova nello stato di eliminazione temporanea, ovvero sta per essere eliminato.

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

Eseguire quindi l'operazione di annullamento dell'eliminazione usando il cmdlet [Undo-AzRecoveryServicesBackupItemDeletion](https://docs.microsoft.com/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion?view=azps-3.1.0) PS.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

Il ' DeleteState ' dell'elemento di backup verrà ripristinato in ' NotDeleted '. Ma la protezione è ancora stata arrestata. È necessario [riprendere il backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#change-policy-for-backup-items) per abilitare nuovamente la protezione.

### <a name="soft-delete-for-vms-using-rest-api"></a>Eliminazione temporanea per le macchine virtuali con l'API REST

- Eliminare i backup usando l'API REST come indicato [qui](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data).
- Se l'utente desidera annullare queste operazioni di eliminazione, fare riferimento ai passaggi indicati [qui](backup-azure-arm-userestapi-backupazurevms.md#undo-the-stop-protection-and-delete-data).

## <a name="disabling-soft-delete"></a>Disabilitazione dell'eliminazione temporanea

L'eliminazione temporanea è abilitata per impostazione predefinita negli insiemi di credenziali appena creati per proteggere i dati di backup da eliminazioni accidentali o dannose.  La disabilitazione di questa funzionalità non è consigliata. L'unica circostanza in cui è consigliabile disabilitare l'eliminazione temporanea è se si prevede di trasferire gli elementi protetti in un nuovo insieme di credenziali e non è possibile attendere i 14 giorni necessari prima dell'eliminazione e della riprotezione (ad esempio in un ambiente di test). Questa funzionalità può essere disabilitata solo da un amministratore di backup. Se questa funzionalità viene disabilitata, tutte le eliminazioni di elementi protetti comporteranno la rimozione immediata, senza la possibilità di eseguire il ripristino. I dati di backup in stato di eliminazione temporanea prima della disabilitazione di questa funzionalità rimarranno in stato di eliminazione temporanea. Per eliminare definitivamente questi elementi immediatamente, è necessario annullarne l'eliminazione ed eliminarli di nuovo per eliminarli definitivamente.

### <a name="disabling-soft-delete-using-azure-portal"></a>Disabilitazione dell'eliminazione temporanea con portale di Azure

Per disabilitare l'eliminazione temporanea, attenersi alla procedura seguente:

1. Nel portale di Azure passare all'insieme di credenziali, quindi passare a **impostazioni** -> **Proprietà**.
2. Nel riquadro Proprietà selezionare impostazioni di **sicurezza** -> **Aggiorna**.  
3. Nel riquadro impostazioni di sicurezza, in **eliminazione**temporanea, selezionare **Disabilita**.

![Disabilitare l'eliminazione temporanea](./media/backup-azure-security-feature-cloud/disable-soft-delete.png)

### <a name="disabling-soft-delete-using-azure-powershell"></a>Disabilitazione dell'eliminazione temporanea con Azure PowerShell

> [!IMPORTANT]
> La versione AZ. RecoveryServices necessaria per usare l'eliminazione temporanea con Azure PS è min 2.2.0. Usare ```Install-Module -Name Az.RecoveryServices -Force``` per ottenere la versione più recente.

Per disabilitare, usare il cmdlet [set-AzRecoveryServicesVaultBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty?view=azps-3.1.0) di PS.

```powershell
Set-AzRecoveryServicesVaultProperty -VaultId $myVaultID -SoftDeleteFeatureState Disable


StorageModelType       :
StorageType            :
StorageTypeState       :
EnhancedSecurityState  : Enabled
SoftDeleteFeatureState : Disabled
```

### <a name="disabling-soft-delete-using-rest-api"></a>Disabilitazione dell'eliminazione temporanea con l'API REST

Per disabilitare la funzionalità di eliminazione temporanea usando l'API REST, vedere i passaggi indicati [qui](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api).

## <a name="permanently-deleting-soft-deleted-backup-items"></a>Eliminazione definitiva degli elementi di backup eliminati temporaneamente

I dati di backup in stato di eliminazione temporanea prima della disabilitazione di questa funzionalità rimarranno in stato di eliminazione temporanea. Per eliminare definitivamente questi elementi immediatamente, annullarne l'eliminazione ed eliminarli di nuovo per eliminarli definitivamente.

### <a name="using-azure-portal"></a>Uso del portale di Azure

A tale scopo, seguire questa procedura:

1. Seguire i passaggi per [disabilitare l'eliminazione](#disabling-soft-delete)temporanea.
2. Nel portale di Azure passare all'insieme di credenziali, passare a **elementi di backup**e scegliere la macchina virtuale eliminata temporaneamente.

![Scegliere una macchina virtuale temporaneamente eliminata](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

3. Selezionare l'opzione **Annulla eliminazione**.

![Scegli Annulla eliminazione](./media/backup-azure-security-feature-cloud/choose-undelete.png)


4. Verrà visualizzata una finestra. Selezionare **Annulla eliminazione**.

![Selezionare Annulla eliminazione](./media/backup-azure-security-feature-cloud/undelete-vm.png)

5. Scegliere **Elimina dati di backup** per eliminare definitivamente i dati di backup.

![Scegliere Elimina dati di backup](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-buttom.png)

6. Digitare il nome dell'elemento di backup per confermare che si desidera eliminare i punti di ripristino.

![Digitare il nome dell'elemento di backup](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-data1.png)

7. Per eliminare i dati di backup per l'elemento, selezionare **Elimina**. Un messaggio di notifica informa che i dati di backup sono stati eliminati.

### <a name="using-azure-powershell"></a>Uso di Azure PowerShell

Se gli elementi sono stati eliminati prima della disabilitazione dell'eliminazione temporanea, saranno in uno stato di eliminazione temporanea. Per eliminare immediatamente tali elementi, è necessario invertire l'operazione di eliminazione e quindi eseguire di nuovo l'operazione.

Identificare gli elementi in stato di eliminazione temporanea.

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

Quindi invertire l'operazione di eliminazione eseguita quando è stata abilitata l'eliminazione temporanea.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

Poiché l'eliminazione temporanea è ora disabilitata, l'operazione di eliminazione comporterà la rimozione immediata dei dati di backup.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

### <a name="using-rest-api"></a>Uso dell'API REST

Se gli elementi sono stati eliminati prima della disabilitazione dell'eliminazione temporanea, saranno in uno stato di eliminazione temporanea. Per eliminare immediatamente tali elementi, è necessario invertire l'operazione di eliminazione e quindi eseguire di nuovo l'operazione.

1. Per prima cosa, annullare le operazioni di eliminazione con i passaggi indicati di [seguito](backup-azure-arm-userestapi-backupazurevms.md#undo-the-stop-protection-and-delete-data).
2. Disabilitare quindi la funzionalità di eliminazione temporanea usando l'API REST usando la procedura descritta [qui](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api).
3. Eliminare quindi i backup usando l'API REST come indicato [qui](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data).

## <a name="encryption"></a>Crittografia

### <a name="encryption-of-backup-data-using-microsoft-managed-keys"></a>Crittografia dei dati di backup tramite chiavi gestite da Microsoft

I dati di backup vengono crittografati automaticamente usando la crittografia di archiviazione di Azure. La crittografia protegge i dati e consente di soddisfare gli impegni di sicurezza e conformità dell'organizzazione. I dati vengono crittografati e decrittografati in modo trasparente usando la crittografia AES a 256 bit, una delle crittografie a blocchi più solide disponibili ed è conforme a FIPS 140-2. La crittografia di archiviazione di Azure è simile alla crittografia BitLocker per Windows.

In Azure, i dati in transito tra archiviazione di Azure e l'insieme di credenziali sono protetti da HTTPS. Questi dati rimangono all'interno della rete backbone di Azure.

Per altre informazioni, vedere [crittografia di archiviazione di Azure per dati](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)inattivi. Vedere le domande [frequenti su backup di Azure](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq#encryption) per rispondere a eventuali domande sulla crittografia.

### <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Crittografia dei dati di backup tramite chiavi gestite dal cliente

Quando si esegue il backup di macchine virtuali di Azure, è anche possibile crittografare i dati di backup nell'insieme di credenziali di servizi di ripristino usando le chiavi di crittografia archiviate nel Azure Key Vault.

>[!NOTE]
>Questa funzionalità è attualmente in uso in anticipo. Compilare [questo sondaggio](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapURE9TTDRIUEUyNFhNT1lZS1BNVDdZVllHWi4u) se si vuole crittografare i dati di backup usando chiavi gestite dal cliente. Si noti che la possibilità di usare questa funzionalità è soggetta all'approvazione del servizio backup di Azure.

### <a name="backup-of-managed-disk-vm-encrypted-using-customer-managed-keys"></a>Backup di macchine virtuali con dischi gestiti crittografate con chiavi gestite dal cliente

Backup di Azure consente di eseguire il backup di macchine virtuali di Azure contenenti dischi crittografati usando chiavi gestite dal cliente. Per informazioni dettagliate, vedere [crittografia dei dischi gestiti con chiavi gestite dal cliente](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#customer-managed-keys).

### <a name="backup-of-encrypted-vms"></a>Backup di macchine virtuali crittografate

È possibile eseguire il backup e il ripristino di macchine virtuali di Azure (VM) Windows o Linux con dischi crittografati tramite il servizio backup di Azure. Per istruzioni, vedere [eseguire il backup e il ripristino di macchine virtuali crittografate con backup di Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).

## <a name="other-security-features"></a>Altre funzionalità di sicurezza

### <a name="protection-of-azure-backup-recovery-points"></a>Protezione dei punti di ripristino di backup di Azure

Gli account di archiviazione usati dagli insiemi di credenziali dei servizi di ripristino sono isolati e non è possibile accedervi dagli utenti per eventuali scopi dannosi. L'accesso è consentito solo tramite le operazioni di gestione di backup di Azure, ad esempio il ripristino. Queste operazioni di gestione sono controllate tramite il controllo degli accessi in base al ruolo (RBAC).

Per altre informazioni, vedere [usare il controllo degli accessi in base al ruolo per gestire i punti di ripristino di backup di Azure](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault).

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="for-soft-delete"></a>Per l'eliminazione temporanea

#### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>È necessario abilitare la funzionalità di eliminazione temporanea in ogni insieme di credenziali?

No, viene creato e abilitato per impostazione predefinita per tutti gli insiemi di credenziali dei servizi di ripristino.

#### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-delete-operation-is-complete"></a>È possibile configurare il numero di giorni per cui i dati verranno conservati nello stato di eliminazione temporanea dopo il completamento dell'operazione di eliminazione?

No, viene fissato a 14 giorni di conservazione aggiuntiva dopo l'operazione di eliminazione.

#### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>È necessario pagare il costo per questa conservazione aggiuntiva di 14 giorni?

No, questo periodo di conservazione aggiuntivo di 14 giorni è gratuito come parte della funzionalità di eliminazione temporanea.

#### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>È possibile eseguire un'operazione di ripristino quando i dati sono in stato di eliminazione temporanea?

No, è necessario annullare l'eliminazione della risorsa eliminata temporaneamente per poter eseguire il ripristino. L'operazione di annullamento dell'eliminazione riporterà la risorsa nello **stato di arresto della protezione con Mantieni dati** in cui è possibile eseguire il ripristino in qualsiasi momento. Il Garbage Collector rimane in pausa in questo stato.

#### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>Gli snapshot seguiranno lo stesso ciclo di vita dei punti di ripristino nell'insieme di credenziali?

Sì.

#### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>Come è possibile attivare di nuovo i backup pianificati per una risorsa eliminata temporaneamente?

L'annullamento dell'eliminazione seguito dall'operazione di ripresa proteggerà di nuovo la risorsa. L'operazione di ripresa associa un criterio di backup per attivare i backup pianificati con il periodo di memorizzazione selezionato. Inoltre, il Garbage Collector viene eseguito non appena l'operazione di ripresa viene completata. Se si desidera eseguire un ripristino da un punto di ripristino che supera la data di scadenza, è consigliabile eseguire l'operazione prima di attivare l'operazione di ripresa.

#### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>È possibile eliminare l'insieme di credenziali se sono presenti elementi eliminati temporaneamente nell'insieme di credenziali?

Non è possibile eliminare l'insieme di credenziali di servizi di ripristino se sono presenti elementi di backup nello stato di eliminazione temporanea nell'insieme di credenziali. Gli elementi eliminati temporaneamente vengono eliminati definitivamente 14 giorni dopo l'operazione di eliminazione. Se non è possibile attendere 14 giorni, [disabilitare l'eliminazione](#disabling-soft-delete)temporanea, annullare l'eliminazione degli elementi eliminati temporaneamente ed eliminarli di nuovo per eliminarli definitivamente. Dopo aver verificato che non siano presenti elementi protetti e che non siano presenti elementi eliminati temporaneamente, è possibile eliminare l'insieme di credenziali.  

#### <a name="can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>È possibile eliminare i dati prima del periodo di eliminazione temporanea di 14 giorni dopo l'eliminazione?

No. Non è possibile forzare l'eliminazione degli elementi eliminati temporaneamente, che vengono eliminati automaticamente dopo 14 giorni. Questa funzionalità di sicurezza è abilitata per salvaguardare i dati di backup da eliminazioni accidentali o dannose.  È necessario attendere 14 giorni prima di eseguire qualsiasi altra azione nella macchina virtuale.  Gli elementi eliminati temporaneamente non verranno addebitati.  Se è necessario riproteggere le VM contrassegnate per l'eliminazione temporanea entro 14 giorni da un nuovo insieme di credenziali, contattare il supporto tecnico Microsoft.

#### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>È possibile eseguire operazioni di eliminazione temporanea in PowerShell o nell'interfaccia della riga di comando?

Le operazioni di eliminazione temporanea possono essere eseguite usando [PowerShell](#soft-delete-for-vms-using-azure-powershell). Attualmente, l'interfaccia della riga di comando non è supportata.

#### <a name="is-soft-delete-supported-for-other-cloud-workloads-like-sql-server-in-azure-vms-and-sap-hana-in-azure-vms"></a>L'eliminazione temporanea è supportata per altri carichi di lavoro cloud, ad esempio SQL Server in macchine virtuali di Azure e SAP HANA in macchine virtuali di Azure?

No. L'eliminazione temporanea è attualmente supportata solo per le macchine virtuali di Azure.

## <a name="next-steps"></a>Passaggi successivi

- Vedere informazioni sui [controlli di sicurezza per backup di Azure](backup-security-controls.md).
