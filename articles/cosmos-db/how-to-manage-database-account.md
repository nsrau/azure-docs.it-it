---
title: Informazioni su come gestire gli account di database in Azure Cosmos DB
description: Informazioni su come gestire gli account di database in Azure Cosmos DB
author: christopheranderson
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/17/2018
ms.author: chrande
ms.openlocfilehash: 6efa0bab6327022bfe4a1f6d94a6a135cd1f91f3
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2019
ms.locfileid: "58849062"
---
# <a name="manage-an-azure-cosmos-account"></a>Gestire un account Azure Cosmos

Questo articolo descrive come gestire gli account Azure Cosmos DB. Si apprenderà come configurare il multihoming, aggiungere o rimuovere un'area, configurare più aree di scrittura e configurare le priorità di failover. 

## <a name="create-a-database-account"></a>Creare un account di database

### <a id="create-database-account-via-portal"></a>Portale di Azure

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a id="create-database-account-via-cli"></a>

```bash
# Create an account
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group Name>
```

## <a name="configure-clients-for-multi-homing"></a>Configurare i client per il multihoming

### <a id="configure-clients-multi-homing-dotnet"></a>.NET SDK v2

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");

// Pass the connection policy with the preferred locations on it to the client.
DocumentClient client = new DocumentClient(new Uri(this.accountEndpoint), this.accountKey, policy);
```

### <a id="configure-clients-multi-homing-dotnet-v3"></a>.NET SDK v3 (anteprima)

```csharp
CosmosConfiguration config = new CosmosConfiguration("endpoint", "key");
config.UseCurrentRegion("West US");
CosmosClient client = new CosmosClient(config);
```

### <a id="configure-clients-multi-homing-java-async"></a>Java Async SDK

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Collections.singletonList(region));

AsyncDocumentClient client =
    new AsyncDocumentClient.Builder()
        .withMasterKeyOrResourceToken(this.accountKey)
        .withServiceEndpoint(this.accountEndpoint)
        .withConsistencyLevel(ConsistencyLevel.Eventual)
        .withConnectionPolicy(policy).build();
```

### <a id="configure-clients-multi-homing-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.UseMultipleWriteLocations = true;
connectionPolicy.PreferredLocations = [region];

const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy,
  consistencyLevel: ConsistencyLevel.Eventual
});
```

### <a id="configure-clients-multi-homing-python"></a>Python SDK

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="addremove-regions-from-your-database-account"></a>Aggiungere o rimuovere aree dall'account di database

### <a id="add-remove-regions-via-portal"></a>Portale di Azure

1. Passare all'account Azure Cosmos DB e aprire il menu **Replica i dati a livello globale**.

2. Per aggiungere aree, selezionare gli esagoni sulla mappa con etichetta **+** corrispondenti all'area geografica desiderata. Per aggiungere un'area, selezionare l'opzione **+ Aggiungi area** e scegliere un'area dal menu a discesa.

3. Per rimuovere aree, deselezionare una o più aree nella mappa selezionando gli esagoni blu con segni di spunta. In alternativa, selezionare l'icona del cestino (🗑) accanto all'area sul lato destro.

4. Selezionare **OK** per salvare le modifiche.

   ![Aggiungere o rimuovere aree](./media/how-to-manage-database-account/add-region.png)

In modalità di scrittura in una singola area non è possibile rimuovere l'area di scrittura. È necessario eseguire il failover in un'area diversa prima di poter eliminare l'area di scrittura corrente.

In modalità di scrittura in più aree è possibile aggiungere o rimuovere qualsiasi area, purché resti almeno un'area.

### <a id="add-remove-regions-via-cli"></a>

```bash
# Given an account created with 1 region like so
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations eastus=0

# Add a new region by adding another region to the list
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations eastus=0 westus=1

# Remove a region by removing a region from the list
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations westus=0
```

## <a name="configure-multiple-write-regions"></a>Configurare più aree di scrittura

### <a id="configure-multiple-write-regions-portal"></a>Portale di Azure

Quando si crea un account di database, assicurarsi che sia abilitata l'impostazione **Multi-region Writes** (Scritture in più aree).

![Screenshot di creazione di un account Azure Cosmos](./media/how-to-manage-database-account/account-create.png)

### <a id="configure-multiple-write-regions-cli"></a>

```bash
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-multiple-write-locations true
```

### <a id="configure-multiple-write-regions-arm"></a>modello di Resource Manager

Il codice JSON seguente rappresenta un esempio di modello di Azure Resource Manager. È possibile usarlo per distribuire un account Azure Cosmos DB con un criterio di coerenza con decadimento ristretto. L'intervallo di decadimento massimo è impostato su 5 secondi. Il numero massimo di richieste non aggiornate tollerate è impostato su 100. Per informazioni sul formato e sulla sintassi dei modelli di Resource Manager, vedere [Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String"
        },
        "locationName": {
            "type": "String"
        },
        "defaultExperience": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2015-04-08",
            "location": "[parameters('location')]",
            "tags": {
                "defaultExperience": "[parameters('defaultExperience')]"
            },
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": {
                    "defaultConsistencyLevel": "BoundedStaleness",
                    "maxIntervalInSeconds": 5,
                    "maxStalenessPrefix": 100
                },
                "locations": [
                    {
                        "id": "[concat(parameters('name'), '-', parameters('location'))]",
                        "failoverPriority": 0,
                        "locationName": "[parameters('locationName')]"
                    }
                ],
                "isVirtualNetworkFilterEnabled": false,
                "enableMultipleWriteLocations": true,
                "virtualNetworkRules": [],
                "dependsOn": []
            }
        }
    ]
}
```


## <a id="manual-failover"></a>Abilitare il failover manuale per l'account Azure Cosmos DB

### <a id="enable-manual-failover-via-portal"></a>Portale di Azure

1. Passare all'account Azure Cosmos DB e aprire il menu **Replica i dati a livello globale**.

2. Nella parte superiore del menu selezionare **Failover manuale**.

   ![Menu Replica i dati a livello globale](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Nel menu **Failover manuale** selezionare la nuova area di scrittura. Selezionare la casella di controllo per indicare di aver compreso che questa opzione modifica l'area di scrittura.

4. Per attivare il failover, selezionare **OK**.

   ![Menu del portale Failover manuale](./media/how-to-manage-database-account/manual-failover.png)

### <a id="enable-manual-failover-via-cli"></a>

```bash
# Given your account currently has regions with priority like so: eastus=0 westus=1
# Change the priority order to trigger a failover of the write region
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations westus=0 eastus=1
```

## <a id="automatic-failover"></a>Abilitare il failover automatico per l'account Azure Cosmos DB

### <a id="enable-automatic-failover-via-portal"></a>Portale di Azure

1. Dall'account Azure Cosmos DB aprire il riquadro **Replica i dati a livello globale**. 

2. Nella parte superiore del riquadro fare clic sul pulsante **Failover automatico**.

   ![Menu Replica i dati a livello globale](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Nel riquadro **Failover automatico** assicurarsi che **Abilita failover automatico** sia impostato su **SÌ**. 

4. Selezionare **Salva**.

   ![Menu del portale Failover automatico](./media/how-to-manage-database-account/automatic-failover.png)

In questo menu è anche possibile impostare le priorità di failover.

### <a id="enable-automatic-failover-via-cli"></a>

```bash
# Enable automatic failover on account creation
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-automatic-failover true

# Enable automatic failover on an existing account
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-automatic-failover true

# Disable automatic failover on an existing account
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-automatic-failover false
```

## <a name="set-failover-priorities-for-your-azure-cosmos-db-account"></a>Impostare le priorità di failover per l'account Azure Cosmos DB

### <a id="set-failover-priorities-via-portal"></a>Portale di Azure

1. Dall'account Azure Cosmos DB aprire il riquadro **Replica i dati a livello globale**. 

2. Nella parte superiore del riquadro fare clic sul pulsante **Failover automatico**.

   ![Menu Replica i dati a livello globale](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Nel riquadro **Failover automatico** assicurarsi che **Abilita failover automatico** sia impostato su **SÌ**. 

4. Per modificare la priorità di failover, trascinare le aree di lettura tramite i tre puntini a sinistra nella riga, visualizzati al passaggio del mouse. 

5. Selezionare **Salva**.

   ![Menu del portale Failover automatico](./media/how-to-manage-database-account/automatic-failover.png)

Non è possibile modificare l'area di scrittura in questo menu. Per modificare manualmente l'area di scrittura, è necessario eseguire un failover manuale.

### <a id="set-failover-priorities-via-cli"></a>

```bash
# Assume region order is initially eastus=0 westus=1 automatic failover on account creation
az cosmosdb failover-priority-change --name <Azure Cosmos account name> --resource-group <Resource Group name> --failover-policies westus=0 eastus=1
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come gestire i conflitti di dati e i livelli di coerenza in Azure Cosmos DB. Vedere gli articoli seguenti:

* [Gestire la coerenza](how-to-manage-consistency.md)
* [Gestire i conflitti tra le aree](how-to-manage-conflicts.md)

