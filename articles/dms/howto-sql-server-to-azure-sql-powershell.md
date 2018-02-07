---
title: Usare il modulo Servizio Migrazione del database di Azure in Microsoft Azure PowerShell per migrare un database SQL Server locale in un database SQL di Azure | Microsoft Docs
description: Informazioni su come migrare da SQL Server locale in Azure SQL con Azure PowerShell.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: 
ms.reviewer: 
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 01/24/2018
ms.openlocfilehash: 8569bf65d04f677a45935284dc61d68879014c10
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2018
---
# <a name="migrate-sql-server-on-premises-to-azure-sql-db-using-azure-powershell"></a>Migrare SQL Server locale al database SQL di Azure con Azure PowerShell
In questo articolo si esegue la migrazione del database **Adventureworks2012** ripristinato a un'istanza locale di SQL Server 2016 o versione successiva verso un database SQL di Azure tramite Microsoft Azure PowerShell. È possibile migrare i database da un'istanza di SQL Server locale al database SQL di Azure tramite il modulo `AzureRM.DataMigration` in Microsoft Azure PowerShell.

In questo articolo viene spiegato come:
> [!div class="checklist"]
> * Creare un gruppo di risorse.
> * Creare un'istanza del Servizio Migrazione del database di Azure.
> * Creare un progetto di migrazione in un'istanza del Servizio Migrazione del database di Azure.
> * Eseguire la migrazione.

## <a name="prerequisites"></a>prerequisiti
Per completare questi passaggi è necessario disporre di:

- [SQL Server 2016 o versione successiva](https://www.microsoft.com/sql-server/sql-server-downloads) (qualsiasi edizione)
- Con l'installazione di SQL Server Express, il protocollo TCP/IP è disabilitato per impostazione predefinita. Abilitarlo seguendo le [istruzioni di questo articolo](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Per configurare [Windows Firewall per l'accesso al motore di database](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Un'istanza del database SQL di Azure. È possibile creare un'istanza del database SQL di Azure seguendo le istruzioni riportate nell'articolo [Creare un database SQL di Azure nel portale di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
- [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) versione 3.3 o successiva.
- Servizio Migrazione del database di Azure richiede una rete virtuale creata usando il modello di distribuzione Azure Resource Manager, che offre la connettività da sito a sito ai server di origine locali usando [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) o [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Valutazione completa della migrazione del database e dello schema locale mediante Data Migration Assistant, come descritto nell'articolo [Performing a SQL Server migration assessment](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) (Eseguire la valutazione di una migrazione di SQL Server)
- Scaricare e installare il modulo AzureRM.DataMigration da PowerShell Gallery usando il [cmdlet di PowerShell Install-Module](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1)
- Le credenziali usate per connettersi all'istanza SQL Server di origine devono avere l'autorizzazione [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql).
- Le credenziali usate per connettersi all'istanza del database SQL di Azure di destinazione devono avere l'autorizzazione CONTROL DATABASE nei database SQL di Azure di destinazione.
- Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Accedere alla sottoscrizione di Microsoft Azure
Seguire le istruzioni riportate nell'articolo [Accedere ad Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps?view=azurermps-4.4.1) per accedere alla sottoscrizione di Azure tramite PowerShell.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Creare un gruppo di risorse per poter creare una macchina virtuale.

Creare un gruppo di risorse usando il comando [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-4.4.1). 

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nell'area *EastUS*.

```powershell
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```
## <a name="create-an-azure-database-migration-service-instance"></a>Creare un'istanza del Servizio Migrazione del database di Azure 
È possibile creare una nuova istanza del Servizio Migrazione del database di Azure tramite il cmdlet `New-AzureRmDataMigrationService`. Questo cmdlet si aspetta i parametri obbligatori seguenti:
- *Nome del gruppo di risorse di Azure*. È possibile usare il comando [New AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-4.4.1) per creare il gruppo di risorse di Azure come mostrato in precedenza e fornire il suo nome come parametro.
- *Nome del servizio*. Stringa che corrisponde al nome di servizio univoco desiderato per il Servizio Migrazione del database di Azure 
- *Località*. Specifica il percorso del servizio. Specificare un percorso di data center di Azure, ad esempio Stati Uniti occidentali o Asia sud-orientale
- *Sku*. Questo parametro corrisponde al nome Sku DMS. Attualmente sono supportati i nomi Sku *Basic_1vCore*, *Basic_2vCores*, *GeneralPurpose_4vCores*
- *Identificatore della subnet virtuale*. Per creare una subnet è possibile usare il cmdlet [New-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig?view=azurermps-4.4.1). 

L'esempio seguente crea un servizio denominato *MyDMS* nel gruppo di risorse *MyDMSResourceGroup*, che si trova nell'area *Stati Uniti orientali* usando una rete virtuale denominata *MyVNET* e una subnet denominata *MySubnet*.

```powershell
 $vNet = Get-AzureRmVirtualNetwork -ResourceGroupName MyDMSResourceGroup -Name MyVNET

$vSubNet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vNet -Name MySubnet

$service = New-AzureRmDms -ResourceGroupName myResourceGroup `
  -ServiceName MyDMS `
  -Location EastUS `
  -Sku Basic_2vCores `  
  -VirtualSubnetId $vSubNet.Id`
```

## <a name="create-a-migration-project"></a>Creare un progetto di migrazione
Dopo aver creato un'istanza del Servizio Migrazione del database di Azure, creare un progetto di migrazione. Un progetto Servizio Migrazione del database di Azure richiede informazioni di connessione per entrambe le istanze di origine e di destinazione, nonché un elenco dei database che si desidera migrare come parte del progetto.

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Creare un oggetto informazioni di connessione del database per le connessioni di origine e di destinazione
È possibile creare un oggetto informazioni di connessione del database usando il cmdlet `New-AzureRmDmsConnInfo`. Questo cmdlet si aspetta i parametri seguenti:
- *ServerType*. Il tipo di connessione al database richiesta, ad esempio SQL, Oracle o MySQL. Usare SQL per SQL Server e SQL Azure.
- *DataSource*. Il nome o indirizzo IP di un'istanza SQL o un server SQL Azure.
- *AuthType*. Il tipo di autenticazione per la connessione, che può essere SqlAuthentication o WindowsAuthentication.
- Il parametro *TrustServerCertificate* imposta un valore che indica se il canale è crittografato, bypassando l'analisi della catena di certificati per convalidare l'attendibilità. Il valore può essere "true" o "false".

Nell'esempio seguente viene creato l'oggetto Informazioni di connessione per un'istanza SQL Server di origine denominata MySQLSourceServer mediante l'autenticazione SQL 

```powershell
$sourceConnInfo = New-AzureRmDmsConnInfo -ServerType SQL `
  -DataSource MySQLSourceServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

L'esempio successivo illustrata la creazione dell'oggetto informazioni di connessione per il server di database SQL di Azure denominato MySQLAzureTarget mediante l'autenticazione SQL

```powershell
$targetConnInfo = New-AzureRmDmsConnInfo -ServerType SQL `
  -DataSource "mysqlazuretarget.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Fornire i database per il progetto di migrazione
Creare un elenco di oggetti `AzureRmDataMigrationDatabaseInfo` che specifica i database come parte del progetto di migrazione di database di Azure che possono essere forniti come parametro per la creazione del progetto. Il cmdlet `New-AzureRmDataMigrationDatabaseInfo` può essere usato per creare AzureRmDataMigrationDatabaseInfo. 

L'esempio seguente crea il progetto `AzureRmDataMigrationDatabaseInfo` per il database AdventureWorks2016 e lo aggiunge all'elenco come parametro per la creazione del progetto.

```powershell
$dbInfo1 = New-AzureRmDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks2016
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Creare un oggetto progetto
Infine è possibile creare il progetto di migrazione del database di Azure denominato *MyDMSProject* nell'area *Stati Uniti orientali* usando `New-AzureRmDataMigrationProject` e aggiungendo le connessioni di origine e di destinazione create in precedenza e l'elenco dei database da migrare.

```powershell
$project = New-AzureRmDataMigrationProject -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName MyDMSProject `
  -Location EastUS `
  -SourceType SQL `
  -TargetType SQLDB `
  -SourceConnection $sourceConnInfo `
  -TargetConnection $targetConnInfo `
  -DatabaseInfos $dbList
```

## <a name="create-and-start-a-migration-task"></a>Creare e avviare un'attività di migrazione
Infine creare e avviare l'attività di migrazione del database di Azure. L'attività di migrazione del database di Azure richiede informazioni sulle credenziali di connessione sia per l'origine che per la destinazione e l'elenco delle tabelle di database da migrare, oltre alle informazioni già fornite con il progetto creato come prerequisito. 

### <a name="create-credential-parameters-for-source-and-target"></a>Creare i parametri delle credenziali per l'origine e la destinazione
Le credenziali di sicurezza della connessione possono essere create come oggetto [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0). 

L'esempio seguente illustra la creazione di oggetti *PSCredential* per le connessioni sia di origine che di destinazione fornendo le password come variabili stringa *$sourcePassword* e *$ targetPassword*. 

```powershell
secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-table-map-and-select-source-and-target-parameters-for-migration"></a>Creare una mappa delle tabelle e selezionare i parametri di origine e di destinazione per la migrazione
Un altro parametro necessario per la migrazione è il mapping delle tabelle da migrare dall'origine alla destinazione. Creare il dizionario delle tabelle che fornisce il mapping tra le tabelle di origine e di destinazione per la migrazione. L'esempio seguente illustra il mapping tra le tabelle di origine e di destinazione per lo schema delle risorse umane per il database AdventureWorks 2016.

```powershell
$tableMap = New-Object 'system.collections.generic.dictionary[string,string]'
$tableMap.Add("HumanResources.Department", "HumanResources.Department")
$tableMap.Add("HumanResources.Employee","HumanResources.Employee")
$tableMap.Add("HumanResources.EmployeeDepartmentHistory","HumanResources.EmployeeDepartmentHistory")
$tableMap.Add("HumanResources.EmployeePayHistory","HumanResources.EmployeePayHistory")
$tableMap.Add("HumanResources.JobCandidate","HumanResources.JobCandidate")
$tableMap.Add("HumanResources.Shift","HumanResources.Shift")
```

Il passaggio successivo consiste nel selezionare i database di origine e di destinazione e specificare il mapping delle tabelle per la migrazione come parametro usando il cmdlet `New-AzureRmDmsSqlServerSqlDbSelectedDB`, come mostrato nell'esempio seguente:

```powershell
$selectedDbs = New-AzureRmDmsSqlServerSqlDbSelectedDB -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -TableMap $tableMap
```

### <a name="create-and-start-a-migration-task"></a>Creare e avviare un'attività di migrazione

Usare il cmdlet `New-AzureRmDataMigrationTask` per creare e avviare un'attività di migrazione. Questo cmdlet si aspetta i parametri seguenti:
- *TaskType*. Tipo di attività di migrazione da creare per il tipo di migrazione da SQL Server a SQL Azure: è previsto *MigrateSqlServerSqlDb*. 
- *Nome del gruppo di risorse*. Nome del gruppo di risorse di Azure in cui creare l'attività.
- *ServiceName*. Istanza del Servizio Migrazione del database di Azure in cui creare l'attività.
- *ProjectName*. Nome del progetto di migrazione del database di Azure in cui creare l'attività. 
- *TaskName*. Nome dell'attività da creare. 
- *Connessione di origine*. Oggetto AzureRmDmsConnInfo che rappresenta la connessione di origine.
- *Connessione di destinazione*. Oggetto AzureRmDmsConnInfo che rappresenta la connessione di destinazione.
- *SourceCred*. Oggetto [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) per la connessione al server di origine.
- *TargetCred*. Oggetto [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) per la connessione al server di destinazione.
- *SelectedDatabase*. Oggetto AzureRmDmsSqlServerSqlDbSelectedDB che rappresenta il mapping del database di origine e di destinazione.

L'esempio seguente crea e avvia un'attività di migrazione denominata myDMSTask:

```powershell
$migTask = New-AzureRmDataMigrationTask -TaskType MigrateSqlServerSqlDb `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs`
```

## <a name="monitor-the-migration"></a>Monitorare la migrazione
È possibile monitorare l'attività di migrazione in esecuzione eseguendo query sulla proprietà di stato dell'attività, come illustrato nell'esempio seguente:

```powershell
if (($mytask.ProjectTask.Properties.State -eq "Running") -or ($mytask.ProjectTask.Properties.State -eq "Queued"))
{
  write-host "migration task running"
}
```

## <a name="next-steps"></a>Passaggi successivi
- Rivedere le linee guida sulla migrazione nella [Guida alla migrazione di database](https://datamigration.microsoft.com/) di Microsoft.