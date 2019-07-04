---
title: Informazioni su come gestire gli account di database in Azure Cosmos DB
description: Informazioni su come gestire gli account di database in Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 0abeb3235f296e2dc873bcfe88910cdd12555d71
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476205"
---
# <a name="manage-an-azure-cosmos-account"></a>Gestire un account Azure Cosmos

Questo articolo descrive come gestire varie attività relative a un account Azure Cosmos usando il portale di Azure, Azure PowerShell, l'interfaccia della riga di comando di Azure e i modelli di Azure Resource Manager.

## <a name="create-an-account"></a>Creare un account

### <a id="create-database-account-via-portal"></a>Portale di Azure

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a id="create-database-account-via-cli"></a>

```azurecli-interactive
# Create an account
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname' # must be lower case.

az cosmosdb create \
   --name $accountName \
   --resource-group $resourceGroupName \
   --kind GlobalDocumentDB \
   --default-consistency-level Session \
   --locations regionName=WestUS failoverPriority=0 isZoneRedundant=False \
   --locations regionName=EastUS failoverPriority=1 isZoneRedundant=False \
   --enable-multiple-write-locations true
```

### <a id="create-database-account-via-ps"></a>Azure PowerShell
```azurepowershell-interactive
# Create an Azure Cosmos account for Core (SQL) API
$resourceGroupName = "myResourceGroup"
$location = "West US"
$accountName = "mycosmosaccount" # must be lower case.

$locations = @(
    @{ "locationName"="West US"; "failoverPriority"=0 },
    @{ "locationName"="East US"; "failoverPriority"=1 }
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
    "enableMultipleWriteLocations"="true"
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Location $location `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a id="create-database-account-via-arm-template"></a>Modello di Azure Resource Manager

Questo modello di Azure Resource Manager consentirà di creare, per qualsiasi API supportata, un account Azure Cosmos configurato con due aree e opzioni per selezionare il livello di coerenza, il failover automatico e la funzionalità multimaster. Per distribuire il modello, fare clic su Deploy to Azure (Distribuisci in Azure) nella pagina README relativa alla [creazione di un account Azure Cosmos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-create-multi-region-account).

## <a name="addremove-regions-from-your-database-account"></a>Aggiungere o rimuovere aree dall'account di database

### <a id="add-remove-regions-via-portal"></a>Portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com). 

1. Passare all'account Azure Cosmos e aprire il menu **Replica i dati a livello globale**.

1. Per aggiungere aree, selezionare gli esagoni sulla mappa con l'etichetta **+** corrispondente alle aree geografiche desiderate. In alternativa, per aggiungere un'area, selezionare l'opzione **+ Aggiungi area** e scegliere un'area dal menu a discesa.

1. Per rimuovere aree, deselezionare una o più aree nella mappa selezionando gli esagoni blu con segni di spunta. In alternativa, selezionare l'icona del cestino (🗑) accanto all'area sul lato destro.

1. Selezionare **OK** per salvare le modifiche.

   ![Aggiungere o rimuovere aree](./media/how-to-manage-database-account/add-region.png)

In modalità di scrittura in una singola area non è possibile rimuovere l'area di scrittura. È necessario eseguire il failover in un'area diversa prima di poter eliminare l'area di scrittura corrente.

In modalità di scrittura in più aree è possibile aggiungere o rimuovere qualsiasi area, purché resti almeno un'area.

### <a id="add-remove-regions-via-cli"></a>

```azurecli-interactive
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'

# Create an account with 1 region
az cosmosdb create --name $accountName --resource-group $resourceGroupName --locations regionName=westus failoverPriority=0 isZoneRedundant=False

# Add a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName --locations regionName=westus failoverPriority=0 isZoneRedundant=False --locations regionName=EastUS failoverPriority=1 isZoneRedundant=False

# Remove a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName --locations regionName=westus failoverPriority=0 isZoneRedundant=False
```

### <a id="add-remove-regions-via-ps"></a>Azure PowerShell

```azurepowershell-interactive
# Create an account with 1 region
$resourceGroupName = "myResourceGroup"
$location = "West US"
$accountName = "mycosmosaccount" # must be lower case.

$locations = @( @{ "locationName"="West US"; "failoverPriority"=0 } )
$consistencyPolicy = @{ "defaultConsistencyLevel"="Session" }
$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy
}
New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Location $location `
    -Name $accountName -PropertyObject $CosmosDBProperties

# Add a region
$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$locations = @( 
    @{ "locationName"="West US"; "failoverPriority"=0 },
    @{ "locationName"="East Us"; "failoverPriority"=1 } 
)

$account.Properties.locations = $locations
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties

# Azure Resource Manager does not wait on the resource update
Write-Host "Confirm region added before continuing..."

# Remove a region
$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$locations = @( @{ "locationName"="West US"; "failoverPriority"=0 } )

$account.Properties.locations = $locations
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a id="configure-multiple-write-regions"></a>Configurare più aree di scrittura

### <a id="configure-multiple-write-regions-portal"></a>Portale di Azure

Aprire la scheda **Replica i dati a livello globale** e selezionare **Abilita** per abilitare le operazioni di scrittura in più aree. Dopo aver abilitato tali operazioni, tutte le aree di lettura di cui si dispone attualmente nell'account diventeranno aree di lettura e scrittura. 

> [!NOTE]
> Dopo aver abilitato le operazioni di scrittura in più aree, non è possibile disabilitarle. 

![Screenshot relativo alla configurazione della funzionalità multimaster per l'account Azure Cosmos](./media/how-to-manage-database-account/single-to-multi-master.png)

Per altre domande su questa funzionalità, scrivere all'alias askcosmosdb@microsoft.com. 

### <a id="configure-multiple-write-regions-cli"></a>

```azurecli-interactive
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'
az cosmosdb update --name $accountName --resource-group $resourceGroupName --enable-multiple-write-locations true
```

### <a id="configure-multiple-write-regions-ps"></a>Azure PowerShell

```azurepowershell-interactive
# Update an Azure Cosmos account from single to multi-master

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$account.Properties.enableMultipleWriteLocations = "true"
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a id="configure-multiple-write-regions-arm"></a>modello di Resource Manager

È possibile eseguire la migrazione di un account da master singolo a multimaster distribuendo il modello di Resource Manager usato per creare l'account e impostando `enableMultipleWriteLocations: true`. Il modello di Azure Resource Manager seguente è il modello minimo essenziale che distribuirà un account Azure Cosmos per l'API SQL con un'unica area e la funzionalità multimaster abilitata.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2015-04-08",
            "location": "[parameters('location')]",
            "tags": {},
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": { "defaultConsistencyLevel": "Session" },
                "locations": [
                    {
                        "locationName": "[parameters('location')]",
                        "failoverPriority": 0
                    }
                ],
                "enableMultipleWriteLocations": true
            }
        }
    ]
}
```

## <a id="automatic-failover"></a>Abilitare il failover automatico per l'account Azure Cosmos

L'opzione di failover automatico consente ad Azure Cosmos DB di eseguire il failover all'area con la priorità di failover più alta senza alcuna azione da parte dell'utente nel caso in cui un'area diventasse non disponibile. Quando il failover automatico è abilitato, è possibile modificare la priorità delle aree. Per abilitare il failover automatico, è necessario che l'account abbia almeno due aree.

### <a id="enable-automatic-failover-via-portal"></a>Portale di Azure

1. Dall'account Azure Cosmos aprire il riquadro **Replica i dati a livello globale**.

2. Nella parte superiore del riquadro fare clic sul pulsante **Failover automatico**.

   ![Menu Replica i dati a livello globale](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Nel riquadro **Failover automatico** assicurarsi che **Abilita failover automatico** sia impostato su **SÌ**. 

4. Selezionare **Salva**.

   ![Menu del portale Failover automatico](./media/how-to-manage-database-account/automatic-failover.png)

### <a id="enable-automatic-failover-via-cli"></a>

```azurecli-interactive
# Enable automatic failover on an existing account
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'

az cosmosdb update --name $accountName --resource-group $resourceGroupName --enable-automatic-failover true
```

### <a id="enable-automatic-failover-via-ps"></a>Azure PowerShell

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName

$account.Properties.enableAutomaticFailover="true";
$CosmosDBProperties = $account.Properties;

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Impostare le priorità di failover per l'account Azure Cosmos

Dopo aver configurato un account Cosmos per il failover automatico, è possibile modificare la priorità di failover per le aree.

> [!IMPORTANT]
> Se l'account è configurato per il failover automatico, non è possibile modificare l'area di scrittura (priorità di failover zero). Per cambiare l'area di scrittura, è necessario disabilitare il failover automatico ed eseguire un failover manuale.

### <a id="set-failover-priorities-via-portal"></a>Portale di Azure

1. Dall'account Azure Cosmos aprire il riquadro **Replica i dati a livello globale**.

2. Nella parte superiore del riquadro fare clic sul pulsante **Failover automatico**.

   ![Menu Replica i dati a livello globale](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Nel riquadro **Failover automatico** assicurarsi che **Abilita failover automatico** sia impostato su **SÌ**.

4. Per modificare la priorità di failover, trascinare le aree di lettura tramite i tre puntini a sinistra nella riga, visualizzati al passaggio del mouse.

5. Selezionare **Salva**.

   ![Menu del portale Failover automatico](./media/how-to-manage-database-account/automatic-failover.png)

### <a id="set-failover-priorities-via-cli"></a>

```azurecli-interactive
# Assume region order is initially eastus=0 westus=1 southeastasia=2 on account creation
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'

az cosmosdb failover-priority-change --name $accountName --resource-group $resourceGroupName --failover-policies eastus=0 southeastasia=1 westus=2
```

### <a id="set-failover-priorities-via-ps"></a>Azure PowerShell

```azurepowershell-interactive
# Assume account currently has regions with priority: West US = 0, East US = 1, Southeast Asia = 2
$resourceGroupName = "myResourceGroup"
$accountName = "myaccountname"

$failoverPolicies = @(
    @{ "locationName"="West US"; "failoverPriority"=0 },
    @{ "locationName"="Southeast Asia"; "failoverPriority"=1 },
    @{ "locationName"="East US"; "failoverPriority"=2 }
)

Invoke-AzResourceAction -Action failoverPriorityChange `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $failoverPolicies
```

## <a id="manual-failover"></a>Eseguire un failover manuale per un account Azure Cosmos

> [!IMPORTANT]
> Affinché questa operazione abbia esito positivo, è necessario configurare l'account Azure Cosmos per il failover manuale.

Il processo di esecuzione di un failover manuale prevede la modifica dell'area di scrittura dell'account (priorità di failover = 0) mediante l'impostazione di un'altra area configurata per l'account.

> [!NOTE]
> Non è possibile sottoporre a failover manuale gli account multimaster. Per le applicazioni che usano l'SDK di Azure Cosmos, l'SDK rileverà quando un'area diventa non disponibile e quindi eseguirà automaticamente il reindirizzamento all'area successiva più vicina, se nell'SDK viene usata l'API multihosting.

### <a id="enable-manual-failover-via-portal"></a>Portale di Azure

1. Passare all'account Azure Cosmos e aprire il menu **Replica i dati a livello globale**.

2. Nella parte superiore del menu selezionare **Failover manuale**.

   ![Menu Replica i dati a livello globale](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Nel menu **Failover manuale** selezionare la nuova area di scrittura. Selezionare la casella di controllo per indicare di aver compreso che questa opzione modifica l'area di scrittura.

4. Per attivare il failover, selezionare **OK**.

   ![Menu del portale Failover manuale](./media/how-to-manage-database-account/manual-failover.png)

### <a id="enable-manual-failover-via-cli"></a>

```azurecli-interactive
# Assume account currently has regions with priority: eastus=0 westus=1
# Change the priority order to trigger a failover of the write region
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'

az cosmosdb update --name $accountName --resource-group $resourceGroupName --locations regionName=westus failoverPriority=0 isZoneRedundant=False --locations regionName=eastus failoverPriority=1 isZoneRedundant=False
```

### <a id="enable-manual-failover-via-ps"></a>Azure PowerShell

```azurepowershell-interactive
# Assume account currently has regions with priority: West US = 0, East US = 1
# Change the priority order to trigger a failover of the write region
$resourceGroupName = "myResourceGroup"
$accountName = "myaccountname"

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName

$locations = @(
    @{ "locationName"="East US"; "failoverPriority"=0 },
    @{ "locationName"="West US"; "failoverPriority"=1 }
)

$account.Properties.locations=$locations;
$CosmosDBProperties = $account.Properties;

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni ed esempi su come gestire l'account Azure Cosmos, nonché il database e i contenitori, leggere gli articoli seguenti:

* [Gestire Azure Cosmos DB usando Azure PowerShell](manage-with-powershell.md)
* [Gestire Azure Cosmos DB usando l'interfaccia della riga di comando di Azure](manage-with-cli.md)
