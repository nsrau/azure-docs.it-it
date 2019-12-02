---
title: 'Esercitazione: Aggiungere output al modello'
description: Aggiungere output al modello di Azure Resource Manager per semplificare la sintassi.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 654d3f7cbf6362d982549c86e6f54fea1e890cfc
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74405990"
---
# <a name="tutorial-add-outputs-to-your-resource-manager-template"></a>Esercitazione: Aggiungere output al modello di Resource Manager

Questa esercitazione illustra come restituire un valore dal modello. Gli output vengono usati quando è necessario ottenere un valore da una risorsa distribuita. Per completare l'esercitazione, sono necessari **7 minuti**.

## <a name="prerequisites"></a>Prerequisiti

È consigliabile, ma non obbligatorio, completare l'[esercitazione sulle variabili](template-tutorial-add-variables.md).

È necessario avere Visual Studio Code con l'estensione Strumenti di Resource Manager e Azure PowerShell oppure l'interfaccia della riga di comando di Azure. Per altre informazioni, vedere gli [strumenti per i modelli](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Rivedere il modello

Al termine dell'esercitazione precedente, il modello includeva il codice JSON seguente:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.json)]

Distribuisce un account di archiviazione, ma non restituisce informazioni relative all'account di archiviazione. Può essere necessario acquisire le proprietà da una nuova risorsa in modo che siano disponibili in seguito per riferimento.

## <a name="add-outputs"></a>Aggiungere gli output

È possibile usare gli output per restituire i valori dal modello. Può, ad esempio, essere utile ottenere gli endpoint per il nuovo account di archiviazione.

L'esempio seguente evidenzia la modifica apportata al modello per aggiungere un valore di output. Copiare l'intero file e sostituire il modello con il contenuto del file.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json?range=1-53&highlight=47-52)]

Ecco alcune importanti aspetti da considerare sul valore di output aggiunto.

Il tipo di valore restituito è impostato su **object**, per indicare che restituisce un oggetto JSON.

Usa la funzione [reference](resource-group-template-functions-resource.md#reference) per ottenere lo stato di runtime dell'account di archiviazione. Per ottenere lo stato di runtime di una risorsa, si deve passare il nome o l'ID di una risorsa. In questo caso si usa la stessa variabile usata per creare il nome dell'account di archiviazione.

Infine, restituisce la proprietà **primaryEndpoints** dell'account di archiviazione.

## <a name="deploy-template"></a>Distribuire il modello

È ora possibile distribuire il modello ed esaminare il valore restituito.

Se non è stato ancora creato il gruppo di risorse, vedere [Creare il gruppo di risorse](template-tutorial-create-first-template.md#create-resource-group). Nell'esempio si presuppone che la variabile **templateFile** sia stata impostata sul percorso del file modello, come illustrato nella [prima esercitazione](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addoutputs `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addoutputs \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

Nell'output del comando di distribuzione verrà visualizzato un oggetto simile al seguente:

```json
{
    "dfs": "https://storeluktbfkpjjrkm.dfs.core.windows.net/",
    "web": "https://storeluktbfkpjjrkm.z19.web.core.windows.net/",
    "blob": "https://storeluktbfkpjjrkm.blob.core.windows.net/",
    "queue": "https://storeluktbfkpjjrkm.queue.core.windows.net/",
    "table": "https://storeluktbfkpjjrkm.table.core.windows.net/",
    "file": "https://storeluktbfkpjjrkm.file.core.windows.net/"
}
```

## <a name="review-your-work"></a>Esaminare il lavoro

Nelle ultime sei esercitazioni sono state completate numerose procedure. Dedichiamo qualche istante a esaminare il lavoro svolto finora. È stato creato un modello con parametri facili da specificare. Il modello è riutilizzabile in ambienti diversi perché permette la personalizzazione e crea i valori necessari in modo dinamico. Restituisce anche informazioni sull'account di archiviazione che è possibile usare nello script.

A questo punto si esaminerà il gruppo di risorse e la cronologia di distribuzione.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nel menu di sinistra selezionare **Gruppi di risorse**.
1. Selezionare il gruppo di risorse in cui è stata eseguita la distribuzione.
1. A seconda dei passaggi eseguiti, dovrebbero essere presenti uno o più account di archiviazione nel gruppo di risorse,
1. oltre a diverse distribuzioni riuscite nella cronologia. Selezionare il collegamento.

   ![Selezionare le distribuzioni](./media/template-tutorial-add-outputs/select-deployments.png)

1. Vengono visualizzate tutte le distribuzioni presenti nella cronologia. Selezionare la distribuzione denominata **addoutputs**.

   ![Visualizzare la cronologia delle distribuzioni](./media/template-tutorial-add-outputs/show-history.png)

1. È possibile esaminare gli input.

   ![Visualizzare gli input](./media/template-tutorial-add-outputs/show-inputs.png)

1. È possibile esaminare gli output.

   ![Visualizzare gli output](./media/template-tutorial-add-outputs/show-outputs.png)

1. È possibile rivedere il modello.

   ![Mostrare il modello](./media/template-tutorial-add-outputs/show-template.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende passare all'esercitazione successiva, non è necessario eliminare il gruppo di risorse.

Se invece ci si ferma, è opportuno eliminare il gruppo di risorse per rimuovere le risorse distribuite.

1. Nel portale di Azure selezionare **Gruppo di risorse** nel menu a sinistra.
2. Immettere il nome del gruppo di risorse nel campo **Filtra per nome**.
3. Selezionare il nome del gruppo di risorse.
4. Selezionare **Elimina gruppo di risorse** nel menu in alto.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato aggiunto un valore restituito al modello. Nell'esercitazione successiva verrà illustrato come esportare un modello e come usare parti del modello esportato nel modello.

> [!div class="nextstepaction"]
> [Usare il modello esportato](template-tutorial-export-template.md)
