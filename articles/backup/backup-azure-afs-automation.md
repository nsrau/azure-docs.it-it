---
title: Eseguire il backup e ripristino dei file di Azure con Backup di Azure e PowerShell
description: Eseguire il backup e ripristino dei file di Azure con Backup di Azure e PowerShell.
author: pvrk
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: pullabhk
ms.openlocfilehash: 489de7b2fe87d1bb218e3fce39f269d866d66dd7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57845172"
---
# <a name="back-up-and-restore-azure-files-with-powershell"></a>Eseguire il backup e ripristinare i file di Azure con PowerShell

Questo articolo descrive come usare Azure PowerShell per eseguire il backup e ripristinare una condivisione file file di Azure usando un [Backup di Azure](backup-overview.md) insieme di credenziali dei servizi di ripristino. 

In questa esercitazione viene illustrato come:

> [!div class="checklist"]
> * Configurazione di PowerShell e registrare il Provider di servizi di ripristino di Azure.
> * Creare un insieme di credenziali dei servizi di ripristino.
> * Configurare il backup per una condivisione file di Azure.
> * Eseguire un processo di backup.
> * Ripristinare un backup condivisione file di Azure o un singolo file da una condivisione.
> * Monitorare il backup e ripristino di processi.


## <a name="before-you-start"></a>Prima di iniziare

- [Altre informazioni](backup-azure-recovery-services-vault-overview.md) su insiemi di credenziali di servizi di ripristino.
- Scopri le funzionalità di anteprima per [backup di condivisioni file di Azure](backup-azure-files.md).
- Esaminare la gerarchia di oggetti PowerShell per servizi di ripristino.


## <a name="recovery-services-object-hierarchy"></a>Gerarchia di oggetti dei servizi di ripristino

Nel diagramma seguente viene riepilogata la gerarchia di oggetti.

![Gerarchia di oggetti dei servizi di ripristino](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Rivedere le **Az.RecoveryServices** [riferimento dei cmdlet](/powershell/module/az.recoveryservices) riferimento nella raccolta di Azure.


## <a name="set-up-and-install"></a>Configurare e installare

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Configurare PowerShell nel modo seguente:

1. [Scaricare la versione più recente di Az PowerShell](/powershell/azure/install-az-ps). La versione 1.0.0 è la versione minima necessaria.

2. Trovare i cmdlet di PowerShell di Backup di Azure con questo comando:

    ```powershell
    Get-Command *azrecoveryservices*
    ```
3. Esaminare i cmdlet e gli alias per il Backup di Azure, Azure Site Recovery e l'insieme di credenziali di servizi di ripristino vengono visualizzati. Di seguito è riportato un esempio di ciò che potrebbe essere visualizzato. Non è un elenco completo dei cmdlet.

    ![Elenco di cmdlet dei Servizi di ripristino](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

3. Accedere al proprio account Azure con **Connect-AzAccount**.
4. Nella pagina web che viene visualizzato, viene chiesto di immettere le credenziali dell'account.

    - In alternativa, è possibile includere le credenziali dell'account come parametro nel **Connect-AzAccount** cmdlet con **-Credential**.
    - Se sei un partner CSP che opera per conto di un tenant, specificare il cliente come tenant, usando il nome di dominio primario ID tenant o tenant. Un esempio è **Connect-AzAccount -Tenant** fabrikam.com.

4. Associare la sottoscrizione da usare con l'account, perché un account può avere molte sottoscrizioni.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

5. Se si sta usando Backup di Azure per la prima volta, usare il cmdlet **Register-AzResourceProvider** per registrare il provider di Servizi di ripristino di Azure con la propria sottoscrizione.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Verificare che i provider di stato registrato correttamente:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
7. Nell'output del comando, verificare che **RegistrationState** diventa **Registered**. Se non, viene eseguito il **Register-AzResourceProvider** cmdlet nuovamente.



## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

Seguire questa procedura per creare un insieme di credenziali di Servizi di ripristino.

- L'insieme di credenziali di Servizi di ripristino è una risorsa di Resource Manager, quindi è necessario inserirlo all'interno di un gruppo di risorse. È possibile usare un gruppo di risorse esistente o crearne uno con il cmdlet **New-AzResourceGroup**. Quando si crea un nuovo gruppo di risorse, è necessario specificare il nome e il percorso per il gruppo di risorse. 

1. Un insieme di credenziali viene inserito in un gruppo di risorse. Se non hai una risorsa esistente di gruppo, crearne uno nuovo con il [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0). In questo esempio, creiamo un nuovo gruppo di risorse nell'area Stati Uniti occidentali.

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```
2. Usare la [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) cmdlet per creare l'insieme di credenziali. Specificare per l'insieme di credenziali lo stesso percorso usato per il gruppo di risorse.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. Specificare il tipo di ridondanza da utilizzare per l'archiviazione dell'insieme di credenziali.

   - È possibile usare l'[archiviazione con ridondanza locale](../storage/common/storage-redundancy-lrs.md) o l'[archiviazione con ridondanza geografica](../storage/common/storage-redundancy-grs.md).
   - L'esempio seguente imposta la **- BackupStorageRedundancy** opzione per il[Set-AzRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperties?view=azps-1.4.0) cmd per **testvault** impostato su  **Con ridondanza geografica**.

     ```powershell
     $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
     Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
     ```

### <a name="view-the-vaults-in-a-subscription"></a>Visualizzare gli insiemi di credenziali in un abbonamento

Per visualizzare tutti gli insiemi di credenziali disponibili nella sottoscrizione, usare [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0).

```powershell
Get-AzRecoveryServicesVault
```

L'output è simile al seguente. Si noti che il gruppo di risorse associato e il percorso vengono forniti.

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

L'oggetto dell'insieme di credenziali di Store in una variabile e impostare il contesto dell'insieme di credenziali.

- Molti cmdlet di Backup di Azure richiede l'oggetto dell'insieme di credenziali di servizi di ripristino come input, pertanto è utile archiviare l'oggetto dell'insieme di credenziali in una variabile.
- Il contesto dell'insieme di credenziali definisce il tipo di dati protetti nell'insieme di credenziali. Impostarlo con [Set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0). Dopo aver impostato il contesto, si applica a tutti i cmdlet successivi.


L'esempio seguente imposta il contesto per **testvault**.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Recuperare l'ID dell'insieme di credenziali

È prevista la deprecazione di contesto dell'insieme di credenziali impostazione in base alle linee guida di Azure PowerShell. In alternativa, è possibile archiviare o recuperare l'ID dell'insieme di credenziali e passarla a comandi rilevanti, come indicato di seguito:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-a-backup-policy"></a>Configurare un criterio di backup

Un criterio di backup specifica la pianificazione per i backup e mantenimento dei punti di ripristino dei backup quanto tempo:

- I criteri di backup sono associati ai criteri di conservazione. Un criterio di conservazione definisce per quanto tempo un punto di recupero viene mantenuto prima dell'eliminazione.
- Visualizza la conservazione dei criteri di backup predefinita utilizzando [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
- Visualizza la pianificazione dei criteri di backup predefinita utilizzando [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0).
-  Si utilizza il [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) cmdlet per creare un nuovo criterio di backup. Gli oggetti Criteri di conservazione e pianificazione di input.

Nell'esempio seguente i criteri di pianificazione e i criteri di conservazione vengono archiviati nelle variabili. Utilizza quindi tali variabile come parametri per un nuovo criterio (**NewAFSPolicy**). **NewAFSPolicy** effettua un backup giornaliero e lo conserva per 30 giorni.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

L'output è simile al seguente.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2017 1:30:00 AM
```



## <a name="enable-backup"></a>Abilitare il backup

Dopo aver definito i criteri di backup, è possibile abilitare la protezione per la condivisione file di Azure usando i criteri.

### <a name="retrieve-a-backup-policy"></a>Recuperare un criterio di backup

Recuperare l'oggetto relativo al criterio con [Get-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0). Utilizzare questo cmdlet per ottenere un criterio specifico, o per visualizzare i criteri associati a un tipo di carico di lavoro.

#### <a name="retrieve-a-policy-for-a-workload-type"></a>Recuperare i criteri per un tipo di carico di lavoro

Nell'esempio seguente recupera i criteri per il tipo di carico di lavoro **risorsa**.

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

Abilitare la protezione con [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). Dopo che il criterio è associato l'insieme di credenziali, i backup vengono attivati in conformità con la pianificazione dei criteri.

Nell'esempio seguente abilita la protezione per la condivisione file di Azure **testAzureFileShare** nell'account di archiviazione **testStorageAcct**, con i criteri **dailyafs**.

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

Uso [Backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem?view=azps-1.4.0) per eseguire un backup su richiesta per una condivisione file di Azure protetta.

1. Recuperare l'account di archiviazione e della condivisione file dal contenitore nell'insieme di credenziali che contiene i dati di backup con [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices.backup/get-Azrecoveryservicesbackupcontainer).
2. Per avviare un processo di backup, è ottenere informazioni relative alla macchina virtuale con [Get-AzRecoveryServicesBackupItem](/powershell/module/Az.RecoveryServices.Backup/Get-AzRecoveryServicesBackupItem).
3. Eseguire un backup su richiesta con[Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices.backup/backup-Azrecoveryservicesbackupitem).

Eseguire il backup su richiesta come indicato di seguito:
    
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

### <a name="modify-the-protection-policy"></a>Modificare i criteri di protezione

Per modificare i criteri utilizzati per il backup di backup della condivisione file di Azure, usare [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). Specificare l'elemento di backup pertinente e il nuovo criterio di backup.

L'esempio seguente modifica il criterio di protezione **testAzureFS** da **dailyafs** a **monthlyafs**.

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="restore-azure-file-shares-and-files"></a>Ripristina file e condivisioni file di Azure

È possibile ripristinare un'intera condivisione file o file specifici nella condivisione. È possibile ripristinare nella posizione originale o in un percorso alternativo. 

### <a name="fetch-recovery-points"></a>Recuperare i punti di recupero

Uso [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint?view=azps-1.4.0) per elencare tutti i punti di ripristino per l'elemento sottoposto a backup.

Nello script seguente:

- La variabile **$rp** è una matrice di punti di ripristino per l'elemento di backup selezionato degli ultimi sette giorni.
- La matrice viene ordinata in ordine inverso di tempo con il punto di recupero più recente in posizione **0** nell'indice.
- Per scegliere il punto di ripristino, usare l'indicizzazione standard della matrice di PowerShell.
- Nell'esempio **$rp [0]** seleziona il punto di recupero più recente.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $afsBkpItem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()

$rp[0] | fl
```

L'output è simile al seguente.

```powershell
FileShareSnapshotUri : https://testStorageAcct.file.core.windows.net/testAzureFS?sharesnapshot=2018-11-20T00:31:04.00000
                       00Z
RecoveryPointType    : FileSystemConsistent
RecoveryPointTime    : 11/20/2018 12:31:05 AM
RecoveryPointId      : 86593702401459
ItemName             : testAzureFS
Id                   : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Micros                      oft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;teststorageRG;testStorageAcct/protectedItems/AzureFileShare;testAzureFS/recoveryPoints/86593702401462
WorkloadType         : AzureFiles
ContainerName        : storage;teststorageRG;testStorageAcct
ContainerType        : AzureStorage
BackupManagementType : AzureStorage
```
Dopo aver selezionato il punto di ripristino pertinente, ripristinare la condivisione file o il file nel percorso originale o in un percorso alternativo.

### <a name="restore-an-azure-file-share-to-an-alternate-location"></a>Ripristinare una condivisione file di Azure in un percorso alternativo

Usare la [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/en-us/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) per ripristinare il punto di ripristino selezionato. Specificare i parametri seguenti per identificare il percorso alternativo: 

- **TargetStorageAccountName**: account di archiviazione in cui ripristinare il contenuto sottoposto a backup. L'account di archiviazione di destinazione deve trovarsi nella stessa posizione dell'insieme di credenziali.
- **TargetFileShareName**: condivisioni file nell'account di archiviazione di destinazione in cui ripristinare il contenuto sottoposto a backup.
- **TargetFolder**: cartella nella condivisione file in cui vengono ripristinati i dati. Se il contenuto sottoposto a backup deve essere ripristinato in una cartella radice, fornire i valori della cartella di destinazione come stringa vuota.
- **ResolveConflict**: istruzione in caso di conflitto con i dati ripristinati. Accetta **Overwrite** o **skip**.

Eseguire il cmdlet con i parametri come segue:

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
```

Il comando restituisce un processo con un ID di cui tenere traccia, come mostrato nell'esempio seguente.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
```

### <a name="restore-an-azure-file-to-an-alternate-location"></a>Ripristinare un file di Azure in un percorso alternativo

Usare la [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/en-us/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) per ripristinare il punto di ripristino selezionato. Specificare questi parametri per identificare il percorso alternativo e per identificare in modo univoco il file da ripristinare.

* **TargetStorageAccountName**: account di archiviazione in cui ripristinare il contenuto sottoposto a backup. L'account di archiviazione di destinazione deve trovarsi nella stessa posizione dell'insieme di credenziali.
* **TargetFileShareName**: condivisioni file nell'account di archiviazione di destinazione in cui ripristinare il contenuto sottoposto a backup.
* **TargetFolder**: cartella nella condivisione file in cui vengono ripristinati i dati. Se il contenuto sottoposto a backup deve essere ripristinato in una cartella radice, fornire i valori della cartella di destinazione come stringa vuota.
* **SourceFilePath**: percorso assoluto del file da ripristinare all'interno della condivisione file, sotto forma di stringa. Questo percorso è uguale a quello usato nel cmdlet **Get-AzStorageFile** di PowerShell.
* **SourceFileType**: indica se è selezionato un file o una directory. Accetta **Directory** o **File**.
* **ResolveConflict**: istruzione in caso di conflitto con i dati ripristinati. Accetta **Overwrite** o **skip**.

I parametri aggiuntivi (percorsofileorigine e SourceFileType) riguardano solo il singolo file da ripristinare.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

Questo comando restituisce un processo con un ID da rilevare, come illustrato nella sezione precedente.

### <a name="restore-azure-file-shares-and-files-to-the-original-location"></a>Ripristinare file e condivisioni file di Azure nel percorso originale

Quando si ripristina in un percorso originale, non devi specificare parametri relativi alla destinazione e destinazione. Deve essere fornito solo **ResolveConflict**.

#### <a name="overwrite-an-azure-file-share"></a>Sovrascrivere una condivisione file di Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

#### <a name="overwrite-an-azure-file"></a>Sovrascrivere un file di Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="track-backup-and-restore-jobs"></a>Tenere traccia dei processi di backup e ripristino

Operazioni di backup e ripristino on demand restituiscono un processo con un ID, come mostrato quando si [è stato eseguito un backup su richiesta](#trigger-an-on-demand-backup). Usare la [Get-AzRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob?view=azps-1.4.0) cmdlet per tenere traccia dell'avanzamento del processo e i dettagli.

```powershell
$job = Get-AzRecoveryServicesBackupJob -JobId 00000000-6c46-496e-980a-3740ccb2ad75 -VaultId $vaultID

 $job | fl


IsCancellable        : False
IsRetriable          : False
ErrorDetails         : {Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureFileShareJobErrorInfo}
ActivityId           : 00000000-5b71-4d73-9465-8a4a91f13a36
JobId                : 00000000-6c46-496e-980a-3740ccb2ad75
Operation            : Restore
Status               : Failed
WorkloadName         : testAFS
StartTime            : 12/10/2018 9:56:38 AM
EndTime              : 12/10/2018 11:03:03 AM
Duration             : 01:06:24.4660027
BackupManagementType : AzureStorage

$job.ErrorDetails

 ErrorCode ErrorMessage                                          Recommendations
 --------- ------------                                          ---------------
1073871825 Microsoft Azure Backup encountered an internal error. Wait for a few minutes and then try the operation again. If the issue persists, please contact Microsoft support.
```
## <a name="next-steps"></a>Passaggi successivi
[Informazioni su](backup-azure-files.md) backup dei file di Azure nel portale di Azure.
