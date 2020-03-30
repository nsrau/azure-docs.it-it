---
title: Distribuire le risorse al gruppo di gestioneDeploy resources to management group
description: Viene descritto come distribuire le risorse nell'ambito del gruppo di gestione in un modello di Azure Resource Manager.Describes how to deploy resources at the management group scope in an Azure Resource Manager template.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 863d1330412fa238b820eb0f1f05351fc723de6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460314"
---
# <a name="create-resources-at-the-management-group-level"></a>Creare risorse a livello di gruppo di gestioneCreate resources at the management group level

Man mano che l'organizzazione matura, potrebbe essere necessario definire e assegnare [criteri](../../governance/policy/overview.md) o controlli di [accesso basati](../../role-based-access-control/overview.md) sui ruoli per un gruppo di gestione. Con i modelli a livello di gruppo di gestione, è possibile applicare in modo dichiarativo i criteri e assegnare ruoli a livello di gruppo di gestione.

## <a name="supported-resources"></a>Risorse supportate

È possibile distribuire i tipi di risorse seguenti a livello di gruppo di gestione:You can deploy the following resource types at the management group level:

* [distribuzioni:](/azure/templates/microsoft.resources/deployments) per i modelli annidati che vengono distribuiti in sottoscrizioni o gruppi di risorse.Deployments - for nested templates that deploy to subscriptions or resource groups.
* [policyAssegnazioni](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [RoleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>SCHEMA

Lo schema usato per le distribuzioni dei gruppi di gestione è diverso dallo schema per le distribuzioni di gruppi di risorse.

Per i modelli, utilizzare:

```json
https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#
```

Lo schema per un file di parametri è lo stesso per tutti gli ambiti di distribuzione. Per i file di parametri, utilizzare:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="deployment-commands"></a>Comandi di distribuzione

I comandi per le distribuzioni dei gruppi di gestione sono diversi dai comandi per le distribuzioni di gruppi di risorse.

Per l'interfaccia della riga di comando di Azure, usare [az deployment mg create:](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-create)

```azurecli-interactive
az deployment mg create \
  --name demoMGDeployment \
  --location WestUS \
  --management-group-id myMG \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

Per Azure PowerShell, usare [New-AzManagementGroupDeployment](/powershell/module/az.resources/new-azmanagementgroupdeployment).

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name demoMGDeployment `
  -Location "West US" `
  -ManagementGroupId "myMG" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

Per l'API REST, usare [Distribuzioni - Crea in ambito del gruppo](/rest/api/resources/deployments/createorupdateatmanagementgroupscope)di gestione .

## <a name="deployment-location-and-name"></a>Percorso e nome di distribuzione

Per le distribuzioni a livello di gruppo di gestione, è necessario specificare un percorso per la distribuzione. Il percorso della distribuzione è separato dal percorso delle risorse distribuite. Il percorso di distribuzione specifica dove archiviare i dati di distribuzione.

È possibile specificare un nome per la distribuzione o usare il nome di distribuzione predefinito. Il nome predefinito è il nome del file modello. Ad esempio, la distribuzione di un modello denominato **azuredeploy.json** crea un nome di distribuzione predefinito di **azuredeploy**.

Per ogni nome di distribuzione, il percorso non è modificabile. Non è possibile creare una distribuzione in una posizione quando è presente una distribuzione esistente con lo stesso nome in un percorso diverso. Se viene visualizzato il codice di errore `InvalidDeploymentLocation`, utilizzare un nome diverso o lo stesso percorso come la distribuzione precedente per tale nome.

## <a name="use-template-functions"></a>Usare le funzioni di modello

Per le distribuzioni dei gruppi di gestione, esistono alcune considerazioni importanti quando si usano le funzioni del modello:For management group deployments, there are some important considerations when using template functions:

* La funzione [resourceGroup()](template-functions-resource.md#resourcegroup)**non** è supportata.
* La funzione [subscription()](template-functions-resource.md#subscription) **non** è supportata.
* Le funzioni [reference()](template-functions-resource.md#reference) e [list()](template-functions-resource.md#list) sono supportate.
* La funzione [resourceId()](template-functions-resource.md#resourceid) funzione è supportata. Utilizzarlo per ottenere l'ID risorsa per le risorse utilizzate nelle distribuzioni a livello di gruppo di gestione. Non fornire un valore per il parametro del gruppo di risorse.

  Ad esempio, per ottenere l'ID risorsa per una definizione di criteri, usare:For example, to get the resource ID for a policy definition, use:
  
  ```json
  resourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```
  
  L'ID risorsa restituito ha il seguente formato:
  
  ```json
  /providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-policies"></a>Creare criteri

### <a name="define-policy"></a>Definire i criteriDefine policy

Nell'esempio seguente viene illustrato come [definire](../../governance/policy/concepts/definition-structure.md) un criterio a livello di gruppo di gestione.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Authorization/policyDefinitions",
      "apiVersion": "2018-05-01",
      "name": "locationpolicy",
      "properties": {
        "policyType": "Custom",
        "parameters": {},
        "policyRule": {
          "if": {
            "field": "location",
            "equals": "northeurope"
          },
          "then": {
            "effect": "deny"
          }
        }
      }
    }
  ]
}
```

### <a name="assign-policy"></a>Assegnare un criterio

Nell'esempio seguente viene assegnata una definizione di criteri esistente al gruppo di gestione. Se i criteri accettano parametri, specificarli come oggetto. Se non accettano parametri, usare l'oggetto vuoto predefinito.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "policyDefinitionID": {
      "type": "string"
    },
    "policyName": {
      "type": "string"
    },
    "policyParameters": {
      "type": "object",
      "defaultValue": {}
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Authorization/policyAssignments",
      "apiVersion": "2018-03-01",
      "name": "[parameters('policyName')]",
      "properties": {
        "policyDefinitionId": "[parameters('policyDefinitionID')]",
        "parameters": "[parameters('policyParameters')]"
      }
    }
  ]
}
```

## <a name="template-sample"></a>Esempio di modello

* Creare un gruppo di [risorse, un criterio e un'assegnazione di criteri.](https://github.com/Azure/azure-docs-json-samples/blob/master/management-level-deployment/azuredeploy.json)

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sull'assegnazione di ruoli, vedere [Gestire l'accesso alle risorse di Azure usando i modelli RBAC e Azure Resource Manager.](../../role-based-access-control/role-assignments-template.md)
* Per un esempio di distribuzione delle impostazioni dell'area di lavoro per il Centro sicurezza di Azure, vedere [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* È inoltre possibile distribuire modelli a livello di [sottoscrizione](deploy-to-subscription.md) e a livello di [tenant.](deploy-to-tenant.md)
