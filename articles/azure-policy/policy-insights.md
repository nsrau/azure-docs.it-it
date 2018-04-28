---
title: Creare criteri a livello di codice e visualizzare i dati di conformità con Criteri di Azure | Microsoft Docs
description: Questo articolo illustra la creazione a livello di codice e la gestione dei criteri per Criteri di Azure.
services: azure-policy
keywords: ''
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/28/2018
ms.topic: article
ms.service: azure-policy
manager: carmonm
ms.custom: ''
ms.openlocfilehash: bd0dbb1b6b44b34fc86b8c73fa586b1b4cf880f3
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2018
---
# <a name="programmatically-create-policies-and-view-compliance-data"></a>Creare criteri a livello di codice e visualizzare i dati di conformità

Questo articolo illustra la creazione a livello di codice e la gestione dei criteri. Illustra anche come visualizzare gli stati di conformità delle risorse e i criteri. Le definizioni dei criteri applicano regole e azioni diverse in relazione alle risorse. L'imposizione consente di assicurare che le risorse rimangano conformi con gli standard aziendali e i contratti di servizio.

## <a name="prerequisites"></a>prerequisiti

Prima di iniziare, verificare che i prerequisiti seguenti siano soddisfatti:

1. Se non è già stato fatto, installare [ARMClient](https://github.com/projectkudu/ARMClient). È uno strumento che invia richieste HTTP alle API basate su Azure Resource Manager.
2. Aggiornare il modulo AzureRM di PowerShell all'ultima versione. Per altre informazioni sulla versione più recente, vedere Azure PowerShell https://github.com/Azure/azure-powershell/releases.
3. Per garantire che la sottoscrizione funzioni con il provider di risorse, registrare il provider di risorse Policy Insights usando Azure PowerShell. Per registrare un provider di risorse, è necessaria l'autorizzazione per eseguire l'operazione /register/action per il provider di risorse. Questa operazione è inclusa nei ruoli Collaboratore e Proprietario. Eseguire il comando seguente per registrare il provider di risorse:

  ```azurepowershell-interactive
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.PolicyInsights
  ```

  Per altre informazioni sulla registrazione e la visualizzazione di provider di risorse, vedere [Provider e tipi di risorse](../azure-resource-manager/resource-manager-supported-services.md).
4. Installare l'interfaccia della riga di comando di Azure, se non è già installata. È possibile ottenere la versione più recente in [Installare l'interfaccia della riga di comando di Azure 2.0 in Windows](/azure/install-azure-cli-windows?view=azure-cli-latest).

## <a name="create-and-assign-a-policy-definition"></a>Creare e assegnare una definizione di criteri

Il primo passo per una migliore visibilità delle risorse consiste nel creare e assegnare i criteri oltre le risorse. Il passaggio successivo illustra come creare e assegnare un criterio a livello di codice. Il criterio di esempio controlla gli account di archiviazione aperti a tutte le reti pubbliche usando PowerShell, l'interfaccia della riga di comando di Azure e le richieste HTTP.

I comandi seguenti creano le definizioni dei criteri per il livello Standard. Il livello Standard offre funzionalità di gestione, valutazione della conformità e correzione su vasta scala. Per altre informazioni sui piani tariffari, vedere [Prezzi di Criteri di Azure](https://azure.microsoft.com/pricing/details/azure-policy).

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

  Per altre informazioni sulla creazione di una definizione dei criteri, vedere [Struttura delle definizioni di Criteri di Azure](policy-definition.md).
2. Eseguire il comando seguente per creare una definizione di criteri usando il file AuditStorageAccounts.json.

  ```azurepowershell-interactive
  New-AzureRmPolicyDefinition -Name 'AuditStorageAccounts' -DisplayName 'Audit Storage Accounts Open to Public Networks' -Policy AuditStorageAccounts.json
  ```

  Il comando crea una definizione di criteri denominata _Audit Storage Accounts Open to Public Networks_. Per altre informazioni sui parametri aggiuntivi che è possibile usare, vedere [New-AzureRmPolicyDefinition](/powershell/module/azurerm.resources/new-azurermpolicydefinition).
3. Dopo aver creato la definizione dei criteri, è possibile creare un'assegnazione di criteri eseguendo i comandi seguenti:

  ```azurepowershell-interactive
  $rg = Get-AzureRmResourceGroup -Name 'ContosoRG'

  $Policy = Get-AzureRmPolicyDefinition -Name 'AuditStorageAccounts'

  New-AzureRmPolicyAssignment -Name 'AuditStorageAccounts' -PolicyDefinition $Policy -Scope $rg.ResourceId –Sku @{Name='A1';Tier='Standard'}
  ```

  Sostituire _ContosoRG_ con il nome del gruppo di risorse previsto.

Per ulteriori informazioni sulla gestione dei criteri di risorse con il modulo PowerShell di Azure Resource Manager, vedere [AzureRM.Resources](/powershell/module/azurerm.resources/#policies).

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

2. Creare la definizione dei criteri usando la chiamata seguente:

  ```
  armclient PUT "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>
  ```

  Sostituire il precedente &lt;subscriptionId&gt; con l'ID della sottoscrizione prevista.

Per altre informazioni sulla struttura della query, vedere [Definizioni dei criteri: creazione o aggiornamento](/rest/api/resources/policydefinitions/createorupdate).

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
      },
      "sku": {
          "name": "A1",
          "tier": "Standard"
      }
  }
  ```

2. Creare l'assegnazione dei criteri usando la chiamata seguente:

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

2. Per creare una definizione dei criteri, eseguire il comando seguente:

  ```azurecli-interactive
az policy definition create --name 'audit-storage-accounts-open-to-public-networks' --display-name 'Audit Storage Accounts Open to Public Networks' --description 'This policy ensures that storage accounts with exposures to public networks are audited.' --rules '<path to json file>' --mode All
  ```

3. Usare questo comando per creare un'assegnazione di criteri. Sostituire le informazioni di esempio incluse nei simboli &lt;&gt; con i valori desiderati.

  ```azurecli-interactive
  az policy assignment create --name '<name>' --scope '<scope>' --policy '<policy definition ID>' --sku 'standard'
  ```

È possibile ottenere l'ID definizione dei criteri usando PowerShell con il comando seguente:

```azurecli-interactive
az policy definition show --name 'Audit Storage Accounts with Open Public Networks'
```

L'ID definizione dei criteri per la definizione dei criteri creata dovrebbe essere simile al seguente:

```
"/subscription/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks"
```

Per altre informazioni su come gestire i criteri di risorse con l'interfaccia della riga di comando di Azure, vedere [Criteri di risorse dell'interfaccia della riga di comando di Azure](/cli/azure/policy?view=azure-cli-latest).

## <a name="identify-non-compliant-resources"></a>Identificare le risorse non conformi

In un'assegnazione una risorsa non è conforme se non segue i criteri o le regole delle iniziative. La tabella seguente illustra il funzionamento delle diverse azioni dei criteri in base alla valutazione della condizione per lo stato di conformità risultante:

| **Stato della risorsa** | **Azione** | **Valutazione dei criteri** | **Stato di conformità** |
| --- | --- | --- | --- |
| Exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | True  | Non conforme |
| Exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | False | Conforme |
| Nuovo | Audit, AuditIfNotExist\* | True  | Non conforme |
| Nuovo | Audit, AuditIfNotExist\* | False | Conforme |

\* Per le azioni Append, DeployIfNotExist e AuditIfNotExist l'istruzione IF deve essere TRUE. Per non essere conformi, è anche necessario che la condizione di esistenza per le azioni sia FALSE. Se è TRUE, la condizione IF attiva la valutazione della condizione di esistenza per le risorse correlate.

Per comprendere meglio come le risorse vengano contrassegnate come non conformi, verrà usato l'esempio di assegnazione dei criteri creato sopra.

Ad esempio, si supponga di avere un gruppo di risorse, ContosoRG, con alcuni account di archiviazione (evidenziati in rosso) esposti su reti pubbliche.

![Account di archiviazione esposti su reti pubbliche](./media/policy-insights/resource-group01.png)

In questo esempio, è necessario essere ben consapevoli dei rischi di sicurezza. Dopo avere creato un'assegnazione dei criteri, questa viene valutata per tutti gli account di archiviazione nel gruppo di risorse ContosoRG. Controlla i tre account di archiviazione non conformi e di conseguenza ne modifica quindi gli stati impostandoli come **non conformi**.

![Account di archiviazione non conformi controllati](./media/policy-insights/resource-group03.png)

Usare la procedura seguente per identificare le risorse di un gruppo di risorse non conformi all'assegnazione di criteri creata. Nell'esempio le risorse sono gli account di archiviazione nel gruppo di risorse ContosoRG.

1. Ottenere l'ID dell'assegnazione dei criteri eseguendo i comandi seguenti:

  ```azurepowershell-interactive
  $policyAssignment = Get-AzureRmPolicyAssignment | Where-Object {$_.Properties.displayName -eq 'Audit Storage Accounts with Open Public Networks'}

  $policyAssignment.PolicyAssignmentId
  ```

  Per altre informazioni sull'acquisizione dell'ID di un'assegnazione di criteri, vedere [Get-AzureRMPolicyAssignment](https://docs.microsoft.com/powershell/module/azurerm.resources/Get-AzureRmPolicyAssignment).

2. Eseguire questo comando per copiare gli ID delle risorse non conformi in un file JSON:

  ```
  armclient POST "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2017-12-12-preview&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
  ```

3. Il risultato sarà simile al seguente:

  ```json
  {
      "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
      "@odata.count": 3,
      "value": [{
              "@odata.id": null,
              "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
              "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.storage/storageaccounts/<storageaccount1Id>"
          },
          {
              "@odata.id": null,
              "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
              "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.storage/storageaccounts/<storageaccount2Id>"
          },
          {
              "@odata.id": null,
              "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
              "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.storage/storageaccounts/<storageaccount3ID>"
          }
      ]
  }
  ```

I risultati sono equivalenti alle informazioni in genere riportate sotto **Risorse non conformi** nella [visualizzazione nel portale di Azure](assign-policy-definition.md#identify-non-compliant-resources).

Attualmente, le risorse non conformi vengono identificate solo usando il portale di Azure con le richieste HTTP. Per altre informazioni sull'esecuzione di query sugli stati dei criteri, vedere l'articolo di riferimento sull'API [Policy State](/rest/api/policy-insights/policystates) (Stato dei criteri).

## <a name="view-policy-events"></a>Visualizzare eventi criteri

Quando si crea o si aggiorna una risorsa, viene generato un risultato di valutazione dei criteri. I risultati sono chiamati _eventi criteri_. Eseguire la query seguente per visualizzare tutti gli eventi dei criteri associati all'assegnazione dei criteri.

```
armclient POST "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks/providers/Microsoft.PolicyInsights/policyEvents/default/queryResults?api-version=2017-12-12-preview"
```

I risultati saranno simili all'esempio seguente:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default/$entity",
        "NumAuditEvents": 3
    }]
}
```

Come per gli stati dei criteri, è possibile visualizzare gli eventi dei criteri solo con le richieste HTTP. Per altre informazioni sull'esecuzione di query sugli eventi dei criteri, vedere l'articolo di riferimento [Policy Events](/rest/api/policy-insights/policyevents) (Eventi dei criteri).

## <a name="change-a-policy-assignments-pricing-tier"></a>Modificare il piano tariffario di un'assegnazione dei criteri

È possibile usare il cmdlet di PowerShell *Set-AzureRmPolicyAssignment* per aggiornare il piano tariffario a Standard o Gratuito per un'assegnazione dei criteri esistente. Ad esempio: 

```azurepowershell-interactive
Set-AzureRmPolicyAssignment -Id '/subscriptions/<subscriptionId/resourceGroups/<resourceGroupName>/providers/Microsoft.Authorization/policyAssignments/<policyAssignmentID>' -Sku @{Name='A1';Tier='Standard'}
```

Per altre informazioni sul cmdlet, vedere [Set-AzureRmPolicyAssignment](/powershell/module/azurerm.resources/Set-AzureRmPolicyAssignment).

## <a name="next-steps"></a>Passaggi successivi

Esaminare gli articoli seguenti per altre informazioni sui comandi e sulle query di questo articolo.

- [Risorse di API REST di Azure](/rest/api/resources/)
- [Moduli PowerShell RM](/powershell/module/azurerm.resources/#policies)
- [Comandi dei criteri dell'interfaccia della riga di comando di Azure](/cli/azure/policy?view=azure-cli-latest)
- [Policy Insights resource provider REST API reference (Informazioni di riferimento sull'API REST del provider di risorse Policy Insights)](/rest/api/policy-insights)
