---
title: 'Esercitazione: Usare i modelli di avvio rapido di Azure | Microsoft Docs'
description: Informazioni su come usare i modelli di avvio rapido di Azure per completare lo sviluppo del modello.
services: azure-resource-manager
author: mumian
manager: carmonmills
ms.service: azure-resource-manager
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: a29d86d105579dda7c12b885e2977406f7b598a4
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001491"
---
# <a name="tutorial-use-azure-quickstart-templates"></a>Esercitazione: Usare i modelli di avvio rapido di Azure

I [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/) costituiscono un repository di modelli aggiunti come contributo dalla community. È possibile usare i modelli di esempio nello sviluppo del modello. In questa esercitazione si individuerà una definizione di risorsa del sito Web che verrà aggiunta al modello personalizzato. Per completare l'esercitazione, sono necessari circa **12 minuti**.

## <a name="prerequisites"></a>Prerequisiti

È consigliabile, ma non obbligatorio, completare l'[esercitazione sui modelli esportati](template-tutorial-export-template.md).

È necessario avere Visual Studio Code con l'estensione Strumenti di Resource Manager e Azure PowerShell oppure l'interfaccia della riga di comando di Azure. Per altre informazioni, vedere gli [strumenti per i modelli](template-tutorial-create-first-template.md#get-tools).

## <a name="review-your-template"></a>Esaminare il modello

Al termine dell'esercitazione precedente, il modello includeva il codice JSON seguente:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.json)]

Questo modello è adatto per la distribuzione di account di archiviazione e piani di servizio app, ma è possibile che si voglia aggiungere un sito Web. È possibile usare i modelli predefiniti per individuare rapidamente il codice JSON necessario per la distribuzione di una risorsa.

## <a name="find-a-template"></a>Trovare un modello

1. Aprire i [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/)
1. In **Cerca** immettere **distribuisci app Web Linux**.
1. Selezionare il modello **Deploy a basic Linux web app**. Se non si riesce a trovarlo, ecco il [collegamento diretto](https://azure.microsoft.com/resources/templates/101-webapp-basic-linux/).
1. Selezionare **Sfoglia su GitHub**.
1. Selezionare **azuredeploy.json**.
1. Esaminare il modello. In particolare, cercare la risorsa `Microsoft.Web/sites`.

    ![Avvio rapido per i modelli di Resource Manager: sito Web](./media/template-tutorial-quickstart-template/resource-manager-template-quickstart-template-web-site.png)

## <a name="revise-the-existing-template"></a>Modificare il modello esistente

Unire il modello di avvio rapido con quello esistente:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json?range=1-108&highlight=32-45,49,85-100)]

Il nome dell'app Web deve essere univoco in Azure. Per evitare nomi duplicati, la variabile **webAppPortalName** è stata aggiornata da **"webAppPortalName": "[concat(parameters('webAppName'), '-webapp')]"** a **"webAppPortalName": "[concat(parameters('webAppName'), uniqueString(resourceGroup().id))]"** .

Aggiungere una virgola alla fine della definizione di `Microsoft.Web/serverfarms` per separare la definizione della risorsa dalla definizione di `Microsoft.Web/sites`.

In questa nuova risorsa sono presenti due importanti funzionalità da considerare.

Si noterà che include un elemento denominato **dependsOn** impostato sul piano di servizio app. Questa impostazione è obbligatoria perché il piano di servizio app deve esistere prima che venga creata l'app Web. L'elemento **dependsOn** indica a Resource Manager come ordinare le risorse per la distribuzione.

La proprietà **serverFarmId** usa la funzione [resourceId](resource-group-template-functions-resource.md#resourceid). Questa funzione consente di ottenere l'identificatore univoco per una risorsa. In questo caso, ottiene l'identificatore univoco per il piano di servizio app. L'app Web è associata a un piano di servizio app specifico.

## <a name="deploy-the-template"></a>Distribuire il modello

Per distribuire un modello, usare l'interfaccia della riga di comando di Azure o Azure PowerShell.

Se non è stato ancora creato il gruppo di risorse, vedere [Creare il gruppo di risorse](template-tutorial-create-first-template.md#create-resource-group). Nell'esempio si presuppone che la variabile **templateFile** sia stata impostata sul percorso del file modello, come illustrato nella [prima esercitazione](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addwebapp `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addwebapp \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende passare all'esercitazione successiva, non è necessario eliminare il gruppo di risorse.

Se invece ci si ferma, è opportuno eliminare il gruppo di risorse per rimuovere le risorse distribuite.

1. Nel portale di Azure selezionare **Gruppo di risorse** nel menu a sinistra.
2. Immettere il nome del gruppo di risorse nel campo **Filtra per nome**.
3. Selezionare il nome del gruppo di risorse.
4. Selezionare **Elimina gruppo di risorse** nel menu in alto.

## <a name="next-steps"></a>Passaggi successivi

È stato illustrato come usare un modello di avvio rapido per lo sviluppo di modelli. Nell'esercitazione successiva si aggiungeranno tag alle risorse.

> [!div class="nextstepaction"]
> [Aggiungere i tag](template-tutorial-add-tags.md)
