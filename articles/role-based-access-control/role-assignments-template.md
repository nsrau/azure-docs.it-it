---
title: Gestire l'accesso alle risorse di Azure usando il controllo degli accessi in base al ruolo e i modelli di Azure Resource Manager | Microsoft Docs
description: Informazioni su come gestire l'accesso alle risorse di Azure per utenti, gruppi e applicazioni tramite il controllo degli accessi in base al ruolo e i modelli di Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/20/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 5f57ea658df0569c4e69e476513863abe6940471
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692898"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-resource-manager-templates"></a>Gestire l'accesso alle risorse di Azure usando il controllo degli accessi in base al ruolo e i modelli di Azure Resource Manager

Il [controllo degli accessi in base al ruolo](overview.md) è la modalità di gestione dell'accesso alle risorse di Azure. Oltre a usare Azure PowerShell o l'interfaccia della riga di comando di Azure, è possibile gestire l'accesso alle risorse di Azure usando [modelli Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). I modelli possono essere usati per distribuire le risorse in modo coerente e ripetuto. Questo articolo descrive come è possibile gestire l'accesso tramite RBAC e modelli.

## <a name="create-a-role-assignment-at-a-resource-group-scope-without-parameters"></a>Creare un'assegnazione di ruolo in un ambito del gruppo di risorse (senza parametri)

Per concedere l'accesso mediante il controllo degli accessi in base al ruolo, si crea un'assegnazione di ruolo. Il modello seguente mostra un metodo di base per creare un'assegnazione di ruolo. Alcuni valori sono specificati all'interno del modello. Il modello seguente illustra:

-  Come assegnare il ruolo [Reader](built-in-roles.md#reader) a un utente, a un gruppo o a un'applicazione in un ambito del gruppo di risorse

Per utilizzare il modello, è necessario eseguire le operazioni seguenti:

- Creare un nuovo file JSON e copiare il modello
- Sostituire `<your-principal-id>` con l'identificatore univoco di un utente, un gruppo o un'applicazione a cui assegnare il ruolo. Il formato dell'identificatore è: `11111111-1111-1111-1111-111111111111`

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

Di seguito sono riportati i comandi di esempio [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) e [AZ Group Deployment create](/cli/azure/group/deployment#az-group-deployment-create) per come avviare la distribuzione in un gruppo di risorse denominato ExampleGroup.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json
```

Di seguito viene illustrato un esempio di assegnazione di ruolo Reader a un utente per un gruppo di risorse dopo la distribuzione del modello.

![Assegnazione di ruolo nell'ambito del gruppo di risorse](./media/role-assignments-template/role-assignment-template.png)

## <a name="create-a-role-assignment-at-a-resource-group-or-subscription-scope"></a>Creare un'assegnazione di ruolo in un gruppo di risorse o in un ambito di sottoscrizione

Il modello precedente non è molto flessibile. Il modello seguente usa parametri e può essere usato in ambiti diversi. Il modello seguente illustra:

- Come assegnare un ruolo a un utente, a un gruppo o a un'applicazione nell'ambito di un gruppo di risorse o di una sottoscrizione
- Come specificare i ruoli proprietario, collaboratore e lettore come parametro

Per usare il modello, è necessario specificare gli input seguenti:

- L'identificatore univoco dell'utente, del gruppo o dell'applicazione che è stato assegnato al ruolo
- Il ruolo da assegnare
- Identificatore univoco che verrà utilizzato per l'assegnazione di ruolo oppure è possibile utilizzare l'identificatore predefinito

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

Per ottenere l'identificatore univoco di un utente a cui assegnare il ruolo, è possibile usare i comandi [Get-AzADUser](/powershell/module/az.resources/get-azaduser) o [AZ ad User Show](/cli/azure/ad/user#az-ad-user-show) .

```azurepowershell
$userid = (Get-AzADUser -DisplayName "{name}").id
```

```azurecli
userid=$(az ad user show --upn-or-object-id "{email}" --query objectId --output tsv)
```

L'ambito dell'assegnazione di ruolo è determinato dal livello della distribuzione. Di seguito sono riportati i comandi di esempio [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) e [AZ Group Deployment create](/cli/azure/group/deployment#az-group-deployment-create) per come avviare la distribuzione in un ambito del gruppo di risorse.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $userid -builtInRoleType Reader
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$userid builtInRoleType=Reader
```

Di seguito sono riportati i comandi di esempio [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) e [AZ Deployment create](/cli/azure/deployment#az-deployment-create) per come avviare la distribuzione in un ambito di sottoscrizione e specificare il percorso.

```azurepowershell
New-AzDeployment -Location centralus -TemplateFile rbac-test.json -principalId $userid -builtInRoleType Reader
```

```azurecli
az deployment create --location centralus --template-file rbac-test.json --parameters principalId=$userid builtInRoleType=Reader
```

> [!NOTE]
> Questo modello non è idempotente a meno che non venga specificato lo stesso valore di `roleNameGuid` come parametro per ogni distribuzione del modello. Se non viene specificato alcun `roleNameGuid`, per impostazione predefinita viene generato un nuovo GUID per ogni distribuzione e le distribuzioni successive avranno esito negativo con un errore `Conflict: RoleAssignmentExists`.

## <a name="create-a-role-assignment-at-a-resource-scope"></a>Creare un'assegnazione di ruolo in un ambito di risorsa

Se è necessario creare un'assegnazione di ruolo al livello di una risorsa, il formato dell'assegnazione di ruolo è diverso. Specificare lo spazio dei nomi del provider di risorse e il tipo di risorsa della risorsa a cui assegnare il ruolo. È anche possibile includere il nome della risorsa nel nome dell'assegnazione di ruolo.

Per il tipo e il nome dell'assegnazione di ruolo, usare il formato seguente:

```json
"type": "{resource-provider-namespace}/{resource-type}/providers/roleAssignments",
"name": "{resource-name}/Microsoft.Authorization/{role-assign-GUID}"
```

Il modello seguente illustra:

- Come creare un nuovo account di archiviazione
- Come assegnare un ruolo a un utente, a un gruppo o a un'applicazione nell'ambito dell'account di archiviazione
- Come specificare i ruoli proprietario, collaboratore e lettore come parametro

Per usare il modello, è necessario specificare gli input seguenti:

- L'identificatore univoco dell'utente, del gruppo o dell'applicazione che è stato assegnato al ruolo
- Il ruolo da assegnare

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

Per distribuire il modello precedente, usare i comandi del gruppo di risorse. Di seguito sono riportati i comandi di esempio [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) e [AZ Group Deployment create](/cli/azure/group/deployment#az-group-deployment-create) per la modalità di avvio della distribuzione in un ambito di risorse.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $userid -builtInRoleType Contributor
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$userid builtInRoleType=Contributor
```

Di seguito viene illustrato un esempio di assegnazione di ruolo Collaboratore a un utente per un account di archiviazione dopo la distribuzione del modello.

![Assegnazione di ruolo nell'ambito delle risorse](./media/role-assignments-template/role-assignment-template-resource.png)

## <a name="create-a-role-assignment-for-a-new-service-principal"></a>Creare un'assegnazione di ruolo per una nuova entità servizio

Se si crea una nuova entità servizio e si tenta immediatamente di assegnare un ruolo a tale entità servizio, l'assegnazione di ruolo può non riuscire in alcuni casi. Se, ad esempio, si crea una nuova identità gestita e quindi si tenta di assegnare un ruolo a tale entità servizio nello stesso modello di Azure Resource Manager, l'assegnazione del ruolo potrebbe non riuscire. Il motivo di questo errore è probabilmente un ritardo di replica. L'entità servizio viene creata in un'area; Tuttavia, l'assegnazione di ruolo potrebbe verificarsi in un'area diversa che non ha ancora replicato l'entità servizio. Per risolvere questo scenario, è necessario impostare la proprietà `principalType` su `ServicePrincipal` quando si crea l'assegnazione di ruolo.

Il modello seguente illustra:

- Come creare una nuova entità servizio identità gestita
- Come specificare il `principalType`
- Come assegnare il ruolo Collaboratore a tale entità servizio in un ambito del gruppo di risorse

Per usare il modello, è necessario specificare gli input seguenti:

- Nome di base dell'identità gestita oppure è possibile usare la stringa predefinita

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

Di seguito sono riportati i comandi di esempio [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) e [AZ Group Deployment create](/cli/azure/group/deployment#az-group-deployment-create) per come avviare la distribuzione in un ambito del gruppo di risorse.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup2 -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup2 --template-file rbac-test.json
```

Di seguito viene illustrato un esempio di assegnazione di ruolo Collaboratore a una nuova entità servizio identità gestita dopo la distribuzione del modello.

![Assegnazione di ruolo per una nuova entità servizio identità gestita](./media/role-assignments-template/role-assignment-template-msi.png)

## <a name="next-steps"></a>Passaggi successivi

- [Guida introduttiva: Creare e distribuire modelli di Azure Resource Manager con il portale di Azure](../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)
- [Comprendere la struttura e la sintassi dei modelli di Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)
- [Creare gruppi di risorse e risorse a livello di sottoscrizione](../azure-resource-manager/deploy-to-subscription.md)
- [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/?term=rbac)
