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
ms.date: 03/12/2019
ms.openlocfilehash: ed0d65a0f00bd5ebc3227a249beec6bafd791347
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60532559"
---
# <a name="migrate-sql-server-on-premises-to-azure-sql-database-managed-instance-using-azure-powershell"></a>Eseguire la migrazione di SQL Server a Istanza gestita di database SQL di Azure con Azure PowerShell
In questo articolo si esegue la migrazione del database **Adventureworks2012**, ripristinato in un'istanza locale di SQL Server 2005 o versione successiva, verso un'istanza gestita di database SQL di Azure tramite Microsoft Azure PowerShell. Per eseguire la migrazione di database da un'istanza di SQL Server locale a un'istanza gestita di database SQL di Azure è possibile usare il modulo `Az.DataMigration` di Microsoft Azure PowerShell.

In questo articolo viene spiegato come:
> [!div class="checklist"]
> * Creare un gruppo di risorse.
> * Creare un'istanza del Servizio Migrazione del database di Azure.
> * Creare un progetto di migrazione in un'istanza del Servizio Migrazione del database di Azure.
> * Eseguire la migrazione.

## <a name="prerequisites"></a>Prerequisiti
Per completare questi passaggi è necessario disporre di:

- [SQL Server 2016 o versione successiva](https://www.microsoft.com/sql-server/sql-server-downloads) (qualsiasi edizione)
- Per abilitare il protocollo TCP/IP che è disabilitato per impostazione predefinita con l'installazione di SQL Server Express. Abilitare il protocollo TCP/IP seguendo l'articolo [Abilitare o disabilitare un protocollo di rete server](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Per configurare [Windows Firewall per l'accesso al motore di database](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Un'istanza del database SQL di Azure. È possibile creare un'istanza del database SQL di Azure seguendo le istruzioni riportate nell'articolo [Creare un database SQL di Azure nel portale di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
- [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) versione 3.3 o successiva.
- Per creare una rete virtuale tramite il modello di distribuzione Azure Resource Manager, fornito dal Servizio Migrazione del database di Azure con la connettività da sito a sito ai server di origine locali, usando [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) o [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Aver completato una valutazione della migrazione di database e schema in locale usando Data Migration Assistant, come descritto nell'articolo [eseguire una valutazione della migrazione di SQL Server](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)
- Per scaricare e installare il modulo Az.DataMigration da PowerShell Gallery usando [cmdlet di PowerShell Install-Module](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1)
- Per assicurarsi che le credenziali usate per connettersi all'istanza di origine di SQL Server abbiano l'autorizzazione [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql).
- Per assicurarsi che le credenziali usate per connettersi all'istanza del database SQL di Azure di destinazione abbiano l'autorizzazione CONTROL DATABASE nei database SQL di Azure di destinazione.
- Una sottoscrizione di Azure. Se non se ne dispone, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Accedere alla sottoscrizione di Microsoft Azure
Seguire le istruzioni riportate nell'articolo [Accedere ad Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps) per accedere alla sottoscrizione di Azure tramite PowerShell.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Creare un gruppo di risorse per poter creare una macchina virtuale.

Creare un gruppo di risorse usando il [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) comando. 

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nell'area *EastUS*.

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```
## <a name="create-an-instance-of-the-azure-database-migration-service"></a>Creare un'istanza del Servizio Migrazione del database di Azure 
È possibile creare una nuova istanza del Servizio Migrazione del database di Azure tramite il cmdlet `New-AzDataMigrationService`. Questo cmdlet si aspetta i parametri obbligatori seguenti:
- *Nome del gruppo di risorse di Azure*. È possibile usare [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) comando per creare il gruppo di risorse di Azure come indicato in precedenza e fornire il suo nome come parametro.
- *Nome del servizio*. Stringa che corrisponde al nome di servizio univoco desiderato per il Servizio Migrazione del database di Azure 
- *Località*. Specifica il percorso del servizio. Specificare un percorso di data center di Azure, ad esempio Stati Uniti occidentali o Asia sud-orientale
- *Sku*. Questo parametro corrisponde al nome Sku DMS. Attualmente sono supportati i nomi Sku *Basic_1vCore*, *Basic_2vCores*, *GeneralPurpose_4vCores*
- *Identificatore della subnet virtuale*. È possibile usare cmdlet [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com//powershell/module/az.network/new-azvirtualnetworksubnetconfig) per creare una subnet. 

L'esempio seguente crea un servizio denominato *MyDMS* nel gruppo di risorse *MyDMSResourceGroup*, che si trova nell'area *Stati Uniti orientali* usando una rete virtuale denominata *MyVNET* e una subnet denominata *MySubnet*.

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
È possibile creare un oggetto informazioni di connessione del database usando il cmdlet `New-AzDmsConnInfo`. Questo cmdlet si aspetta i parametri seguenti:
- *ServerType*. Il tipo di connessione al database richiesta, ad esempio SQL, Oracle o MySQL. Usare SQL per SQL Server e SQL Azure.
- *DataSource*. Il nome o indirizzo IP di un'istanza Microsoft SQL Server o un database SQL di Azure.
- *AuthType*. Il tipo di autenticazione per la connessione, che può essere SqlAuthentication o WindowsAuthentication.
- Il parametro *TrustServerCertificate* imposta un valore che indica se il canale è crittografato, bypassando l'analisi della catena di certificati per convalidare l'attendibilità. Il valore può essere "true" o "false".

Nell'esempio seguente viene creato l'oggetto Informazioni di connessione per un'istanza SQL Server di origine denominata MySourceSQLServe mediante l'autenticazione SQL: 

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

L'esempio seguente mostra la creazione delle informazioni di connessione per un server di Istanza gestita di database SQL di Azure denominato "targetmanagedinstance.database.windows.net" con l'autenticazione sql:

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "targetmanagedinstance.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Fornire i database per il progetto di migrazione
Creare un elenco di oggetti `AzDataMigrationDatabaseInfo` che specifica i database come parte del progetto di migrazione di database di Azure che possono essere forniti come parametro per la creazione del progetto. Il Cmdlet `New-AzDataMigrationDatabaseInfo` può essere utilizzato per creare AzDataMigrationDatabaseInfo. 

L'esempio seguente crea il progetto `AzDataMigrationDatabaseInfo` per il **database AdventureWorks2016** e lo aggiunge all'elenco come parametro per la creazione del progetto.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Creare un oggetto progetto
Infine è possibile creare il progetto di migrazione del database di Azure denominato *MyDMSProject* nell'area *Stati Uniti orientali* usando `New-AzDataMigrationProject` e aggiungendo le connessioni di origine e di destinazione create in precedenza e l'elenco dei database da migrare.

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
Infine creare e avviare l'attività di migrazione del database di Azure. L'attività di migrazione del database di Azure richiede informazioni sulle credenziali di connessione sia per l'origine che per la destinazione e l'elenco delle tabelle di database da migrare, oltre alle informazioni già fornite con il progetto creato come prerequisito. 

### <a name="create-credential-parameters-for-source-and-target"></a>Creare i parametri delle credenziali per l'origine e la destinazione
Le credenziali di sicurezza della connessione possono essere create come oggetto [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0). 

L'esempio seguente illustra la creazione di oggetti *PSCredential* per le connessioni sia di origine che di destinazione fornendo le password come variabili stringa *$sourcePassword* e *$ targetPassword*. 

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-backup-fileshare-object"></a>Creare l'oggetto di condivisione file di backup
Ora creare una condivisione file oggetto che rappresenta la condivisione di rete SMB locale che il servizio migrazione del Database può richiedere l'origine i backup dei database per l'uso del cmdlet New-AzDmsFileShare.

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>Creare l'oggetto di database selezionato
Il passaggio successivo consiste nel selezionare i database di origine e di destinazione usando il cmdlet New-AzDmsSelectedDB, come illustrato nell'esempio seguente:

```powershell
$selectedDbs = @()
$selectedDbs += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

### <a name="sas-uri-for-azure-storage-container"></a>URI di firma di accesso condiviso per il contenitore di Archiviazione di Azure
Creare variabili che contengono l'URI di firma di accesso condiviso che consente al Servizio Migrazione del database di Azure di accedere al contenitore dell'account di archiviazione in cui il servizio caricherà i file di backup per la migrazione.

```powershell
$blobSasUri="https://mystorage.blob.core.windows.net/test?st=2018-07-13T18%3A10%3A33Z&se=2019-07-14T18%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=qKlSA512EVtest3xYjvUg139tYSDrasbftY%3D"
```

### <a name="select-logins"></a>Seleziona account di accesso
Creare l'elenco degli account di accesso da sottoporre a migrazione come illustrato nell'esempio seguente:  Si noti che il Servizio Migrazione del database di Azure supporta attualmente solo la migrazione di account di accesso SQL. 

```powershell
$selectedLogins = @("user1", "user2")
```

### <a name="select-agent-jobs"></a>Seleziona un processo agente
Creare l'elenco di processi degli agenti da sottoporre a migrazione come illustrato nell'esempio seguente:

>[!NOTE]
>Servizio Migrazione del database di Azure supporta attualmente solo i processi con passaggi di processo del sottosistema T-SQL.

```powershell
$selectedAgentJobs = @("agentJob1", "agentJob2")
```

### <a name="create-and-start-a-migration-task"></a>Creare e avviare un'attività di migrazione

Usare il cmdlet `New-AzDataMigrationTask` per creare e avviare un'attività di migrazione. Questo cmdlet si aspetta i parametri seguenti:
- *TaskType*. Tipo di attività di migrazione da creare per la migrazione da SQL Server a Istanza gestita di database SQL di Azure. Il tipo previsto è *MigrateSqlServerSqlDbMi*. 
- *Nome del gruppo di risorse*. Nome del gruppo di risorse di Azure in cui creare l'attività.
- *ServiceName*. Istanza del Servizio Migrazione del database di Azure in cui creare l'attività.
- *ProjectName*. Nome del progetto di migrazione del Servizio Migrazione del database di Azure in cui creare l'attività. 
- *TaskName*. Nome dell'attività da creare. 
- *SourceConnection*. Oggetto AzDmsConnInfo che rappresenta la connessione di SQL Server di origine.
- *TargetConnection*. Oggetto AzDmsConnInfo che rappresenta la connessione di istanza gestita di Azure SQL Database di destinazione.
- *SourceCred*. Oggetto [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) per la connessione al server di origine.
- *TargetCred*. Oggetto [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) per la connessione al server di destinazione.
- *SelectedDatabase*. Oggetto AzDataMigrationSelectedDB che rappresenta il mapping del database di origine e di destinazione.
- *BackupFileShare*. L'oggetto di condivisione di rete che rappresenta la condivisione di rete locale dove il Servizio Migrazione del database di Azure può salvare i backup del database di origine.
- *BackupBlobSasUri*. L'URI di firma di accesso condiviso che consente al Servizio Migrazione del database di Azure di accedere al contenitore dell'account di archiviazione in cui il servizio caricherà i file di backup. Informazioni su come ottenere l'URI di firma di accesso condiviso per un contenitore BLOB.
- *SelectedLogins*. L'elenco di account di accesso selezionato per eseguire la migrazione.
- *SelectedAgentJobs*. L'elenco di processi dell'agente selezionati per eseguire la migrazione.

L'esempio seguente crea e avvia un'attività di migrazione denominata myDMSTask:

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

## <a name="monitor-the-migration"></a>Monitorare la migrazione
È possibile monitorare l'attività di migrazione in esecuzione eseguendo query sulla proprietà di stato dell'attività, come illustrato nell'esempio seguente:

```powershell
if (($mytask.ProjectTask.Properties.State -eq "Running") -or ($mytask.ProjectTask.Properties.State -eq "Queued"))
{
  write-host "migration task running"
}
```

## <a name="deleting-the-dms-instance"></a>Eliminazione dell'istanza del Servizio Migrazione del database di Azure
Al termine della migrazione, è possibile eliminare l'istanza del Servizio Migrazione del database di Azure:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="next-steps"></a>Passaggi successivi
- Rivedere le linee guida sulla migrazione nella [Guida alla migrazione di database](https://datamigration.microsoft.com/) di Microsoft.
