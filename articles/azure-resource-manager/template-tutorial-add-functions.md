---
title: 'Esercitazione: Aggiungere le funzioni del modello'
description: Aggiungere funzioni del modello al modello di Azure Resource Manager per costruire valori.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 03a38178ec66c1c1a10934975d20778369d80dbe
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74405968"
---
# <a name="tutorial-add-template-functions-to-your-resource-manager-template"></a>Esercitazione: Aggiungere funzioni del modello al modello di Azure Resource Manager

Questa esercitazione illustra come aggiungere [funzioni del modello](resource-group-template-functions.md) al modello. Le funzioni consentono di costruire i valori in modo dinamico. Oltre alle funzioni del modello fornite dal sistema, è anche possibile creare [funzioni definite dall'utente](./template-user-defined-functions.md). Per completare l'esercitazione, sono necessari **7 minuti**.

## <a name="prerequisites"></a>Prerequisiti

È consigliabile, ma non obbligatorio, completare l'[esercitazione sui parametri](template-tutorial-add-parameters.md).

È necessario avere Visual Studio Code con l'estensione Strumenti di Resource Manager e Azure PowerShell oppure l'interfaccia della riga di comando di Azure. Per altre informazioni, vedere gli [strumenti per i modelli](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Rivedere il modello

Al termine dell'esercitazione precedente, il modello includeva il codice JSON seguente:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json)]

La località dell'account di archiviazione è impostata come hardcoded su **Stati Uniti orientali**. Può però essere necessario distribuire l'account di archiviazione in altre aree. Anche in questo caso si deve affrontare il problema della mancanza di flessibilità del modello. È possibile aggiungere un parametro per la località, ma sarebbe ancor meglio se il relativo valore predefinito fosse più significativo rispetto a un valore hardcoded.

## <a name="use-function"></a>Usare la funzione

Se è stata completata l'esercitazione precedente di questa serie, è già stata usata una funzione. Quando si è aggiunto **"[parameters('storageName')]"** , si è usata la funzione [parameters](resource-group-template-functions-deployment.md#parameters). Le parentesi quadre indicano che la sintassi racchiusa tra parentesi è un'[espressione del modello](template-expressions.md). Resource Manager risolve la sintassi invece di considerarla come valore letterale.

Le funzioni aggiungono flessibilità al modello perché consentono di ottenere i valori in modo dinamico durante la distribuzione. In questa esercitazione si userà una funzione per ottenere la località del gruppo di risorse usato per la distribuzione.

Nell'esempio seguente sono evidenziate le modifiche da apportare per aggiungere un parametro denominato **location**.  Il valore predefinito del parametro chiama la funzione [resourceGroup](resource-group-template-functions-resource.md#resourcegroup). Questa funzione restituisce un oggetto contenente informazioni sul gruppo di risorse usato per la distribuzione. Una delle proprietà dell'oggetto è una proprietà location. Quando si usa il valore predefinito, la località dell'account di archiviazione è uguale a quella del gruppo di risorse. Le risorse all'interno di un gruppo di risorse non devono condividere la stessa località. Quando necessario, è anche possibile specificare una località diversa.

Copiare l'intero file e sostituire il modello con il contenuto del file.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.json?range=1-44&highlight=24-27,34)]

## <a name="deploy-template"></a>Distribuire il modello

Nelle esercitazioni precedenti è stato creato un account di archiviazione nell'area Stati Uniti orientali, ma il gruppo di risorse è stato creato nell'area Stati Uniti centrali. Per questa esercitazione l'account di archiviazione viene creato nella stessa area del gruppo di risorse. Usare il valore predefinito per location, in modo che non sia necessario specificare il valore di tale parametro. È necessario specificare un nuovo nome per l'account di archiviazione perché si sta creando un account di archiviazione in una località diversa. Usare, ad esempio, il prefisso **store2** invece di **store1**.

Se non è stato ancora creato il gruppo di risorse, vedere [Creare il gruppo di risorse](template-tutorial-create-first-template.md#create-resource-group). Nell'esempio si presuppone che la variabile **templateFile** sia stata impostata sul percorso del file modello, come illustrato nella [prima esercitazione](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addlocationparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{new-unique-name}"
```

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addlocationparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={new-unique-name}
```

---

## <a name="verify-deployment"></a>Verificare la distribuzione

Per verificare la distribuzione, esplorare il gruppo di risorse nel portale di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nel menu di sinistra selezionare **Gruppi di risorse**.
1. Selezionare il gruppo di risorse in cui è stata eseguita la distribuzione.
1. Si noterà che una risorsa dell'account di archiviazione è stata distribuita nella stessa località del gruppo di risorse.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende passare all'esercitazione successiva, non è necessario eliminare il gruppo di risorse.

Se invece ci si ferma, è opportuno eliminare il gruppo di risorse per rimuovere le risorse distribuite.

1. Nel portale di Azure selezionare **Gruppo di risorse** nel menu a sinistra.
2. Immettere il nome del gruppo di risorse nel campo **Filtra per nome**.
3. Selezionare il nome del gruppo di risorse.
4. Selezionare **Elimina gruppo di risorse** nel menu in alto.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata usata una funzione durante la definizione del valore predefinito per un parametro. In questa serie di esercitazioni si continueranno a usare le funzioni. Al termine della serie, si aggiungeranno funzioni a ogni sezione del modello.

> [!div class="nextstepaction"]
> [Aggiungere le variabili](template-tutorial-add-variables.md)
