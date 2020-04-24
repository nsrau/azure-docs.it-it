---
title: Distribuire le risorse nel tenant
description: Viene descritto come distribuire le risorse nell'ambito del tenant in un modello di Azure Resource Manager.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: fcdfc5b1c4333a0d7eeec80a09ad85579a1f8b77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460263"
---
# <a name="create-resources-at-the-tenant-level"></a>Creare risorse a livello di tenant

Con la maturità dell'organizzazione, potrebbe essere necessario definire e assegnare i [criteri](../../governance/policy/overview.md) o i [controlli degli accessi in base al ruolo](../../role-based-access-control/overview.md) nel tenant Azure ad. Con i modelli a livello di tenant, è possibile applicare i criteri in modo dichiarativo e assegnare ruoli a livello globale.

## <a name="supported-resources"></a>Risorse supportate

È possibile distribuire i tipi di risorse seguenti a livello di tenant:

* [distribuzioni](/azure/templates/microsoft.resources/deployments) : per i modelli annidati che vengono distribuiti in gruppi di gestione o sottoscrizioni.
* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>SCHEMA

Lo schema usato per le distribuzioni tenant è diverso dallo schema per le distribuzioni di gruppi di risorse.

Per i modelli, usare:

```json
https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#
```

Lo schema per un file di parametri è lo stesso per tutti gli ambiti di distribuzione. Per i file di parametri, usare:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="required-access"></a>Accesso richiesto

L'entità che distribuisce il modello deve avere le autorizzazioni per creare le risorse nell'ambito del tenant. L'entità deve disporre dell'autorizzazione per eseguire le azioni di`Microsoft.Resources/deployments/*`distribuzione () e per creare le risorse definite nel modello. Per creare un gruppo di gestione, ad esempio, l'entità deve disporre dell'autorizzazione Collaboratore nell'ambito del tenant. Per creare assegnazioni di ruolo, l'entità deve disporre dell'autorizzazione Owner.

L'amministratore globale del Azure Active Directory non dispone automaticamente delle autorizzazioni per assegnare i ruoli. Per abilitare le distribuzioni modello nell'ambito del tenant, l'amministratore globale deve eseguire i passaggi seguenti:

1. Elevare l'accesso agli account in modo che l'amministratore globale possa assegnare i ruoli. Per altre informazioni, vedere [elevare l'accesso per gestire tutte le sottoscrizioni e i gruppi di gestione di Azure](../../role-based-access-control/elevate-access-global-admin.md).

1. Assegnare un proprietario o un collaboratore al principale che deve distribuire i modelli.

   ```azurepowershell-interactive
   New-AzRoleAssignment -SignInName "[userId]" -Scope "/" -RoleDefinitionName "Owner"
   ```

   ```azurecli-interactive
   az role assignment create --assignee "[userId]" --scope "/" --role "Owner"
   ```

L'entità dispone ora delle autorizzazioni necessarie per distribuire il modello.

## <a name="deployment-commands"></a>Comandi di distribuzione

I comandi per le distribuzioni tenant sono diversi dai comandi per le distribuzioni di gruppi di risorse.

Per l'interfaccia della riga di comando di Azure, usare [AZ Deployment tenant create](/cli/azure/deployment/tenant?view=azure-cli-latest#az-deployment-tenant-create):

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

Per l'API REST, usare [distribuzioni: creare o aggiornare nell'ambito del tenant](/rest/api/resources/deployments/createorupdateattenantscope).

## <a name="deployment-location-and-name"></a>Percorso e nome della distribuzione

Per le distribuzioni a livello di tenant, è necessario specificare un percorso per la distribuzione. Il percorso della distribuzione è separato dal percorso delle risorse distribuite. Il percorso di distribuzione specifica dove archiviare i dati di distribuzione.

È possibile specificare un nome per la distribuzione oppure utilizzare il nome predefinito della distribuzione. Il nome predefinito è il nome del file modello. Ad esempio, la distribuzione di un modello denominato **azuredeploy.json** crea un nome di distribuzione predefinito di **azuredeploy**.

Per ogni nome di distribuzione, il percorso non è modificabile. Non è possibile creare una distribuzione in un'unica posizione quando esiste una distribuzione esistente con lo stesso nome in una posizione diversa. Se viene visualizzato il codice di errore `InvalidDeploymentLocation`, utilizzare un nome diverso o lo stesso percorso come la distribuzione precedente per tale nome.

## <a name="use-template-functions"></a>Usare le funzioni di modello

Per le distribuzioni tenant, è necessario tenere presenti alcune considerazioni importanti quando si usano le funzioni di modello:

* La funzione [resourceGroup()](template-functions-resource.md#resourcegroup)**non** è supportata.
* La funzione [Subscription ()](template-functions-resource.md#subscription) **non** è supportata.
* Le funzioni [reference()](template-functions-resource.md#reference) e [list()](template-functions-resource.md#list) sono supportate.
* Usare la funzione [tenantResourceId ()](template-functions-resource.md#tenantresourceid) per ottenere l'ID risorsa per le risorse distribuite a livello di tenant.

  Ad esempio, per ottenere l'ID risorsa per una definizione dei criteri, usare:
  
  ```json
  tenantResourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```
  
  Il formato dell'ID risorsa restituito è il seguente:
  
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

Nel [modello seguente](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-level-deployments/tenant-role-assignment) viene assegnato un ruolo nell'ambito del tenant.

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

* Per informazioni sull'assegnazione dei ruoli, vedere [gestire l'accesso alle risorse di Azure usando i modelli RBAC e Azure Resource Manager](../../role-based-access-control/role-assignments-template.md).
* È anche possibile distribuire modelli a livello di [sottoscrizione](deploy-to-subscription.md) o di [gruppo di gestione](deploy-to-management-group.md).
