---
title: Come e dove distribuire i modelli
titleSuffix: Azure Machine Learning service
description: Informazioni su come e dove distribuire i modelli del servizio Azure Machine Learning, inclusi Istanze di Azure Container, il servizio Azure Kubernetes, Azure IoT Edge e dispositivi FPGA.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 04/02/2019
ms.custom: seoapril2019
ms.openlocfilehash: 1528b5e92e1952bf85799afd71bd5dac16aedcf4
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58878299"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Distribuire modelli con il servizio di Azure Machine Learning

In questo documento viene descritto come distribuire un modello come servizio Web nel cloud di Azure o nei dispositivi IoT Edge. 

## <a name="compute-targets-for-deployment"></a>Destinazioni di calcolo per la distribuzione

Usare il SDK di Azure Machine Learning per distribuire il modello sottoposto a training nei percorsi seguenti:

| Destinazione del calcolo | Tipo di distribuzione | DESCRIZIONE |
| ----- | ----- | ----- |
| [Servizio Azure Kubernetes](#aks) | Inferenza in tempo reale | Soluzione ideale per le distribuzioni di produzione su vasta scala. Fornisce la scalabilità automatica e tempi di risposta rapidi. |
| [Azure Machine Learning di calcolo (amlcompute)](#azuremlcompute) | Inferenza del batch | Eseguire una stima in batch a risorse di calcolo senza server. Supporta le macchine virtuali con priorità bassa o normale. |
| [Istanze di Azure Container](#aci) | Test | Soluzione ideale per lo sviluppo o il test. **Non è adatto per i carichi di lavoro di produzione.** |
| [Azure IoT Edge](#iotedge) | (Anteprima) Modulo di IoT | Distribuzione di modelli nei dispositivi IoT. Inferenza nel dispositivo. |
| [Field-programmable Gate Array (FPGA)](#fpga) | (Anteprima) Servizio Web | Latenza estremamente bassa per inferenza in tempo reale. |

## <a name="deployment-workflow"></a>Flusso di lavoro per la distribuzione

Il processo di distribuzione di un modello è simile per tutte le destinazioni di calcolo:

1. Eseguire il training e la registrazione di un modello.
1. Configurare e registrare un'immagine che usa il modello.
1. Distribuire l'immagine in una destinazione di calcolo.
1. Test della distribuzione

Il video seguente illustra la distribuzione in istanze di contenitore di Azure:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Kwk3]


Per altre informazioni sui concetti relativi al flusso di lavoro di distribuzione, vedere [Gestire e distribuire modelli con il servizio Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="prerequisites-for-deployment"></a>Prerequisiti per la distribuzione

[!INCLUDE [aml-prereq](../../../includes/aml-prereq.md)]

- Un modello con training. Se non si ha un modello con training, usare la procedura descritta nell'esercitazione [Eseguire il training di modelli](tutorial-train-models-with-aml.md) per eseguire il training di un modello e registrarlo con il servizio Azure Machine Learning.

    > [!NOTE]
    > Anche se il servizio di Azure Machine Learning può funzionare con qualsiasi modello generico che può essere caricato in Python 3, gli esempi in questo documento illustrano l'uso di un modello archiviato in formato pickle di Python.
    >
    > Per altre informazioni sull'uso di modelli ONNX, vedere il documento [ONNX e Azure Machine Learning](how-to-build-deploy-onnx.md).

## <a id="registermodel"></a> Registrare un modello con training

Il registro di modelli consente di archiviare e organizzare i modelli con training nel cloud di Azure. I modelli vengono registrati nell'area di lavoro del servizio Azure Machine Learning. Il modello può essere sottoposto a training usando Azure Machine Learning o un altro servizio. Il codice seguente illustra come registrare un modello dal file, impostare un nome, tag e una descrizione:

```python
from azureml.core.model import Model

model = Model.register(model_path = "outputs/sklearn_mnist_model.pkl",
                       model_name = "sklearn_mnist",
                       tags = {"key": "0.1"},
                       description = "test",
                       workspace = ws)
```

**Tempo stimato**: circa 10 secondi.

Per un esempio di registrazione di un modello, vedere [eseguire il training di un classificatore di immagini](tutorial-train-models-with-aml.md).

Per altre informazioni, vedere la documentazione di riferimento per la [classe Model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a id="configureimage"></a> Creare e registrare un'immagine

I modelli distribuiti vengono inseriti in pacchetti come immagine. L'immagine contiene le dipendenze necessarie per eseguire il modello.

Per le distribuzioni di **Istanze di Azure Container**, del **servizio Azure Kubernetes** e di **Azure IoT Edge**, viene usata la classe [azureml.core.image.ContainerImage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py) per creare una configurazione di immagine. Tale configurazione viene quindi usata per creare una nuova immagine Docker.

Il codice seguente illustra come creare una nuova configurazione di immagine:

```python
from azureml.core.image import ContainerImage

# Image configuration
image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                 runtime = "python",
                                                 conda_file = "myenv.yml"}
                                                 )
```

**Tempo stimato**: circa 10 secondi.

I parametri importanti di questo esempio sono descritti nella tabella seguente:

| Parametro | DESCRIZIONE |
| ----- | ----- |
| `execution_script` | Specifica uno script di Python usato per ricevere le richieste inviate al servizio. In questo esempio lo script è contenuto nel file `score.py`. Per altre informazioni, vedere la sezione [Script di esecuzione](#script). |
| `runtime` | Indica che l'immagine usa Python. L'altra opzione è `spark-py`, che usa Python con Apache Spark. |
| `conda_file` | Usato per specificare un file di ambiente Conda. Questo file definisce l'ambiente Conda per il modello distribuito. Per altre informazioni sulla creazione di questo file, vedere [Creare un file di ambiente (myenv.yml)](tutorial-deploy-models-with-aml.md#create-environment-file). |

Per un esempio di creazione di una configurazione dell'immagine, vedere [distribuire un classificatore di immagini](tutorial-deploy-models-with-aml.md).

Per altre informazioni, vedere la documentazione di riferimento per la [classe ContainerImage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py).

### <a id="script"></a> Script di esecuzione

Lo script di esecuzione riceve i dati inviati a un'immagine distribuita e li passa al modello. Riceve quindi la risposta restituita dal modello e la restituisce al client. **Lo script è specifico per il modello**; è necessario comprendere i dati che il modello accetta e restituisce. Per uno script di esempio che funziona con un modello di classificazione delle immagini, vedere [distribuire un classificatore di immagini](tutorial-deploy-models-with-aml.md).

Lo script contiene due funzioni che caricano ed eseguono il modello:

* `init()`: questa funzione carica in genere il modello in un oggetto globale. Questa funzione viene eseguita una sola volta all'avvio del contenitore Docker.

* `run(input_data)`: questa funzione usa il modello per stimare un valore in base ai dati di input. Per la serializzazione e la deserializzazione, gli input e gli output dell'esecuzione usano in genere JSON. È anche possibile usare dati binari non elaborati. È possibile trasformare i dati prima dell'invio al modello o prima della restituzione al client.

#### <a name="working-with-json-data"></a>Uso dei dati JSON

Lo script di esempio seguente accetta e restituisce dati JSON. La funzione `run` trasforma i dati da JSON in un formato previsto dal modello e quindi trasforma la risposta in JSON prima di restituirla:

```python
%%writefile score.py
import json
import numpy as np
import os
import pickle
from sklearn.externals import joblib
from sklearn.linear_model import LogisticRegression
from azureml.core.model import Model

# load the model
def init():
    global model
    # retrieve the path to the model file using the model name
    model_path = Model.get_model_path('sklearn_mnist')
    model = joblib.load(model_path)

# Passes data to the model and returns the prediction
def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    return json.dumps(y_hat.tolist())
```

#### <a name="working-with-binary-data"></a>Uso dei dati binari

Se il modello accetta i __dati binari__, usare `AMLRequest`, `AMLResponse` e `rawhttp`. Lo script di esempio seguente accetta dati binari e restituisce i byte invertiti per le richieste POST. Per le richieste GET, restituisce l'URL completo nel corpo della risposta:

```python
from azureml.contrib.services.aml_request  import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse

def init():
    print("This is init()")

# Accept and return binary data
@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    # handle GET requests
    if request.method == 'GET':
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    # handle POST requests
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        respBody = bytearray(reqBody)
        respBody.reverse()
        respBody = bytes(respBody)
        return AMLResponse(respBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> Lo spazio dei nomi `azureml.contrib` cambia di frequente perché Microsoft è attualmente impegnata a migliorare il servizio. Qualsiasi elemento in questo spazio dei nomi deve essere pertanto considerato come anteprima e non è completamente supportato da Microsoft.
>
> Se è necessario eseguire un test nell'ambiente di sviluppo locale, è possibile installare i componenti nello spazio dei nomi `contrib` usando il comando seguente:
> ```shell
> pip install azureml-contrib-services
> ```

### <a id="createimage"></a> Registrare l'immagine

Dopo aver creato la configurazione di immagine, è possibile usarla per registrare un'immagine. Questa immagine viene archiviata nel registro contenitori relativo all'area di lavoro. Una volta creata, la stessa immagine può essere distribuita a più servizi.

```python
# Register the image from the image configuration
image = ContainerImage.create(name = "myimage",
                              models = [model], #this is the model object
                              image_config = image_config,
                              workspace = ws
                              )
```

**Tempo stimato**: circa 3 minuti.

Quando si registrano più immagini con lo stesso nome, alle immagini vengono assegnate automaticamente diverse versioni. Ad esempio, alla prima immagine registrata come `myimage` viene assegnato un ID `myimage:1`. La volta successiva che si registra un'immagine come `myimage`, l'ID della nuova immagine è `myimage:2`.

Per altre informazioni, vedere la documentazione di riferimento per la [classe ContainerImage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py).

## <a id="deploy"></a> Distribuire come servizio web

Quando si raggiunge la fase di distribuzione, il processo varia leggermente a seconda della destinazione di calcolo. Vedere le sezioni seguenti per informazioni su come eseguire la distribuzione in:

| Destinazione del calcolo | Tipo di distribuzione | DESCRIZIONE |
| ----- | ----- | ----- |
| [Servizio Azure Kubernetes](#aks) | Servizio Web (inferenza in tempo reale)| Soluzione ideale per le distribuzioni di produzione su vasta scala. Fornisce la scalabilità automatica e tempi di risposta rapidi. |
| [Calcolo di Azure Machine Learning](#azuremlcompute) | Servizio Web (l'inferenza del Batch)| Eseguire una stima in batch a risorse di calcolo senza server. Supporta le macchine virtuali con priorità bassa o normale. |
| [Istanze di Azure Container](#aci) | Servizio Web (sviluppo/test)| Soluzione ideale per lo sviluppo o il test. **Non è adatto per i carichi di lavoro di produzione.** |
| [Azure IoT Edge](#iotedge) | (Anteprima) Modulo di IoT | Distribuzione di modelli nei dispositivi IoT. Inferenza nel dispositivo. |
| [Field-programmable Gate Array (FPGA)](#fpga) | (Anteprima) Servizio Web | Latenza estremamente bassa per inferenza in tempo reale. |

> [!IMPORTANT]
> La condivisione di risorse tra le origini (CORS) non è attualmente supportata quando si distribuisce un modello come servizio Web.

Gli esempi in questa sezione usano [deploy_from_image](https://docs.microsoft.com/en-us/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-), che richiede di registrare il modello e l'immagine prima di eseguire una distribuzione. Per altre informazioni su altri metodi di distribuzione, vedere [distribuire](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none-) e [deploy_from_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-).

### <a id="aci"></a> Distribuire in istanze di contenitore di Azure (sviluppo/test)

Usare Istanze di Azure Container per distribuire i modelli come servizio Web se una o più delle condizioni seguenti sono vere:

- È necessario distribuire e convalidare rapidamente il modello. La distribuzione di ACI viene completata in meno di 5 minuti.
- Si sta eseguendo il test di un modello in fase di sviluppo. Per informazioni sulla disponibilità di quote e aree per Istanze di Azure Container, vedere il documento [Quote e aree disponibili per Istanze di Azure Container](https://docs.microsoft.com/azure/container-instances/container-instances-quotas).

Per eseguire la distribuzione in Istanze di Azure Container, seguire questa procedura:

1. Definire la configurazione della distribuzione. Questa configurazione dipende dai requisiti del modello. L'esempio seguente definisce una configurazione che usa un core CPU e 1 GB di memoria:

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=configAci)]

2. Per distribuire l'immagine creata nella sezione [Creare l'immagine](#createimage) di questo documento, usare il codice seguente:

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=option3Deploy)]

    **Tempo stimato**: Circa 5 minuti.

Per altre informazioni, vedere la documentazione di riferimento per le classi [AciWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py) e [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).

### <a id="aks"></a> Distribuire in Azure Kubernetes Service (produzione)

Per distribuire il modello come servizio Web in uno scenario di produzione su vasta scala, usare il servizio Azure Kubernetes. È possibile usare un cluster servizio Azure Kubernetes esistente o crearne uno nuovo tramite il SDK di Azure Machine Learning, l'interfaccia della riga di comando o il portale di Azure.

La creazione di un cluster servizio Azure Kubernetes si esegue una sola volta per l'area di lavoro. È possibile riutilizzare questo cluster per più distribuzioni.

> [!IMPORTANT]
> Se si elimina il cluster, per eseguire la distribuzione successiva sarà necessario creare un nuovo cluster.

Il servizio Azure Kubernetes offre le funzionalità seguenti:

* Scalabilità automatica
* Registrazione
* Raccolta di dati del modello
* Tempi di risposta rapidi per i servizi Web
* Terminazione TLS
* Authentication

#### <a name="autoscaling"></a>Scalabilità automatica

La scalabilità automatica può essere controllata definendo `autoscale_target_utilization`, `autoscale_min_replicas`, e `autoscale_max_replicas` per il servizio contenitore di AZURE al servizio web. Nell'esempio seguente viene illustrato come abilitare la scalabilità automatica:

```python
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=True,
                                                autoscale_target_utilization=30,
                                                autoscale_min_replicas=1,
                                                autoscale_max_replicas=4)
```

Le decisioni di aumentare/ridurre le dimensioni si basa su utilizzo delle repliche del contenitore corrente. Il numero di repliche attive (elaborazione di una richiesta) diviso per il totale numero di repliche corrente è l'utilizzo corrente. Se questo numero supera l'utilizzo di destinazione, vengono create più repliche. Se è inferiore, le repliche vengono ridotti. Per impostazione predefinita, l'utilizzo di destinazione è 70%.

Decisioni di aggiunta di repliche vengono apportate e implementate rapidamente (entro 1 secondo). Decisioni per rimuovere le repliche di richiedere più tempo (circa 1 minuto). Questo comportamento mantiene le repliche inattive per un minuto nel caso in cui arrivano le nuove richieste possano gestire.

È possibile calcolare le repliche necessarie usando il codice seguente:

```python
from math import ceil
# target requests per second
targetRps = 20
# time to process the request (in seconds)
reqTime = 10
# Maximum requests per container
maxReqPerContainer = 1
# target_utilization. 70% in this example
targetUtilization = .7

concurrentRequests = targetRps * reqTime / targetUtilization

# Number of container replicas
replicas = ceil(concurrentRequests / maxReqPerContainer)
```

Per altre informazioni sull'impostazione `autoscale_target_utilization`, `autoscale_max_replicas`, e `autoscale_min_replicas`, vedere il [AksWebservice.deploy_configuration](https://docs.microsoft.com/en-us/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none-) riferimento.

#### <a name="create-a-new-cluster"></a>Creare un nuovo cluster

Per creare un nuovo cluster del servizio Azure Kubernetes, usare il codice seguente:

> [!IMPORTANT]
> La creazione del cluster servizio Azure Kubernetes si esegue una sola volta per l'area di lavoro. Dopo aver creato il cluster, è possibile riutilizzarlo per più distribuzioni. Se si elimina il cluster o il gruppo di risorse che lo contiene, per eseguire la distribuzione successiva sarà necessario creare un nuovo cluster.
> Per [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py), se si selezionano valori personalizzati per agent_count e vm_size, è necessario verificare che il valore di agent_count moltiplicato per il valore di vm_size sia maggiore o uguale a 12 CPU virtuali. Ad esempio, se come vm_size si usa "Standard_D3_v2", che dispone di 4 CPU virtuali, per agent_count è consigliabile selezionare 3 o un valore superiore.

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aml-aks-1'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

**Tempo stimato**: circa 20 minuti.

#### <a name="use-an-existing-cluster"></a>Usare un cluster esistente

Se si dispone già del cluster servizio contenitore di AZURE nella sottoscrizione di Azure, ed è versione 1.12. # # e dispone di almeno 12 CPU virtuali, è possibile usarla per distribuire l'immagine. Il codice seguente illustra come collegare un 1.12 servizio contenitore di AZURE esistente. # # cluster all'area di lavoro:

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'mycluster'

# Attach the cluster to your workgroup
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'mycompute', attach_config)

# Wait for the operation to complete
aks_target.wait_for_completion(True)
```

**Tempo stimato**: circa 3 minuti.

Per altre informazioni sulla creazione di un cluster del servizio contenitore di AZURE di fuori di Azure Machine Learning SDK, vedere gli articoli seguenti:

* [Creare un cluster del servizio Azure Container](https://docs.microsoft.com/cli/azure/aks?toc=%2Fen-us%2Fazure%2Faks%2FTOC.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [Creare un cluster del servizio contenitore di AZURE (portale)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

#### <a name="deploy-the-image"></a>Distribuire l'immagine

Per distribuire nel cluster di server Kubernetes di Azure l'immagine creata nella sezione [Creare l'immagine](#createimage) di questo documento, usare il codice seguente:

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set configuration and service name
aks_config = AksWebservice.deploy_configuration()
aks_service_name ='aks-service-1'
# Deploy from image
service = Webservice.deploy_from_image(workspace = ws,
                                            name = aks_service_name,
                                            image = image,
                                            deployment_config = aks_config,
                                            deployment_target = aks_target)
# Wait for the deployment to complete
service.wait_for_deployment(show_output = True)
print(service.state)
```

**Tempo stimato**: circa 3 minuti.

Per altre informazioni, vedere la documentazione di riferimento per le classi [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) e [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py).

### <a id="azuremlcompute"></a> Inferenza alle risorse di calcolo di Azure Machine Learning

Le destinazioni di calcolo di Azure Machine Learning vengono create e gestite dal servizio di Azure Machine Learning. Possono essere utilizzati per la stima batch dalle pipeline di Machine Learning di Azure.

Per una procedura dettagliata di inferenza del batch con calcolo di Azure Machine Learning, vedere la [come eseguire stime in Batch](how-to-run-batch-predictions.md) documento.


### <a id="fpga"></a> Eseguire la distribuzione in dispositivi FPGA

Project Brainwave consente di ottenere una latenza estremamente bassa per le richieste di inferenza in tempo reale. Project Brainwave accelera reti neurali profonde (DNN) distribuite in field-programmable gate array nel cloud di Azure. I DNN comunemente usati sono disponibili come featurizers per il trasferimento induttivo o sottoposti a training personalizzabile con pesi formati a partire dai propri dati.

Per una procedura dettagliata sulla distribuzione di un modello con Project Brainwave, vedere il documento [Distribuire un modello in un FPGA](how-to-deploy-fpga-web-service.md).

## <a name="define-schema"></a>Definizione di schema

Elementi Decorator personalizzati può essere usato per [OpenAPI](https://swagger.io/docs/specification/about/) specifica generazione e input digitare manipolazione quando si distribuisce il servizio web. Nel `score.py` file, disponibile un esempio di input e/o output nel costruttore per uno degli oggetti dal tipo definito e il tipo e un esempio vengono utilizzati per generare automaticamente lo schema. Attualmente sono supportati i tipi seguenti:

* `pandas`
* `numpy`
* `pyspark`
* standard Python

Prima di tutto le dipendenze necessarie per la `inference-schema` pacchetto sono inclusi nel `env.yml` file dell'ambiente conda. Questo esempio Usa la `numpy` tipo di parametro per lo schema, pertanto il pip extra `[numpy-support]` viene installato anche.

```python
%%writefile myenv.yml
name: project_environment
dependencies:
  - python=3.6.2
  - pip:
    - azureml-defaults
    - scikit-learn
    - inference-schema[numpy-support]
```

A questo punto, modificare il `score.py` file per importare il `inference-schema` pacchetti. Definire l'input e output formato di esempio tra il `input_sample` e `output_sample` variabili, che rappresentano i formati di richiesta e risposta per il servizio web. Utilizzare questi esempi di input e output elementi Decorator di funzione nel `run()` (funzione).

```python
%%writefile score.py
import json
import numpy as np
import os
import pickle
from sklearn.externals import joblib
from sklearn.linear_model import LogisticRegression
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType


def init():
    global model
    model_path = Model.get_model_path('sklearn_mnist')
    model = joblib.load(model_path)


input_sample = np.array([[1.8]])
output_sample = np.array([43638.88])

@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    y_hat = model.predict(data)
    return json.dumps(y_hat.tolist())
```

Dopo aver completato l'immagine normale web e registrazione del servizio processo di distribuzione con l'aggiornamento `score.py` file, recuperare l'uri di Swagger dal servizio. Questo uri di richiesta restituirà il `swagger.json` file.

```python
service.wait_for_deployment(show_output=True)
print(service.swagger_uri)
```



Quando si crea una nuova immagine, è necessario aggiornare manualmente ogni servizio che deve usarla. Per aggiornare il servizio Web, usare il metodo `update`. Il codice seguente illustra come aggiornare il servizio Web in modo da usare una nuova immagine:

```python
from azureml.core.webservice import Webservice
from azureml.core.image import Image

service_name = 'aci-mnist-3'
# Retrieve existing service
service = Webservice(name = service_name, workspace = ws)

# point to a different image
new_image = Image(workspace = ws, id="myimage2:1")

# Update the image used by the service
service.update(image = new_image)
print(service.state)
```

Per altre informazioni, vedere la documentazione di riferimento per la classe [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py).

## <a name="test-web-service-deployments"></a>Testare le distribuzioni del servizio web

Per eseguire il test di una distribuzione di servizio Web, è possibile usare il metodo `run` dell'oggetto Webservice. Nell'esempio seguente, un documento JSON è impostato su un servizio Web e il risultato viene visualizzato. I dati inviati devono corrispondere a quelli previsti dal modello. In questo esempio, il formato dei dati corrisponde all'input previsto dal modello diabetes.

```python
import json

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')

prediction = service.run(input_data = test_sample)
print(prediction)
```

Il servizio Web è un'API REST, quindi è possibile creare applicazioni client in svariati linguaggi di programmazione. Per altre informazioni, vedere [Creare applicazioni client per utilizzare i servizi Web](how-to-consume-web-service.md).

## <a id="update"></a> Aggiornare il servizio Web

Quando si crea una nuova immagine, è necessario aggiornare manualmente ogni servizio che deve usarla. Per aggiornare il servizio Web, usare il metodo `update`. Il codice seguente illustra come aggiornare il servizio Web in modo da usare una nuova immagine:

```python
from azureml.core.webservice import Webservice
from azureml.core.image import Image

service_name = 'aci-mnist-3'
# Retrieve existing service
service = Webservice(name = service_name, workspace = ws)

# point to a different image
new_image = Image(workspace = ws, id="myimage2:1")

# Update the image used by the service
service.update(image = new_image)
print(service.state)
```

Per altre informazioni, vedere la documentazione di riferimento per la classe [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py).

## <a id="iotedge"></a> Eseguire la distribuzione in Azure IoT Edge

Un dispositivo Azure IoT Edge è un dispositivo Linux o Windows che esegue il runtime di Azure IoT Edge. Tramite l'IoT Hub di Azure, è possibile distribuire i modelli di machine learning per questi dispositivi come moduli di IoT Edge. La distribuzione di un modello in un dispositivo IoT Edge consente al dispositivo di usare il modello direttamente invece di inviare i dati al cloud per l'elaborazione. Si ottengono tempi di risposta più rapidi e un trasferimento dei dati inferiore.

I moduli Azure IoT Edge vengono distribuiti nel dispositivo da un registro contenitori. Quando si crea un'immagine dal modello, questa viene archiviata nel registro contenitori dell'area di lavoro.

> [!IMPORTANT]
> Le informazioni contenute in questa sezione si presuppongono che abbia già familiarità con i moduli di IoT Hub di Azure e Azure IoT Edge. Anche se alcune delle informazioni in questa sezione è specifico al servizio Azure Machine Learning, la maggior parte del processo di distribuzione in un dispositivo perimetrale viene eseguito dopo il servizio IoT di Azure.
>
> Se non si ha familiarità con Azure IoT, vedere [nozioni di base di Azure IoT](https://docs.microsoft.com/azure/iot-fundamentals/) e [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/) per le informazioni di base. Usare quindi altri collegamenti in questa sezione per altre informazioni su operazioni specifiche.

### <a name="set-up-your-environment"></a>Configurazione dell'ambiente

* Un ambiente di sviluppo. Per altre informazioni, vedere il documento [Come configurare un ambiente di sviluppo](how-to-configure-environment.md).

* Un [hub IoT di Azure](../../iot-hub/iot-hub-create-through-portal.md) nella sottoscrizione di Azure.

* Un modello con training. Per un esempio di esecuzione del training di un modello, vedere il documento [Eseguire il training di un modello di classificazione delle immagini con Azure Machine Learning](tutorial-train-models-with-aml.md). Nel [repository GitHub di AI Toolkit per Azure IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial) è disponibile un modello con training preliminare.

### <a id="getcontainer"></a> Ottenere il contenitore delle credenziali del Registro di sistema

Per distribuire un modulo IoT Edge nel dispositivo, Azure IoT richiede le credenziali per il registro contenitori in cui il servizio Azure Machine Learning archivia le immagini Docker.

È possibile ottenere le credenziali in due modi:

+ **Nel portale di Azure**:

  1. Accedere al [portale di Azure](https://portal.azure.com/signin/index).

  1. Passare all'area di lavoro del servizio Azure Machine Learning e selezionare __Panoramica__. Per passare alle impostazioni del registro contenitori selezionare il collegamento __Registro__.

     ![Immagine della voce nel registro contenitori](./media/how-to-deploy-and-where/findregisteredcontainer.png)

  1. Una volta aperto il registro contenitori, selezionare **Chiavi di accesso** e quindi abilitare l'utente amministratore.

     ![Immagine della schermata delle chiavi di accesso](./media/how-to-deploy-and-where/findaccesskey.png)

  1. Salvare i valori per **Server di accesso**, **Nome utente** e **Password**.

+ **Con uno script Python**:

  1. Usare lo script Python seguente dopo il codice che è stato eseguito per creare un contenitore:

     ```python
     # Getting your container details
     container_reg = ws.get_details()["containerRegistry"]
     reg_name=container_reg.split("/")[-1]
     container_url = "\"" + image.image_location + "\","
     subscription_id = ws.subscription_id
     from azure.mgmt.containerregistry import ContainerRegistryManagementClient
     from azure.mgmt import containerregistry
     client = ContainerRegistryManagementClient(ws._auth,subscription_id)
     result= client.registries.list_credentials(resource_group_name, reg_name, custom_headers=None, raw=False)
     username = result.username
     password = result.passwords[0].value
     print('ContainerURL{}'.format(image.image_location))
     print('Servername: {}'.format(reg_name))
     print('Username: {}'.format(username))
     print('Password: {}'.format(password))
     ```
  1. Salvare i valori per ContainerURL, Servername, Username e Password.

     Queste credenziali sono necessarie per consentire al dispositivo IoT Edge di accedere alle immagini nel registro contenitori privato.

### <a name="prepare-the-iot-device"></a>Preparare il dispositivo IoT

Registrare il dispositivo con IoT Hub di Azure e quindi installare il runtime di IoT Edge nel dispositivo. Se non si ha familiarità con questo processo, vedere [Guida introduttiva: Distribuire il primo modulo di IoT Edge in un dispositivo Linux x64](../../iot-edge/quickstart-linux.md).

Altri metodi di registrazione di un dispositivo sono:

* [Portale di Azure](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal)
* [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-cli)
* [Visual Studio Code](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-vscode)

### <a name="deploy-the-model-to-the-device"></a>Distribuire il modello nel dispositivo

Per distribuire il modello di dispositivo, usare le informazioni del Registro di sistema raccolte durante la [ottenere le credenziali del Registro di contenitori](#getcontainer) sezione con la distribuzione del modulo i passaggi per i moduli di IoT Edge. Ad esempio, quando [moduli di distribuzione di Azure IoT Edge dal portale di Azure](../../iot-edge/how-to-deploy-modules-portal.md), è necessario configurare il __le impostazioni del Registro di sistema__ per il dispositivo. Usare la __server di accesso__, __username__, e __password__ per il registro contenitori di area di lavoro.

È anche possibile distribuire mediante [Azure CLI](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-cli) e [Visual Studio Code](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-vscode).

## <a name="clean-up"></a>Eseguire la pulizia

Per eliminare un servizio Web distribuito, usare `service.delete()`.

Per eliminare un'immagine, usare `image.delete()`.

Per eliminare un modello registrato, usare `model.delete()`.

Per altre informazioni, vedere la documentazione di riferimento per [WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--), [Image.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image(class)?view=azure-ml-py#delete--) e [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

## <a name="next-steps"></a>Passaggi successivi

* [Risoluzione dei problemi di distribuzione](how-to-troubleshoot-deployment.md)
* [Proteggere i servizi Web di Azure Machine Learning con SSL](how-to-secure-web-service.md)
* [Utilizzare un modello di Machine Learning distribuito come servizio web](how-to-consume-web-service.md)
* [Come eseguire stime in batch](how-to-run-batch-predictions.md)
* [Monitorare i modelli di Azure Machine Learning con Application Insights](how-to-enable-app-insights.md)
* [Raccogliere i dati per i modelli nell'ambiente di produzione](how-to-enable-data-collection.md)
* [Azure SDK per servizi Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Usare il servizio di Azure Machine Learning con reti virtuali di Azure](how-to-enable-virtual-network.md)
* [Le procedure consigliate per la creazione di sistemi di raccomandazione](https://github.com/Microsoft/Recommenders)
* [Creare un'API per raccomandazioni in tempo reale in Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
