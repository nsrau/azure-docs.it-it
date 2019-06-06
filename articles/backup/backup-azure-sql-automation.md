---
title: 'Backup di Azure: Eseguire il backup e ripristino di database SQL in macchine virtuali di Azure con Backup di Azure e PowerShell'
description: Eseguire il backup e ripristinare i database SQL in macchine virtuali di Azure con Backup di Azure e PowerShell.
services: backup
author: pvrk
manager: vijayts
keywords: Backup di Azure; SQL;
ms.service: backup
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pullabhk
ms.assetid: 57854626-91f9-4677-b6a2-5d12b6a866e1
ms.openlocfilehash: 6a2e065466ab4426a6472b64fae19d264ff8dd81
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734230"
---
# <a name="back-up-and-restore-sql-databases-in-azure--vms-with-powershell"></a>Eseguire il backup e ripristino di database SQL in macchine virtuali di Azure con PowerShell

Questo articolo descrive come usare Azure PowerShell per eseguire il backup e ripristinare un database SQL all'interno di una macchina virtuale di Azure usando [Backup di Azure](backup-overview.md) insieme di credenziali dei servizi di ripristino.

In questa esercitazione viene illustrato come:

> [!div class="checklist"]
> * Configurazione di PowerShell e registrare il Provider di servizi di ripristino di Azure.
> * Creare un insieme di credenziali dei servizi di ripristino.
> * Configurare il backup per database di SQL all'interno di una VM di Azure.
> * Eseguire un processo di backup.
> * Ripristinare un backup dei database SQL.
> * Monitorare il backup e ripristino di processi.

## <a name="before-you-start"></a>Prima di iniziare

* [Altre informazioni](backup-azure-recovery-services-vault-overview.md) su insiemi di credenziali di servizi di ripristino.
* Informazioni sulle funzionalità per la funzionalità [backup di database SQL all'interno di macchine virtuali di Azure](backup-azure-sql-database.md#before-you-start).
* Esaminare la gerarchia di oggetti PowerShell per servizi di ripristino.

### <a name="recovery-services-object-hierarchy"></a>Gerarchia di oggetti dei servizi di ripristino

Nel diagramma seguente viene riepilogata la gerarchia di oggetti.

![Gerarchia di oggetti dei servizi di ripristino](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Rivedere le **Az.RecoveryServices** [riferimento dei cmdlet](/powershell/module/az.recoveryservices) riferimento nella raccolta di Azure.

### <a name="set-up-and-install"></a>Configurare e installare

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Configurare PowerShell nel modo seguente:

1. [Scaricare la versione più recente di Az PowerShell](/powershell/azure/install-az-ps). La versione minima richiesta è 1.5.0.

2. Trovare i cmdlet di PowerShell di Backup di Azure con questo comando:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Esaminare i cmdlet e gli alias per il Backup di Azure e l'insieme di credenziali di servizi di ripristino. Di seguito è riportato un esempio di ciò che potrebbe essere visualizzato. Non è un elenco completo dei cmdlet.

    ![Elenco di cmdlet dei Servizi di ripristino](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Accedere al proprio account Azure con **Connect-AzAccount**.
5. Nella pagina web che viene visualizzato, viene chiesto di immettere le credenziali dell'account.

    * In alternativa, è possibile includere le credenziali dell'account come parametro nel **Connect-AzAccount** cmdlet con **-Credential**.
    * Se sei un partner CSP che opera per un tenant, specificare il cliente come tenant, usando il nome di dominio primario ID tenant o tenant. Un esempio è **Connect-AzAccount -Tenant** fabrikam.com.

6. Associare la sottoscrizione da usare con l'account, perché un account può avere molte sottoscrizioni.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Se si sta usando Backup di Azure per la prima volta, usare il cmdlet **Register-AzResourceProvider** per registrare il provider di Servizi di ripristino di Azure con la propria sottoscrizione.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. Verificare che i provider di stato registrato correttamente:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. Nell'output del comando, verificare che **RegistrationState** diventa **Registered**. Se non, viene eseguito il **Register-AzResourceProvider** cmdlet nuovamente.

## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

Seguire questa procedura per creare un insieme di credenziali di Servizi di ripristino.

L'insieme di credenziali di Servizi di ripristino è una risorsa di Resource Manager, quindi è necessario inserirlo all'interno di un gruppo di risorse. È possibile usare un gruppo di risorse esistente o crearne uno con il cmdlet **New-AzResourceGroup**. Quando si crea un nuovo gruppo di risorse, è necessario specificare il nome e il percorso per il gruppo di risorse.

1. Un insieme di credenziali viene inserito in un gruppo di risorse. Se non hai una risorsa esistente di gruppo, crearne uno nuovo con il [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0). In questo esempio, creiamo un nuovo gruppo di risorse nell'area Stati Uniti occidentali.

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. Usare la [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) cmdlet per creare l'insieme di credenziali. Specificare per l'insieme di credenziali lo stesso percorso usato per il gruppo di risorse.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Specificare il tipo di ridondanza da utilizzare per l'archiviazione dell'insieme di credenziali.

    * È possibile usare l'[archiviazione con ridondanza locale](../storage/common/storage-redundancy-lrs.md) o l'[archiviazione con ridondanza geografica](../storage/common/storage-redundancy-grs.md).
    * L'esempio seguente imposta la **- BackupStorageRedundancy** opzione per il[Set-AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) cmd per **testvault** impostato su  **Con ridondanza geografica**.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

### <a name="view-the-vaults-in-a-subscription"></a>Visualizzare gli insiemi di credenziali in un abbonamento

Per visualizzare tutti gli insiemi di credenziali disponibili nella sottoscrizione, usare [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0).

```powershell
Get-AzRecoveryServicesVault
```

L'output è simile al seguente. Vengono forniti il gruppo di risorse associato e il percorso.

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

* Molti cmdlet di Backup di Azure richiede l'oggetto dell'insieme di credenziali di servizi di ripristino come input, pertanto è utile archiviare l'oggetto dell'insieme di credenziali in una variabile.
* Il contesto dell'insieme di credenziali definisce il tipo di dati protetti nell'insieme di credenziali. Impostarlo con [Set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0). Dopo aver impostato il contesto, si applica a tutti i cmdlet successivi.

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

* I criteri di backup sono associati ai criteri di conservazione. Un criterio di conservazione definisce per quanto tempo un punto di recupero viene mantenuto prima dell'eliminazione.
* Visualizza la conservazione dei criteri di backup predefinita utilizzando [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
* Visualizza la pianificazione dei criteri di backup predefinita utilizzando [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0).
* Si utilizza il [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) cmdlet per creare un nuovo criterio di backup. Gli oggetti Criteri di conservazione e pianificazione di input.

Nell'esempio seguente i criteri di pianificazione e i criteri di conservazione vengono archiviati nelle variabili. Utilizza quindi tali variabili come parametri per un nuovo criterio (**NewSQLPolicy**). **NewSQLPolicy** esegue il backup giornaliero "completo", li conserva per 180 giorni e richiede un backup del log ogni 2 ore

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
NewSQLPolicy         MSSQL              AzureWorkload        3/15/2019 9:00:00 PM      Daily                                    False                True
```

## <a name="enable-backup"></a>Abilitare il backup

### <a name="registering-the-sql-vm"></a>Registrare la VM SQL

Per i backup di macchine Virtuali di Azure e le condivisioni File di Azure, servizio di Backup può connettersi a queste risorse di Azure Resource Manager e recuperare i dettagli più importanti. Poiché SQL è un'applicazione all'interno di una VM di Azure, il servizio Backup richiede l'autorizzazione per accedere all'applicazione per ottenere i dettagli necessari. Per questo scopo, è necessario *'register'* macchina virtuale di Azure che contiene l'applicazione SQL con un insieme di credenziali di servizi di ripristino. Quando si registra una VM di SQL con un insieme di credenziali, è possibile proteggere il database SQL per solo tale insieme di credenziali. Uso [Register-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) cmdlet di Powershell per registrare la macchina virtuale.

````powershell
 $myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
````

Il comando restituirà un contenitore' backup' della risorsa e lo stato 'registrerà'

> [!NOTE]
> Se non viene specificato il parametro force, verrà chiesto di confermare con un testo 'si desidera disabilitare la protezione per questo contenitore'. . Ignorare questo testo e pronunciare "Y" per confermare. Si tratta di un problema noto e stiamo lavorando per rimuovere il testo e il requisito per il parametro force

### <a name="fetching-sql-dbs"></a>Durante il recupero dei database SQL

Una volta che viene eseguita la registrazione, il servizio Backup sarà in grado di elencare tutti i componenti SQL disponibili all'interno della macchina virtuale. Per visualizzare tutti i componenti SQL ancora essere eseguito il backup per questo insieme di credenziali usare [Get-AzRecoveryServicesBackupProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) cmdlet PS

````powershell
Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -VaultId $targetVault.ID
````

L'output visualizzerà tutti i componenti SQL non protetti tra tutte le VM di SQL registrato nell'insieme di credenziali con tipo di elemento e ServerName. È possibile filtrare ulteriormente per una VM di SQL specifica passando il '-contenitore ' parametro oppure usare la combinazione di 'Name' e 'ServerName' insieme a ItemType flag per arrivare a un elemento SQL univoco.

````powershell
$SQLDB = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID -Name "<Item Name>" -ServerName "<Server Name>"
````

### <a name="configuring-backup"></a>La configurazione del backup

Ora che abbiamo il database SQL necessari e i criteri con cui si deve eseguire il backup, è possibile usare la [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) cmdlet per configurare il backup per il database SQL.

````powershell
Enable-AzRecoveryServicesBackupProtection -ProtectableItem $SQLDB -Policy $NewSQLPolicy
````

Il comando attende fino a quando non di configurare il backup viene completato e restituisce l'output seguente.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 6:00:21 PM      3/18/2019 6:01:35 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="fetching-new-sql-dbs"></a>Durante il recupero dei database SQL nuova

Dopo aver registrato il computer, il servizio Backup recupererà i dettagli dei database disponibili, quindi. Se l'utente aggiunge le istanze attive dei database SQL/SQL per i computer registrati in un secondo momento, è necessario attivare manualmente il servizio backup per eseguire una nuova richiesta ' informazioni' per ottenere tutti i database non protetti (inclusi quelli appena aggiunti) nuovamente. Usare la [Initialize-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Initialize-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) cmdlet di Powershell nella VM di SQL per eseguire una nuova richiesta di informazioni. Il comando attende fino al completamento dell'operazione. In un secondo momento usare il [Get-AzRecoveryServicesBackupProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) cmdlet di PS per ottenere l'elenco più recente non protetti dei componenti di SQL

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Initialize-AzRecoveryServicesBackupProtectableItem -Container $SQLContainer -WorkloadType MSSQL -VaultId $targetvault.ID
Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID
````

Una volta che vengono recuperati i relativi elementi da proteggere, abilitare i backup come indicato nel [sopra la sezione](#configuring-backup).
Se uno non intende rilevare manualmente i nuovi database, essi possono optare per autoprotection come spiegato [seguito](#enable-autoprotection).

## <a name="enable-autoprotection"></a>Abilitare AutoProtection

Un utente può configurare il backup in modo che tutti i database aggiunti in futuro vengano protetti automaticamente con determinati criteri. Per abilitare autoprotection, usare [Enable-AzRecoveryServicesBackupAutoProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) cmdlet di Powershell.

Poiché l'istruzione di backup di tutti i futuri attive dei database, l'operazione venga effettuata con un SQLInstance livello.

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Enable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -Policy $targetPolicy -VaultId $targetvault.ID
```

Una volta che viene concesso l'intento autoprotection, la richiesta nella macchina per recuperare appena aggiunto i database vengono eseguiti come attività in background pianificati ogni 8 ore.

## <a name="restore-sql-dbs"></a>Ripristinare i database SQL

Backup di Azure è possibile ripristinare i database di SQL Server in esecuzione su macchine virtuali di Azure come indicato di seguito:

1. Ripristinare una data specifica o un'ora (al secondo) con i backup del log delle transazioni. Backup di Azure determina automaticamente il backup completo differenziale appropriato e la catena di backup del log necessari per ripristinare in base l'ora selezionata.
2. Ripristinare un backup completo o differenziale specifico da ripristinare in un punto di recupero specifico.

Verificare i prerequisiti indicati [qui](restore-sql-database-azure-vm.md#prerequisites) prima di ripristinare i database SQL.

Prima di tutto recuperare il relativo backup di database SQL con il [Get-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem?view=azps-1.5.0) cmdlet di Powershell.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
````

### <a name="fetch-the-relevant-restore-time"></a>Recuperare il tempo di ripristino pertinenti

Come illustrato in precedenza, l'utente può ripristinare il database SQL di backup per una copia completa/differenziale **o** in un log punto nel tempo.

#### <a name="fetch-distinct-recovery-points"></a>Recuperare i punti di ripristino distinte

Uso [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryPoint?view=azps-1.5.0) per recuperare i punti di ripristino (completa/differenziale) distinti per un database di SQL sottoposte a backup.

````powershell
$startDate = (Get-Date).AddDays(-7).ToUniversalTime()
$endDate = (Get-Date).ToUniversalTime()
Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -StartDate $startdate -EndDate $endDate
````

L'output è simile all'esempio seguente

````powershell
RecoveryPointId    RecoveryPointType  RecoveryPointTime      ItemName                             BackupManagemen
                                                                                                  tType
---------------    -----------------  -----------------      --------                             ---------------
6660368097802      Full               3/18/2019 8:09:35 PM   MSSQLSERVER;model             AzureWorkload
````

Usare il filtro 'RecoveryPointId' o una matrice per recuperare il punto di ripristino pertinenti.

````powershell
$FullRP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -RecoveryPointId "6660368097802"
````

#### <a name="fetch-point-in-time-recovery-point"></a>Recuperare un punto di ripristino temporizzato in

Se l'utente vuole ripristinare il database in un determinato punto nel tempo, usare [Get-AzRecoveryServicesBackupRecoveryLogChain](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryLogChain?view=azps-1.5.0) cmdlet di Powershell. Il cmdlet restituisce un elenco di date che rappresentano l'ora di inizio e fine di una catena di log non interrotta, continua per tale elemento di backup di SQL. Il desiderato point-in-time deve essere entro questo intervallo.

```powershell
Get-AzRecoveryServicesBackupRecoveryLogChain -Item $bkpItem -Item -VaultId $targetVault.ID
```

L'output sarà simile all'esempio seguente.

````powershell
ItemName                       StartTime                      EndTime
--------                       ---------                      -------
SQLDataBase;MSSQLSERVER;azu... 3/18/2019 8:09:35 PM           3/19/2019 12:08:32 PM
````

L'output sopra riportato significa che l'utente può ripristinare in qualsiasi punto nel tempo tra l'ora di inizio visualizzato e l'ora di fine. Gli orari sono in formato UTC. Creare qualsiasi punto nel tempo in Powershell che è compreso nell'intervallo illustrato in precedenza.

> [!NOTE]
> Quando un log punto nel tempo selezionato per il ripristino, utente necessario non specificare il punto di partenza, ad esempio, il backup completo da cui viene ripristinato il database. Servizio Backup di Azure si occuperà del piano di ripristino intero, ad esempio, che completa di backup da scegliere, quali i backup da applicare e così via del log.

### <a name="determine-recovery-configuration"></a>Determinare la configurazione di ripristino

In caso di ripristino di database SQL, sono supportati i seguenti scenari di ripristino.

1. Il database di SQL sottoposte a backup con dati da un altro punto di ripristino - OriginalWorkloadRestore viene sottoposto a override
2. Il ripristino di database SQL come un nuovo database nella stessa istanza di SQL - AlternateWorkloadRestore
3. Il ripristino di database SQL come un nuovo database in un'altra istanza SQL in un'altra VM di SQL - AlternateWorkloadRestore

Dopo il recupero il punto di ripristino pertinente (distinti o log point-in-time), usare [Get-AzRecoveryServicesBackupWorkloadRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) cmdlet di Powershell per recuperare l'oggetto di configurazione di ripristino in base al piano di ripristino desiderato.

#### <a name="original-workload-restore"></a>Ripristino di carichi di lavoro originale

Per eseguire l'override del database di backup con i dati dal punto di ripristino, specificare solo il flag a destra e il punto di ripristino pertinenti come illustrato di seguito riportati esempi.

##### <a name="original-restore-with-distinct-recovery-point"></a>Ripristino originale con il punto di ripristino distinto

````powershell
$OverwriteWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -OriginalWorkloadRestore -VaultId $targetVault.ID
````

##### <a name="original-restore-with-log-point-in-time"></a>Ripristino originale con log point-in-time

```powershell
$OverwriteWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem  -OriginalWorkloadRestore -VaultId $targetVault.ID
```

#### <a name="alternate-workload-restore"></a>Ripristino di carichi di lavoro alternative

> [!IMPORTANT]
> Un backup dei database SQL possono essere ripristinati come un nuovo database a un altro SQLInstance, solo in una macchina virtuale di Azure registrati nell'insieme di credenziali.

Come illustrato in precedenza, se la destinazione SQLInstance si trova all'interno di un'altra macchina virtuale di Azure, assicurarsi che sia [registrato nell'insieme di credenziali](#registering-the-sql-vm) e il parametro SQLInstance pertinente viene visualizzato come un elemento da proteggere.

````powershell
$TargetInstance = Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -ItemType SQLInstance -Name "<SQLInstance Name>" -ServerName "<SQL VM name>" -VaultId $targetVault.ID
````

Quindi sufficiente passare il punto di ripristino pertinente, l'istanza di SQL di destinazione con il flag a destra come mostrato di seguito.

##### <a name="alternate-restore-with-distinct-recovery-point"></a>Ripristino alternativo con distinct punto di ripristino

````powershell
$AnotherInstanceWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetItem $TargetInstance -AlternateWorkloadRestore -VaultId $targetVault.ID
````

##### <a name="alternate-restore-with-log-point-in-time"></a>Ripristino alternativo con log point-in-time

```powershell
$AnotherInstanceWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem -AlternateWorkloadRestore -VaultId $targetVault.ID
```

L'oggetto di configurazione del punto di ripristino finale ottenuto da [Get-AzRecoveryServicesBackupWorkloadRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) cmdlet PS ha tutte le informazioni rilevanti per il ripristino e come illustrato di seguito.

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

È possibile modificare i campi nome, OverwriteWLIfpresent, NoRecoveryMode e targetPhysicalPath database ripristinati. Ottenere altri dettagli per i percorsi dei file di destinazione come illustrato di seguito.

````powershell
$AnotherInstanceWithFullConfig.targetPhysicalPath

MappingType SourceLogicalName SourcePath                  TargetPath
----------- ----------------- ----------                  ----------
Data        azurebackup1      F:\Data\azurebackup1.mdf    F:\Data\azurebackup1_1553001753.mdf
Log         azurebackup1_log  F:\Log\azurebackup1_log.ldf F:\Log\azurebackup1_log_1553001753.ldf
````

Impostare le proprietà PS come valori di stringa, come illustrato di seguito.

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
> Assicurarsi che l'oggetto di configurazione di ripristino finale abbia tutti i valori necessari e appropriati poiché si baserà l'operazione di ripristino per l'oggetto di configurazione.

### <a name="restore-with-relevant-configuration"></a>Ripristino con configurazione pertinente

Una volta che l'oggetto di configurazione di ripristino pertinente viene ricevuto e verificato, usare il [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Restore-AzRecoveryServicesBackupItem?view=azps-1.5.0) cmdlet di Powershell per avviare il processo di ripristino.

````powershell
Restore-AzRecoveryServicesBackupItem -WLRecoveryConfig $AnotherInstanceWithLogConfig -VaultId $targetVault.ID
````

L'operazione di ripristino restituisce un processo per tenere traccia.

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Restore              InProgress           3/17/2019 10:02:45 AM                                3274xg2b-e4fg-5952-89b4-8cb566gc1748
````

## <a name="manage-sql-backups"></a>Gestire i backup SQL

### <a name="on-demand-backup"></a>Backup su richiesta

Dopo aver abilitato il backup per un database, utente può anche attivare un backup su richiesta per il database usando [Backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Backup-AzRecoveryServicesBackupItem?view=azps-1.5.0) cmdlet di Powershell. Nell'esempio seguente attiva un backup completo in un database SQL con compressione abilitata e il backup completo deve essere conservato per 60 giorni.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
Backup-AzRecoveryServicesBackupItem -Item $bkpItem -BackupType Full -EnableCompression -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
````

Il comando di backup ad hoc restituisce un processo per tenere traccia.

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Backup               InProgress           3/18/2019 8:41:27 PM                                2516bb1a-d3ef-4841-97a3-9ba455fb0637
````

Se l'output viene persa o se si desidera ottenere l'ID del processo rilevante [Ottiene l'elenco dei processi](#track-azure-backup-jobs) da Backup di Azure del servizio e quindi tenerne traccia e i relativi dettagli.

### <a name="change-policy-for-backup-items"></a>Modifica criteri per gli elementi di backup

Utente può modificare i criteri esistenti o modificare i criteri dell'elemento di backup da Policy1 a criterio2. Per passare i criteri per un elemento di backup, è sufficiente recuperare i criteri pertinenti ed eseguire il backup di elemento e usare la [Enable-AzRecoveryServices](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) comando con elemento di backup come parametro.

````powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName>
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType MSSQL -BackupManagementType AzureWorkload -Name "<BackupItemName>"
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1
````

Il comando attende fino a quando non di configurare il backup viene completato e restituisce l'output seguente.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="re-register-sql-vms"></a>Registrare nuovamente le macchine virtuali SQL

> [!WARNING]
> Assicurarsi di leggere ciò [documento](backup-sql-server-azure-troubleshoot.md#re-registration-failures) per comprendere i sintomi di errore e le cause prima di tentare di ri-registrazione

Per attivare la ri-registrazione di VM di SQL, recuperare il contenitore di backup pertinente e passarla al cmdlet register.

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Register-AzRecoveryServicesBackupContainer -Container $SQLContainer -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID
````

### <a name="stop-protection"></a>Arresta protezione

#### <a name="retain-data"></a>Mantenere i dati

Se l'utente desidera arrestare la protezione dati, è possibile usare la [Disable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) cmdlet di Powershell. Questa operazione interromperà i backup pianificati, ma i dati sottoposti a backup fino a questo punto vengono conservati all'infinito.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
````

#### <a name="delete-backup-data"></a>Elimina dati di backup

Per rimuovere completamente i dati di backup archiviati nell'insieme di credenziali, è sufficiente aggiungere '-flag/passaggio degli RemoveRecoveryPoints per la ['disable' comando protezione](#retain-data).

````powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
````

#### <a name="disable-auto-protection"></a>Disabilitare la protezione automatica

Se è stato configurato un SQLInstance autoprotection, utente possibile disabilitarlo usando il [Disable-AzRecoveryServicesBackupAutoProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) cmdlet di Powershell.

````powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Disable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetvault.ID
````

#### <a name="unregister-sql-vm"></a>Annullare la registrazione della macchina virtuale SQL

Se tutti i database di SQL server [non sono più presenti dati protetti e non backup](#delete-backup-data), utente può annullare la registrazione di VM di SQL da questo insieme di credenziali. Solo allora utente può proteggere i database a un altro insieme di credenziali. Uso [Unregister-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Unregister-AzRecoveryServicesBackupContainer?view=azps-1.5.0) cmdlet di PS per annullare la registrazione di VM di SQL.

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
 Unregister-AzRecoveryServicesBackupContainer -Container $SQLContainer -VaultId $targetvault.ID
````

### <a name="track-azure-backup-jobs"></a>Tenere traccia dei processi di Backup di Azure

È importante tenere presente che Backup di Azure rileva solo processi utente attivato in backup di SQL. I backup pianificati (inclusi i backup del log) non sono visibili nel portale o powershell. Tuttavia, eventuali pianificati processi esito negativo, un [avviso backup](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) generato e visualizzato nel portale. [Usare monitoraggio di Azure](backup-azure-monitoring-use-azuremonitor.md) per tenere traccia di tutti i processi pianificati e altre informazioni rilevanti.

Gli utenti possono tenere traccia delle operazioni ad hoc/utente attivato con l'ID processo restituito nel [output](#on-demand-backup) di processi asincroni, ad esempio backup. Uso [Get-AzRecoveryServicesBackupJobDetail](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJobDetail) cmdlet di PS per tenere traccia del processo e i dettagli.

````powershell
 Get-AzRecoveryServicesBackupJobDetails -JobId 2516bb1a-d3ef-4841-97a3-9ba455fb0637 -VaultId $targetVault.ID
````

Per ottenere l'elenco dei processi ad hoc e i relativi stati dal servizio Backup di Azure, usare [Get-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJob?view=azps-1.5.0) cmdlet di Powershell. L'esempio seguente restituisce tutti i processi SQL in corso.

```powershell
Get-AzRecoveryServicesBackupJob -Status InProgress -BackupManagementType AzureWorkload
```

Per annullare un processo in corso, usare il [Stop-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Stop-AzRecoveryServicesBackupJob?view=azps-1.5.0) cmdlet di Powershell.

## <a name="managing-sql-always-on-availability-groups"></a>Gestione dei gruppi SQL disponibilità Always On

Per SQL gruppi di disponibilità AlwaysOn, assicurarsi di [registra tutti i nodi](#registering-the-sql-vm) del gruppo di disponibilità (AG). Dopo la registrazione viene eseguita per tutti i nodi, un oggetto gruppo di disponibilità SQL in modo logico viene creato sotto gli elementi da proteggere. Verranno elencati i database nel gruppo di disponibilità SQL come 'SQLDatabase'. Verranno visualizzati i nodi come istanze autonome e i database SQL predefinito interno di esse verranno elencati come anche i database SQL.

Ad esempio, si supponga che un gruppo di disponibilità SQL ha due nodi: "sql-server-0" e "sql-server-1" e 1 gruppo di disponibilità SQL DB. Una volta registrati entrambi questi nodi, se utente [vengono elencati gli elementi da proteggere](#fetching-sql-dbs), vengono elencati i componenti seguenti

1. Un oggetto - gruppo di disponibilità SQL da proteggere come SQLAvailabilityGroup tipo di elemento
2. Gruppo di disponibilità un SQL database - tipo di elemento da proteggere come database SQL
3. il tipo di elemento da proteggere SQL-server-0 - come SQLInstance
4. SQL-server-1 - tipo di elemento da proteggere come SQLInstance
5. Qualsiasi impostazione predefinita i database SQL (master, model, msdb) in sql-server-0 - tipo di elemento da proteggere come database SQL
6. Qualsiasi impostazione predefinita i database SQL (master, model, msdb) in sql-server-1 - tipo di elemento da proteggere come database SQL

SQL-server-0, 1 di sql server sarà elencato anche come "AzureVMAppContainer" quando [sono elencati i contenitori di backup](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupContainer?view=azps-1.5.0).

Recuperare il database SQL corrispondente a [abilitare il backup](#configuring-backup) e il [backup adhoc](#on-demand-backup) e [ripristinare i cmdlet di PS](#restore-sql-dbs) sono identici.
