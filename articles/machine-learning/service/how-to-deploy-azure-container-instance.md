---
title: Come distribuire i modelli in istanze di contenitore di Azure
titleSuffix: Azure Machine Learning
description: Informazioni su come distribuire i modelli di Azure Machine Learning come servizio Web usando istanze di contenitore di Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/08/2019
ms.openlocfilehash: 27988edaf9d6fe70288352b0ba45945e32976d4d
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71034640"
---
# <a name="deploy-a-model-to-azure-container-instances"></a>Distribuire un modello in istanze di contenitore di Azure

Informazioni su come usare Azure Machine Learning per distribuire un modello come servizio Web in istanze di contenitore di Azure (ACI). Usare istanze di contenitore di Azure se si verifica una delle condizioni seguenti:

- È necessario distribuire e convalidare rapidamente il modello. Non è necessario creare in anticipo i contenitori ACI. Vengono creati come parte del processo di distribuzione.
- Si sta eseguendo il test di un modello in fase di sviluppo. 

Per informazioni sulla disponibilità di quote e aree per ACI, vedere l'articolo relativo alla [disponibilità di quote e aree per istanze di contenitore di Azure](https://docs.microsoft.com/azure/container-instances/container-instances-quotas) .

## <a name="prerequisites"></a>Prerequisiti

- Un'area di lavoro di Azure Machine Learning. Per altre informazioni, vedere [creare un'area di lavoro Azure Machine Learning](how-to-manage-workspace.md).

- Un modello di apprendimento automatico registrato nell'area di lavoro. Se non si dispone di un modello registrato, vedere [come e dove distribuire i modelli](how-to-deploy-and-where.md).

- Estensione dell'interfaccia della riga [di comando di Azure per il servizio Machine Learning](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)o l' [estensione di Visual Studio code Azure Machine Learning](how-to-vscode-tools.md).

- I frammenti di codice __Python__ in questo articolo presuppongono che siano impostate le variabili seguenti:

    * `ws`-Impostare sull'area di lavoro.
    * `model`: Impostare sul modello registrato.
    * `inference_config`-Impostare sulla configurazione di inferenza per il modello.

    Per ulteriori informazioni sull'impostazione di queste variabili, vedere [come e dove distribuire i modelli](how-to-deploy-and-where.md).

- I frammenti di codice dell' __interfaccia__ della riga di comando in questo articolo `inferenceconfig.json` presuppongono che sia stato creato un documento. Per ulteriori informazioni sulla creazione di questo documento, vedere [come e dove distribuire i modelli](how-to-deploy-and-where.md).

## <a name="deploy-to-aci"></a>Distribuire in ACI

Per distribuire un modello in istanze di contenitore di Azure, creare una __configurazione di distribuzione__ che descriva le risorse di calcolo necessarie. Ad esempio, numero di core e memoria. È inoltre necessaria una __configurazione di inferenza__, che descrive l'ambiente necessario per ospitare il modello e il servizio Web. Per ulteriori informazioni sulla creazione della configurazione di inferenza, vedere [come e dove distribuire i modelli](how-to-deploy-and-where.md).

### <a name="using-the-sdk"></a>Uso dell'SDK

```python
from azureml.core.webservice import AciWebservice, Webservice
from azureml.core.model import Model

deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Per ulteriori informazioni sulle classi, i metodi e i parametri utilizzati in questo esempio, vedere i documenti di riferimento seguenti:

* [AciWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-)
* [Model. deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-)
* [WebService. wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>Uso dell'interfaccia della riga di comando

Per eseguire la distribuzione usando l'interfaccia della riga di comando, usare il comando seguente. Sostituire `mymodel:1` con il nome e la versione del modello registrato. Sostituire `myservice` con il nome da assegnare al servizio:

```azurecli-interactive
az ml model deploy -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../../includes/machine-learning-service-aci-deploy-config.md)]

Per ulteriori informazioni, vedere il riferimento [AZ ml Model deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) . 

## <a name="using-vs-code"></a>Uso di VS Code

Vedere [distribuire i modelli con vs code](how-to-vscode-tools.md#deploy-and-manage-models).

> [!IMPORTANT]
> Non è necessario creare un contenitore ACI da testare in anticipo. I contenitori ACI vengono creati in base alle esigenze.

## <a name="update-the-web-service"></a>Aggiornare il servizio Web

[!INCLUDE [aml-update-web-service](../../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Come distribuire un modello usando un'immagine Docker personalizzata](how-to-deploy-custom-docker-image.md)
* [Risoluzione dei problemi di distribuzione](how-to-troubleshoot-deployment.md)
* [Proteggere i servizi Web di Azure Machine Learning con SSL](how-to-secure-web-service.md)
* [Usare un modello di Machine Learning distribuito come servizio Web](how-to-consume-web-service.md)
* [Monitorare i modelli di Azure Machine Learning con Application Insights](how-to-enable-app-insights.md)
* [Raccogliere i dati per i modelli nell'ambiente di produzione](how-to-enable-data-collection.md)
