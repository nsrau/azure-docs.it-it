---
title: Pacchetto FPGA per l'accelerazione hardware
titleSuffix: Azure Machine Learning service
description: Questo articolo è deprecato.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: reference
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 05/07/2018
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: 44c0818d7ddb418e34e6e124ddc0b27460bb24a0
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2018
ms.locfileid: "53262079"
---
# <a name="azure-machine-learning-hardware-acceleration-package"></a>Pacchetto di accelerazione hardware di Azure Machine Learning

>[!Note]
>**Questo articolo è deprecato.** Questo pacchetto FPGA è stato deprecato. È stato aggiunto il supporto per questa funzionalità per il SDK di Azure Machine Learning. Il supporto per questo pacchetto termina in modo incrementale. [Visualizzare la sequenza temporale del supporto](overview-what-happened-to-workbench.md#timeline). Informazioni sul [supporto FPGA](concept-accelerate-with-fpgas.md) aggiornato.

Il pacchetto di accelerazione hardware di Azure Machine Learning è un'estensione Python installabile con pip per Azure Machine Learning che consente ai data scientist e agli sviluppatori di intelligenza artificiale di eseguire rapidamente le operazioni seguenti:

+ Definire le caratteristiche delle immagini con una versione quantizzata di ResNet 50

+ Eseguire il training di classificatori in base a determinate funzionalità

+ Distribuire i modelli in dispositivi [FPGA (Field Programmable Gate Array)](concept-accelerate-with-fpgas.md) in Azure per inferenze a latenza estremamente bassa

## <a name="prerequisites"></a>Prerequisiti

>[!Note]
>Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare subito la [versione gratuita o a pagamento del servizio Azure Machine Learning](http://aka.ms/AMLFree).

1. Un account di Gestione modelli di Azure Machine Learning. Per altre informazioni sulla creazione dell'account, vedere [Guida introduttiva: Installare e iniziare a usare i servizi di Azure Machine Learning](../desktop-workbench/quickstart-installation.md). 

1. È necessario installare il pacchetto. 

 
## <a name="how-to-install-the-package"></a>Come installare il pacchetto

1. Scaricare e installare la versione più recente di [Git](https://git-scm.com/downloads).

2. Installare [Anaconda (Python 3.6)](https://conda.io/miniconda.html).

   Per scaricare un ambiente Anaconda preconfigurato, usare il comando seguente dal prompt di Git:

    ```
    git clone https://aka.ms/aml-real-time-ai
    ```
1. Per creare l'ambiente, aprire un**prompt di Anaconda** e usare il comando seguente:

    ```
    conda env create -f aml-real-time-ai/environment.yml
    ```

1. Per attivare l'ambiente, usare il comando seguente:

    ```
    conda activate amlrealtimeai
    ```

## <a name="sample-code"></a>Codice di esempio

Questo codice di esempio illustra l'uso dell'SDK per distribuire un modello in un dispositivo FPGA.

1. Importare il pacchetto:
   ```python
   import amlrealtimeai
   from amlrealtimeai import resnet50
   ```

1. Pre-elaborare l'immagine:
   ```python 
   from amlrealtimeai.resnet50.model import LocalQuantizedResNet50
   model_path = os.path.expanduser('~/models')
   model = LocalQuantizedResNet50(model_path)
   print(model.version)
   ```

1. Definire le caratteristiche delle immagini:
   ```python 
   from amlrealtimeai.resnet50.model import LocalQuantizedResNet50
   model_path = os.path.expanduser('~/models')
   model = LocalQuantizedResNet50(model_path)
   print(model.version)
   ```

1. Creare un classificatore:
   ```python
   model.import_graph_def(include_featurizer=False)
   print(model.classifier_input)
   print(model.classifier_output)
   ```

1. Creare la definizione del servizio:
   ```python
   from amlrealtimeai.pipeline import ServiceDefinition, TensorflowStage, BrainWaveStage
   save_path = os.path.expanduser('~/models/save')
   service_def_path = os.path.join(save_path, 'service_def.zip')

   service_def = ServiceDefinition()
   service_def.pipeline.append(TensorflowStage(tf.Session(), in_images, image_tensors))
   service_def.pipeline.append(BrainWaveStage(model))
   service_def.pipeline.append(TensorflowStage(tf.Session(), model.classifier_input, model.classifier_output))
   service_def.save(service_def_path)
   print(service_def_path)
   ```
 
1. Preparare il modello per l'esecuzione in un dispositivo FPGA:
   ```python
   from amlrealtimeai import DeploymentClient

   subscription_id = "<Your Azure Subscription ID>"
   resource_group = "<Your Azure Resource Group Name>"
   model_management_account = "<Your AzureML Model Management Account Name>"

   model_name = "resnet50-model"
   service_name = "quickstart-service"

   deployment_client = DeploymentClient(subscription_id, resource_group, model_management_account)
   ```

1. Distribuire il modello per l'esecuzione in un dispositivo FPGA:
   ```python
   service = deployment_client.get_service_by_name(service_name)
   model_id = deployment_client.register_model(model_name, service_def_path)

   if(service is None):
      service = deployment_client.create_service(service_name, model_id)    
   else:
      service = deployment_client.update_service(service.id, model_id)
   ```

1. Creare il client:
    ```python
   from amlrealtimeai import PredictionClient
   client = PredictionClient(service.ipAddress, service.port)  
   ```

1. Chiamare l'API:
   ```python
   image_file = R'C:\path_to_file\image.jpg'
   results = client.score_image(image_file)
   ```

## <a name="reporting-issues"></a>Segnalazione di problemi

Usare il [forum](https://aka.ms/aml-forum-service) per segnalare eventuali problemi riscontrati con il pacchetto.

## <a name="next-steps"></a>Passaggi successivi

[Deploy a model as a web service on an FPGA](how-to-deploy-fpga-web-service.md) (Distribuire un modello come servizio Web in un dispositivo FPGA)
