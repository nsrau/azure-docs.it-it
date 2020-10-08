---
title: 'Esercitazione: Usare i modelli di avvio rapido'
description: Informazioni su come usare i modelli di avvio rapido di Azure per completare lo sviluppo del modello.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 445e7ce2d6e609d75bff38bb3d049a87f184be12
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2020
ms.locfileid: "91613595"
---
# <a name="tutorial-use-azure-quickstart-templates"></a>Esercitazione: Usare i modelli di avvio rapido di Azure

I [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/) costituiscono un repository di modelli aggiunti come contributo dalla community. È possibile usare i modelli di esempio nello sviluppo del modello. In questa esercitazione si individuerà una definizione di risorsa del sito Web che verrà aggiunta al modello personalizzato. Per completare l'esercitazione, sono necessari circa **12 minuti**.

## <a name="prerequisites"></a>Prerequisiti

È consigliabile, ma non obbligatorio, completare l'[esercitazione sui modelli esportati](template-tutorial-export-template.md).

È necessario avere Visual Studio Code con l'estensione Strumenti di Resource Manager e Azure PowerShell oppure l'interfaccia della riga di comando di Azure. Per altre informazioni, vedere gli [strumenti per i modelli](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Rivedere il modello

Al termine dell'esercitazione precedente, il modello includeva il codice JSON seguente:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.json":::

Questo modello è adatto per la distribuzione di account di archiviazione e piani di servizio app, ma è possibile che si voglia aggiungere un sito Web. È possibile usare i modelli predefiniti per individuare rapidamente il codice JSON necessario per la distribuzione di una risorsa.

## <a name="find-template"></a>Trovare il modello

1. Aprire i [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/)
1. In **Cerca** immettere **distribuisci app Web Linux**.
1. Selezionare il modello **Deploy a basic Linux web app**. Se non si riesce a trovarlo, ecco il [collegamento diretto](https://azure.microsoft.com/resources/templates/101-webapp-basic-linux/).
1. Selezionare **Sfoglia su GitHub**.
1. Selezionare **azuredeploy.json**.
1. Esaminare il modello. In particolare, cercare la risorsa `Microsoft.Web/sites`.

    ![Avvio rapido per i modelli di Resource Manager: sito Web](./media/template-tutorial-quickstart-template/resource-manager-template-quickstart-template-web-site.png)

## <a name="revise-existing-template"></a>Modificare il modello esistente

Unire il modello di avvio rapido con quello esistente:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json" range="1-108" highlight="32-45,49,85-100":::

Il nome dell'app Web deve essere univoco in Azure. Per evitare nomi duplicati, la variabile **webAppPortalName** è stata aggiornata da **"webAppPortalName": "[concat(parameters('webAppName'), '-webapp')]"** a **"webAppPortalName": "[concat(parameters('webAppName'), uniqueString(resourceGroup().id))]"** .

Aggiungere una virgola alla fine della definizione di `Microsoft.Web/serverfarms` per separare la definizione della risorsa dalla definizione di `Microsoft.Web/sites`.

In questa nuova risorsa sono presenti due importanti funzionalità da considerare.

Si noterà che include un elemento denominato **dependsOn** impostato sul piano di servizio app. Questa impostazione è obbligatoria perché il piano di servizio app deve esistere prima che venga creata l'app Web. L'elemento **dependsOn** indica a Resource Manager come ordinare le risorse per la distribuzione.

La proprietà **serverFarmId** usa la funzione [resourceId](template-functions-resource.md#resourceid). Questa funzione consente di ottenere l'identificatore univoco per una risorsa. In questo caso, ottiene l'identificatore univoco per il piano di servizio app. L'app Web è associata a un piano di servizio app specifico.

## <a name="deploy-template"></a>Distribuire il modello

Per distribuire un modello, usare l'interfaccia della riga di comando di Azure o Azure PowerShell.

Se non è stato ancora creato il gruppo di risorse, vedere [Creare il gruppo di risorse](template-tutorial-create-first-template.md#create-resource-group). Nell'esempio si presuppone che la variabile **templateFile** sia stata impostata sul percorso del file modello, come illustrato nella [prima esercitazione](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addwebapp `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per eseguire questo comando di distribuzione, è necessario usare la versione più recente dell'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

```azurecli
az deployment group create \
  --name addwebapp \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

> [!NOTE]
> Se la distribuzione non è riuscita, usare l'opzione **verbose** per ottenere informazioni sulle risorse create. Usare l'opzione **debug** per ottenere altre informazioni per il debug.

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
