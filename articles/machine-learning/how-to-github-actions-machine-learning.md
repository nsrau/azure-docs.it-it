---
title: Azioni di GitHub per CI/CD
titleSuffix: Azure Machine Learning
description: Informazioni su come creare un flusso di lavoro di azioni GitHub per eseguire il training di un modello in Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: juliakm
ms.author: jukullam
ms.date: 10/19/2020
ms.topic: conceptual
ms.custom: github-actions-azure
ms.openlocfilehash: 4336827dc7f8cb45f04e4cef94d79d1e6409d5c0
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92795211"
---
# <a name="use-github-actions-with-azure-machine-learning"></a>Usare le azioni di GitHub con Azure Machine Learning

Inizia a usare le [azioni di GitHub](https://help.github.com/en/articles/about-github-actions) per eseguire il training di un modello in Azure Machine Learning. 

> [!NOTE]
> Le azioni di GitHub per Azure Machine Learning vengono fornite così come sono e non sono completamente supportate da Microsoft. Se si verificano problemi con un'azione specifica, aprire un problema nel repository per l'azione. Se, ad esempio, si verifica un problema con l'azione AML-deploy, segnalare il problema nel [https://github.com/Azure/aml-deploy]( https://github.com/Azure/aml-deploy) repository.

## <a name="prerequisites"></a>Prerequisiti 

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un account GitHub. Se non si ha un accesso, iscriversi [gratuitamente](https://github.com/join).  

## <a name="workflow-file-overview"></a>Panoramica dei file del flusso di lavoro

Un flusso di lavoro viene definito da un file YAML (con estensione yml) nel percorso `/.github/workflows/` del repository. Questa definizione contiene i vari passaggi e i parametri che costituiscono il flusso di lavoro.

Il file è costituito da quattro sezioni:

|Sezione  |Attività  |
|---------|---------|
|**autenticazione** | 1. Definire un'entità servizio. <br /> 2. Creare un segreto GitHub. |
|**Connettere** | 1. connettersi all'area di lavoro di machine learning. <br /> 2. connettersi a una destinazione di calcolo. |
|**Esegui** | 1. inviare un'esecuzione di training. |
|**Distribuzione** | 1. registrare il modello nel registro di sistema Azure Machine Learning. 1. Distribuire il modello. |

## <a name="create-repository"></a>Crea repository

Creare un nuovo repository da Machine Learning [con azioni GitHub e Azure Machine Learning modello](https://github.com/machine-learning-apps/ml-template-azure). 

1. Aprire il [modello](https://github.com/machine-learning-apps/ml-template-azure) in GitHub. 
2. Selezionare **Usa questo modello** . 

    :::image type="content" source="media/how-to-github-actions-machine-learning/gh-actions-use-template.png" alt-text="Selezionare Usa questo modello":::
3. Creare un nuovo repository dal modello. Impostare il nome del repository su `ml-learning` o il nome desiderato. 


## <a name="generate-deployment-credentials"></a>Generare le credenziali per la distribuzione

È possibile creare un'[entità servizio](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) con il comando [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) dell'[interfaccia della riga di comando di Azure](/cli/azure/). Eseguire questo comando con [Azure Cloud Shell](https://shell.azure.com/) nel portale di Azure oppure selezionando il pulsante **Prova** .

```azurecli-interactive
az ad sp create-for-rbac --name "myML" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name> \
                            --sdk-auth
```

Nell'esempio precedente sostituire i segnaposto con l'ID sottoscrizione, il nome del gruppo di risorse e il nome dell'app. L'output è un oggetto JSON con le credenziali di assegnazione di ruolo che consentono di accedere all'app del servizio app in modo simile a quanto riportato di seguito. Copiare questo oggetto JSON per un momento successivo.

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

## <a name="configure-the-github-secret"></a>Configurare il segreto di GitHub

1. In [GitHub](https://github.com/)esplorare il repository, selezionare **Impostazioni > Secrets > aggiungere un nuovo segreto** .

2. Incollare l'intero output JSON del comando dell'interfaccia della riga di comando di Azure nel campo del valore del segreto. Assegnare al segreto il nome `AZURE_CREDENTIALS`.

## <a name="connect-to-the-workspace"></a>Connettersi all'area di lavoro

Usare l' [azione area di lavoro di Azure Machine Learning](https://github.com/marketplace/actions/azure-machine-learning-workspace) per connettersi all'area di lavoro di Azure Machine Learning. 

```yaml
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```

Per impostazione predefinita, l'azione prevede un `workspace.json` file. Se il file JSON ha un nome diverso, è possibile specificarlo con il `parameters_file` parametro di input. Se non è presente un file, ne verrà creato uno nuovo con il nome del repository.


```yaml
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          parameters_file: "alternate_workspace.json"
```
L'azione scrive le proprietà dell'area di lavoro Azure Resource Manager (ARM) in un file di configurazione, che verrà selezionato da tutte le azioni future Azure Machine Learning GitHub. Il file viene salvato in `GITHUB_WORKSPACE/aml_arm_config.json` . 

## <a name="connect-to-a-compute-target-in-azure-machine-learning"></a>Connettersi a una destinazione di calcolo in Azure Machine Learning

Usare l' [azione di calcolo Azure Machine Learning](https://github.com/Azure/aml-compute) per connettersi a una destinazione di calcolo in Azure Machine Learning.  Se la destinazione di calcolo esiste, l'azione si connetterà a tale destinazione. In caso contrario, l'azione creerà una nuova destinazione di calcolo. L' [azione di calcolo AML](https://github.com/Azure/aml-compute) supporta solo il cluster di calcolo di Azure ml e il servizio Azure KUBERNETES (AKS). 

```yaml
    - name: Connect/Create Azure Machine Learning Compute Target
      id: aml_compute_training
      uses: Azure/aml-compute@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```
## <a name="submit-training-run"></a>Inviare l'esecuzione di training

Usare l' [azione di Training Azure Machine Learning](https://github.com/Azure/aml-run) per inviare un scriptrun, uno strumento di stima o una Pipeline per Azure Machine Learning. 

```yaml
    - name: Submit training run
      id: aml_run
      uses: Azure/aml-run@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```

## <a name="register-model-in-registry"></a>Registrare il modello nel registro di sistema

Usare l' [azione Azure Machine Learning registra modello](https://github.com/Azure/aml-registermodel) per registrare un modello per la Azure Machine Learning.

```yaml
    - name: Register model
      id: aml_registermodel
      uses: Azure/aml-registermodel@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          run_id:  ${{ steps.aml_run.outputs.run_id }}
          experiment_name: ${{ steps.aml_run.outputs.experiment_name }}
```

## <a name="deploy-model-to-azure-machine-learning-to-aci"></a>Distribuire il modello in Azure Machine Learning ad ACI

Utilizzare l' [azione Azure Machine Learning Distribuisci](https://github.com/Azure/aml-deploy) per distribuire un modello e creare un endpoint per il modello. È anche possibile usare la distribuzione di Azure Machine Learning per eseguire la distribuzione nel servizio Azure Kubernetes. Vedere [questo flusso di lavoro di esempio](https://github.com/Azure-Samples/mlops-enterprise-template) per un modello distribuito nel servizio Azure Kubernetes.

```yaml
    - name: Deploy model
      id: aml_deploy
      uses: Azure/aml-deploy@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          model_name:  ${{ steps.aml_registermodel.outputs.model_name }}
          model_version: ${{ steps.aml_registermodel.outputs.model_version }}

```

## <a name="complete-example"></a>Esempio completo

Eseguire il training del modello e distribuirlo in Azure Machine Learning. 

```yaml
# Actions train a model on Azure Machine Learning
name: Azure Machine Learning training and deployment
on:
  push:
    branches:
      - master
    # paths:
    #   - 'code/*'
jobs:
  train:
    runs-on: ubuntu-latest
    steps:
    # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
    - name: Check Out Repository
      id: checkout_repository
      uses: actions/checkout@v2
        
    # Connect or Create the Azure Machine Learning Workspace
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Connect or Create a Compute Target in Azure Machine Learning
    - name: Connect/Create Azure Machine Learning Compute Target
      id: aml_compute_training
      uses: Azure/aml-compute@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Submit a training run to the Azure Machine Learning
    - name: Submit training run
      id: aml_run
      uses: Azure/aml-run@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}

    # Register model in Azure Machine Learning model registry
    - name: Register model
      id: aml_registermodel
      uses: Azure/aml-registermodel@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          run_id:  ${{ steps.aml_run.outputs.run_id }}
          experiment_name: ${{ steps.aml_run.outputs.experiment_name }}

    # Deploy model in Azure Machine Learning to ACI
    - name: Deploy model
      id: aml_deploy
      uses: Azure/aml-deploy@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          model_name:  ${{ steps.aml_registermodel.outputs.model_name }}
          model_version: ${{ steps.aml_registermodel.outputs.model_version }}

```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il gruppo di risorse e il repository non sono più necessari, pulire le risorse distribuite eliminando il gruppo di risorse e il repository GitHub. 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare ed eseguire le pipeline di Machine Learning con Azure Machine Learning SDK](how-to-create-your-first-pipeline.md)
