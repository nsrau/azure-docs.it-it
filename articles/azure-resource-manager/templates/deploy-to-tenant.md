---
title: Distribuire le risorse nel tenantDeploy resources to tenant
description: Viene descritto come distribuire le risorse nell'ambito tenant in un modello di Azure Resource Manager.Describes how to deploy resources at the tenant scope in an Azure Resource Manager template.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: fcdfc5b1c4333a0d7eeec80a09ad85579a1f8b77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460263"
---
# <a name="create-resources-at-the-tenant-level"></a>Creare risorse a livello di tenantCreate resources at the tenant level

Man mano che l'organizzazione matura, potrebbe essere necessario definire e assegnare [criteri](../../governance/policy/overview.md) o controlli di [accesso basati](../../role-based-access-control/overview.md) sui ruoli nel tenant di Azure AD. Con i modelli a livello di tenant, è possibile applicare in modo dichiarativo i criteri e assegnare ruoli a livello globale.

## <a name="supported-resources"></a>Risorse supportate

È possibile distribuire i tipi di risorse seguenti a livello di tenant:You can deploy the following resource types at the tenant level:

* [distribuzioni:](/azure/templates/microsoft.resources/deployments) per i modelli annidati distribuiti in gruppi di gestione o sottoscrizioni.Deployments - for nested templates that deploy to management groups or subscriptions.
* [policyAssegnazioni](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [RoleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>SCHEMA

Lo schema usato per le distribuzioni tenant è diverso dallo schema per le distribuzioni di gruppi di risorse.

Per i modelli, utilizzare:

```json
https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#
```

Lo schema per un file di parametri è lo stesso per tutti gli ambiti di distribuzione. Per i file di parametri, utilizzare:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="required-access"></a>Accesso richiesto

L'entità che distribuisce il modello deve disporre delle autorizzazioni per creare risorse nell'ambito tenant. L'entità deve disporre dell'autorizzazione`Microsoft.Resources/deployments/*`per eseguire le azioni di distribuzione ( ) e per creare le risorse definite nel modello. Ad esempio, per creare un gruppo di gestione, l'entità deve disporre dell'autorizzazione Collaboratore nell'ambito tenant. Per creare assegnazioni di ruolo, l'entità deve disporre dell'autorizzazione Proprietario.To create role assignments, the principal must have Owner permission.

L'amministratore globale di Azure Active Directory non dispone automaticamente dell'autorizzazione per assegnare ruoli. Per abilitare le distribuzioni di modelli nell'ambito tenant, l'amministratore globale deve eseguire la procedura seguente:To enable template deployments at the tenant scope, the Global Administrator must do the following steps:

1. Elevare l'accesso all'account in modo che l'amministratore globale possa assegnare ruoli. Per altre informazioni, vedere [Elevare l'accesso per gestire tutte le sottoscrizioni e i gruppi](../../role-based-access-control/elevate-access-global-admin.md)di gestione di Azure.For more information, see Elevate access to manage all Azure subscriptions and management Groups .

1. Assegnare Proprietario o Collaboratore all'entità che deve distribuire i modelli.

   ```azurepowershell-interactive
   New-AzRoleAssignment -SignInName "[userId]" -Scope "/" -RoleDefinitionName "Owner"
   ```

   ```azurecli-interactive
   az role assignment create --assignee "[userId]" --scope "/" --role "Owner"
   ```

L'entità dispone ora delle autorizzazioni necessarie per distribuire il modello.

## <a name="deployment-commands"></a>Comandi di distribuzione

I comandi per le distribuzioni tenant sono diversi dai comandi per le distribuzioni di gruppi di risorse.

Per l'interfaccia della riga di comando di Azure usare [az deployment tenant create:](/cli/azure/deployment/tenant?view=azure-cli-latest#az-deployment-tenant-create)

```azurecli-interactive
az deployment tenant create \
  --name demoTenantDeployment \
  --location WestUS \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-level-deployments/new-mg/azuredeploy.json"
```

Per Azure PowerShell, usare [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

```azurepowershell-interactive
New-AzTenantDeployment `
  -Name demoTenantDeployment `
  -Location "West US" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-level-deployments/new-mg/azuredeploy.json"
```

Per l'API REST, usare [Deployments - Create or Update at Tenant Scope](/rest/api/resources/deployments/createorupdateattenantscope).

## <a name="deployment-location-and-name"></a>Percorso e nome di distribuzione

Per le distribuzioni a livello di tenant, è necessario specificare un percorso per la distribuzione. Il percorso della distribuzione è separato dal percorso delle risorse distribuite. Il percorso di distribuzione specifica dove archiviare i dati di distribuzione.

È possibile specificare un nome per la distribuzione o usare il nome di distribuzione predefinito. Il nome predefinito è il nome del file modello. Ad esempio, la distribuzione di un modello denominato **azuredeploy.json** crea un nome di distribuzione predefinito di **azuredeploy**.

Per ogni nome di distribuzione, il percorso non è modificabile. Non è possibile creare una distribuzione in una posizione quando è presente una distribuzione esistente con lo stesso nome in un percorso diverso. Se viene visualizzato il codice di errore `InvalidDeploymentLocation`, utilizzare un nome diverso o lo stesso percorso come la distribuzione precedente per tale nome.

## <a name="use-template-functions"></a>Usare le funzioni di modello

Per le distribuzioni tenant, esistono alcune considerazioni importanti quando si usano le funzioni del modello:For tenant deployments, there are some important considerations when using template functions:

* La funzione [resourceGroup()](template-functions-resource.md#resourcegroup)**non** è supportata.
* La funzione [subscription()](template-functions-resource.md#subscription) **non** è supportata.
* Le funzioni [reference()](template-functions-resource.md#reference) e [list()](template-functions-resource.md#list) sono supportate.
* Usare la funzione [tenantResourceId()](template-functions-resource.md#tenantresourceid) per ottenere l'ID risorsa per le risorse distribuite a livello di tenant.

  Ad esempio, per ottenere l'ID risorsa per una definizione di criteri, usare:For example, to get the resource ID for a policy definition, use:
  
  ```json
  tenantResourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```
  
  L'ID risorsa restituito ha il seguente formato:
  
  ```json
  /providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-management-group"></a>Creare un gruppo di gestione

Il [modello seguente](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-level-deployments/new-mg) crea un gruppo di gestione.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "mgName": {
      "type": "string",
      "defaultValue": "[concat('mg-', uniqueString(newGuid()))]"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Management/managementGroups",
      "apiVersion": "2019-11-01",
      "name": "[parameters('mgName')]",
      "properties": {
      }
    }
  ]
}
```

## <a name="assign-role"></a>Assegnare un ruolo

Il [modello seguente](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-level-deployments/tenant-role-assignment) assegna un ruolo nell'ambito tenant.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "principalId if the user that will be given contributor access to the resourceGroup"
      }
    },
    "roleDefinitionId": {
      "type": "string",
      "defaultValue": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
      "metadata": {
        "description": "roleDefinition for the assignment - default is owner"
      }
    }
  },
  "variables": {
    // This creates an idempotent guid for the role assignment
    "roleAssignmentName": "[guid('/', parameters('principalId'), parameters('roleDefinitionId'))]"
  },
  "resources": [
    {
      "name": "[variables('roleAssignmentName')]",
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2019-04-01-preview",
      "properties": {
        "roleDefinitionId": "[tenantResourceId('Microsoft.Authorization/roleDefinitions', parameters('roleDefinitionId'))]",
        "principalId": "[parameters('principalId')]",
        "scope": "/"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sull'assegnazione di ruoli, vedere [Gestire l'accesso alle risorse di Azure usando i modelli RBAC e Azure Resource Manager.](../../role-based-access-control/role-assignments-template.md)
* È inoltre possibile distribuire modelli a livello di [sottoscrizione](deploy-to-subscription.md) o di gruppo di [gestione.](deploy-to-management-group.md)
