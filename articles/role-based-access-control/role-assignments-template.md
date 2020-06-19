---
title: Aggiungere assegnazioni di ruolo di Azure tramite modelli di Azure Resource Manager - Controllo degli accessi in base al ruolo di Azure
description: Informazioni su come consentire l'accesso alle risorse di Azure per utenti, gruppi ed entità servizio tramite i modelli di Azure Resource Manager e il controllo degli accessi in base al ruolo di Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/26/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: baf309a93f8ba976cb6511c05ba5032ad07a0fc9
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83874045"
---
# <a name="add-azure-role-assignments-using-azure-resource-manager-templates"></a>Aggiungere assegnazioni di ruolo di Azure tramite modelli di Azure Resource Manager

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] Oltre a usare Azure PowerShell o l'interfaccia della riga di comando di Azure, è possibile assegnare ruoli tramite [modelli di Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md). I modelli possono essere usati per distribuire le risorse in modo coerente e ripetuto. Questo articolo descrive come assegnare ruoli usando i modelli.

## <a name="get-object-ids"></a>Recuperare ID oggetto

Per assegnare un ruolo, è necessario specificare l'ID dell'utente, del gruppo o dell'applicazione a cui si vuole assegnare il ruolo. Il formato dell'ID è il seguente: `11111111-1111-1111-1111-111111111111`. È possibile ottenere l'ID tramite il portale di Azure, Azure PowerShell o l'interfaccia della riga di comando di Azure.

### <a name="user"></a>Utente

Per ottenere l'ID di un utente, è possibile usare i comandi [Get-AzADUser](/powershell/module/az.resources/get-azaduser) o [az ad user show](/cli/azure/ad/user#az-ad-user-show).

```azurepowershell
$objectid = (Get-AzADUser -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad user show --id "{email}" --query objectId --output tsv)
```

### <a name="group"></a>Gruppo

Per ottenere l'ID di un gruppo, è possibile usare i comandi [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup) o [az ad group show](/cli/azure/ad/group#az-ad-group-show).

```azurepowershell
$objectid = (Get-AzADGroup -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad group show --group "{name}" --query objectId --output tsv)
```

### <a name="application"></a>Applicazione

Per ottenere l'ID di un'entità servizio (identità usata da un'applicazione), è possibile usare i comandi [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) o [az ad sp list](/cli/azure/ad/sp#az-ad-sp-list). Per un'entità servizio usare l'ID oggetto, **non** l'ID applicazione.

```azurepowershell
$objectid = (Get-AzADServicePrincipal -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad sp list --display-name "{name}" --query [].objectId --output tsv)
```

## <a name="add-a-role-assignment"></a>Aggiungi un'assegnazione di ruolo

Per concedere l'accesso mediante il controllo degli accessi in base al ruolo di Azure, aggiungere un'assegnazione di ruolo.

### <a name="resource-group-without-parameters"></a>Gruppo di risorse (senza parametri)

Il modello seguente mostra un metodo di base per aggiungere un'assegnazione di ruolo. Alcuni valori sono specificati all'interno del modello. Il modello seguente illustra:

-  Come assegnare il ruolo [Lettore](built-in-roles.md#reader) a un utente, un gruppo o un'applicazione nell'ambito di un gruppo di risorse

Per usare il modello, è necessario:

- Creare un nuovo file JSON e copiare il modello
- Sostituire `<your-principal-id>` con l'ID di un utente, un gruppo o un'applicazione a cui assegnare il ruolo

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

Di seguito sono riportati esempi di comandi [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) e [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create) per comprendere come avviare la distribuzione in un gruppo di risorse denominato ExampleGroup.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json
```

L'esempio seguente illustra un'assegnazione del ruolo Lettore a un utente per un gruppo di risorse dopo la distribuzione del modello.

![Assegnare un ruolo nell'ambito di un gruppo di risorse](./media/role-assignments-template/role-assignment-template.png)

### <a name="resource-group-or-subscription"></a>Gruppo di risorse o sottoscrizione

Il modello precedente non è molto flessibile. Il modello seguente usa parametri e può essere usato in ambiti diversi. Il modello seguente illustra:

- Come assegnare un ruolo a un utente, un gruppo o un'applicazione nell'ambito di un gruppo di risorse o di una sottoscrizione
- Come specificare i ruoli proprietario, collaboratore e lettore come parametro

Per usare il modello, è necessario specificare gli input seguenti:

- L'ID di un utente, un gruppo o un'applicazione a cui assegnare il ruolo
- Un ID univoco che verrà usato per l'assegnazione del ruolo. In alternativa, è possibile usare l'ID predefinito

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
> Questo modello non è idempotente, a meno che non venga specificato lo stesso valore di `roleNameGuid` come parametro per ogni distribuzione del modello. Se non viene specificato alcun valore di `roleNameGuid`, per impostazione predefinita viene generato un nuovo GUID per ogni distribuzione e le distribuzioni successive avranno esito negativo con la visualizzazione di un errore `Conflict: RoleAssignmentExists`.

L'ambito dell'assegnazione del ruolo è determinato dal livello della distribuzione. Di seguito sono riportati alcuni esempi di comandi [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) e [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create) per avviare la distribuzione nell'ambito di un gruppo di risorse.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

Di seguito sono riportati alcuni esempi di comandi [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) e [az deployment create](/cli/azure/deployment#az-deployment-create) per avviare la distribuzione nell'ambito di una sottoscrizione e specificare la posizione.

```azurepowershell
New-AzDeployment -Location centralus -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az deployment create --location centralus --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

### <a name="resource"></a>Risorsa

Se è necessario aggiungere un'assegnazione di ruolo al livello di una risorsa, il formato dell'assegnazione di ruolo è diverso. Specificare lo spazio dei nomi del provider di risorse e il tipo della risorsa a cui assegnare il ruolo. È anche possibile includere il nome della risorsa nel nome dell'assegnazione di ruolo.

Per il tipo e il nome dell'assegnazione di ruolo, usare il formato seguente:

```json
"type": "{resource-provider-namespace}/{resource-type}/providers/roleAssignments",
"name": "{resource-name}/Microsoft.Authorization/{role-assign-GUID}"
```

Il modello seguente illustra:

- Come creare un nuovo account di archiviazione
- Come assegnare un ruolo a un utente, un gruppo o un'applicazione nell'ambito dell'account di archiviazione
- Come specificare i ruoli proprietario, collaboratore e lettore come parametro

Per usare il modello, è necessario specificare gli input seguenti:

- L'ID di un utente, un gruppo o un'applicazione a cui assegnare il ruolo

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

Per distribuire il modello precedente, usare i comandi del gruppo di risorse. Di seguito sono riportati alcuni esempi di comandi [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) e [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create) per avviare la distribuzione nell'ambito di una risorsa.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Contributor
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Contributor
```

L'esempio seguente illustra un'assegnazione del ruolo Collaboratore a un utente per un account di archiviazione dopo la distribuzione del modello.

![Assegnazione di ruolo nell'ambito di una risorsa](./media/role-assignments-template/role-assignment-template-resource.png)

### <a name="new-service-principal"></a>Nuova entità servizio

Se si crea una nuova entità servizio e si prova ad assegnare subito un ruolo a tale entità, in alcuni casi l'assegnazione di ruolo può avere esito negativo. Se ad esempio si crea una nuova identità gestita e si prova quindi ad assegnare un ruolo a tale entità nello stesso modello di Azure Resource Manager, l'assegnazione del ruolo potrebbe avere esito negativo. Il motivo di questo errore è probabilmente un ritardo di replica. L'entità servizio viene creata in un'area. L'assegnazione di ruolo potrebbe tuttavia verificarsi in un'area diversa che non ha ancora replicato l'entità servizio. Per far fronte a questo scenario, è necessario impostare la proprietà `principalType` su `ServicePrincipal` quando si crea l'assegnazione di ruolo.

Il modello seguente illustra:

- Come creare una nuova entità servizio dell'identità gestita
- Come specificare la proprietà `principalType`
- Come assegnare il ruolo Collaboratore a tale entità servizio nell'ambito di un gruppo di risorse

Per usare il modello, è necessario specificare gli input seguenti:

- Nome di base dell'identità gestita. In alternativa, è possibile usare la stringa predefinita

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

Di seguito sono riportati alcuni esempi di comandi [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) e [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create) per avviare la distribuzione nell'ambito di un gruppo di risorse.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup2 -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup2 --template-file rbac-test.json
```

L'esempio seguente illustra un'assegnazione del ruolo Collaboratore a un'entità servizio dell'identità gestita dopo la distribuzione del modello.

![Assegnazione di ruolo per una nuova entità servizio dell'identità gestita](./media/role-assignments-template/role-assignment-template-msi.png)

## <a name="remove-a-role-assignment"></a>Rimuovere un'assegnazione di ruolo

Per rimuovere l'accesso a una risorsa di Azure in Controllo degli accessi in base al ruolo di Azure, rimuovere l'assegnazione di ruolo. Non è possibile rimuovere un'assegnazione di ruolo usando un modello. Per rimuovere un'assegnazione di ruolo, è necessario usare altri strumenti, ad esempio:

- [Azure portal](role-assignments-portal.md#remove-a-role-assignment)
- [Azure PowerShell](role-assignments-powershell.md#remove-a-role-assignment)
- [Interfaccia della riga di comando di Azure](role-assignments-cli.md#remove-a-role-assignment)
- [REST API](role-assignments-rest.md#remove-a-role-assignment)

## <a name="next-steps"></a>Passaggi successivi

- [Avvio rapido: Creare e distribuire modelli di Azure Resource Manager con il portale di Azure](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)
- [Comprendere la struttura e la sintassi dei modelli di Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)
- [Creare gruppi di risorse e risorse a livello di sottoscrizione](../azure-resource-manager/templates/deploy-to-subscription.md)
- [Modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/?term=rbac)
