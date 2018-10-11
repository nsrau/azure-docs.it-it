---
title: Abilitare il supporto multimaster per account Azure Cosmos DB
description: Questo articolo descrive come abilitare il supporto multimaster durante la creazione di un account Azure Cosmos DB con il portale di Azure, PowerShell, l'interfaccia della riga di comando o un modello di Azure Resource Manager.
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: b6fc04fc728f753dc8a3900b26c6c01f03cc7710
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2018
ms.locfileid: "49077239"
---
# <a name="enable-multi-master-for-azure-cosmos-db-accounts"></a>Abilitare il supporto multimaster per account Azure Cosmos DB

Il supporto multimaster viene abilitato durante la creazione di un account Azure Cosmos DB. Un account Azure Cosmos DB può essere creato usando il portale di Azure, PowerShell, l'interfaccia della riga di comando o un modello di Azure Resource Manager.

> [!IMPORTANT]
> Attualmente il supporto multimaster può essere abilitato solo per nuovi account Azure Cosmos DB. Gli account Azure Cosmos DB esistenti non possono usare questa funzionalità. Microsoft sta lavorando per fornire supporto per gli account esistenti e annuncerà il supporto non appena sarà disponibile.

Dopo aver creato un account Azure Cosmos DB con supporto multimaster, è possibile creare database e contenitori, caricare documenti e assegnare criteri di risoluzione dei conflitti. Per informazioni sulla risoluzione dei conflitti con il supporto multimaster e alcuni esempi di codice, vedere [Esempi di codice multimaster](multi-master-conflict-resolution.md#code-samples).

## <a name="enable-multi-master-using-azure-portal"></a>Abilitare il supporto multimaster nel portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com/)

2. Selezionare **Crea una risorsa > Database > Azure Cosmos DB**.

3. Nel riquadro **Nuovo account** immettere le impostazioni seguenti per un nuovo account Azure Cosmos DB:

   |**Impostazione**  |**Valore consigliato** |**Descrizione**|
   |---------|---------|---------|
   |Sottoscrizione   | {Sottoscrizione in uso}  |Selezionare la sottoscrizione di Azure da usare per questo account Azure Cosmos DB.  |
   |Gruppo di risorse  |   {Nome del gruppo di risorse}    |  Selezionare un gruppo di risorse esistente oppure  **Crea nuovo**, quindi immettere il nome di un nuovo gruppo di risorse per il proprio account. |
   |Nome account | {Nome account}   |  Immettere un nome univoco per identificare l'account Azure Cosmos DB.        |
   |API  |   Qualsiasi   |  Selezionare un tipo di API.   |
   |Località  | Selezionare qualsiasi area   | Selezionare la posizione geografica in cui ospitare l'account Azure Cosmos DB. Poiché questo account sarà in più aree, è possibile scegliere qualsiasi area.  |
   |Abilita ridondanza geografica   |  Abilita  |  Selezionare questa opzione per abilitare il supporto multimaster, attraverso l'opzione seguente.   |
   |Abilita Multimaster | Abilita  | Selezionare questa opzione per abilitare il supporto multimaster per questo account. |


## <a name="using-multi-master-in-sdks"></a>Uso del supporto multimaster negli SDK

Con un account abilitato per il supporto multimaster, all'interno delle applicazioni è possibile sfruttare tutti i vantaggi del supporto multimaster attraverso ConnectionPolicy, come mostrato di seguito.

```csharp
ConnectionPolicy policy = new ConnectionPolicy
{
   ConnectionMode = ConnectionMode.Direct,
   ConnectionProtocol = Protocol.Tcp,
   UseMultipleWriteLocations = true,
};
policy.PreferredLocations.Add(LocationNames.WestUS);
policy.PreferredLocations.Add(LocationNames.NorthEurope);
policy.PreferredLocations.Add(LocationNames.SoutheastAsia);
```

## <a name="enable-multi-master-using-powershell"></a>Abilitare il supporto multimaster con PowerShell

È anche possibile creare un account Cosmos DB abilitato per il supporto multimaster impostando il parametro `enableMultipleWriteLocations` su "true". Per creare un account Azure Cosmos DB con il supporto multimaster abilitato, aprire una finestra di PowerShell ed eseguire lo script seguente:

```azurepowershell-interactive
$locations = @(@{"locationName"="East US"; "failoverPriority"=0},
             @{"locationName"="West US"; "failoverPriority"=1})

$iprangefilter = "<ip-range-filter>"

$consistencyPolicy = @{"defaultConsistencyLevel"="Session";
                       "maxIntervalInSeconds"= "10";
                       "maxStalenessPrefix"="200"}

$CosmosDBProperties = @{"databaseAccountOfferType"="Standard";
                        "locations"=$locations;
                        "consistencyPolicy"=$consistencyPolicy;
                        "ipRangeFilter"=$iprangefilter;
                        "enableMultipleWriteLocations"="true"}

New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
  -ApiVersion "2015-04-08" `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -Name "myCosmosDbAccount" `
  -Properties $CosmosDBProperties
```

## <a name="enable-multi-master-using-cli"></a>Abilitare il supporto multimaster con l'interfaccia della riga di comando

È possibile abilitare il supporto multimaster impostando il parametro enable-multiple-write-locations su "true". Per creare un account Azure Cosmos DB con il supporto multimaster abilitato, aprire l'interfaccia della riga di comando di Azure o Cloud Shell ed eseguire il comando seguente:

```azurecli-interactive
az cosmosdb create \
   –-name "myCosmosDbAccount" \
   --resource-group "myResourceGroup" \
   --default-consistency-level "Session" \
   --enable-automatic-failover "true" \
   --locations "EastUS=0" "WestUS=1" \
   --enable-multiple-write-locations true \
```

## <a name="enable-multi-master-using-resource-manager-template"></a>Abilitare il supporto multimaster con un modello di Resource Manager

Il codice JSON seguente è un modello di Resource Manager di esempio che è possibile usare per distribuire un account Azure Cosmos DB. Per informazioni sul formato e sulla sintassi dei modelli di Resource Manager, vedere la documentazione di [Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). Il parametro importante da notare in questo modello è "enableMultipleWriteLocations": true.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
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

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato descritto come abilitare il supporto multimaster per account Azure Cosmos DB. Esaminare quindi le risorse seguenti:

* [Uso del multimaster con database NoSQL open source](multi-master-oss-nosql.md)

* [Risoluzione dei conflitti multimaster in Azure Cosmos DB](multi-master-conflict-resolution.md)
