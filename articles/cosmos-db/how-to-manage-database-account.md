---
title: Informazioni su come gestire gli account di database in Azure Cosmos DB
description: Informazioni su come gestire gli account di database in Azure Cosmos DB
services: cosmos-db
author: christopheranderson
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/17/2018
ms.author: chrande
ms.openlocfilehash: 69eafaa3be7a966bec179713d3e7eecd48ea3b67
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2018
ms.locfileid: "50243691"
---
# <a name="manage-database-account"></a>Gestire un account di database

Questo articolo descrive come gestire l'account Cosmos DB per configurare il multihoming, aggiungere/rimuovere un'area, configurare più aree di scrittura e le priorità di failover. 

## <a name="create-a-database-account"></a>Creare un account di database

### <a id="create-database-account-via-portal"></a>Portale di Azure

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a id="create-database-account-via-cli"></a>

```bash
# Create an account
az cosmosdb create --name <Cosmos DB Account name> --resource-group <Resource Group Name>
```

## <a name="configure-clients-for-multi-homing"></a>Configurare i client per il multihoming

### <a id="configure-clients-multi-homing-dotnet"></a>.NET

```csharp
// Create a new Connection Policy
ConnectionPolicy policy = new ConnectionPolicy
    {
        // Note: These aren't required settings for multi-homing,
        // just suggested defaults
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true,
    };
// Add regions to Preferred locations
// The name of the location will match what you see in the portal/etc.
policy.PreferredLocations.Add("East US");
policy.PreferredLocations.Add("North Europe");

// Pass the Connection policy with the preferred locations on it to the client.
DocumentClient client = new DocumentClient(new Uri(this.accountEndpoint), this.accountKey, policy);
```

### <a id="configure-clients-multi-homing-java-async"></a>Java Async

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setPreferredLocations(Collections.singleton("West US"));
AsyncDocumentClient client =
        new AsyncDocumentClient.Builder()
                .withMasterKey(this.accountKey)
                .withServiceEndpoint(this.accountEndpoint)
                .withConnectionPolicy(policy).build();
```

### <a id="configure-clients-multi-homing-java-sync"></a>Java Sync

```java
ConnectionPolicy connectionPolicy = new ConnectionPolicy();
Collection<String> preferredLocations = new ArrayList<String>();
preferredLocations.add("Australia East");
connectionPolicy.setPreferredLocations(preferredLocations);
DocumentClient client = new DocumentClient(accountEndpoint, accountKey, connectionPolicy);
```

### <a id="configure-clients-multi-homing-javascript"></a>Node.js/JavaScript/TypeScript

```javascript
// Set up the connection policy with your preferred regions
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.PreferredLocations = ["West US", "Australia East"];

// Pass that connection policy to the client
const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy
});
```

### <a id="configure-clients-multi-homing-python"></a>Python

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.PreferredLocations = ['West US', 'Japan West']
client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy)

```

## <a name="addremove-regions-from-your-database-account"></a>Aggiungere o rimuovere aree dall'account di database

### <a id="add-remove-regions-via-portal"></a>Portale di Azure

1. Passare all'account Azure Cosmos DB e aprire il menu **Replica i dati a livello globale**.

2. Per aggiungere aree, selezionare una o più aree nella mappa facendo clic sugli esagoni vuoti con l'etichetta **"+"** corrispondenti all'area desiderata. È possibile aggiungere un'area anche selezionando l'opzione **+ Aggiungi area** e scegliendo un'area dal menu a discesa.

3. Per rimuovere aree, deselezionare una o più aree nella mappa facendo clic sugli esagoni blu con un segno di spunta oppure selezionare l'icona del cestino (🗑) accanto all'area sul lato destro.

4. Fare clic su Salva per salvare le modifiche.

   ![Menu Aggiungi/Rimuovi aree](./media/how-to-manage-database-account/add-region.png)

In modalità di scrittura in una singola area non è possibile rimuovere l'area di scrittura. È necessario eseguire il failover in un'area diversa prima di eliminare l'area di scrittura corrente.

In modalità di scrittura in più aree è possibile aggiungere o rimuovere qualsiasi area, purché resti almeno un'area.

### <a id="add-remove-regions-via-cli"></a>

```bash
# Given an account created with 1 region like so
az cosmosdb create --name <Cosmos DB Account name> --resource-group <Resource Group name> --locations 'eastus=0'

# Add a new region by adding another region to the list
az cosmosdb update --name <Cosmos DB Account name> --resource-group <Resource Group name> --locations 'eastus=0 westus=1'

# Remove a region by removing a region from the list
az cosmosdb update --name <Cosmos DB Account name> --resource-group <Resource Group name> --locations 'westus=0'
```

## <a name="configure-multiple-write-regions"></a>Configurare più aree di scrittura

### <a id="configure-multiple-write-regions-portal"></a>Portale di Azure

Quando si crea un account di database, assicurarsi che sia abilitata l'impostazione **Multi-region Writes** (Scritture in più aree).

![Screenshot di creazione account Cosmos DB](./media/how-to-manage-database-account/account-create.png)

### <a id="configure-multiple-write-regions-cli"></a>

```bash
az cosmosdb create --name <Cosmos DB Account name> --resource-group <Resource Group name> --enable-multiple-write-locations true
```

### <a id="configure-multiple-write-regions-arm"></a>modello di Resource Manager

Il codice JSON seguente rappresenta un modello di esempio di Resource Manager. Può essere usato per distribuire un account Azure Cosmos DB con un criterio di coerenza con decadimento ristretto, un intervallo di decadimento massimo di 5 secondi e un numero massimo di richieste non aggiornate tollerate uguale a 100. Per informazioni sul formato e sulla sintassi dei modelli di Resource Manager, vedere la documentazione di [Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

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


## <a name="enable-manual-failover-for-your-cosmos-account"></a>Abilitare il failover manuale per l'account Cosmos

### <a id="enable-manual-failover-via-portal"></a>Portale di Azure

1. Passare all'account Azure Cosmos DB e aprire il menu **Replica i dati a livello globale**.

2. Fare clic sul pulsante **Failover manuale** nella parte superiore del menu.

   ![Menu Replica i dati a livello globale](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Nel menu **Failover manuale** selezionare la nuova area di scrittura e selezionare la casella per confermare di aver compreso che questa operazione modificherà l'area di scrittura.

4. Fare clic su "OK" per attivare il failover.

   ![Menu del portale Failover manuale](./media/how-to-manage-database-account/manual-failover.png)

### <a id="enable-manual-failover-via-cli"></a>

```bash
# Given your account currently has regions with priority like so: 'eastus=0 westus=1'
# Change the priority order to trigger a failover of the write region
az cosmosdb update --name <Cosmos DB Account name> --resource-group <Resource Group name> --locations 'eastus=1 westus=0'
```

## <a name="enable-automatic-failover-for-your-cosmos-account"></a>Abilitare il failover automatico per l'account Cosmos

### <a id="enable-automatic-failover-via-portal"></a>Portale di Azure

1. Dall'account Azure Cosmos DB aprire il riquadro **Replica i dati a livello globale**. 

2. Fare clic sul pulsante **Failover automatico** nella parte superiore del riquadro.

   ![Menu Replica i dati a livello globale](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Nel riquadro **Failover automatico** assicurarsi che il **Abilita failover automatico** sia impostato su **SÌ**. 

4. Fare clic su salva nella parte inferiore del menu.

   ![Menu del portale Failover automatico](./media/how-to-manage-database-account/automatic-failover.png)

In questo menu è anche possibile impostare le priorità di failover.

### <a id="enable-automatic-failover-via-cli"></a>

```bash
# Enable automatic failover on account creation
az cosmosdb create --name <Cosmos DB Account name> --resource-group <Resource Group name> --enable-automatic-failover true

# Enable automatic failover on an existing account
az cosmosdb update --name <Cosmos DB Account name> --resource-group <Resource Group name> --enable-automatic-failover true

# Disable automatic failover on an existing account
az cosmosdb update --name <Cosmos DB Account name> --resource-group <Resource Group name> --enable-automatic-failover false
```

## <a name="set-failover-priorities-for-your-cosmos-account"></a>Impostare le priorità di failover per l'account Cosmos

### <a id="set-failover-priorities-via-portal"></a>Portale di Azure

1. Dall'account Azure Cosmos DB aprire il riquadro **Replica i dati a livello globale**. 

2. Fare clic sul pulsante **Failover automatico** nella parte superiore del riquadro.

   ![Menu Replica i dati a livello globale](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Nel riquadro **Failover automatico** assicurarsi che il **Abilita failover automatico** sia impostato su **SÌ**. 

4. È possibile modificare la priorità di failover facendo clic e trascinando le aree di lettura tramite i tre puntini a sinistra nella riga, visualizzati al passaggio del mouse. 

5. Fare clic su salva nella parte inferiore del menu.

   ![Menu del portale Failover automatico](./media/how-to-manage-database-account/automatic-failover.png)

Non è possibile modificare l'area di scrittura in questo menu. È necessario eseguire un failover manuale per modificare manualmente l'area di scrittura.

### <a id="set-failover-priorities-via-cli"></a>

```bash
az cosmosdb failover-priority-change --name <Cosmos DB Account name> --resource-group <Resource Group name> --failover-policies 'eastus=0 westus=2 southcentralus=1'
```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sulla gestione dei conflitti di dati e dei livelli di coerenza in Cosmos DB, vedere la documentazione seguente:

* [Gestire la coerenza](how-to-manage-consistency.md)
* [Gestire i conflitti tra le aree](how-to-manage-conflicts.md)

