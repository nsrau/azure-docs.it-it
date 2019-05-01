---
title: Eseguire la migrazione di SQL Server a Istanza gestita di database SQL di Azure con Servizio Migrazione del database e PowerShell | Microsoft Docs
description: Informazioni su come eseguire la migrazione da SQL Server locale a Istanza gestita di database SQL di Azure tramite Azure PowerShell.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 04/29/2019
ms.openlocfilehash: 96ee3f5e1b3cfe67cb75e50c6247e41f0d901393
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2019
ms.locfileid: "64867910"
---
# <a name="migrate-sql-server-on-premises-to-an-azure-sql-database-managed-instance-using-azure-powershell"></a>Eseguire la migrazione di SQL Server locale a un'istanza gestita di Database SQL di Azure usando Azure PowerShell
In questo articolo, si esegue la migrazione di **Adventureworks2016** database ripristinato a un'istanza locale di SQL Server 2005 o versioni successive a un Database SQL di Azure istanza gestita con Microsoft Azure PowerShell. È possibile migrare i database da un'istanza di SQL Server locale a un'istanza gestita di Database SQL di Azure usando il `Az.DataMigration` modulo in Microsoft Azure PowerShell.

In questo articolo viene spiegato come:
> [!div class="checklist"]
>
> * Creare un gruppo di risorse.
> * Creare un'istanza del servizio migrazione del Database.
> * Creare un progetto di migrazione in un'istanza del servizio migrazione del Database.
> * Eseguire la migrazione.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Questo articolo include dettagli su come eseguire le migrazioni sia online che offline.

## <a name="prerequisites"></a>Prerequisiti

Per completare questi passaggi è necessario disporre di:

* [SQL Server 2016 o versione successiva](https://www.microsoft.com/sql-server/sql-server-downloads) (qualsiasi edizione).
* Una copia locale del **AdventureWorks2016** database, che può essere scaricato [qui](https://docs.microsoft.com/sql/samples/adventureworks-install-configure?view=sql-server-2017).
* Per abilitare il protocollo TCP/IP che è disabilitato per impostazione predefinita con l'installazione di SQL Server Express. Abilitare il protocollo TCP/IP seguendo l'articolo [Abilitare o disabilitare un protocollo di rete server](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
* Per configurare [Windows Firewall per l'accesso al motore di database](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Una sottoscrizione di Azure. Se non se ne ha una, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Un'istanza gestita di Database SQL di Azure. È possibile creare un'istanza gestita di Database SQL di Azure seguendo le istruzioni riportate nell'articolo [creare un'istanza gestita di Database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started).
* Per scaricare e installare [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 o versione successiva.
* Una rete virtuale (VNet) di Azure create usando il modello di distribuzione Azure Resource Manager, che fornisce il servizio migrazione del Database di Azure con connettività site-to-site ai server di origine locali usando [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) oppure [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
* Una valutazione completata della migrazione di database e lo schema locale mediante Data Migration Assistant, come descritto nell'articolo [eseguire una valutazione della migrazione di SQL Server](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem).
* Per scaricare e installare il `Az.DataMigration` module (versione 0.7.2 o versioni successive) da PowerShell Gallery usando [cmdlet di PowerShell Install-Module](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1).
* Per assicurarsi che le credenziali usate per connettersi all'istanza di SQL Server di origine abbiano il [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) l'autorizzazione.
* Per assicurarsi che le credenziali usate per connettersi al Database SQL di Azure di destinazione istanza gestita ha l'autorizzazione CONTROL DATABASE nel database di istanza Database SQL di Azure gestiti di destinazione.

    > [!IMPORTANT]
    > Per la migrazione online, è necessario già stato configurato le credenziali di Azure Active Directory. Per altre informazioni, vedere l'articolo [usare il portale per creare un'entità applicazione e del servizio che può accedere alle risorse di Azure AD](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Accedi alla sottoscrizione di Microsoft Azure

Accedere alla sottoscrizione di Azure usando PowerShell. Per altre informazioni, vedere l'articolo [Accedi con Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

Creare un gruppo di risorse usando il [ `New-AzResourceGroup` ](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) comando.

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nel *Stati Uniti orientali* area.

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Creare un'istanza del servizio migrazione del Database di Azure

È possibile creare una nuova istanza del Servizio Migrazione del database di Azure tramite il cmdlet `New-AzDataMigrationService`.
Questo cmdlet si aspetta i parametri obbligatori seguenti:

* *Nome del gruppo di risorse di Azure*. È possibile usare [ `New-AzResourceGroup` ](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) comando per creare un gruppo di risorse di Azure come indicato in precedenza e fornire il suo nome come parametro.
* *Nome del servizio*. Stringa che corrisponde al nome di servizio univoco desiderato per il servizio migrazione del Database di Azure.
* *Località*. Specifica il percorso del servizio. Specificare un percorso di centro dati di Azure, ad esempio Stati Uniti occidentali o Asia sud-orientale.
* *Sku*. Questo parametro corrisponde al nome Sku DMS. Sono attualmente supportati i nomi Sku *Basic_1vCore*, *Basic_2vCores*, *GeneralPurpose_4vCores*.
* *Identificatore della subnet virtuale*. È possibile usare il cmdlet [ `New-AzVirtualNetworkSubnetConfig` ](https://docs.microsoft.com//powershell/module/az.network/new-azvirtualnetworksubnetconfig) per creare una subnet.

L'esempio seguente crea un servizio denominato *MyDMS* nel gruppo di risorse *MyDMSResourceGroup* che si trova nel *Stati Uniti orientali* area usando una rete virtuale denominata  *MyVNET* e una subnet denominata *MySubnet*.

> [!IMPORTANT]
> Il frammento di codice riportato di seguito è per una migrazione offline, che non richiede un'istanza del servizio migrazione del Database di Azure in base uno SKU Premium. Per una migrazione online, il valore del parametro - Sku deve includere uno SKU Premium.

```powershell
 $vNet = Get-AzVirtualNetwork -ResourceGroupName MyDMSResourceGroup -Name MyVNET

$vSubNet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vNet -Name MySubnet

$service = New-AzDms -ResourceGroupName myResourceGroup `
  -ServiceName MyDMS `
  -Location EastUS `
  -Sku Basic_2vCores `  
  -VirtualSubnetId $vSubNet.Id`
```

## <a name="create-a-migration-project"></a>Creare un progetto di migrazione

Dopo aver creato un'istanza del Servizio Migrazione del database di Azure, creare un progetto di migrazione. Un progetto Servizio Migrazione del database di Azure richiede informazioni di connessione per entrambe le istanze di origine e di destinazione, nonché un elenco dei database che si desidera migrare come parte del progetto.

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Creare un oggetto informazioni di connessione del database per le connessioni di origine e di destinazione

È possibile creare un oggetto informazioni di connessione di Database usando la `New-AzDmsConnInfo` cmdlet, che prevede i parametri seguenti:

* *ServerType*. Il tipo di connessione al database richiesta, ad esempio SQL, Oracle o MySQL. Usare SQL per SQL Server e SQL Azure.
* *DataSource*. Il nome o indirizzo IP di un'istanza di SQL Server o Database SQL di Azure.
* *AuthType*. Il tipo di autenticazione per la connessione, che può essere SqlAuthentication o WindowsAuthentication.
* *TrustServerCertificate*. Questo parametro imposta un valore che indica se il canale è crittografato, bypassando l'analisi della catena di certificati per convalidare l'attendibilità. Il valore può essere `$true` o `$false`.

L'esempio seguente crea un oggetto informazioni di connessione per un'origine SQL Server denominata *MySourceSQLServer* mediante l'autenticazione sql:

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

L'esempio seguente mostra la creazione delle informazioni di connessione per un server di istanza gestita di Database SQL di Azure denominato 'targetmanagedinstance.database.windows.net' con l'autenticazione sql:

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "targetmanagedinstance.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Fornire i database per il progetto di migrazione

Creare un elenco di `AzDataMigrationDatabaseInfo` gli oggetti che specifica i database come parte del progetto di servizio migrazione del Database, che può essere specificato come parametro per la creazione del progetto. È possibile usare il cmdlet `New-AzDataMigrationDatabaseInfo` per creare `AzDataMigrationDatabaseInfo`.

L'esempio seguente crea il `AzDataMigrationDatabaseInfo` del progetto per il **AdventureWorks2016** del database e lo aggiunge all'elenco da specificare come parametro per la creazione del progetto.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Creare un oggetto progetto

Infine, è possibile creare un progetto di servizio migrazione del Database denominato *MyDMSProject* che si trova *Stati Uniti orientali* usando `New-AzDataMigrationProject` e aggiungere le connessioni di origine e di destinazione create in precedenza e il elenco di database per eseguire la migrazione.

```powershell
$project = New-AzDataMigrationProject -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName MyDMSProject `
  -Location EastUS `
  -SourceType SQL `
  -TargetType SQLMI `
  -SourceConnection $sourceConnInfo `
  -TargetConnection $targetConnInfo `
  -DatabaseInfo $dbList
```

## <a name="create-and-start-a-migration-task"></a>Creare e avviare un'attività di migrazione

Successivamente, creare e avviare un'attività di migrazione di Database di Azure. Questa attività richiede informazioni sulle credenziali di connessione per sia l'origine e destinazione, nonché l'elenco delle tabelle di database da sottoporre a migrazione e le informazioni già fornite con il progetto creato come prerequisito.

### <a name="create-credential-parameters-for-source-and-target"></a>Creare i parametri delle credenziali per l'origine e la destinazione

Creare le credenziali di sicurezza come connessione una [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) oggetto.

L'esempio seguente illustra la creazione di *PSCredential* gli oggetti per le connessioni di origine e di destinazione, fornendo le password come variabili di stringhe *$sourcePassword* e *$ targetPassword*.

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-backup-fileshare-object"></a>Creare un oggetto condivisione file di backup

A questo punto creare un oggetto condivisione file che rappresenta la condivisione di rete SMB locale a cui servizio migrazione del Database può richiedere l'origine dei backup di database usando la `New-AzDmsFileShare` cmdlet.

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>Creare l'oggetto di database selezionato

Il passaggio successivo consiste nel selezionare i database di origine e di destinazione utilizzando il `New-AzDmsSelectedDB` cmdlet.

L'esempio seguente è per la migrazione di un singolo database di SQL Server a un'istanza gestita di Database SQL di Azure:

```powershell
$selectedDbs = @()
$selectedDbs += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

Se un'intera istanza di SQL Server deve istanza gestita di una modalità lift-and-shift in un Database SQL di Azure, quindi di seguito viene fornito un ciclo per sfruttare tutti i database dall'origine. Nell'esempio seguente, per $Server $SourceUserName e $SourcePassword, specificare l'origine dei dettagli di SQL Server.

```powershell
$Query = "(select name as Database_Name from master.sys.databases where Database_id>4)";
$Databases= (Invoke-Sqlcmd -ServerInstance "$Server" -Username $SourceUserName
-Password $SourcePassword -database master -Query $Query)
$selectedDbs=@()
foreach($DataBase in $Databases.Database_Name)
    {
      $SourceDB=$DataBase
      $TargetDB=$DataBase
      
$selectedDbs += New-AzureRmDmsSelectedDB -MigrateSqlServerSqlDbMi `
                                              -Name $SourceDB `
                                              -TargetDatabaseName $TargetDB `
                                              -BackupFileShare $backupFileShare
      }
```

### <a name="sas-uri-for-azure-storage-container"></a>URI di firma di accesso condiviso per il contenitore di Archiviazione di Azure

Creare variabili che contengono l'URI di firma di accesso condiviso che consente al Servizio Migrazione del database di Azure di accedere al contenitore dell'account di archiviazione in cui il servizio caricherà i file di backup per la migrazione.

```powershell
$blobSasUri="https://mystorage.blob.core.windows.net/test?st=2018-07-13T18%3A10%3A33Z&se=2019-07-14T18%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=qKlSA512EVtest3xYjvUg139tYSDrasbftY%3D"
```

### <a name="additional-configuration-requirements"></a>Requisiti di configurazione aggiuntivi

Esistono alcuni requisiti aggiuntivi che è necessario risolvere, ma differiscono a seconda se si stia eseguendo una migrazione offline oppure online.

#### <a name="offline-migrations"></a>Migrazioni offline

Per le migrazioni offline solo eseguire le attività di configurazione aggiuntive seguenti.

* **Selezionare gli account di accesso**. Creare un elenco di account di accesso da sottoporre a migrazione come illustrato nell'esempio seguente:

    ```powershell
    $selectedLogins = @(“user1”, “user2”)
    ```

    > [!IMPORTANT]
    > Servizio migrazione del Database di Azure supporta attualmente solo la migrazione account di accesso SQL.

* **Selezionare i processi di agent**. Creare l'elenco dell'agente di processi da sottoporre a migrazione come illustrato nell'esempio seguente:

    ```powershell
    $selectedAgentJobs = @("agentJob1", "agentJob2")
    ```

    > [!IMPORTANT]
    > Servizio migrazione del Database di Azure supporta attualmente solo i processi con i passaggi di processo del sottosistema di T-SQL.

#### <a name="online-migrations"></a>Migrazioni online

Per migrazioni solo online, eseguire le attività di configurazione aggiuntive seguenti.

* **Configurare l'App di Azure Active Directory**

    ```powershell
    $pwd = "Azure App Key"
    $appId = "Azure App Id"
    $AppPasswd = ConvertTo-SecureString $pwd -AsPlainText -Force
    $app = New-AzDmsAdApp -ApplicationId $appId -AppKey $AppPasswd
    ```

* **Configurare risorse di archiviazione**

    ```powershell
    $storageResourceId = "Storage Resource Id"
    ```

### <a name="create-and-start-the-migration-task"></a>Creare e avviare l'attività di migrazione

Usare il cmdlet `New-AzDataMigrationTask` per creare e avviare un'attività di migrazione.

#### <a name="specify-parameters"></a>Specificare i parametri

##### <a name="common-parameters"></a>Parametri comuni

Indipendentemente dal fatto che si sta eseguendo una migrazione offline oppure online, il `New-AzDataMigrationTask` cmdlet si aspetta i parametri seguenti:

* *TaskType*. Tipo di attività di migrazione da creare per la migrazione da SQL Server a Istanza gestita di database SQL di Azure. Il tipo previsto è *MigrateSqlServerSqlDbMi*. 
* *Nome del gruppo di risorse*. Nome del gruppo di risorse di Azure in cui creare l'attività.
* *ServiceName*. Istanza del Servizio Migrazione del database di Azure in cui creare l'attività.
* *ProjectName*. Nome del progetto di migrazione del Servizio Migrazione del database di Azure in cui creare l'attività. 
* *TaskName*. Nome dell'attività da creare. 
* *SourceConnection*. Oggetto AzDmsConnInfo che rappresenta la connessione di SQL Server di origine.
* *TargetConnection*. Oggetto AzDmsConnInfo che rappresenta la connessione di istanza gestita di Azure SQL Database di destinazione.
* *SourceCred*. Oggetto [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) per la connessione al server di origine.
* *TargetCred*. Oggetto [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) per la connessione al server di destinazione.
* *SelectedDatabase*. Oggetto AzDataMigrationSelectedDB che rappresenta il mapping del database di origine e di destinazione.
* *BackupFileShare*. L'oggetto di condivisione di rete che rappresenta la condivisione di rete locale dove il Servizio Migrazione del database di Azure può salvare i backup del database di origine.
* *BackupBlobSasUri*. L'URI di firma di accesso condiviso che consente al Servizio Migrazione del database di Azure di accedere al contenitore dell'account di archiviazione in cui il servizio caricherà i file di backup. Informazioni su come ottenere l'URI di firma di accesso condiviso per un contenitore BLOB.
* *SelectedLogins*. L'elenco di account di accesso selezionato per eseguire la migrazione.
* *SelectedAgentJobs*. L'elenco di processi dell'agente selezionati per eseguire la migrazione.

##### <a name="additional-parameters"></a>Parametri aggiuntivi

Il `New-AzDataMigrationTask` cmdlet si aspetta anche parametri che sono univoci per il tipo di migrazione, non in linea o non in linea, che si sta eseguendo.

* **Le migrazioni offline**. Per le migrazioni offline il `New-AzDataMigrationTask` cmdlet prevede inoltre i parametri seguenti:

  * *SelectedLogins*. L'elenco di account di accesso selezionato per eseguire la migrazione.
  * *SelectedAgentJobs*. L'elenco di processi dell'agente selezionati per eseguire la migrazione.

* **Le migrazioni online**. Per la migrazione online, il `New-AzDataMigrationTask` cmdlet prevede inoltre i parametri seguenti.

* *AzureActiveDirectoryApp*. Applicazione di Active Directory.
* *StorageResourceID*. ID risorsa dell'Account di archiviazione.

#### <a name="create-and-start-an-offline-migration-task"></a>Creare e avviare un'attività di migrazione offline

Nell'esempio seguente crea e avvia un'attività di migrazione offline denominata **myDMSTask**:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMi `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -BackupFileShare $backupFileShare `
  -BackupBlobSasUri $blobSasUri `
  -SelectedLogins $selectedLogins `
  -SelectedAgentJobs $selectedJobs `
```

#### <a name="create-and-start-an-online-migration-task"></a>Creare e avviare un'attività di migrazione online

Nell'esempio seguente crea e avvia un'attività di migrazione online denominata **myDMSTask**:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMiSync `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -BackupFileShare $backupFileShare `
  -SelectedDatabase $selectedDbs `
  -AzureActiveDirectoryApp $app `
  -StorageResourceId $storageResourceId
```

## <a name="monitor-the-migration"></a>Monitorare la migrazione

Per monitorare la migrazione, eseguire le attività seguenti.

1. Consolidare tutti i dettagli della migrazione in una variabile denominata $CheckTask.

    Per combinare i dettagli sulla migrazione, ad esempio proprietà, lo stato e informazioni di database associati alla migrazione, usare il frammento di codice seguente:

    ```powershell
    $CheckTask= Get-AzDataMigrationTask     -ResourceGroupName myResourceGroup `
                                            -ServiceName $service.Name `
                                        -ProjectName $project.Name `
                                            -Name myDMSTask `
                                            -ResultType DatabaseLevelOutput `
                        -Expand 
    Write-Host ‘$CheckTask.ProjectTask.Properties.Output’
    ```

2. Usare il `$CheckTask` variabile per ottenere lo stato corrente dell'attività di migrazione.

    Usare il `$CheckTask` variabili per ottenere lo stato corrente dell'attività di migrazione, è possibile monitorare l'attività di migrazione in esecuzione eseguendo una query la proprietà state dell'attività, come illustrato nell'esempio seguente:

    ```powershell
    if (($CheckTask.ProjectTask.Properties.State -eq "Running") -or ($CheckTask.ProjectTask.Properties.State -eq "Queued"))
    {
      write-host "migration task running"
    }
    Else if($CheckTask.ProjectTask.Properties.State -eq "Succeeded")
    { 
      write-host "Migration task is completed Successfully"
    }
    Else if($CheckTask.ProjectTask.Properties.State -eq "Failed" -or $CheckTask.ProjectTask.Properties.State -eq "FailedInputValidation"  -or $CheckTask.ProjectTask.Properties.State -eq "Faulted")
    { 
      write-host “Migration Task Failed”
    }
    ```

## <a name="performing-the-cutover-online-migrations-only"></a>Eseguire la migrazione completa (online solo migrazioni)

Con una migrazione online, viene eseguito un backup completo e il ripristino di database e quindi procede di lavoro sul ripristino di log delle transazioni archiviati nel BackupFileShare.

Quando il database in un'istanza gestita di Database SQL di Azure viene aggiornato con dati più recenti e sia sincronizzato con il database di origine, è possibile eseguire una migrazione completa.

Nell'esempio seguente verrà completato il cutover\migration. Gli utenti richiamano questo comando a propria discrezione.

```powershell
$command = Invoke-AzDmsCommand -CommandType CompleteSqlMiSync `
                               -ResourceGroupName myResourceGroup `
                               -ServiceName $service.Name `
                               -ProjectName $project.Name `
                               -TaskName myDMSTask `
                               -DatabaseName "Source DB Name"
```

## <a name="deleting-the-instance-of-azure-database-migration-service"></a>Eliminazione dell'istanza del servizio migrazione del Database di Azure

Al termine della migrazione, è possibile eliminare l'istanza del servizio migrazione del Database:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="additional-resources"></a>Risorse aggiuntive

Per informazioni sugli scenari di migrazione aggiuntivi (coppie origine/destinazione), vedere Microsoft [Guida alla migrazione di Database](https://datamigration.microsoft.com/).

## <a name="next-steps"></a>Passaggi successivi

Scopri di più sul servizio migrazione del Database di Azure nell'articolo [che cos'è il servizio migrazione del Database di Azure?](https://docs.microsoft.com/azure/dms/dms-overview).
