---
title: Eliminazione temporanea per SQL Server in una macchina virtuale di Azure e SAP HANA nei carichi di lavoro delle macchine virtuali di Azure
description: Informazioni su come l'eliminazione temporanea per SQL Server in macchine virtuali di Azure e SAP HANA nei carichi di lavoro delle macchine virtuali di Azure rende più sicuri i backup.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 26525ec758b3a27d6e0e1b9754b11041bd1fa0d2
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "89022293"
---
# <a name="soft-delete-for-sql-server-in-azure-vm-and-sap-hana-in-azure-vm-workloads"></a>Eliminazione temporanea per SQL Server in una macchina virtuale di Azure e SAP HANA nei carichi di lavoro delle macchine virtuali di Azure

Backup di Azure ora fornisce l'eliminazione temporanea per SQL Server in una macchina virtuale di Azure e SAP HANA nei carichi di lavoro delle macchine virtuali di Azure. Questa funzionalità è aggiunta allo scenario di eliminazione temporanea di [macchine virtuali di Azure](soft-delete-virtual-machines.md)già supportato.

L' [eliminazione](backup-azure-security-feature-cloud.md) temporanea è una funzionalità di sicurezza che consente di proteggere i dati di backup anche dopo l'eliminazione. Con l'eliminazione temporanea, anche se un attore malintenzionato Elimina il backup di un database (o i dati di backup vengono accidentalmente eliminati), i dati di backup vengono conservati per 14 giorni aggiuntivi. In questo modo è possibile recuperare l'elemento di backup senza perdita di dati. Questa conservazione aggiuntiva di 14 giorni dei dati di backup nello stato "eliminazione temporanea" non comporta alcun costo per il cliente.

>[!NOTE]
>Una volta abilitata l'anteprima per una sottoscrizione, non è possibile disabilitare l'eliminazione temporanea solo per SQL Server o SAP HANA database e mantenerlo abilitato per le macchine virtuali nello stesso insieme di credenziali. È possibile creare insiemi di credenziali separati per il controllo granulare.

## <a name="steps-to-enroll-in-preview"></a>Passaggi per la registrazione in anteprima

1. Accedere al proprio account Azure.

   ```powershell
   Login-AzureRmAccount
   ```

2. Selezionare la sottoscrizione che si vuole registrare nell'anteprima:

   ```powershell
   Get-AzureRmSubscription –SubscriptionName "Subscription Name" | Select-AzureRmSubscription
   ```

3. Registra questa sottoscrizione al programma di anteprima:

   ```powershell
   Register-AzureRMProviderFeature -FeatureName WorkloadBackupSoftDelete -ProviderNamespace Microsoft.RecoveryServices
   ```

4. Attendere 30 minuti per la registrazione della sottoscrizione nell'anteprima.

5. Per controllare lo stato, eseguire i cmdlet seguenti:

   ```powershell
   Get-AzureRmProviderFeature -FeatureName WorkloadBackupSoftDelete -ProviderNamespace Microsoft.RecoveryServices
   ```

6. Quando la sottoscrizione viene visualizzata come registrata, eseguire il comando seguente:

   ```powershell
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices
   ```

>[!NOTE]
>Ogni volta che viene creato un nuovo insieme di credenziali/insiemi di credenziali nella sottoscrizione con eliminazione temporanea abilitata, è necessario eseguire di nuovo il comando seguente per abilitare la funzionalità per gli insiemi di credenziali appena creati.<BR>
> `Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices`

## <a name="soft-delete-for-sql-server-in-azure-vm-using-azure-portal"></a>Eliminazione temporanea per SQL Server in una macchina virtuale di Azure con portale di Azure

>[!NOTE]
>Queste istruzioni si applicano anche a SAP HANA in una macchina virtuale di Azure.

1. Per eliminare i dati di backup di un database in SQL Server, è necessario arrestare il backup. Nel portale di Azure passare all'insieme di credenziali di servizi di ripristino, passare all'elemento di backup e scegliere **Interrompi backup**.

   ![Interrompi backup](./media/soft-delete-sql-saphana-in-azure-vm/stop-backup.png)

2. Nella finestra seguente verrà offerta la possibilità di eliminare o mantenere i dati di backup. Se si sceglie **Elimina dati di backup**, il backup del database non verrà eliminato definitivamente. I dati di backup verranno invece conservati per 14 giorni nello stato di eliminazione temporanea. L'eliminazione viene posticipata fino al 15 ° giorno con messaggi di posta elettronica di avviso regolari il primo, il dodicesimo e il 15 ° giorno per informare l'utente sullo stato di backup del database.

   ![Elimina dati di backup](./media/soft-delete-sql-saphana-in-azure-vm/delete-backup-data.png)

3. Durante questi 14 giorni, nell'insieme di credenziali di servizi di ripristino, l'elemento eliminato temporaneamente verrà visualizzato con un'icona "soft-delete" rossa accanto.

   ![Elementi eliminati temporaneamente](./media/soft-delete-sql-saphana-in-azure-vm/soft-deleted-items.png)

4. Per ripristinare il database eliminato temporaneamente, è prima necessario annullarne l'eliminazione. Per annullare l'eliminazione, scegliere il database eliminato temporaneamente, quindi selezionare l'opzione **Annulla eliminazione**.

   ![Annulla eliminazione database](./media/soft-delete-sql-saphana-in-azure-vm/undelete-database.png)

   Verrà visualizzata una finestra con un avviso che indica che se si sceglie Annulla eliminazione, tutti i punti di ripristino per il database verranno annullati e resi disponibili per l'esecuzione di un'operazione di ripristino. L'elemento di backup verrà mantenuto in uno stato "Arresta protezione dati con Mantieni dati" con i backup sospesi e i dati di backup mantenuti per sempre senza alcun criterio di backup valido.

   ![Annulla eliminazione avviso](./media/soft-delete-sql-saphana-in-azure-vm/undelete-warning.png)

5. A questo punto, è anche possibile ripristinare i dati selezionando **Ripristina** per l'elemento di backup eliminato temporaneamente scelto.

   ![Ripristino della VM](./media/soft-delete-sql-saphana-in-azure-vm/restore-vm.png)

6. Al termine del processo di annullamento dell'eliminazione, lo stato tornerà a "Interrompi backup con Mantieni dati", quindi è possibile scegliere **Riprendi backup**. L'operazione di **ripresa del backup** riporta l'elemento di backup nello stato attivo, associato a un criterio di backup selezionato dall'utente che definisce le pianificazioni di backup e conservazione.

   ![Riprendi backup](./media/soft-delete-sql-saphana-in-azure-vm/resume-backup.png)

## <a name="soft-delete-for-sql-server-in-vm-using-azure-powershell"></a>Eliminazione temporanea per SQL Server nella macchina virtuale con Azure PowerShell

>[!NOTE]
>La versione AZ. RecoveryServices necessaria per usare l'eliminazione temporanea con Azure PowerShell è la 2.2.0 minima. Usare `Install-Module -Name Az.RecoveryServices -Force` per ottenere la versione più recente.

La sequenza di passaggi per l'utilizzo di Azure PowerShell è identica a quella del portale di Azure, descritta in precedenza.

### <a name="delete-the-backup-item-using-azure-powershell"></a>Eliminare l'elemento di backup usando Azure PowerShell

Eliminare l'elemento di backup usando il cmdlet [Disable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) di PowerShell.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force
```

Il **DeleteState** dell'elemento di backup cambierà da **NotDeleted** a **ToBeDeleted**. I dati di backup verranno conservati per 14 giorni. Se si desidera annullare l'operazione di eliminazione, è necessario eseguire l'annullamento dell'eliminazione.

### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>Esecuzione dell'operazione di eliminazione utilizzando Azure PowerShell

Prima di tutto, recuperare l'elemento di backup pertinente che si trova nello stato di eliminazione temporanea, ovvero sta per essere eliminato.

```powershell
Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType SQLDataBase -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType SQLDataBase -VaultId $myVaultID -Name AppVM1
```

Eseguire quindi l'operazione di annullamento dell'eliminazione usando il cmdlet di PowerShell [Undo-AzRecoveryServicesBackupItemDeletion](/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion) .

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force
```

Il **DeleteState** dell'elemento di backup verrà ripristinato in **NotDeleted**. Ma la protezione è ancora stata arrestata. Riprendere il backup per abilitare di nuovo la protezione.

## <a name="how-to-disable-soft-delete"></a>Come disabilitare l'eliminazione temporanea

La disabilitazione di questa funzionalità non è consigliata. L'unica circostanza in cui è consigliabile disabilitare l'eliminazione temporanea è se si prevede di trasferire gli elementi protetti in un nuovo insieme di credenziali e non è possibile attendere i 14 giorni necessari prima dell'eliminazione e della riprotezione (ad esempio in un ambiente di test). Per istruzioni su come disabilitare l'eliminazione temporanea, vedere [Abilitazione e disabilitazione dell'eliminazione](backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete)temporanea.

## <a name="next-steps"></a>Passaggi successivi

- Leggi le [domande frequenti](backup-azure-security-feature-cloud.md#frequently-asked-questions) sull'eliminazione temporanea
- Leggere le informazioni su tutte le [funzionalità di sicurezza di backup di Azure](security-overview.md)
