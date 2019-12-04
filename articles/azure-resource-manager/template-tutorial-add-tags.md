---
title: 'Esercitazione: Aggiungere tag alle risorse nel modello'
description: Aggiungere tag alle risorse distribuite in un modello di Azure Resource Manager. I tag consentono di organizzare le risorse in modo logico.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 078d85b3120baf5b8bea9c305fb1f97763f8df59
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406072"
---
# <a name="tutorial-add-tags-in-your-resource-manager-template"></a>Esercitazione: Aggiungere tag nel modello di Azure Resource Manager

Questa esercitazione illustra come aggiungere tag alle risorse nel modello. I [tag](resource-group-using-tags.md) consentono di organizzare le risorse in modo logico. I valori dei tag vengono visualizzati nei report sui costi. Per completare l'esercitazione, sono necessari **8 minuti**.

## <a name="prerequisites"></a>Prerequisiti

È consigliabile, ma non obbligatorio, completare l'[esercitazione sui modelli di avvio rapido](template-tutorial-quickstart-template.md).

È necessario avere Visual Studio Code con l'estensione Strumenti di Resource Manager e Azure PowerShell oppure l'interfaccia della riga di comando di Azure. Per altre informazioni, vedere gli [strumenti per i modelli](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Rivedere il modello

Con il modello precedente sono stati distribuiti un account di archiviazione, un piano di servizio app e un'app Web.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json)]

Dopo la distribuzione di queste risorse, può essere necessario tenere traccia dei costi e individuare le risorse appartenenti a una categoria. Per risolvere questi problemi, è possibile aggiungere tag.

## <a name="add-tags"></a>Aggiungi tag

L'aggiunta di un tag alle risorse consente di aggiungere valori per identificarne più facilmente l'uso. È ad esempio possibile aggiungere tag che elencano l'ambiente e il progetto, nonché aggiungere tag che identificano un centro di costo oppure il team a cui appartiene la risorsa. Aggiungere tutti i valori appropriati per l'organizzazione.

L'esempio seguente evidenzia le modifiche apportate al modello. Copiare l'intero file e sostituire il modello con il contenuto del file.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json?range=1-118&highlight=46-52,64,78,100)]

## <a name="deploy-template"></a>Distribuire il modello

È ora possibile distribuire il modello ed esaminare i risultati.

Se non è stato ancora creato il gruppo di risorse, vedere [Creare il gruppo di risorse](template-tutorial-create-first-template.md#create-resource-group). Nell'esempio si presuppone che la variabile **templateFile** sia stata impostata sul percorso del file modello, come illustrato nella [prima esercitazione](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addtags `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addtags \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

## <a name="verify-deployment"></a>Verificare la distribuzione

Per verificare la distribuzione, esplorare il gruppo di risorse nel portale di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nel menu di sinistra selezionare **Gruppi di risorse**.
1. Selezionare il gruppo di risorse in cui è stata eseguita la distribuzione.
1. Selezionare una delle risorse, ad esempio la risorsa dell'account di archiviazione. Si noterà che ora include tag.

   ![Visualizzare i tag](./media/template-tutorial-add-tags/show-tags.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende passare all'esercitazione successiva, non è necessario eliminare il gruppo di risorse.

Se invece ci si ferma, è opportuno eliminare il gruppo di risorse per rimuovere le risorse distribuite.

1. Nel portale di Azure selezionare **Gruppo di risorse** nel menu a sinistra.
2. Immettere il nome del gruppo di risorse nel campo **Filtra per nome**.
3. Selezionare il nome del gruppo di risorse.
4. Selezionare **Elimina gruppo di risorse** nel menu in alto.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati aggiunti tag alle risorse. Nell'esercitazione successiva verrà illustrato come usare i file di parametri per semplificare il passaggio dei valori al modello.

> [!div class="nextstepaction"]
> [Usare il file di parametri](template-tutorial-use-parameter-file.md)
