---
title: Distribuire e gestire i backup per le macchine virtuali distribuite con Resource Manager usando PowerShell | Microsoft Docs
description: Utilizzare PowerShell per distribuire e gestire i backup per le macchine virtuali distribuite con Resource Manager in Azure
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
ms.assetid: 68606e4f-536d-4eac-9f80-8a198ea94d52
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/20/2017
ms.author: markgal;trinadhk;pullabhk
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 474c5a6d0e7d3647ca14cb61e7b2718c99fdfa72
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2018
---
# <a name="use-azurermrecoveryservicesbackup-cmdlets-to-back-up-virtual-machines"></a>Usare i cmdlet AzureRM.RecoveryServices.Backup per eseguire il backup di macchine virtuali

In questo articolo viene illustrato come usare i cmdlet di Azure PowerShell per eseguire il backup e ripristinare una macchina virtuale (VM) di Azure da un insieme di credenziali dei servizi di ripristino. Un insieme di credenziali dei servizi di ripristino è una risorsa di Resource Manager di Azure e viene utilizzato per proteggere i dati e asset nei servizi di Backup di Azure e di Azure Site Recovery. È possibile usare un insieme di credenziali di Servizi di ripristino per proteggere le macchine virtuali distribuite con Service Manager di Azure e Azure Resource Manager.

> [!NOTE]
> Azure offre due modelli di distribuzione per creare e usare le risorse: [Resource Manager e distribuzione classica](../azure-resource-manager/resource-manager-deployment-model.md). Questo articolo si riferisce alle VM create tramite il modello Resource Manager.
>
>

In questo articolo viene illustrato come usare PowerShell per proteggere una macchina virtuale e ripristinare i dati da un punto di ripristino.

## <a name="concepts"></a>Concetti
Se non si ha familiarità con il servizio di Backup di Azure, vedere l'argomento [Che cos'è Backup di Azure?](backup-introduction-to-azure-backup.md) per una panoramica sul servizio Prima di iniziare, assicurarsi di avere gli elementi di base per i prerequisiti necessari per usare il servizio Backup di Azure, e le limitazioni della soluzione attuale di backup della macchina virtuale.

Per un utilizzo efficace di PowerShell, è necessario comprendere la gerarchia degli oggetti e da dove iniziare.

![Gerarchia di oggetti dei servizi di ripristino](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Per visualizzare il riferimento al cmdlet AzureRm.RecoveryServices.Backup di PowerShell, vedere [Azure Backup - Recovery Services Cmdlets (Backup di Azure: i cmdlet dei servizi di ripristino)](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup) nella libreria di Azure.

## <a name="setup-and-registration"></a>Installazione e registrazione
Per iniziare:

1. [Scaricare la versione più recente di PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) (la versione minima richiesta è: 1.4.0)
2. Cercare i cmdlet PowerShell di Azure Backup disponibili digitando il comando seguente:

```
PS C:\> Get-Command *azurermrecoveryservices*

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Backup-AzureRmRecoveryServicesBackupItem           1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Disable-AzureRmRecoveryServicesBackupProtection    1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Enable-AzureRmRecoveryServicesBackupProtection     1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupContainer         1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupItem              1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupJob               1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupJobDetails        1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupManagementServer  1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupProperties        1.4.0      AzureRM.RecoveryServices
Cmdlet          Get-AzureRmRecoveryServicesBackupProtectionPolicy  1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRMRecoveryServicesBackupRecoveryPoint     1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupRetentionPolic... 1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupSchedulePolicy... 1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesVault                   1.4.0      AzureRM.RecoveryServices
Cmdlet          Get-AzureRmRecoveryServicesVaultSettingsFile       1.4.0      AzureRM.RecoveryServices
Cmdlet          New-AzureRmRecoveryServicesBackupProtectionPolicy  1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          New-AzureRmRecoveryServicesVault                   1.4.0      AzureRM.RecoveryServices
Cmdlet          Remove-AzureRmRecoveryServicesProtectionPolicy     1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Remove-AzureRmRecoveryServicesVault                1.4.0      AzureRM.RecoveryServices
Cmdlet          Restore-AzureRMRecoveryServicesBackupItem          1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Set-AzureRmRecoveryServicesBackupProperties        1.4.0      AzureRM.RecoveryServices
Cmdlet          Set-AzureRmRecoveryServicesBackupProtectionPolicy  1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Set-AzureRmRecoveryServicesVaultContext            1.4.0      AzureRM.RecoveryServices
Cmdlet          Stop-AzureRmRecoveryServicesBackupJob              1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Unregister-AzureRmRecoveryServicesBackupContainer  1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Unregister-AzureRmRecoveryServicesBackupManagem... 1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Wait-AzureRmRecoveryServicesBackupJob              1.4.0      AzureRM.RecoveryServices.Backup
```
3. Accedi a di Azure account tramite **accesso AzureRmAccount**. Questo cmdlet consente di visualizzare una pagina web viene chiesto di immettere le credenziali dell'account: 
    - In alternativa, è possibile includere le credenziali dell'account come parametro nel cmdlet **Login-AzureRmAccount**, usando il parametro **-Credential**.
    - Se si è un partner CSP che opera per conto di un tenant, è necessario specificare il cliente come tenant usando l'ID tenant o il nome di dominio primario del tenant. Ad esempio: **Login-AzureRmAccount -Tenant "fabrikam.com"**
4. Associare la sottoscrizione che si vuole usare all'account perché un account può avere molte sottoscrizioni:

    ```
    PS C:\> Select-AzureRmSubscription -SubscriptionName $SubscriptionName
    ```

5. Se si sta usando Backup di Azure per la prima volta, è necessario usare il cmdlet **[Register-AzureRmResourceProvider](http://docs.microsoft.com/powershell/module/azurerm.resources/register-azurermresourceprovider)** per registrare il provider dei Servizi di ripristino di Azure con la propria sottoscrizione.

    ```
    PS C:\> Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    PS C:\> Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Backup"
    ```

6. È possibile verificare che i provider registrati correttamente, usando i seguenti comandi:
    ```
    PS C:\> Get-AzureRmResourceProvider -ProviderNamespace  "Microsoft.RecoveryServices"
    PS C:\> Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Backup"
    ``` 
Nell'output del comando, il **RegistrationState** imposti su **registrati**. In caso contrario, solo eseguire di nuovo il  **[registro AzureRmResourceProvider](http://docs.microsoft.com/powershell/module/azurerm.resources/register-azurermresourceprovider)**  cmdlet illustrato in precedenza.

Le attività seguenti possono essere automatizzate tramite PowerShell:

* Creare un insieme di credenziali di Servizi di ripristino
* Eseguire il backup delle VM di Azure
* Attivare un processo di backup
* Monitorare un processo di backup
* Ripristinare una macchina virtuale di Azure

## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali dei servizi di ripristino
Nei passaggi seguenti viene descritto come creare un insieme di credenziali dei servizi di ripristino. Un insieme di credenziali dei servizi di ripristino è diverso da un insieme di credenziali di backup.

1. L'insieme di credenziali dei Servizi di ripristino è una risorsa Resource Manager, pertanto è necessario inserirlo all'interno di un gruppo di risorse. È possibile usare un gruppo di risorse esistente o crearne uno con il cmdlet **[New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup)**. Quando si crea un nuovo gruppo di risorse, è necessario specificare il nome e il percorso per il gruppo di risorse.  

    ```
    PS C:\> New-AzureRmResourceGroup -Name "test-rg" -Location "West US"
    ```
2. Usare il cmdlet **[New-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/new-azurermrecoveryservicesvault)** per creare un nuovo insieme di credenziali dei Servizi di ripristino. Assicurarsi di specificare per l'insieme di credenziali lo stesso percorso usato per il gruppo di risorse.

    ```
    PS C:\> New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
    ```
3. Specificare il tipo di ridondanza di archiviazione da usare, ad esempio [archiviazione con ridondanza locale (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage) o [archiviazione con ridondanza geografica (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage). Nell'esempio seguente l'opzione BackupStorageRedundancy per testvault è impostata su GeoRedundant.

    ```
    PS C:\> $vault1 = Get-AzureRmRecoveryServicesVault -Name "testvault"
    PS C:\> Set-AzureRmRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > Molti cmdlet di Backup di Azure richiedono l'oggetto dell'insieme di credenziali dei servizi di ripristino come input. Per questo motivo, è utile archiviare l'oggetto dell'insieme di credenziali dei servizi di ripristino di Backup in una variabile.
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>Visualizzare gli insiemi di credenziali in un abbonamento
Usare **[Get-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/get-azurermrecoveryservicesvault)** per visualizzare l'elenco di tutti gli insiemi di credenziali della sottoscrizione corrente. È possibile usare questo comando per verificare che sia stato creato un nuovo insieme di credenziali o per vedere gli insiemi di credenziali nella sottoscrizione.

Eseguire il comando Get-AzureRmRecoveryServicesVault per visualizzare tutti gli insiemi di credenziali disponibili nella sottoscrizione. L'esempio seguente mostra le informazioni visualizzate per ogni insieme di credenziali.

```
PS C:\> Get-AzureRmRecoveryServicesVault
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

```
PS C:\> Get-AzureRmRecoveryServicesVault -Name "testvault" | Set-AzureRmRecoveryServicesVaultContext
```

### <a name="create-a-protection-policy"></a>Creare i criteri di protezione
Quando si crea un insieme di credenziali di Servizi di ripristino, questo è dotato di protezione predefinita e criteri di conservazione. I criteri di protezione predefinita attivano un processo di backup ogni giorno all'ora specificata. I criteri di conservazione predefiniti consentono di mantenere il punto di recupero giornaliero per 30 giorni. È possibile usare i criteri predefiniti per proteggere rapidamente la macchina virtuale e modificare i criteri in un secondo momento con dettagli diversi.

Per visualizzare l'elenco dei criteri di protezione disponibili nell'insieme di credenziali, usare **[Get-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupprotectionpolicy)**. È possibile usare questo cmdlet per ottenere un criterio specifico o per visualizzare i criteri associati a un tipo di carico di lavoro. L'esempio seguente ottiene i criteri per il tipo di carico di lavoro AzureVM.

```
PS C:\> Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [!NOTE]
> Il fuso orario del campo BackupTime in PowerShell è UTC. Tuttavia, l'orario di backup nel portale di Azure è allineato al fuso orario locale.
>
>

I criteri di protezione del backup sono associati almeno a un criterio di conservazione. I criteri di conservazione definiscono per quanto tempo un punto di recupero viene mantenuto prima dell'eliminazione. Per visualizzare il criterio di conservazione predefinito usare **[Get-AzureRmRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupretentionpolicyobject)**.  Nello stesso modo, per ottenere il criterio di pianificazione predefinito usare **[Get-AzureRmRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupschedulepolicyobject)**. Il cmdlet **[New-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)** crea un oggetto di PowerShell che contiene le informazioni relative ai criteri di backup. Gli oggetti dei criteri di conservazione e di pianificazione vengono usati come input per il cmdlet **[New-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)**. Nell'esempio seguente i criteri di pianificazione e i criteri di conservazione vengono archiviati nelle variabili. L'esempio usa tali variabili per definire i parametri durante la creazione di un criterio di protezione, *NewPolicy*.

```
PS C:\> $schPol = Get-AzureRmRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
PS C:\> $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\> New-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType "AzureVM" -RetentionPolicy $retPol -SchedulePolicy $schPol
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```


### <a name="enable-protection"></a>Abilitare la protezione
Dopo aver definito i criteri di protezione di backup è comunque necessario abilitare i criteri per un elemento. Per abilitare la protezione usare **[Enable-AzureRmRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/enable-azurermrecoveryservicesbackupprotection)**. Per abilitare la protezione sono necessari due oggetti, l'elemento e i criteri. Dopo aver associato i criteri all'insieme di credenziali, il flusso di lavoro di backup verrà attivato al momento definito nella pianificazione dei criteri.

L'esempio seguente abilita la protezione per l'elemento V2VM usando i criteri NewPolicy. Per abilitare la protezione in macchine virtuali di Resource Manager non crittografate

```
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Per abilitare la protezione in macchine virtuali crittografate con BEK e KEK, è necessario assegnare le autorizzazioni per il servizio di Backup di Azure per la lettura di chiavi e segreti dal Key Vault.

```
PS C:\> Set-AzureRmKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Per abilitare la protezione in macchine virtuali crittografate (solo con BEK), è necessario assegnare le autorizzazioni per il servizio di Backup di Azure per la lettura di segreti dall'insieme di credenziali delle chiavi.

```
PS C:\> Set-AzureRmKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToSecrets backup,get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

> [!NOTE]
> Se si usa il cloud di Azure per enti pubblici, usare il valore ff281ffe-705c-4f53-9f37-a40e6f2c68f3 per il parametro **-ServicePrincipalName** nel cmdlet [Set-AzureRmKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy).
>
>

Macchine virtuali classiche

```
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V1VM" -ServiceName "ServiceName1"
```

### <a name="modify-a-protection-policy"></a>Modificare i criteri di protezione
Per modificare i criteri di protezione, usare [Set-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/set-azurermrecoveryservicesbackupprotectionpolicy) per modificare gli oggetti SchedulePolicy o RetentionPolicy.

Nell'esempio seguente viene modificato il punto di recupero a 365 giorni.

```
PS C:\> $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\> $retPol.DailySchedule.DurationCountInDays = 365
PS C:\> $pol= Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Set-AzureRmRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy $RetPol
```

## <a name="trigger-a-backup"></a>Attivare un backup
È possibile usare **[Backup-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem)** per attivare un processo di backup. Se si tratta del backup iniziale, è un backup completo. I backup successivi saranno incrementali. Per impostare il contesto dell'insieme di credenziali prima di attivare il processo di backup, usare **[Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)**. L'esempio seguente presuppone che sia stato impostato il contesto dell'insieme di credenziali.

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
PS C:\> $item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
PS C:\> $job = Backup-AzureRmRecoveryServicesBackupItem -Item $item
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup               InProgress            4/23/2016 5:00:30 PM                       cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [!NOTE]
> Il fuso orario dei campi StartTime ed EndTime in PowerShell è UTC. Tuttavia, l'orario visualizzato nel portale di Azure è allineato al fuso orario locale.
>
>

## <a name="monitoring-a-backup-job"></a>Monitoraggio di un processo di backup
È possibile monitorare le operazioni a esecuzione prolungata, ad esempio i processi di backup, senza usare il portale di Azure. Per ottenere lo stato di un processo in corso, usare il cmdlet **[Get-AzureRmRecoveryservicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjob)**. Questo cmdlet ottiene i processi di backup per un insieme di credenziali specifico e tale insieme di credenziali è indicato nel relativo contesto. L'esempio seguente ottiene lo stato di un processo in corso sotto forma di matrice e archivia lo stato nella variabile $joblist.

```
PS C:\> $joblist = Get-AzureRmRecoveryservicesBackupJob –Status "InProgress"
PS C:\> $joblist[0]
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016 5:00:30 PM           cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Invece di eseguire il polling dei processi per ottenere lo stato di completamento, operazione non necessaria che prevede codice aggiuntivo, usare il cmdlet **[Wait-AzureRmRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob)**. Questo cmdlet sospende l'esecuzione fino al completamento del processo o fino a quando non viene raggiunto il valore di timeout specificato.

```
PS C:\> Wait-AzureRmRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200
```

## <a name="restore-an-azure-vm"></a>Ripristinare una macchina virtuale di Azure
C'è una differenza chiave tra il ripristino di una macchina virtuale tramite il portale di Azure e il ripristino di una macchina virtuale tramite PowerShell. Con PowerShell l'operazione di ripristino è completata quando sono stati creati i dischi e le informazioni sulla configurazione dal punto di ripristino Se si desidera ripristinare e recuperare alcuni file da un backup della macchina virtuale di Azure, vedere la [sezione relativa al recupero di file](backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup)

> [!NOTE]
> L'operazione di ripristino non crea una macchina virtuale.
>
>

Per creare una macchina virtuale dal disco, vedere la sezione [Creare la macchina virtuale da dischi archiviati](backup-azure-vms-automation.md#create-a-vm-from-stored-disks). I passaggi di base per ripristinare una macchina virtuale di Azure sono:

* Selezionare la macchina virtuale
* Scegliere un punto di ripristino
* Ripristinare i dischi
* Creare la macchina virtuale da dischi archiviati

Il grafico seguente mostra la gerarchia degli oggetti da RecoveryServicesVault fino a BackupRecoveryPoint.

![Gerarchia di oggetti dei servizi di ripristino con BackupContainer](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

Per ripristinare i dati di backup, identificare l'elemento sottoposto a backup e il punto di ripristino che contiene i dati temporizzati. Usare il cmdlet **[Restore-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/restore-azurermrecoveryservicesbackupitem)** per ripristinare i dati dall'insieme di credenziali nell'account del cliente.

### <a name="select-the-vm"></a>Selezionare la macchina virtuale
Per ottenere l'oggetto di PowerShell che identifica l'elemento di backup corretto, iniziare dal contenitore nell'insieme di credenziali e procedere verso il basso nella gerarchia degli oggetti. Per selezionare il contenitore che rappresenta la macchina virtuale, usare il cmdlet **[Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer)** e inviarlo tramite pipe al cmdlet **[Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupitem)**.

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
PS C:\> $backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>Scegliere un punto di ripristino
Usare il cmdlet **[Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprecoverypoint)** per elencare tutti i punti di recupero dell'elemento di backup. Quindi scegliere il punto di ripristino per ripristinare. Se non si sa quale punto di ripristino usare, è consigliabile scegliere il punto più recente RecoveryPointType = AppConsistent nell'elenco.

Nello script seguente la variabile **$rp** è una matrice di punti di recupero per l'elemento di backup selezionato negli ultimi sette giorni. La matrice viene ordinata in ordine inverso di tempo con il punto di ripristino più recente in posizione 0 nell'indice. Per scegliere il punto di ripristino, usare l'indicizzazione standard della matrice di PowerShell. Nell'esempio $rp [0] seleziona il punto di ripristino più recente.

```
PS C:\> $startDate = (Get-Date).AddDays(-7)
PS C:\> $endDate = Get-Date
PS C:\> $rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
PS C:\> $rp[0]
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
Per ripristinare i dati e la configurazione di un elemento di backup a un punto di recupero, usare il cmdlet **[Restore-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/restore-azurermrecoveryservicesbackupitem)**. Dopo aver identificato un punto di ripristino, usarlo come valore per il parametro **-RecoveryPoint** . Nell'esempio di codice precedente **$rp [0]** è il punto di ripristino da usare. Nell'esempio di codice seguente **$rp[0]** è il punto di ripristino da usare per il ripristino del disco.

Per ripristinare i dischi e le informazioni di configurazione:

```
PS C:\> $restorejob = Restore-AzureRmRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG"
PS C:\> $restorejob
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Usare il cmdlet **[Wait-AzureRmRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob)** per attendere il completamento del processo di ripristino.

```
PS C:\> Wait-AzureRmRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

Dopo il completamento del processo di ripristino, usare il cmdlet **[Get-AzureRmRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjobdetails)** per ottenere i dettagli dell'operazione di ripristino. La proprietà JobDetails contiene le informazioni necessarie per ricreare la macchina virtuale.

```
PS C:\> $restorejob = Get-AzureRmRecoveryServicesBackupJob -Job $restorejob
PS C:\> $details = Get-AzureRmRecoveryServicesBackupJobDetails -Job $restorejob
```

Dopo aver ripristinato i dischi, passare alla sezione successiva per creare la macchina virtuale.

## <a name="create-a-vm-from-restored-disks"></a>Creare una macchina virtuale da dischi ripristinati
Dopo aver ripristinato i dischi, seguire questa procedura per creare e configurare la macchina virtuale dal disco.

> [!NOTE]
> Per creare macchine virtuali crittografate da dischi ripristinati, il ruolo di Azure deve disporre dell'autorizzazione per eseguire l'azione, ovvero **Microsoft.KeyVault/vaults/deploy/action**. Se il ruolo non dispone di questa autorizzazione, crearne uno personalizzato con questa azione. Per altre informazioni, vedere [Ruoli personalizzati nel Controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-control-custom-roles.md).
>
>

1. Ricercare i dettagli del processo nelle proprietà del disco ripristinato.

  ```
  PS C:\> $properties = $details.properties
  PS C:\> $storageAccountName = $properties["Target Storage Account Name"]
  PS C:\> $containerName = $properties["Config Blob Container Name"]
  PS C:\> $blobName = $properties["Config Blob Name"]
  ```

2. Impostare il contesto di archiviazione di Azure e ripristinare il file di configurazione JSON.

    ```
    PS C:\> Set-AzureRmCurrentStorageAccount -Name $storageaccountname -ResourceGroupName "testvault"
    PS C:\> $destination_path = "C:\vmconfig.json"
    PS C:\> Get-AzureStorageBlobContent -Container $containerName -Blob $blobName -Destination $destination_path
    PS C:\> $obj = ((Get-Content -Path $destination_path -Raw -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
    ```

3. Usare il file di configurazione JSON per creare la configurazione della macchina virtuale.

    ```
   PS C:\> $vm = New-AzureRmVMConfig -VMSize $obj.'properties.hardwareProfile'.vmSize -VMName "testrestore"
    ```

4. Collegare il disco del sistema operativo e i dischi dei dati. In base alla configurazione delle macchine virtuali, vedere la sezione appropriata per visualizzare i rispettivi cmdlet:

    #### <a name="non-managed-non-encrypted-vms"></a>Macchine virtuali non gestite, non crittografate

    Usare l'esempio seguente per macchine virtuali non gestite e non crittografate.

    ```
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
    PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
    PS C:\> foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
    {
    $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
    }
    ```

    #### <a name="non-managed-encrypted-vms-bek-only"></a>Macchine virtuali non gestite, crittografate (solo con BEK)

    Per le macchine virtuali non gestite e crittografate (solo con BEK), è necessario ripristinare il segreto nell'insieme di credenziali delle chiavi prima di collegare i dischi. Per altre informazioni, vedere l'articolo [Ripristinare una macchina virtuale codificata da un punto di ripristino di Backup di Azure](backup-azure-restore-key-secret.md). L'esempio seguente illustra come collegare dischi del sistema operativo e i dati per le macchine virtuali crittografate.

    ```
    PS C:\> $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    PS C:\> $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
    PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
    PS C:\> foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
     }
    ```

    #### <a name="non-managed-encrypted-vms-bek-and-kek"></a>Macchine virtuali non gestite, crittografate (con BEK e KEK)

    Per le macchine virtuali non gestite e crittografate (con BEK e KEK), è necessario ripristinare la chiave e il segreto nell'insieme di credenziali delle chiavi prima di collegare i dischi. Per altre informazioni, vedere l'articolo [Ripristinare una macchina virtuale codificata da un punto di ripristino di Backup di Azure](backup-azure-restore-key-secret.md). L'esempio seguente illustra come collegare dischi del sistema operativo e i dati per le macchine virtuali crittografate.

    ```
    PS C:\> $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    PS C:\> $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
    PS C:\> $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
    PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
    PS C:\> foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
     }
    ```

    #### <a name="managed-non-encrypted-vms"></a>Macchine virtuali gestite, non crittografate

    Per macchine virtuali gestite e non crittografate, è necessario creare dischi gestiti dall'archiviazione BLOB e quindi collegarli. Per informazioni dettagliate, vedere l'articolo [Collegare un disco dati a una macchina virtuale Windows con PowerShell](../virtual-machines/windows/attach-disk-ps.md). Il codice di esempio seguente illustra come collegare dischi dati per macchine virtuali non crittografate.

    ```
    PS C:\> $storageType = "StandardLRS"
    PS C:\> $osDiskName = $vm.Name + "_osdisk"
    PS C:\> $osVhdUri = $obj.'properties.storageProfile'.osDisk.vhd.uri
    PS C:\> $diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $osVhdUri
    PS C:\> $osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk $diskConfig -ResourceGroupName "test"
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -CreateOption "Attach" -Windows
    PS C:\> foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $dataDiskName = $vm.Name + $dd.name ;
     $dataVhdUri = $dd.vhd.uri ;
     $dataDiskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $dataVhdUri ;
     $dataDisk2 = New-AzureRmDisk -DiskName $dataDiskName -Disk $dataDiskConfig -ResourceGroupName "test" ;
     Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -ManagedDiskId $dataDisk2.Id -Lun $dd.Lun -CreateOption "Attach"
    }
    ```

    #### <a name="managed-encrypted-vms-bek-only"></a>Macchine virtuali gestite, crittografate (solo con BEK)

    Per le macchine virtuali gestite e crittografate (solo con BEK), sarà necessario creare dischi gestiti dall'archiviazione BLOB e quindi collegarli. Per informazioni dettagliate, vedere l'articolo [Collegare un disco dati a una macchina virtuale Windows con PowerShell](../virtual-machines/windows/attach-disk-ps.md). Il codice di esempio seguente illustra come collegare dischi di dati per le macchine virtuali crittografate.

     ```
    PS C:\> $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    PS C:\> $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    PS C:\> $storageType = "StandardLRS"
    PS C:\> $osDiskName = $vm.Name + "_osdisk"
    PS C:\> $osVhdUri = $obj.'properties.storageProfile'.osDisk.vhd.uri
    PS C:\> $diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $osVhdUri
    PS C:\> $osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk $diskConfig -ResourceGroupName "test"
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
    PS C:\> foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $dataDiskName = $vm.Name + $dd.name ;
     $dataVhdUri = $dd.vhd.uri ;
     $dataDiskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $dataVhdUri ;
     $dataDisk2 = New-AzureRmDisk -DiskName $dataDiskName -Disk $dataDiskConfig -ResourceGroupName "test" ;
     Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -ManagedDiskId $dataDisk2.Id -Lun $dd.Lun -CreateOption "Attach"
     }
    ```

    #### <a name="managed-encrypted-vms-bek-and-kek"></a>Macchine virtuali gestite, crittografate (con BEK e KEK)

    Per le macchine virtuali gestite e crittografate (con BEK e KEK) , è necessario creare dischi gestiti dall'archiviazione BLOB e quindi collegarli. Per informazioni dettagliate, vedere l'articolo [Collegare un disco dati a una macchina virtuale Windows con PowerShell](../virtual-machines/windows/attach-disk-ps.md). Il codice di esempio seguente illustra come collegare dischi di dati per le macchine virtuali crittografate.

     ```
    PS C:\> $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    PS C:\> $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
    PS C:\> $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    PS C:\> $storageType = "StandardLRS"
    PS C:\> $osDiskName = $vm.Name + "_osdisk"
    PS C:\> $osVhdUri = $obj.'properties.storageProfile'.osDisk.vhd.uri
    PS C:\> $diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $osVhdUri
    PS C:\> $osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk $diskConfig -ResourceGroupName "test"
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
    PS C:\> foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $dataDiskName = $vm.Name + $dd.name ;
     $dataVhdUri = $dd.vhd.uri ;
     $dataDiskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $dataVhdUri ;
     $dataDisk2 = New-AzureRmDisk -DiskName $dataDiskName -Disk $dataDiskConfig -ResourceGroupName "test" ;
     Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -ManagedDiskId $dataDisk2.Id -Lun $dd.Lun -CreateOption "Attach"
     }
    ```

5. Configurare le impostazioni di rete.

    ```
    PS C:\> $nicName="p1234"
    PS C:\> $pip = New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName "test" -Location "WestUS" -AllocationMethod Dynamic
    PS C:\> $vnet = Get-AzureRmVirtualNetwork -Name "testvNET" -ResourceGroupName "test"
    PS C:\> $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName "test" -Location "WestUS" -SubnetId $vnet.Subnets[$subnetindex].Id -PublicIpAddressId $pip.Id
    PS C:\> $vm=Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
    ```
6. Creare la macchina virtuale.

    ```    
    PS C:\> New-AzureRmVM -ResourceGroupName "test" -Location "WestUS" -VM $vm
    ```

## <a name="restore-files-from-an-azure-vm-backup"></a>Ripristinare file da un backup della macchina virtuale di Azure

Oltre al ripristino di dischi è anche possibile ripristinare singoli file da un backup della macchina virtuale di Azure. La funzionalità di ripristino di file consente di accedere a tutti i file in un punto di recupero specifico in modo da renderne possibile la gestione tramite la funzionalità Esplora file, in modo analogo a quanto avviene per i file normali.

La procedura di base per il ripristino di un file dal backup della macchina virtuale di Azure si articola nei passaggi seguenti:

* Selezionare la macchina virtuale
* Scegliere un punto di ripristino
* Montare i dischi del punto di ripristino
* Copiare i file necessari
* Smontare il disco


### <a name="select-the-vm"></a>Selezionare la macchina virtuale
Per ottenere l'oggetto di PowerShell che identifica l'elemento di backup corretto, iniziare dal contenitore nell'insieme di credenziali e procedere verso il basso nella gerarchia degli oggetti. Per selezionare il contenitore che rappresenta la macchina virtuale, usare il cmdlet **[Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer)** e inviarlo tramite pipe al cmdlet **[Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupitem)**.

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
PS C:\> $backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>Scegliere un punto di ripristino
Usare il cmdlet **[Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprecoverypoint)** per elencare tutti i punti di recupero dell'elemento di backup. Quindi scegliere il punto di ripristino per ripristinare. Se non si sa quale punto di ripristino usare, è consigliabile scegliere il punto più recente RecoveryPointType = AppConsistent nell'elenco.

Nello script seguente la variabile **$rp** è una matrice di punti di recupero per l'elemento di backup selezionato negli ultimi sette giorni. La matrice viene ordinata in ordine inverso di tempo con il punto di ripristino più recente in posizione 0 nell'indice. Per scegliere il punto di ripristino, usare l'indicizzazione standard della matrice di PowerShell. Nell'esempio $rp [0] seleziona il punto di ripristino più recente.

```
PS C:\> $startDate = (Get-Date).AddDays(-7)
PS C:\> $endDate = Get-Date
PS C:\> $rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
PS C:\> $rp[0]
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
> I dischi sono montati come dischi collegati iSCSI al computer in cui viene eseguito lo script. Di conseguenza l'esito dell'operazione è quasi istantaneo e non comporta alcun addebito.
>
>

```
PS C:\> Get-AzureRmRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]

OsType  Password        Filename
------  --------        --------
Windows e3632984e51f496 V2VM_wus2_8287309959960546283_451516692429_cbd6061f7fc543c489f1974d33659fed07a6e0c2e08740.exe
```
Eseguire lo script nel computer in cui si vogliono recuperare i file. È necessario immettere la password sopra riportata per eseguire lo script. Dopo aver collegato i dischi, è possibile utilizzare Esplora file di Windows per sfogliare i nuovi volumi e file. Per altre informazioni, vedere la [documentazione relativa al ripristino dei file](backup-azure-restore-files-from-vm.md).

### <a name="unmount-the-disks"></a>Smontare i disco
Dopo aver copiato i file necessari, smontare i dischi usando il cmdlet **[Disable-AzureRmRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/disable-azurermrecoveryservicesbackuprpmountscript?view=azurermps-5.0.0)**. Questo passaggio è altamente consigliato perché in questo modo si garantisce la rimozione dell'accesso ai file del punto di ripristino.

```
PS C:\> Disable-AzureRmRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]
```


## <a name="next-steps"></a>Passaggi successivi
Se si preferisce usare PowerShell per coinvolgere le risorse di Azure, vedere l'articolo PowerShell [Distribuire e gestire il backup in Azure per server Windows/client Windows mediante PowerShell](backup-client-automation.md). Se si gestiscono i backup DPM, vedere l'articolo [Distribuire e gestire il backup per DPM](backup-dpm-automation.md). Entrambi gli articoli prevedono due versioni: una per le distribuzioni con Resource Manager, l'altra per le distribuzioni classiche.  
