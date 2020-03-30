---
title: Come distribuire modelli in istanze del contenitore di AzureHow to deploy models to Azure Container Instances
titleSuffix: Azure Machine Learning
description: Informazioni su come distribuire i modelli di Azure Machine Learning come servizio Web usando le istanze del contenitore di Azure.Learn how to deploy your Azure Machine Learning models as a web service using Azure Container Instances.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 12/27/2019
ms.openlocfilehash: cb5603c12f06e4f5d5675ca27e171321ecc8827a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536446"
---
# <a name="deploy-a-model-to-azure-container-instances"></a>Distribuire un modello a Istanze di Azure Container
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Informazioni su come usare Azure Machine Learning per distribuire un modello come servizio Web in Istanze del contenitore di Azure.Learn how to use Azure Machine Learning to deploy a model as a web service on Azure Container Instances (ACI). Usare le istanze del contenitore di Azure se si verifica una delle condizioni seguenti:Use Azure Container Instances if one of the following conditions is true:

- È necessario distribuire e convalidare rapidamente il modello. Non è necessario creare i contenitori ACI in anticipo. Vengono creati come parte del processo di distribuzione.
- Si sta eseguendo il test di un modello in fase di sviluppo. 

Per informazioni sulla quota e sulla disponibilità di aree per l'aCI, vedere [l'articolo Quote e disponibilità dell'area per le istanze del contenitore](https://docs.microsoft.com/azure/container-instances/container-instances-quotas) di Azure.For information on quota and region availability for ACI, see Quotas and region availability for Azure Container Instances article.

## <a name="prerequisites"></a>Prerequisiti

- Un'area di lavoro di Azure Machine Learning. Per altre informazioni, vedere [Creare un'area](how-to-manage-workspace.md)di lavoro di Azure Machine Learning.For more information, see Create an Azure Machine Learning workspace .

- Un modello di apprendimento automatico registrato nell'area di lavoro. Se non si dispone di un modello registrato, vedere [Come e dove distribuire i modelli](how-to-deploy-and-where.md).

- [L'estensione dell'interfaccia della](reference-azure-machine-learning-cli.md)riga di comando di Azure per il servizio Machine Learning , [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)o l'estensione di codice di Visual Studio di Azure [Machine Learning](tutorial-setup-vscode-extension.md).

- I frammenti di codice __Python__ in questo articolo presuppongono che siano impostate le seguenti variabili:

    * `ws`- Impostare l'area di lavoro.
    * `model`- Impostare il modello registrato.
    * `inference_config`- Impostare la configurazione di inferenza per il modello.

    Per ulteriori informazioni sull'impostazione di queste variabili, vedere [Come e dove distribuire i modelli](how-to-deploy-and-where.md).

- I frammenti __CLI__ in questo articolo presuppongono che sia stato creato un `inferenceconfig.json` documento. Per ulteriori informazioni sulla creazione di questo documento, vedere [Come e dove distribuire i modelli](how-to-deploy-and-where.md).

## <a name="deploy-to-aci"></a>Distribuire in ACI

Per distribuire un modello in Istanze del contenitore di Azure, creare una configurazione di __distribuzione__ che descriva le risorse di calcolo necessarie. Ad esempio, numero di core e memoria. È inoltre necessaria una configurazione di __inferenza__, che descrive l'ambiente necessario per ospitare il modello e il servizio Web. Per ulteriori informazioni sulla creazione della configurazione di inferenza, vedere [Come e dove distribuire](how-to-deploy-and-where.md)i modelli .

### <a name="using-the-sdk"></a>Uso dell'SDK

```python
from azureml.core.webservice import AciWebservice, Webservice
from azureml.core.model import Model

deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Per ulteriori informazioni sulle classi, i metodi e i parametri utilizzati in questo esempio, vedere i seguenti documenti di riferimento:

* [AciWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)
* [Modello.deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice.wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>Uso dell'interfaccia della riga di comando

Per eseguire la distribuzione tramite l'interfaccia della riga di comando, utilizzare il comando seguente. Sostituire `mymodel:1` con il nome e la versione del modello registrato. Sostituire `myservice` con il nome per assegnare questo servizio:Replace with the name to give this service:

```azurecli-interactive
az ml model deploy -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

Per altre informazioni, vedere il riferimento alla distribuzione del [modello az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) 

## <a name="using-vs-code"></a>Uso di VS Code

Vedere [Distribuire i modelli con il codice VS](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model).

> [!IMPORTANT]
> Non è necessario creare un contenitore ACI per testare in anticipo. I contenitori ACI vengono creati in base alle esigenze.

## <a name="update-the-web-service"></a>Aggiornare il servizio Web

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Come distribuire un modello usando un'immagine Docker personalizzataHow to deploy a model using a custom Docker image](how-to-deploy-custom-docker-image.md)
* [Risoluzione dei problemi di distribuzione](how-to-troubleshoot-deployment.md)
* [Proteggere i servizi Web di Azure Machine Learning con SSL](how-to-secure-web-service.md)
* [Usare un modello di Machine Learning distribuito come servizio Web](how-to-consume-web-service.md)
* [Monitorare i modelli di Azure Machine Learning con Application InsightsMonitor your Azure Machine Learning models with Application Insights](how-to-enable-app-insights.md)
* [Raccogliere i dati per i modelli nell'ambiente di produzione](how-to-enable-data-collection.md)
