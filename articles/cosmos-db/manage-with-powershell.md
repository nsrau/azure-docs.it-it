---
title: Gestire Azure Cosmos DB risorse API Core (SQL) usando PowerShell
description: Gestire Azure Cosmos DB risorse API Core (SQL) usando PowerShell.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: 2d99a760e3bf44febcee05bf9827479616bf7bd6
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93333427"
---
# <a name="manage-azure-cosmos-db-core-sql-api-resources-using-powershell"></a>Gestire Azure Cosmos DB risorse API Core (SQL) con PowerShell
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

La guida seguente illustra come usare PowerShell per creare script e automatizzare la gestione di risorse API Azure Cosmos DB Core (SQL), tra cui l'account Cosmos, il database, il contenitore e la velocità effettiva. Per i cmdlet di PowerShell relativi ad altre API, vedere [Esempi di PowerShell per Cassandra](powershell-samples-cassandra.md), [Esempi di PowerShell per l'API MongoDB](powershell-samples-mongodb.md), [Esempi di PowerShell per Gremlin](powershell-samples-gremlin.md), [Esempi di PowerShell per Tabella](powershell-samples-table.md)

> [!NOTE]
> Negli esempi di questo articolo vengono usati i cmdlet di gestione di [Az.CosmosDB](/powershell/module/az.cosmosdb). Per informazioni sulle ultime modifiche, vedere la pagina di informazioni di riferimento dell'API [Az.CosmosDB](/powershell/module/az.cosmosdb).

Per la gestione multipiattaforma dell'account Azure Cosmos DB, è possibile usare i cmdlet `Az` e `Az.CosmosDB` con [PowerShell multipiattaforma](/powershell/scripting/install/installing-powershell), l'[interfaccia della riga di comando di Azure](manage-with-cli.md), l'[API REST][rp-rest-api] o il [portale di Azure](create-sql-api-dotnet.md#create-account).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Introduzione

Seguire le istruzioni indicate in [Come installare e configurare Azure PowerShell][powershell-install-configure] per installare e accedere all'account Azure in PowerShell.

> [!IMPORTANT]
> Non è possibile rinominare Azure Cosmos DB risorse perché questo viola il modo in cui Azure Resource Manager funziona con gli URI delle risorse.

## <a name="azure-cosmos-accounts"></a>Account Azure Cosmos

Le sezioni seguenti illustrano come gestire l'account Azure Cosmos:

* [Creare un account Azure Cosmos](#create-account)
* [Aggiornare un account Azure Cosmos](#update-account)
* [Elencare tutti gli account Azure Cosmos in una sottoscrizione](#list-accounts)
* [Ottenere un account Azure Cosmos](#get-account)
* [Eliminare un account Azure Cosmos](#delete-account)
* [Aggiornare i tag per un account Azure Cosmos](#update-tags)
* [Elencare le chiavi per un account Azure Cosmos](#list-keys)
* [Rigenerare le chiavi per un account Azure Cosmos](#regenerate-keys)
* [Elencare le stringhe di connessione per un account Azure Cosmos](#list-connection-strings)
* [Modificare la priorità di failover di un account Azure Cosmos](#modify-failover-priority)
* [Attivare un failover manuale per un account Azure Cosmos](#trigger-manual-failover)
* [Elencare i blocchi delle risorse in un account Azure Cosmos DB](#list-account-locks)

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a> Creare un account Azure Cosmos

Questo comando crea un account del database Azure Cosmos DB con [più aree][distribute-data-globally] e [failover automatico](how-to-manage-database-account.md#automatic-failover) e il [criterio di coerenza](consistency-levels.md) con decadimento ristretto.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$apiKind = "Sql"
$consistencyLevel = "BoundedStaleness"
$maxStalenessInterval = 300
$maxStalenessPrefix = 100000
$locations = @()
$locations += New-AzCosmosDBLocationObject -LocationName "East US" -FailoverPriority 0 -IsZoneRedundant 0
$locations += New-AzCosmosDBLocationObject -LocationName "West US" -FailoverPriority 1 -IsZoneRedundant 0

New-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -LocationObject $locations `
    -Name $accountName `
    -ApiKind $apiKind `
    -EnableAutomaticFailover:$true `
    -DefaultConsistencyLevel $consistencyLevel `
    -MaxStalenessIntervalInSeconds $maxStalenessInterval `
    -MaxStalenessPrefix $maxStalenessPrefix
```

* `$resourceGroupName`: gruppo di risorse di Azure in cui distribuire l'account Cosmos. Deve essere già esistente.
* `$locations` Le aree per l'account del database, l'area con `FailoverPriority 0` è l'area di scrittura.
* `$accountName` Il nome dell'account Azure Cosmos. Deve essere univoco, includere solo caratteri alfanumerici minuscoli e '-' e avere una lunghezza compresa tra 3 e 31 caratteri.
* `$apiKind`: tipo di account Cosmos da creare. Per altre informazioni, vedere [API in Cosmos DB](introduction.md#simplified-application-development).
* `$consistencyPolicy`,`$maxStalenessInterval` e`$maxStalenessPrefix`: livello di coerenza predefinito e impostazioni dell'account Azure Cosmos. Per altre informazioni, vedere [Livelli di coerenza in Azure Cosmos DB](consistency-levels.md).

Gli account Azure Cosmos possono essere configurati con firewall IP, endpoint servizio di rete virtuale ed endpoint privati. Per informazioni su come configurare il firewall IP per Azure Cosmos DB, vedere [Configurare il firewall IP](how-to-configure-firewall.md). Per informazioni su come abilitare gli endpoint servizio per Azure Cosmos DB, vedere [Configurare l'accesso da reti virtuali](how-to-configure-vnet-service-endpoint.md). Per informazioni su come abilitare gli endpoint privati per Azure Cosmos DB, vedere [Configurare l'accesso da endpoint privati](how-to-configure-private-endpoints.md).

### <a name="list-all-azure-cosmos-accounts-in-a-resource-group"></a><a id="list-accounts"></a> Elencare tutti gli account Azure Cosmos in un gruppo di risorse

Questo comando elenca tutti gli account Azure Cosmos in un gruppo di risorse.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"

Get-AzCosmosDBAccount -ResourceGroupName $resourceGroupName
```

### <a name="get-the-properties-of-an-azure-cosmos-account"></a><a id="get-account"></a> Ottenere le proprietà di un account Azure Cosmos

Questo comando consente di ottenere le proprietà di un account Azure Cosmos esistente.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccount -ResourceGroupName $resourceGroupName -Name $accountName
```

### <a name="update-an-azure-cosmos-account"></a><a id="update-account"></a> Aggiornare un account Azure Cosmos

Questo comando consente di aggiornare le proprietà di un account del database Azure Cosmos DB. Le proprietà che possono essere aggiornate includono:

* Aggiunta o rimozione di aree
* Modifica dei criteri di coerenza predefiniti
* Modifica del filtro dell'intervallo IP
* Modifica delle configurazioni di reti virtuali
* Abilitazione di scritture in più aree

> [!NOTE]
> Non è possibile aggiungere o rimuovere simultaneamente aree (`locations`) e cambiare l'ordine delle proprietà per un account Azure Cosmos. La modifica delle aree deve essere eseguita come operazione distinta rispetto a qualsiasi altra modifica apportata all'account.
> [!NOTE]
> Questo comando consente di aggiungere e rimuovere aree, ma non di modificare le priorità di failover o attivare un failover manuale. Vedere [Modificare la priorità di failover](#modify-failover-priority) e [Attivare un failover manuale](#trigger-manual-failover).

```azurepowershell-interactive
# Create account with two regions
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$apiKind = "Sql"
$consistencyLevel = "Session"
$enableAutomaticFailover = $true
$locations = @()
$locations += New-AzCosmosDBLocationObject -LocationName "East US" -FailoverPriority 0 -IsZoneRedundant 0
$locations += New-AzCosmosDBLocationObject -LocationName "West US" -FailoverPriority 1 -IsZoneRedundant 0

# Create the Cosmos DB account
New-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -LocationObject $locations `
    -Name $accountName `
    -ApiKind $apiKind `
    -EnableAutomaticFailover:$enableAutomaticFailover `
    -DefaultConsistencyLevel $consistencyLevel

# Add a region to the account
$locationObject2 = @()
$locationObject2 += New-AzCosmosDBLocationObject -LocationName "East US" -FailoverPriority 0 -IsZoneRedundant 0
$locationObject2 += New-AzCosmosDBLocationObject -LocationName "West US" -FailoverPriority 1 -IsZoneRedundant 0
$locationObject2 += New-AzCosmosDBLocationObject -LocationName "South Central US" -FailoverPriority 2 -IsZoneRedundant 0

Update-AzCosmosDBAccountRegion `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -LocationObject $locationObject2

Write-Host "Update-AzCosmosDBAccountRegion returns before the region update is complete."
Write-Host "Check account in Azure portal or using Get-AzCosmosDBAccount for region status."
Write-Host "When region was added, press any key to continue."
$HOST.UI.RawUI.ReadKey("NoEcho,IncludeKeyDown") | OUT-NULL
$HOST.UI.RawUI.Flushinputbuffer()

# Remove West US region from the account
$locationObject3 = @()
$locationObject3 += New-AzCosmosDBLocationObject -LocationName "East US" -FailoverPriority 0 -IsZoneRedundant 0
$locationObject3 += New-AzCosmosDBLocationObject -LocationName "South Central US" -FailoverPriority 1 -IsZoneRedundant 0

Update-AzCosmosDBAccountRegion `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -LocationObject $locationObject3

Write-Host "Update-AzCosmosDBAccountRegion returns before the region update is complete."
Write-Host "Check account in Azure portal or using Get-AzCosmosDBAccount for region status."
```

### <a name="enable-multiple-write-regions-for-an-azure-cosmos-account"></a><a id="multi-region-writes"></a> Abilitare più aree di scrittura per un account Azure Cosmos

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$enableAutomaticFailover = $false
$enableMultiMaster = $true

# First disable automatic failover - cannot have both automatic
# failover and multi-region writes on an account
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableAutomaticFailover:$enableAutomaticFailover

# Now enable multi-region writes
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableMultipleWriteLocations:$enableMultiMaster
```

### <a name="delete-an-azure-cosmos-account"></a><a id="delete-account"></a> Eliminare un account Azure Cosmos

Questo comando elimina un account Azure Cosmos esistente.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Remove-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -PassThru:$true
```

### <a name="update-tags-of-an-azure-cosmos-account"></a><a id="update-tags"></a> Aggiornare i tag per un account Azure Cosmos

Questo comando imposta i [tag delle risorse di Azure][azure-resource-tags] per un account Azure Cosmos. I tag possono essere impostati sia durante la creazione dell'account con `New-AzCosmosDBAccount`, sia durante l'aggiornamento dell'account usando `Update-AzCosmosDBAccount`.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$tags = @{dept = "Finance"; environment = "Production";}

Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -Tag $tags
```

### <a name="list-account-keys"></a><a id="list-keys"></a> Elencare le chiavi dell'account

Quando si crea un account Azure Cosmos, il servizio genera due chiavi di accesso primarie che possono essere usate per l'autenticazione quando si accede all'account Azure Cosmos. Vengono generate anche chiavi di sola lettura per l'autenticazione delle operazioni di sola lettura.
Generando due chiavi di accesso, Azure Cosmos DB consente di rigenerare e ruotare una chiave alla volta senza interruzioni dell'account Azure Cosmos.
Gli account Cosmos DB hanno due chiavi di lettura/scrittura (primaria e secondaria) e due chiavi di sola lettura (primaria e secondaria).

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -Type "Keys"
```

### <a name="list-connection-strings"></a><a id="list-connection-strings"></a> Elencare le stringhe di connessione

Il comando seguente recupera le stringhe di connessione per connettere le app all'account Cosmos DB.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -Type "ConnectionStrings"
```

### <a name="regenerate-account-keys"></a><a id="regenerate-keys"></a> Rigenerare le chiavi dell'account

Le chiavi di accesso a un account Azure Cosmos devono essere rigenerate periodicamente per garantire la sicurezza delle connessioni. All'account sono assegnate una chiave di accesso primaria e una secondaria. In questo modo i client possono mantenere l'accesso quando una delle chiavi viene rigenerata.
Esistono quattro tipi di chiavi per un account Azure Cosmos: Primary, Secondary, PrimaryReadonly e SecondaryReadonly

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup" # Resource Group must already exist
$accountName = "mycosmosaccount" # Must be all lower case
$keyKind = "primary" # Other key kinds: secondary, primaryReadOnly, secondaryReadOnly

New-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -KeyKind $keyKind
```

### <a name="enable-automatic-failover"></a><a id="enable-automatic-failover"></a> Abilitare il failover automatico

Il comando seguente imposta il failover automatico di un account Cosmos DB nell'area secondaria se l'area primaria diventa non disponibile.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$enableAutomaticFailover = $true
$enableMultiMaster = $false

# First disable multi-region writes - cannot have both automatic
# failover and multi-region writes on an account
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableMultipleWriteLocations:$enableMultiMaster

# Now enable automatic failover
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableAutomaticFailover:$enableAutomaticFailover
```

### <a name="modify-failover-priority"></a><a id="modify-failover-priority"></a> Modificare la priorità di failover

Per gli account configurati con failover automatico, è possibile cambiare l'ordine in cui Cosmos alzerà di livello le repliche secondarie a primarie nel caso quelle primarie diventassero indisponibili.

Per l'esempio seguente, si supponga che la priorità di failover corrente sia `West US 2 = 0`, `East US 2 = 1`, `South Central US = 2`. Il comando la modificherà impostandola su `West US 2 = 0`, `South Central US = 1`, `East US 2 = 2`.

> [!CAUTION]
> La modifica della località per `failoverPriority=0` attiverà un failover manuale per un account Azure Cosmos. Qualsiasi altra modifica della priorità non attiverà un failover.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$locations = @("West US 2", "South Central US", "East US 2") # Regions ordered by UPDATED failover priority

Update-AzCosmosDBAccountFailoverPriority `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -FailoverPolicy $locations
```

### <a name="trigger-manual-failover"></a><a id="trigger-manual-failover"></a> Attivare un failover manuale

Per gli account configurati con failover manuale, è possibile eseguire il failover e alzare di livello qualsiasi replica secondaria a primaria modificando `failoverPriority=0`. Questa operazione può essere usata per avviare un'esercitazione sul ripristino di emergenza per testarne la pianificazione.

Per l'esempio seguente, si presuppone che l'account abbia la priorità di failover corrente impostata su `West US 2 = 0` e `East US 2 = 1` e che le aree vengano invertite.

> [!CAUTION]
> Se si cambia `locationName` per `failoverPriority=0`, verrà attivato un failover manuale per un account Azure Cosmos. Qualsiasi altra modifica della priorità non attiverà un failover.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$locations = @("East US 2", "West US 2") # Regions ordered by UPDATED failover priority

Update-AzCosmosDBAccountFailoverPriority `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -FailoverPolicy $locations
```

### <a name="list-resource-locks-on-an-azure-cosmos-db-account"></a><a id="list-account-locks"></a> Elencare i blocchi delle risorse in un account Azure Cosmos DB

È possibile inserire blocchi delle risorse per le risorse di Azure Cosmos DB, inclusi database e raccolte. L'esempio seguente mostra come elencare tutti i blocchi delle risorse di Azure in un account Azure Cosmos DB.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceTypeAccount = "Microsoft.DocumentDB/databaseAccounts"
$accountName = "mycosmosaccount"

Get-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceTypeAccount `
    -ResourceName $accountName
```

## <a name="azure-cosmos-db-database"></a>Database Azure Cosmos DB

Le sezioni seguenti illustrano come gestire il database Azure Cosmos DB:

* [Creare un database Azure Cosmos DB](#create-db)
* [Creare un database Azure Cosmos DB con unità elaborate condivise](#create-db-ru)
* [Ottenere le unità elaborate di un database Azure Cosmos DB](#get-db-ru)
* [Eseguire la migrazione della velocità effettiva del database alla scalabilità automatica](#migrate-db-ru)
* [Elencare tutti i database Azure Cosmos DB in un account](#list-db)
* [Ottenere un singolo database Azure Cosmos DB](#get-db)
* [Eliminare un database Azure Cosmos DB](#delete-db)
* [Creare un blocco delle risorsa per un database Azure Cosmos DB per evitare l'eliminazione](#create-db-lock)
* [Rimuovere un blocco delle risorse per un database Azure Cosmos DB](#remove-db-lock)

### <a name="create-an-azure-cosmos-db-database"></a><a id="create-db"></a>Creare un database Azure Cosmos DB

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

New-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="create-an-azure-cosmos-db-database-with-shared-throughput"></a><a id="create-db-ru"></a>Creare un database Azure Cosmos DB con unità elaborate condivise

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$databaseRUs = 400

New-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName `
    -Throughput $databaseRUs
```

### <a name="get-the-throughput-of-an-azure-cosmos-db-database"></a><a id="get-db-ru"></a>Ottenere le unità elaborate di un database Azure Cosmos DB

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlDatabaseThroughput `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

## <a name="migrate-database-throughput-to-autoscale"></a><a id="migrate-db-ru"></a>Eseguire la migrazione della velocità effettiva del database alla scalabilità automatica

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Invoke-AzCosmosDBSqlDatabaseThroughputMigration `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName `
    -ThroughputType Autoscale
```

### <a name="get-all-azure-cosmos-db-databases-in-an-account"></a><a id="list-db"></a>Ottenere tutti i database Azure Cosmos DB in un account

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName
```

### <a name="get-a-single-azure-cosmos-db-database"></a><a id="get-db"></a>Ottenere un singolo database Azure Cosmos DB

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="delete-an-azure-cosmos-db-database"></a><a id="delete-db"></a>Eliminare un database Azure Cosmos DB

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Remove-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="create-a-resource-lock-on-an-azure-cosmos-db-database-to-prevent-delete"></a><a id="create-db-lock"></a>Creare un blocco delle risorsa per un database Azure Cosmos DB per evitare l'eliminazione

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDB/databaseAccounts/sqlDatabases"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$resourceName = "$accountName/$databaseName"
$lockName = "myResourceLock"
$lockLevel = "CanNotDelete"

New-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceType `
    -ResourceName $resourceName `
    -LockName $lockName `
    -LockLevel $lockLevel
```

### <a name="remove-a-resource-lock-on-an-azure-cosmos-db-database"></a><a id="remove-db-lock"></a>Rimuovere un blocco delle risorse per un database Azure Cosmos DB

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDB/databaseAccounts/sqlDatabases"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$resourceName = "$accountName/$databaseName"
$lockName = "myResourceLock"

Remove-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceType `
    -ResourceName $resourceName `
    -LockName $lockName
```

## <a name="azure-cosmos-db-container"></a>Contenitore Azure Cosmos DB

Le sezioni seguenti illustrano come gestire il contenitore Azure Cosmos DB:

* [Creare un contenitore Azure Cosmos DB](#create-container)
* [Creare un contenitore Azure Cosmos DB con scalabilità automatica](#create-container-autoscale)
* [Creare un contenitore Azure Cosmos DB con una chiave di partizione di grandi dimensioni](#create-container-big-pk)
* [Ottenere le unità elaborate di un contenitore Azure Cosmos DB](#get-container-ru)
* [Eseguire la migrazione della velocità effettiva del contenitore alla scalabilità automatica](#migrate-container-ru)
* [Creare un contenitore Azure Cosmos DB con indicizzazione personalizzata](#create-container-custom-index)
* [Creare un contenitore Azure Cosmos DB con indicizzazione disattivata](#create-container-no-index)
* [Creare un contenitore Azure Cosmos DB con chiave univoca e TTL](#create-container-unique-key-ttl)
* [Creare un contenitore Azure Cosmos DB con risoluzione dei conflitti](#create-container-lww)
* [Elencare tutti i contenitori Azure Cosmos DB in un database](#list-containers)
* [Ottenere un singolo contenitore Azure Cosmos DB in un database](#get-container)
* [Eliminare un contenitore Azure Cosmos DB](#delete-container)
* [Creare un blocco delle risorse per un contenitore Azure Cosmos DB per impedire l'eliminazione](#create-container-lock)
* [Rimuovere un blocco delle risorse per un contenitore Azure Cosmos DB](#remove-container-lock)

### <a name="create-an-azure-cosmos-db-container"></a><a id="create-container"></a>Creare un contenitore Azure Cosmos DB

```azurepowershell-interactive
# Create an Azure Cosmos DB container with default indexes and throughput at 400 RU
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$throughput = 400 #minimum = 400

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -Throughput $throughput
```

### <a name="create-an-azure-cosmos-db-container-with-autoscale"></a><a id="create-container-autoscale"></a>Creare un contenitore Azure Cosmos DB con scalabilità automatica

```azurepowershell-interactive
# Create an Azure Cosmos DB container with default indexes and autoscale throughput at 4000 RU
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$autoscaleMaxThroughput = 4000 #minimum = 4000

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -AutoscaleMaxThroughput $autoscaleMaxThroughput
```

### <a name="create-an-azure-cosmos-db-container-with-a-large-partition-key-size"></a><a id="create-container-big-pk"></a>Creare un contenitore Azure Cosmos DB con una chiave di partizione di grandi dimensioni

```azurepowershell-interactive
# Create an Azure Cosmos DB container with a large partition key value (version = 2)
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -PartitionKeyVersion 2
```

### <a name="get-the-throughput-of-an-azure-cosmos-db-container"></a><a id="get-container-ru"></a>Ottenere le unità elaborate di un contenitore Azure Cosmos DB

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Get-AzCosmosDBSqlContainerThroughput `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName
```

### <a name="migrate-container-throughput-to-autoscale"></a><a id="migrate-container-ru"></a>Eseguire la migrazione della velocità effettiva del contenitore alla scalabilità automatica

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Invoke-AzCosmosDBSqlContainerThroughputMigration `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -ThroughputType Autoscale
```

### <a name="create-an-azure-cosmos-db-container-with-custom-index-policy"></a><a id="create-container-custom-index"></a>Creare un contenitore Azure Cosmos DB con un criterio di indicizzazione personalizzato

```azurepowershell-interactive
# Create a container with a custom indexing policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$indexPathIncluded = "/*"
$indexPathExcluded = "/myExcludedPath/*"

$includedPathIndex = New-AzCosmosDBSqlIncludedPathIndex -DataType String -Kind Range
$includedPath = New-AzCosmosDBSqlIncludedPath -Path $indexPathIncluded -Index $includedPathIndex

$indexingPolicy = New-AzCosmosDBSqlIndexingPolicy `
    -IncludedPath $includedPath `
    -ExcludedPath $indexPathExcluded `
    -IndexingMode Consistent `
    -Automatic $true

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -IndexingPolicy $indexingPolicy
```

### <a name="create-an-azure-cosmos-db-container-with-indexing-turned-off"></a><a id="create-container-no-index"></a>Creare un contenitore Azure Cosmos DB con indicizzazione disattivata

```azurepowershell-interactive
# Create an Azure Cosmos DB container with no indexing
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"

$indexingPolicy = New-AzCosmosDBSqlIndexingPolicy `
    -IndexingMode None

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -IndexingPolicy $indexingPolicy
```

### <a name="create-an-azure-cosmos-db-container-with-unique-key-policy-and-ttl"></a><a id="create-container-unique-key-ttl"></a>Creare un contenitore Azure Cosmos DB con chiave univoca e TTL

```azurepowershell-interactive
# Create a container with a unique key policy and TTL of one day
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$uniqueKeyPath = "/myUniqueKeyPath"
$ttlInSeconds = 86400 # Set this to -1 (or don't use it at all) to never expire

$uniqueKey = New-AzCosmosDBSqlUniqueKey `
    -Path $uniqueKeyPath

$uniqueKeyPolicy = New-AzCosmosDBSqlUniqueKeyPolicy `
    -UniqueKey $uniqueKey

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -UniqueKeyPolicy $uniqueKeyPolicy `
    -TtlInSeconds $ttlInSeconds
```

### <a name="create-an-azure-cosmos-db-container-with-conflict-resolution"></a><a id="create-container-lww"></a>Creare un contenitore Azure Cosmos DB con risoluzione dei conflitti

Per scrivere tutti i conflitti in ConflictsFeed e gestirli separatamente, passare `-Type "Custom" -Path ""`.

```azurepowershell-interactive
# Create container with last-writer-wins conflict resolution policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$conflictResolutionPath = "/myResolutionPath"

$conflictResolutionPolicy = New-AzCosmosDBSqlConflictResolutionPolicy `
    -Type LastWriterWins `
    -Path $conflictResolutionPath

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -ConflictResolutionPolicy $conflictResolutionPolicy
```

Per creare un criterio di risoluzione dei conflitti per l'uso di una stored procedure, chiamare `New-AzCosmosDBSqlConflictResolutionPolicy` e passare i parametri `-Type` e `-ConflictResolutionProcedure`.

```azurepowershell-interactive
# Create container with custom conflict resolution policy using a stored procedure
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$conflictResolutionSprocName = "mysproc"

$conflictResolutionSproc = "/dbs/$databaseName/colls/$containerName/sprocs/$conflictResolutionSprocName"

$conflictResolutionPolicy = New-AzCosmosDBSqlConflictResolutionPolicy `
    -Type Custom `
    -ConflictResolutionProcedure $conflictResolutionSproc

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -ConflictResolutionPolicy $conflictResolutionPolicy
```


### <a name="list-all-azure-cosmos-db-containers-in-a-database"></a><a id="list-containers"></a>Elencare tutti i contenitori Azure Cosmos DB in un database

```azurepowershell-interactive
# List all Azure Cosmos DB containers in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName
```

### <a name="get-a-single-azure-cosmos-db-container-in-a-database"></a><a id="get-container"></a>Ottenere un singolo contenitore Azure Cosmos DB in un database

```azurepowershell-interactive
# Get a single Azure Cosmos DB container in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Get-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName
```

### <a name="delete-an-azure-cosmos-db-container"></a><a id="delete-container"></a>Eliminare un contenitore Azure Cosmos DB

```azurepowershell-interactive
# Delete an Azure Cosmos DB container
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Remove-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName
```
### <a name="create-a-resource-lock-on-an-azure-cosmos-db-container-to-prevent-delete"></a><a id="create-container-lock"></a>Creare un blocco delle risorse per un contenitore Azure Cosmos DB per impedire l'eliminazione

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$resourceName = "$accountName/$databaseName/$containerName"
$lockName = "myResourceLock"
$lockLevel = "CanNotDelete"

New-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceType `
    -ResourceName $resourceName `
    -LockName $lockName `
    -LockLevel $lockLevel
```

### <a name="remove-a-resource-lock-on-an-azure-cosmos-db-container"></a><a id="remove-container-lock"></a>Rimuovere un blocco delle risorse per un contenitore Azure Cosmos DB

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$resourceName = "$accountName/$databaseName/$containerName"
$lockName = "myResourceLock"

Remove-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceType `
    -ResourceName $resourceName `
    -LockName $lockName
```

## <a name="next-steps"></a>Passaggi successivi

* [Tutti gli esempi di PowerShell](powershell-samples.md)
* [Come gestire un account Azure Cosmos](how-to-manage-database-account.md)
* [Creare un contenitore Azure Cosmos DB](how-to-create-container.md)
* [Configurare la durata (TTL) in Azure Cosmos DB](how-to-time-to-live.md)

<!--Reference style links - using these makes the source content way more readable than using inline links-->

[powershell-install-configure]: /powershell/azure/
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: ../azure-resource-manager/management/overview.md#resource-groups
[azure-resource-tags]: ../azure-resource-manager/management/tag-resources.md
[rp-rest-api]: /rest/api/cosmos-db-resource-provider/