---
title: Distribuire e gestire i backup per le macchina virtuali distribuite con Resource Manager utilizzando PowerShell
description: Utilizzare PowerShell per distribuire e gestire i backup per le macchine virtuali distribuite con Resource Manager in Azure
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 10/20/2018
ms.author: markgal
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c29a91a40df34ecd9270d5805209d361cf990754
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2018
ms.locfileid: "49638036"
---
# <a name="use-powershell-to-back-up-and-restore-virtual-machines"></a>Usare PowerShell per il backup e il ripristino di macchine virtuali

Questo articolo illustra come usare i cmdlet di Azure PowerShell per eseguire il backup e il ripristino di una macchina virtuale di Azure da un insieme di credenziali di Servizi di ripristino. Un insieme di credenziali di Servizi di ripristino è una risorsa di Azure Resource Manager usata per proteggere dati e risorse nei servizi Backup di Azure e Azure Site Recovery. 

> [!NOTE]
> Azure offre due modelli di distribuzione per creare e usare le risorse: [Resource Manager e distribuzione classica](../azure-resource-manager/resource-manager-deployment-model.md). Questo articolo si riferisce alle VM create tramite il modello Resource Manager.
>
>

In questo articolo viene illustrato come usare PowerShell per proteggere una macchina virtuale e ripristinare i dati da un punto di ripristino.

## <a name="concepts"></a>Concetti
Se non si ha familiarità con il servizio Backup di Azure, vedere l'articolo [Informazioni su Backup di Azure](backup-introduction-to-azure-backup.md) per una panoramica del servizio. Prima di iniziare, assicurarsi di conoscere i prerequisiti necessari per Backup di Azure e le limitazioni dell'attuale soluzione di backup delle macchine virtuali.

Per un utilizzo efficace di PowerShell, è necessario comprendere la gerarchia degli oggetti e da dove iniziare.

![Gerarchia di oggetti dei servizi di ripristino](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Per visualizzare il riferimento al cmdlet AzureRm.RecoveryServices.Backup di PowerShell, vedere [Azure Backup - Recovery Services Cmdlets (Backup di Azure: i cmdlet dei servizi di ripristino)](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup) nella libreria di Azure.

## <a name="setup-and-registration"></a>Installazione e registrazione

Per iniziare:

1. [Scaricare la versione più recente di PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) (la versione minima richiesta è: 1.4.0)

2. Cercare i cmdlet PowerShell di Azure Backup disponibili digitando il comando seguente:
   
    ```powershell
    Get-Command *azurermrecoveryservices*
    ```    
    Verranno visualizzati alias e cmdlet per Backup di Azure, Azure Site Recovery e l'insieme di credenziali di Servizi di ripristino. L'immagine seguente è un esempio di quanto verrà visualizzato. Non è l'elenco completo dei cmdlet.

    ![Elenco di Servizi di ripristino](./media/backup-azure-vms-automation/list-of-recoveryservices-ps.png)

3. Accedere all'account Azure tramite **Connect-AzureRmAccount**. Questo cmdlet visualizza una pagina Web che richiede le credenziali dell'account:

    * In alternativa, è possibile includere le credenziali dell'account come parametro nel cmdlet **Connect-AzureRmAccount**, usando il parametro **-Credential**.
    * Se si è un partner CSP che opera per conto di un tenant, è necessario specificare il cliente come tenant usando l'ID tenant o il nome di dominio primario del tenant. Ad esempio: **Connect-AzureRmAccount -Tenant "fabrikam.com"**

4. Associare la sottoscrizione che si vuole usare all'account perché un account può avere molte sottoscrizioni:

    ```powershell
    Select-AzureRmSubscription -SubscriptionName $SubscriptionName
    ```

5. Se si sta usando Backup di Azure per la prima volta, è necessario usare il cmdlet **[Register-AzureRmResourceProvider](http://docs.microsoft.com/powershell/module/azurerm.resources/register-azurermresourceprovider)** per registrare il provider dei Servizi di ripristino di Azure con la propria sottoscrizione.

    ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. È possibile verificare che i provider siano stati registrati correttamente usando i comandi seguenti:
    ```powershell
    Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ``` 
    Nell'output del comando **RegistrationState** dovrebbe essere modificato in **Registered**. In caso contrario, eseguire di nuovo il cmdlet **[Register-AzureRmResourceProvider](http://docs.microsoft.com/powershell/module/azurerm.resources/register-azurermresourceprovider)**.

Le attività seguenti possono essere automatizzate tramite PowerShell:

* [Creare un insieme di credenziali di Servizi di ripristino](backup-azure-vms-automation.md#create-a-recovery-services-vault)
* [Eseguire un backup delle VM di Azure](backup-azure-vms-automation.md#back-up-azure-vms)
* [Attivare un processo di backup](backup-azure-vms-automation.md#trigger-a-backup-job)
* [Monitorare un processo di backup](backup-azure-vms-automation.md#monitoring-a-backup-job)
* [Ripristinare una macchina virtuale di Azure](backup-azure-vms-automation.md#restore-an-azure-vm)

## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

Nei passaggi seguenti viene descritto come creare un insieme di credenziali dei servizi di ripristino. Un insieme di credenziali dei servizi di ripristino è diverso da un insieme di credenziali di backup.

1. L'insieme di credenziali dei Servizi di ripristino è una risorsa Resource Manager, pertanto è necessario inserirlo all'interno di un gruppo di risorse. È possibile usare un gruppo di risorse esistente o crearne uno con il cmdlet **[New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup)**. Quando si crea un nuovo gruppo di risorse, è necessario specificare il nome e il percorso per il gruppo di risorse.  

    ```powershell
    New-AzureRmResourceGroup -Name "test-rg" -Location "West US"
    ```
2. Usare il cmdlet **[New-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/new-azurermrecoveryservicesvault)** per creare un nuovo insieme di credenziali dei Servizi di ripristino. Assicurarsi di specificare per l'insieme di credenziali lo stesso percorso usato per il gruppo di risorse.

    ```powershell
    New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. Specificare il tipo di ridondanza di archiviazione da usare, ad esempio [archiviazione con ridondanza locale (LRS)](../storage/common/storage-redundancy-lrs.md) o [archiviazione con ridondanza geografica (GRS)](../storage/common/storage-redundancy-grs.md). Nell'esempio seguente l'opzione BackupStorageRedundancy per testvault è impostata su GeoRedundant.

    ```powershell
    $vault1 = Get-AzureRmRecoveryServicesVault -Name "testvault"
    Set-AzureRmRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > Molti cmdlet di Backup di Azure richiedono l'oggetto dell'insieme di credenziali dei servizi di ripristino come input. Per questo motivo, è utile archiviare l'oggetto dell'insieme di credenziali dei servizi di ripristino di Backup in una variabile.
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>Visualizzare gli insiemi di credenziali in un abbonamento

Per visualizzare tutti gli insiemi di credenziali disponibili nella sottoscrizione, usare **[Get-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/get-azurermrecoveryservicesvault)**:

```powershell
Get-AzureRmRecoveryServicesVault
```

L'output è simile all'esempio seguente. Si noti che vengono inclusi gli elementi ResourceGroupName e Location associati.

```
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```


## <a name="back-up-azure-vms"></a>Eseguire il backup delle VM di Azure

Usare un insieme di credenziali di Servizi di ripristino per proteggere le macchine virtuali. Prima di applicare la protezione, impostare il contesto dell'insieme di credenziali (tipo di dati protetti nell'insieme di credenziali) e verificare i criteri di protezione. I criteri di protezione rappresentano la pianificazione per l'esecuzione dei processi di backup e il periodo di conservazione di ogni snapshot del backup.

### <a name="set-vault-context"></a>Impostazione del contesto dell'insieme di credenziali

Prima di abilitare la protezione per una macchina virtuale, usare **[Set AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)** per impostare il contesto dell'insieme di credenziali. Una volta impostato il contesto dell'insieme di credenziali, si applica a tutti i cmdlet successivi. L'esempio seguente imposta il contesto per l'insieme di credenziali, *testvault*.

```powershell
Get-AzureRmRecoveryServicesVault -Name "testvault" | Set-AzureRmRecoveryServicesVaultContext
```

### <a name="create-a-protection-policy"></a>Creare i criteri di protezione

Quando si crea un insieme di credenziali di Servizi di ripristino, questo è dotato di protezione predefinita e criteri di conservazione. I criteri di protezione predefinita attivano un processo di backup ogni giorno all'ora specificata. I criteri di conservazione predefiniti consentono di mantenere il punto di recupero giornaliero per 30 giorni. È possibile usare i criteri predefiniti per proteggere rapidamente la macchina virtuale e modificare i criteri in un secondo momento con dettagli diversi.

Per visualizzare i criteri di protezione disponibili nell'insieme di credenziali, usare **[Get-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupprotectionpolicy)**. È possibile usare questo cmdlet per ottenere un criterio specifico o per visualizzare i criteri associati a un tipo di carico di lavoro. L'esempio seguente ottiene i criteri per il tipo di carico di lavoro AzureVM.

```powershell
Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
```

L'output è simile all'esempio seguente:

```
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [!NOTE]
> Il fuso orario del campo BackupTime in PowerShell è UTC. Tuttavia, l'orario di backup nel portale di Azure è allineato al fuso orario locale.
>
>

I criteri di protezione del backup sono associati almeno a un criterio di conservazione. I criteri di conservazione definiscono per quanto tempo un punto di recupero viene mantenuto prima dell'eliminazione. Per visualizzare il criterio di conservazione predefinito usare **[Get-AzureRmRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupretentionpolicyobject)**.  Nello stesso modo, per ottenere il criterio di pianificazione predefinito usare **[Get-AzureRmRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupschedulepolicyobject)**. Il cmdlet **[New-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)** crea un oggetto di PowerShell che contiene le informazioni relative ai criteri di backup. Gli oggetti dei criteri di conservazione e di pianificazione vengono usati come input per il cmdlet **[New-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)**. Nell'esempio seguente i criteri di pianificazione e i criteri di conservazione vengono archiviati nelle variabili. L'esempio usa tali variabili per definire i parametri durante la creazione di un criterio di protezione, *NewPolicy*.

```powershell
$schPol = Get-AzureRmRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
New-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType "AzureVM" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

L'output è simile all'esempio seguente:

```
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```

### <a name="enable-protection"></a>Abilitare la protezione

Dopo aver definito i criteri di protezione è necessario abilitarli per un elemento. Per abilitare la protezione usare **[Enable-AzureRmRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/enable-azurermrecoveryservicesbackupprotection)**. Per abilitare la protezione sono necessari due oggetti, l'elemento e i criteri. Dopo aver associato i criteri all'insieme di credenziali, il flusso di lavoro di backup verrà attivato al momento definito nella pianificazione dei criteri.

Gli esempi seguenti abilitano la protezione per l'elemento V2VM usando i criteri NewPolicy. Gli esempi variano a seconda del fatto che la macchina virtuale sia crittografata o meno e in base al tipo di crittografia. 

Per abilitare la protezione in **macchine virtuali di Resource Manager non crittografate**:

```powershell
$pol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Per abilitare la protezione in **macchine virtuali crittografate con BEK e KEK** è necessario concedere al servizio Backup di Azure le autorizzazioni necessarie per la lettura di chiavi e segreti dall'insieme di credenziali delle chiavi.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Per abilitare la protezione in **macchine virtuali crittografate solo con BEK** è necessario concedere al servizio Backup di Azure le autorizzazioni necessarie per la lettura dei segreti dall'insieme di credenziali delle chiavi.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToSecrets backup,get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

> [!NOTE]
> Se si usa il cloud di Azure per enti pubblici, usare il valore ff281ffe-705c-4f53-9f37-a40e6f2c68f3 per il parametro **-ServicePrincipalName** nel cmdlet [Set-AzureRmKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy).
>
>

Per abilitare la protezione in una macchina virtuale classica:

```powershell
$pol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V1VM" -ServiceName "ServiceName1"
```

### <a name="modify-a-protection-policy"></a>Modificare i criteri di protezione

Per modificare i criteri di protezione, usare [Set-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/set-azurermrecoveryservicesbackupprotectionpolicy) per modificare gli oggetti SchedulePolicy o RetentionPolicy.

Nell'esempio seguente viene modificato il punto di recupero a 365 giorni.

```powershell
$retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
$retPol.DailySchedule.DurationCountInDays = 365
$pol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Set-AzureRmRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy $RetPol
```

## <a name="trigger-a-backup"></a>Attivare un backup

Usare **[Backup-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem)** per attivare un processo di backup. Se si tratta del backup iniziale, è un backup completo. I backup successivi saranno incrementali. Per impostare il contesto dell'insieme di credenziali prima di attivare il processo di backup, usare **[Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)**. L'esempio seguente presuppone che il contesto dell'insieme di credenziali sia già stato impostato.

```powershell
$namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
$job = Backup-AzureRmRecoveryServicesBackupItem -Item $item
```

L'output è simile all'esempio seguente:

```
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup              InProgress          4/23/2016                  5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [!NOTE]
> Il fuso orario dei campi StartTime ed EndTime in PowerShell è UTC. Tuttavia, l'orario visualizzato nel portale di Azure è allineato al fuso orario locale.
>
>

## <a name="monitoring-a-backup-job"></a>Monitoraggio di un processo di backup

È possibile monitorare le operazioni a esecuzione prolungata, ad esempio i processi di backup, senza usare il portale di Azure. Per ottenere lo stato di un processo in corso, usare il cmdlet **[Get-AzureRmRecoveryservicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjob)**. Questo cmdlet ottiene i processi di backup per un insieme di credenziali specifico e tale insieme di credenziali è indicato nel relativo contesto. L'esempio seguente ottiene lo stato di un processo in corso sotto forma di matrice e archivia lo stato nella variabile $joblist.

```powershell
$joblist = Get-AzureRmRecoveryservicesBackupJob –Status "InProgress"
$joblist[0]
```

L'output è simile all'esempio seguente:

```
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016                5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Invece di eseguire il polling dei processi per ottenere lo stato di completamento, operazione non necessaria che prevede codice aggiuntivo, usare il cmdlet **[Wait-AzureRmRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob)**. Questo cmdlet sospende l'esecuzione fino al completamento del processo o fino a quando non viene raggiunto il valore di timeout specificato.

```powershell
Wait-AzureRmRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200
```

## <a name="restore-an-azure-vm"></a>Ripristinare una macchina virtuale di Azure

Tra il ripristino di una macchina virtuale tramite il portale di Azure e il ripristino di una macchina virtuale tramite PowerShell c'è un'importante differenza. Con PowerShell l'operazione di ripristino è completata quando sono stati creati i dischi e le informazioni sulla configurazione dal punto di ripristino L'operazione di ripristino non crea la macchina virtuale. Per creare una macchina virtuale dal disco, vedere la sezione [Creare la macchina virtuale da dischi ripristinati](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). Se non si vuole ripristinare l'intera macchina virtuale, ma si vuole ripristinare o recuperare alcuni file da un backup della macchina virtuale di Azure, vedere la [sezione relativa al recupero di file](backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup).

> [!Tip]
> L'operazione di ripristino non crea la macchina virtuale.
>
>

Il grafico seguente mostra la gerarchia degli oggetti da RecoveryServicesVault fino a BackupRecoveryPoint.

![Gerarchia di oggetti dei servizi di ripristino con BackupContainer](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

Per ripristinare i dati di backup, identificare l'elemento sottoposto a backup e il punto di ripristino che contiene i dati temporizzati. Usare **[Restore-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/restore-azurermrecoveryservicesbackupitem)** per ripristinare i dati dall'insieme di credenziali nel proprio account.

I passaggi di base per ripristinare una macchina virtuale di Azure sono:

* Selezionare la macchina virtuale.
* Scegliere un punto di ripristino.
* Ripristinare i dischi.
* Creare la macchina virtuale dai dischi archiviati.

### <a name="select-the-vm"></a>Selezionare la macchina virtuale

Per ottenere l'oggetto di PowerShell che identifica l'elemento di backup corretto, iniziare dal contenitore nell'insieme di credenziali e procedere verso il basso nella gerarchia degli oggetti. Per selezionare il contenitore che rappresenta la macchina virtuale, usare il cmdlet **[Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer)** e inviarlo tramite pipe al cmdlet **[Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupitem)**.

```powershell
$namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>Scegliere un punto di ripristino

Usare il cmdlet **[Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprecoverypoint)** per elencare tutti i punti di recupero dell'elemento di backup. Quindi scegliere il punto di ripristino per ripristinare. Se non si sa quale punto di ripristino usare, è consigliabile scegliere il punto più recente RecoveryPointType = AppConsistent nell'elenco.

Nello script seguente la variabile **$rp** è una matrice di punti di recupero per l'elemento di backup selezionato negli ultimi sette giorni. La matrice viene ordinata in ordine inverso di tempo con il punto di ripristino più recente in posizione 0 nell'indice. Per scegliere il punto di ripristino, usare l'indicizzazione standard della matrice di PowerShell. Nell'esempio $rp [0] seleziona il punto di ripristino più recente.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
$rp[0]
```

L'output è simile all'esempio seguente:

```
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="restore-the-disks"></a>Ripristinare i dischi

Per ripristinare i dati e la configurazione di un elemento di backup a un punto di recupero, usare il cmdlet **[Restore-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/restore-azurermrecoveryservicesbackupitem)**. Dopo aver identificato un punto di ripristino, usarlo come valore per il parametro **-RecoveryPoint**. Nell'esempio precedente **$rp[0]** è il punto di ripristino da usare. Nell'esempio di codice seguente **$rp[0]** è il punto di ripristino da usare per il ripristino del disco.

Per ripristinare i dischi e le informazioni di configurazione:

```powershell
$restorejob = Restore-AzureRmRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG"
$restorejob
```
#### <a name="restore-managed-disks"></a>Ripristinare i dischi gestiti

> [!NOTE]
> Se la macchina virtuale sottoposta a backup dispone di dischi gestiti e si desidera ripristinarli come dischi gestiti, Microsoft ha introdotto la funzionalità da Azure Powershell v 6.7.0. in poi
>
>

Indicare un parametro aggiuntivo **TargetResourceGroupName** per specificare il gruppo di risorse in cui verranno ripristinati i dischi gestiti.


```powershell
$restorejob = Restore-AzureRmRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks"
```

Il file **VMConfig.JSON** verrà ripristinato nell'account di archiviazione e i dischi gestiti verranno ripristinati nel gruppo di risorse di destinazione specificato.


L'output è simile all'esempio seguente:
```
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Usare il cmdlet **[Wait-AzureRmRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob)** per attendere il completamento del processo di ripristino.

```powershell
Wait-AzureRmRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

Dopo il completamento del processo di ripristino, usare il cmdlet **[Get-AzureRmRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjobdetails)** per ottenere i dettagli dell'operazione di ripristino. La proprietà JobDetails contiene le informazioni necessarie per ricreare la macchina virtuale.

```powershell
$restorejob = Get-AzureRmRecoveryServicesBackupJob -Job $restorejob
$details = Get-AzureRmRecoveryServicesBackupJobDetails -Job $restorejob
```

Dopo aver ripristinato i dischi, passare alla sezione successiva per creare la macchina virtuale.

## <a name="create-a-vm-from-restored-disks"></a>Creare una macchina virtuale da dischi ripristinati

Dopo aver ripristinato i dischi, usare la procedura seguente per creare e configurare la macchina virtuale dal disco.

> [!NOTE]
> Per creare macchine virtuali crittografate da dischi ripristinati, il ruolo di Azure deve disporre dell'autorizzazione per eseguire l'azione, ovvero **Microsoft.KeyVault/vaults/deploy/action**. Se il ruolo non dispone di questa autorizzazione, crearne uno personalizzato con questa azione. Per altre informazioni, vedere [Ruoli personalizzati nel Controllo degli accessi in base al ruolo di Azure](../role-based-access-control/custom-roles.md).
>
>

1. Ricercare i dettagli del processo nelle proprietà del disco ripristinato.

   ```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $configBlobName = $properties["Config Blob Name"]
   ```

2. Impostare il contesto di archiviazione di Azure e ripristinare il file di configurazione JSON.

   ```powershell
   Set-AzureRmCurrentStorageAccount -Name $storageaccountname -ResourceGroupName "testvault"
   $destination_path = "C:\vmconfig.json"
   Get-AzureStorageBlobContent -Container $containerName -Blob $configBlobName -Destination $destination_path
   $obj = ((Get-Content -Path $destination_path -Raw -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
   ```

3. Usare il file di configurazione JSON per creare la configurazione della macchina virtuale.

   ```powershell
   $vm = New-AzureRmVMConfig -VMSize $obj.'properties.hardwareProfile'.vmSize -VMName "testrestore"
   ```

4. Collegare il disco del sistema operativo e i dischi dei dati. Questo passaggio include esempi di varie configurazioni di macchine virtuali gestite e crittografate. Usare l'esempio adatto alla configurazione della propria macchina virtuale.

   * **Macchine virtuali non gestite e non crittografate**: usare l'esempio seguente per macchine virtuali non gestite e non crittografate.

       ```powershell
       Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
       $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
       foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
       {
        $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
       }
       ```

   * **Macchine virtuali non gestite e crittografate (solo BEK)**: per le macchine virtuali non gestite e crittografate (solo con BEK) è necessario ripristinare il segreto nell'insieme di credenziali delle chiavi prima di collegare i dischi. Per altre informazioni, vedere l'articolo [Ripristinare una macchina virtuale crittografata da un punto di ripristino di Backup di Azure](backup-azure-restore-key-secret.md). L'esempio seguente illustra come collegare dischi del sistema operativo e i dati per le macchine virtuali crittografate. Durante l'impostazione del disco del sistema operativo, assicurarsi di indicare il tipo di sistema operativo pertinente.

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $dekUrl = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows/Linux
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
      foreach($dd in $obj.'properties.storageProfile'.dataDisks)
      {
       $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
      ```
      Usare il comando seguente per abilitare manualmente la crittografia per i dischi dati.

      ```powershell
      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $dekUrl -VolumeType Data
      ```

   * **Macchine virtuali non gestite e crittografate (BEK e KEK)**: per le macchine virtuali non gestite e crittografate (con BEK e KEK) ripristinare la chiave e il segreto nell'insieme di credenziali delle chiavi prima di collegare i dischi. Per altre informazioni, vedere l'articolo [Ripristinare una macchina virtuale crittografata da un punto di ripristino di Backup di Azure](backup-azure-restore-key-secret.md). L'esempio seguente illustra come collegare dischi del sistema operativo e i dati per le macchine virtuali crittografate.

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
      foreach($dd in $obj.'properties.storageProfile'.dataDisks)
       {
       $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
       }
      ```

      Usare il comando seguente per abilitare manualmente la crittografia per i dischi dati.

      ```powershell
      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $dekUrl -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

   * **Macchine virtuali gestite e non crittografate**: per le macchine virtuali gestite e non crittografate, collegare i dischi gestiti ripristinati. Per informazioni dettagliate, vedere l'articolo [Collegare un disco dati a una macchina virtuale Windows con PowerShell](../virtual-machines/windows/attach-disk-ps.md).

   * **Macchine virtuali gestite e crittografate (solo BEK)**: per le macchine virtuali gestite e crittografate (solo con BEK) collegare i dischi gestiti ripristinati. Per informazioni dettagliate, vedere l'articolo [Collegare un disco dati a una macchina virtuale Windows con PowerShell](../virtual-machines/windows/attach-disk-ps.md).
   
      Usare il comando seguente per abilitare manualmente la crittografia per i dischi dati.

       ```powershell
       Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -VolumeType Data
       ```

   * **Macchine virtuali gestite e crittografate (BEK e KEK)**: per le macchine virtuali gestite e crittografate (con BEK e KEK) collegare i dischi gestiti ripristinati. Per informazioni dettagliate, vedere l'articolo [Collegare un disco dati a una macchina virtuale Windows con PowerShell](../virtual-machines/windows/attach-disk-ps.md). 

      Usare il comando seguente per abilitare manualmente la crittografia per i dischi dati.

      ```powershell
      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $dekUrl -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

5. Configurare le impostazioni di rete.

    ```powershell
    $nicName="p1234"
    $pip = New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName "test" -Location "WestUS" -AllocationMethod Dynamic
    $virtualNetwork = New-AzureRmVirtualNetwork -ResourceGroupName "test" -Location "WestUS" -Name "testvNET" -AddressPrefix 10.0.0.0/16
    $virtualNetwork | Set-AzureRmVirtualNetwork
    $vnet = Get-AzureRmVirtualNetwork -Name "testvNET" -ResourceGroupName "test"
    $subnetindex=0
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName "test" -Location "WestUS" -SubnetId $vnet.Subnets[$subnetindex].Id -PublicIpAddressId $pip.Id
    $vm=Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
    ```
6. Creare la macchina virtuale.

    ```powershell  
    New-AzureRmVM -ResourceGroupName "test" -Location "WestUS" -VM $vm
    ```

## <a name="restore-files-from-an-azure-vm-backup"></a>Ripristinare file da un backup della macchina virtuale di Azure

Oltre al ripristino di dischi è anche possibile ripristinare singoli file da un backup della macchina virtuale di Azure. La funzionalità di ripristino dei file consente di accedere a tutti i file inclusi in un punto di ripristino. Gestire i file tramite Esplora file come si farebbe con i file normali.

La procedura di base per il ripristino di un file dal backup di una macchina virtuale di Azure si articola nei passaggi seguenti:

* Selezionare la macchina virtuale
* Scegliere un punto di ripristino
* Montare i dischi del punto di ripristino
* Copiare i file necessari
* Smontare il disco


### <a name="select-the-vm"></a>Selezionare la macchina virtuale

Per ottenere l'oggetto di PowerShell che identifica l'elemento di backup corretto, iniziare dal contenitore nell'insieme di credenziali e procedere verso il basso nella gerarchia degli oggetti. Per selezionare il contenitore che rappresenta la macchina virtuale, usare il cmdlet **[Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer)** e inviarlo tramite pipe al cmdlet **[Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupitem)**.

```powershell
$namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>Scegliere un punto di ripristino

Usare il cmdlet **[Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprecoverypoint)** per elencare tutti i punti di recupero dell'elemento di backup. Quindi scegliere il punto di ripristino per ripristinare. Se non si sa quale punto di ripristino usare, è consigliabile scegliere il punto più recente RecoveryPointType = AppConsistent nell'elenco.

Nello script seguente la variabile **$rp** è una matrice di punti di recupero per l'elemento di backup selezionato negli ultimi sette giorni. La matrice viene ordinata in ordine inverso di tempo con il punto di ripristino più recente in posizione 0 nell'indice. Per scegliere il punto di ripristino, usare l'indicizzazione standard della matrice di PowerShell. Nell'esempio $rp [0] seleziona il punto di ripristino più recente.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
$rp[0]
```

L'output è simile all'esempio seguente:

```
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="mount-the-disks-of-recovery-point"></a>Montare i dischi del punto di ripristino

Usare il cmdlet **[Get-AzureRmRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprpmountscript)** per ottenere lo script necessario per montare tutti i dischi del punto di ripristino.

> [!NOTE]
> I dischi sono montati come dischi collegati iSCSI al computer in cui viene eseguito lo script. Il montaggio avviene immediatamente e non si incorre in alcun addebito.
>
>

```powershell
Get-AzureRmRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]
```

L'output è simile all'esempio seguente:

```
OsType  Password        Filename
------  --------        --------
Windows e3632984e51f496 V2VM_wus2_8287309959960546283_451516692429_cbd6061f7fc543c489f1974d33659fed07a6e0c2e08740.exe
```

Eseguire lo script nel computer in cui si vogliono recuperare i file. Per eseguire lo script è necessario immettere la password specificata. Dopo aver collegato i dischi, usare Esplora risorse per esplorare i nuovi volumi e file. Per altre informazioni, vedere l'articolo [Ripristinare i file da un backup della macchina virtuale di Azure](backup-azure-restore-files-from-vm.md).

### <a name="unmount-the-disks"></a>Smontare i disco

Dopo aver copiato i file necessari, usare **[Disable-AzureRmRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/disable-azurermrecoveryservicesbackuprpmountscript?view=azurermps-5.0.0)** per smontare i dischi. Assicurarsi di smontare i dischi in modo che l'accesso ai file del punto di ripristino venga rimosso.

```powershell
Disable-AzureRmRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]
```

## <a name="next-steps"></a>Passaggi successivi

Se si preferisce usare PowerShell per interagire con le risorse di Azure, vedere l'articolo PowerShell [Distribuire e gestire il servizio di backup per Windows Server](backup-client-automation.md). Se si gestiscono i backup DPM, vedere l'articolo [Distribuire e gestire il backup per DPM](backup-dpm-automation.md). Entrambi gli articoli prevedono due versioni: una per le distribuzioni con Resource Manager, l'altra per le distribuzioni classiche.  
