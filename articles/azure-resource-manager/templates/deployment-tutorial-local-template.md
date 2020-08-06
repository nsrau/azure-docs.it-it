---
title: 'Esercitazione: Distribuire un modello di Azure Resource Manager locale'
description: Informazioni su come distribuire un modello di Azure Resource Manager da un computer locale
ms.date: 05/20/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: ef52d20b20446e3b8f3b761949bf255b99d358cc
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87499586"
---
# <a name="tutorial-deploy-a-local-azure-resource-manager-template"></a>Esercitazione: Distribuire un modello di Azure Resource Manager locale

Informazioni su come distribuire un modello di Azure Resource Manager da un computer locale. Per completare l'esercitazione, sono necessari circa **8 minuti**.

Questa è la prima esercitazione di una serie. Nelle esercitazioni successive della serie si modularizzerà il modello creando un modello collegato, si archivierà il modello collegato in un account di archiviazione e si proteggerà il modello collegato usando un token di firma di accesso condiviso nonché si apprenderà come creare una pipeline DevOp per distribuire i modelli. Questa serie riguarda la distribuzione dei modelli.  Per informazioni sullo sviluppo dei modelli, vedere le [esercitazioni per principianti](./template-tutorial-create-first-template.md).

## <a name="get-tools"></a>Ottenere gli strumenti

Per iniziare, verificare di disporre degli strumenti necessari per distribuire i modelli.

### <a name="command-line-deployment"></a>Distribuzione dalla riga di comando

Per distribuire il modello, è necessario usare l'interfaccia della riga di comando di Azure oppure Azure PowerShell. Per le istruzioni di installazione, vedere:

- [Installare Azure PowerShell](/powershell/azure/install-az-ps)
- [Installare l'interfaccia della riga di comando di Azure in Windows](/cli/azure/install-azure-cli-windows)
- [Installare l'interfaccia della riga di comando di Azure in Linux](/cli/azure/install-azure-cli-linux)

Dopo aver installato Azure PowerShell o l'interfaccia della riga di comando di Azure, assicurarsi di accedere per la prima volta. Per informazioni, vedere [Accesso - PowerShell](/powershell/azure/install-az-ps#sign-in) or [Accesso - Interfaccia della riga di comando di Azure](/cli/azure/get-started-with-azure-cli#sign-in).

### <a name="editor-optional"></a>Editor (facoltativo)

I modelli sono file JSON. Per controllare/modificare i modelli, è necessario un editor JSON valido. È consigliabile usare Visual Studio Code con l'estensione Strumenti di Resource Manager. Se è necessario installare questi strumenti, vedere [Avvio rapido: Creare modelli di Azure Resource Manager con Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).

## <a name="review-template"></a>Rivedere il modello

Con il modello vengono distribuiti un account di archiviazione, un piano di servizio app e un'app Web. Se si è interessati alla creazione del modello, è possibile eseguire l'[esercitazione sui modelli di avvio rapido](template-tutorial-quickstart-template.md). Tuttavia, questo passaggio non è necessario per completare questa esercitazione.

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

> [!IMPORTANT]
> I nomi degli account di archiviazione devono essere di lunghezza compresa tra 3 e 24 caratteri e utilizzare solo numeri e lettere minuscole. Il nome deve essere univoco. Nel modello il nome dell'account di archiviazione è il nome del progetto con l'aggiunta di "store" e il nome del progetto deve avere una lunghezza compresa tra 3 e 11 caratteri. Quindi il nome del progetto deve soddisfare i requisiti del nome dell'account di archiviazione e deve avere meno di 11 caratteri.

Salvare una copia del modello nel computer locale con l'estensione JSON, ad esempio azuredeploy.json. Questo modello verrà distribuito più avanti nell'esercitazione.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Per iniziare a usare Azure PowerShell o l'interfaccia della riga di comando di Azure per distribuire un modello, accedere con le credenziali di Azure.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
az login
```

---

Se si dispone di più sottoscrizioni di Azure, selezionare quella da usare:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Set-AzContext [SubscriptionID/SubscriptionName]
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
az account set --subscription [SubscriptionID/SubscriptionName]
```

---

## <a name="create-resource-group"></a>Creare un gruppo di risorse

Quando si distribuisce un modello, si specifica un gruppo di risorse che conterrà le risorse. Prima di eseguire il comando di distribuzione, creare il gruppo di risorse usando l'interfaccia della riga di comando di Azure oppure Azure PowerShell. Selezionare le schede nella sezione di codice seguente per scegliere tra Azure PowerShell e l'interfaccia della riga di comando di Azure. Gli esempi dell'interfaccia della riga di comando in questo articolo sono scritti per la shell Bash.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource and resource group names"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup `
  -Name $resourceGroupName `
  -Location "Central US"
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
echo "Enter a project name that is used to generate resource and resource group names:"
read projectName
resourceGroupName="${projectName}rg"

az group create \
  --name $resourceGroupName \
  --location "Central US"
```

---

## <a name="deploy-template"></a>Distribuire il modello

Usare una o entrambe le opzioni di distribuzione per distribuire il modello.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name"
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroupDeployment `
  -Name DeployLocalTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile $templateFile `
  -projectName $projectName `
  -verbose
```

Per informazioni dettagliate sulla distribuzione del modello con Azure PowerShell, vedere [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](./deploy-powershell.md).

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
echo "Enter the same project name:"
read projectName
echo "Enter the template file path and file name:"
read templateFile
resourceGroupName="${projectName}rg"

az deployment group create \
  --name DeployLocalTemplate \
  --resource-group $resourceGroupName \
  --template-file $templateFile \
  --parameters projectName=$projectName \
  --verbose
```

Per informazioni dettagliate sulla distribuzione del modello con l'interfaccia della riga di comando di Azure, vedere [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](./deploy-cli.md).

---

## <a name="clean-up-resources"></a>Pulire le risorse

Eliminare le risorse distribuite eliminando il gruppo di risorse.

1. Nel portale di Azure selezionare **Gruppo di risorse** nel menu a sinistra.
2. Immettere il nome del gruppo di risorse nel campo **Filtra per nome**.
3. Selezionare il nome del gruppo di risorse.
4. Selezionare **Elimina gruppo di risorse** nel menu in alto.

## <a name="next-steps"></a>Passaggi successivi

Si è appreso come distribuire un modello locale. Nell'esercitazione successiva il modello verrà separato in un modello principale e un modello collegato e verrà illustrato come archiviare e proteggere il modello collegato.

> [!div class="nextstepaction"]
> [Distribuire un modello collegato](./deployment-tutorial-linked-template.md)
