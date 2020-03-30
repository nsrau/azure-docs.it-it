---
title: Funzionalità di sicurezza per proteggere i carichi di lavoro cloudSecurity features to help protect cloud workloads
description: Informazioni su come usare le funzionalità di sicurezza in Backup di Azure per rendere più sicuri i backup.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: 57c9fd76ae32aea49f480f2a88d8296538d8052d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156073"
---
# <a name="security-features-to-help-protect-cloud-workloads-that-use-azure-backup"></a>Funzionalità di sicurezza per proteggere i carichi di lavoro cloud che usano Backup di AzureSecurity features to help protect cloud workloads that use Azure Backup

Le preoccupazioni riguardo ai problemi di sicurezza, come malware, ransomware e intrusioni, aumentano continuamente. Questi problemi di sicurezza possono essere costosi in termini di denaro e di dati. Per proteggersi da tali attacchi, Backup di Azure offre ora funzionalità di sicurezza che consentono di proteggere i dati di backup anche dopo l'eliminazione.

Una di queste caratteristiche è l'eliminazione temporanea. Con l'eliminazione temporanea, anche se un attore malintenzionato elimina il backup di una macchina virtuale (o i dati di backup vengono eliminati accidentalmente), i dati di backup vengono conservati per 14 giorni aggiuntivi, consentendo il ripristino di tale elemento di backup senza perdita di dati. La conservazione dei dati di backup aggiuntivi di 14 giorni nello stato "eliminazione temporanea" non comporta alcun costo per il cliente. Azure crittografa anche tutti i dati di cui è stato eseguito il backup usando [Archiviazione Service Encryption](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) per proteggere ulteriormente i dati.

La protezione dell'eliminazione temporanea per le macchine virtuali di Azure è generalmente disponibile.

>[!NOTE]
>L'eliminazione temporanea per il server SQL nella macchina virtuale di Azure e l'eliminazione temporanea per SAP HANA nei carichi di lavoro della macchina virtuale di Azure sono ora disponibili in anteprima.<br>
>Per iscriverti all'anteprima, scrivici all'utenteAskAzureBackupTeam@microsoft.com

## <a name="soft-delete"></a>Eliminazione temporanea

### <a name="soft-delete-for-vms"></a>Eliminazione temporanea per le macchine virtualiSoft delete for VMs

L'eliminazione temporanea per le macchine virtuali protegge i backup delle macchine virtuali dall'eliminazione involontaria. Anche dopo l'eliminazione, i backup vengono mantenuti in stato di eliminazione temporanea per altri 14 giorni.

> [!NOTE]
> L'eliminazione temporanea protegge solo i dati di backup eliminati. Se una macchina virtuale viene eliminata senza un backup, la funzionalità di eliminazione temporanea non conserverà i dati. Tutte le risorse devono essere protette con Backup di Azure per garantire la piena resilienza.
>

### <a name="supported-regions"></a>Aree supportate

L'eliminazione temporanea è attualmente supportata negli Stati Uniti centro-occidentali, Asia orientale, Canada centrale, Canada orientale, Francia centrale, Francia Sud, Corea centrale, Corea del Sud, Regno Unito Sud, Regno Unito occidentale, Australia orientale, Australia Sud-est, Nord Europa, Stati Uniti occidentali, Stati Uniti occidentali2, Stati Uniti centrali, Sud Asia orientale, Stati Uniti centro-settentrionali, Stati Uniti centro-meridionali, Giappone orientale, Giappone occidentale, India meridionale, India centrale, India occidentale, Stati Uniti orientali 2, Svizzera settentrionale, Svizzera occidentale e tutte le regioni nazionali.

### <a name="soft-delete-for-vms-using-azure-portal"></a>Eliminazione temporanea per le macchine virtuali tramite il portale di AzureSoft delete for VMs using Azure portal

1. Per eliminare i dati di backup di una macchina virtuale, il backup deve essere interrotto. Nel portale di Azure passare all'insieme di credenziali dei servizi di ripristino, fare clic con il pulsante destro del mouse sull'elemento di backup e scegliere **Interrompi backup**.

   ![Schermata degli elementi di backup del portale di Azure](./media/backup-azure-security-feature-cloud/backup-stopped.png)

2. Nella finestra seguente verrà data la possibilità di eliminare o conservare i dati di backup. Se si sceglie **Elimina dati di backup** e quindi Interrompi **backup**, il backup della macchina virtuale non verrà eliminato definitivamente. Piuttosto, i dati di backup verranno conservati per 14 giorni nello stato eliminato temporaneamente. Se si preda **Elimina dati di backup,** viene inviato un avviso di eliminazione e-mail all'ID di posta elettronica configurato per informare l'utente che rimangono 14 giorni di conservazione estesa per i dati di backup. Inoltre, un avviso e-mail viene inviato il 12 giorno informando che ci sono altri due giorni per resuscitare i dati cancellati. L'eliminazione viene rinviata fino al quindicesimo giorno, quando si verificherà l'eliminazione permanente e verrà inviato un avviso e-mail finale che informa sulla cancellazione permanente dei dati.

   ![Screenshot del portale di Azure, schermata Interrompi backup](./media/backup-azure-security-feature-cloud/delete-backup-data.png)

3. Durante questi 14 giorni, nell'insieme di credenziali dei servizi di ripristino, la macchina virtuale eliminata temporaneamente verrà visualizzata con un'icona rossa "soft-delete" accanto.

   ![Screenshot del portale di Azure, della macchina virtuale in stato di eliminazione temporanea](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

   > [!NOTE]
   > Se nell'insieme di credenziali sono presenti elementi di backup eliminati temporaneamente, l'insieme di credenziali non può essere eliminato in quel momento. Provare l'eliminazione dell'insieme di credenziali dopo che gli elementi di backup sono stati eliminati definitivamente e non è rimasto alcun elemento eliminato temporaneamente nell'insieme di credenziali.

4. Per ripristinare la macchina virtuale eliminata in modo reato, è necessario prima annullarne l'eliminazione. Per annullare l'eliminazione, scegliere la macchina virtuale eliminata in modo restatunitense e quindi selezionare l'opzione **Annulla eliminazione**.

   ![Screenshot del portale di Azure, Annullamento eliminazione macchina virtuale](./media/backup-azure-security-feature-cloud/choose-undelete.png)

   Verrà visualizzata una finestra che avverte che se si seleziona l'annullamento dell'eliminazione, tutti i punti di ripristino per la macchina virtuale verranno annullati e saranno disponibili per l'esecuzione di un'operazione di ripristino. La macchina virtuale verrà mantenuta in uno stato di "stop protection con retain data" con i backup sospesi e i dati di backup conservati per sempre senza alcun criterio di backup efficace.

   ![Screenshot del portale di Azure, Conferma annullamento eliminazione macchina virtuale](./media/backup-azure-security-feature-cloud/undelete-vm.png)

   A questo punto, è anche possibile ripristinare la macchina virtuale selezionando **Ripristina macchina virtuale** dal punto di ripristino scelto.  

   ![Screenshot del portale di Azure, opzione Ripristina macchina virtuale](./media/backup-azure-security-feature-cloud/restore-vm.png)

   > [!NOTE]
   > Il Garbage Collector verrà eseguito e pulirà i punti di ripristino scaduti solo dopo che l'utente eseguirà l'operazione **di backup Di riprendi.**

5. Al termine del processo di annullamento dell'eliminazione, lo stato tornerà a "Interrompi backup con conservazione dati" e sarà possibile scegliere **Riprendi backup**. L'operazione **Riprendi backup** riporta l'elemento di backup nello stato attivo, associato a un criterio di backup selezionato dall'utente che definisce le pianificazioni di backup e conservazione.

   ![Screenshot del portale di Azure, opzione Riprendi backup](./media/backup-azure-security-feature-cloud/resume-backup.png)

Questo diagramma di flusso mostra i diversi passaggi e stati di un elemento di backup quando l'eliminazione temporanea è abilitata:This flow chart shows the different steps and states of a backup item when Soft Delete is enabled:

![Ciclo di vita dell'elemento di backup eliminato temporaneamente](./media/backup-azure-security-feature-cloud/lifecycle.png)

Per ulteriori informazioni, vedere la sezione [Domande frequenti](backup-azure-security-feature-cloud.md#frequently-asked-questions) di seguito.

### <a name="soft-delete-for-vms-using-azure-powershell"></a>Eliminazione temporanea per le macchine virtuali tramite Azure PowerShellSoft delete for VMs using Azure PowerShell

> [!IMPORTANT]
> La versione di Az.RecoveryServices necessaria per usare l'eliminazione temporanea tramite Azure PS è min 2.2.0.The Az.RecoveryServices version required to use soft-delete using Azure PS is min 2.2.0. Utilizzare ```Install-Module -Name Az.RecoveryServices -Force``` per ottenere la versione più recente.

Come descritto in precedenza per il portale di Azure, la sequenza di passaggi è la stessa durante l'uso di Azure PowerShell.

#### <a name="delete-the-backup-item-using-azure-powershell"></a>Eliminare l'elemento di backup tramite Azure PowerShellDelete the backup item using Azure PowerShell

Eliminare l'elemento di backup utilizzando il cmdlet [Disable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

L'elemento 'DeleteState' dell'elemento di backup cambierà da 'NotDeleted' a 'ToBeDeleted'. I dati di backup verranno conservati per 14 giorni. Se si desidera annullare l'operazione di eliminazione, deve essere eseguita l'operazione di annullamento-eliminazione.

#### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>Annullamento dell'operazione di eliminazione tramite Azure PowerShellUndoing the deletion operation using Azure PowerShell

In primo luogo, recuperare l'elemento di backup pertinente che si trova in stato di eliminazione temporanea (vale a dire, sta per essere eliminato).

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

Eseguire quindi l'operazione di annullamento dell'eliminazione utilizzando il cmdlet [Undo-AzRecoveryServicesBackupItemDeletion](https://docs.microsoft.com/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion?view=azps-3.1.0) PS.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

Il 'DeleteState' dell'elemento di backup verrà ripristinato a 'NotDeleted'. Ma la protezione è ancora interrotta. [Riprendere il backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#change-policy-for-backup-items) per riattivare la protezione.

### <a name="soft-delete-for-vms-using-rest-api"></a>Eliminazione temporanea per le macchine virtuali tramite l'API RESTSoft delete for VMs using REST API

- Eliminare i backup utilizzando l'API REST come indicato [di seguito](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data).
- Se l'utente desidera annullare queste operazioni di eliminazione, fare riferimento alla procedura descritta [di seguito](backup-azure-arm-userestapi-backupazurevms.md#undo-the-stop-protection-and-delete-data).

## <a name="disabling-soft-delete"></a>Disattivazione dell'eliminazione temporanea

L'eliminazione temporanea è abilitata per impostazione predefinita sui vault appena creati per proteggere i dati di backup da eliminazioni accidentali o dannose.  La disattivazione di questa funzionalità non è consigliata. L'unica circostanza in cui è consigliabile disabilitare l'eliminazione temporanea è se si prevede di spostare gli elementi protetti in un nuovo insieme di credenziali e non è possibile attendere i 14 giorni necessari prima dell'eliminazione e della rimozione, ad esempio in un ambiente di test. Solo il proprietario del vault può disattivare questa funzione. Se si disabilita questa funzionalità, tutte le eliminazioni future di elementi protetti comporteranno la rimozione immediata, senza la possibilità di ripristino. I dati di backup presenti in stato di eliminazione temporanea prima di disabilitare questa funzionalità rimarranno in stato di eliminazione temporanea per il periodo di 14 giorni. Se si desidera eliminare definitivamente questi immediatamente, allora è necessario annullare l'eliminazione ed eliminarli di nuovo per ottenere eliminato definitivamente.

### <a name="disabling-soft-delete-using-azure-portal"></a>Disabilitazione dell'eliminazione temporanea tramite il portale di AzureDisabling soft delete using Azure portal

Per disattivare l'eliminazione temporanea, attenersi alla seguente procedura:

1. Nel portale di Azure passare all'insieme di credenziali e quindi a**Proprietà** **impostazioni.** -> 
2. Nel riquadro delle proprietà selezionare**Aggiornamento** **impostazioni** -> protezione .  
3. Nel riquadro delle impostazioni di protezione, in **Eliminazione temporanea**, selezionare **Disabilita**.

![Disabilitare l'eliminazione temporanea](./media/backup-azure-security-feature-cloud/disable-soft-delete.png)

### <a name="disabling-soft-delete-using-azure-powershell"></a>Disabilitazione dell'eliminazione temporanea tramite Azure PowerShellDisabling soft delete using Azure PowerShell

> [!IMPORTANT]
> La versione di Az.RecoveryServices necessaria per usare l'eliminazione temporanea tramite Azure PS è min 2.2.0.The Az.RecoveryServices version required to use soft-delete using Azure PS is min 2.2.0. Utilizzare ```Install-Module -Name Az.RecoveryServices -Force``` per ottenere la versione più recente.

Per disabilitare, utilizzare il cmdlet [Set-AzRecoveryServicesVaultBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty?view=azps-3.1.0) PS.

```powershell
Set-AzRecoveryServicesVaultProperty -VaultId $myVaultID -SoftDeleteFeatureState Disable


StorageModelType       :
StorageType            :
StorageTypeState       :
EnhancedSecurityState  : Enabled
SoftDeleteFeatureState : Disabled
```

### <a name="disabling-soft-delete-using-rest-api"></a>Disabilitazione dell'eliminazione temporanea tramite l'API RESTDisabling soft delete using REST API

Per disabilitare la funzionalità di eliminazione temporanea tramite l'API REST, fare riferimento alla procedura descritta [di seguito.](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api)

## <a name="permanently-deleting-soft-deleted-backup-items"></a>Eliminazione definitiva degli elementi di backup eliminati temporaneamente

I dati di backup in stato di eliminazione temporanea prima della disabilitazione di questa funzionalità rimarranno in stato di eliminazione temporanea. Se si desidera eliminare definitivamente questi immediatamente, quindi annullare l'eliminazione ed eliminarli nuovamente per ottenere eliminato definitivamente.

### <a name="using-azure-portal"></a>Uso del portale di Azure

A tale scopo, seguire questa procedura:

1. Attenersi alla procedura per [disabilitare l'eliminazione temporanea](#disabling-soft-delete).
2. Nel portale di Azure passare all'insieme di credenziali, passare a Elementi di **backup**e scegliere la macchina virtuale eliminata temporaneamente.

   ![Scegliere la macchina virtuale eliminata temporaneamenteChoose soft deleted VM](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

3. Selezionare l'opzione **Annulla eliminazione**.

   ![Scegliere Annulla eliminazione](./media/backup-azure-security-feature-cloud/choose-undelete.png)

4. Apparirà una finestra. Selezionare **Annulla eliminazione**.

   ![Selezionare Annulla eliminazione](./media/backup-azure-security-feature-cloud/undelete-vm.png)

5. Scegliere **Elimina dati di backup** per eliminare definitivamente i dati di backup.

   ![Scegliere Elimina dati di backup](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-buttom.png)

6. Digitare il nome dell'elemento di backup per confermare che si desidera eliminare i punti di ripristino.

   ![Digitare il nome dell'elemento di backup](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-data1.png)

7. Per eliminare i dati di backup per l'elemento, selezionare **Elimina**. Un messaggio di notifica indica che i dati di backup sono stati eliminati.

### <a name="using-azure-powershell"></a>Uso di Azure PowerShell

Se gli elementi sono stati eliminati prima della disabilitazione dell'eliminazione temporanea, si troveranno in uno stato di eliminazione temporanea. Per eliminarli immediatamente, l'operazione di eliminazione deve essere invertita e quindi eseguita di nuovo.

Identificare gli elementi che si trovano in stato di eliminazione temporanea.

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

Quindi invertire l'operazione di eliminazione che è stata eseguita quando è stata abilitata l'eliminazione temporanea.

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

Se gli elementi sono stati eliminati prima della disabilitazione dell'eliminazione temporanea, si troveranno in uno stato di eliminazione temporanea. Per eliminarli immediatamente, l'operazione di eliminazione deve essere invertita e quindi eseguita di nuovo.

1. Innanzitutto, annullare le operazioni di eliminazione con i passaggi indicati [qui](backup-azure-arm-userestapi-backupazurevms.md#undo-the-stop-protection-and-delete-data).
2. Quindi disabilitare la funzionalità di eliminazione temporanea utilizzando l'API REST utilizzando la procedura descritta [di seguito](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api).
3. Quindi eliminare i backup utilizzando l'API REST come indicato [qui](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data).

## <a name="encryption"></a>Crittografia

Tutti i dati di cui è stato eseguito il backup vengono crittografati automaticamente quando vengono archiviati nel cloud usando la crittografia di Archiviazione di Azure, che consente di soddisfare gli impegni di sicurezza e conformità. Questi dati inattivi vengono crittografati utilizzando la crittografia AES a 256 bit, una delle più forti crittografie a blocchi disponibili, ed è conforme a FIPS 140-2.

Oltre alla crittografia inattivi, tutti i dati di backup in transito vengono trasferiti tramite HTTPS. Rimane sempre nella rete backbone di Azure.It always remains on the Azure backbone network.

Per altre informazioni, vedere Crittografia di Archiviazione di Azure per i [dati inattivi](https://docs.microsoft.com/azure/storage/common/storage-service-encryption). Fare riferimento alle [domande frequenti](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq#encryption) su Backup di Azure per rispondere a tutte le domande sulla crittografia.

### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>Crittografia dei dati di backup utilizzando chiavi gestite dalla piattaforma

Per impostazione predefinita, tutti i dati vengono crittografati utilizzando chiavi gestite dalla piattaforma. Non è necessario intraprendere alcuna azione esplicita da parte dell'utente per abilitare questa crittografia e si applica a tutti i carichi di lavoro di cui viene eseguito il backup nell'insieme di credenziali di Servizi di ripristino.

### <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Crittografia dei dati di backup utilizzando chiavi gestite dal cliente

Quando si esegue il backup delle macchine virtuali di Azure, è ora possibile crittografare i dati usando chiavi di proprietà e gestite dall'utente. Backup di Azure consente di usare le chiavi RSA archiviate nell'insieme di credenziali delle chiavi di Azure per crittografare i backup. La chiave di crittografia utilizzata per crittografare i backup può essere diversa da quella utilizzata per l'origine. I dati sono protetti utilizzando una chiave deK (Data Encryption Key) basata su AES 256, che a sua volta è protetta utilizzando le chiavi. Questo ti dà il pieno controllo sui dati e le chiavi. To allow encryption, it's required that the Recovery Services vault be granted access to the encryption key in the Azure Key Vault. È possibile disabilitare la chiave o revocare l'accesso quando necessario. Tuttavia, è necessario abilitare la crittografia utilizzando le chiavi prima di tentare di proteggere gli elementi nel vault.

>[!NOTE]
>Questa funzionalità è attualmente a disponibilità limitata. Si prega di compilare AskAzureBackupTeam@microsoft.com [questo sondaggio](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapURE9TTDRIUEUyNFhNT1lZS1BNVDdZVllHWi4u) e inviarci un'e-mail a se si desidera crittografare i dati di backup utilizzando le chiavi gestite dal cliente. Si noti che la possibilità di usare questa funzionalità è soggetta all'approvazione del servizio Backup di Azure.Note that the ability to use this feature is subject to approval from the Azure Backup service.

### <a name="backup-of-managed-disk-vms-encrypted-using-customer-managed-keys"></a>Backup di macchine virtuali del disco gestito crittografate con chiavi gestite dal cliente

Backup di Azure consente inoltre di eseguire il backup delle macchine virtuali di Azure che usano la chiave per la crittografia lato server. La chiave usata per crittografare i dischi viene archiviata nell'insieme di credenziali delle chiavi di Azure e gestita dall'utente. La crittografia lato server che usa chiavi gestite dal cliente differisce da Crittografia disco di Azure, poiché ADE sfrutta BitLocker (per Windows) e DM-Crypt (per Linux) per eseguire la crittografia in-guest, SSE crittografa i dati nel servizio di archiviazione, consentendo di usare qualsiasi sistema operativo o immagini per le macchine virtuali. Per ulteriori dettagli, vedere [Crittografia dei dischi gestiti con chiavi gestite dal cliente.](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#customer-managed-keys)

### <a name="backup-of-vms-encrypted-using-ade"></a>Backup di macchine virtuali crittografate tramite ADE

Con Backup di Azure è anche possibile eseguire il backup delle macchine virtuali di Azure con il sistema operativo o i dischi dati crittografati usando Crittografia disco di Azure.With Azure Backup, you can also back up your Azure Virtual machines that have their OS or data disks encrypted using Azure Disk Encryption. ADE utilizza BitLocker per le macchine virtuali Windows e DM-Crypt per le macchine virtuali Linux per eseguire la crittografia in-guest. Per informazioni dettagliate, vedere [Eseguire il backup e il ripristino di macchine virtuali crittografate con Backup di Azure.](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)

## <a name="private-endpoints"></a>Endpoint privati

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

## <a name="other-security-features"></a>Altre funzioni di sicurezza

### <a name="protection-of-azure-backup-recovery-points"></a>Protezione dei punti di ripristino di Backup di AzureProtection of Azure Backup recovery points

Gli account di archiviazione utilizzati dagli insiemi di credenziali dei servizi di ripristino sono isolati e non sono accessibili agli utenti per scopi dannosi. L'accesso è consentito solo tramite le operazioni di gestione di Backup di Azure, ad esempio il ripristino. Queste operazioni di gestione vengono controllate tramite il controllo degli accessi in base al ruolo.

Per altre informazioni, vedere Usare il controllo degli accessi in base al ruolo per gestire i punti di ripristino di Backup di Azure.For more information, see [Use Role-Based Access Control to manage Azure Backup recovery points.](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault)

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="for-soft-delete"></a>Per Eliminazione temporanea

#### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>È necessario abilitare la funzione di eliminazione temporanea in ogni vault?

No, è costruito e abilitato per impostazione predefinita per tutti gli insiemi di credenziali dei servizi di ripristino.

#### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-delete-operation-is-complete"></a>È possibile configurare il numero di giorni per cui i dati verranno conservati in stato di eliminazione temporanea al termine dell'operazione di eliminazione?

No, è fissato a 14 giorni di conservazione aggiuntiva dopo l'operazione di eliminazione.

#### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>Devo pagare il costo per questa ritenuta aggiuntiva di 14 giorni?

No, questa conservazione aggiuntiva di 14 giorni è gratuita come parte della funzionalità di eliminazione temporanea.

#### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>È possibile eseguire un'operazione di ripristino quando i dati sono in stato di eliminazione temporanea?

No, è necessario annullare l'eliminazione della risorsa eliminata temporaneamente per il ripristino. L'operazione di annullamento dell'eliminazione riporterà la risorsa nella **protezione Stop con mantenere lo stato** dei dati in cui è possibile eseguire il ripristino in qualsiasi momento. Garbage Collector rimane in pausa in questo stato.

#### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>Gli snapshot seguiranno lo stesso ciclo di vita dei punti di ripristino nell'insieme di credenziali?

Sì.

#### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>Come è possibile attivare nuovamente i backup pianificati per una risorsa eliminata in modo resistativo?

L'annullamento dell'eliminazione seguito dall'operazione di ripresa proteggerà nuovamente la risorsa. L'operazione di ripristino associa un criterio di backup per attivare i backup pianificati con il periodo di conservazione selezionato. Inoltre, il Garbage Collector viene eseguito non appena l'operazione di ripresa viene completata. Se si desidera eseguire un ripristino da un punto di ripristino che supera la data di scadenza, è consigliabile eseguire questa operazione prima di attivare l'operazione di ripresa.

#### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>È possibile eliminare il vault se nel vault sono presenti elementi eliminati in modo remunerato?

L'insieme di credenziali dei servizi di ripristino non può essere eliminato se sono presenti elementi di backup in stato di eliminazione temporanea nell'insieme di credenziali. Gli elementi eliminati temporaneamente vengono eliminati definitivamente 14 giorni dopo l'operazione di eliminazione. Se non è possibile attendere 14 giorni, [disabilitare l'eliminazione temporanea](#disabling-soft-delete), annullare l'eliminazione degli elementi eliminati temporaneamente ed eliminarli nuovamente in modo permanente. Dopo aver verificato che non vi siano elementi protetti e elementi eliminati temporaneamente, il vault può essere eliminato.  

#### <a name="can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>È possibile eliminare i dati precedenti al periodo di eliminazione temporanea dei 14 giorni dopo l'eliminazione?

No. Non è possibile forzare l'eliminazione degli elementi eliminati temporaneamente, che vengono eliminati automaticamente dopo 14 giorni. Questa funzionalità di sicurezza è abilitata per proteggere i dati di cui è stato eseguito il backup da eliminazioni accidentali o dannose.  È consigliabile attendere 14 giorni prima di eseguire qualsiasi altra azione nella macchina virtuale.  Gli oggetti eliminati temporaneamente non verranno addebitati.  Se è necessario rimuovere nuovamente la protezione delle macchine virtuali contrassegnate per l'eliminazione temporanea entro 14 giorni per un nuovo insieme di credenziali, contattare il supporto tecnico Microsoft.If you need reprotecting the VMs marked for soft-delete within 14 days to a new vault, then contact Microsoft support.

#### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>È possibile eseguire operazioni di eliminazione temporanea in PowerShell o CLI?

Le operazioni di eliminazione temporanea possono essere eseguite tramite [PowerShell](#soft-delete-for-vms-using-azure-powershell). Attualmente, l'interfaccia della riga di comando non è supportata.

#### <a name="is-soft-delete-supported-for-other-cloud-workloads-like-sql-server-in-azure-vms-and-sap-hana-in-azure-vms"></a>L'eliminazione temporanea è supportata per altri carichi di lavoro cloud, ad esempio SQL Server nelle macchine virtuali di Azure e SAP HANA nelle macchine virtuali di Azure?

No. Attualmente l'eliminazione temporanea è supportata solo per le macchine virtuali di Azure.Currently soft delete is only supported for Azure virtual machines.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni, vedere [Controlli di sicurezza per Backup di Azure](backup-security-controls.md).
