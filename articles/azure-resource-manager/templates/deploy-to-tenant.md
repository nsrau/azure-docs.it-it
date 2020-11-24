---
title: Distribuire le risorse nel tenant
description: Descrive come distribuire le risorse nell'ambito del tenant in un modello di Azure Resource Manager.
ms.topic: conceptual
ms.date: 11/24/2020
ms.openlocfilehash: 5733c5d6eb6cbd86207589244c22badc17fe7073
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95807642"
---
# <a name="tenant-deployments-with-arm-templates"></a>Distribuzioni tenant con modelli ARM

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

Per la creazione di sottoscrizioni, usare:

* [alias](/azure/templates/microsoft.subscription/aliases)

Per la gestione dei costi, usare:

* [billingProfiles](/azure/templates/microsoft.billing/billingaccounts/billingprofiles)
* [istruzioni](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/instructions)
* [invoiceSections](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/invoicesections)

Per la configurazione del portale, usare:

* [tenantConfigurations](/azure/templates/microsoft.portal/tenantconfigurations)

## <a name="schema"></a>Schema

Lo schema usato per le distribuzioni a livello di tenant è diverso rispetto allo schema per le distribuzioni di gruppi di risorse.

Per i modelli, usare:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
    ...
}
```

Lo schema per un file di parametri è lo stesso per tutti gli ambiti di distribuzione. Per i file di parametri, usare:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    ...
}
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

## <a name="deployment-commands"></a>Comandi di distribuzione

I comandi per le distribuzioni a livello di tenant sono diversi rispetto ai comandi per le distribuzioni di gruppi di risorse.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per l'interfaccia della riga di comando di Azure usare [az deployment tenant create](/cli/azure/deployment/tenant#az-deployment-tenant-create):

```azurecli-interactive
az deployment tenant create \
  --name demoTenantDeployment \
  --location WestUS \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per Azure PowerShell usare [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

```azurepowershell-interactive
New-AzTenantDeployment `
  -Name demoTenantDeployment `
  -Location "West US" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

---

Per informazioni più dettagliate sui comandi e sulle opzioni di distribuzione per la distribuzione di modelli ARM, vedere:

* [Distribuire le risorse con i modelli ARM e portale di Azure](deploy-portal.md)
* [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](deploy-cli.md)
* [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](deploy-powershell.md)
* [Distribuire le risorse con i modelli ARM e Azure Resource Manager API REST](deploy-rest.md)
* [Usare un pulsante di distribuzione per distribuire i modelli dal repository GitHub](deploy-to-azure-button.md)
* [Distribuire modelli ARM da Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-location-and-name"></a>Percorso e nome della distribuzione

Per le distribuzioni a livello di tenant, è necessario specificare un percorso di distribuzione. Il percorso di distribuzione è separato dal percorso delle risorse distribuite e specifica dove archiviare i dati di distribuzione. Le distribuzioni di [sottoscrizioni](deploy-to-subscription.md) e [gruppi di gestione](deploy-to-management-group.md) richiedono anche un percorso. Per le distribuzioni di [gruppi di risorse](deploy-to-resource-group.md) , il percorso del gruppo di risorse viene usato per archiviare i dati di distribuzione.

È possibile specificare un nome per la distribuzione oppure usare il nome predefinito. Il nome predefinito è il nome del file modello. Ad esempio, la distribuzione di un modello denominato **azuredeploy.json** crea un nome di distribuzione predefinito di **azuredeploy**.

Per ogni nome di distribuzione il percorso non è modificabile. Non è possibile creare una distribuzione in un percorso se esiste una distribuzione con lo stesso nome in un percorso diverso. Se, ad esempio, si crea una distribuzione tenant con il nome **Deployment1** in **centralus**, non sarà possibile creare in un secondo momento un'altra distribuzione con il nome **Deployment1** ma un percorso di **westus**. Se viene visualizzato il codice di errore `InvalidDeploymentLocation`, utilizzare un nome diverso o lo stesso percorso come la distribuzione precedente per tale nome.

## <a name="deployment-scopes"></a>Ambiti di distribuzione

Quando si esegue la distribuzione in un tenant, è possibile distribuire le risorse in:

* tenant
* gruppi di gestione all'interno del tenant
* subscriptions
* gruppi di risorse
* [le risorse di estensione](scope-extension-resources.md) possono essere applicate alle risorse

L'utente che distribuisce il modello deve avere accesso all'ambito specificato.

In questa sezione viene illustrato come specificare ambiti diversi. È possibile combinare questi ambiti diversi in un singolo modello.

### <a name="scope-to-tenant"></a>Ambito al tenant

Le risorse definite all'interno della sezione Resources del modello vengono applicate al tenant.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-tenant.json" highlight="5":::

### <a name="scope-to-management-group"></a>Ambito al gruppo di gestione

Per impostare come destinazione un gruppo di gestione all'interno del tenant, aggiungere una distribuzione annidata e specificare la `scope` Proprietà.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-mg.json" highlight="10,17,18,22":::

### <a name="scope-to-subscription"></a>Ambito della sottoscrizione

È anche possibile fare riferimento a sottoscrizioni all'interno del tenant. L'utente che distribuisce il modello deve avere accesso all'ambito specificato.

Per fare riferimento a una sottoscrizione all'interno del tenant, usare una distribuzione annidata e la `subscriptionId` Proprietà.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-subscription.json" highlight="9,10,18":::

### <a name="scope-to-resource-group"></a>Ambito al gruppo di risorse

È anche possibile fare riferimento ai gruppi di risorse all'interno del tenant. L'utente che distribuisce il modello deve avere accesso all'ambito specificato.

Per fare riferimento a un gruppo di risorse all'interno del tenant, usare una distribuzione nidificata. Impostare le proprietà `subscriptionId` e `resourceGroup`. Non impostare un percorso per la distribuzione annidata perché è distribuito nella posizione del gruppo di risorse.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-rg.json" highlight="9,10,18":::

## <a name="create-management-group"></a>Creare un gruppo di gestione

Il modello seguente crea un gruppo di gestione.

:::code language="json" source="~/quickstart-templates/tenant-deployments/new-mg/azuredeploy.json":::

## <a name="assign-role"></a>Assegnare il ruolo

Il modello seguente assegna un ruolo nell'ambito del tenant.

:::code language="json" source="~/quickstart-templates/tenant-deployments/tenant-role-assignment/azuredeploy.json":::

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sull'assegnazione dei ruoli, vedere [aggiungere assegnazioni di ruolo di Azure usando modelli di Azure Resource Manager](../../role-based-access-control/role-assignments-template.md).
* È anche possibile distribuire modelli a [livello di sottoscrizione](deploy-to-subscription.md) e a [livello di gruppo di gestione](deploy-to-management-group.md).
