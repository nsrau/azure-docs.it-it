---
title: Eseguire il backup e il ripristino di database SQL in macchine virtuali di Azure con PowerShell-backup di Azure
description: Eseguire il backup e il ripristino di database SQL in macchine virtuali di Azure con backup di Azure e PowerShell.
ms.reviewer: pullabhk
author: dcurwin
manager: carmonm
keywords: Backup di Azure; SQL
ms.service: backup
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: dacurwin
ms.assetid: 57854626-91f9-4677-b6a2-5d12b6a866e1
ms.openlocfilehash: 242eaf06b9cd0b3783a626ab13eb0cb92300652f
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249063"
---
# <a name="back-up-and-restore-sql-databases-in-azure--vms-with-powershell"></a>Eseguire il backup e il ripristino di database SQL in macchine virtuali di Azure con PowerShell

Questo articolo descrive come usare Azure PowerShell per eseguire il backup e il ripristino di un database SQL in una VM di Azure usando l'insieme di credenziali di servizi di ripristino di [backup di Azure](backup-overview.md) .

In questa esercitazione viene illustrato come:

> [!div class="checklist"]
> * Configurare PowerShell e registrare il provider di servizi di ripristino di Azure.
> * Creare un insieme di credenziali dei servizi di ripristino.
> * Configurare il backup per il database SQL in una macchina virtuale di Azure.
> * Eseguire un processo di backup.
> * Ripristinare un database SQL di cui è stato eseguito il backup.
> * Monitorare i processi di backup e ripristino.

## <a name="before-you-start"></a>Prima di iniziare

* [Altre](backup-azure-recovery-services-vault-overview.md) informazioni sugli insiemi di credenziali dei servizi di ripristino.
* Informazioni sulle funzionalità per il backup di database [SQL in macchine virtuali di Azure](backup-azure-sql-database.md#before-you-start).
* Esaminare la gerarchia di oggetti di PowerShell per i servizi di ripristino.

### <a name="recovery-services-object-hierarchy"></a>Gerarchia di oggetti dei servizi di ripristino

La gerarchia di oggetti viene riepilogata nel diagramma seguente.

![Gerarchia di oggetti dei servizi di ripristino](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Esaminare il riferimento al [cmdlet](/powershell/module/az.recoveryservices) **AZ. RecoveryServices** nella libreria di Azure.

### <a name="set-up-and-install"></a>Configurare e installare

Configurare PowerShell nel modo seguente:

1. [Scaricare la versione più recente di Az PowerShell](/powershell/azure/install-az-ps). La versione minima richiesta è la versione 1.5.0.

2. Trovare i cmdlet di PowerShell per backup di Azure con questo comando:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Esaminare gli alias e i cmdlet per backup di Azure e l'insieme di credenziali di servizi di ripristino. Ecco un esempio di ciò che è possibile vedere. Non si tratta di un elenco completo di cmdlet.

    ![Elenco di cmdlet dei Servizi di ripristino](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Accedere al proprio account Azure con **Connect-AzAccount**.
5. Nella pagina Web visualizzata verrà richiesto di immettere le credenziali dell'account.

    * In alternativa, è possibile includere le credenziali dell'account come parametro nel cmdlet **Connect-AzAccount** con **-Credential**.
    * Se si è un partner CSP che lavora per un tenant, specificare il cliente come tenant, usando il nome di dominio primario tenantID o tenant. Un esempio è **Connect-AzAccount -Tenant** fabrikam.com.

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

Seguire questa procedura per creare un insieme di credenziali di Servizi di ripristino.

L'insieme di credenziali di Servizi di ripristino è una risorsa di Resource Manager, quindi è necessario inserirlo all'interno di un gruppo di risorse. È possibile usare un gruppo di risorse esistente o crearne uno con il cmdlet **New-AzResourceGroup**. Quando si crea un nuovo gruppo di risorse, è necessario specificare il nome e il percorso per il gruppo di risorse.

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
    * Nell'esempio seguente viene impostata l'opzione **-BackupStorageRedundancy** per[set-AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) cmd per **testvault** impostato su **georidondante**.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

### <a name="view-the-vaults-in-a-subscription"></a>Visualizzare gli insiemi di credenziali in un abbonamento

Per visualizzare tutti gli insiemi di credenziali disponibili nella sottoscrizione, usare [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0).

```powershell
Get-AzRecoveryServicesVault
```

L'output è simile al seguente. Vengono forniti il gruppo di risorse e la località associati.

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

Si prevede di deprecare l'impostazione del contesto dell'insieme di credenziali in base alle linee guida Azure PowerShell. È invece possibile archiviare o recuperare l'ID dell'insieme di credenziali e passarlo ai comandi pertinenti, come indicato di seguito:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-a-backup-policy"></a>Configurare un criterio di backup

I criteri di backup specificano la pianificazione per i backup e il tempo di mantenimento dei punti di ripristino del backup:

* I criteri di backup sono associati ai criteri di conservazione. Un criterio di conservazione definisce per quanto tempo un punto di recupero viene mantenuto prima dell'eliminazione.
* Visualizzare il periodo di conservazione predefinito dei criteri di backup usando [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
* Visualizzare la pianificazione predefinita dei criteri di backup utilizzando [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0).
* Usare il cmdlet [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) per creare un nuovo criterio di backup. Vengono inseriti gli oggetti Criteri di pianificazione e conservazione.

Per impostazione predefinita, nell'oggetto Criteri di pianificazione è definita un'ora di inizio. Usare l'esempio seguente per modificare l'ora di inizio con l'ora di inizio desiderata. L'ora di inizio desiderata dovrebbe essere anche UTC. Nell'esempio seguente si presuppone che l'ora di inizio desiderata sia 01:00 UTC per i backup giornalieri.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> È necessario specificare l'ora di inizio solo in più di 30 minuti. Nell'esempio precedente può essere solo "01:00:00" o "02:30:00". L'ora di inizio non può essere "01:15:00"

Nell'esempio seguente i criteri di pianificazione e i criteri di conservazione vengono archiviati nelle variabili. USA quindi tali variabili come parametri per un nuovo criterio (**NewSQLPolicy**). **NewSQLPolicy** esegue un backup giornaliero "completo", lo conserva per 180 giorni ed esegue un backup del log ogni 2 ore

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "MSSQL"
$NewSQLPolicy = New-AzRecoveryServicesBackupProtectionPolicy -Name "NewSQLPolicy" -WorkloadType "MSSQL" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

L'output è simile al seguente.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                Frequency                                IsDifferentialBackup IsLogBackupEnabled
                                                                                                                                Enabled
----                 ------------       -------------------- ----------                ---------                                -------------------- ------------------
NewSQLPolicy         MSSQL              AzureWorkload        3/15/2019 01:30:00 AM      Daily                                    False                True
```

## <a name="enable-backup"></a>Abilita backup

### <a name="registering-the-sql-vm"></a>Registrazione della macchina virtuale SQL

Per i backup di macchine virtuali di Azure e le condivisioni file di Azure, il servizio di backup può connettersi a queste risorse Azure Resource Manager e recuperare i dettagli pertinenti. Poiché SQL è un'applicazione all'interno di una macchina virtuale di Azure, il servizio di backup richiede l'autorizzazione per accedere all'applicazione e recuperare i dettagli necessari. A tale scopo, è necessario *registrare* la macchina virtuale di Azure che contiene l'applicazione SQL con un insieme di credenziali di servizi di ripristino. Dopo la registrazione di una VM SQL con un insieme di credenziali, è possibile proteggere solo i database SQL in tale insieme di credenziali. Usare il cmdlet [Register-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) PS per registrare la macchina virtuale.

````powershell
 $myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
````

Il comando restituirà' backup container ' di questa risorsa e lo stato sarà' registered '

> [!NOTE]
> Se non si specifica il parametro Force, viene richiesto all'utente di confermare con il testo "disabilitare la protezione per questo contenitore". Ignorare questo testo e pronunciare "Y" per confermare. Si tratta di un problema noto e si sta lavorando per rimuovere il testo e il requisito per il parametro Force.

### <a name="fetching-sql-dbs"></a>Recupero di database SQL

Al termine della registrazione, il servizio backup sarà in grado di elencare tutti i componenti SQL disponibili all'interno della macchina virtuale. Per visualizzare tutti i componenti SQL ancora di cui eseguire il backup in questo insieme di credenziali, usare il cmdlet [Get-AzRecoveryServicesBackupProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS

````powershell
Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -VaultId $targetVault.ID
````

L'output visualizzerà tutti i componenti SQL non protetti in tutte le macchine virtuali SQL registrate in questo insieme di credenziali con tipo di elemento e ServerName. È possibile filtrare ulteriormente per una particolare VM SQL passando il parametro '-container ' o usare la combinazione di ' name ' è ServerName ' insieme al flag ItemType per giungere a un elemento SQL univoco.

````powershell
$SQLDB = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID -Name "<Item Name>" -ServerName "<Server Name>"
````

### <a name="configuring-backup"></a>Configurazione del backup

Ora che si dispone del database SQL necessario e dei criteri con cui è necessario eseguirne il backup, è possibile usare il cmdlet [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) per configurare il backup per questo database SQL.

````powershell
Enable-AzRecoveryServicesBackupProtection -ProtectableItem $SQLDB -Policy $NewSQLPolicy
````

Il comando attende fino al completamento del backup di configurazione e restituisce il seguente output.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 6:00:21 PM      3/18/2019 6:01:35 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="fetching-new-sql-dbs"></a>Recupero di nuovi database SQL

Al termine della registrazione del computer, il servizio di backup recupererà i dettagli dei database disponibili. Se l'utente aggiunge le istanze di database SQL/SQL al computer registrato in un secondo momento, è necessario attivare manualmente il servizio di backup per eseguire un'indagine aggiornata per ottenere di nuovo tutti i database non protetti, inclusi quelli appena aggiunti. Usare il cmdlet [Initialize-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Initialize-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS nella macchina virtuale SQL per eseguire una nuova richiesta. Il comando attende fino al completamento dell'operazione. Usare quindi il cmdlet [Get-AzRecoveryServicesBackupProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS per ottenere l'elenco dei componenti SQL non protetti più recenti

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Initialize-AzRecoveryServicesBackupProtectableItem -Container $SQLContainer -WorkloadType MSSQL -VaultId $targetvault.ID
Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID
````

Una volta recuperati gli elementi da proteggere pertinenti, abilitare i backup come indicato nella [sezione precedente](#configuring-backup).
Se non si desidera rilevare manualmente i nuovi database, è possibile optare per la protezione, come illustrato di [seguito](#enable-autoprotection).

## <a name="enable-autoprotection"></a>Abilita protezione dati

Un utente può configurare il backup in modo che tutti i database aggiunti in futuro vengano automaticamente protetti con un determinato criterio. Per abilitare la protezione dati, usare il cmdlet [Enable-AzRecoveryServicesBackupAutoProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) PS.

Poiché l'istruzione consiste nel eseguire il backup di tutti i database futuri, l'operazione viene eseguita a livello di SQLInstance.

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Enable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -Policy $NewSQLPolicy -VaultId $targetvault.ID
```

Una volta assegnato lo scopo di protezione automatica, la richiesta di recupero dei database appena aggiunti viene eseguita come attività in background pianificata ogni 8 ore.

## <a name="restore-sql-dbs"></a>Ripristinare database SQL

Backup di Azure è in grado di ripristinare SQL Server database in esecuzione in macchine virtuali di Azure, come indicato di seguito:

1. Ripristinare una data o un'ora specifica (al secondo) utilizzando i backup del log delle transazioni. Backup di Azure determina automaticamente il backup completo differenziale appropriato e la catena di backup del log necessari per il ripristino in base all'ora selezionata.
2. Ripristinare un backup completo o differenziale specifico per eseguire il ripristino in un punto di ripristino specifico.

Verificare i prerequisiti indicati [qui](restore-sql-database-azure-vm.md#prerequisites) prima di ripristinare il database SQL.

Recuperare prima di tutto il database SQL di cui è stato eseguito il backup usando il cmdlet [Get-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
````

### <a name="fetch-the-relevant-restore-time"></a>Recuperare il tempo di ripristino pertinente

Come descritto in precedenza, l'utente può ripristinare il database SQL di cui è stato eseguito il backup in una copia completa/differenziale **o** in un log temporizzato.

#### <a name="fetch-distinct-recovery-points"></a>Recupera punti di ripristino distinti

Usare [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryPoint?view=azps-1.5.0) per recuperare punti di ripristino distinti (completi/differenziali) per un database SQL di cui è stato eseguito il backup.

````powershell
$startDate = (Get-Date).AddDays(-7).ToUniversalTime()
$endDate = (Get-Date).ToUniversalTime()
Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -StartDate $startdate -EndDate $endDate
````

L'output è simile all'esempio seguente:

````powershell
RecoveryPointId    RecoveryPointType  RecoveryPointTime      ItemName                             BackupManagemen
                                                                                                  tType
---------------    -----------------  -----------------      --------                             ---------------
6660368097802      Full               3/18/2019 8:09:35 PM   MSSQLSERVER;model             AzureWorkload
````

Usare il filtro ' RecoveryPointId ' o un filtro di matrice per recuperare il punto di ripristino pertinente.

````powershell
$FullRP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -RecoveryPointId "6660368097802"
````

#### <a name="fetch-point-in-time-recovery-point"></a>Recupera punto di ripristino temporizzato

Se l'utente vuole ripristinare il database a un determinato punto nel tempo, usare il cmdlet [Get-AzRecoveryServicesBackupRecoveryLogChain](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryLogChain?view=azps-1.5.0) PS. Il cmdlet restituisce un elenco di date che rappresentano l'ora di inizio e di fine di una catena di log continua non interruppe per tale elemento di backup SQL. Il punto nel tempo desiderato deve essere compreso in questo intervallo.

```powershell
Get-AzRecoveryServicesBackupRecoveryLogChain -Item $bkpItem -Item -VaultId $targetVault.ID
```

L'output sarà simile all'esempio seguente.

````powershell
ItemName                       StartTime                      EndTime
--------                       ---------                      -------
SQLDataBase;MSSQLSERVER;azu... 3/18/2019 8:09:35 PM           3/19/2019 12:08:32 PM
````

L'output precedente indica che l'utente può eseguire il ripristino in qualsiasi punto nel tempo compreso tra l'ora di inizio e l'ora di fine visualizzate. Gli orari sono in formato UTC. Costruisce qualsiasi punto nel tempo in PS compreso nell'intervallo indicato sopra.

> [!NOTE]
> Quando si seleziona un punto di ripristino temporizzato per il ripristino, l'utente non deve specificare il punto di partenza, ad esempio il backup completo da cui viene ripristinato il database. Il servizio backup di Azure si occuperà dell'intero piano di ripristino, ad esempio il backup completo da scegliere, i backup del log da applicare e così via.

### <a name="determine-recovery-configuration"></a>Determinare la configurazione del ripristino

In caso di ripristino del database SQL, sono supportati i seguenti scenari di ripristino.

1. Override del database SQL di cui è stato eseguito il backup con i dati di un altro punto di ripristino-OriginalWorkloadRestore
2. Ripristino del database SQL come nuovo database nella stessa istanza di SQL-AlternateWorkloadRestore
3. Ripristino del database SQL come nuovo database in un'altra istanza di SQL in un'altra VM SQL-AlternateWorkloadRestore

Dopo aver recuperato il punto di ripristino pertinente (separato o temporizzato del log), usare il cmdlet [Get-AzRecoveryServicesBackupWorkloadRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) PS per recuperare l'oggetto di configurazione di ripristino in base al piano di ripristino desiderato.

#### <a name="original-workload-restore"></a>Ripristino originale del carico di lavoro

Per eseguire l'override del database di cui è stato eseguito il backup con i dati del punto di ripristino, è sufficiente specificare il flag destro e il punto di ripristino pertinente, come illustrato negli esempi seguenti.

##### <a name="original-restore-with-distinct-recovery-point"></a>Ripristino originale con punto di ripristino distinto

````powershell
$OverwriteWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -OriginalWorkloadRestore -VaultId $targetVault.ID
````

##### <a name="original-restore-with-log-point-in-time"></a>Ripristino originale con temporizzazione log

```powershell
$OverwriteWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem  -OriginalWorkloadRestore -VaultId $targetVault.ID
```

#### <a name="alternate-workload-restore"></a>Ripristino del carico di lavoro alternativo

> [!IMPORTANT]
> Un database SQL di cui è stato eseguito il backup può essere ripristinato come nuovo database in un'altra istanza di SQLInstance, in una macchina virtuale di Azure ' registered ' in questo insieme di credenziali.

Come illustrato in precedenza, se l'istanza di SQLInstance di destinazione si trova all'interno di un'altra VM di Azure, assicurarsi che sia [registrata in questo insieme di](#registering-the-sql-vm) credenziali e che la relativa istanza di SQLInstance venga visualizzata come elemento da proteggere.

````powershell
$TargetInstance = Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -ItemType SQLInstance -Name "<SQLInstance Name>" -ServerName "<SQL VM name>" -VaultId $targetVault.ID
````

Quindi, è sufficiente passare il punto di ripristino pertinente, l'istanza di SQL di destinazione con il flag right, come illustrato di seguito.

##### <a name="alternate-restore-with-distinct-recovery-point"></a>Ripristino alternativo con punto di ripristino distinto

````powershell
$AnotherInstanceWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetItem $TargetInstance -AlternateWorkloadRestore -VaultId $targetVault.ID
````

##### <a name="alternate-restore-with-log-point-in-time"></a>Ripristino alternativo con log temporizzato

```powershell
$AnotherInstanceWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem -AlternateWorkloadRestore -VaultId $targetVault.ID
```

L'oggetto di configurazione del punto di ripristino finale ottenuto dal cmdlet [Get-AzRecoveryServicesBackupWorkloadRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) PS contiene tutte le informazioni rilevanti per il ripristino ed è illustrato di seguito.

````powershell
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
````

È possibile modificare i campi nome database ripristinato, OverwriteWLIfpresent, NoRecoveryMode e targetPhysicalPath. Per ulteriori informazioni sui percorsi dei file di destinazione, vedere di seguito.

````powershell
$AnotherInstanceWithFullConfig.targetPhysicalPath

MappingType SourceLogicalName SourcePath                  TargetPath
----------- ----------------- ----------                  ----------
Data        azurebackup1      F:\Data\azurebackup1.mdf    F:\Data\azurebackup1_1553001753.mdf
Log         azurebackup1_log  F:\Log\azurebackup1_log.ldf F:\Log\azurebackup1_log_1553001753.ldf
````

Impostare le proprietà di PS pertinenti come valori stringa come illustrato di seguito.

````powershell
$AnotherInstanceWithFullConfig.OverwriteWLIfpresent = "Yes"
$AnotherInstanceWithFullConfig | fl

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
````

> [!IMPORTANT]
> Verificare che l'oggetto di configurazione del ripristino finale includa tutti i valori necessari e appropriati, in quanto l'operazione di ripristino sarà basata sull'oggetto config.

### <a name="restore-with-relevant-configuration"></a>Ripristino con la configurazione pertinente

Una volta ottenuto e verificato l'oggetto di configurazione di ripristino pertinente, usare il cmdlet [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Restore-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS per avviare il processo di ripristino.

````powershell
Restore-AzRecoveryServicesBackupItem -WLRecoveryConfig $AnotherInstanceWithLogConfig -VaultId $targetVault.ID
````

L'operazione di ripristino restituisce un processo da rilevare.

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Restore              InProgress           3/17/2019 10:02:45 AM                                3274xg2b-e4fg-5952-89b4-8cb566gc1748
````

## <a name="manage-sql-backups"></a>Gestire i backup SQL

### <a name="on-demand-backup"></a>Backup su richiesta

Una volta abilitato il backup per un database, l'utente può anche attivare un backup su richiesta per il database usando il cmdlet [backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Backup-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS. Nell'esempio seguente viene attivato un backup completo in un database SQL con la compressione abilitata e il backup completo deve essere mantenuto per 60 giorni.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
Backup-AzRecoveryServicesBackupItem -Item $bkpItem -BackupType Full -EnableCompression -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
````

Il comando di backup ad hoc restituisce un processo da rilevare.

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Backup               InProgress           3/18/2019 8:41:27 PM                                2516bb1a-d3ef-4841-97a3-9ba455fb0637
````

Se l'output viene perso o se si vuole ottenere l'ID del processo pertinente, [ottenere l'elenco dei processi](#track-azure-backup-jobs) dal servizio backup di Azure e quindi tenerne traccia e i relativi dettagli.

### <a name="change-policy-for-backup-items"></a>Modificare i criteri per gli elementi di backup

L'utente può modificare i criteri esistenti o modificare i criteri dell'elemento di cui è stato eseguito il backup da Policy1 a Policy2. Per modificare i criteri per un elemento di cui è stato eseguito il backup, è sufficiente recuperare i criteri pertinenti ed eseguire il backup dell'elemento e usare il comando [Enable-AzRecoveryServices](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) con l'elemento di backup come parametro.

````powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName>
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType MSSQL -BackupManagementType AzureWorkload -Name "<BackupItemName>"
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1
````

Il comando attende fino al completamento del backup di configurazione e restituisce il seguente output.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="re-register-sql-vms"></a>Registrare di nuovo le VM SQL

> [!WARNING]
> Prima di provare a ripetere la registrazione, assicurarsi di leggere questo [documento](backup-sql-server-azure-troubleshoot.md#re-registration-failures) per comprendere i sintomi e le cause dell'errore.

Per attivare la ripetizione della registrazione della macchina virtuale SQL, recuperare il contenitore di backup pertinente e passarlo al cmdlet Register.

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Register-AzRecoveryServicesBackupContainer -Container $SQLContainer -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID
````

### <a name="stop-protection"></a>Arresta protezione

#### <a name="retain-data"></a>Conserva dati

Se l'utente desidera arrestare la protezione, può usare il cmdlet [Disable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS. Questa operazione arresterà i backup pianificati, ma i dati di cui è stato eseguito il backup fino a questo momento verranno conservati per sempre.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
````

#### <a name="delete-backup-data"></a>Elimina dati di backup

Per rimuovere completamente i dati di backup archiviati nell'insieme di credenziali, è sufficiente aggiungere il flag '-RemoveRecoveryPoints '/passare al [comando di protezione ' Disable '](#retain-data).

````powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
````

#### <a name="disable-auto-protection"></a>Disabilitare la protezione automatica

Se la protezione con autoprotection è stata configurata su un'istanza di SQLInstance, l'utente può disabilitarla usando il cmdlet [Disable-AzRecoveryServicesBackupAutoProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) PS.

````powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Disable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetvault.ID
````

#### <a name="unregister-sql-vm"></a>Annulla registrazione macchina virtuale SQL

Se tutti i database di SQL Server [non sono più protetti e non esistono dati di backup](#delete-backup-data), l'utente può annullare la registrazione della macchina virtuale SQL da questo insieme di credenziali. Solo l'utente può proteggere i database in un altro insieme di credenziali. Usare il cmdlet [Unregister-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Unregister-AzRecoveryServicesBackupContainer?view=azps-1.5.0) PS per annullare la registrazione della macchina virtuale SQL.

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
 Unregister-AzRecoveryServicesBackupContainer -Container $SQLContainer -VaultId $targetvault.ID
````

### <a name="track-azure-backup-jobs"></a>Tenere traccia dei processi di backup di Azure

È importante notare che backup di Azure tiene traccia solo dei processi attivati dall'utente nel backup SQL. I backup pianificati, inclusi i backup del log, non sono visibili nel portale/PowerShell. Tuttavia, in caso di esito negativo di un processo pianificato, viene generato un [avviso di backup](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) che viene visualizzato nel portale. [Usare monitoraggio di Azure](backup-azure-monitoring-use-azuremonitor.md) per tenere traccia di tutti i processi pianificati e altre informazioni rilevanti.

Gli utenti possono tenere traccia delle operazioni attivate dall'utente o ad hoc con JobID restituito nell' [output](#on-demand-backup) di processi asincroni, ad esempio backup. Usare il cmdlet [Get-AzRecoveryServicesBackupJobDetail](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJobDetail) PS per tenere traccia del processo e dei relativi dettagli.

````powershell
 Get-AzRecoveryServicesBackupJobDetails -JobId 2516bb1a-d3ef-4841-97a3-9ba455fb0637 -VaultId $targetVault.ID
````

Per ottenere l'elenco dei processi ad hoc e i relativi stati dal servizio backup di Azure, usare il cmdlet [Get-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJob?view=azps-1.5.0) PS. Nell'esempio seguente vengono restituiti tutti i processi SQL in corso.

```powershell
Get-AzRecoveryServicesBackupJob -Status InProgress -BackupManagementType AzureWorkload
```

Per annullare un processo in corso, usare il cmdlet [Stop-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Stop-AzRecoveryServicesBackupJob?view=azps-1.5.0) PS.

## <a name="managing-sql-always-on-availability-groups"></a>Gestione dei gruppi di disponibilità di SQL Always On

Per i gruppi di disponibilità SQL Always On, assicurarsi di [registrare tutti i nodi](#registering-the-sql-vm) del gruppo di disponibilità (AG). Una volta eseguita la registrazione per tutti i nodi, un oggetto gruppo di disponibilità SQL viene creato logicamente in elementi da proteggere. I database di SQL AG verranno elencati come ' SqlDatabase '. I nodi verranno visualizzati come istanze autonome e i database SQL predefiniti in essi contenuti verranno elencati anche come database SQL.

Si supponga, ad esempio, che un gruppo di disponibilità di SQL Server disponga di due nodi: "SQL-Server-0" e "SQL-Server-1" e 1 database AG di SQL Server. Una volta registrati entrambi questi nodi, se [l'utente elenca gli elementi da proteggere](#fetching-sql-dbs), elenca i componenti seguenti

1. Tipo di elemento da proteggere con oggetti di SQL AG come SQLAvailabilityGroup
2. Tipo di elemento da proteggere con database di SQL AG come SQLDatabase
3. tipo di elemento da proteggere con SQL-Server-0 come SQLInstance
4. tipo di elemento da proteggere con SQL-Server-1 come SQLInstance
5. Qualsiasi database SQL predefinito (Master, Model, msdb) nel tipo di elemento da proteggere con SQL-Server-0 come SQLDatabase
6. Qualsiasi database SQL predefinito (Master, Model, msdb) nel tipo di elemento da proteggere con SQL-Server-1 come SQLDatabase

SQL-Server-0, SQL-Server-1 verrà elencato anche come "AzureVMAppContainer" quando [vengono elencati i contenitori di backup](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupContainer?view=azps-1.5.0).

È sufficiente recuperare il database SQL pertinente per [abilitare il backup](#configuring-backup) e i [cmdlet](#restore-sql-dbs) di backup e ripristino ad [hoc](#on-demand-backup) sono identici.
