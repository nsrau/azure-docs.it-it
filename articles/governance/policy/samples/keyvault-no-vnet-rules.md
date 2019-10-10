---
title: Esempio - Controllare gli insiemi di credenziali delle chiavi per rilevare l'assenza di endpoint di rete virtuale
description: Questa definizione di criteri di esempio controlla gli insiemi di credenziali Key Vault per rilevare le istanze che non hanno endpoint servizio di rete virtuale.
author: DCtheGeek
ms.service: azure-policy
ms.topic: sample
ms.date: 01/26/2019
ms.author: dacoulte
ms.openlocfilehash: ccfba0eae8d3c51cf153f58d554dc09b574d3873
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2019
ms.locfileid: "71976973"
---
# <a name="sample---key-vault-vaults-with-no-virtual-network-endpoints"></a>Esempio - Insiemi di credenziali Key Vault senza endpoint di rete virtuale

Questo criterio controlla gli insiemi di credenziali delle chiavi Key Vault senza endpoint di rete virtuali. Usare per imporre requisiti di sicurezza. Per altre informazioni, vedere [Endpoint servizio di rete virtuale in Key Vault](../../../key-vault/key-vault-overview-vnet-service-endpoints.md)

È possibile distribuire questo criterio di esempio tramite:

- Il[portale di Azure](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Interfaccia della riga di comando di Azure](#azure-cli)
- [API REST](#rest-api)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-policy"></a>Criterio di esempio

### <a name="policy-definition"></a>Definizione di criteri

Definizione JSON completa del criterio, usata dall'API REST, dai pulsanti "Distribuisci in Azure" e manualmente nel portale.

[!code-json[full](../../../../policy-templates/samples/KeyVault/audit-keyvault-vnet-rules/azurepolicy.json "KeyVault vnet rules")]

> [!NOTE]
> Se si crea manualmente un criterio nel portale, usare le sezioni **properties.parameters**  e  **properties.policyRule** dell'esempio precedente. Eseguire il wrapping delle due sezioni con le parentesi graffe `{}` per renderle di formato JSON valido.

### <a name="policy-rules"></a>Regole del criterio

Il codice JSON che definisce le regole del criterio, usato dall'interfaccia della riga di comando di Azure e Azure PowerShell.

[!code-json[rule](../../../../policy-templates/samples/KeyVault/audit-keyvault-vnet-rules/azurepolicy.rules.json "Policy rules (JSON)")]

### <a name="policy-parameters"></a>Parametri dei criteri

Questa definizione di criteri di esempio non ha parametri definiti.

## <a name="azure-portal"></a>Portale di Azure

[![Distribuire l'esempio in Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FKeyVault%2Faudit-keyvault-vnet-rules%2Fazurepolicy.json)
[![Distribuire l'esempio in Azure Gov](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FKeyVault%2Faudit-keyvault-vnet-rules%2Fazurepolicy.json)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

### <a name="deploy-with-azure-powershell"></a>Distribuire con Azure PowerShell

```azurepowershell-interactive
# Create the Policy Definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name "audit-keyvault-vnet-rules" -DisplayName "Audit if Key Vault has no virtual network rules" -description "Audits Key Vault vaults if they do not have virtual network service endpoints set up. More information on virtual network service endpoints in Key Vault is available here: https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints" -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/KeyVault/audit-keyvault-vnet-rules/azurepolicy.rules.json' -Mode Indexed

# Set the scope to a resource group; may also be a subscription or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Create the Policy Assignment
$assignment = New-AzPolicyAssignment -Name 'audit-keyvault-vnet-rules-assignment' -DisplayName 'Audit Key Vault Assignment' -Scope $scope.ResourceId -PolicyDefinition $definition
```

### <a name="remove-with-azure-powershell"></a>Rimuovere con Azure PowerShell

Eseguire questi comandi per rimuovere l'assegnazione e la definizione precedenti:

```azurepowershell-interactive
# Remove the Policy Assignment
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the Policy Definition
Remove-AzPolicyDefinition -Id $definition.ResourceId
```

### <a name="azure-powershell-explanation"></a>Spiegazione di Azure PowerShell

Gli script di distribuzione e rimozione usano i comandi seguenti. Ogni comando della tabella seguente include collegamenti alla documentazione specifica del comando:

| Comando | Note |
|---|---|
| [New-AzPolicyDefinition](/powershell/module/az.resources/New-Azpolicydefinition) | Crea una nuova definizione di Criteri di Azure. |
| [Get-AzResourceGroup](/powershell/module/az.resources/Get-Azresourcegroup) | Ottiene un singolo gruppo di risorse. |
| [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) | Crea una nuova assegnazione di Criteri di Azure. In questo esempio si specifica una definizione, ma può assumere un'iniziativa. |
| [Remove-AzPolicyAssignment](/powershell/module/az.resources/Remove-Azpolicyassignment) | Rimuove un'assegnazione di Criteri di Azure esistente. |
| [Remove-AzPolicyDefinition](/powershell/module/az.resources/Remove-Azpolicydefinition) | Rimuove una definizione di Criteri di Azure esistente. |

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

### <a name="deploy-with-azure-cli"></a>Distribuire con l'interfaccia della riga di comando di Azure

```azurecli-interactive
# Create the Policy Definition (Subscription scope)
definition=$(az policy definition create --name 'audit-keyvault-vnet-rules' --display-name 'Audit if Key Vault has no virtual network rules' --description 'Audits Key Vault vaults if they do not have virtual network service endpoints set up. More information on virtual network service endpoints in Key Vault is available here: https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/KeyVault/audit-keyvault-vnet-rules/azurepolicy.rules.json' --mode Indexed)

# Set the scope to a resource group; may also be a subscription or management group
scope=$(az group show --name 'YourResourceGroup')

# Create the Policy Assignment
assignment=$(az policy assignment create --name 'audit-keyvault-vnet-rules-assignment' --display-name 'Audit Key Vault Assignment' --scope `echo $scope | jq '.id' -r` --policy `echo $definition | jq '.name' -r`)
```

### <a name="remove-with-azure-cli"></a>Rimuovere con l'interfaccia della riga di comando di Azure

Eseguire questi comandi per rimuovere l'assegnazione e la definizione precedenti:

```azurecli-interactive
# Remove the Policy Assignment
az policy assignment delete --name `echo $assignment | jq '.name' -r`

# Remove the Policy Definition
az policy definition delete --name `echo $definition | jq '.name' -r`
```

### <a name="azure-cli-explanation"></a>Spiegazione dell'interfaccia della riga di comando di Azure

| Comando | Note |
|---|---|
| [az policy definition create](/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-create) | Crea una nuova definizione di Criteri di Azure. |
| [az group show](/cli/azure/group?view=azure-cli-latest#az-group-show) | Ottiene un singolo gruppo di risorse. |
| [az policy assignment create](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) | Crea una nuova assegnazione di Criteri di Azure. In questo esempio si specifica una definizione, ma può assumere un'iniziativa. |
| [az policy assignment delete](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-delete) | Rimuove un'assegnazione di Criteri di Azure esistente. |
| [az policy definition delete](/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-delete) | Rimuove una definizione di Criteri di Azure esistente. |

## <a name="rest-api"></a>API REST

Sono disponibili diversi strumenti che possono essere usati per interagire con l'API REST di Resource Manager, ad esempio [ARMClient](https://github.com/projectkudu/ARMClient) o PowerShell.

### <a name="deploy-with-rest-api"></a>Distribuire con l'API REST

- Creare la definizione del criterio (ambito della sottoscrizione). Usare il codice JSON di [definizione del criterio](#policy-definition) per il corpo della richiesta.

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/audit-keyvault-vnet-rules?api-version=2018-05-01
  ```

- Creare l'assegnazione del criterio (ambito del gruppo di risorse)

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/YourResourceGroup/providers/Microsoft.Authorization/policyAssignments/audit-keyvault-vnet-rules-assignment?api-version=2018-05-01
  ```

  Usare questo codice JSON di esempio per il corpo della richiesta:

  ```json
  {
      "properties": {
          "displayName": "Audit Key Vault Assignment",
          "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/audit-keyvault-vnet-rules"
      }
  }
  ```

### <a name="remove-with-rest-api"></a>Rimuovere con l'API REST

- Rimuovere l'assegnazione del criterio

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/audit-keyvault-vnet-rules-assignment?api-version=2018-05-01
  ```

- Rimuovere la definizione del criterio

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/audit-keyvault-vnet-rules?api-version=2018-05-01
  ```

### <a name="rest-api-explanation"></a>Spiegazione dell'API REST

| Service | Gruppo | Operazione | Note |
|---|---|---|---|
| Gestione delle risorse | Definizioni dei criteri | [Creare](/rest/api/resources/policydefinitions/createorupdate) | Crea una nuova definizione di Criteri di Azure a livello della sottoscrizione. Alternativa: [Creare a livello di gruppo di gestione](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup) |
| Gestione delle risorse | Assegnazioni di criteri | [Creare](/rest/api/resources/policyassignments/create) | Crea una nuova assegnazione di Criteri di Azure. In questo esempio si specifica una definizione, ma può assumere un'iniziativa. |
| Gestione delle risorse | Assegnazioni di criteri | [Elimina](/rest/api/resources/policyassignments/delete) | Rimuove un'assegnazione di Criteri di Azure esistente. |
| Gestione delle risorse | Definizioni dei criteri | [Elimina](/rest/api/resources/policydefinitions/delete) | Rimuove una definizione di Criteri di Azure esistente. Alternativa: [Eliminare a livello di gruppo di gestione](/rest/api/resources/policydefinitions/deleteatmanagementgroup) |

## <a name="next-steps"></a>Passaggi successivi

- Vedere altri [esempi di Criteri di Azure](index.md)
- Vedere [Struttura delle definizioni di Criteri di Azure](../concepts/definition-structure.md)