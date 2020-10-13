---
title: Distribuire modelli ML in FPGA
titleSuffix: Azure Machine Learning
description: Informazioni sulle matrici di Gate programmabili da campo. È possibile distribuire un servizio Web in un FPGA con Azure Machine Learning per l'inferenza di latenza estremamente bassa.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: jordane
author: jpe316
ms.date: 09/24/2020
ms.topic: conceptual
ms.custom: how-to, contperfq2, devx-track-python, deploy
ms.openlocfilehash: 18b1c155c0bb85e346ec28d5c145e6578ca3ec48
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91999076"
---
# <a name="deploy-ml-models-to-field-programmable-gate-arrays-fpgas-with-azure-machine-learning"></a>Distribuire modelli di Machine Learning in FPGA (Field-Programmable Gate Array) con Azure Machine Learning 

In questo articolo vengono fornite informazioni sugli FPGA e su come distribuire i modelli ML in una scheda Azure FPGA usando il [pacchetto python dei modelli con accelerazione hardware](https://docs.microsoft.com/python/api/azureml-accel-models/azureml.accel?view=azure-ml-py&preserve-view=true) da [Azure Machine Learning](overview-what-is-azure-ml.md).

## <a name="what-are-fpgas"></a>Che cosa sono gli FPGA?
Un circuito FPGA contiene un array di blocchi programmabili per la logica e una gerarchia di interconnessioni riconfigurabili. Le interconnessioni consentono di configurare questi blocchi in diversi modi dopo la produzione. I circuiti FPGA offrono una combinazione di programmabilità e prestazioni superiore agli altri chip. 

Gli FPGA consentono di ottenere una bassa latenza per le richieste di inferenza in tempo reale (o punteggio del modello). Non sono necessarie richieste asincrone (invio in batch). L'invio in batch può causare latenza perché rende necessario elaborare più dati. Le implementazioni delle unità di elaborazione neurale non richiedono l'invio in batch; la latenza può pertanto essere più bassa rispetto ai processori CPU e GPU.

I circuiti FPGA possono essere riconfigurati per diversi tipi di modelli di Machine Learning. Questa flessibilità consente di accelerare le applicazioni basandosi sul modello ottimale di memoria e precisione numerica utilizzato. Dal momento che sono riconfigurabili, è possibile rimanere al passo con i requisiti degli algoritmi AI in costante evoluzione.

![Diagramma del confronto Azure Machine Learning FPGA](./media/how-to-deploy-fpga-web-service/azure-machine-learning-fpga-comparison.png)

|Processore| Abbreviazione |Descrizione|
|---|:-------:|------|
|Application-specific integrated circuit|ASIC|I circuiti personalizzati, ad esempio Google TensorFlow Processor Unit (TPU), offrono la massima efficienza. Non possono essere riconfigurati in base alle esigenze.|
|Field-programmable Gate Arrays|FPGA|I circuiti FPGA, ad esempio quelli disponibili in Azure, assicurano prestazioni simili a quelle dei circuiti ASIC. Sono flessibili e possono essere riconfigurati nel corso del tempo per implementare la nuova logica.|
|Graphics processing unit|GPU|Una scelta comune per i calcoli di intelligenza artificiale che offre funzionalità di elaborazione parallela e rende più veloce il rendering delle immagini rispetto alle CPU.|
|Central processing unit|CPU|Processori generici, le cui prestazioni non sono ideali per l'elaborazione di video e grafica.|

## <a name="fpga-support-in-azure"></a>Supporto per FPGA in Azure

Microsoft Azure ha realizzato l'investimento in dispositivi FPGA per il cloud più importante al mondo. Microsoft usa gli FPGA per la valutazione DNN (Deep Neural Network), la classificazione di ricerca Bing e l'accelerazione SDN (Software Defined Networking) per ridurre la latenza e liberare le CPU per altre attività.

Gli FPGA in Azure si basano sui dispositivi FPGA di Intel, che i data scientist e gli sviluppatori usano per accelerare i calcoli di intelligenza artificiale in tempo reale. L'architettura abilitata per i circuiti FPGA offre un livello elevato di prestazioni, flessibilità, scalabilità ed è disponibile in Azure.

Azure FPGA è integrato con Azure Machine Learning. Azure può parallelizzare DNN pre-sottoposte a training tra gli FPGA per scalare orizzontalmente il servizio. È possibile eseguire training preliminare sulle reti neurali profonde, per utilità di funzioni complete per il trasferimento di apprendimento o per ottimizzazione con pesi aggiornati.

|Scenari & configurazioni in Azure|Modelli DNN supportati|Supporto locale|
|--------------------------|--------------------|----------------|
|+ Scenari di classificazione e riconoscimento delle immagini<br/>+ Distribuzione TensorFlow (richiede Tensorflow 1. x)<br/>+ Hardware Intel FPGA|-ResNet 50<br/>-ResNet 152<br/>-DenseNet-121<br/>-VGG-16<br/>-SSD-VGG|-Stati Uniti orientali<br/>-Asia sudorientale<br/> - Europa occidentale<br/>-Stati Uniti occidentali 2|

Per ottimizzare la latenza e la velocità effettiva, il client che invia i dati al modello FPGA deve trovarsi in una delle aree precedenti (quella in cui è stato distribuito il modello).

La **famiglia di macchine virtuali di Azure PBS** contiene Intel ARRIA 10 FPGA. Viene visualizzato come "famiglia PBS standard vCPU" quando si controlla l'allocazione delle quote di Azure. La VM PB6 ha sei vCPU e un FPGA. Il provisioning della macchina virtuale PB6 viene effettuato automaticamente da Azure Machine Learning durante la distribuzione del modello in un FPGA. Viene usato solo con Azure ML e non può eseguire Bitstream arbitrari. Ad esempio, non sarà possibile eseguire il flashing di FPGA con Bitstream per eseguire operazioni di crittografia, codifica e così via.

## <a name="deploy-models-on-fpgas"></a>Distribuire modelli in dispositivi FPGA

È possibile distribuire un modello come servizio Web in FPGA con [Azure Machine Learning modelli con accelerazione hardware](https://docs.microsoft.com/python/api/azureml-accel-models/azureml.accel?view=azure-ml-py&preserve-view=true). L'uso di FPGA fornisce un'inferenza di latenza estremamente bassa, anche con una singola dimensione del batch. 

In questo esempio viene creato un grafico TensorFlow per pre-elaborare l'immagine di input, impostarla come featurizer usando ResNet 50 in un FPGA, quindi eseguire le funzionalità tramite un classificatore sottoposto a training sul set di dati improprio. Quindi, il modello viene distribuito in un cluster AKS.

### <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Se non si ha un account, creare un account [con pagamento in base](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go) al consumo (gli account Azure gratuiti non sono idonei per la quota FPGA).

- Un'area di lavoro Azure Machine Learning e l'SDK Azure Machine Learning per Python installato, come descritto in [creare un'area di lavoro](how-to-manage-workspace.md).
 
- Il pacchetto di modelli con accelerazione hardware:  `pip install --upgrade azureml-accel-models[cpu]`    
    
- L'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)

- Quota FPGA. Inviare una [richiesta di quota](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2nac9-PZhBDnNSV2ITz0LNUN0U5S0hXRkNITk85QURTWk9ZUUFUWkkyTC4u)oppure eseguire questo comando dell'interfaccia della riga di comando per controllare la quota: 

    ```azurecli-interactive
    az vm list-usage --location "eastus" -o table --query "[?localName=='Standard PBS Family vCPUs']"
    ```

   Assicurarsi di avere almeno 6 vCPU in __currentValue__ restituiti.  

### <a name="define-the-tensorflow-model"></a>Definire il modello TensorFlow

Per iniziare, usare l' [SDK Azure Machine Learning per Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true) per creare una definizione del servizio. Una definizione del servizio è un file che descrive una pipeline di grafici (input, utilità di funzioni e classificatore) basata su TensorFlow. Il comando di distribuzione comprime la definizione e i grafici in un file ZIP e carica il file ZIP nell'archiviazione BLOB di Azure. Il DNN è già distribuito per l'esecuzione nell'FPGA.

1. Carica Azure Machine Learning area di lavoro

   ```python
   import os
   import tensorflow as tf
   
   from azureml.core import Workspace
  
   ws = Workspace.from_config()
   print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep='\n')
   ```

1. Pre-elaborazione immagine. L'input per il servizio Web è un'immagine JPEG.  Il primo passaggio consiste nel decodificare l'immagine JPEG e pre-elaborarla.  Le immagini JPEG vengono considerate come stringhe e il risultato è costituito da tensori che saranno l'input per il modello ResNet 50.

   ```python
   # Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
   import azureml.accel.models.utils as utils
   tf.reset_default_graph()
   
   in_images = tf.placeholder(tf.string)
   image_tensors = utils.preprocess_array(in_images)
   print(image_tensors.shape)
   ```

1. Caricare featurizer. Inizializzare il modello e scaricare un checkpoint TensorFlow della versione quantizzata ResNet50 da usare come utilità di funzioni.  Sostituire "QuantizedResnet50" nel frammento di codice per importare altre reti neurali profonde:

   - QuantizedResnet152
   - QuantizedVgg16
   - Densenet121

   ```python
   from azureml.accel.models import QuantizedResnet50
   save_path = os.path.expanduser('~/models')
   model_graph = QuantizedResnet50(save_path, is_frozen=True)
   feature_tensor = model_graph.import_graph_def(image_tensors)
   print(model_graph.version)
   print(feature_tensor.name)
   print(feature_tensor.shape)
   ```

1. Aggiungere un classificatore. Questo classificatore è stato sottoposto a training sul set di dati di imagent.

   ```python
   classifier_output = model_graph.get_default_classifier(feature_tensor)
   print(classifier_output)
   ```

1. L'utente salva il modello. Ora che sono stati caricati il preprocessore ResNet 50 featurizer e il classificatore, salvare il grafo e le variabili associate come modello.

   ```python
   model_name = "resnet50"
   model_save_path = os.path.join(save_path, model_name)
   print("Saving model in {}".format(model_save_path))
   
   with tf.Session() as sess:
       model_graph.restore_weights(sess)
       tf.saved_model.simple_save(sess, model_save_path,
                                  inputs={'images': in_images},
                                  outputs={'output_alias': classifier_output})
   ```

1. Consente di salvare i tensori di input e di output **, che vengono usati per la conversione dei modelli e le richieste di inferenza**. 

   ```python
   input_tensors = in_images.name
   output_tensors = classifier_output.name

   print(input_tensors)
   print(output_tensors)
   ```

   I modelli seguenti sono elencati con i rispettivi tensori di output di classificazione per l'inferenza se è stato usato il classificatore predefinito.

   + Resnet50, QuantizedResnet50
     ```python
     output_tensors = "classifier_1/resnet_v1_50/predictions/Softmax:0"
     ```
   + Resnet152, QuantizedResnet152
     ```python
     output_tensors = "classifier/resnet_v1_152/predictions/Softmax:0"
     ```
   + Densenet121, QuantizedDensenet121
     ```python
     output_tensors = "classifier/densenet121/predictions/Softmax:0"
     ```
   + Vgg16, QuantizedVgg16
     ```python
     output_tensors = "classifier/vgg_16/fc8/squeezed:0"
     ```
   + SsdVgg, QuantizedSsdVgg
     ```python
     output_tensors = ['ssd_300_vgg/block4_box/Reshape_1:0', 'ssd_300_vgg/block7_box/Reshape_1:0', 'ssd_300_vgg/block8_box/Reshape_1:0', 'ssd_300_vgg/block9_box/Reshape_1:0', 'ssd_300_vgg/block10_box/Reshape_1:0', 'ssd_300_vgg/block11_box/Reshape_1:0', 'ssd_300_vgg/block4_box/Reshape:0', 'ssd_300_vgg/block7_box/Reshape:0', 'ssd_300_vgg/block8_box/Reshape:0', 'ssd_300_vgg/block9_box/Reshape:0', 'ssd_300_vgg/block10_box/Reshape:0', 'ssd_300_vgg/block11_box/Reshape:0']
     ```

### <a name="convert-the-model-to-the-open-neural-network-exchange-format-onnx"></a>Convertire il modello nel formato Open Neural Network Exchange (ONNX)

Prima di poter distribuire in FPGA, convertire il modello nel formato [ONNX](https://onnx.ai/) .

1. [Registrare](concept-model-management-and-deployment.md) il modello usando l'SDK con il file zip nell'archiviazione BLOB di Azure. L'aggiunta di tag e altri metadati sul modello consente di tenere traccia dei modelli sottoposti a training.

   ```python
   from azureml.core.model import Model

   registered_model = Model.register(workspace=ws,
                                     model_path=model_save_path,
                                     model_name=model_name)

   print("Successfully registered: ", registered_model.name,
         registered_model.description, registered_model.version, sep='\t')
   ```

   Se è già stato registrato un modello e si vuole caricarlo, è possibile recuperarlo.

   ```python
   from azureml.core.model import Model
   model_name = "resnet50"
   # By default, the latest version is retrieved. You can specify the version, i.e. version=1
   registered_model = Model(ws, name="resnet50")
   print(registered_model.name, registered_model.description,
         registered_model.version, sep='\t')
   ```

1. Convertire il grafico TensorFlow nel formato ONNX.  È necessario specificare i nomi dei tensori di input e di output, in modo che il client possa utilizzarli quando si utilizza il servizio Web.

   ```python
   from azureml.accel import AccelOnnxConverter

   convert_request = AccelOnnxConverter.convert_tf_model(
       ws, registered_model, input_tensors, output_tensors)

   # If it fails, you can run wait_for_completion again with show_output=True.
   convert_request.wait_for_completion(show_output=False)

   # If the above call succeeded, get the converted model
   converted_model = convert_request.result
   print("\nSuccessfully converted: ", converted_model.name, converted_model.url, converted_model.version,
         converted_model.id, converted_model.created_time, '\n')
   ```

### <a name="containerize-and-deploy-the-model"></a>Distribuire e distribuire il modello

Successivamente, creare un'immagine Docker dal modello convertito e da tutte le dipendenze.  È quindi possibile distribuire e creare un'istanza dell'immagine docker.  Le destinazioni di distribuzione supportate includono Azure Kubernetes Service (AKS) nel cloud o un dispositivo perimetrale, ad esempio [Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview).  È anche possibile aggiungere tag e descrizioni per l'immagine Docker registrata.

   ```python
   from azureml.core.image import Image
   from azureml.accel import AccelContainerImage

   image_config = AccelContainerImage.image_configuration()
   # Image name must be lowercase
   image_name = "{}-image".format(model_name)

   image = Image.create(name=image_name,
                        models=[converted_model],
                        image_config=image_config,
                        workspace=ws)
   image.wait_for_creation(show_output=False)
   ```

   Elencare le immagini in base al tag e ottenere i log dettagliati per qualsiasi debug.

   ```python
   for i in Image.list(workspace=ws):
       print('{}(v.{} [{}]) stored at {} with build log {}'.format(
           i.name, i.version, i.creation_state, i.image_location, i.image_build_log_uri))
   ```

#### <a name="deploy-to-an-azure-kubernetes-service-cluster"></a>Eseguire la distribuzione in un cluster del servizio Kubernetes di Azure

1. Per distribuire il modello come servizio Web di produzione su vasta scala, usare AKS. È possibile crearne uno nuovo usando Azure Machine Learning SDK, l'interfaccia della riga di comando o [Azure Machine Learning Studio](https://ml.azure.com).

    ```python
    from azureml.core.compute import AksCompute, ComputeTarget
    
    # Specify the Standard_PB6s Azure VM and location. Values for location may be "eastus", "southeastasia", "westeurope", or "westus2". If no value is specified, the default is "eastus".
    prov_config = AksCompute.provisioning_configuration(vm_size = "Standard_PB6s",
                                                        agent_count = 1,
                                                        location = "eastus")
    
    aks_name = 'my-aks-cluster'
    # Create the cluster
    aks_target = ComputeTarget.create(workspace=ws,
                                      name=aks_name,
                                      provisioning_configuration=prov_config)
    ```

    La distribuzione di AKS potrebbe richiedere circa 15 minuti.  Verificare che la distribuzione sia stata completata correttamente.

    ```python
    aks_target.wait_for_completion(show_output=True)
    print(aks_target.provisioning_state)
    print(aks_target.provisioning_errors)
    ```

1. Distribuire il contenitore nel cluster AKS.

    ```python
    from azureml.core.webservice import Webservice, AksWebservice
    
    # For this deployment, set the web service configuration without enabling auto-scaling or authentication for testing
    aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                    num_replicas=1,
                                                    auth_enabled=False)
    
    aks_service_name = 'my-aks-service'
    
    aks_service = Webservice.deploy_from_image(workspace=ws,
                                               name=aks_service_name,
                                               image=image,
                                               deployment_config=aks_config,
                                               deployment_target=aks_target)
    aks_service.wait_for_deployment(show_output=True)
    ```

#### <a name="deploy-to-a-local-edge-server"></a>Eseguire la distribuzione in un server perimetrale locale

Tutti i [dispositivi Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview
) contengono un FPGA per l'esecuzione del modello.  È possibile eseguire un solo modello su FPGA in una sola volta.  Per eseguire un modello diverso, è sufficiente distribuire un nuovo contenitore. Le istruzioni e il codice di esempio sono disponibili in [questo esempio di Azure](https://github.com/Azure-Samples/aml-hardware-accelerated-models).

### <a name="consume-the-deployed-model"></a>Utilizzare il modello distribuito

Infine, usare il client di esempio per chiamare l'immagine Docker per ottenere le stime dal modello.  Il codice client di esempio è disponibile:
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C#](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

L'immagine Docker supporta gRPC e TensorFlow che servono l'API "Predict".

È anche possibile scaricare un client di esempio per il servizio TensorFlow.

```python
# Using the grpc client in Azure ML Accelerated Models SDK package
from azureml.accel import PredictionClient

address = aks_service.scoring_uri
ssl_enabled = address.startswith("https")
address = address[address.find('/')+2:].strip('/')
port = 443 if ssl_enabled else 80

# Initialize Azure ML Accelerated Models client
client = PredictionClient(address=address,
                          port=port,
                          use_ssl=ssl_enabled,
                          service_name=aks_service.name)
```

Poiché il classificatore è stato sottoposto al training sul set di dati [Imagent](http://www.image-net.org/) , eseguire il mapping delle classi alle etichette leggibili.

```python
import requests
classes_entries = requests.get(
    "https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()

# Score image with input and output tensor names
results = client.score_file(path="./snowleopardgaze.jpg",
                            input_name=input_tensors,
                            outputs=output_tensors)

# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
```

### <a name="clean-up-resources"></a>Pulire le risorse

Per evitare i costi non necessari, pulire le risorse **in questo ordine**: servizio Web, immagine, quindi il modello.

```python
aks_service.delete()
aks_target.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

## <a name="next-steps"></a>Passaggi successivi

+ Informazioni su come [proteggere il documento dei servizi Web](how-to-secure-web-service.md) .

+ [Hardware iperscalabile: ML su larga scala in Azure + FPGA: Build 2018 (video)](https://channel9.msdn.com/events/Build/2018/BRK3202)

+ [Cloud configurabile basato su Microsoft FPGA (video)](https://channel9.msdn.com/Events/Build/2017/B8063)

+ [Progetto di intelligenza artificiale per intelligenza artificiale in tempo reale](https://www.microsoft.com/research/project/project-brainwave/)

+ [Sistema di controllo ottico automatico](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)
