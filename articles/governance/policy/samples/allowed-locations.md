---
title: Esempio - Posizioni consentite
description: Questo criterio di esempio richiede che tutte le risorse vengano distribuite nelle località approvate.
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 01/23/2019
ms.author: dacoulte
ms.openlocfilehash: 18111e752a77c74cb1f634e0a800fabb79b468b2
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2019
ms.locfileid: "54848700"
---
# <a name="allowed-locations"></a>Percorsi consentiti

Questi criteri consentono di limitare le posizioni che l'organizzazione può specificare durante la distribuzione delle risorse. Usare per imporre requisiti di conformità geografica. Esclude i gruppi di risorse, Microsoft.AzureActiveDirectory/b2cDirectories e le risorse che usano l'area "global". Si specifica una matrice di posizioni consentite.

È possibile distribuire questo criterio di esempio tramite:

- Il[portale di Azure](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Interfaccia della riga di comando di Azure](#azure-cli)
- [API REST](#rest-api)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-policy"></a>Criterio di esempio

### <a name="policy-definition"></a>Definizione di criteri

Definizione JSON completa del criterio, usata dall'API REST, dai pulsanti "Distribuisci in Azure" e manualmente nel portale.

[!code-json[full](../../../../policy-templates/samples/built-in-policy/allowed-locations/azurepolicy.json "Allowed locations")]

> [!NOTE]
> Se si crea manualmente un criterio nel portale, usare le sezioni **properties.parameters**  e  **properties.policyRule** dell'esempio precedente. Eseguire il wrapping delle due sezioni con le parentesi graffe `{}` per renderle di formato JSON valido.

### <a name="policy-rules"></a>Regole del criterio

Il codice JSON che definisce le regole del criterio, usato dall'interfaccia della riga di comando di Azure e Azure PowerShell.

[!code-json[rule](../../../../policy-templates/samples/built-in-policy/allowed-locations/azurepolicy.rules.json "Policy rules (JSON)")]

### <a name="policy-parameters"></a>Parametri dei criteri

Il codice JSON che definisce i parametri del criterio, usato dall'interfaccia della riga di comando di Azure e Azure PowerShell.

[!code-json[parameters](../../../../policy-templates/samples/built-in-policy/allowed-locations/azurepolicy.parameters.json "Policy parameters (JSON)")]

## <a name="parameters"></a>Parametri

|NOME |type |Campo |DESCRIZIONE |
|---|---|---|---|
|listOfAllowedLocations |Array |locations |Lista di posizioni consentite|

Quando si crea un'assegnazione tramite PowerShell o l'interfaccia della riga di comando di Azure, i valori dei parametri possono essere passati in formato JSON in una stringa o tramite un file usando `-PolicyParameter` (PowerShell) o `--params` (interfaccia della riga di comando di Azure).
PowerShell supporta anche `-PolicyParameterObject`, che richiede di passare al cmdlet una tabella hash nome/valore il cmdlet in cui il **nome** è il nome del parametro e il **valore** è il valore singolo o la matrice di valori passati durante assegnazione.

In questo parametro di esempio, saranno consentite solo le posizioni _eastus2_ o _westus_.

```json
{
    "listOfAllowedLocations": {
        "value": [
            "eastus2",
            "westus"
        ]
    }
}
```

## <a name="azure-portal"></a>Portale di Azure

[![Distribuire in Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2Fbuilt-in-policy%2Fallowed-locations%2Fazurepolicy.json)
[![Distribuire in Azure per enti pubblici](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2Fbuilt-in-policy%2Fallowed-locations%2Fazurepolicy.json)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh.md)]

### <a name="deploy-with-azure-powershell"></a>Distribuire con Azure PowerShell

```azurepowershell-interactive
# Create the Policy Definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name "allowed-locations" -DisplayName "Allowed locations" -description "This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements. Excludes resource groups, Microsoft.AzureActiveDirectory/b2cDirectories, and resources that use the 'global' region." -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/allowed-locations/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/allowed-locations/azurepolicy.parameters.json' -Mode Indexed

# Set the scope to a resource group; may also be a subscription or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Set the Policy Parameter (JSON format)
$policyparam = '{ "listOfAllowedLocations": { "value": [ "eastus2", "westus" ] } }'

# Create the Policy Assignment
$assignment = New-AzPolicyAssignment -Name 'allowed-locations-assignment' -DisplayName 'Allowed locations Assignment' -Scope $scope.ResourceId -PolicyDefinition $definition -PolicyParameter $policyparam
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
definition=$(az policy definition create --name 'allowed-locations' --display-name 'Allowed locations' --description 'This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements. Excludes resource groups, Microsoft.AzureActiveDirectory/b2cDirectories, and resources that use the 'global' region.' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/allowed-locations/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/allowed-locations/azurepolicy.parameters.json' --mode Indexed)

# Set the scope to a resource group; may also be a subscription or management group
scope=$(az group show --name 'YourResourceGroup')

# Set the Policy Parameter (JSON format)
policyparam='{ "listOfAllowedLocations": { "value": [ "eastus2", "westus" ] } }'

# Create the Policy Assignment
assignment=$(az policy assignment create --name 'allowed-locations-assignment' --display-name 'Allowed locations Assignment' --scope `echo $scope | jq '.id' -r` --policy `echo $definition | jq '.name' -r` --params "$policyparam")
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
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/allowed-locations?api-version=2018-05-01
  ```

- Creare l'assegnazione del criterio (ambito del gruppo di risorse)

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/YourResourceGroup/providers/Microsoft.Authorization/policyAssignments/allowed-locations-assignment?api-version=2018-05-01
  ```

  Usare questo codice JSON di esempio per il corpo della richiesta:

  ```json
  {
      "properties": {
          "displayName": "Allowed locations Assignment",
          "policyDefinitionId": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/allowed-locations",
          "parameters": {
              "listOfAllowedLocations": {
                  "value": [
                      "eastus2",
                      "westus"
                  ]
              }
          }
      }
  }
  ```

### <a name="remove-with-rest-api"></a>Rimuovere con l'API REST

- Rimuovere l'assegnazione del criterio

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/allowed-locations-assignment?api-version=2018-05-01
  ```

- Rimuovere la definizione del criterio

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/allowed-locations?api-version=2018-05-01
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