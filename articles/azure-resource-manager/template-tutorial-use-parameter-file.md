---
title: 'Esercitazione: Usare un file di parametri per distribuire il modello'
description: Usare file di parametri che contengono i valori da usare per la distribuzione del modello di Azure Resource Manager.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 1b01e9ece2d194d76c7184a676f17d626c41a011
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74405980"
---
# <a name="tutorial-use-parameter-files-to-deploy-your-resource-manager-template"></a>Esercitazione: Usare file di parametri per distribuire il modello di Azure Resource Manager

Questa esercitazione illustra come usare i [file di parametri](resource-manager-parameter-files.md) per archiviare i valori passati durante la distribuzione. Nelle esercitazioni precedenti sono stati usati parametri inline con il comando di distribuzione. Questo approccio ha funzionato per il test del modello, ma quando si automatizzano le distribuzioni può risultare più agevole passare un set di valori per l'ambiente. I file di parametri semplificano la creazione di un pacchetto di valori di parametri per un ambiente specifico. In questa esercitazione si creeranno file di parametri per gli ambienti di sviluppo e di produzione. Per completare l'esercitazione, sono necessari circa **12 minuti**.

## <a name="prerequisites"></a>Prerequisiti

È consigliabile, ma non obbligatorio, completare l'[esercitazione sui tag](template-tutorial-add-tags.md).

È necessario avere Visual Studio Code con l'estensione Strumenti di Resource Manager e Azure PowerShell oppure l'interfaccia della riga di comando di Azure. Per altre informazioni, vedere gli [strumenti per i modelli](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Rivedere il modello

Il modello include numerosi parametri che è possibile specificare durante la distribuzione. Al termine dell'esercitazione precedente, il modello era simile al seguente:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json)]

Questo modello funziona correttamente, ma ora si vogliono gestire facilmente i parametri passati per il modello.

## <a name="add-parameter-files"></a>Aggiungere file di parametri

I file di parametri sono file JSON con una struttura simile a quella del modello. Nel file è possibile specificare i valori dei parametri da passare durante la distribuzione.

In VS Code creare un nuovo file con il contenuto seguente. Salvare il file con il nome **azuredeploy.parameters.dev.json**.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.dev.json)]

Questo è il file di parametri per l'ambiente di sviluppo. Si noti che usa Standard_LRS come account di archiviazione, aggiunge il prefisso **dev** alle risorse e imposta il tag **Environment** su **Dev**.

Creare un nuovo file con il contenuto seguente. Salvare il file con il nome **azuredeploy.parameters.prod.json**.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.prod.json)]

Questo è il file di parametri per l'ambiente di produzione. Si noti che usa Standard_GRS come account di archiviazione, aggiunge il prefisso **contoso** alle risorse e imposta il tag **Environment** su **Production**. In un ambiente di produzione reale si vuole anche usare un servizio app con uno SKU diverso da quello gratuito, ma per questa esercitazione si continuerà a usare lo SKU gratuito.

## <a name="deploy-template"></a>Distribuire il modello

Per distribuire il modello, usare l'interfaccia della riga di comando di Azure o Azure PowerShell.

Come test finale del modello, creare due nuovi gruppi di risorse, uno per l'ambiente di sviluppo e uno per l'ambiente di produzione.

Prima di tutto, verrà eseguita la distribuzione nell'ambiente di sviluppo.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{provide-the-path-to-the-template-file}"
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

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
templateFile="{provide-the-path-to-the-template-file}"
az group create \
  --name myResourceGroupDev \
  --location "East US"
az group deployment create \
  --name devenvironment \
  --resource-group myResourceGroupDev \
  --template-file $templateFile \
  --parameters azuredeploy.parameters.dev.json
```

---

A questo punto, verrà eseguita la distribuzione nell'ambiente di produzione.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

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

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
az group create \
  --name myResourceGroupProd \
  --location "West US"
az group deployment create \
  --name prodenvironment \
  --resource-group myResourceGroupProd \
  --template-file $templateFile \
  --parameters azuredeploy.parameters.prod.json
```

---

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

È ora possibile passare a concetti più avanzati relativi ai modelli. L'esercitazione successiva illustra in modo più dettagliato l'uso della documentazione di riferimento dei modelli per la definizione delle risorse da distribuire.

> [!div class="nextstepaction"]
> [Utilizzare il riferimento del modello](resource-manager-tutorial-create-encrypted-storage-accounts.md)
