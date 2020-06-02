---
title: "Esercitazione: Creare un'area di lavoro di Azure ML - Modello di Resource Manager"
description: In questa esercitazione si userà un modello di Azure Resource Manager per distribuire rapidamente un'area di lavoro di Azure per Machine Learning
services: machine-learning
author: lobrien
ms.author: laobri
ms.custom: subject-armqs
ms.date: 05/26/2020
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.openlocfilehash: b6eac711ed6c420330b036cb6094c841508cee9f
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83790716"
---
# <a name="tutorial-deploy-an-azure-machine-learning-workspace-using-a-resource-manager-template"></a>Esercitazione: Distribuire un'area di lavoro di Azure Machine Learning con un modello di Resource Manager
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questa esercitazione illustra come creare un'area di lavoro di Azure Machine Learning con un modello di Azure Resource Manager. Le aree di lavoro di Azure Machine Learning organizzano tutte le risorse di Machine Learning da set di dati di base in modelli distribuiti. Le aree di lavoro sono un'unica posizione in cui collaborare con i colleghi per la creazione, l'esecuzione e la revisione degli esperimenti, gestire le risorse di calcolo per il training e l'inferenza nonché monitorare e controllare la versione dei modelli distribuiti.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://aka.ms/AMLFree) prima di iniziare

* Per usare i comandi dell'interfaccia della riga di comando in questo documento dall'**ambiente locale**, è necessaria l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="create-a-workspace"></a>Creare un'area di lavoro

### <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-machine-learning-create/).

:::code language="json" source="~/quickstart-templates/101-machine-learning-create/azuredeploy.json":::

Nel modello sono definite le risorse seguenti:

* [Microsoft.MachineLearningServices/workspaces](/azure/templates/microsoft.machinelearningservices/workspaces): Creare un'area di lavoro Azure ML In questo modello la posizione e il nome sono parametri che l'utente può passare o immettere in modo interattivo.

### <a name="deploy-the-template"></a>Distribuire il modello 

Per usare il modello dall'interfaccia della riga di comando di Azure, accedere e scegliere la sottoscrizione (vedere [Accedere tramite l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)). Eseguire quindi:

```azurecli-interactive
read -p "Enter a project name that is used for generating resource names:" projectName &&
read -p "Enter the location (i.e. centralus):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-machine-learning-create/azuredeploy.json" &&
resourceGroupName="${projectName}rg" &&
workspaceName="${projectName}ws" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri $templateUri --parameters workspaceName=$workspaceName location=$location && 
echo "Press [ENTER] to continue ..." &&
read
```

Quando si esegue il comando precedente, immettere:

1. Nome del progetto che formerà la base dei nomi del gruppo di risorse e dell'area di lavoro di Azure ML creati
1. Località di Azure in cui si vuole eseguire la distribuzione

## <a name="review-deployed-resources"></a>Esaminare le risorse distribuite

Per visualizzare l'area di lavoro di Azure ML:

1. Passare a https://portal.azure.com. 
1. Accesso 
1. Scegliere l'area di lavoro appena creata

Verrà visualizzata la home page di Azure Machine Learning: 

:::image type="content" source="media/tutorial-resource-manager-workspace/workspace-home.png" alt-text="Screenshot dell'area di lavoro di Azure ML":::

Per visualizzare tutte le risorse associate alla distribuzione, fare clic sul collegamento in alto a sinistra con il nome dell'area di lavoro (nella schermata `my_templated_ws`). Tale collegamento consente di accedere al gruppo di risorse nel portale di Azure. Il nome del gruppo di risorse è `{projectName}rg` e l'area di lavoro è denominata `{projectName}ws`.

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si vuole usare questa area di lavoro, eliminarla. Poiché l'area di lavoro è associata ad altre risorse, ad esempio un account di archiviazione, è probabile che si voglia eliminare l'intero gruppo di risorse creato. È possibile eliminare il gruppo di risorse tramite il portale facendo clic sul pulsante "Elimina" e confermando l'operazione. In alternativa, è possibile eliminare il gruppo di risorse dall'interfaccia della riga di comando con: 

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata creata un'area di lavoro di Azure Machine Learning da un modello di Azure Resource Manager. Se si vuole esplorare Azure Machine Learning, continuare con l'esercitazione. 

> [!div class="nextstepaction"]
> [Esercitazione: Introduzione alla creazione del primo esperimento di Machine Learning con Python SDK](tutorial-1st-experiment-sdk-setup.md)
