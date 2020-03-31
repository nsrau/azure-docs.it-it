---
title: Aggiungere assegnazioni di ruolo con contratti di controllo degli accessi in base al ruolo e modelli di Azure Resource ManagerAdd role assignments with RBAC and Azure Resource Manager templates
description: Informazioni su come concedere l'accesso alle risorse di Azure per utenti, gruppi, entità servizio o identità gestite usando i modelli di controllo degli accessi in base al ruolo di Azure e Azure Resource Manager.Learn how to grant access to Azure resources for users, groups, service principals, or managed identities using Azure role-based access control (RBAC) and Azure Resource Manager templates.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 9f817880f938f5d03024e3aacd9b84817a5ac721
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77138292"
---
# <a name="add-role-assignments-using-azure-rbac-and-azure-resource-manager-templates"></a>Aggiungere assegnazioni di ruolo usando il controllo degli accessi in base al ruolo di Azure e i modelli di Azure Resource ManagerAdd role assignments using Azure RBAC and Azure Resource Manager templates

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]Oltre a usare Azure PowerShell o l'interfaccia della riga di comando di Azure, è possibile assegnare ruoli usando i modelli di [Azure Resource Manager.](../azure-resource-manager/templates/template-syntax.md) I modelli possono essere usati per distribuire le risorse in modo coerente e ripetuto. In questo articolo viene descritto come assegnare ruoli utilizzando i modelli.

## <a name="get-object-ids"></a>Ottenere gli ID oggettoGet object IDs

Per assegnare un ruolo, è necessario specificare l'ID dell'utente, del gruppo o dell'applicazione a cui si desidera assegnare il ruolo. L'ID ha `11111111-1111-1111-1111-111111111111`il formato: . È possibile ottenere l'ID usando il portale di Azure, Azure PowerShell o l'interfaccia della riga di comando di Azure.You can get the ID using the Azure portal, Azure PowerShell, or Azure CLI.

### <a name="user"></a>Utente

Per ottenere l'ID di un utente, puoi utilizzare i comandi di visualizzazione dell'utente dell'annuncio [Get-AzADUser](/powershell/module/az.resources/get-azaduser) o [az.](/cli/azure/ad/user#az-ad-user-show)

```azurepowershell
$objectid = (Get-AzADUser -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad user show --id "{email}" --query objectId --output tsv)
```

### <a name="group"></a>Gruppo

Per ottenere l'ID di un gruppo, puoi utilizzare i comandi di visualizzazione del gruppo di annunci [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup) o [az.](/cli/azure/ad/group#az-ad-group-show)

```azurepowershell
$objectid = (Get-AzADGroup -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad group show --group "{name}" --query objectId --output tsv)
```

### <a name="application"></a>Applicazione

Per ottenere l'ID di un'entità servizio (identità utilizzata da un'applicazione), è possibile usare i comandi [Dell'elenco Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) o [az ad sp.](/cli/azure/ad/sp#az-ad-sp-list) Per un'entità servizio, usare l'ID oggetto e **non** l'ID applicazione.

```azurepowershell
$objectid = (Get-AzADServicePrincipal -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad sp list --display-name "{name}" --query [].objectId --output tsv)
```

## <a name="add-a-role-assignment"></a>Aggiungi un'assegnazione di ruolo

In RBAC, per concedere l'accesso, si aggiunge un'assegnazione di ruolo.

### <a name="resource-group-without-parameters"></a>Gruppo di risorse (senza parametri)

Nel modello seguente viene illustrato un modo di base per aggiungere un'assegnazione di ruolo. Alcuni valori vengono specificati all'interno del modello. Il modello seguente illustra:

-  Come assegnare il ruolo Lettore a un utente, un gruppo o un'applicazione in un ambito di gruppo di risorseHow to assign the [Reader](built-in-roles.md#reader) role to a user, group, or application at a resource group scope

Per utilizzare il modello, è necessario effettuare le seguenti operazioni:

- Creare un nuovo file JSON e copiare il modelloCreate a new JSON file and copy the template
- Sostituire `<your-principal-id>` con l'ID di un utente, di un gruppo o di un'applicazione a cui assegnare il ruolo

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(resourceGroup().id)]",
            "properties": {
                "roleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
                "principalId": "<your-principal-id>"
            }
        }
    ]
}
```

Di seguito sono riportati alcuni comandi di creazione di new-AzResourceGroupDeployment e az group per l'avvio della distribuzione in un gruppo di risorse denominato ExampleGroup.Here are example [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) and [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create) commands for how to start the deployment in a resource group named ExampleGroup.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json
```

Di seguito viene illustrato un esempio dell'assegnazione di ruolo Lettore a un utente per un gruppo di risorse dopo la distribuzione del modello.

![Assegnazione di ruolo nell'ambito del gruppo di risorseRole assignment at resource group scope](./media/role-assignments-template/role-assignment-template.png)

### <a name="resource-group-or-subscription"></a>Gruppo di risorse o sottoscrizione

Il modello precedente non è molto flessibile. Il modello seguente utilizza parametri e può essere utilizzato in ambiti diversi. Il modello seguente illustra:

- Come assegnare un ruolo a un utente, un gruppo o un'applicazione in un gruppo di risorse o un ambito di sottoscrizioneHow to assign a role to a user, group, or application at either a resource group or subscription scope
- Come specificare i ruoli proprietario, collaboratore e lettore come parametro

Per usare il modello, è necessario specificare gli input seguenti:

- ID di un utente, di un gruppo o di un'applicazione a cui assegnare il ruolo
- Un ID univoco che verrà utilizzato per l'assegnazione di ruolo oppure è possibile utilizzare l'ID predefinito

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "roleNameGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "A new GUID used to identify the role assignment"
            }
        }
    },
    "variables": {
        "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('roleNameGuid')]",
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

> [!NOTE]
> Questo modello non è idempotente `roleNameGuid` a meno che non venga fornito lo stesso valore come parametro per ogni distribuzione del modello. Se `roleNameGuid` non viene specificato alcun valore, per impostazione predefinita viene generato `Conflict: RoleAssignmentExists` un nuovo GUID in ogni distribuzione e le distribuzioni successive avranno esito negativo con un errore.

L'ambito dell'assegnazione di ruolo è determinato dal livello della distribuzione. Ecco alcuni esempi [di New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) e az [group deployment creano](/cli/azure/group/deployment#az-group-deployment-create) comandi per avviare la distribuzione in un ambito di gruppo di risorse.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

Di seguito sono riportati alcuni comandi [di creazione New-AzDeployment](/powershell/module/az.resources/new-azdeployment) e [az deployment](/cli/azure/deployment#az-deployment-create) per avviare la distribuzione in un ambito di sottoscrizione e specificare il percorso.

```azurepowershell
New-AzDeployment -Location centralus -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az deployment create --location centralus --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

### <a name="resource"></a>Risorsa

Se è necessario aggiungere un'assegnazione di ruolo a livello di risorsa, il formato dell'assegnazione di ruolo è diverso. Specificare lo spazio dei nomi del provider di risorse e il tipo di risorsa della risorsa a cui assegnare il ruolo. È inoltre possibile includere il nome della risorsa nel nome dell'assegnazione di ruolo.

Per il tipo e il nome dell'assegnazione di ruolo, utilizzare il formato seguente:

```json
"type": "{resource-provider-namespace}/{resource-type}/providers/roleAssignments",
"name": "{resource-name}/Microsoft.Authorization/{role-assign-GUID}"
```

Il modello seguente illustra:

- Come creare un nuovo account di archiviazione
- Come assegnare un ruolo a un utente, un gruppo o un'applicazione nell'ambito dell'account di archiviazioneHow to assign a role to a user, group, or application at the storage account scope
- Come specificare i ruoli proprietario, collaboratore e lettore come parametro

Per usare il modello, è necessario specificare gli input seguenti:

- ID di un utente, di un gruppo o di un'applicazione a cui assegnare il ruolo

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
        "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        },
        {
            "type": "Microsoft.Storage/storageAccounts/providers/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[concat(variables('storageName'), '/Microsoft.Authorization/', guid(uniqueString(variables('storageName'))))]",
            "dependsOn": [
                "[variables('storageName')]"
            ],
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

Per distribuire il modello precedente, usare i comandi del gruppo di risorse. Ecco alcuni esempi [di New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) e az [group deployment creano](/cli/azure/group/deployment#az-group-deployment-create) comandi per avviare la distribuzione in un ambito di risorse.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Contributor
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Contributor
```

Di seguito viene illustrato un esempio dell'assegnazione di ruolo Collaboratore a un utente per un account di archiviazione dopo la distribuzione del modello.

![Assegnazione di ruolo nell'ambito della risorsaRole assignment at resource scope](./media/role-assignments-template/role-assignment-template-resource.png)

### <a name="new-service-principal"></a>Nuova entità servizio

Se si crea una nuova entità servizio e si tenta immediatamente di assegnare un ruolo a tale entità servizio, tale assegnazione di ruolo può avere esito negativo in alcuni casi. Ad esempio, se si crea una nuova identità gestita e quindi si tenta di assegnare un ruolo a tale entità servizio nello stesso modello di Azure Resource Manager, l'assegnazione di ruolo potrebbe non riuscire. Il motivo di questo errore è probabilmente un ritardo di replica. L'entità servizio viene creata in un'area. Tuttavia, l'assegnazione di ruolo potrebbe verificarsi in un'area diversa che non ha ancora replicato l'entità servizio. Per risolvere questo scenario, `principalType` è `ServicePrincipal` necessario impostare la proprietà su quando si crea l'assegnazione di ruolo.

Il modello seguente illustra:

- Come creare una nuova entità servizio di identità gestitaHow to create a new managed identity service principal
- Come specificare il`principalType`
- Come assegnare il ruolo Collaboratore a tale entità servizio nell'ambito di un gruppo di risorseHow to assign the Contributor role to that service principal at a resource group scope

Per usare il modello, è necessario specificare gli input seguenti:

- Il nome di base dell'identità gestita oppure è possibile utilizzare la stringa predefinita

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "baseName": {
            "type": "string",
            "defaultValue": "msi-test"
        }
    },
    "variables": {
        "identityName": "[concat(parameters('baseName'), '-bootstrap')]",
        "bootstrapRoleAssignmentId": "[guid(concat(resourceGroup().id, 'contributor'))]",
        "contributorRoleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]"
    },
    "resources": [
        {
            "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
            "name": "[variables('identityName')]",
            "apiVersion": "2018-11-30",
            "location": "[resourceGroup().location]"
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[variables('bootstrapRoleAssignmentId')]",
            "dependsOn": [
                "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
            ],
            "properties": {
                "roleDefinitionId": "[variables('contributorRoleDefinitionId')]",
                "principalId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName')), '2018-11-30').principalId]",
                "scope": "[resourceGroup().id]",
                "principalType": "ServicePrincipal"
            }
        }
    ]
}
```

Ecco alcuni esempi [di New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) e az [group deployment creano](/cli/azure/group/deployment#az-group-deployment-create) comandi per avviare la distribuzione in un ambito di gruppo di risorse.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup2 -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup2 --template-file rbac-test.json
```

Di seguito viene illustrato un esempio dell'assegnazione di ruolo Collaboratore a una nuova entità servizio identità gestita dopo la distribuzione del modello.

![Assegnazione di ruolo per una nuova entità servizio identità gestitaRole assignment for a new managed identity service principal](./media/role-assignments-template/role-assignment-template-msi.png)

## <a name="next-steps"></a>Passaggi successivi

- [Guida introduttiva: Creare e distribuire modelli di Azure Resource Manager con il portale di Azure](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)
- [Comprendere la struttura e la sintassi dei modelli di Azure Resource ManagerUnderstand the structure and syntax of Azure Resource Manager Templates](../azure-resource-manager/templates/template-syntax.md)
- [Creare gruppi di risorse e risorse a livello di sottoscrizione](../azure-resource-manager/templates/deploy-to-subscription.md)
- [Modelli di Guida introduttiva di AzureAzure Quickstart Templates](https://azure.microsoft.com/resources/templates/?term=rbac)
