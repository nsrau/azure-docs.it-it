---
title: "PowerShell: Eseguire la migrazione di SQL Server all'istanza gestita SQLPowerShell: Migrate SQL Server to SQL managed instance"
titleSuffix: Azure Database Migration Service
description: Informazioni sulla migrazione da SQL Server locale all'istanza gestita del database SQL di Azure usando Azure PowerShell e il servizio migrazione del database di Azure.Learn to migrate from on-premises SQL Server to Azure SQL Database managed instance by using Azure PowerShell and the Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 9ea9f55681b93e79eec836f5808d2c6feaa6bb29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650725"
---
# <a name="migrate-sql-server-to-sql-database-managed-instance-with-powershell--azure-database-migration-service"></a>Eseguire la migrazione di SQL Server all'istanza gestita del database SQL con PowerShell & servizio di migrazione del database di AzureMigrate SQL Server to SQL Database managed instance with PowerShell & Azure Database Migration Service

In questo articolo viene eseguita la migrazione del database **Adventureworks2016** ripristinato in un'istanza locale di SQL Server 2005 o versione successiva in un'istanza gestita del database SQL di Azure tramite Microsoft Azure PowerShell. È possibile eseguire la migrazione di database da un'istanza di SQL `Az.DataMigration` Server locale a un'istanza gestita del database SQL di Azure usando il modulo in Microsoft Azure PowerShell.You can migrate databases from an on-premises SQL Server instance to an Azure SQL Database managed instance by using the module in Microsoft Azure PowerShell.

In questo articolo vengono illustrate le operazioni seguenti:
> [!div class="checklist"]
>
> * Creare un gruppo di risorse.
> * Creare un'istanza del Servizio Migrazione del database di Azure.
> * Creare un progetto di migrazione in un'istanza del servizio Migrazione del database di Azure.Create a migration project in an instance of Azure Database Migration Service.
> * Eseguire la migrazione.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Questo articolo include informazioni dettagliate su come eseguire migrazioni online e offline.

## <a name="prerequisites"></a>Prerequisiti

Per completare questi passaggi è necessario disporre di:

* [SQL Server 2016 o versione successiva](https://www.microsoft.com/sql-server/sql-server-downloads) (qualsiasi edizione).
* Una copia locale del database **AdventureWorks2016AdventureWorks2016** , disponibile per il download [qui](https://docs.microsoft.com/sql/samples/adventureworks-install-configure?view=sql-server-2017).
* Per abilitare il protocollo TCP/IP che è disabilitato per impostazione predefinita con l'installazione di SQL Server Express. Abilitare il protocollo TCP/IP seguendo l'articolo [Abilitare o disabilitare un protocollo di rete server](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
* Per configurare [Windows Firewall per l'accesso al motore di database](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Una sottoscrizione di Azure. Se non ne hai uno, [crea un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Un'istanza gestita del database SQL di Azure.An Azure SQL Database managed instance. È possibile creare un'istanza gestita del database SQL di Azure seguendo i dettagli nell'articolo [Creare un'istanza gestita del database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)di Azure.You can create an Azure SQL Database managed instance by following the detail in the article Create an Azure SQL Database managed instance.
* Per scaricare e installare [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 o versione successiva.
* Una rete virtuale di Microsoft Azure creata utilizzando il modello di distribuzione di Azure Resource Manager, che fornisce al servizio di migrazione del database di Azure la connettività da sito a sito ai server di origine locali tramite [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) o [VPN.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)
* Valutazione completa della migrazione del database e dello schema locale tramite Assistente migrazione dati, come descritto nell'articolo [Esecuzione di una valutazione](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)della migrazione di SQL Server .
* Per scaricare e `Az.DataMigration` installare il modulo (versione 0.7.2 o successiva) da PowerShell Gallery utilizzando il [cmdlet PowerShell Install-Module](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1).
* Per assicurarsi che le credenziali utilizzate per connettersi all'istanza di SQL Server di origine dispongano dell'autorizzazione [CONTROL SERVER.](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql)
* Per assicurarsi che le credenziali utilizzate per connettersi all'istanza gestita del database SQL di Azure di destinazione dispongano dell'autorizzazione CONTROL DATABASE nei database dell'istanza gestita del database SQL di Azure di destinazione.

    > [!IMPORTANT]
    > Per le migrazioni online, è necessario avere già configurato le credenziali di Azure Active Directory.For online migrations, you must already have set up your Azure Active Directory credentials. Per altre informazioni, vedere l'articolo [Usare il portale per creare un'applicazione Azure AD e un'entità servizio in grado](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)di accedere alle risorse.

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Accedere alla sottoscrizione di Microsoft AzureSign in to your Microsoft Azure subscription

Accedere alla sottoscrizione di Azure tramite PowerShell.Sign in to your Azure subscription by using PowerShell. Per altre informazioni, vedere l'articolo Accedere con Azure PowerShell.For more information, see the article [Sign in with Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

Creare un gruppo di [`New-AzResourceGroup`](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) risorse usando il comando.

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nell'area *Stati Uniti orientali.*

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Creare un'istanza del Servizio Migrazione del database di Azure

È possibile creare una nuova istanza del Servizio Migrazione del database di Azure tramite il cmdlet `New-AzDataMigrationService`.
Questo cmdlet si aspetta i parametri obbligatori seguenti:

* *Nome del gruppo di risorse di Azure*. È possibile [`New-AzResourceGroup`](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) usare il comando per creare un gruppo di risorse di Azure come illustrato in precedenza e specificarne il nome come parametro.
* *Nome del servizio*. Stringa che corrisponde al nome di servizio univoco desiderato per il servizio migrazione del database di Azure.String that corresponds to the desired unique service name for Azure Database Migration Service.
* *Posizione*. Specifica il percorso del servizio. Specificare una posizione del data center di Azure, ad esempio Stati Uniti occidentali o Sud-est asiatico.
* *Sku*. Questo parametro corrisponde al nome Sku DMS. I nomi Sku attualmente supportati sono *Basic_1vCore*, *Basic_2vCores*, *GeneralPurpose_4vCores*.
* *Identificatore della subnet virtuale*. È possibile utilizzare [`New-AzVirtualNetworkSubnetConfig`](https://docs.microsoft.com//powershell/module/az.network/new-azvirtualnetworksubnetconfig) il cmdlet per creare una subnet.

Nell'esempio seguente viene creato un servizio denominato *MyDMS* nel gruppo di risorse *MyDMSResourceGroup* che si trova nell'area *Stati Uniti orientali* utilizzando una rete virtuale denominata *MyVNET* e una subnet denominata *MySubnet*.

> [!IMPORTANT]
> Il frammento di codice seguente è per una migrazione offline, che non richiede un'istanza del servizio migrazione del database di Azure basata su uno SKU Premium.The code snippet below is for an offline migration, which does not require an instance of Azure Database Migration Service based on a Premium SKU. Per una migrazione online, il valore del parametro -Sku deve includere uno SKU Premium.

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

È possibile creare un oggetto Informazioni `New-AzDmsConnInfo` di connessione al database utilizzando il cmdlet, che prevede i parametri seguenti:

* *TipoServer*. Il tipo di connessione al database richiesta, ad esempio SQL, Oracle o MySQL. Usare SQL per SQL Server e SQL Azure.
* *DataSource*. Nome o indirizzo IP di un'istanza di SQL Server O di un'istanza del database SQL di Azure.The name or IP of a SQL Server instance or Azure SQL Database instance.
* *AuthType*. Il tipo di autenticazione per la connessione, che può essere SqlAuthentication o WindowsAuthentication.
* *TrustServerCertificate*. Questo parametro imposta un valore che indica se il canale è crittografato ignorando l'essenza della catena di certificati per convalidare l'attendibilità. Il valore `$true` può `$false`essere o .

Nell'esempio seguente viene creato un oggetto Connection Info per un SQL Server di origine denominato MySourceSQLServer usando l'autenticazione SQL:The following example creates a Connection Info object for a source SQL Server called *MySourceSQLServer* using sql authentication:

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

L'esempio seguente mostra la creazione di informazioni di connessione per un server di istanza gestita del database SQL di Azure denominato 'targetmanagedinstance.database.windows.net' usando l'autenticazione sql:The next example shows creation of Connection Info for an Azure SQL Database managed instance server named 'targetmanagedinstance.database.windows.net' using sql authentication:

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "targetmanagedinstance.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Fornire i database per il progetto di migrazione

Creare un `AzDataMigrationDatabaseInfo` elenco di oggetti che specifica i database come parte del progetto del servizio Migrazione del database di Azure, che può essere fornito come parametro per la creazione del progetto. È possibile utilizzare `New-AzDataMigrationDatabaseInfo` il `AzDataMigrationDatabaseInfo`cmdlet per creare .

L'esempio seguente `AzDataMigrationDatabaseInfo` crea il progetto per il database **AdventureWorks2016** e lo aggiunge all'elenco da fornire come parametro per la creazione del progetto.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Creare un oggetto progetto

Infine, è possibile creare un progetto del servizio Migrazione di `New-AzDataMigrationProject` database di Azure denominato *MyDMSProject* che si trova negli Stati Uniti orientali usando *e* aggiungere le connessioni di origine e di destinazione create in precedenza e l'elenco dei database di cui eseguire la migrazione.

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

Successivamente, creare e avviare un'attività del servizio di migrazione del database di Azure.Next, create and start an Azure Database Migration Service task. Questa attività richiede informazioni sulle credenziali di connessione sia per l'origine che per la destinazione, nonché per l'elenco delle tabelle di database da migrare e per le informazioni già fornite con il progetto creato come prerequisito.

### <a name="create-credential-parameters-for-source-and-target"></a>Creare i parametri delle credenziali per l'origine e la destinazione

Creare le credenziali di sicurezza della connessione come oggetto [PSCredential.Create connection](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) security credentials as a PSCredential object.

Nell'esempio seguente viene illustrata la creazione di oggetti *PSCredential* per le connessioni di origine e di destinazione, fornendo password come variabili stringa *$sourcePassword* e *$targetPassword*.

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-backup-fileshare-object"></a>Creare un oggetto FileShare di backup

Creare ora un oggetto FileShare che rappresenta la condivisione di rete SMB locale `New-AzDmsFileShare` in cui il servizio Migrazione del database di Azure può eseguire i backup del database di origine usando il cmdlet.

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>Creare l'oggetto di database selezionato

Il passaggio successivo consiste nel selezionare `New-AzDmsSelectedDB` i database di origine e di destinazione utilizzando il cmdlet.

L'esempio seguente è per la migrazione di un singolo database da SQL Server a un'istanza gestita del database SQL di Azure:The following example is for migrating a single database from SQL Server to an Azure SQL Database managed instance:

```powershell
$selectedDbs = @()
$selectedDbs += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

Se un'intera istanza di SQL Server richiede un passaggio in un'istanza gestita del database SQL di Azure, viene fornito un ciclo per accettare tutti i database dall'origine. Nell'esempio seguente, ad $Server, $SourceUserName e $SourcePassword, specificare i dettagli di SQL Server di origine.

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

È necessario soddisfare alcuni requisiti aggiuntivi, ma variano a seconda che si stia eseguendo una migrazione offline o online.

#### <a name="offline-migrations"></a>Migrazioni offline

Solo per le migrazioni offline, eseguire le attività di configurazione aggiuntive seguenti.

* **Selezionare account di accesso**. Creare un elenco di account di accesso di cui eseguire la migrazione, come illustrato nell'esempio seguente:Create a list of logins to be migrated as shown in the following example:

    ```powershell
    $selectedLogins = @("user1", "user2")
    ```

    > [!IMPORTANT]
    > Attualmente, il servizio di migrazione del database di Azure supporta solo la migrazione degli account di accesso SQL.

* **Selezionare i processi agente**. Creare un elenco di processi agente di cui eseguire la migrazione come illustrato nell'esempio seguente:Create list of agent jobs to be migrated as shown in the following example:

    ```powershell
    $selectedAgentJobs = @("agentJob1", "agentJob2")
    ```

    > [!IMPORTANT]
    > Attualmente, il servizio di migrazione del database di Azure supporta solo i processi con passaggi di processo del sottosistema T-SQL.

#### <a name="online-migrations"></a>Migrazioni online

Solo per le migrazioni online, eseguire le attività di configurazione aggiuntive seguenti.

* **Configurare l'app Azure Active Directory**

    ```powershell
    $pwd = "Azure App Key"
    $appId = "Azure App Id"
    $AppPasswd = ConvertTo-SecureString $pwd -AsPlainText -Force
    $app = New-AzDmsAdApp -ApplicationId $appId -AppKey $AppPasswd
    ```

* **Configurare la risorsa di archiviazioneConfigure Storage Resource**

    ```powershell
    $storageResourceId = "Storage Resource Id"
    ```

### <a name="create-and-start-the-migration-task"></a>Creare e avviare l'attività di migrazione

Usare il cmdlet `New-AzDataMigrationTask` per creare e avviare un'attività di migrazione.

#### <a name="specify-parameters"></a>Specificare i parametri

##### <a name="common-parameters"></a>Parametri comuni

Indipendentemente dal fatto che si stia eseguendo `New-AzDataMigrationTask` una migrazione offline o online, il cmdlet prevede i seguenti parametri:

* *TaskType*. Tipo di attività di migrazione da creare per la migrazione da SQL Server a Istanza gestita di database SQL di Azure. Il tipo previsto è *MigrateSqlServerSqlDbMi*. 
* *Nome gruppo di risorse*. Nome del gruppo di risorse di Azure in cui creare l'attività.
* *NomeServizio*. Istanza del Servizio Migrazione del database di Azure in cui creare l'attività.
* *NomeProgetto*. Nome del progetto di migrazione del Servizio Migrazione del database di Azure in cui creare l'attività. 
* *NomeAttività*. Nome dell'attività da creare. 
* *SourceConnection*. Oggetto AzDmsConnInfo che rappresenta la connessione SQL Server di origine.
* *TargetConnection*. Oggetto AzDmsConnInfo che rappresenta la connessione dell'istanza gestita del database SQL di Azure di destinazione.
* *SourceCred*. Oggetto [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) per la connessione al server di origine.
* *TargetCred*. Oggetto [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) per la connessione al server di destinazione.
* *SelectedDatabase*. Oggetto AzDataMigrationSelectedDB che rappresenta il mapping del database di origine e di destinazione.
* *BackupFileShare*. L'oggetto di condivisione di rete che rappresenta la condivisione di rete locale dove il Servizio Migrazione del database di Azure può salvare i backup del database di origine.
* *BackupBlobSasUri*. L'URI di firma di accesso condiviso che consente al Servizio Migrazione del database di Azure di accedere al contenitore dell'account di archiviazione in cui il servizio caricherà i file di backup. Informazioni su come ottenere l'URI di firma di accesso condiviso per un contenitore BLOB.
* *SelectedLogins*. L'elenco di account di accesso selezionato per eseguire la migrazione.
* *SelectedAgentJobs*. L'elenco di processi dell'agente selezionati per eseguire la migrazione.

##### <a name="additional-parameters"></a>Parametri aggiuntivi

Il `New-AzDataMigrationTask` cmdlet prevede inoltre parametri univoci per il tipo di migrazione, offline o online, che si stanno eseguendo.

* **Migrazioni offline**. Per le migrazioni `New-AzDataMigrationTask` offline, il cmdlet prevede anche i parametri seguenti:

  * *SelectedLogins*. L'elenco di account di accesso selezionato per eseguire la migrazione.
  * *SelectedAgentJobs*. L'elenco di processi dell'agente selezionati per eseguire la migrazione.

* **Migrazioni online**. Per le migrazioni `New-AzDataMigrationTask` online, il cmdlet prevede anche i parametri seguenti.

* *AzureActiveDirectoryApp*. Applicazione Active Directory.
* *StorageResourceID*. ID risorsa dell'account di archiviazione.

#### <a name="create-and-start-an-offline-migration-task"></a>Creare e avviare un'attività di migrazione offlineCreate and start an offline migration task

L'esempio seguente crea e avvia un'attività di migrazione offline denominata **myDMSTask**:

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

#### <a name="create-and-start-an-online-migration-task"></a>Creare e avviare un'attività di migrazione onlineCreate and start an online migration task

L'esempio seguente crea e avvia un'attività di migrazione online denominata **myDMSTask**:

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

    Per combinare i dettagli di migrazione, ad esempio le proprietà, lo stato e le informazioni del database associate alla migrazione, utilizzare il frammento di codice seguente:To combine migration details such as properties, state, and database information associated with the migration, use the following code snippet:

    ```powershell
    $CheckTask= Get-AzDataMigrationTask     -ResourceGroupName myResourceGroup `
                                            -ServiceName $service.Name `
                                        -ProjectName $project.Name `
                                            -Name myDMSTask `
                                            -ResultType DatabaseLevelOutput `
                        -Expand 
    Write-Host ‘$CheckTask.ProjectTask.Properties.Output’
    ```

2. Utilizzare `$CheckTask` la variabile per ottenere lo stato corrente dell'attività di migrazione.

    Per utilizzare `$CheckTask` la variabile per ottenere lo stato corrente dell'attività di migrazione, è possibile monitorare l'attività di migrazione in esecuzione eseguendo una query sulla proprietà state dell'attività, come illustrato nell'esempio seguente:

    ```powershell
    if (($CheckTask.ProjectTask.Properties.State -eq "Running") -or ($CheckTask.ProjectTask.Properties.State -eq "Queued"))
    {
      Write-Host "migration task running"
    }
    else if($CheckTask.ProjectTask.Properties.State -eq "Succeeded")
    { 
      Write-Host "Migration task is completed Successfully"
    }
    else if($CheckTask.ProjectTask.Properties.State -eq "Failed" -or $CheckTask.ProjectTask.Properties.State -eq "FailedInputValidation" -or $CheckTask.ProjectTask.Properties.State -eq "Faulted")
    { 
      Write-Host "Migration Task Failed"
    }
    ```

## <a name="performing-the-cutover-online-migrations-only"></a>Esecuzione del cutover (solo migrazioni online)

Con una migrazione online, viene eseguito un backup completo e il ripristino dei database, quindi il lavoro procede con il ripristino dei registri delle transazioni archiviati in BackupFileShare.

Quando il database in un'istanza gestita del database SQL di Azure viene aggiornato con i dati più recenti ed è sincronizzato con il database di origine, è possibile eseguire un cutover.

Nell'esempio seguente verrà completata la migrazione completa. Gli utenti richiamano questo comando a propria discrezione.

```powershell
$command = Invoke-AzDmsCommand -CommandType CompleteSqlMiSync `
                               -ResourceGroupName myResourceGroup `
                               -ServiceName $service.Name `
                               -ProjectName $project.Name `
                               -TaskName myDMSTask `
                               -DatabaseName "Source DB Name"
```

## <a name="deleting-the-instance-of-azure-database-migration-service"></a>Eliminazione dell'istanza del servizio di migrazione del database di AzureDeleting the instance of Azure Database Migration Service

Al termine della migrazione, è possibile eliminare l'istanza del servizio Migrazione del database di Azure:After the migration is complete, you can delete the Azure Database Migration Service instance:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="additional-resources"></a>Risorse aggiuntive

Per informazioni sugli scenari di migrazione aggiuntivi (coppie di origine/destinazione), vedere la Guida alla [migrazione di database](https://datamigration.microsoft.com/)Microsoft .

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul servizio di migrazione del database di Azure, vedere l'articolo [Che cos'è il servizio Migrazione del database](https://docs.microsoft.com/azure/dms/dms-overview)di Azure? .
