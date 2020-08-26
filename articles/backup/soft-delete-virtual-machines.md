---
title: Eliminazione temporanea per le macchine virtuali
description: Informazioni sul modo in cui l'eliminazione temporanea per le macchine virtuali rende più sicuri i backup.
ms.topic: conceptual
ms.date: 04/30/2020
ms.custom: references_regions
ms.openlocfilehash: a79f2bf77bcc184db8ac61b51107c1bb4a2a989d
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88889827"
---
# <a name="soft-delete-for-virtual-machines"></a>Eliminazione temporanea per le macchine virtuali

L'eliminazione temporanea per le VM protegge i backup delle macchine virtuali da eliminazioni indesiderate. Anche dopo l'eliminazione, i backup vengono conservati nello stato di eliminazione temporanea per 14 giorni aggiuntivi.

> [!NOTE]
> L'eliminazione temporanea protegge solo i dati di backup eliminati. Se una macchina virtuale viene eliminata senza un backup, la funzionalità di eliminazione temporanea non manterrà i dati. Tutte le risorse devono essere protette con backup di Azure per garantire la resilienza completa.
>

## <a name="supported-regions"></a>Aree supportate

L'eliminazione temporanea è attualmente supportata negli Stati Uniti centro-occidentali, Asia orientale, Canada centrale, Canada orientale, Francia centrale, Francia meridionale, Corea centrale, Corea meridionale, Regno Unito meridionale, Regno Unito occidentale, Australia orientale, Australia sud-orientale, Europa settentrionale, Stati Uniti occidentali, Stati Uniti centro-occidentali, Stati Uniti centro-meridionali, Stati Uniti centro-settentrionali, Stati Uniti centro Asia orientale-occidentali, Giappone orientale, Giappone occidentale, India meridionale, India centrale, India occidentale , Svizzera settentrionale, Svizzera occidentale, Norvegia occidentale, Norvegia orientale e tutte le aree nazionali.

## <a name="soft-delete-for-vms-using-azure-portal"></a>Eliminazione temporanea per le macchine virtuali con portale di Azure

1. Per eliminare i dati di backup di una macchina virtuale, è necessario arrestare il backup. Nel portale di Azure passare all'insieme di credenziali di servizi di ripristino, fare clic con il pulsante destro del mouse sull'elemento di backup e scegliere **Interrompi backup**.

   ![Screenshot degli elementi di backup portale di Azure](./media/backup-azure-security-feature-cloud/backup-stopped.png)

2. Nella finestra seguente verrà offerta la possibilità di eliminare o mantenere i dati di backup. Se si sceglie **Elimina dati di backup** e quindi **Interrompi backup**, il backup della macchina virtuale non verrà eliminato definitivamente. I dati di backup verranno invece conservati per 14 giorni nello stato di eliminazione temporanea. Se si sceglie **Elimina dati di backup** , viene inviato un avviso di eliminazione tramite posta elettronica all'ID di posta elettronica configurato, che informa l'utente che rimane un periodo di conservazione esteso per i dati di backup di 14 giorni. Viene inoltre inviato un avviso di posta elettronica il giorno 12 per informare che ci sono altri due giorni rimasti per resuscitare i dati eliminati. L'eliminazione viene posticipata fino al 15 ° giorno, quando si verifica l'eliminazione permanente e viene inviato un avviso di posta elettronica finale che informa sull'eliminazione permanente dei dati.

   ![Screenshot della schermata portale di Azure, Interrompi backup](./media/backup-azure-security-feature-cloud/delete-backup-data.png)

3. Durante questi 14 giorni, nell'insieme di credenziali di servizi di ripristino, la macchina virtuale eliminata temporaneamente verrà visualizzata con un'icona "soft-delete" rossa accanto.

   ![Screenshot di portale di Azure, VM in stato di eliminazione temporanea](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

   > [!NOTE]
   > Se nell'insieme di credenziali sono presenti elementi di backup eliminati temporaneamente, non è possibile eliminare l'insieme di credenziali in quel momento. Provare a eliminare l'insieme di credenziali dopo che gli elementi di backup sono stati eliminati definitivamente e non ci sono elementi in stato di eliminazione temporanea rimasti nell'insieme di credenziali.

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

## <a name="soft-delete-for-vms-using-azure-powershell"></a>Eliminazione temporanea per le macchine virtuali con Azure PowerShell

> [!IMPORTANT]
> La versione AZ. RecoveryServices necessaria per usare l'eliminazione temporanea con Azure PowerShell è la 2.2.0 minima. Usare ```Install-Module -Name Az.RecoveryServices -Force``` per ottenere la versione più recente.

Come descritto in precedenza per portale di Azure, la sequenza dei passaggi è identica anche quando si usa Azure PowerShell.

### <a name="delete-the-backup-item-using-azure-powershell"></a>Eliminare l'elemento di backup usando Azure PowerShell

Eliminare l'elemento di backup usando il cmdlet [Disable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) di PowerShell.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

Il ' DeleteState ' dell'elemento di backup cambierà da' NotDeleted ' a' ToBeDeleted '. I dati di backup verranno conservati per 14 giorni. Se si desidera annullare l'operazione di eliminazione, è necessario eseguire l'annullamento dell'eliminazione.

### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>Esecuzione dell'operazione di eliminazione utilizzando Azure PowerShell

Per prima cosa, recuperare l'elemento di backup pertinente che si trova nello stato di eliminazione temporanea, ovvero sta per essere eliminato.

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

Eseguire quindi l'operazione di annullamento dell'eliminazione usando il cmdlet di PowerShell [Undo-AzRecoveryServicesBackupItemDeletion](/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion) .

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

Il ' DeleteState ' dell'elemento di backup verrà ripristinato in ' NotDeleted '. Ma la protezione è ancora stata arrestata. [Riprendere il backup](./backup-azure-vms-automation.md#change-policy-for-backup-items) per abilitare di nuovo la protezione.

## <a name="soft-delete-for-vms-using-rest-api"></a>Eliminazione temporanea per le macchine virtuali con l'API REST

- Eliminare i backup usando l'API REST come indicato [qui](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data).
- Se si desidera annullare queste operazioni di eliminazione, fare riferimento ai passaggi indicati [qui](backup-azure-arm-userestapi-backupazurevms.md#undo-the-deletion).

## <a name="how-to-disable-soft-delete"></a>Come disabilitare l'eliminazione temporanea

La disabilitazione di questa funzionalità non è consigliata. L'unica circostanza in cui è consigliabile disabilitare l'eliminazione temporanea è se si prevede di trasferire gli elementi protetti in un nuovo insieme di credenziali e non è possibile attendere i 14 giorni necessari prima dell'eliminazione e della riprotezione (ad esempio in un ambiente di test). Per istruzioni su come disabilitare l'eliminazione temporanea, vedere [Abilitazione e disabilitazione dell'eliminazione](backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete)temporanea.

## <a name="next-steps"></a>Passaggi successivi

- Leggi le [domande frequenti](backup-azure-security-feature-cloud.md#frequently-asked-questions) sull'eliminazione temporanea
- Leggere le informazioni su tutte le [funzionalità di sicurezza di backup di Azure](security-overview.md)
