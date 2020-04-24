---
title: Funzionalità di sicurezza per proteggere i carichi di lavoro cloud
description: Informazioni su come usare le funzionalità di sicurezza in backup di Azure per rendere più sicuri i backup.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: bd7c86e18114513a264a0f9252589533fb7ff2d3
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668730"
---
# <a name="security-features-to-help-protect-cloud-workloads-that-use-azure-backup"></a>Funzionalità di sicurezza che consentono di proteggere i carichi di lavoro cloud che usano backup di Azure

Le preoccupazioni riguardo ai problemi di sicurezza, come malware, ransomware e intrusioni, aumentano continuamente. Questi problemi di sicurezza possono essere costosi in termini di denaro e di dati. Per evitare tali attacchi, backup di Azure offre ora funzionalità di sicurezza che consentono di proteggere i dati di backup anche dopo l'eliminazione.

Una di queste funzionalità è l'eliminazione temporanea. Con l'eliminazione temporanea, anche se un attore malintenzionato Elimina il backup di una macchina virtuale (o i dati di backup vengono accidentalmente eliminati), i dati di backup vengono conservati per 14 giorni aggiuntivi, consentendo il recupero dell'elemento di backup senza perdita di dati. Gli altri 14 giorni di conservazione dei dati di backup nello stato "eliminazione temporanea" non comportano alcun costo per il cliente. Azure crittografa inoltre tutti i dati di backup inattivi usando [crittografia del servizio di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) per proteggere ulteriormente i dati.

La protezione dell'eliminazione temporanea per le macchine virtuali di Azure è disponibile a livello generale.

>[!NOTE]
>L'eliminazione temporanea per SQL Server nella macchina virtuale di Azure e l'eliminazione temporanea per SAP HANA nei carichi di lavoro delle macchine virtuali di Azure sono ora disponibili in anteprima.<br>
>Per iscriverti all'anteprima, scrivici all'indirizzoAskAzureBackupTeam@microsoft.com

## <a name="soft-delete"></a>Eliminazione temporanea

### <a name="soft-delete-for-vms"></a>Eliminazione temporanea per le macchine virtuali

L'eliminazione temporanea per le VM protegge i backup delle macchine virtuali da eliminazioni indesiderate. Anche dopo l'eliminazione, i backup vengono conservati nello stato di eliminazione temporanea per 14 giorni aggiuntivi.

> [!NOTE]
> L'eliminazione temporanea protegge solo i dati di backup eliminati. Se una macchina virtuale viene eliminata senza un backup, la funzionalità di eliminazione temporanea non manterrà i dati. Tutte le risorse devono essere protette con backup di Azure per garantire la resilienza completa.
>

### <a name="supported-regions"></a>Aree supportate

L'eliminazione temporanea è attualmente supportata negli Stati Uniti centro-occidentali, Asia orientale, Canada centrale, Canada orientale, Francia centrale, Francia meridionale, Corea centrale, Corea meridionale, Regno Unito meridionale, Regno Unito occidentale, Australia orientale, Australia sud-orientale, Europa settentrionale, Stati Uniti occidentali, Stati Uniti centro-occidentali, Stati Uniti centro-meridionali, Stati Uniti centro-settentrionali, Stati Uniti centro Asia orientale-occidentali, Giappone orientale, Giappone occidentale, India meridionale, India centrale, India occidentale , Svizzera settentrionale, Svizzera occidentale, Norvegia occidentale, Norvegia orientale e tutte le aree nazionali.

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

#### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>Esecuzione dell'operazione di eliminazione utilizzando Azure PowerShell

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

Il ' DeleteState ' dell'elemento di backup verrà ripristinato in ' NotDeleted '. Ma la protezione è ancora stata arrestata. [Riprendere il backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#change-policy-for-backup-items) per abilitare di nuovo la protezione.

### <a name="soft-delete-for-vms-using-rest-api"></a>Eliminazione temporanea per le macchine virtuali con l'API REST

- Eliminare i backup usando l'API REST come indicato [qui](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data).
- Se l'utente desidera annullare queste operazioni di eliminazione, fare riferimento ai passaggi indicati [qui](backup-azure-arm-userestapi-backupazurevms.md#undo-the-stop-protection-and-delete-data).

## <a name="disabling-soft-delete"></a>Disabilitazione dell'eliminazione temporanea

L'eliminazione temporanea è abilitata per impostazione predefinita negli insiemi di credenziali appena creati per proteggere i dati di backup da eliminazioni accidentali o dannose.  La disabilitazione di questa funzionalità non è consigliata. L'unica circostanza in cui è consigliabile disabilitare l'eliminazione temporanea è se si prevede di trasferire gli elementi protetti in un nuovo insieme di credenziali e non è possibile attendere i 14 giorni necessari prima dell'eliminazione e della riprotezione (ad esempio in un ambiente di test). Questa funzionalità può essere disabilitata solo dal proprietario dell'insieme di credenziali. Se questa funzionalità viene disabilitata, tutte le successive eliminazioni di elementi protetti comporteranno la rimozione immediata, senza la possibilità di eseguire il ripristino. I dati di backup presenti nello stato di eliminazione temporanea prima di disabilitare questa funzionalità rimarranno in stato di eliminazione temporanea per il periodo di 14 giorni. Per eliminare definitivamente questi elementi immediatamente, è necessario annullarne l'eliminazione ed eliminarli di nuovo per eliminarli definitivamente.

### <a name="disabling-soft-delete-using-azure-portal"></a>Disabilitazione dell'eliminazione temporanea con portale di Azure

Per disabilitare l'eliminazione temporanea, attenersi alla procedura seguente:

1. Nel portale di Azure passare all'insieme di credenziali, quindi passare a **Impostazioni** -> **proprietà**.
2. Nel riquadro Proprietà selezionare **Impostazioni** -> di sicurezza**Aggiorna**.  
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

Tutti i dati di cui è stato eseguito il backup vengono crittografati automaticamente quando vengono archiviati nel cloud usando la crittografia di archiviazione di Azure, che consente di soddisfare gli impegni di sicurezza e conformità. I dati inattivi vengono crittografati usando la crittografia AES a 256 bit, una delle crittografie a blocchi più solide disponibili ed è conforme a FIPS 140-2.

Oltre alla crittografia dei dati inattivi, tutti i dati di backup in transito vengono trasferiti tramite HTTPS. Rimane sempre nella rete backbone di Azure.

Per altre informazioni, vedere [crittografia di archiviazione di Azure per dati](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)inattivi. Vedere le domande [frequenti su backup di Azure](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq#encryption) per rispondere a eventuali domande sulla crittografia.

### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>Crittografia dei dati di backup tramite chiavi gestite dalla piattaforma

Per impostazione predefinita, tutti i dati vengono crittografati usando chiavi gestite dalla piattaforma. Non è necessario eseguire alcuna azione esplicita da parte dell'utente per abilitare questa crittografia e si applica a tutti i carichi di lavoro di cui viene eseguito il backup nell'insieme di credenziali di servizi di ripristino.

### <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Crittografia dei dati di backup tramite chiavi gestite dal cliente

Quando si esegue il backup delle macchine virtuali di Azure, è ora possibile crittografare i dati usando le chiavi di proprietà e gestite dall'utente. Backup di Azure consente di usare le chiavi RSA archiviate nel Azure Key Vault per la crittografia dei backup. La chiave di crittografia usata per crittografare i backup può essere diversa da quella usata per l'origine. I dati vengono protetti usando una chiave di crittografia dei dati basata su AES 256, che a sua volta è protetta usando le chiavi. In questo modo si ottiene il controllo completo sui dati e sulle chiavi. Per consentire la crittografia, è necessario che all'insieme di credenziali dei servizi di ripristino sia concesso l'accesso alla chiave di crittografia nel Azure Key Vault. È possibile disabilitare la chiave o revocare l'accesso ogni volta che è necessario. Tuttavia, è necessario abilitare la crittografia usando le chiavi prima di provare a proteggere gli elementi nell'insieme di credenziali.

>[!NOTE]
>Questa funzionalità è attualmente in disponibilità limitata. Compila [questo sondaggio](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapURE9TTDRIUEUyNFhNT1lZS1BNVDdZVllHWi4u) e invia un messaggio di posta AskAzureBackupTeam@microsoft.com elettronica all'indirizzo se desideri crittografare i dati di backup usando chiavi gestite dal cliente. Si noti che la possibilità di usare questa funzionalità è soggetta all'approvazione del servizio backup di Azure.

### <a name="backup-of-managed-disk-vms-encrypted-using-customer-managed-keys"></a>Backup di macchine virtuali con dischi gestiti crittografate con chiavi gestite dal cliente

Backup di Azure consente anche di eseguire il backup delle macchine virtuali di Azure che usano la chiave per la crittografia lato server. La chiave usata per crittografare i dischi è archiviata nel Azure Key Vault e gestita dall'utente. La crittografia lato server con chiavi gestite dal cliente è diversa da quella di crittografia dischi di Azure, perché ADE USA BitLocker (per Windows) e DM-crypt (per Linux) per eseguire la crittografia in-Guest, SSE crittografa i dati nel servizio di archiviazione, consentendo di usare qualsiasi sistema operativo o immagine per le macchine virtuali. Per altri dettagli, vedere [crittografia dei dischi gestiti con chiavi gestite dal cliente](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#customer-managed-keys) .

### <a name="backup-of-vms-encrypted-using-ade"></a>Backup di macchine virtuali crittografate tramite ADE

Con backup di Azure è anche possibile eseguire il backup delle macchine virtuali di Azure con il sistema operativo o i dischi dati crittografati tramite crittografia dischi di Azure. ADE USA BitLocker per le macchine virtuali Windows e DM-Crypt per le macchine virtuali Linux per eseguire la crittografia nel guest. Per informazioni dettagliate, vedere backup [e ripristino di macchine virtuali crittografate con backup di Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).

## <a name="private-endpoints"></a>Endpoint privati

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

## <a name="other-security-features"></a>Altre funzionalità di sicurezza

### <a name="protection-of-azure-backup-recovery-points"></a>Protezione dei punti di ripristino di backup di Azure

Gli account di archiviazione usati dagli insiemi di credenziali dei servizi di ripristino sono isolati e non è possibile accedervi dagli utenti per eventuali scopi dannosi. L'accesso è consentito solo tramite le operazioni di gestione di backup di Azure, ad esempio il ripristino. Queste operazioni di gestione sono controllate tramite il controllo degli accessi in base al ruolo (RBAC).

Per altre informazioni, vedere [usare il controllo degli accessi in base al ruolo per gestire i punti di ripristino di backup di Azure](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault).

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="for-soft-delete"></a>Per l'eliminazione temporanea

#### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>È necessario abilitare la funzionalità di eliminazione temporanea in ogni insieme di credenziali?

No, viene creato e abilitato per impostazione predefinita per tutti gli insiemi di credenziali dei servizi di ripristino.

#### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-delete-operation-is-complete"></a>È possibile configurare il numero di giorni per cui i dati verranno conservati nello stato di eliminazione temporanea dopo il completamento dell'operazione di eliminazione?

No, è stato fissato a 14 giorni di conservazione aggiuntiva dopo l'operazione di eliminazione.

#### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>È necessario pagare il costo per questa conservazione aggiuntiva di 14 giorni?

No, questo periodo di conservazione aggiuntivo di 14 giorni è gratuito come parte della funzionalità di eliminazione temporanea.

#### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>È possibile eseguire un'operazione di ripristino quando i dati sono in stato di eliminazione temporanea?

No, è necessario annullare l'eliminazione della risorsa eliminata temporaneamente per poter eseguire il ripristino. L'operazione di annullamento dell'eliminazione riporterà la risorsa nello **stato di arresto della protezione con Mantieni dati** in cui è possibile eseguire il ripristino in qualsiasi momento. Il Garbage Collector rimane in pausa in questo stato.

#### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>Gli snapshot seguiranno lo stesso ciclo di vita dei punti di ripristino nell'insieme di credenziali?

Sì.

#### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>Come è possibile attivare di nuovo i backup pianificati per una risorsa eliminata temporaneamente?

L'annullamento dell'eliminazione seguito dall'operazione di ripresa proteggerà di nuovo la risorsa. L'operazione di ripresa associa un criterio di backup per attivare i backup pianificati con il periodo di memorizzazione selezionato. Inoltre, il Garbage Collector viene eseguito non appena l'operazione di ripresa viene completata. Se si desidera eseguire un ripristino da un punto di ripristino che supera la data di scadenza, è consigliabile eseguire l'operazione prima di attivare l'operazione di ripresa.

#### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>È possibile eliminare l'insieme di credenziali se sono presenti elementi eliminati temporaneamente nell'insieme di credenziali?

Non è possibile eliminare l'insieme di credenziali di servizi di ripristino se sono presenti elementi di backup in stato di eliminazione temporanea nell'insieme di credenziali. Gli elementi eliminati temporaneamente vengono eliminati definitivamente 14 giorni dopo l'operazione di eliminazione. Se non è possibile attendere 14 giorni, [disabilitare l'eliminazione](#disabling-soft-delete)temporanea, annullare l'eliminazione degli elementi eliminati temporaneamente ed eliminarli di nuovo per eliminarli definitivamente. Dopo aver verificato che non siano presenti elementi protetti e che non siano presenti elementi eliminati temporaneamente, è possibile eliminare l'insieme di credenziali.  

#### <a name="can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>È possibile eliminare i dati prima del periodo di eliminazione temporanea di 14 giorni dopo l'eliminazione?

No. Non è possibile forzare l'eliminazione degli elementi eliminati temporaneamente, che verranno eliminati automaticamente dopo 14 giorni. Questa funzionalità di sicurezza è abilitata per salvaguardare i dati di backup da eliminazioni accidentali o dannose.  È necessario attendere 14 giorni prima di eseguire qualsiasi altra azione nella macchina virtuale.  Verranno addebitati gli elementi eliminati temporaneamente.  Se è necessario riproteggere le VM contrassegnate per l'eliminazione temporanea entro 14 giorni da un nuovo insieme di credenziali, contattare il supporto tecnico Microsoft.

#### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>È possibile eseguire operazioni di eliminazione temporanea in PowerShell o nell'interfaccia della riga di comando?

Le operazioni di eliminazione temporanea possono essere eseguite usando [PowerShell](#soft-delete-for-vms-using-azure-powershell). Attualmente, l'interfaccia della riga di comando non è supportata.

#### <a name="is-soft-delete-supported-for-other-cloud-workloads-like-sql-server-in-azure-vms-and-sap-hana-in-azure-vms"></a>L'eliminazione temporanea è supportata per altri carichi di lavoro cloud, ad esempio SQL Server in macchine virtuali di Azure e SAP HANA in macchine virtuali di Azure?

No. L'eliminazione temporanea è attualmente supportata solo per le macchine virtuali di Azure.

## <a name="next-steps"></a>Passaggi successivi

- Vedere informazioni sui [controlli di sicurezza per backup di Azure](backup-security-controls.md).
