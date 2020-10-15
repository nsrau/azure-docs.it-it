---
title: Distribuire le risorse nel tenant
description: Descrive come distribuire le risorse nell'ambito del tenant in un modello di Azure Resource Manager.
ms.topic: conceptual
ms.date: 09/24/2020
ms.openlocfilehash: 48b3fbcedb119ae699624e79f83297f4ecbc9ede
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91372392"
---
# <a name="create-resources-at-the-tenant-level"></a>Creare risorse a livello di tenant

Con la maturità dell'organizzazione, potrebbe essere necessario definire e assegnare [criteri](../../governance/policy/overview.md) o il [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../../role-based-access-control/overview.md) nel tenant Azure ad. Con i modelli a livello di tenant, è possibile applicare i criteri in modo dichiarativo e assegnare ruoli a livello globale.

## <a name="supported-resources"></a>Risorse supportate

Non tutti i tipi di risorse possono essere distribuiti a livello di tenant. Questa sezione elenca i tipi di risorse supportati.

Per i criteri di Azure, usare:

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)

Per il controllo degli accessi in base al ruolo di Azure (RBAC di Azure), usare:

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)

Per i modelli annidati che si distribuiscono in gruppi di gestione, sottoscrizioni o gruppi di risorse, usare:

* [distribuzioni](/azure/templates/microsoft.resources/deployments)

Per la creazione di gruppi di gestione, usare:

* [managementGroups](/azure/templates/microsoft.management/managementgroups)

Per la gestione dei costi, usare:

* [billingProfiles](/azure/templates/microsoft.billing/billingaccounts/billingprofiles)
* [istruzioni](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/instructions)
* [invoiceSections](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/invoicesections)

## <a name="schema"></a>Schema

Lo schema usato per le distribuzioni a livello di tenant è diverso rispetto allo schema per le distribuzioni di gruppi di risorse.

Per i modelli, usare:

```json
https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#
```

Lo schema per un file di parametri è lo stesso per tutti gli ambiti di distribuzione. Per i file di parametri, usare:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="required-access"></a>Accesso obbligatorio

L'entità di sicurezza che distribuisce il modello deve avere le autorizzazioni per creare le risorse nell'ambito del tenant. L'entità deve disporre dell'autorizzazione per eseguire le azioni di distribuzione (`Microsoft.Resources/deployments/*`) e per creare le risorse definite nel modello. Per creare un gruppo di gestione, ad esempio, l'entità di sicurezza deve disporre dell'autorizzazione come collaboratore nell'ambito del tenant. Per creare assegnazioni di ruolo, l'entità di sicurezza deve disporre dell'autorizzazione come proprietario.

L'amministratore globale di Azure Active Directory non dispone automaticamente dell'autorizzazione per assegnare ruoli. Per abilitare le distribuzioni di modelli nell'ambito del tenant, l'amministratore globale deve seguire questa procedura:

1. Elevare i privilegi di accesso dell'account in modo che l'amministratore globale possa assegnare ruoli. Per altre informazioni, vedere [Elevare i privilegi di accesso per gestire tutte le sottoscrizioni e i gruppi di gestione di Azure](../../role-based-access-control/elevate-access-global-admin.md).

1. Assegnare un proprietario o un collaboratore all'entità di sicurezza che deve distribuire i modelli.

   ```azurepowershell-interactive
   New-AzRoleAssignment -SignInName "[userId]" -Scope "/" -RoleDefinitionName "Owner"
   ```

   ```azurecli-interactive
   az role assignment create --assignee "[userId]" --scope "/" --role "Owner"
   ```

L'entità di sicurezza ha ora le autorizzazioni necessarie per distribuire il modello.

## <a name="deployment-scopes"></a>Ambiti di distribuzione

Quando si esegue la distribuzione in un tenant, è possibile usare come destinazione il tenant o i gruppi di gestione, le sottoscrizioni e i gruppi di risorse nel tenant. L'utente che distribuisce il modello deve avere accesso all'ambito specificato.

Le risorse definite all'interno della sezione Resources del modello vengono applicate al tenant.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-tenant.json" highlight="5":::

Per impostare come destinazione un gruppo di gestione all'interno del tenant, aggiungere una distribuzione annidata e specificare la `scope` Proprietà.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-mg.json" highlight="10,17,22":::

## <a name="deployment-commands"></a>Comandi di distribuzione

I comandi per le distribuzioni a livello di tenant sono diversi rispetto ai comandi per le distribuzioni di gruppi di risorse.

Per l'interfaccia della riga di comando di Azure usare [az deployment tenant create](/cli/azure/deployment/tenant#az-deployment-tenant-create):

```azurecli-interactive
az deployment tenant create \
  --name demoTenantDeployment \
  --location WestUS \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

Per Azure PowerShell usare [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

```azurepowershell-interactive
New-AzTenantDeployment `
  -Name demoTenantDeployment `
  -Location "West US" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

Per l'API REST, usare [Distribuzioni - Creare o aggiornare nell'ambito del tenant](/rest/api/resources/deployments/createorupdateattenantscope).

## <a name="deployment-location-and-name"></a>Percorso e nome della distribuzione

Per le distribuzioni a livello di tenant, è necessario specificare un percorso di distribuzione. Il percorso di distribuzione è separato dal percorso delle risorse distribuite e specifica dove archiviare i dati di distribuzione.

È possibile specificare un nome per la distribuzione oppure usare il nome predefinito. Il nome predefinito è il nome del file modello. Ad esempio, la distribuzione di un modello denominato **azuredeploy.json** crea un nome di distribuzione predefinito di **azuredeploy**.

Per ogni nome di distribuzione il percorso non è modificabile. Non è possibile creare una distribuzione in un percorso se esiste una distribuzione con lo stesso nome in un percorso diverso. Se viene visualizzato il codice di errore `InvalidDeploymentLocation`, utilizzare un nome diverso o lo stesso percorso come la distribuzione precedente per tale nome.

## <a name="use-template-functions"></a>Usare le funzioni di modello

Per le distribuzioni a livello di tenant, esistono alcune considerazioni importanti quando si usano funzioni di modello:

* La funzione [resourceGroup()](template-functions-resource.md#resourcegroup)**non** è supportata.
* La funzione [subscription()](template-functions-resource.md#subscription) **non** è supportata.
* Le funzioni [reference()](template-functions-resource.md#reference) e [list()](template-functions-resource.md#list) sono supportate.
* Non usare [ResourceID ()](template-functions-resource.md#resourceid) per ottenere l'ID risorsa per le risorse distribuite a livello di tenant.

  Usare invece la funzione [tenantResourceId ()](template-functions-resource.md#tenantresourceid) .

  Ad esempio, per ottenere l'ID risorsa per una definizione dei criteri predefinita, usare:

  ```json
  tenantResourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```

  Il formato dell'ID risorsa restituito è il seguente:

  ```json
  /providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-management-group"></a>Creare un gruppo di gestione

Il [modello seguente](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-deployments/new-mg) crea un gruppo di gestione.

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

Il [modello seguente](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-deployments/tenant-role-assignment) assegna un ruolo nell'ambito del tenant.

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

* Per informazioni sull'assegnazione dei ruoli, vedere [aggiungere assegnazioni di ruolo di Azure usando modelli di Azure Resource Manager](../../role-based-access-control/role-assignments-template.md).
* È anche possibile distribuire modelli a [livello di sottoscrizione](deploy-to-subscription.md) e a [livello di gruppo di gestione](deploy-to-management-group.md).
