---
title: Eseguire il backup di File di Azure con PowerShell
description: Questo articolo illustra come eseguire il backup di File di Azure usando il servizio backup di Azure e PowerShell.
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 5147ab893d4ebad395d7dbd8cc25872177ec10a2
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773097"
---
# <a name="back-up-azure-files-with-powershell"></a>Eseguire il backup di File di Azure con PowerShell

Questo articolo descrive come usare Azure PowerShell per eseguire il backup di una condivisione file File di Azure usando un insieme di credenziali di servizi di ripristino di [backup di Azure](backup-overview.md) .

Questo articolo spiega come:

> [!div class="checklist"]
>
> * Configurare PowerShell e registrare il provider di servizi di ripristino di Azure.
> * Creare un insieme di credenziali dei servizi di ripristino.
> * Configurare il backup per una condivisione file di Azure.
> * Eseguire un processo di backup.

## <a name="before-you-start"></a>Prima di iniziare

* [Altre](backup-azure-recovery-services-vault-overview.md) informazioni sugli insiemi di credenziali dei servizi di ripristino.
* Leggere le informazioni sulle funzionalità di anteprima per il [backup di condivisioni file di Azure](backup-afs.md).
* Esaminare la gerarchia di oggetti di PowerShell per i servizi di ripristino.

## <a name="recovery-services-object-hierarchy"></a>Gerarchia di oggetti dei servizi di ripristino

La gerarchia di oggetti viene riepilogata nel diagramma seguente.

![Gerarchia di oggetti dei servizi di ripristino](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Esaminare il riferimento al [cmdlet](/powershell/module/az.recoveryservices) **AZ. RecoveryServices** nella libreria di Azure.

## <a name="set-up-and-install"></a>Configurare e installare

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Configurare PowerShell nel modo seguente:

1. [Scaricare la versione più recente di Az PowerShell](/powershell/azure/install-az-ps). La versione 1.0.0 è la versione minima necessaria.

2. Trovare i cmdlet di PowerShell per backup di Azure con questo comando:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Esaminare gli alias e i cmdlet per backup di Azure, Azure Site Recovery e l'insieme di credenziali di servizi di ripristino. Ecco un esempio di ciò che è possibile vedere. Non si tratta di un elenco completo di cmdlet.

    ![Elenco di cmdlet dei Servizi di ripristino](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Accedere al proprio account Azure con **Connect-AzAccount**.
5. Nella pagina Web visualizzata verrà richiesto di immettere le credenziali dell'account.

    * In alternativa, è possibile includere le credenziali dell'account come parametro nel cmdlet **Connect-AzAccount** con **-Credential**.
    * Se si è un partner CSP che lavora per conto di un tenant, specificare il cliente come tenant, usando il nome di dominio primario tenantID o tenant. Un esempio è **Connect-AzAccount -Tenant** fabrikam.com.

6. Associare la sottoscrizione che si vuole usare all'account perché un account può avere più sottoscrizioni.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Se si sta usando Backup di Azure per la prima volta, usare il cmdlet **Register-AzResourceProvider** per registrare il provider di Servizi di ripristino di Azure con la propria sottoscrizione.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. Verificare che i provider siano stati registrati correttamente:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. Nell'output del comando verificare che **RegistrationState** venga modificato in **registrato**. In caso contrario, eseguire di nuovo il cmdlet **Register-AzResourceProvider** .

## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

L'insieme di credenziali di Servizi di ripristino è una risorsa di Resource Manager, quindi è necessario inserirlo all'interno di un gruppo di risorse. È possibile usare un gruppo di risorse esistente o crearne uno con il cmdlet **New-AzResourceGroup**. Quando si crea un nuovo gruppo di risorse, è necessario specificare il nome e il percorso per il gruppo di risorse.

Seguire questa procedura per creare un insieme di credenziali di Servizi di ripristino.

1. Un insieme di credenziali viene inserito in un gruppo di risorse. Se non si dispone di un gruppo di risorse esistente, crearne uno nuovo con [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0). In questo esempio viene creato un nuovo gruppo di risorse nell'area Stati Uniti occidentali.

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```

2. Usare il cmdlet [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) per creare l'insieme di credenziali. Specificare per l'insieme di credenziali lo stesso percorso usato per il gruppo di risorse.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Specificare il tipo di ridondanza da usare per l'archiviazione dell'insieme di credenziali.

   * È possibile usare l'[archiviazione con ridondanza locale](../storage/common/storage-redundancy-lrs.md) o l'[archiviazione con ridondanza geografica](../storage/common/storage-redundancy-grs.md).
   * Nell'esempio seguente viene impostata l'opzione **-BackupStorageRedundancy** per[set-AzRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) cmd per **testvault** impostato su **georidondante**.

     ```powershell
     $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
     Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
     ```

### <a name="view-the-vaults-in-a-subscription"></a>Visualizzare gli insiemi di credenziali in un abbonamento

Per visualizzare tutti gli insiemi di credenziali disponibili nella sottoscrizione, usare [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0).

```powershell
Get-AzRecoveryServicesVault
```

L'output è simile al seguente. Si noti che vengono forniti il gruppo di risorse e la località associati.

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>Impostare il contesto dell'insieme di credenziali

Archiviare l'oggetto insieme di credenziali in una variabile e impostare il contesto dell'insieme di credenziali.

* Molti cmdlet di backup di Azure richiedono l'oggetto insieme di credenziali dei servizi di ripristino come input, quindi è consigliabile archiviare l'oggetto insieme di credenziali in una variabile.
* Il contesto dell'insieme di credenziali definisce il tipo di dati protetti nell'insieme di credenziali. Impostarla con [set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0). Una volta impostato il contesto, si applica a tutti i cmdlet successivi.

L'esempio seguente imposta il contesto per **testvault**.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Recuperare l'ID dell'insieme di credenziali

Si prevede di deprecare l'impostazione del contesto dell'insieme di credenziali in base alle linee guida Azure PowerShell. È invece possibile archiviare o recuperare l'ID dell'insieme di credenziali e passarlo ai comandi pertinenti. Quindi, se il contesto dell'insieme di credenziali non è stato impostato o si vuole specificare il comando da eseguire per un determinato insieme di credenziali, passare l'ID dell'insieme di credenziali come "-vaultID" a tutti i comandi pertinenti, come indicato di seguito:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultID $vaultID
```

## <a name="configure-a-backup-policy"></a>Configurare un criterio di backup

I criteri di backup specificano la pianificazione per i backup e il tempo di mantenimento dei punti di ripristino del backup:

* I criteri di backup sono associati ai criteri di conservazione. Un criterio di conservazione definisce per quanto tempo un punto di recupero viene mantenuto prima dell'eliminazione.
* Visualizzare il periodo di conservazione predefinito dei criteri di backup usando [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
* Visualizzare la pianificazione predefinita dei criteri di backup utilizzando [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0).
* Usare il cmdlet [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) per creare un nuovo criterio di backup. Vengono inseriti gli oggetti Criteri di pianificazione e conservazione.

Per impostazione predefinita, nell'oggetto Criteri di pianificazione è definita un'ora di inizio. Usare l'esempio seguente per modificare l'ora di inizio con l'ora di inizio desiderata. L'ora di inizio desiderata dovrebbe essere anche UTC. Nell'esempio seguente si presuppone che l'ora di inizio desiderata sia 01:00 UTC per i backup giornalieri.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> È necessario specificare l'ora di inizio solo in più di 30 minuti. Nell'esempio precedente può essere solo "01:00:00" o "02:30:00". L'ora di inizio non può essere "01:15:00"

Nell'esempio seguente i criteri di pianificazione e i criteri di conservazione vengono archiviati nelle variabili. USA quindi tali variabili come parametri per un nuovo criterio (**NewAFSPolicy**). **NewAFSPolicy** effettua un backup giornaliero e lo conserva per 30 giorni.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

L'output è simile al seguente.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2019 1:30:00 AM
```

## <a name="enable-backup"></a>Abilita backup

Dopo aver definito i criteri di backup, è possibile abilitare la protezione per la condivisione file di Azure usando il criterio.

### <a name="retrieve-a-backup-policy"></a>Recuperare un criterio di backup

Per recuperare l'oggetto criteri pertinente, [usare Get-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0). Utilizzare questo cmdlet per ottenere un criterio specifico o per visualizzare i criteri associati a un tipo di carico di lavoro.

#### <a name="retrieve-a-policy-for-a-workload-type"></a>Recuperare i criteri per un tipo di carico di lavoro

Nell'esempio seguente vengono recuperati i criteri per il tipo di carico di lavoro **risorsa**.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

L'output è simile al seguente.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> Il fuso orario del campo **BackupTime** in PowerShell corrisponde all'ora UTC (Coordinated Universal Time). L'orario di backup nel portale di Azure è allineato al fuso orario locale.

### <a name="retrieve-a-specific-policy"></a>Recuperare un criterio specifico

Il criterio seguente recupera il criterio di backup denominato **dailyafs**.

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-backup-and-apply-policy"></a>Abilitare il backup e applicare i criteri

Abilitare la protezione con [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). Dopo che il criterio è stato associato all'insieme di credenziali, i backup vengono attivati in base alla pianificazione dei criteri.

L'esempio seguente abilita la protezione per la condivisione file di Azure **testAzureFileShare** nell'account di archiviazione **testStorageAcct**, con il criterio **dailyafs**.

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

Il comando attende fino a quando il processo di protezione della configurazione non viene completato e fornisce un output simile al seguente.

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

## <a name="trigger-an-on-demand-backup"></a>Attivare un backup su richiesta

Usare [backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem?view=azps-1.4.0) per eseguire un backup su richiesta per una condivisione file di Azure protetta.

1. Recuperare l'account di archiviazione e la condivisione file dal contenitore nell'insieme di credenziali che include i dati di backup con [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Per avviare un processo di backup, ottenere le informazioni relative alla VM con [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Eseguire un backup su richiesta con [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem).

Eseguire il backup su richiesta nel modo seguente:

```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -Name "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

Il comando restituisce un processo con un ID di cui tenere traccia, come mostrato nell'esempio seguente.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

Durante l'esecuzione dei backup vengono usati gli snapshot di condivisione file di Azure, quindi in genere il processo viene completato nel momento in cui il comando restituisce l'output.

### <a name="using-on-demand-backups-to-extend-retention"></a>Uso di backup su richiesta per estendere la conservazione

È possibile usare i backup su richiesta per conservare gli snapshot per 10 anni. È possibile usare le utilità di pianificazione per eseguire script di PowerShell su richiesta con la conservazione scelta e quindi creare snapshot a intervalli regolari ogni settimana, mese o anno. Durante l'esecuzione di snapshot regolari, vedere le [limitazioni dei backup su richiesta](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#how-many-on-demand-backups-can-i-take-per-file-share) con backup di Azure.

Per gli script di esempio, è possibile fare riferimento allo script di esempio in GitHub (<https://github.com/Azure-Samples/Use-PowerShell-for-long-term-retention-of-Azure-Files-Backup>) usando Runbook di automazione di Azure che consente di pianificare i backup periodicamente e mantenerli anche fino a 10 anni.

## <a name="next-steps"></a>Passaggi successivi

[Informazioni sul](backup-afs.md) backup di File di Azure nel portale di Azure.
