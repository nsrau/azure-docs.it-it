---
title: Distribuire le risorse tra sottoscrizioni e gruppi di risorse
description: Illustra come specificare come destinazione più sottoscrizioni e gruppi di risorse di Azure durante la distribuzione.
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 2ef68dcb933075833c323d973b023cdaee61bd2f
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650629"
---
# <a name="deploy-azure-resources-across-subscriptions-or-resource-groups"></a>Distribuire le risorse di Azure tra sottoscrizioni o gruppi di risorse

Resource Manager consente di eseguire la distribuzione in più gruppi di risorse in una singola distribuzione. Per specificare i gruppi di risorse diversi dal gruppo di risorse dell'operazione di distribuzione, si usano modelli annidati. I gruppi di risorse possono trovarsi in sottoscrizioni diverse.

> [!NOTE]
> Una singola distribuzione può interessare fino a **800 gruppi di risorse**. Questa limitazione significa in genere che è possibile eseguire la distribuzione in un solo gruppo di risorse specificato per il modello padre e in un massimo di 799 gruppi di risorse nelle distribuzioni annidate o collegate. Tuttavia, se il modello padre contiene solo modelli annidati o collegati e non distribuisce risorse, è possibile includere fino a 800 gruppi di risorse nelle distribuzioni annidate o collegate.

## <a name="specify-subscription-and-resource-group"></a>Specificare la sottoscrizione e il gruppo di risorse

Per impostare come destinazione un gruppo di risorse diverso da quello del modello padre, usare un [modello annidato o collegato](linked-templates.md). Nel tipo di risorsa di distribuzione specificare i valori relativi all'ID sottoscrizione e al gruppo di risorse in cui si vuole distribuire il modello annidato.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json" range="38-43" highlight="5-6":::

Se non si specifica l'ID sottoscrizione o il gruppo di risorse, vengono usati la sottoscrizione e il gruppo di risorse del modello padre. Tutti i gruppi di risorse devono esistere prima di eseguire la distribuzione.

L'account usato per distribuire il modello deve disporre dell'autorizzazione per la distribuzione per l'ID sottoscrizione specificato. Se la sottoscrizione specificata è presente in un tenant di Azure Active Directory diverso, è necessario [aggiungere gli utenti guest da un'altra directory](../../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).

L'esempio seguente consente di distribuire due account di archiviazione. Il primo account di archiviazione viene distribuito nel gruppo di risorse specificato nell'operazione di distribuzione. Il secondo account di archiviazione viene distribuito nel gruppo di risorse specificato nei parametri `secondResourceGroup` e `secondSubscriptionID`:

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json":::

Se si imposta `resourceGroup` sul nome di un gruppo di risorse che non esiste, la distribuzione non riesce.

Per testare il modello precedente e visualizzare i risultati, usare PowerShell o l'interfaccia della riga di comando di Azure.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per distribuire due account di archiviazione in due gruppi di risorse nella **stessa sottoscrizione**, usare:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

New-AzResourceGroup -Name $firstRG -Location southcentralus
New-AzResourceGroup -Name $secondRG -Location eastus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus
```

Per distribuire due account di archiviazione in **due sottoscrizioni**, usare:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

$firstSub = "<first-subscription-id>"
$secondSub = "<second-subscription-id>"

Select-AzSubscription -Subscription $secondSub
New-AzResourceGroup -Name $secondRG -Location eastus

Select-AzSubscription -Subscription $firstSub
New-AzResourceGroup -Name $firstRG -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus `
  -secondSubscriptionID $secondSub
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per distribuire due account di archiviazione in due gruppi di risorse nella **stessa sottoscrizione**, usare:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

az group create --name $firstRG --location southcentralus
az group create --name $secondRG --location eastus
az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=tfstorage secondResourceGroup=$secondRG secondStorageLocation=eastus
```

Per distribuire due account di archiviazione in **due sottoscrizioni**, usare:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

firstSub="<first-subscription-id>"
secondSub="<second-subscription-id>"

az account set --subscription $secondSub
az group create --name $secondRG --location eastus

az account set --subscription $firstSub
az group create --name $firstRG --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=storage secondResourceGroup=$secondRG secondStorageLocation=eastus secondSubscriptionID=$secondSub
```

---

## <a name="use-functions"></a>Usare le funzioni

Le funzioni [resourceGroup()](template-functions-resource.md#resourcegroup) e [subscription()](template-functions-resource.md#subscription) vengono risolte in modo diverso in base al modo in cui si specifica il modello. Quando si esegue il collegamento a un modello esterno, le funzioni vengono sempre risolte nell'ambito di tale modello. Quando si annida un modello all'interno di un modello padre, usare la proprietà `expressionEvaluationOptions` per specificare se le funzioni vengono risolte nel gruppo di risorse e nella sottoscrizione del modello padre o del modello annidato. Impostare la proprietà su `inner` per risolvere le funzioni nell'ambito del modello annidato. Impostare la proprietà su `outer` per risolvere le funzioni nell'ambito del modello padre.

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
