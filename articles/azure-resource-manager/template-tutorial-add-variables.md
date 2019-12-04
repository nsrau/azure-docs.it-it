---
title: 'Esercitazione: Aggiungere una variabile al modello'
description: Aggiungere variabili al modello di Azure Resource Manager per semplificare la sintassi.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 0598da3c060b8a8055ffb045fe4aae60b3806060
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406044"
---
# <a name="tutorial-add-variables-to-your-resource-manager-template"></a>Esercitazione: Aggiungere variabili al modello di Resource Manager

Questa esercitazione illustra come aggiungere una variabile al modello. Le variabili semplificano i modelli perché consentono di scrivere un'espressione una sola volta e di riutilizzarla nel modello. Per completare l'esercitazione, sono necessari **7 minuti**.

## <a name="prerequisites"></a>Prerequisiti

È consigliabile, ma non obbligatorio, completare l'[esercitazione sulle funzioni](template-tutorial-add-functions.md).

È necessario avere Visual Studio Code con l'estensione Strumenti di Resource Manager e Azure PowerShell oppure l'interfaccia della riga di comando di Azure. Per altre informazioni, vedere gli [strumenti per i modelli](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Rivedere il modello

Al termine dell'esercitazione precedente, il modello includeva il codice JSON seguente:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.json)]

Il parametro per il nome dell'account di archiviazione è difficile da usare perché richiede la specifica di un nome univoco. Se sono state completate le esercitazioni precedenti di questa serie, probabilmente non si ha voglia di formulare un nome univoco. Per risolvere questo problema, aggiungere una variabile che costruisce un nome univoco per l'account di archiviazione.

## <a name="use-variable"></a>Usare la variabile

Nell'esempio seguente sono evidenziate le modifiche per aggiungere al modello una variabile che crea un nome di account di archiviazione univoco. Copiare l'intero file e sostituire il modello con il contenuto del file.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.json?range=1-47&highlight=5-9,29-31,36)]

Si noti che include una variabile denominata **uniqueStorageName**. Questa variabile usa quattro funzioni per costruire un valore stringa.

La funzione [parameters](resource-group-template-functions-deployment.md#parameters) è già nota, quindi non verrà esaminata.

Anche la funzione [resourceGroup](resource-group-template-functions-resource.md#resourcegroup) è già nota. In questo caso, si ottiene la proprietà **id** invece della proprietà **location**, come illustrato nell'esercitazione precedente. La proprietà **id** restituisce l'identificatore completo del gruppo di risorse, inclusi l'ID sottoscrizione e il nome del gruppo di risorse.

La funzione [uniqueString](resource-group-template-functions-string.md#uniquestring) crea un valore hash di 13 caratteri. Il valore restituito dipende dai parametri passati. Per questa esercitazione come input per il valore hash si userà l'ID del gruppo di risorse. Questo significa che è possibile distribuire questo modello in più gruppi di risorse e ottenere un valore stringa univoco diverso. Si ottiene però lo stesso valore se si esegue la distribuzione nello stesso gruppo di risorse.

La funzione [concat](resource-group-template-functions-string.md#concat) accetta i valori e li combina. Per questa variabile accetta la stringa dal parametro e la stringa dalla funzione uniqueString e le combina in un'unica stringa.

Il parametro **storagePrefix** consente di passare un prefisso per facilitare l'identificazione degli account di archiviazione. È possibile creare una convenzione di denominazione personalizzata per agevolare l'identificazione degli account di archiviazione dopo la distribuzione da un lungo elenco di risorse.

Si noti infine che il nome dell'account di archiviazione è ora impostato sulla variabile e non su un parametro.

## <a name="deploy-template"></a>Distribuire il modello

A questo punto è possibile distribuire il modello. La distribuzione di questo modello è più semplice rispetto ai modelli precedenti perché viene specificato solo il prefisso del nome dell'account di archiviazione.

Se non è stato ancora creato il gruppo di risorse, vedere [Creare il gruppo di risorse](template-tutorial-create-first-template.md#create-resource-group). Nell'esempio si presuppone che la variabile **templateFile** sia stata impostata sul percorso del file modello, come illustrato nella [prima esercitazione](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnamevariable `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addnamevariable \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

## <a name="verify-deployment"></a>Verificare la distribuzione

Per verificare la distribuzione, esplorare il gruppo di risorse nel portale di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nel menu di sinistra selezionare **Gruppi di risorse**.
1. Selezionare il gruppo di risorse in cui è stata eseguita la distribuzione.
1. Si noterà che è stata distribuita una risorsa dell'account di archiviazione. Il nome dell'account di archiviazione è costituito dal prefisso **store** e da una stringa di caratteri casuali.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende passare all'esercitazione successiva, non è necessario eliminare il gruppo di risorse.

Se invece ci si ferma, è opportuno eliminare il gruppo di risorse per rimuovere le risorse distribuite.

1. Nel portale di Azure selezionare **Gruppo di risorse** nel menu a sinistra.
2. Immettere il nome del gruppo di risorse nel campo **Filtra per nome**.
3. Selezionare il nome del gruppo di risorse.
4. Selezionare **Elimina gruppo di risorse** nel menu in alto.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata aggiunta una variabile che consente di creare un nome univoco per un account di archiviazione. Nell'esercitazione successiva si restituirà un valore dall'account di archiviazione distribuito.

> [!div class="nextstepaction"]
> [Aggiungere gli output](template-tutorial-add-outputs.md)
