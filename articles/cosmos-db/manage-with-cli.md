---
title: Gestire Azure Cosmos DB risorse API Core (SQL) con l'interfaccia della riga di comando di Azure
description: Gestire Azure Cosmos DB risorse API Core (SQL) usando l'interfaccia della riga di comando di Azure.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/13/2020
ms.author: mjbrown
ms.openlocfilehash: e5e5a057d6c88e0963ab674aa7dbf2d420ff21b8
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93083926"
---
# <a name="manage-azure-cosmos-core-sql-api-resources-using-azure-cli"></a>Gestire risorse API di Azure Cosmos Core (SQL) con l'interfaccia della riga di comando di Azure
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

La guida seguente illustra i comandi comuni per automatizzare la gestione degli account, dei database e dei contenitori di Azure Cosmos DB usando l'interfaccia della riga di comando di Azure. Per tutti i comandi dell'interfaccia della riga di comando di Azure Cosmos DB sono disponibili pagine di riferimento in [Informazioni di riferimento sull'interfaccia della riga di comando di Azure](/cli/azure/cosmosdb). Altri esempi sono disponibili in [Esempi dell'interfaccia della riga di comando di Azure Cosmos DB](cli-samples.md), incluse le procedure per creare e gestire gli account, i database e i contenitori di Cosmos DB per MongoDB, Gremlin, Cassandra e API Tabella.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo argomento è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.12.1 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

Per gli esempi dell'interfaccia della riga di comando di Azure relativi ad altre API, vedere [Esempi dell'interfaccia della riga di comando per Cassandra](cli-samples-cassandra.md), [Esempi dell'interfaccia della riga di comando per l'API MongoDB](cli-samples-mongodb.md), [Esempi dell'interfaccia della riga di comando per Gremlin](cli-samples-gremlin.md), [Esempi dell'interfaccia della riga di comando per Tabella](cli-samples-table.md)

> [!IMPORTANT]
> Non è possibile rinominare Azure Cosmos DB risorse perché questo viola il modo in cui Azure Resource Manager funziona con gli URI delle risorse.

## <a name="azure-cosmos-accounts"></a>Account Azure Cosmos

Le sezioni seguenti illustrano come gestire l'account Azure Cosmos:

* [Creare un account Azure Cosmos](#create-an-azure-cosmos-db-account)
* [Aggiungere o rimuovere aree](#add-or-remove-regions)
* [Abilitare le Scritture in più aree](#enable-multiple-write-regions)
* [Imposta priorità di failover a livello di area](#set-failover-priority)
* [Abilita failover automatico](#enable-automatic-failover)
* [Attivare il failover manuale](#trigger-manual-failover)
* [Elencare le chiavi dell'account](#list-account-keys)
* [Elencare le chiavi dell'account di sola lettura](#list-read-only-account-keys)
* [Elencare le stringhe di connessione](#list-connection-strings)
* [Rigenera chiave dell'account](#regenerate-account-key)

### <a name="create-an-azure-cosmos-db-account"></a>Creare un account Azure Cosmos DB

Creare un account di Azure Cosmos DB con l'API SQL, coerenza della sessione nelle aree Stati Uniti occidentali 2 e Stati Uniti orientali 2:

> [!IMPORTANT]
> Il nome dell'account Azure Cosmos deve essere minuscolo e inferiore a 44 caratteri.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount' #needs to be lower case and less than 44 characters

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --default-consistency-level Session \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --locations regionName='East US 2' failoverPriority=1 isZoneRedundant=False
```

### <a name="add-or-remove-regions"></a>Aggiungere o rimuovere aree

Creare un account Azure Cosmos con due aree, aggiungere un'area e rimuovere un'area.

> [!NOTE]
> Non è possibile aggiungere o rimuovere simultaneamente aree `locations` e cambiare l'ordine delle proprietà per un account Azure Cosmos. La modifica delle aree deve essere eseguita come operazione distinta rispetto a qualsiasi altra modifica apportata alla risorsa dell'account.
> [!NOTE]
> Questo comando consente di aggiungere e rimuovere aree, ma non di modificare le priorità di failover o attivare un failover manuale. Vedere [impostare la priorità di failover](#set-failover-priority) e [attivare il failover manuale](#trigger-manual-failover).

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Create an account with 2 regions
az cosmosdb create --name $accountName --resource-group $resourceGroupName \
    --locations regionName="West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName="East US 2" failoverPriority=1 isZoneRedundant=False

# Add a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName \
    --locations regionName="West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName="East US 2" failoverPriority=1 isZoneRedundant=False \
    --locations regionName="South Central US" failoverPriority=2 isZoneRedundant=False

# Remove a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName \
    --locations regionName="West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName="East US 2" failoverPriority=1 isZoneRedundant=False
```

### <a name="enable-multiple-write-regions"></a>Abilita più aree di scrittura

Abilitare le Scritture in più aree per un account Cosmos

```azurecli-interactive
# Update an Azure Cosmos account from single write region to multiple write regions
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

az cosmosdb update --ids $accountId --enable-multiple-write-locations true
```

### <a name="set-failover-priority"></a>Imposta priorità di failover

Impostare la priorità di failover per un account Azure Cosmos configurato per il failover automatico

```azurecli-interactive
# Assume region order is initially 'West US 2'=0 'East US 2'=1 'South Central US'=2 for account
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

# Make South Central US the next region to fail over to instead of East US 2
az cosmosdb failover-priority-change --ids $accountId \
    --failover-policies 'West US 2=0' 'South Central US=1' 'East US 2=2'
```

### <a name="enable-automatic-failover"></a> Abilitare il failover automatico

```azurecli-interactive
# Enable automatic failover on an existing account
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

az cosmosdb update --ids $accountId --enable-automatic-failover true
```

### <a name="trigger-manual-failover"></a>Attivare il failover manuale

> [!CAUTION]
> Se si modifica Region con Priority = 0, viene attivato un failover manuale per un account Azure Cosmos. Qualsiasi altra modifica della priorità non attiverà un failover.

```azurecli-interactive
# Assume region order is initially 'West US 2=0' 'East US 2=1' 'South Central US=2' for account
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

# Trigger a manual failover to promote East US 2 as new write region
az cosmosdb failover-priority-change --ids $accountId \
    --failover-policies 'East US 2=0' 'South Central US=1' 'West US 2=2'
```

### <a name="list-all-account-keys"></a><a id="list-account-keys"></a> Elencare tutte le chiavi dell'account

Ottenere tutte le chiavi per un account Cosmos.

```azurecli-interactive
# List all account keys
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
   -n $accountName \
   -g $resourceGroupName
```

### <a name="list-read-only-account-keys"></a>Elencare le chiavi dell'account di sola lettura

Ottenere le chiavi di sola lettura per un account Cosmos.

```azurecli-interactive
# List read-only account keys
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
    -n $accountName \
    -g $resourceGroupName \
    --type read-only-keys
```

### <a name="list-connection-strings"></a>Elencare le stringhe di connessione

Ottenere le stringhe di connessione per un account Cosmos.

```azurecli-interactive
# List connection strings
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
    -n $accountName \
    -g $resourceGroupName \
    --type connection-strings
```

### <a name="regenerate-account-key"></a>Rigenerare una chiave dell'account

Rigenerare una nuova chiave per un account Cosmos.

```azurecli-interactive
# Regenerate secondary account keys
# key-kind values: primary, primaryReadonly, secondary, secondaryReadonly
az cosmosdb keys regenerate \
    -n $accountName \
    -g $resourceGroupName \
    --key-kind secondary
```

## <a name="azure-cosmos-db-database"></a>Database Azure Cosmos DB

Le sezioni seguenti illustrano come gestire il database Azure Cosmos DB:

* [Creare un database](#create-a-database)
* [Creazione di un database con velocità effettiva condivisa](#create-a-database-with-shared-throughput)
* [Migrare un database alla velocità effettiva di scalabilità automatica](#migrate-a-database-to-autoscale-throughput)
* [Modificare la velocità effettiva del database](#change-database-throughput)
* [Impedisci l'eliminazione di un database](#prevent-a-database-from-being-deleted)

### <a name="create-a-database"></a>Creazione di un database

Creare un database Cosmos.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'

az cosmosdb sql database create \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName
```

### <a name="create-a-database-with-shared-throughput"></a>Creazione di un database con velocità effettiva condivisa

Creare un database Cosmos con una velocità effettiva condivisa.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
throughput=400

az cosmosdb sql database create \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName \
    --throughput $throughput
```

### <a name="migrate-a-database-to-autoscale-throughput"></a>Migrare un database alla velocità effettiva di scalabilità automatica

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'

# Migrate to autoscale throughput
az cosmosdb sql database throughput migrate \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName \
    -t 'autoscale'

# Read the new autoscale max throughput
az cosmosdb sql database throughput show \
    -g $resourceGroupName \
    -a $accountName \
    -n $databaseName \
    --query resource.autoscaleSettings.maxThroughput \
    -o tsv
```

### <a name="change-database-throughput"></a>Modificare la velocità effettiva del database

Aumentare la velocità effettiva di un database Cosmos di 1000 ur/sec.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
newRU=1000

# Get minimum throughput to make sure newRU is not lower than minRU
minRU=$(az cosmosdb sql database throughput show \
    -g $resourceGroupName -a $accountName -n $databaseName \
    --query resource.minimumThroughput -o tsv)

if [ $minRU -gt $newRU ]; then
    newRU=$minRU
fi

az cosmosdb sql database throughput update \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName \
    --throughput $newRU
```

### <a name="prevent-a-database-from-being-deleted"></a>Impedisci l'eliminazione di un database

Inserire un blocco di eliminazione delle risorse di Azure in un database per impedirne l'eliminazione. Questa funzionalità richiede che il blocco dell'account Cosmos venga modificato dagli SDK del piano dati. Per altre informazioni, vedere [prevenzione delle modifiche dagli SDK](role-based-access-control.md#prevent-sdk-changes). I blocchi di risorse di Azure possono anche impedire la modifica di una risorsa specificando un `ReadOnly` tipo di blocco. Per un database Cosmos, può essere usato per impedire la modifica della velocità effettiva.

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'

lockType='CanNotDelete' # CanNotDelete or ReadOnly
databaseParent="databaseAccounts/$accountName"
databaseLockName="$databaseName-Lock"

# Create a delete lock on database
az lock create --name $databaseLockName \
    --resource-group $resourceGroupName \
    --resource-type Microsoft.DocumentDB/sqlDatabases \
    --lock-type $lockType \
    --parent $databaseParent \
    --resource $databaseName

# Delete lock on database
lockid=$(az lock show --name $databaseLockName \
        --resource-group $resourceGroupName \
        --resource-type Microsoft.DocumentDB/sqlDatabases \
        --resource $databaseName \
        --parent $databaseParent \
        --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="azure-cosmos-db-container"></a>Contenitore di Azure Cosmos DB

Le sezioni seguenti illustrano come gestire il contenitore Azure Cosmos DB:

* [Creare un contenitore](#create-a-container)
* [Creare un contenitore con scalabilità automatica](#create-a-container-with-autoscale)
* [Creare un contenitore con TTL abilitato](#create-a-container-with-ttl)
* [Creare un contenitore con un criterio di indicizzazione personalizzato](#create-a-container-with-a-custom-index-policy)
* [Modificare la velocità effettiva del contenitore](#change-container-throughput)
* [Migrare un contenitore per la velocità effettiva di scalabilità automatica](#migrate-a-container-to-autoscale-throughput)
* [Impedisci l'eliminazione di un contenitore](#prevent-a-container-from-being-deleted)

### <a name="create-a-container"></a>Creare un contenitore

Creare un contenitore Cosmos con criteri di indice predefiniti, chiave di partizione e ur/s di 400.

```azurecli-interactive
# Create a SQL API container
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
partitionKey='/myPartitionKey'
throughput=400

az cosmosdb sql container create \
    -a $accountName -g $resourceGroupName \
    -d $databaseName -n $containerName \
    -p $partitionKey --throughput $throughput
```

### <a name="create-a-container-with-autoscale"></a>Creare un contenitore con scalabilità automatica

Creare un contenitore Cosmos con i criteri di indice predefiniti, la chiave di partizione e le UR/s di scalabilità automatica di 4000.

```azurecli-interactive
# Create a SQL API container
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
partitionKey='/myPartitionKey'
maxThroughput=4000

az cosmosdb sql container create \
    -a $accountName -g $resourceGroupName \
    -d $databaseName -n $containerName \
    -p $partitionKey --max-throughput $maxThroughput
```

### <a name="create-a-container-with-ttl"></a>Creare un contenitore con TTL

Creare un contenitore Cosmos con TTL abilitato.

```azurecli-interactive
# Create an Azure Cosmos container with TTL of one day
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'

az cosmosdb sql container update \
    -g $resourceGroupName \
    -a $accountName \
    -d $databaseName \
    -n $containerName \
    --ttl=86400
```

### <a name="create-a-container-with-a-custom-index-policy"></a>Creare un contenitore con criteri di indice personalizzati

Creare un contenitore Cosmos con criteri di indice personalizzati, un indice spaziale, un indice composto, una chiave di partizione e ur/s di 400.

```azurecli-interactive
# Create a SQL API container
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
partitionKey='/myPartitionKey'
throughput=400

# Generate a unique 10 character alphanumeric string to ensure unique resource names
uniqueId=$(env LC_CTYPE=C tr -dc 'a-z0-9' < /dev/urandom | fold -w 10 | head -n 1)

# Define the index policy for the container, include spatial and composite indexes
idxpolicy=$(cat << EOF
{
    "indexingMode": "consistent",
    "includedPaths": [
        {"path": "/*"}
    ],
    "excludedPaths": [
        { "path": "/headquarters/employees/?"}
    ],
    "spatialIndexes": [
        {"path": "/*", "types": ["Point"]}
    ],
    "compositeIndexes":[
        [
            { "path":"/name", "order":"ascending" },
            { "path":"/age", "order":"descending" }
        ]
    ]
}
EOF
)
# Persist index policy to json file
echo "$idxpolicy" > "idxpolicy-$uniqueId.json"


az cosmosdb sql container create \
    -a $accountName -g $resourceGroupName \
    -d $databaseName -n $containerName \
    -p $partitionKey --throughput $throughput \
    --idx @idxpolicy-$uniqueId.json

# Clean up temporary index policy file
rm -f "idxpolicy-$uniqueId.json"
```

### <a name="change-container-throughput"></a>Modificare la velocità effettiva del contenitore

Aumentare la velocità effettiva di un contenitore Cosmos di 1000 ur/sec.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
newRU=1000

# Get minimum throughput to make sure newRU is not lower than minRU
minRU=$(az cosmosdb sql container throughput show \
    -g $resourceGroupName -a $accountName -d $databaseName \
    -n $containerName --query resource.minimumThroughput -o tsv)

if [ $minRU -gt $newRU ]; then
    newRU=$minRU
fi

az cosmosdb sql container throughput update \
    -a $accountName \
    -g $resourceGroupName \
    -d $databaseName \
    -n $containerName \
    --throughput $newRU
```

### <a name="migrate-a-container-to-autoscale-throughput"></a>Migrare un contenitore per la velocità effettiva di scalabilità automatica

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'

# Migrate to autoscale throughput
az cosmosdb sql container throughput migrate \
    -a $accountName \
    -g $resourceGroupName \
    -d $databaseName \
    -n $containerName \
    -t 'autoscale'

# Read the new autoscale max throughput
az cosmosdb sql container throughput show \
    -g $resourceGroupName \
    -a $accountName \
    -d $databaseName \
    -n $containerName \
    --query resource.autoscaleSettings.maxThroughput \
    -o tsv
```

### <a name="prevent-a-container-from-being-deleted"></a>Impedisci l'eliminazione di un contenitore

Inserire un blocco di eliminazione delle risorse di Azure in un contenitore per impedirne l'eliminazione. Questa funzionalità richiede che il blocco dell'account Cosmos venga modificato dagli SDK del piano dati. Per altre informazioni, vedere [prevenzione delle modifiche dagli SDK](role-based-access-control.md#prevent-sdk-changes). I blocchi di risorse di Azure possono anche impedire la modifica di una risorsa specificando un `ReadOnly` tipo di blocco. Per un contenitore Cosmos, questo può essere usato per impedire che la velocità effettiva o qualsiasi altra proprietà venga modificata.

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'

lockType='CanNotDelete' # CanNotDelete or ReadOnly
databaseParent="databaseAccounts/$accountName"
containerParent="databaseAccounts/$accountName/sqlDatabases/$databaseName"
containerLockName="$containerName-Lock"

# Create a delete lock on container
az lock create --name $containerLockName \
    --resource-group $resourceGroupName \
    --resource-type Microsoft.DocumentDB/containers \
    --lock-type $lockType \
    --parent $containerParent \
    --resource $containerName

# Delete lock on container
lockid=$(az lock show --name $containerLockName \
        --resource-group $resourceGroupName \
        --resource-type Microsoft.DocumentDB/containers \
        --resource-name $containerName \
        --parent $containerParent \
        --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere:

* [Installare l'interfaccia da riga di comando di Azure](/cli/azure/install-azure-cli)
* [Riferimenti per l'interfaccia della riga di comando](/cli/azure/cosmosdb)
* [Esempi dell'interfaccia della riga di comando di Azure Cosmos DB](cli-samples.md)