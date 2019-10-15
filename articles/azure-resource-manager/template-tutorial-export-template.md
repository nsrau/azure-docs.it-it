---
title: 'Esercitazione: Esportare un modello di Azure Resource Manager dal portale di Azure'
description: Informazioni su come usare un modello esportato per completare lo sviluppo del modello.
services: azure-resource-manager
author: mumian
manager: carmonmills
ms.service: azure-resource-manager
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 6e4f246cac0ecc1ab5942e522595f59c3625db8f
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243216"
---
# <a name="tutorial-use-exported-template-from-the-azure-portal"></a>Esercitazione: Usare il modello esportato dal portale di Azure

In questa serie di esercitazioni è stato creato un modello per distribuire un account di archiviazione di Azure. Nelle due esercitazioni successive verrà aggiunto un *piano di servizio app* e un *sito Web*. Invece di creare modelli da zero, verrà illustrato come esportare i modelli dal portale di Azure e come usare i modelli di esempio dei [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/) e si personalizzeranno tali modelli per l'uso. Questa esercitazione è incentrata sull'esportazione di modelli e sulla personalizzazione del risultato per il modello. Per completare l'esercitazione, sono necessari circa **14 minuti**.

## <a name="prerequisites"></a>Prerequisiti

È consigliabile, ma non obbligatorio, completare l'[esercitazione sugli output](template-tutorial-add-outputs.md).

È necessario avere Visual Studio Code con l'estensione Strumenti di Resource Manager e Azure PowerShell oppure l'interfaccia della riga di comando di Azure. Per altre informazioni, vedere gli [strumenti per i modelli](template-tutorial-create-first-template.md#get-tools).

## <a name="review-your-template"></a>Esaminare il modello

Al termine dell'esercitazione precedente, il modello includeva il codice JSON seguente:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json)]

Questo modello è adatto per la distribuzione di account di archiviazione, ma è possibile che si voglia aggiungere altre risorse. È possibile esportare un modello da una risorsa esistente per ottenere rapidamente il codice JSON per tale risorsa.

## <a name="create-app-service-plan"></a>Creare un piano di servizio app

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare **Crea una risorsa**.
1. In **Cerca nel Marketplace** immettere **Piano di servizio app** e quindi selezionare **Piano di servizio app**.  Non selezionare **Piano di servizio app (versione classica)** .
1. Selezionare **Create** (Crea).
1. Digitare:

    - **Sottoscrizione**: selezionare la sottoscrizione di Azure.
    - **Gruppo di risorse**: selezionare **Crea nuovo** e quindi specificare un nome. Specificare un nome di gruppo di risorse diverso rispetto a quello usato in questa serie di esercitazioni.
    - **Nome**: immettere un nome per il piano di servizio app.
    - **Sistema operativo**: selezionare **Linux**.
    - **Località**: selezionare una località di Azure. Ad esempio **Stati Uniti centrali**.
    - **Piano tariffario**: per ridurre i costi, modificare lo SKU in **Basic B1** (in Sviluppo/test).

    ![Modello di Resource Manager - Esporta modello - Portale](./media/template-tutorial-export-template/resource-manager-template-export.png)
1. Selezionare **Rivedi e crea**.
1. Selezionare **Create** (Crea). La creazione della risorsa richiede alcuni minuti.

## <a name="export-the-template"></a>Esportare il modello

1. Selezionare **Vai alla risorsa**.

    ![Vai alla risorsa](./media/template-tutorial-export-template/resource-manager-template-export-go-to-resource.png)

1. Selezionare **Esporta modello**.

    ![Modello di Resource Manager - Esporta modello](./media/template-tutorial-export-template/resource-manager-template-export-template.png)

   La funzionalità Esporta modello accetta lo stato corrente di una risorsa e genera un modello per distribuirlo. L'esportazione di un modello può essere utile per ottenere rapidamente il codice JSON necessario per distribuire una risorsa.

1. Copiare la definizione **Microsoft.Web/serverfarms** e la definizione del parametro nel modello.

    ![Modello di Resource Manager - Esporta modello - Modello esportato](./media/template-tutorial-export-template/resource-manager-template-exported-template.png)

> [!IMPORTANT]
> In genere, il modello esportato è più dettagliato di quanto necessario quando si crea un modello. Ad esempio, l'oggetto SKU nel modello esportato include cinque proprietà. Questo modello funziona, ma magari si preferisce usare solo la proprietà **name**. È possibile iniziare con il modello esportato e quindi modificarlo in base alle esigenze.

## <a name="revise-the-existing-template"></a>Modificare il modello esistente

Il modello esportato include la maggior parte del codice JSON necessario, che però deve essere personalizzato per il modello. Prestare particolare attenzione alle differenze tra parametri e variabili tra il modello e il modello esportato. Ovviamente, il processo di esportazione non conosce i parametri e le variabili già definiti nel modello.

L'esempio seguente evidenzia le aggiunte apportate al modello. Contiene infatti il codice esportato oltre ad alcune modifiche. In primo luogo, modifica il nome del parametro in modo che corrisponda alla convenzione di denominazione. Quindi, usa il parametro location per la località del piano di servizio app. Infine rimuove la proprietà **name** all'interno dell'oggetto **properties** perché questo valore è ridondante con la proprietà **name** a livello di risorsa.

Copiare l'intero file e sostituire il modello con il contenuto del file.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.json?range=1-77&highlight=28-31,50-69)]

## <a name="deploy-the-template"></a>Distribuire il modello

Per distribuire un modello, usare l'interfaccia della riga di comando di Azure o Azure PowerShell.

Se non è stato ancora creato il gruppo di risorse, vedere [Creare il gruppo di risorse](template-tutorial-create-first-template.md#create-resource-group). Nell'esempio si presuppone che la variabile **templateFile** sia stata impostata sul percorso del file modello, come illustrato nella [prima esercitazione](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addappserviceplan `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addappserviceplan \
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
1. Il gruppo di risorse contiene un account di archiviazione e un piano di servizio app.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende passare all'esercitazione successiva, non è necessario eliminare il gruppo di risorse.

Se invece ci si ferma, è opportuno eliminare il gruppo di risorse per rimuovere le risorse distribuite.

1. Nel portale di Azure selezionare **Gruppo di risorse** nel menu a sinistra.
2. Immettere il nome del gruppo di risorse nel campo **Filtra per nome**.
3. Selezionare il nome del gruppo di risorse.
4. Selezionare **Elimina gruppo di risorse** nel menu in alto.

## <a name="next-steps"></a>Passaggi successivi

È stato illustrato come esportare un modello dal portale di Azure e come usare il modello esportato per lo sviluppo di modelli. Per semplificare lo sviluppo di modelli, è anche possibile usare i modelli di avvio rapido di Azure.

> [!div class="nextstepaction"]
> [Usare i modelli di avvio rapido di Azure](template-tutorial-quickstart-template.md)
