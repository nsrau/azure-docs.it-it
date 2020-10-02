---
title: 'Esercitazione: Usare un file di parametri per distribuire il modello'
description: Usare file di parametri che contengono i valori da usare per la distribuzione del modello di Azure Resource Manager.
author: mumian
ms.date: 09/10/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: de72f9f32a3b08ad1742ee2055efce5b93cab899
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90069510"
---
# <a name="tutorial-use-parameter-files-to-deploy-your-arm-template"></a>Esercitazione: Usare file di parametri per distribuire il modello di Azure Resource Manager

Questa esercitazione illustra come usare i [file di parametri](parameter-files.md) per archiviare i valori passati durante la distribuzione. Nelle esercitazioni precedenti sono stati usati parametri inline con il comando di distribuzione. Questo approccio ha funzionato per il test del modello di Azure Resource Manager, ma quando si automatizzano le distribuzioni può risultare più agevole passare un set di valori per l'ambiente. I file di parametri semplificano la creazione di un pacchetto di valori di parametri per un ambiente specifico. In questa esercitazione si creeranno file di parametri per gli ambienti di sviluppo e di produzione. Per completare l'esercitazione, sono necessari circa **12 minuti**.

## <a name="prerequisites"></a>Prerequisiti

È consigliabile, ma non obbligatorio, completare l'[esercitazione sui tag](template-tutorial-add-tags.md).

È necessario avere Visual Studio Code con l'estensione Strumenti di Resource Manager e Azure PowerShell oppure l'interfaccia della riga di comando di Azure. Per altre informazioni, vedere gli [strumenti per i modelli](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Rivedere il modello

Il modello include numerosi parametri che è possibile specificare durante la distribuzione. Al termine dell'esercitazione precedente, il modello era simile al seguente:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json":::

Questo modello funziona correttamente, ma ora si vogliono gestire facilmente i parametri passati per il modello.

## <a name="add-parameter-files"></a>Aggiungere file di parametri

I file di parametri sono file JSON con una struttura simile a quella del modello. Nel file è possibile specificare i valori dei parametri da passare durante la distribuzione.

Nel file di parametri specificare i valori per i parametri del modello. Il nome di ogni parametro nel file di parametri deve corrispondere al nome di un parametro nel modello. Il nome non fa distinzione tra maiuscole/minuscole, ma per vedere facilmente i valori corrispondenti, è consigliabile usare la stessa combinazione del modello.

Non è necessario specificare un valore per ogni parametro. Se un parametro non specificato ha un valore predefinito, verrà usato durante la distribuzione. Se un parametro non ha un valore predefinito e non viene specificato nel file di parametri, viene chiesto di specificarlo durante la distribuzione.

Nel file di parametri non è possibile specificare un nome di parametro che non corrisponde a un nome di parametro nel modello. Se si specificano parametri sconosciuti, si riceve un errore.

In VS Code creare un nuovo file con il contenuto seguente. Salvare il file con il nome **azuredeploy.parameters.dev.json**.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.dev.json":::

Questo è il file di parametri per l'ambiente di sviluppo. Si noti che usa Standard_LRS come account di archiviazione, aggiunge il prefisso **dev** alle risorse e imposta il tag **Environment** su **Dev**.

Creare un nuovo file con il contenuto seguente. Salvare il file con il nome **azuredeploy.parameters.prod.json**.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.prod.json":::

Questo è il file di parametri per l'ambiente di produzione. Si noti che usa Standard_GRS come account di archiviazione, aggiunge il prefisso **contoso** alle risorse e imposta il tag **Environment** su **Production**. In un ambiente di produzione reale si vuole anche usare un servizio app con uno SKU diverso da quello gratuito, ma per questa esercitazione si continuerà a usare lo SKU gratuito.

## <a name="deploy-template"></a>Distribuire il modello

Per distribuire il modello, usare l'interfaccia della riga di comando di Azure o Azure PowerShell.

Come test finale del modello, creare due nuovi gruppi di risorse, uno per l'ambiente di sviluppo e uno per l'ambiente di produzione.

Prima di tutto, verrà eseguita la distribuzione nell'ambiente di sviluppo.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{path-to-the-template-file}"
$parameterFile="{path-to-azuredeploy.parameters.dev.json}"
New-AzResourceGroup `
  -Name myResourceGroupDev `
  -Location "East US"
New-AzResourceGroupDeployment `
  -Name devenvironment `
  -ResourceGroupName myResourceGroupDev `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per eseguire questo comando di distribuzione, è necessario usare la versione più recente dell'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

```azurecli
templateFile="{path-to-the-template-file}"
devParameterFile="{path-to-azuredeploy.parameters.dev.json}"
az group create \
  --name myResourceGroupDev \
  --location "East US"
az deployment group create \
  --name devenvironment \
  --resource-group myResourceGroupDev \
  --template-file $templateFile \
  --parameters $devParameterFile
```

---

A questo punto, verrà eseguita la distribuzione nell'ambiente di produzione.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$parameterFile="{path-to-azuredeploy.parameters.prod.json}"
New-AzResourceGroup `
  -Name myResourceGroupProd `
  -Location "West US"
New-AzResourceGroupDeployment `
  -Name prodenvironment `
  -ResourceGroupName myResourceGroupProd `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
prodParameterFile="{path-to-azuredeploy.parameters.prod.json}"
az group create \
  --name myResourceGroupProd \
  --location "West US"
az deployment group create \
  --name prodenvironment \
  --resource-group myResourceGroupProd \
  --template-file $templateFile \
  --parameters $prodParameterFile
```

---

> [!NOTE]
> Se la distribuzione non è riuscita, usare l'opzione **verbose** per ottenere informazioni sulle risorse create. Usare l'opzione **debug** per ottenere altre informazioni per il debug.

## <a name="verify-deployment"></a>Verificare la distribuzione

Per verificare la distribuzione, esplorare i gruppi di risorse nel portale di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nel menu di sinistra selezionare **Gruppi di risorse**.
1. Vengono visualizzati i due nuovi gruppi di risorse distribuiti in questa esercitazione.
1. Selezionare uno dei gruppi di risorse e visualizzare le risorse distribuite. Si noti che le risorse corrispondono ai valori specificati nel file di parametri per tale ambiente.

## <a name="clean-up-resources"></a>Pulire le risorse

1. Nel portale di Azure selezionare **Gruppo di risorse** nel menu a sinistra.
2. Immettere il nome del gruppo di risorse nel campo **Filtra per nome**. Se questa serie è stata completata, è necessario eliminare tre gruppi di risorse, ovvero myResourceGroup, myResourceGroupDev e myResourceGroupProd.
3. Selezionare il nome del gruppo di risorse.
4. Selezionare **Elimina gruppo di risorse** nel menu in alto.

## <a name="next-steps"></a>Passaggi successivi

L'introduzione alla distribuzione di modelli in Azure è stata completata. Usare la sezione dei commenti per inviare eventuali commenti e suggerimenti. Grazie.

La serie di esercitazioni successiva illustra in modo più dettagliato la distribuzione dei modelli.

> [!div class="nextstepaction"]
> [Distribuire un modello locale](./deployment-tutorial-local-template.md)
