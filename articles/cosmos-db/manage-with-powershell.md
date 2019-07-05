---
title: Creare e gestire Azure Cosmos DB mediante PowerShell
description: Usare Azure Powershell per gestire l'account Azure Cosmos DB, database, i contenitori e velocità effettiva.
author: markjbrown
ms.service: cosmos-db
ms.topic: samples
ms.date: 07/03/2019
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: 40f041f1b41077824aa3141f6196901b51415c35
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565926"
---
# <a name="manage-azure-cosmos-db-sql-api-resources-using-powershell"></a>Gestire le risorse API SQL di Azure Cosmos DB mediante PowerShell

La Guida seguente illustra come usare PowerShell per lo script e automatizzare la gestione delle risorse di Azure Cosmos DB, tra cui account, database, contenitore e della velocità effettiva. Gestione di Azure Cosmos DB è gestita tramite il cmdlet AzResource direttamente al provider di risorse di Azure Cosmos DB. Per visualizzare tutte le proprietà che possono essere gestite tramite PowerShell per il provider di risorse di Azure Cosmos DB, vedere [dello schema provider di risorse di Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)

Per la gestione multipiattaforma di Azure Cosmos DB, è possibile usare [Azure CLI](manage-with-cli.md), il [API REST][rp-rest-api], o la [portale di Azure](create-sql-api-dotnet.md#create-account).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Introduzione

Seguire le istruzioni in [come installare e configurare Azure PowerShell][powershell-install-configure] per installare e accedere all'account di Azure PowerShell.

* Per eseguire i comandi seguenti senza richiedere la conferma dell'utente, aggiungere il flag `-Force` al comando.
* Tutti i comandi seguenti sono sincroni.

## <a name="azure-cosmos-accounts"></a>Account Azure Cosmos

Le sezioni seguenti illustrano come gestire l'account Azure Cosmos, tra cui:

* [Creare un account Azure Cosmos](#create-account)
* [Aggiornare un account Azure Cosmos](#update-account)
* [Ottenere un account Azure Cosmos](#get-account)
* [Eliminare un account Azure Cosmos](#delete-account)
* [Aggiornare i tag per un account Azure Cosmos](#update-tags)
* [Elenca le chiavi per un account Azure Cosmos](#list-keys)
* [Rigenerare le chiavi per un account Azure Cosmos](#regenerate-keys)
* [Elenco di stringhe di connessione per un account Azure Cosmos](#list-connection-strings)
* [Modificare la priorità di failover per un account Azure Cosmos](#modify-failover-priority)

### <a id="create-account"></a> Creare un account Azure Cosmos

Questo comando crea un account di database Azure Cosmos DB con [multiplo-regions][distribute-data-globally], con obsolescenza associata [criterio di coerenza](consistency-levels.md).

```azurepowershell-interactive
# Create an Azure Cosmos Account for Core (SQL) API
$resourceGroupName = "myResourceGroup"
$location = "West US 2"
$accountName = "mycosmosaccount" # must be lower case.

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 },
    @{ "locationName"="East US 2"; "failoverPriority"=1 }
)

$consistencyPolicy = @{
    "defaultConsistencyLevel"="BoundedStaleness";
    "maxIntervalInSeconds"=300;
    "maxStalenessPrefix"=100000
}

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy;
    "enableMultipleWriteLocations"="false"
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Location $location `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

* `$accountName` Il nome dell'account Azure Cosmos. Deve essere in minuscolo, accetta caratteri alfanumerici e il '-' e tipo di carattere, compresa tra 3 e 31 caratteri.
* `$location` Il percorso per la risorsa dell'account Azure Cosmos.
* `$locations` Le aree di replica per l'account del database. Deve essere presente un'area di scrittura per ogni account di database con un valore di priorità di failover pari a 0.
* `$consistencyPolicy` Il livello di coerenza predefinito dell'account Azure Cosmos. Per altre informazioni, vedere [Livelli di coerenza in Azure Cosmos DB](consistency-levels.md).
* `$CosmosDBProperties` I valori delle proprietà passato al provider di Cosmos DB Azure Resource Manager per effettuare il provisioning dell'account.

Endpoint del servizio account possono essere configurati con Firewall IP come rete virtuale di Azure Cosmos. Per informazioni su come configurare il Firewall IP per Azure Cosmos DB, vedere [Configura Firewall IP](how-to-configure-firewall.md).  Per altre informazioni su come abilitare gli endpoint di servizio per Azure Cosmos DB, vedere [configurare l'accesso da reti virtuali](how-to-configure-vnet-service-endpoint.md) .

### <a id="get-account"></a> Ottiene le proprietà di un account Azure Cosmos

Questo comando consente di ottenere le proprietà di un account Azure Cosmos esistente.

```azurepowershell-interactive
# Get the properties of an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName | Select-Object Properties
```

### <a id="update-account"></a> Aggiornare un account Azure Cosmos

Questo comando consente di aggiornare le proprietà di un account del database Azure Cosmos DB. Le proprietà che possono essere aggiornate includono quanto segue:

* Aggiungere o rimuovere aree
* Modifica dei criteri di coerenza predefinito
* Modifica dei criteri di Failover
* Modificare il filtro di intervallo IP
* Modifica delle configurazioni di rete virtuale
* Abilitazione di multi-master

> [!NOTE]
> Il comando consente di aggiungere e rimuovere aree, ma non di modificare le priorità di failover. Per modificare la priorità di failover, vedere [modificare la priorità di failover per un account Azure Cosmos](#modify-failover-priority).

```azurepowershell-interactive
# Update an Azure Cosmos Account and set Consistency level to Session

$resourceGroupName = "myResourceGroup"
$accountName = "myaccountname"

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$consistencyPolicy = @{ "defaultConsistencyLevel"="Session" }

$account.Properties.consistencyPolicy = $consistencyPolicy
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a id="delete-account"></a> Eliminare un account Azure Cosmos

Questo comando consente di eliminare un account Azure Cosmos esistente.

```azurepowershell-interactive
# Delete an Azure Cosmos Account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName
```

### <a id="update-tags"></a> Aggiornare i tag di un account Azure Cosmos

Nell'esempio seguente viene descritto come impostare [tag delle risorse di Azure][azure-resource-tags] per un account Azure Cosmos.

> [!NOTE]
> Questo comando può essere combinato con i comandi di creazione o aggiornamento aggiungendo il flag `-Tags` con il parametro corrispondente.

```azurepowershell-interactive
# Update tags for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$tags = @{
    "dept" = "Finance";
    "environment" = "Production"
}

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -Tags $tags
```

### <a id="list-keys"></a> Elencare le chiavi dell'account

Quando si crea un account Azure Cosmos DB, il servizio genera due chiavi di accesso principali che possono essere usate per l'autenticazione quando si accede all'account di Azure Cosmos DB. Generando due chiavi di accesso, Azure Cosmos DB consente di rigenerare le chiavi senza interruzioni dell'account Azure Cosmos DB. Sono disponibili anche chiavi di sola lettura per l'autenticazione delle operazioni di sola lettura. Esistono due chiavi di lettura/scrittura (primaria e secondaria) e due chiavi di sola lettura (primaria e secondaria).

```azurepowershell-interactive
# List keys for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keys = Invoke-AzResourceAction -Action listKeys `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName

Select-Object $keys
```

### <a id="list-connection-strings"></a> Elencare le stringhe di connessione

Per gli account di MongoDB, è possibile recuperare la stringa di connessione per connettere l'app MongoDB all'account del database usando il comando seguente.

```azurepowershell-interactive
# List connection strings for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keys = Invoke-AzResourceAction -Action listConnectionStrings `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName

Select-Object $keys
```

### <a id="regenerate-keys"></a> Rigenerare le chiavi dell'Account

Per mantenere più sicure le connessioni è necessario rigenerare periodicamente le chiavi di accesso a un account Azure Cosmos. Una chiave di accesso primarie e secondarie assegnate all'account. In questo modo i client mantenere l'accesso, mentre l'altra viene rigenerata. Esistono quattro tipi di chiavi per un account Azure Cosmos (database primario, secondario, PrimaryReadonly e SecondaryReadonly)

```azurepowershell-interactive
# Regenerate the primary key for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keyKind = @{ "keyKind"="Primary" }

$keys = Invoke-AzResourceAction -Action regenerateKey `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $keyKind

Select-Object $keys
```

### <a id="modify-failover-priority"></a> Modificare la priorità di Failover

Per gli account di database in più aree, è possibile modificare l'ordine in cui un account Cosmos alzerà di lettura a repliche secondarie deve verificarsi un failover regionale sulla replica di scrittura primaria. Quando l'area con `failoverPriority=0` viene modificato, questo comando può essere utilizzato anche per avviare un ripristino di emergenza per eseguire il test di pianificazione del ripristino di emergenza.

Ad esempio riportato di seguito presuppongono l'account ha una priorità di failover corrente di westus = 0 e Stati Uniti orientali = 1 e invertire le aree.

> [!CAUTION]
> Questa operazione attiva un failover manuale di un account Azure Cosmos.

```azurepowershell-interactive
# Change the failover priority for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$failoverPolicies = @(
    @{ "locationName"="East US"; "failoverPriority"=0 },
    @{ "locationName"="West US"; "failoverPriority"=1 }
)

Invoke-AzResourceAction -Action failoverPriorityChange `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $failoverPolicies
```

## <a name="azure-cosmos-database"></a>Database di Azure Cosmos

Le sezioni seguenti illustrano come gestire il database Cosmos Azure, tra cui:

* [Creare un database di Azure Cosmos](#create-db)
* [Creare un database Cosmos Azure con una velocità effettiva condivisa](#create-db-ru)
* [Ottenere la velocità effettiva di un database di Azure Cosmos](#get-db-ru)
* [Elencare tutti i database di Azure Cosmos in un account](#get-all-db)
* [Ottenere un singolo database di Azure Cosmos](#get-db)
* [Eliminare un database di Azure Cosmos](#delete-db)

### <a id="create-db"></a>Creare un database di Azure Cosmos

```azurepowershell-interactive
# Create an Azure Cosmos database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

$DataBaseProperties = @{
    "resource"=@{"id"=$databaseName}
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $DataBaseProperties
```

### <a id="create-db-ru"></a>Creare un database Cosmos Azure con una velocità effettiva condivisa

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database2"
$resourceName = $accountName + "/sql/" + $databaseName

$DataBaseProperties = @{
    "resource"=@{ "id"=$databaseName };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $DataBaseProperties
```

### <a id="get-db-ru"></a>Ottenere la velocità effettiva di un database di Azure Cosmos

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$databaseThroughputResourceType = "Microsoft.DocumentDb/databaseAccounts/apis/databases/settings"
$databaseThroughputResourceName = $accountName + "/sql/" + $databaseName + "/throughput"

Get-AzResource -ResourceType $databaseThroughputResourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $databaseThroughputResourceName  | Select-Object Properties
```

### <a id="get-all-db"></a>Recupera tutti i database di Azure Cosmos in un account

```azurepowershell-interactive
# Get all databases in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$resourceName = $accountName + "/sql/"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName  | Select-Object Properties
```

### <a id="get-db"></a>Ottenere un singolo database di Azure Cosmos

```azurepowershell-interactive
# Get a single database in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a id="delete-db"></a>Eliminare un database di Azure Cosmos

```azurepowershell-interactive
# Delete a database in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName
Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $resourceName
```

## <a name="azure-cosmos-container"></a>Contenitore di Azure Cosmos

Le sezioni seguenti illustrano come gestire il contenitore Cosmos Azure, tra cui:

* [Creare un contenitore di Azure Cosmos](#create-container)
* [Ottenere la velocità effettiva di un contenitore di Azure Cosmos](#get-container-ru)
* [Creare un contenitore di Azure Cosmos con velocità effettiva condivisa](#create-container-ru)
* [Creare un contenitore di Azure Cosmos con indicizzazione personalizzati](#create-container-custom-index)
* [Creare un contenitore di Azure Cosmos con indicizzazione disattivata](#create-container-no-index)
* [Creare un contenitore di Azure Cosmos con chiave univoca e durata (TTL)](#create-container-unique-key-ttl)
* [Creare un contenitore Cosmos Azure con la risoluzione dei conflitti](#create-container-lww)
* [Elencare tutti i contenitori di Azure Cosmos in un database](#list-all-container)
* [Ottenere un singolo contenitore di Azure Cosmos in un database](#get-container)
* [Eliminare un contenitore di Azure Cosmos](#delete-container)

### <a id="create-container"></a>Creare un contenitore di Azure Cosmos

```azurepowershell-interactive
# Create an Azure Cosmos container with default indexes and throughput at 400 RU
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="get-container-ru"></a>Ottenere la velocità effettiva di un contenitore di Azure Cosmos

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$containerThroughputResourceType = "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers/settings"
$containerThroughputResourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName + "/throughput"

Get-AzResource -ResourceType $containerThroughputResourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $containerThroughputResourceName  | Select-Object Properties
```

### <a id="create-container-ru"></a>Creare un contenitore di Azure Cosmos con velocità effettiva condivisa

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container2"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        }
    };
    "options"=@{}
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties 
```

### <a id="create-container-custom-index"></a>Creare un contenitore Cosmos Azure con i criteri di indicizzazione personalizzati

```azurepowershell-interactive
# Create a container with a custom indexing policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container3"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey"); 
            "kind"="Hash"
        }; 
        "indexingPolicy"=@{
            "indexingMode"="Consistent";
            "includedPaths"= @(@{
                "path"="/*";
                "indexes"= @()
            });
            "excludedPaths"= @(@{
                "path"="/myPathToNotIndex/*"
            })
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-no-index"></a>Creare un contenitore di Azure Cosmos con indicizzazione disattivata

```azurepowershell-interactive
# Create an Azure Cosmos container with no indexing
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container4"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        };
        "indexingPolicy"=@{
            "indexingMode"="none"
        }
    };
    "options"=@{ "Throughput"="400" }
} 

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-unique-key-ttl"></a>Creare un contenitore di Azure Cosmos con criteri di chiave univoca e durata (TTL)

```azurepowershell-interactive
# Create a container with a unique key policy and TTL
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container5"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        }; 
        "indexingPolicy"=@{
            "indexingMode"="Consistent";
            "includedPaths"= @(@{
                "path"="/*";
                "indexes"= @()
            });
            "excludedPaths"= @()
        };
        "uniqueKeyPolicy"= @{
            "uniqueKeys"= @(@{
                "paths"= @(
                    "/myUniqueKey1";
                    "/myUniqueKey2"
                )
            })
        };
        "defaultTtl"= 100;
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-lww"></a>Creare un contenitore Cosmos Azure con la risoluzione dei conflitti

Per creare criteri di risoluzione dei conflitti per l'uso di una stored procedure, impostare `"mode"="custom"` e impostare il percorso di risoluzione come il nome della stored procedure, `"conflictResolutionPath"="myResolverStoredProcedure"`. Per tutti i conflitti di scrittura per il ConflictsFeed e gestire separatamente, impostare `"mode"="custom"` e `"conflictResolutionPath"=""`

```azurepowershell-interactive
# Create container with last-writer-wins conflict resolution policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container6"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        };
        "conflictResolutionPolicy"=@{
            "mode"="lastWriterWins";
            "conflictResolutionPath"="/myResolutionPath"
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="list-all-container"></a>Elencare tutti i contenitori di Azure Cosmos in un database

```azurepowershell-interactive
# List all Azure Cosmos containers in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a id="get-container"></a>Ottenere un singolo contenitore di Azure Cosmos in un database

```azurepowershell-interactive
# Get a single Azure Cosmos container in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container5"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a id="delete-container"></a>Eliminare un contenitore di Azure Cosmos

```azurepowershell-interactive
# Delete an Azure Cosmos container
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $resourceName
```

## <a name="next-steps"></a>Passaggi successivi

* [Tutti gli esempi di PowerShell](powershell-samples.md)
* [Come gestire account Azure Cosmos](how-to-manage-database-account.md)
* [Creare un contenitore di Azure Cosmos](how-to-create-container.md)
* [Configurare time-to-live in Azure Cosmos DB](how-to-time-to-live.md)

<!--Reference style links - using these makes the source content way more readable than using inline links-->

[powershell-install-configure]: https://docs.microsoft.com/azure/powershell-install-configure
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/
