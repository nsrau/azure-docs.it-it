---
title: Distribuire le risorse nei gruppi di risorse
description: Viene descritto come distribuire le risorse in un modello di Azure Resource Manager. Mostra come definire come destinazione più di un gruppo di risorse.
ms.topic: conceptual
ms.date: 10/26/2020
ms.openlocfilehash: fd211641d7fcc02a1db154053597497583b21ae5
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92681543"
---
# <a name="resource-group-deployments-with-arm-templates"></a>Distribuzioni di gruppi di risorse con modelli ARM

Questo articolo descrive come definire l'ambito della distribuzione in un gruppo di risorse. Usare un modello di Azure Resource Manager (modello ARM) per la distribuzione. Questo articolo illustra anche come espandere l'ambito oltre il gruppo di risorse nell'operazione di distribuzione.

## <a name="supported-resources"></a>Risorse supportate

La maggior parte delle risorse può essere distribuita in un gruppo di risorse. Per un elenco delle risorse disponibili, vedere informazioni di [riferimento sul modello ARM](/azure/templates).

## <a name="schema"></a>SCHEMA

Per i modelli, utilizzare lo schema seguente:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    ...
}
```

Per i file di parametri, usare:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    ...
}
```

## <a name="deployment-commands"></a>Comandi di distribuzione

Per eseguire la distribuzione in un gruppo di risorse, usare i comandi di distribuzione del gruppo di risorse.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per l'interfaccia della riga di comando di Azure, usare [AZ Deployment Group create](/cli/azure/deployment/group#az_deployment_group_create). L'esempio seguente distribuisce un modello per creare un gruppo di risorse:

```azurecli-interactive
az deployment group create \
  --name demoRGDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per il comando di distribuzione di PowerShell, usare [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). L'esempio seguente distribuisce un modello per creare un gruppo di risorse:

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Name demoRGDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -storageAccountType Standard_GRS `
```

---

Per informazioni più dettagliate sui comandi e sulle opzioni di distribuzione per la distribuzione di modelli ARM, vedere:

* [Distribuire le risorse con i modelli ARM e portale di Azure](deploy-portal.md)
* [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](deploy-cli.md)
* [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](deploy-powershell.md)
* [Distribuire le risorse con i modelli ARM e Azure Resource Manager API REST](deploy-rest.md)
* [Usare un pulsante di distribuzione per distribuire i modelli dal repository GitHub](deploy-to-azure-button.md)
* [Distribuire modelli ARM da Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-scopes"></a>Ambiti di distribuzione

Quando si esegue la distribuzione in un gruppo di risorse, è possibile distribuire le risorse in:

* gruppo di risorse di destinazione dall'operazione
* altri gruppi di risorse nella stessa sottoscrizione o in altre sottoscrizioni
* [le risorse di estensione](scope-extension-resources.md) possono essere applicate alle risorse

L'utente che distribuisce il modello deve avere accesso all'ambito specificato.

In questa sezione viene illustrato come specificare ambiti diversi. È possibile combinare questi ambiti diversi in un singolo modello.

### <a name="scope-to-target-resource-group"></a>Ambito del gruppo di risorse di destinazione

Per distribuire le risorse nella risorsa di destinazione, aggiungere tali risorse alla sezione Resources del modello.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-rg.json" highlight="5":::

### <a name="scope-to-resource-group-in-same-subscription"></a>Ambito del gruppo di risorse nella stessa sottoscrizione

Per distribuire le risorse in un gruppo di risorse diverso nella stessa sottoscrizione, aggiungere una distribuzione annidata e includere la `resourceGroup` Proprietà. Se non si specifica l'ID sottoscrizione o il gruppo di risorse, vengono usati la sottoscrizione e il gruppo di risorse del modello padre. Tutti i gruppi di risorse devono esistere prima di eseguire la distribuzione.

Nell'esempio seguente la distribuzione annidata è destinata a un gruppo di risorse denominato `demoResourceGroup` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/same-sub-to-resource-group.json" highlight="9,13":::

### <a name="scope-to-resource-group-in-different-subscription"></a>Ambito del gruppo di risorse in una sottoscrizione diversa

Per distribuire le risorse in un gruppo di risorse in una sottoscrizione diversa, aggiungere una distribuzione annidata e includere le `subscriptionId` `resourceGroup` proprietà e. Nell'esempio seguente la distribuzione annidata è destinata a un gruppo di risorse denominato `demoResourceGroup` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/different-sub-to-resource-group.json" highlight="9,10,14":::

## <a name="cross-resource-groups"></a>Gruppi di risorse incrociati

È possibile eseguire la distribuzione in più di un gruppo di risorse in un singolo modello ARM. Per impostare come destinazione un gruppo di risorse diverso da quello del modello padre, usare un [modello annidato o collegato](linked-templates.md). Nel tipo di risorsa di distribuzione specificare i valori relativi all'ID sottoscrizione e al gruppo di risorse in cui si vuole distribuire il modello annidato. I gruppi di risorse possono trovarsi in sottoscrizioni diverse.

> [!NOTE]
> Una singola distribuzione può interessare fino a **800 gruppi di risorse** . Questa limitazione significa in genere che è possibile eseguire la distribuzione in un solo gruppo di risorse specificato per il modello padre e in un massimo di 799 gruppi di risorse nelle distribuzioni annidate o collegate. Tuttavia, se il modello padre contiene solo modelli annidati o collegati e non distribuisce risorse, è possibile includere fino a 800 gruppi di risorse nelle distribuzioni annidate o collegate.

L'esempio seguente consente di distribuire due account di archiviazione. Il primo account di archiviazione viene distribuito nel gruppo di risorse specificato nell'operazione di distribuzione. Il secondo account di archiviazione viene distribuito nel gruppo di risorse specificato nei parametri `secondResourceGroup` e `secondSubscriptionID`:

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json":::

Se si imposta `resourceGroup` sul nome di un gruppo di risorse che non esiste, la distribuzione non riesce.

Per testare il modello precedente e visualizzare i risultati, usare PowerShell o l'interfaccia della riga di comando di Azure.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per distribuire due account di archiviazione in due gruppi di risorse nella **stessa sottoscrizione** , usare:

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

Per distribuire due account di archiviazione in **due sottoscrizioni** , usare:

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

Per distribuire due account di archiviazione in due gruppi di risorse nella **stessa sottoscrizione** , usare:

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

Per distribuire due account di archiviazione in **due sottoscrizioni** , usare:

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

## <a name="next-steps"></a>Passaggi successivi

* Per un esempio di distribuzione delle impostazioni dell'area di lavoro per il Centro sicurezza di Azure, vedere [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
