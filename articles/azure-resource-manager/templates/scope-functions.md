---
title: Funzioni modello nelle distribuzioni con ambito
description: Viene descritto come vengono risolte le funzioni modello nelle distribuzioni con ambito. L'ambito può essere un tenant, gruppi di gestione, sottoscrizioni e gruppi di risorse.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: cb02a3dc808604a80fd9943138c1cd0d8648904e
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92681588"
---
# <a name="arm-template-functions-in-deployment-scopes"></a>Funzioni modello ARM negli ambiti di distribuzione

Con i modelli di Azure Resource Manager (modelli ARM), è possibile eseguire la distribuzione in gruppi di risorse, sottoscrizioni, gruppi di gestione o tenant. In genere, le [funzioni modello ARM](template-functions.md) funzionano allo stesso scopo per tutti gli ambiti. Questo articolo descrive le differenze esistenti per alcune funzioni a seconda dell'ambito.

## <a name="supported-functions"></a>Funzioni supportate

Quando si esegue la distribuzione in ambiti diversi, è necessario tenere presenti alcune considerazioni importanti:

* La funzione [resourceGroup ()](template-functions-resource.md#resourcegroup) è **supportata** per le distribuzioni di gruppi di risorse.
* La funzione [Subscription ()](template-functions-resource.md#subscription) è **supportata** per le distribuzioni di gruppi di risorse e sottoscrizioni.
* Le funzioni [Reference ()](template-functions-resource.md#reference) e [List ()](template-functions-resource.md#list) sono **supportate** per tutti gli ambiti.
* Usare [ResourceID ()](template-functions-resource.md#resourceid) per ottenere l'ID per una risorsa distribuita nel gruppo di risorse.

  ```json
  "subnet": {
    "id": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnet1Name'))]"
  }
  ```

* Usare la funzione [subscriptionResourceId ()](template-functions-resource.md#subscriptionresourceid) per ottenere l'ID per una risorsa distribuita nella sottoscrizione.

  Ad esempio, per ottenere l'ID risorsa per una definizione di criteri distribuita in una sottoscrizione, usare:

  ```json
  "roleDefinitionId": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
  ```

* Utilizzare la funzione [extensionResourceId ()](template-functions-resource.md#extensionresourceid) per le risorse implementate come estensioni del gruppo di gestione. Le definizioni dei criteri personalizzati che vengono distribuite nel gruppo di gestione sono estensioni del gruppo di gestione.

  Per ottenere l'ID risorsa per una definizione di criteri personalizzata a livello di gruppo di gestione, usare:
  
  ```json
  "policyDefinitionId": "[extensionResourceId(variables('mgScope'), 'Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
  ```

* Usare la funzione [tenantResourceId](template-functions-resource.md#tenantresourceid) per ottenere l'ID per una risorsa distribuita nel tenant. Le definizioni dei criteri predefinite sono risorse a livello di tenant. Quando si assegna un criterio predefinito a livello di gruppo di gestione, usare la funzione tenantResourceId.

  Per ottenere l'ID risorsa per una definizione dei criteri predefinita, usare:
  
  ```json
  "policyDefinitionId": "[tenantResourceId('Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
  ```

## <a name="function-resolution-in-scopes"></a>Risoluzione delle funzioni negli ambiti

Quando si esegue la distribuzione in più di un ambito, le funzioni [resourceGroup ()](template-functions-resource.md#resourcegroup) e [Subscription ()](template-functions-resource.md#subscription) vengono risolte in modo diverso in base alla modalità di specifica del modello. Quando si esegue il collegamento a un modello esterno, le funzioni vengono sempre risolte nell'ambito di tale modello. Quando si annida un modello all'interno di un modello padre, usare la proprietà `expressionEvaluationOptions` per specificare se le funzioni vengono risolte nel gruppo di risorse e nella sottoscrizione del modello padre o del modello annidato. Impostare la proprietà su `inner` per risolvere le funzioni nell'ambito del modello annidato. Impostare la proprietà su `outer` per risolvere le funzioni nell'ambito del modello padre.

La tabella seguente mostra se le funzioni vengono risolte nella sottoscrizione e nel gruppo di risorse padre o incorporato.

| Tipo di modello | Ambito | Risoluzione |
| ------------- | ----- | ---------- |
| annidato        | esterno (impostazione predefinita) | Gruppo di risorse padre |
| annidato        | interno | Gruppo di risorse secondarie |
| collegato        | N/D   | Gruppo di risorse secondarie |

Il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) seguente mostra:

* modello annidato con ambito (esterno) esterno
* modello annidato con ambito interno
* modello collegato

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crossresourcegroupproperties.json":::

Per testare il modello precedente e visualizzare i risultati, usare PowerShell o l'interfaccia della riga di comando di Azure.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name parentGroup -Location southcentralus
New-AzResourceGroup -Name inlineGroup -Location southcentralus
New-AzResourceGroup -Name linkedGroup -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

L'output dell'esempio precedente è:

```output
 Name             Type                       Value
 ===============  =========================  ==========
 parentRG         String                     Parent resource group is parentGroup
 defaultScopeRG   String                     Default scope resource group is parentGroup
 innerScopeRG     String                     Inner scope resource group is inlineGroup
 linkedRG         String                     Linked resource group is linkedgroup
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

L'output dell'esempio precedente è:

```output
"outputs": {
  "defaultScopeRG": {
    "type": "String",
    "value": "Default scope resource group is parentGroup"
  },
  "innerScopeRG": {
    "type": "String",
    "value": "Inner scope resource group is inlineGroup"
  },
  "linkedRG": {
    "type": "String",
    "value": "Linked resource group is linkedGroup"
  },
  "parentRG": {
    "type": "String",
    "value": "Parent resource group is parentGroup"
  }
},
```

---

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come definire i parametri nel modello, vedere [Comprendere la struttura e la sintassi dei modelli di Azure Resource Manager](template-syntax.md).
* Per suggerimenti su come risolvere i comuni errori di distribuzione, vedere [Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager](common-deployment-errors.md).
* Per informazioni sulla distribuzione di un modello che richiede un token di firma di accesso condiviso, vedere [Distribuire un modello privato con un token di firma di accesso condiviso](secure-template-with-sas-token.md).
