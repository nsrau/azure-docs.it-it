---
title: Distribuire le risorse nel tenant
description: Viene descritto come distribuire le risorse nell'ambito del tenant in un modello di Azure Resource Manager.
ms.topic: conceptual
ms.date: 03/06/2020
ms.openlocfilehash: d63697f3c140b5ad374607f1ecb00dad20e697de
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2020
ms.locfileid: "78899139"
---
# <a name="create-resources-at-the-tenant-level"></a>Creare risorse a livello di tenant

In genere, le risorse di Azure vengono distribuite a un gruppo nell'ambito della sottoscrizione di Azure. Tuttavia, è anche possibile creare risorse in:

* [livello di sottoscrizione](deploy-to-subscription.md)
* [livello gruppo di gestione](deploy-to-management-group.md)
* livello del tenant (trattato in questo articolo)

Si usano le distribuzioni a livello di tenant per eseguire azioni che hanno senso a tale livello, ad esempio l'assegnazione del [controllo degli accessi in base al ruolo o l'](../../role-based-access-control/overview.md) applicazione di [criteri](../../governance/policy/overview.md).

## <a name="supported-resources"></a>Risorse supportate

È possibile distribuire i tipi di risorse seguenti a livello di tenant:

* [distribuzioni](/azure/templates/microsoft.resources/deployments) : per i modelli annidati che vengono distribuiti in gruppi di gestione o sottoscrizioni.
* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>Schema

Lo schema usato per le distribuzioni tenant è diverso dallo schema per le distribuzioni di gruppi di risorse.

Per i modelli, usare:

```json
https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#
```

Per i file di parametri, usare:

```json
https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentParameters.json#
```

## <a name="required-access"></a>Accesso richiesto

L'entità che distribuisce il modello deve avere le autorizzazioni per creare le risorse nell'ambito del tenant. L'entità deve disporre dell'autorizzazione per eseguire le azioni di distribuzione (`Microsoft.Resources/deployments/*`) e per creare le risorse definite nel modello. Per creare un gruppo di gestione, ad esempio, l'entità deve disporre dell'autorizzazione Collaboratore nell'ambito del tenant. Per creare assegnazioni di ruolo, l'entità deve disporre dell'autorizzazione Owner.

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

Per Azure PowerShell, usare [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

```azurepowershell-interactive
New-AzTenantDeployment `
  -Location "West US" `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-level-deployments/new-mg/azuredeploy.json
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
* Per informazioni sulla creazione di modelli di Gestione risorse di Azure, vedere [Creazione di modelli](template-syntax.md).
* Per un elenco delle funzioni disponibili in un modello, vedere [Funzioni di modelli](template-functions.md).