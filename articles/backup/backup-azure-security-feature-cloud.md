---
title: Eliminazione temporanea per backup di Azure
description: Informazioni su come usare le funzionalità di sicurezza in backup di Azure per rendere più sicuri i backup.
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: d7998c7f9def6ce9965ded3b6ec700f7975891eb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91271546"
---
# <a name="soft-delete-for-azure-backup"></a>Eliminazione temporanea per backup di Azure

Le preoccupazioni riguardo ai problemi di sicurezza, come malware, ransomware e intrusioni, aumentano continuamente. Questi problemi di sicurezza possono essere costosi in termini di denaro e di dati. Per evitare tali attacchi, backup di Azure offre ora funzionalità di sicurezza che consentono di proteggere i dati di backup anche dopo l'eliminazione.

Una di queste funzionalità è l'eliminazione temporanea. Con l'eliminazione temporanea, anche se un attore malintenzionato Elimina un backup (o elimina accidentalmente i dati di backup), i dati di backup vengono conservati per 14 giorni aggiuntivi, consentendo il ripristino di tale elemento di backup senza perdita di dati. Gli altri 14 giorni di conservazione per i dati di backup nello stato "eliminazione temporanea" non comportano alcun costo.

La protezione per l'eliminazione temporanea è disponibile per questi servizi:

- [Eliminazione temporanea per macchine virtuali di Azure](soft-delete-virtual-machines.md)
- [Eliminazione temporanea per SQL Server in macchine virtuali di Azure e eliminazione temporanea per SAP HANA nei carichi di lavoro delle macchine virtuali di Azure](soft-delete-sql-saphana-in-azure-vm.md)

Questo diagramma di flusso Mostra i diversi passaggi e Stati di un elemento di backup quando l'eliminazione temporanea è abilitata:

![Ciclo di vita dell'elemento di backup eliminato temporaneamente](./media/backup-azure-security-feature-cloud/lifecycle.png)

## <a name="enabling-and-disabling-soft-delete"></a>Abilitazione e disabilitazione dell'eliminazione temporanea

L'eliminazione temporanea è abilitata per impostazione predefinita negli insiemi di credenziali appena creati per proteggere i dati di backup da eliminazioni accidentali o dannose.  La disabilitazione di questa funzionalità non è consigliata. L'unica circostanza in cui è consigliabile disabilitare l'eliminazione temporanea è se si prevede di trasferire gli elementi protetti in un nuovo insieme di credenziali e non è possibile attendere i 14 giorni necessari prima dell'eliminazione e della riprotezione (ad esempio in un ambiente di test). Questa funzionalità può essere disabilitata solo dal proprietario dell'insieme di credenziali. Se questa funzionalità viene disabilitata, tutte le successive eliminazioni di elementi protetti comporteranno la rimozione immediata, senza la possibilità di eseguire il ripristino. I dati di backup presenti nello stato di eliminazione temporanea prima di disabilitare questa funzionalità rimarranno in stato di eliminazione temporanea per il periodo di 14 giorni. Per eliminare definitivamente questi elementi immediatamente, è necessario annullarne l'eliminazione ed eliminarli di nuovo per eliminarli definitivamente.

È importante ricordare che quando l'eliminazione temporanea è disabilitata, la funzionalità è disabilitata per tutti i tipi di carichi di lavoro. Ad esempio, non è possibile disabilitare l'eliminazione temporanea solo per SQL Server o SAP HANA database e mantenerlo abilitato per le macchine virtuali nello stesso insieme di credenziali. È possibile creare insiemi di credenziali separati per il controllo granulare.

### <a name="disabling-soft-delete-using-azure-portal"></a>Disabilitazione dell'eliminazione temporanea con portale di Azure

Per disabilitare l'eliminazione temporanea, attenersi alla procedura seguente:

1. Nel portale di Azure passare all'insieme di credenziali, quindi passare a **Impostazioni**  ->  **proprietà**.
2. Nel riquadro Proprietà selezionare impostazioni di **sicurezza**  ->  **Aggiorna**.  
3. Nel riquadro impostazioni di sicurezza, in **eliminazione**temporanea, selezionare **Disabilita**.

![Disabilitare l'eliminazione temporanea](./media/backup-azure-security-feature-cloud/disable-soft-delete.png)

### <a name="disabling-soft-delete-using-azure-powershell"></a>Disabilitazione dell'eliminazione temporanea con Azure PowerShell

> [!IMPORTANT]
> La versione AZ. RecoveryServices necessaria per usare l'eliminazione temporanea con Azure PowerShell è la 2.2.0 minima. Usare ```Install-Module -Name Az.RecoveryServices -Force``` per ottenere la versione più recente.

Per disabilitare, usare il cmdlet di PowerShell [set-AzRecoveryServicesVaultBackupProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) .

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

Seguire questa procedura:

1. Seguire i passaggi per [disabilitare l'eliminazione](#enabling-and-disabling-soft-delete)temporanea.

2. Nel portale di Azure passare all'insieme di credenziali, passare a **elementi di backup**e scegliere l'elemento eliminato temporaneamente.

   ![Scegliere un elemento eliminato temporaneamente](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

3. Selezionare l'opzione **Annulla eliminazione**.

   ![Scegli Annulla eliminazione](./media/backup-azure-security-feature-cloud/choose-undelete.png)

4. Verrà visualizzata una finestra. Selezionare **Annulla eliminazione**.

   ![Selezionare Annulla eliminazione](./media/backup-azure-security-feature-cloud/undelete-vm.png)

5. Scegliere **Elimina dati di backup** per eliminare definitivamente i dati di backup.

   ![Scegliere Elimina dati di backup](/azure/backup/media/backup-azure-manage-vms/delete-backup-buttom.png)

6. Digitare il nome dell'elemento di backup per confermare che si desidera eliminare i punti di ripristino.

   ![Digitare il nome dell'elemento di backup](/azure/backup/media/backup-azure-manage-vms/delete-backup-data1.png)

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

1. Per prima cosa, annullare le operazioni di eliminazione con i passaggi indicati di [seguito](backup-azure-arm-userestapi-backupazurevms.md#undo-the-deletion).
2. Disabilitare quindi la funzionalità di eliminazione temporanea usando l'API REST usando la procedura descritta [qui](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api).
3. Eliminare quindi i backup usando l'API REST come indicato [qui](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data).

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>È necessario abilitare la funzionalità di eliminazione temporanea in ogni insieme di credenziali?

No, è incorporata e abilitata per impostazione predefinita per tutti gli insiemi di credenziali dei servizi di ripristino.

### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-the-delete-operation-is-complete"></a>È possibile configurare il numero di giorni per cui i dati verranno conservati nello stato di eliminazione temporanea dopo il completamento dell'operazione di eliminazione?

No, è stato fissato a 14 giorni di conservazione aggiuntiva dopo l'operazione di eliminazione.

### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>È necessario pagare il costo per questa conservazione aggiuntiva di 14 giorni?

No, questo periodo di conservazione aggiuntivo di 14 giorni è gratuito come parte della funzionalità di eliminazione temporanea.

### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>È possibile eseguire un'operazione di ripristino quando i dati sono in stato di eliminazione temporanea?

No, è necessario annullare l'eliminazione della risorsa eliminata temporaneamente per poter eseguire il ripristino. L'operazione di annullamento dell'eliminazione riporterà la risorsa nello **stato di arresto della protezione con Mantieni dati** in cui è possibile eseguire il ripristino in qualsiasi momento. Il Garbage Collector rimane in pausa in questo stato.

### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>Gli snapshot seguiranno lo stesso ciclo di vita dei punti di ripristino nell'insieme di credenziali?

Sì.

### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>Come è possibile attivare di nuovo i backup pianificati per una risorsa eliminata temporaneamente?

L'annullamento dell'eliminazione seguito da un'operazione di ripresa proteggerà di nuovo la risorsa. L'operazione di ripresa associa un criterio di backup per attivare i backup pianificati con il periodo di memorizzazione selezionato. Inoltre, il Garbage Collector viene eseguito non appena l'operazione di ripresa viene completata. Se si desidera eseguire un ripristino da un punto di ripristino che supera la data di scadenza, è consigliabile eseguire questa operazione prima di attivare l'operazione di ripresa.

### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>È possibile eliminare l'insieme di credenziali se sono presenti elementi eliminati temporaneamente nell'insieme di credenziali?

Non è possibile eliminare l'insieme di credenziali di servizi di ripristino se sono presenti elementi di backup in stato di eliminazione temporanea nell'insieme di credenziali. Gli elementi eliminati temporaneamente vengono eliminati definitivamente 14 giorni dopo l'operazione di eliminazione. Se non è possibile attendere 14 giorni, [disabilitare l'eliminazione](#enabling-and-disabling-soft-delete)temporanea, annullare l'eliminazione degli elementi eliminati temporaneamente ed eliminarli di nuovo per eliminarli definitivamente. Dopo aver verificato che non siano presenti elementi protetti e che non siano presenti elementi eliminati temporaneamente, è possibile eliminare l'insieme di credenziali.  

### <a name="can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>È possibile eliminare i dati prima del periodo di eliminazione temporanea di 14 giorni dopo l'eliminazione?

No. Non è possibile forzare l'eliminazione degli elementi eliminati temporaneamente. Vengono eliminati automaticamente dopo 14 giorni. Questa funzionalità di sicurezza è abilitata per salvaguardare i dati di backup da eliminazioni accidentali o dannose.  È necessario attendere 14 giorni prima di eseguire altre azioni sull'elemento.  Gli elementi eliminati temporaneamente non verranno addebitati.  Se è necessario riproteggere gli elementi contrassegnati per l'eliminazione temporanea entro 14 giorni in un nuovo insieme di credenziali, contattare il supporto tecnico Microsoft.

### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>È possibile eseguire operazioni di eliminazione temporanea in PowerShell o nell'interfaccia della riga di comando?

Le operazioni di eliminazione temporanea possono essere eseguite usando PowerShell. Attualmente, l'interfaccia della riga di comando non è supportata.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica delle funzionalità di sicurezza in Backup di Azure](security-overview.md)
