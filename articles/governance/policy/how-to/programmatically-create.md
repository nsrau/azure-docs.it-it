---
title: Creare criteri a livello di codice e visualizzare i dati di conformità
description: Questo articolo illustra la creazione a livello di codice e la gestione dei criteri per Criteri di Azure.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/31/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: afdd6a238671bf41252eae8b55f1b6e61f358336
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55510827"
---
# <a name="programmatically-create-policies-and-view-compliance-data"></a>Creare criteri a livello di codice e visualizzare i dati di conformità

Questo articolo illustra la creazione a livello di codice e la gestione dei criteri. Le definizioni dei criteri applicano regole ed effetti diversi in relazione alle risorse. L'imposizione consente di assicurare che le risorse rimangano conformi con gli standard aziendali e i contratti di servizio.

Per informazioni sulla conformità, vedere [Ottenere dati sulla conformità](getting-compliance-data.md).

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare che i prerequisiti seguenti siano soddisfatti:

1. Se non è già stato fatto, installare [ARMClient](https://github.com/projectkudu/ARMClient). È uno strumento che invia richieste HTTP alle API basate su Azure Resource Manager.

1. Aggiornare il modulo Azure di PowerShell all'ultima versione. Per informazioni dettagliate, vedere [Installare il modulo di Azure PowerShell](/powershell/azure/install-az-ps). Per altre informazioni sulla versione più recente, vedere [Azure PowerShell](https://github.com/Azure/azure-powershell/releases).

1. Per garantire che la sottoscrizione funzioni con il provider di risorse, registrare il provider di risorse Policy Insights usando Azure PowerShell. Per registrare un provider di risorse, è necessaria l'autorizzazione per eseguire l'operazione /register/action per il provider di risorse. Questa operazione è inclusa nei ruoli Collaboratore e Proprietario. Eseguire il comando seguente per registrare il provider di risorse:

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
   ```

   Per altre informazioni sulla registrazione e la visualizzazione di provider di risorse, vedere [Provider e tipi di risorse](../../../azure-resource-manager/resource-manager-supported-services.md).

1. Installare l'interfaccia della riga di comando di Azure, se non è già installata. È possibile ottenere la versione più recente in [Installare l'interfaccia della riga di comando di Azure in Windows](/cli/azure/install-azure-cli-windows).

## <a name="create-and-assign-a-policy-definition"></a>Creare e assegnare una definizione di criteri

Il primo passo per una migliore visibilità delle risorse consiste nel creare e assegnare i criteri oltre le risorse. Il passaggio successivo illustra come creare e assegnare un criterio a livello di codice. Il criterio di esempio controlla gli account di archiviazione aperti a tutte le reti pubbliche usando PowerShell, l'interfaccia della riga di comando di Azure e le richieste HTTP.

### <a name="create-and-assign-a-policy-definition-with-powershell"></a>Creare e assegnare una definizione di criteri con PowerShell

1. Usare il frammento JSON seguente per creare un file JSON con il nome AuditStorageAccounts.json.

   ```json
   {
       "if": {
           "allOf": [{
                   "field": "type",
                   "equals": "Microsoft.Storage/storageAccounts"
               },
               {
                   "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                   "equals": "Allow"
               }
           ]
       },
       "then": {
           "effect": "audit"
       }
   }
   ```

   Per altre informazioni sulla creazione di una definizione dei criteri, vedere [Struttura delle definizioni di Criteri di Azure](../concepts/definition-structure.md).

1. Eseguire il comando seguente per creare una definizione di criteri usando il file AuditStorageAccounts.json.

   ```azurepowershell-interactive
   New-AzPolicyDefinition -Name 'AuditStorageAccounts' -DisplayName 'Audit Storage Accounts Open to Public Networks' -Policy 'AuditStorageAccounts.json'
   ```

   Il comando crea una definizione di criteri denominata _Audit Storage Accounts Open to Public Networks_.
   Per altre informazioni sui parametri aggiuntivi che è possibile usare, vedere [New-AzPolicyDefinition](/powershell/module/az.resources/new-azpolicydefinition).

   Se chiamato senza parametri per la posizione, `New-AzPolicyDefinition` salva per impostazione predefinita la definizione dei criteri nella sottoscrizione selezionata del contesto di sessioni. Per salvare la definizione in una posizione diversa, usare i parametri seguenti:

   - **SubscriptionId** - Salva in una sottoscrizione diversa. Richiede un valore _GUID_.
   - **ManagementGroupName** - Salva in un gruppo di gestione. Richiede un valore _stringa_.

1. Dopo aver creato la definizione dei criteri, è possibile creare un'assegnazione di criteri eseguendo i comandi seguenti:

   ```azurepowershell-interactive
   $rg = Get-AzResourceGroup -Name 'ContosoRG'
   $Policy = Get-AzPolicyDefinition -Name 'AuditStorageAccounts'
   New-AzPolicyAssignment -Name 'AuditStorageAccounts' -PolicyDefinition $Policy -Scope $rg.ResourceId
   ```

   Sostituire _ContosoRG_ con il nome del gruppo di risorse previsto.

   Il parametro **Scope** in `New-AzPolicyAssignment` funziona anche con le sottoscrizioni e i gruppi di gestione. Il parametro usa un percorso di risorsa completo, restituito dalla proprietà **ResourceId** in `Get-AzResourceGroup`. Il modello per **Scope** per ogni contenitore è il seguente.
   Sostituire `{rName}`, `{rgName}`, `{subId}` e `{mgName}` rispettivamente con il nome della risorsa, il nome del gruppo di risorse, l'ID della sottoscrizione e il nome del gruppo di gestione. `{rType}` può essere sostituito con il **tipo di risorsa** della risorsa, ad esempio `Microsoft.Compute/virtualMachines` per una macchina virtuale.

   - Risorsa - `/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
   - Gruppo di risorse - `/subscriptions/{subId}/resourceGroups/{rgName}`
   - Sottoscrizione - `/subscriptions/{subId}/`
   - Gruppo di gestione - `/providers/Microsoft.Management/managementGroups/{mgName}`

Per altre informazioni sulla gestione dei criteri di risorse con il modulo PowerShell di Azure Resource Manager, vedere [Az.Resources](/powershell/module/az.resources/#policies).

### <a name="create-and-assign-a-policy-definition-using-armclient"></a>Creare e assegnare una definizione dei criteri usando ARMClient

Usare la procedura seguente per creare una definizione dei criteri.

1. Copiare il frammento di codice JSON seguente per creare un file JSON. Il file verrà chiamato nel passaggio successivo.

   ```json
   "properties": {
       "displayName": "Audit Storage Accounts Open to Public Networks",
       "policyType": "Custom",
       "mode": "Indexed",
       "description": "This policy ensures that storage accounts with exposure to Public Networks are audited.",
       "parameters": {},
       "policyRule": {
           "if": {
               "allOf": [{
                       "field": "type",
                       "equals": "Microsoft.Storage/storageAccounts"
                   },
                   {
                       "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                       "equals": "Allow"
                   }
               ]
           },
           "then": {
               "effect": "audit"
           }
       }
   }
   ```

1. Creare la definizione dei criteri usando una delle chiamate seguenti:

   ```
   # For defining a policy in a subscription
   armclient PUT "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>

   # For defining a policy in a management group
   armclient PUT "/providers/Microsoft.Management/managementgroups/{managementGroupId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>
   ```

   Sostituire il precedente {subscriptionId} con l'ID della sottoscrizione o il {managementGroupId} con l'ID del [gruppo di gestione](../../management-groups/overview.md).

   Per altre informazioni sulla struttura della query, vedere [Policy Definitions – Create or Update](/rest/api/resources/policydefinitions/createorupdate) (Definizioni dei criteri: creazione o aggiornamento) e [Policy Definitions – Create or Update At Management Group](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup) (Definizioni dei criteri: creazione o aggiornamento a livello di gruppo di gestione).

Usare la procedura seguente per creare un'assegnazione dei criteri e assegnare la definizione dei criteri a livello di gruppo di risorse.

1. Copiare il frammento di codice JSON seguente per creare un file di assegnazione dei criteri JSON. Sostituire le informazioni di esempio incluse nei simboli &lt;&gt; con i valori desiderati.

   ```json
   {
       "properties": {
           "description": "This policy assignment makes sure that storage accounts with exposure to Public Networks are audited.",
           "displayName": "Audit Storage Accounts Open to Public Networks Assignment",
           "parameters": {},
           "policyDefinitionId": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks",
           "scope": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>"
       }
   }
   ```

1. Creare l'assegnazione dei criteri usando la chiamata seguente:

   ```
   armclient PUT "/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Authorization/policyAssignments/Audit Storage Accounts Open to Public Networks?api-version=2017-06-01-preview" @<path to Assignment JSON file>
   ```

   Sostituire le informazioni di esempio incluse nei simboli &lt;&gt; con i valori desiderati.

   Per altre informazioni su come eseguire le chiamate HTTP all'API REST, vedere [Risorse di API REST di Azure](/rest/api/resources/).

### <a name="create-and-assign-a-policy-definition-with-azure-cli"></a>Creare e assegnare una definizione dei criteri con l'interfaccia della riga di comando di Azure

Per creare una definizione dei criteri, usare la procedura seguente:

1. Copiare il frammento di codice JSON seguente per creare un file di assegnazione dei criteri JSON.

  ```json
  {
      "if": {
          "allOf": [{
                  "field": "type",
                  "equals": "Microsoft.Storage/storageAccounts"
              },
              {
                  "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                  "equals": "Allow"
              }
          ]
      },
      "then": {
          "effect": "audit"
      }
  }
  ```

   Per altre informazioni sulla creazione di una definizione dei criteri, vedere [Struttura delle definizioni di Criteri di Azure](../concepts/definition-structure.md).

1. Per creare una definizione dei criteri, eseguire il comando seguente:

   ```azurecli-interactive
   az policy definition create --name 'audit-storage-accounts-open-to-public-networks' --display-name 'Audit Storage Accounts Open to Public Networks' --description 'This policy ensures that storage accounts with exposures to public networks are audited.' --rules '<path to json file>' --mode All
   ```

   Il comando crea una definizione di criteri denominata _Audit Storage Accounts Open to Public Networks_.
   Per altre informazioni sui parametri aggiuntivi che è possibile usare, vedere [az policy definition create](/cli/azure/policy/definition#az-policy-definition-create).

   Se chiamato senza parametri per la posizione, `az policy definition creation` salva per impostazione predefinita la definizione dei criteri nella sottoscrizione selezionata del contesto di sessioni. Per salvare la definizione in una posizione diversa, usare i parametri seguenti:

   - **--subscription** - Salva in una sottoscrizione diversa. Richiede un valore _GUID_ per l'ID sottoscrizione o un valore _string_ per il nome della sottoscrizione.
   - **--management-group** - Salva in un gruppo di gestione. Richiede un valore _stringa_.

1. Usare questo comando per creare un'assegnazione di criteri. Sostituire le informazioni di esempio incluse nei simboli &lt;&gt; con i valori desiderati.

   ```azurecli-interactive
   az policy assignment create --name '<name>' --scope '<scope>' --policy '<policy definition ID>'
   ```

   Il parametro **--scope** in `az policy assignment create` funziona con gruppo di gestione, sottoscrizione, gruppo di risorse o un'unica risorsa. Il parametro usa un percorso di risorsa completo. Il modello per **--scope** per ogni contenitore è il seguente. Sostituire `{rName}`, `{rgName}`, `{subId}` e `{mgName}` rispettivamente con il nome della risorsa, il nome del gruppo di risorse, l'ID della sottoscrizione e il nome del gruppo di gestione. `{rType}` può essere sostituito con il **tipo di risorsa** della risorsa, ad esempio `Microsoft.Compute/virtualMachines` per una macchina virtuale.

   - Risorsa - `/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
   - Gruppo di risorse - `/subscriptions/{subID}/resourceGroups/{rgName}`
   - Sottoscrizione - `/subscriptions/{subID}`
   - Gruppo di gestione - `/providers/Microsoft.Management/managementGroups/{mgName}`

È possibile ottenere l'ID definizione dei criteri usando PowerShell con il comando seguente:

```azurecli-interactive
az policy definition show --name 'Audit Storage Accounts with Open Public Networks'
```

L'ID definizione dei criteri per la definizione dei criteri creata dovrebbe essere simile al seguente:

```
"/subscription/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks"
```

Per altre informazioni su come gestire i criteri di risorse con l'interfaccia della riga di comando di Azure, vedere [Criteri di risorse dell'interfaccia della riga di comando di Azure](/cli/azure/policy?view=azure-cli-latest).

## <a name="next-steps"></a>Passaggi successivi

Esaminare gli articoli seguenti per altre informazioni sui comandi e sulle query di questo articolo.

- [Risorse di API REST di Azure](/rest/api/resources/)
- [Moduli di Azure PowerShell](/powershell/module/az.resources/#policies)
- [Comandi dei criteri dell'interfaccia della riga di comando di Azure](/cli/azure/policy?view=azure-cli-latest)
- [Policy Insights resource provider REST API reference (Informazioni di riferimento sull'API REST del provider di risorse Policy Insights)](/rest/api/policy-insights)
- [Organizzare le risorse con i gruppi di gestione di Azure ](../../management-groups/overview.md)