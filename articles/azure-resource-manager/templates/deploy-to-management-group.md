---
title: Distribuire le risorse al gruppo di gestione
description: Viene descritto come distribuire le risorse nell'ambito del gruppo di gestione in un modello di Azure Resource Manager.
ms.topic: conceptual
ms.date: 03/02/2020
ms.openlocfilehash: 3b2eeaf2c63a50cda1a32fee94c1e5b99822075d
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2020
ms.locfileid: "78228100"
---
# <a name="create-resources-at-the-management-group-level"></a>Creazione di risorse a livello di gruppo di gestione

In genere, le risorse di Azure vengono distribuite a un gruppo nell'ambito della sottoscrizione di Azure. Tuttavia, è anche possibile creare risorse a livello di gruppo di gestione. Si utilizzano le distribuzioni a livello di gruppo di gestione per eseguire azioni che hanno senso a tale livello, ad esempio l'assegnazione del [controllo degli accessi in base al ruolo o l'](../../role-based-access-control/overview.md) applicazione di [criteri](../../governance/policy/overview.md).

## <a name="supported-resources"></a>Risorse supportate

È possibile distribuire i tipi di risorse seguenti a livello di gruppo di gestione:

* [distribuzioni](/azure/templates/microsoft.resources/deployments)
* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>Schema

Lo schema usato per le distribuzioni di gruppi di gestione è diverso dallo schema per le distribuzioni di gruppi di risorse.

Per i modelli, usare:

```json
https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#
```

Per i file di parametri, usare:

```json
https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentParameters.json#
```

## <a name="deployment-commands"></a>Comandi di distribuzione

I comandi per le distribuzioni di gruppi di gestione sono diversi da quelli per le distribuzioni di gruppi di risorse.

Per Azure PowerShell, usare [New-AzManagementGroupDeployment](/powershell/module/az.resources/new-azmanagementgroupdeployment). 

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -ManagementGroupId "myMG" `
  -Location "West US" `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json
```

Per l'API REST, usare [distribuzioni-crea nell'ambito del gruppo di gestione](/rest/api/resources/deployments/createorupdateatmanagementgroupscope).

## <a name="deployment-location-and-name"></a>Percorso e nome della distribuzione

Per le distribuzioni a livello di gruppo di gestione, è necessario specificare un percorso per la distribuzione. Il percorso della distribuzione è separato dal percorso delle risorse distribuite. Il percorso di distribuzione specifica dove archiviare i dati di distribuzione.

È possibile specificare un nome per la distribuzione oppure utilizzare il nome predefinito della distribuzione. Il nome predefinito è il nome del file modello. Ad esempio, la distribuzione di un modello denominato **azuredeploy.json** crea un nome di distribuzione predefinito di **azuredeploy**.

Per ogni nome di distribuzione, il percorso non è modificabile. Non è possibile creare una distribuzione in un'unica posizione quando esiste una distribuzione esistente con lo stesso nome in una posizione diversa. Se viene visualizzato il codice di errore `InvalidDeploymentLocation`, utilizzare un nome diverso o lo stesso percorso come la distribuzione precedente per tale nome.

## <a name="use-template-functions"></a>Usare le funzioni di modello

Per le distribuzioni di gruppi di gestione, è necessario tenere presenti alcune considerazioni importanti relative all'utilizzo delle funzioni modello:

* La funzione [resourceGroup()](template-functions-resource.md#resourcegroup)**non** è supportata.
* La funzione [Subscription ()](template-functions-resource.md#subscription) **non** è supportata.
* Le funzioni [reference()](template-functions-resource.md#reference) e [list()](template-functions-resource.md#list) sono supportate.
* La funzione [resourceId()](template-functions-resource.md#resourceid) funzione è supportata. Usarlo per ottenere l'ID risorsa per le risorse usate nelle distribuzioni a livello di gruppo di gestione. Non specificare un valore per il parametro del gruppo di risorse.

  Ad esempio, per ottenere l'ID risorsa per una definizione dei criteri, usare:
  
  ```json
  resourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```
  
  Il formato dell'ID risorsa restituito è il seguente:
  
  ```json
  /providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-policies"></a>Creare criteri

### <a name="define-policy"></a>Definire i criteri

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

Nell'esempio seguente viene assegnata una definizione dei criteri esistente al gruppo di gestione. Se i criteri accettano parametri, specificarli come oggetto. Se non accettano parametri, usare l'oggetto vuoto predefinito.

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

* [Creare un gruppo di risorse, un criterio e un'assegnazione di criteri](https://github.com/Azure/azure-docs-json-samples/blob/master/management-level-deployment/azuredeploy.json).

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sull'assegnazione dei ruoli, vedere [gestire l'accesso alle risorse di Azure usando i modelli RBAC e Azure Resource Manager](../../role-based-access-control/role-assignments-template.md).
* Per un esempio di distribuzione delle impostazioni dell'area di lavoro per il Centro sicurezza di Azure, vedere [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Per informazioni sulla creazione di modelli di Gestione risorse di Azure, vedere [Creazione di modelli](template-syntax.md).
* Per un elenco delle funzioni disponibili in un modello, vedere [Funzioni di modelli](template-functions.md).