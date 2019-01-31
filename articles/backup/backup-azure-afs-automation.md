---
title: Distribuire e gestire i backup per le condivisioni file di Azure usando PowerShell
description: Usare PowerShell per distribuire e gestire i backup in Azure per le condivisioni file di Azure
services: backup
author: pvrk
manager: shivamg
keywords: PowerShell; Backup di File di Azure; Ripristino di File di Azure;
ms.service: backup
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: pullabhk
ms.assetid: 80da8ece-2cce-40dd-8dce-79960b6ae073
ms.openlocfilehash: 912336d697e8f7b5d9c71080ec9a052ca562da4b
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2019
ms.locfileid: "55101133"
---
# <a name="use-powershell-to-back-up-and-restore-azure-file-shares"></a>Usare PowerShell per eseguire il backup e il ripristino di condivisioni file di Azure

Questo articolo illustra come usare i cmdlet di Azure PowerShell per eseguire il backup e il ripristino di una condivisione file di Azure da un insieme di credenziali di Servizi di ripristino. Un insieme di credenziali di Servizi di ripristino è una risorsa di Azure Resource Manager usata per proteggere dati e asset in Backup di Azure e Azure Site Recovery.

## <a name="concepts"></a>Concetti

Se non si ha familiarità con Backup di Azure, vedere [Informazioni su Backup di Azure](backup-introduction-to-azure-backup.md) per una panoramica del servizio. Prima di iniziare, vedere le funzionalità in anteprima usate per eseguire un backup delle condivisioni file di Azure in [Eseguire il backup di condivisioni file di Azure](backup-azure-files.md).

Per un uso efficace di PowerShell, è necessario comprendere la gerarchia degli oggetti e da dove iniziare.

![Gerarchia di oggetti dei servizi di ripristino](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Per visualizzare il riferimento al cmdlet **AzureRm.RecoveryServices.Backup** di PowerShell, vedere [Azure Backup - Recovery Services cmdlets](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup) (Backup di Azure - Cmdlet di Servizi di ripristino) nella libreria di Azure.

## <a name="setup-and-registration"></a>Installazione e registrazione

> [!NOTE]
> Come accennato in [Install the Azure PowerShell module](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-6.13.0) (Installare il modulo di Azure PowerShell), il supporto per le nuove funzionalità nel modulo AzureRM termina a novembre 2018. Il supporto viene fornito per il backup delle condivisioni file di Azure con il nuovo modulo Az PowerShell ora disponibile a livello generale.

Seguire questa procedura per iniziare.

1. [Scaricare la versione più recente di Az PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azurermps-6.13.0). La versione 1.0.0 è la versione minima necessaria.

2. Cercare i cmdlet di **PowerShell di Backup di Azure** disponibili immettendo il comando seguente.

    ```powershell
    Get-Command *azrecoveryservices*
    ```
    Verranno visualizzati alias e cmdlet per Backup di Azure, Azure Site Recovery e l'insieme di credenziali di Servizi di ripristino. L'immagine seguente è un esempio di quanto viene visualizzato. Non è l'elenco completo dei cmdlet.

    ![Elenco di cmdlet dei Servizi di ripristino](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

3. Accedere all'account Azure usando **Connect-AzAccount**. Questo cmdlet visualizza una pagina Web che richiede le credenziali dell'account:

    * In alternativa, è possibile includere le credenziali dell'account come parametro nel cmdlet **Connect-AzAccount**, usando il parametro **-Credential**.
    * Se si è un partner CSP che opera per conto di un tenant, è necessario specificare il cliente come tenant usando l'ID tenant o il nome di dominio primario del tenant. Un esempio è **Connect-AzAccount -Tenant** fabrikam.com.

4. Associare la sottoscrizione che si vuole usare all'account perché un account può avere molte sottoscrizioni.

    ```powershell
    Select-AzureRmSubscription -SubscriptionName $SubscriptionName
    ```

5. Se si sta usando Backup di Azure per la prima volta, usare il cmdlet **Register-AzResourceProvider** per registrare il provider di Servizi di ripristino di Azure con la propria sottoscrizione.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Verificare che i provider siano stati registrati correttamente usando il comando seguente.
    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
    Nell'output del comando il parametro **RegistrationState** viene impostato su **Registered**. Se questa modifica non viene visualizzata, eseguire di nuovo il cmdlet **Register-AzResourceProvider**.

Le attività seguenti possono essere automatizzate tramite PowerShell:

* Creare un insieme di credenziali dei servizi di ripristino.
* Configurare il backup per le condivisioni file di Azure.
* Attivare un processo di backup.
* Monitorare un processo di backup.
* Ripristinare una condivisione file di Azure.
* Ripristinare un singolo file di Azure da una condivisione file di Azure.

## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

Seguire questa procedura per creare un insieme di credenziali di Servizi di ripristino.

1. L'insieme di credenziali di Servizi di ripristino è una risorsa di Resource Manager, quindi è necessario inserirlo all'interno di un gruppo di risorse. È possibile usare un gruppo di risorse esistente o crearne uno con il cmdlet **New-AzResourceGroup**. Quando si crea un nuovo gruppo di risorse, è necessario specificare il nome e il percorso per il gruppo di risorse.  

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```
2. Usare il cmdlet **New-AzRecoveryServicesVault** per creare un nuovo insieme di credenziali di Servizi di ripristino. Specificare per l'insieme di credenziali lo stesso percorso usato per il gruppo di risorse.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. Specificare il tipo di ridondanza di archiviazione da usare. È possibile usare l'[archiviazione con ridondanza locale](../storage/common/storage-redundancy-lrs.md) o l'[archiviazione con ridondanza geografica](../storage/common/storage-redundancy-grs.md). Nell'esempio seguente l'opzione **-BackupStorageRedundancy** per **testvault** è impostata su **GeoRedundant**.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Visualizzare gli insiemi di credenziali in un abbonamento

Per visualizzare tutti gli insiemi di credenziali disponibili nella sottoscrizione, usare **Get-AzRecoveryServicesVault**.

```powershell
Get-AzRecoveryServicesVault
```

L'output è simile all'esempio seguente: Si noti che vengono forniti gli elementi **ResourceGroupName** e **Location** associati.

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

Molti cmdlet di Backup di Azure richiedono l'oggetto dell'insieme di credenziali dei servizi di ripristino come input.

Usare **Set-AzRecoveryServicesVaultContext** per impostare il contesto dell'insieme di credenziali. Dopo essere stato impostato, il contesto dell'insieme di credenziali si applica a tutti i cmdlet successivi. L'esempio seguente imposta il contesto per **testvault**.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

> [!NOTE]
> È previsto che l'impostazione del contesto dell'insieme di credenziali venga deprecata, in base alle linee guida di Azure PowerShell. È invece consigliabile per gli utenti passare l'ID dell'insieme di credenziali come indicato nelle istruzioni seguenti.

In alternativa, archiviare o recuperare l'ID dell'insieme di credenziali in cui si vuole eseguire un'operazione di PowerShell e passarlo al comando pertinente.

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-backup-for-an-azure-file-share"></a>Configurare il backup per una condivisione file di Azure

### <a name="create-a-protection-policy"></a>Creare i criteri di protezione

I criteri di protezione del backup sono associati almeno a un criterio di conservazione. Un criterio di conservazione definisce per quanto tempo un punto di recupero viene mantenuto prima dell'eliminazione. Per visualizzare il criterio di conservazione predefinito usare **Get-AzRecoveryServicesBackupRetentionPolicyObject**. 

Nello stesso modo, per ottenere il criterio di pianificazione predefinito, usare **Get-AzRecoveryServicesBackupSchedulePolicyObject**. Il cmdlet **New-AzRecoveryServicesBackupProtectionPolicy** crea un oggetto di PowerShell che contiene le informazioni relative ai criteri di backup. Gli oggetti dei criteri di conservazione e di pianificazione vengono usati come input per il cmdlet **New-AzRecoveryServicesBackupProtectionPolicy**. 

Nell'esempio seguente i criteri di pianificazione e i criteri di conservazione vengono archiviati nelle variabili. L'esempio usa tali variabili per definire i parametri durante la creazione del criterio di protezione **NewPolicy**.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

L'output è simile all'esempio seguente:

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2017 1:30:00 AM
```

**NewAFSPolicy** effettua un backup giornaliero e lo conserva per 30 giorni.

### <a name="enable-protection"></a>Abilitare la protezione

Dopo aver definito i criteri di protezione, è possibile abilitare la protezione per la condivisione file di Azure con questo criterio.

Prima di tutto, recuperare l'oggetto criteri pertinente con il cmdlet **Get-AzRecoveryServicesBackupProtectionPolicy**. Usare questo cmdlet per ottenere un criterio specifico o per visualizzare i criteri associati a un tipo di carico di lavoro.

L'esempio seguente ottiene i criteri per il tipo di carico di lavoro **AzureFiles**.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

L'output è simile all'esempio seguente:

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> Il fuso orario del campo **BackupTime** in PowerShell corrisponde all'ora UTC (Coordinated Universal Time). L'orario di backup nel portale di Azure è allineato al fuso orario locale.
>
>

Il criterio seguente recupera il criterio di backup denominato **dailyafs**.

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

Usare **Enable-AzRecoveryServicesBackupProtection** per abilitare la protezione dell'elemento con il criterio specificato. Dopo aver associato i criteri all'insieme di credenziali, il flusso di lavoro di backup verrà attivato al momento definito nella pianificazione dei criteri.

L'esempio seguente abilita la protezione per la condivisione file di Azure **testAzureFileShare**, nell'account di archiviazione **testStorageAcct**, con il criterio **dailyafs**.

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

Il comando attende fino a quando il processo di protezione della configurazione non viene completato e fornisce un output simile al seguente.

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

### <a name="trigger-an-on-demand-backup"></a>Attivare un backup su richiesta

Usare **Backup-AzRecoveryServicesBackupItem** per attivare un processo di backup per una condivisione file di Azure protetta. Recuperare la condivisione file e l'account di archiviazione usando i comandi seguenti e attivare un backup su richiesta.

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

Per modificare il criterio di protezione della condivisione file di Azure, usare **Enable-AzRecoveryServicesBackupProtection** con l'elemento di backup pertinente e il nuovo criterio di protezione.

L'esempio seguente modifica il criterio di protezione **testAzureFS** da **dailyafs** a **monthlyafs**.

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="restore-azure-file-shares-and-azure-files"></a>Ripristinare condivisioni file di Azure e file di Azure

È possibile ripristinare un'intera condivisione file nel percorso originale o in uno alternativo. Allo stesso modo, è possibile ripristinare singoli file dalla condivisione file.

### <a name="fetch-recovery-points"></a>Recuperare i punti di recupero

Usare il cmdlet **Get-AzRecoveryServicesBackupRecoveryPoint** per elencare tutti i punti di recupero dell'elemento di backup. Nello script seguente la variabile **$rp** è una matrice di punti di recupero per l'elemento di backup selezionato negli ultimi sette giorni. La matrice viene ordinata in ordine inverso di tempo con il punto di recupero più recente in posizione **0** nell'indice. Per scegliere il punto di ripristino, usare l'indicizzazione standard della matrice di PowerShell. Nell'esempio **$rp [0]** seleziona il punto di recupero più recente.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $afsBkpItem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()

$rp[0] | fl
```

L'output è simile all'esempio seguente:

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

Dopo aver selezionato il punto di recupero pertinente, ripristinare la condivisione file o il file nel percorso originale o in quello alternativo, come illustrato qui.

### <a name="restore-azure-file-shares-to-an-alternate-location"></a>Ripristinare condivisioni file di Azure in un percorso alternativo

#### <a name="restore-an-azure-file-share"></a>Ripristinare una condivisione file di Azure

Specificare il percorso alternativo fornendo le informazioni seguenti:

* **TargetStorageAccountName**: account di archiviazione in cui ripristinare il contenuto sottoposto a backup. L'account di archiviazione di destinazione deve trovarsi nella stessa posizione dell'insieme di credenziali.
* **TargetFileShareName**: condivisioni file nell'account di archiviazione di destinazione in cui ripristinare il contenuto sottoposto a backup.
* **TargetFolder**: cartella nella condivisione file in cui vengono ripristinati i dati. Se il contenuto sottoposto a backup deve essere ripristinato in una cartella radice, fornire i valori della cartella di destinazione come stringa vuota.
* **ResolveConflict**: istruzione in caso di conflitto con i dati ripristinati. Accetta **Overwrite** o **skip**.

Fornire questi parametri al comando Restore per ripristinare una condivisione file sottoposta a backup in un percorso alternativo.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
```

Il comando restituisce un processo con un ID di cui tenere traccia, come mostrato nell'esempio seguente.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
```

#### <a name="restore-an-azure-file"></a>Ripristinare un file di Azure

Per ripristinare un singolo file invece di un'intera condivisione file, identificare in modo univoco il singolo file specificando i parametri seguenti:

* **TargetStorageAccountName**: account di archiviazione in cui ripristinare il contenuto sottoposto a backup. L'account di archiviazione di destinazione deve trovarsi nella stessa posizione dell'insieme di credenziali.
* **TargetFileShareName**: condivisioni file nell'account di archiviazione di destinazione in cui ripristinare il contenuto sottoposto a backup.
* **TargetFolder**: cartella nella condivisione file in cui vengono ripristinati i dati. Se il contenuto sottoposto a backup deve essere ripristinato in una cartella radice, fornire i valori della cartella di destinazione come stringa vuota.
* **SourceFilePath**: percorso assoluto del file da ripristinare all'interno della condivisione file, sotto forma di stringa. Questo percorso è uguale a quello usato nel cmdlet **Get-AzStorageFile** di PowerShell.
* **SourceFileType**: indica se è selezionato un file o una directory. Accetta **Directory** o **File**.
* **ResolveConflict**: istruzione in caso di conflitto con i dati ripristinati. Accetta **Overwrite** o **skip**.

I parametri aggiuntivi riguardano solo il singolo file da ripristinare.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

Questo comando restituisce anche un processo con un ID di cui tener traccia, come illustrato in precedenza.

### <a name="restore-azure-file-shares-to-the-original-location"></a>Ripristinare condivisioni file di Azure nel percorso originale

Quando si esegue il ripristino in un percorso originale, non è necessario specificare tutti i parametri relativi alla destinazione. Deve essere fornito solo **ResolveConflict**.

#### <a name="overwrite-an-azure-file-share"></a>Sovrascrivere una condivisione file di Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

#### <a name="overwrite-an-azure-file"></a>Sovrascrivere un file di Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="track-backup-and-restore-jobs"></a>Tenere traccia dei processi di backup e ripristino

Le operazioni di backup e ripristino su richiesta restituiscono un processo con un ID, come mostrato nella sezione precedente ["Attivare un backup su richiesta"](#trigger-an-on-demand-backup). Usare il cmdlet **Get-AzRecoveryServicesBackupJobDetails** per tener traccia dello stato del processo e recuperare altri dettagli.

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
