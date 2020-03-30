---
title: SQL DB in Azure VM backup & restore via PowerShell
description: Eseguire il backup e il ripristino di database SQL nelle macchine virtuali di Azure usando Backup di Azure e PowerShell.Back up and restore SQL Databases in Azure VMs using Azure Backup and PowerShell.
ms.topic: conceptual
ms.date: 03/15/2019
ms.assetid: 57854626-91f9-4677-b6a2-5d12b6a866e1
ms.openlocfilehash: 9608b02869b1d41d901ec77a42cfaa6d882040e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131821"
---
# <a name="back-up-and-restore-sql-databases-in-azure-vms-with-powershell"></a>Eseguire il backup e il ripristino dei database SQL nelle macchine virtuali di Azure con PowerShellBack up and restore SQL databases in Azure VMs with PowerShell

Questo articolo descrive come usare Azure PowerShell per eseguire il backup e il ripristino di un database SQL all'interno di una macchina virtuale di Azure usando l'insieme di credenziali di Azure Backup Recovery Services.This article describes how to use Azure PowerShell to back up and recover a SQL DB within an Azure VM using [Azure Backup](backup-overview.md) Recovery Services vault.

Questo articolo spiega come:

> [!div class="checklist"]
>
> * Configurare PowerShell e registrare il provider di servizi di ripristino di Azure.Set up PowerShell and register the Azure Recovery Services Provider.
> * Creare un insieme di credenziali dei servizi di ripristino.
> * Configurare il backup per il database SQL all'interno di una macchina virtuale di Azure.Configure backup for SQL DB within an Azure VM.
> * Eseguire un processo di backup.
> * Ripristinare un database SQL di cui è stato eseguito il backup.
> * Monitorare i processi di backup e ripristino.

## <a name="before-you-start"></a>Prima di iniziare

* [Ulteriori informazioni](backup-azure-recovery-services-vault-overview.md) sugli insiemi di credenziali dei servizi di ripristino.
* Informazioni sulle funzionalità per [il backup di database SQL all'interno](backup-azure-sql-database.md#before-you-start)di macchine virtuali di Azure.
* Esaminare la gerarchia di oggetti di PowerShell per Servizi di ripristino.

### <a name="recovery-services-object-hierarchy"></a>Gerarchia di oggetti dei servizi di ripristino

La gerarchia degli oggetti è riepilogata nel diagramma seguente.

![Gerarchia di oggetti dei servizi di ripristino](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Esaminare le informazioni di riferimento sui cmdlet **Az.RecoveryServices** nella libreria di Azure.Review the Az.RecoveryServices [cmdlet reference](/powershell/module/az.recoveryservices) reference in the Azure library.

### <a name="set-up-and-install"></a>Configurare e installare

Configurare PowerShell come segue:Set up PowerShell as follows:

1. [Scaricare la versione più recente di Az PowerShell](/powershell/azure/install-az-ps). La versione minima richiesta è 1.5.0.The minimum version required is 1.5.0.

2. Trovare i cmdlet di Azure Backup PowerShell con questo comando:Find the Azure Backup PowerShell cmdlets with this command:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Esaminare gli alias e i cmdlet per Backup di Azure e l'insieme di credenziali di Servizi di ripristino. Ecco un esempio di ciò che potresti vedere. Non è un elenco completo di cmdlet.

    ![Elenco di cmdlet dei Servizi di ripristino](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Accedere all'account Azure con **Connect-AzAccount**.
5. Nella pagina Web visualizzata viene richiesto di immettere le credenziali dell'account.

    * In alternativa, è possibile includere le credenziali dell'account come parametro nel cmdlet **Connect-AzAccount** con **-Credential**.
    * Se si è un partner CSP che lavora per un tenant, specificare il cliente come tenant, usando il tenantID o il nome di dominio primario del tenant. Un esempio è **Connect-AzAccount -Tenant** fabrikam.com.

6. Associare la sottoscrizione che si vuole usare all'account, perché un account può avere più sottoscrizioni.

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

9. Nell'output del comando verificare che **RegistrationState** sia modificato in **Registered**. In caso contrario, eseguire nuovamente il cmdlet **Register-AzResourceProvider.**

## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

Seguire questa procedura per creare un insieme di credenziali di Servizi di ripristino.

L'insieme di credenziali di Servizi di ripristino è una risorsa di Resource Manager, quindi è necessario inserirlo all'interno di un gruppo di risorse. È possibile usare un gruppo di risorse esistente o crearne uno con il cmdlet **New-AzResourceGroup**. Quando si crea un nuovo gruppo di risorse, è necessario specificare il nome e il percorso per il gruppo di risorse.

1. Un insieme di credenziali viene inserito in un gruppo di risorse. Se non si dispone di un gruppo di risorse esistente, crearne uno nuovo con [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0). In questo esempio viene creato un nuovo gruppo di risorse nell'area Stati Uniti occidentali.

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. Utilizzare il cmdlet [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) per creare l'insieme di credenziali. Specificare per l'insieme di credenziali lo stesso percorso usato per il gruppo di risorse.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Specificare il tipo di ridondanza da utilizzare per l'archiviazione dell'insieme di credenziali.

    * È possibile usare l'[archiviazione con ridondanza locale](../storage/common/storage-redundancy-lrs.md) o l'[archiviazione con ridondanza geografica](../storage/common/storage-redundancy-grs.md).
    * Nell'esempio seguente viene impostata l'opzione **-BackupStorageRedundancy** per il comando[Set-AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) per **testvault** impostato su **GeoRedundant**.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

### <a name="view-the-vaults-in-a-subscription"></a>Visualizzare gli insiemi di credenziali in un abbonamento

Per visualizzare tutti gli insiemi di credenziali disponibili nella sottoscrizione, usare [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0).

```powershell
Get-AzRecoveryServicesVault
```

L'output è simile al seguente. Vengono forniti il gruppo di risorse e il percorso associati.

```output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>Impostare il contesto dell'insieme di credenziali

Memorizzare l'oggetto del vault in una variabile e impostare il contesto del vault.

* Molti cmdlet di Backup di Azure richiedono l'oggetto insieme di credenziali di Servizi di ripristino come input, pertanto è conveniente archiviare l'oggetto insieme di credenziali in una variabile.
* Il contesto dell'insieme di credenziali definisce il tipo di dati protetti nell'insieme di credenziali. Impostarlo con [Set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0). Dopo aver impostato, il contesto viene applicato a tutti i cmdlet successivi.

L'esempio seguente imposta il contesto per **testvault**.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Recuperare l'ID del vault

Si prevede di deprecare l'impostazione del contesto dell'insieme di credenziali in conformità con le linee guida di Azure PowerShell.We plan to deprecating the vault context setting in accordance with Azure PowerShell guidelines. È invece possibile memorizzare o recuperare l'ID dell'insieme di credenziali e passarlo ai comandi pertinenti, come indicato di seguito:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-a-backup-policy"></a>Configurare un criterio di backup

Un criterio di backup specifica la pianificazione per i backup e per quanto tempo devono essere mantenuti i punti di ripristino del backup:

* I criteri di backup sono associati ai criteri di conservazione. Un criterio di conservazione definisce per quanto tempo un punto di recupero viene mantenuto prima dell'eliminazione.
* Visualizzare la conservazione dei criteri di backup predefinita utilizzando [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
* Visualizzare la pianificazione predefinita dei criteri di backup utilizzando [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0).
* Utilizzare il cmdlet [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) per creare un nuovo criterio di backup. Immettere gli oggetti criteri di pianificazione e conservazione.

Per impostazione predefinita, un'ora di inizio è definita nell'oggetto Criteri di pianificazione. Utilizzare l'esempio seguente per modificare l'ora di inizio con l'ora di inizio desiderata. L'ora di inizio desiderata dovrebbe essere in UTC pure. Nell'esempio seguente si presuppone che l'ora di inizio desiderata sia 01:00 AM UTC per i backup giornalieri.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> È necessario fornire l'ora di inizio solo in multipli di 30 minuti. Nell'esempio precedente, può essere solo "01:00:00" o "02:30:00". L'ora di inizio non può essere "01:15:00"

Nell'esempio seguente i criteri di pianificazione e i criteri di conservazione vengono archiviati nelle variabili. Vengono quindi utilizzate tali variabili come parametri per un nuovo criterio (**NewSQLPolicy**). **NewSQLPolicy** esegue un backup "completo" giornaliero, lo conserva per 180 giorni e richiede un backup del log ogni 2 ore

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "MSSQL"
$NewSQLPolicy = New-AzRecoveryServicesBackupProtectionPolicy -Name "NewSQLPolicy" -WorkloadType "MSSQL" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

L'output è simile al seguente.

```output
Name                 WorkloadType       BackupManagementType BackupTime                Frequency                                IsDifferentialBackup IsLogBackupEnabled
                                                                                                                                Enabled
----                 ------------       -------------------- ----------                ---------                                -------------------- ------------------
NewSQLPolicy         MSSQL              AzureWorkload        3/15/2019 01:30:00 AM      Daily                                    False                True
```

## <a name="enable-backup"></a>Abilita backup

### <a name="registering-the-sql-vm"></a>Registrazione della macchina virtuale SQLRegistering the SQL VM

Per i backup delle macchine virtuali di Azure e le condivisioni file di Azure, il servizio di backup può connettersi a queste risorse di Azure Resource Manager e recuperare i dettagli pertinenti. Poiché SQL è un'applicazione all'interno di una macchina virtuale di Azure, il servizio di backup deve disporre dell'autorizzazione per accedere all'applicazione e recuperare i dettagli necessari. A tale scopo, è necessario *'registrare'* la macchina virtuale di Azure che contiene l'applicazione SQL con un insieme di credenziali dei servizi di ripristino. Dopo aver registrato una macchina virtuale SQL con un insieme di credenziali, è possibile proteggere i database SQL solo per tale insieme di credenziali. Utilizzare il cmdlet [Register-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) PS per registrare la macchina virtuale.

```powershell
 $myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```

Il comando restituirà un 'contenitore di backup' di questa risorsa e lo stato sarà 'registrato'

> [!NOTE]
> Se il parametro force non viene specificato, all'utente viene chiesto di confermare con un testo 'Vuoi disabilitare la protezione per questo contenitore'. Ignorare questo testo e dire "Y" per confermare. Questo è un problema noto e stiamo lavorando per rimuovere il testo e il requisito per il parametro force.

### <a name="fetching-sql-dbs"></a>Recupero di DATABASE SQL

Al termine della registrazione, il servizio di backup sarà in grado di elencare tutti i componenti SQL disponibili all'interno della macchina virtuale. Per visualizzare tutti i componenti SQL di cui è ancora stato eseguito il backup in questo insieme di credenziali, utilizzare il cmdlet [Get-AzRecoveryServicesBackupProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS

```powershell
Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -VaultId $targetVault.ID
```

L'output mostrerà tutti i componenti SQL non protetti in tutte le macchine virtuali SQL registrate in questo insieme di credenziali con Tipo di elemento e NomeServer.The output will show all unprotected SQL components across all SQL VMs registered to this vault with Item Type and ServerName. È possibile filtrare ulteriormente a una particolare macchina virtuale SQL passando il parametro '-Container' o usare la combinazione di 'Name' e 'ServerName' insieme al flag ItemType per ottenere un elemento SQL univoco.

```powershell
$SQLDB = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID -Name "<Item Name>" -ServerName "<Server Name>"
```

### <a name="configuring-backup"></a>Configurazione del backup

Ora che abbiamo il database SQL necessario e il criterio con cui deve essere eseguito il backup, è possibile utilizzare il cmdlet [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) per configurare il backup per questo database SQL.

```output
Enable-AzRecoveryServicesBackupProtection -ProtectableItem $SQLDB -Policy $NewSQLPolicy
```

Il comando attende il completamento del backup di configurazione e restituisce l'output seguente.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 6:00:21 PM      3/18/2019 6:01:35 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="fetching-new-sql-dbs"></a>Recupero di nuovi DB SQL

Una volta registrata la macchina, il servizio backup recupererà i dettagli dei DB disponibili. Se l'utente aggiunge DATABASE SQL/istanze SQL al computer registrato in un secondo momento, è necessario attivare manualmente il servizio di backup per eseguire una nuova richiesta per ottenere di nuovo TUTTI i DB non protetti (inclusi quelli appena aggiunti). Utilizzare il cmdlet [Initialize-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Initialize-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS nella macchina virtuale SQL per eseguire una richiesta di informazioni aggiornata. Il comando attende il completamento dell'operazione. Successivamente utilizzare il cmdlet [Get-AzRecoveryServicesBackupProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS per ottenere l'elenco dei componenti SQL non protetti più recenti

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Initialize-AzRecoveryServicesBackupProtectableItem -Container $SQLContainer -WorkloadType MSSQL -VaultId $targetvault.ID
Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID
```

Una volta recuperati gli elementi protetti pertinenti, abilitare i backup come indicato nella [sezione precedente.](#configuring-backup)
Se uno non vuole rilevare manualmente nuovi DB, si può optare per la protezione automatica come spiegato [di seguito](#enable-autoprotection).

## <a name="enable-autoprotection"></a>Abilitare la protezione automaticaEnable autoprotection

Un utente può configurare il backup in modo che tutti i database aggiunti in futuro siano automaticamente protetti con un determinato criterio. Per abilitare la protezione automatica, utilizzare il cmdlet [Enable-AzRecoveryServicesBackupAutoProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) PS.

Poiché l'istruzione consiste nel eseguire il backup di tutti i database futuri, l'operazione viene eseguita a livello di SQLInstance.Since the instruction is to back up all future DBs, the operation is done at a SQLInstance level.

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Enable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -Policy $NewSQLPolicy -VaultId $targetvault.ID
```

Una volta data l'intento di protezione automatica, la richiesta nella macchina per recuperare i database appena aggiunti viene eseguita come attività in background pianificata ogni 8 ore.

## <a name="restore-sql-dbs"></a>Ripristinare i database SQLRestore SQL DBs

Backup di Azure può ripristinare i database di SQL Server in esecuzione nelle macchine virtuali di Azure come segue:Azure Backup can restore SQL Server databases that are running on Azure VMs as follows:

* Ripristinare una data o un'ora specifica (al secondo) utilizzando i backup del log delle transazioni. Backup di Azure determina automaticamente il backup differenziale completo appropriato e la catena di backup del log necessari per il ripristino in base all'ora selezionata.
* Ripristinare un backup completo o differenziale specifico per il ripristino in un punto di ripristino specifico.

Controllare i prerequisiti indicati [qui](restore-sql-database-azure-vm.md#prerequisites) prima di ripristinare i database SQL.

Recuperare innanzitutto il database SQL di cui è stato eseguito il backup utilizzando il cmdlet [Get-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
```

### <a name="fetch-the-relevant-restore-time"></a>Recuperare i relativi tempi di ripristino

Come descritto in precedenza, l'utente può ripristinare il database SQL di cui è stato eseguito il backup a una copia completa/differenziale **OR** in un log point-in-time.

#### <a name="fetch-distinct-recovery-points"></a>Recuperare punti di ripristino distinti

Utilizzare [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryPoint?view=azps-1.5.0) per recuperare punti di ripristino distinti (completo/differenziale) per un database SQL di cui è stato eseguito il backup.

```powershell
$startDate = (Get-Date).AddDays(-7).ToUniversalTime()
$endDate = (Get-Date).ToUniversalTime()
Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -StartDate $startdate -EndDate $endDate
```

L'output è simile all'esempio seguente

```output
RecoveryPointId    RecoveryPointType  RecoveryPointTime      ItemName                             BackupManagemen
                                                                                                  tType
---------------    -----------------  -----------------      --------                             ---------------
6660368097802      Full               3/18/2019 8:09:35 PM   MSSQLSERVER;model             AzureWorkload
```

Utilizzare il filtro 'RecoveryPointId' o un filtro di matrice per recuperare il punto di ripristino pertinente.

```powershell
$FullRP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -RecoveryPointId "6660368097802"
```

#### <a name="fetch-point-in-time-recovery-point"></a>Recuperare il punto di ripristino temporizzatoFetch point-in-time recovery point

Se l'utente desidera ripristinare il database a un determinato punto nel tempo, utilizzare [get-AzRecoveryServicesBackupRecoveryLogChain](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryLogChain?view=azps-1.5.0) PS cmdlet. Il cmdlet restituisce un elenco di date che rappresentano le ore di inizio e di fine di una catena di log continua ininterrotta per l'elemento di backup SQL. Il punto nel tempo desiderato deve essere compreso nell'intervallo.

```powershell
Get-AzRecoveryServicesBackupRecoveryLogChain -Item $bkpItem -Item -VaultId $targetVault.ID
```

L'output sarà simile all'esempio seguente.

```output
ItemName                       StartTime                      EndTime
--------                       ---------                      -------
SQLDataBase;MSSQLSERVER;azu... 3/18/2019 8:09:35 PM           3/19/2019 12:08:32 PM
```

L'output di cui sopra significa che l'utente può ripristinare in qualsiasi punto nel tempo tra l'ora di inizio visualizzata e l'ora di fine. Gli orari sono in utc. Costruire qualsiasi punto nel tempo in PS che si trova all'interno dell'intervallo mostrato sopra.

> [!NOTE]
> Quando un punto di log nel tempo selezionato per il ripristino, l'utente non deve specificare il punto di partenza, ad esempio il backup completo da cui viene ripristinato il database. Il servizio Backup di Azure si occuperà dell'intero piano di ripristino, ad esempio il backup completo da scegliere, i backup del log da applicare e così via.

### <a name="determine-recovery-configuration"></a>Determinare la configurazione di ripristino

In caso di ripristino del database SQL, sono supportati i seguenti scenari di ripristino.

* Override del database SQL di cui è stato eseguito il backup con i dati di un altro punto di ripristino - OriginalWorkloadRestore
* Ripristino del database SQL come nuovo database nella stessa istanza SQL - AlternateWorkloadRestore the SQL DB as a new DB in the same SQL instance - AlternateWorkloadRestore
* Ripristino del database SQL come nuovo database in un'altra istanza SQL in un'altra macchina virtuale SQL - AlternateWorkloadRestore the SQL DB as a new DB in another SQL instance in another SQL VM - AlternateWorkloadRestore
* Ripristino del database SQL come file con estensione bak -RestoreAsFiles

Dopo aver recuperato il punto di ripristino pertinente (distinto o log point-in-time), utilizzare il cmdlet [Get-AzRecoveryServicesBackupWorkloadRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) PS per recuperare l'oggetto di configurazione di ripristino in base al piano di ripristino desiderato.

#### <a name="original-workload-restore"></a>Ripristino originale del carico di lavoro

Per sostituire il database di cui è stato eseguito il backup con i dati del punto di ripristino, è sufficiente specificare il flag corretto e il punto di ripristino pertinente, come illustrato negli esempi seguenti.

##### <a name="original-restore-with-distinct-recovery-point"></a>Ripristino originale con punto di ripristino distinto

```powershell
$OverwriteWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -OriginalWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="original-restore-with-log-point-in-time"></a>Ripristino originale con punto di registrazione nel tempo

```powershell
$OverwriteWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem  -OriginalWorkloadRestore -VaultId $targetVault.ID
```

#### <a name="alternate-workload-restore"></a>Ripristino del carico di lavoro alternativo

> [!IMPORTANT]
> Un database SQL di cui è stato eseguito il backup può essere ripristinato come nuovo database solo in un altro SQLInstance, in una macchina virtuale di Azure 'registrata' in questo insieme di credenziali.

Come descritto in precedenza, se l'istanza SQLInstance di destinazione si trova all'interno di un'altra macchina virtuale di Azure, assicurarsi che sia [registrata in questo insieme di credenziali](#registering-the-sql-vm) e l'istanza SQLInstance pertinente venga visualizzata come elemento protetto.

```powershell
$TargetInstance = Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -ItemType SQLInstance -Name "<SQLInstance Name>" -ServerName "<SQL VM name>" -VaultId $targetVault.ID
```

Quindi è sufficiente passare il punto di ripristino pertinente, istanza SQL di destinazione con il flag corretto, come illustrato di seguito.

##### <a name="alternate-restore-with-distinct-recovery-point"></a>Ripristino alternativo con punto di ripristino distinto

```powershell
$AnotherInstanceWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetItem $TargetInstance -AlternateWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="alternate-restore-with-log-point-in-time"></a>Ripristino alternativo con punto in tempo di logAlternate restore with log point-in-time

```powershell
$AnotherInstanceWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem -AlternateWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="restore-as-files"></a>Ripristina come file

Per ripristinare i dati di backup come file con estensione bak anziché come database, scegliere l'opzione **Ripristina come file.** Il database SQL di cui è stato eseguito il backup può essere ripristinato in qualsiasi macchina virtuale di destinazione registrata in questo insieme di credenziali.

```powershell
$TargetContainer= Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName "VM name" -VaultId $vaultID
```

##### <a name="restore-as-files-with-distinct-recovery-point"></a>Ripristina come file con punto di ripristino distinto

```powershell
$FileRestoreWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

##### <a name="restore-as-files-with-log-point-in-time-from-latest-full"></a>Ripristina come file con punto di registro nel tempo dall'ultima versione completa

```powershell
$FileRestoreWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

##### <a name="restore-as-files-with-log-point-in-time-from-a-specified-full"></a>Ripristina come file con punto di registro nel tempo da un

Se si desidera fornire un completo specifico da utilizzare per il ripristino, utilizzare il comando seguente:

```powershell
$FileRestoreWithLogAndSpecificFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -FromFull $FullRP -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

Il cmdlet Get-AzRecoveryServicesBackupWorkloadRecoveryConfig PS dell'oggetto di configurazione del punto di ripristino finale ottenuto dal cmdlet [Get-AzRecoveryServicesBackupWorkloadRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) dispone di tutte le informazioni rilevanti per il ripristino ed è illustrato di seguito.

```output
TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : No
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
```

È possibile modificare il nome del database ripristinato, OverwriteWLIfpresent, NoRecoveryMode e targetPhysicalPath. Ulteriori dettagli per i percorsi dei file di destinazione, come illustrato di seguito.

```powershell
$AnotherInstanceWithFullConfig.targetPhysicalPath
```

```output
MappingType SourceLogicalName SourcePath                  TargetPath
----------- ----------------- ----------                  ----------
Data        azurebackup1      F:\Data\azurebackup1.mdf    F:\Data\azurebackup1_1553001753.mdf
Log         azurebackup1_log  F:\Log\azurebackup1_log.ldf F:\Log\azurebackup1_log_1553001753.ldf
```

Impostare le proprietà PS rilevanti come valori stringa come illustrato di seguito.

```powershell
$AnotherInstanceWithFullConfig.OverwriteWLIfpresent = "Yes"
$AnotherInstanceWithFullConfig | fl
```

```output
TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : Yes
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
```

> [!IMPORTANT]
> Assicurarsi che l'oggetto di configurazione di ripristino finale disponga di tutti i valori necessari e corretti poiché l'operazione di ripristino sarà basata sull'oggetto di configurazione.

### <a name="restore-with-relevant-configuration"></a>Ripristino con configurazione pertinente

Una volta ottenuto e verificato l'oggetto Config di ripristino pertinente, utilizzare il cmdlet [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Restore-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS per avviare il processo di ripristino.

```powershell
Restore-AzRecoveryServicesBackupItem -WLRecoveryConfig $AnotherInstanceWithLogConfig -VaultId $targetVault.ID
```

L'operazione di ripristino restituisce un processo di cui tenere traccia.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Restore              InProgress           3/17/2019 10:02:45 AM                                3274xg2b-e4fg-5952-89b4-8cb566gc1748
```

## <a name="manage-sql-backups"></a>Gestire i backup SQL

### <a name="on-demand-backup"></a>Backup su richiesta

Una volta che il backup è stato abilitato per un database, l'utente può anche attivare un backup su richiesta per il database utilizzando il cmdlet [Backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Backup-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS. Nell'esempio seguente viene attivato un backup completo in un database SQL con compressione abilitata e il backup completo deve essere mantenuto per 60 giorni.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
Backup-AzRecoveryServicesBackupItem -Item $bkpItem -BackupType Full -EnableCompression -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
```

Il comando di backup su richiesta restituisce un processo di cui tenere traccia.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Backup               InProgress           3/18/2019 8:41:27 PM                                2516bb1a-d3ef-4841-97a3-9ba455fb0637
```

Se l'output viene perso o se si vuole ottenere l'ID processo pertinente, [ottenere l'elenco dei processi](#track-azure-backup-jobs) dal servizio Backup di Azure e quindi tenere traccia di esso e dei relativi dettagli.

### <a name="change-policy-for-backup-items"></a>Modificare i criteri per gli elementi di backup

L'utente può modificare i criteri esistenti o modificare i criteri dell'elemento di cui è stato eseguito il backup da Policy1 a Policy2. Per cambiare criterio per un elemento di cui è stato eseguito il backup, recuperare i criteri pertinenti e l'elemento di backup e utilizzare il comando [Enable-AzRecoveryServices](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) con elemento di backup come parametro.

```powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName>
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType MSSQL -BackupManagementType AzureWorkload -Name "<BackupItemName>"
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1
```

Il comando attende il completamento del backup di configurazione e restituisce l'output seguente.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="re-register-sql-vms"></a>Registrare nuovamente le macchine virtuali SQLRe-register SQL VMs

> [!WARNING]
> Assicurarsi di leggere questo [documento](backup-sql-server-azure-troubleshoot.md#re-registration-failures) per comprendere i sintomi e le cause dell'errore prima di tentare una nuova registrazione

Per attivare la nuova registrazione della macchina virtuale SQL, recuperare il contenitore di backup pertinente e passarlo al cmdlet register.

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Register-AzRecoveryServicesBackupContainer -Container $SQLContainer -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID
```

### <a name="stop-protection"></a>Arresta protezione

#### <a name="retain-data"></a>Mantenere i dati

Se l'utente desidera arrestare la protezione, può utilizzare il cmdlet [Disable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS. In questo modo i backup pianificati verranno interrotti, ma il backup dei dati fino ad ora viene mantenuto per sempre.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
```

#### <a name="delete-backup-data"></a>Elimina dati di backup

Per rimuovere completamente i dati di backup memorizzati nell'insieme di credenziali, è sufficiente aggiungere il flag/interruttore '-RemoveRecoveryPoints' al comando di [protezione 'disable'.](#retain-data)

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
```

#### <a name="disable-auto-protection"></a>Disabilitare la protezione automatica

Se la protezione automatica è stata configurata in un'istanza SQLInstance, l'utente può disabilitarla utilizzando il cmdlet [Disable-AzRecoveryServicesBackupAutoProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) PS.

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Disable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetvault.ID
```

#### <a name="unregister-sql-vm"></a>Annullare la registrazione della macchina virtuale SQLUnregister SQL VM

Se tutti i database di base di un server SQL [non sono più protetti e non esistono dati](#delete-backup-data)di backup , l'utente può annullare la registrazione della macchina virtuale SQL da questo insieme di credenziali. Solo allora l'utente può proteggere i database in un altro vault. Utilizzare il cmdlet [Unregister-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Unregister-AzRecoveryServicesBackupContainer?view=azps-1.5.0) PS per annullare la registrazione della macchina virtuale SQL.

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
 Unregister-AzRecoveryServicesBackupContainer -Container $SQLContainer -VaultId $targetvault.ID
```

### <a name="track-azure-backup-jobs"></a>Tenere traccia dei processi di Backup di AzureTrack Azure Backup jobs

È importante notare che Backup di Azure tiene traccia solo dei processi attivati dall'utente nel backup SQL. I backup pianificati (inclusi i backup del log) non sono visibili nel portale/powershell. Tuttavia, se i processi pianificati hanno esito negativo, viene generato un avviso di [backup](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) che viene visualizzato nel portale. [Usare Monitoraggio di Azure](backup-azure-monitoring-use-azuremonitor.md) per tenere traccia di tutti i processi pianificati e altre informazioni rilevanti.

Gli utenti possono tenere traccia delle operazioni attivate su richiesta/utente con il JobID restituito [nell'output](#on-demand-backup) di processi asincroni, ad esempio il backup. Utilizzare il cmdlet [Get-AzRecoveryServicesBackupJobDetail](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJobDetail) PS per tenere traccia del processo e dei relativi dettagli.

```powershell
 Get-AzRecoveryServicesBackupJobDetails -JobId 2516bb1a-d3ef-4841-97a3-9ba455fb0637 -VaultId $targetVault.ID
```

Per ottenere l'elenco dei processi su richiesta e dei relativi stati dal servizio Backup di Azure, usare il cmdlet [Get-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJob?view=azps-1.5.0) PS. Nell'esempio seguente vengono restituiti tutti i processi SQL in corso.

```powershell
Get-AzRecoveryServicesBackupJob -Status InProgress -BackupManagementType AzureWorkload
```

Per annullare un processo in corso, utilizzare il cmdlet [Stop-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Stop-AzRecoveryServicesBackupJob?view=azps-1.5.0) PS.

## <a name="managing-sql-always-on-availability-groups"></a>Gestione dei gruppi di disponibilità AlwaysOn di SQLManaging SQL Always On Availability groups

Per SQL Always On Availability Groups, assicurarsi di [registrare tutti i nodi](#registering-the-sql-vm) del gruppo di disponibilità. Al termine della registrazione per tutti i nodi, un oggetto gruppo di disponibilità SQL viene creato logicamente con elementi protetti. I database in SQL AG verranno elencati come 'SQLDatabase'. I nodi verranno visualizzati come istanze autonome e anche i database SQL predefiniti al loro sotto verranno elencati come database SQL.

Si supponga, ad esempio, che un gruppo di disponibilità SQL disponga di due nodi: 'sql-server-0' e 'sql-server-1' e 1 database di disponibilità SQL. Una volta registrati entrambi questi nodi, se [l'utente elenca gli elementi protetti,](#fetching-sql-dbs)elenca i seguenti componenti

* Oggetto SQL AG: tipo di elemento proteggebile come SQLAvailabilityGroupA SQL AG object - protectable item type as SQLAvailabilityGroup
* Un database SQL AG - tipo di elemento proteggebile come SQLDatabaseA SQL AG DB - protectable item type as SQLDatabase
* sql-server-0 - tipo di elemento proteggibile come SQLInstance
* sql-server-1 - tipo di elemento protetto come SQLInstance
* Qualsiasi DATABASE SQL predefinito (master, modello, msdb) in sql-server-0 - tipo di elemento proteggibile come SQLDatabase
* Qualsiasi DATABASE SQL predefinito (master, modello, msdb) in sql-server-1 - tipo di elemento proteggibile come SQLDatabase

sql-server-0, sql-server-1 verrà inoltre elencato come "AzureVMAppContainer" quando [sono elencati i contenitori](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupContainer?view=azps-1.5.0)di backup.

È sufficiente recuperare il database SQL pertinente per [abilitare](#configuring-backup) il backup e i cmdlet PS di backup e ripristino [su richiesta](#on-demand-backup) sono [identici.](#restore-sql-dbs)
