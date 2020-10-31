---
title: Impedisci l'eliminazione o la modifica delle risorse Azure Cosmos DB
description: Usare i blocchi delle risorse di Azure per impedire l'eliminazione o la modifica di Azure Cosmos DB risorse.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/06/2020
ms.author: mjbrown
ms.openlocfilehash: 6db7bbf03b023a80032a4ed15f2f4f82dd2b0b98
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93088370"
---
# <a name="prevent-azure-cosmos-db-resources-from-being-deleted-or-changed"></a>Impedisci l'eliminazione o la modifica delle risorse Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In qualità di amministratore, potrebbe essere necessario bloccare un account, un database o un contenitore di Azure Cosmos per impedire ad altri utenti dell'organizzazione di eliminare o modificare accidentalmente le risorse critiche. È possibile impostare il livello di blocco CanNotDelete o ReadOnly.

- **CanNotDelete** significa che gli utenti autorizzati possono leggere e modificare una risorsa, ma non eliminarla.
- **ReadOnly** significa che gli utenti autorizzati possono leggere una risorsa, ma non eliminarla o aggiornarla. L'applicazione di questo blocco è simile alla concessione a tutti gli utenti autorizzati solo le autorizzazioni concesse dal ruolo Lettore.

## <a name="how-locks-are-applied"></a>Come vengono applicati i blocchi

Quando si applica un blocco in un ambito padre, tutte le risorse in tale ambito ereditano lo stesso blocco. Anche le risorse aggiunte successivamente ereditano il blocco dal padre. Il blocco più restrittivo nell'ereditarietà ha la precedenza.

Diversamente dal controllo degli accessi in base al ruolo, i blocchi di gestione consentono di applicare una restrizione a tutti gli utenti e i ruoli. Per informazioni su RBAC per Azure Cosmos DB vedere il [controllo degli accessi in base al ruolo in Azure Cosmos DB](role-based-access-control.md).

I blocchi di Resource Manager si applicano solo alle operazioni che si verificano nel piano di gestione, costituito da operazioni inviate a https://management.azure.com. I blocchi non limitano il modo in cui le risorse eseguono le proprie funzioni. Vengono limitate le modifiche alle risorse, ma non le operazioni delle risorse. Ad esempio, un blocco ReadOnly in un contenitore di Azure Cosmos impedisce l'eliminazione o la modifica del contenitore. Non impedisce la creazione, l'aggiornamento o l'eliminazione di dati nel contenitore. Le transazioni di dati sono consentite in quanto tali operazioni non vengono inviate a https://management.azure.com.

## <a name="manage-locks"></a>Gestisci blocchi

> [!WARNING]
> I blocchi delle risorse non funzionano per le modifiche apportate dagli utenti che accedono a Azure Cosmos DB usando le chiavi dell'account, a meno che l'account Azure Cosmos non venga inizialmente bloccato abilitando la proprietà disableKeyBasedMetadataWriteAccess. È necessario prestare attenzione prima di abilitare questa proprietà per assicurarsi che non interrompa le applicazioni esistenti che apportano modifiche alle risorse usando qualsiasi SDK, portale di Azure o strumenti di terze parti che si connettono tramite chiavi dell'account e modifichino risorse quali la modifica della velocità effettiva, l'aggiornamento dei criteri di indice e così via. Per ulteriori informazioni e per esaminare un elenco di controllo per verificare che le applicazioni continuino a funzionare, vedere [prevenzione delle modifiche da Azure Cosmos DB SDK](role-based-access-control.md#prevent-sdk-changes)

### <a name="powershell"></a>PowerShell

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "my-cosmos-account"
$lockName = "$accountName-Lock"

# First, update the account to prevent changes by anything that connects via account keys
Update-AzCosmosDBAccount -ResourceGroupName $resourceGroupName -Name $accountName -DisableKeyBasedMetadataWriteAccess true

# Create a Delete Lock on an Azure Cosmos account resource and all child resources
New-AzResourceLock `
    -ApiVersion "2020-04-01" `
    -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
    -ResourceGroupName $resourceGroupName `
    -ResourceName $accountName `
    -LockName $lockName `
    -LockLevel "CanNotDelete" # CanNotDelete or ReadOnly
```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

```bash
resourceGroupName='myResourceGroup'
accountName='my-cosmos-account'
$lockName="$accountName-Lock"

# First, update the account to prevent changes by anything that connects via account keys
az cosmosdb update  --name $accountName --resource-group $resourceGroupName  --disable-key-based-metadata-write-access true

# Create a Delete Lock on an Azure Cosmos account resource
az lock create --name $lockName \
    --resource-group $resourceGroupName \
    --resource-type Microsoft.DocumentDB/databaseAccount \
    --lock-type 'CanNotDelete' # CanNotDelete or ReadOnly \
    --resource $accountName
```

### <a name="template"></a>Modello

Quando si applica un blocco a una risorsa Azure Cosmos DB, usare i formati seguenti:

- nome - `{resourceName}/Microsoft.Authorization/{lockName}`
- tipo - `{resourceProviderNamespace}/{resourceType}/providers/locks`

> [!IMPORTANT]
> Quando si modifica un account Azure Cosmos esistente, assicurarsi di includere le altre proprietà per l'account e le risorse figlio quando si redploying con questa proprietà. Non distribuire questo modello così come è o verrà reimpostato tutte le proprietà dell'account.

```json
"resources": [
    {
        "type": "Microsoft.DocumentDB/databaseAccounts",
        "name": "[variables('accountName')]",
        "apiVersion": "2020-04-01",
        "kind": "GlobalDocumentDB",
        "location": "[parameters('location')]",
        "properties": {
            "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
            "locations": "[variables('locations')]",
            "databaseAccountOfferType": "Standard",
            "enableAutomaticFailover": "[parameters('automaticFailover')]",
            "disableKeyBasedMetadataWriteAccess": true
        }
    },
    {
        "type": "Microsoft.DocumentDB/databaseAccounts/providers/locks",
        "apiVersion": "2020-04-01",
        "name": "[concat(variables('accountName'), '/Microsoft.Authorization/siteLock')]",
        "dependsOn": [
        "[resourceId('Microsoft.DocumentDB/databaseAccounts', variables('accountName'))]"
        ],
        "properties": {
        "level": "CanNotDelete",
        "notes": "Cosmos account should not be deleted."
        }
    }
]
```

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dei blocchi di Azure Resource Manager](../azure-resource-manager/management/lock-resources.md)
