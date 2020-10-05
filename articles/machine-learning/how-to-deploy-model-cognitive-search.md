---
title: Distribuire un modello da usare con Ricerca cognitiva
titleSuffix: Azure Machine Learning
description: Questo articolo illustra come usare Azure Machine Learning per distribuire un modello da usare con ricerca cognitiva di Azure. Ricerca cognitiva possibile utilizzare i modelli distribuiti da Azure Machine Learning come abilità personalizzata per migliorare l'esperienza di ricerca.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: cgronlun
author: cjgronlund
ms.reviewer: larryfr
ms.date: 06/11/2020
ms.openlocfilehash: d9d509f23ca62415035bdda5231033a0c2f830ce
ms.sourcegitcommit: 638f326d02d108cf7e62e996adef32f2b2896fd5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91728727"
---
# <a name="deploy-a-model-for-use-with-cognitive-search"></a>Distribuire un modello da usare con Ricerca cognitiva


Questo articolo illustra come usare Azure Machine Learning per distribuire un modello da usare con ricerca cognitiva di [Azure](../search/search-what-is-azure-search.md).

Ricerca cognitiva esegue l'elaborazione del contenuto sul contenuto eterogeneo, per renderlo queryable da esseri umani o da applicazioni. Questo processo può essere migliorato usando un modello distribuito da Azure Machine Learning.

Azure Machine Learning possibile distribuire un modello sottoposto a training come servizio Web. Il servizio Web viene quindi incorporato in una ricerca cognitiva _skill_, che diventa parte della pipeline di elaborazione.

> [!IMPORTANT]
> Le informazioni contenute in questo articolo sono specifiche per la distribuzione del modello. Vengono fornite informazioni sulle configurazioni di distribuzione supportate che consentono di utilizzare il modello in ricerca cognitiva.
>
> Per informazioni su come configurare ricerca cognitiva per l'uso del modello distribuito, vedere l'esercitazione [compilare e distribuire un'abilità personalizzata con Azure Machine Learning](../search/cognitive-search-tutorial-aml-custom-skill.md) .
>
> Per l'esempio su cui si basa l'esercitazione, vedere [https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill) .

Quando si distribuisce un modello per l'uso con ricerca cognitiva di Azure, la distribuzione deve soddisfare i requisiti seguenti:

* Usare il servizio Azure Kubernetes per ospitare il modello per l'inferenza.
* Abilitare Transport Layer Security (TLS) per il servizio Azure Kubernetes. TLS viene usato per proteggere le comunicazioni HTTPS tra ricerca cognitiva e il modello distribuito.
* Lo script di immissione deve usare il `inference_schema` pacchetto per generare uno schema openapi (spavalderia) per il servizio.
* Lo script di immissione deve accettare anche i dati JSON come input e generare JSON come output.


## <a name="prerequisites"></a>Prerequisiti

* Un'area di lavoro di Azure Machine Learning. Per altre informazioni, vedere [creare un'area di lavoro Azure Machine Learning](how-to-manage-workspace.md).

* Ambiente di sviluppo Python con Azure Machine Learning SDK installato. Per ulteriori informazioni, vedere [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true).  

* Modello registrato. Se non si dispone di un modello, utilizzare il notebook di esempio all'indirizzo [https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill) .

* Informazioni generali [su come e dove distribuire i modelli](how-to-deploy-and-where.md).

## <a name="connect-to-your-workspace"></a>Connettersi all'area di lavoro

Un'area di lavoro di Azure Machine Learning offre una posizione centralizzata in cui usare tutti gli elementi creati quando si usa Azure Machine Learning. L'area di lavoro mantiene una cronologia di tutte le esecuzioni di training, inclusi i log, le metriche, l'output e uno snapshot degli script.

Per connettersi a un'area di lavoro esistente, usare il codice seguente:

> [!IMPORTANT]
> Questo frammento di codice prevede che la configurazione dell'area di lavoro sia salvata nella directory corrente o nella relativa directory padre. Per altre informazioni, vedere [creare e gestire aree di lavoro Azure Machine Learning](how-to-manage-workspace.md). Per altre informazioni sul salvataggio della configurazione in un file, vedere [Creare un file di configurazione dell'area di lavoro](how-to-configure-environment.md#workspace).

```python
from azureml.core import Workspace

try:
    # Load the workspace configuration from local cached inffo
    ws = Workspace.from_config()
    print(ws.name, ws.location, ws.resource_group, ws.location, sep='\t')
    print('Library configuration succeeded')
except:
    print('Workspace not found')
```

## <a name="create-a-kubernetes-cluster"></a>Creare un cluster Kubernetes

**Tempo stimato**: circa 20 minuti.

Un cluster Kubernetes è un set di istanze di macchine virtuali, denominate nodi, che vengono usate per l'esecuzione di applicazioni in contenitori.

Quando si distribuisce un modello da Azure Machine Learning al servizio Azure Kubernetes, il modello e tutti gli asset necessari per ospitarlo come servizio Web vengono inseriti in un contenitore docker. Questo contenitore viene quindi distribuito nel cluster.

Il codice seguente illustra come creare un nuovo cluster Azure Kubernetes Service (AKS) per l'area di lavoro:

> [!TIP]
> È anche possibile aggiungere un servizio Azure Kubernetes esistente all'area di lavoro Azure Machine Learning. Per altre informazioni, vedere [How to deploy Models to Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md).

> [!IMPORTANT]
> Si noti che il codice usa il `enable_ssl()` metodo per abilitare il protocollo TLS (Transport Layer Security) per il cluster. Questa operazione è necessaria quando si prevede di usare il modello distribuito da servizi cognitivi.

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Create or attach to an AKS inferencing cluster

# Create the provisioning configuration with defaults
prov_config = AksCompute.provisioning_configuration()

# Enable TLS (sometimes called SSL) communications
# Leaf domain label generates a name using the formula
#  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
#  where "######" is a random series of characters
prov_config.enable_ssl(leaf_domain_label = "contoso")

cluster_name = 'amlskills'
# Try to use an existing compute target by that name.
# If one doesn't exist, create one.
try:
    
    aks_target = ComputeTarget(ws, cluster_name)
    print("Attaching to existing cluster")
except Exception as e:
    print("Creating new cluster")
    aks_target = ComputeTarget.create(workspace = ws, 
                                  name = cluster_name, 
                                  provisioning_configuration = prov_config)
    # Wait for the create process to complete
    aks_target.wait_for_completion(show_output = True)
```

> [!IMPORTANT]
> Azure effettuerà la fatturazione fino a quando il cluster AKS esiste. Assicurarsi di eliminare il cluster AKS al termine dell'operazione.

Per altre informazioni sull'uso di AKS con Azure Machine Learning, vedere [come eseguire la distribuzione nel servizio Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md).

## <a name="write-the-entry-script"></a>Scrivere lo script di immissione

Lo script di immissione riceve i dati inviati al servizio Web, li passa al modello e restituisce i risultati di assegnazione dei punteggi. Lo script seguente carica il modello all'avvio, quindi usa il modello per assegnare un punteggio ai dati. Questo file viene talvolta chiamato `score.py` .

> [!TIP]
> Lo script di avvio è specifico del modello. Ad esempio, lo script deve essere in grado di comprendere il Framework da usare con il modello, i formati di dati e così via.

> [!IMPORTANT]
> Quando si prevede di usare il modello distribuito da servizi cognitivi di Azure, è necessario usare il `inference_schema` pacchetto per abilitare la generazione dello schema per la distribuzione. Questo pacchetto fornisce elementi Decorator che consentono di definire il formato dei dati di input e di output per il servizio Web che esegue l'inferenza usando il modello.

```python
from azureml.core.model import Model
from nlp_architect.models.absa.inference.inference import SentimentInference
from spacy.cli.download import download as spacy_download
import traceback
import json
# Inference schema for schema discovery
from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.standard_py_parameter_type import StandardPythonParameterType

def init():
    """
    Set up the ABSA model for Inference  
    """
    global SentInference
    spacy_download('en')
    aspect_lex = Model.get_model_path('hotel_aspect_lex')
    opinion_lex = Model.get_model_path('hotel_opinion_lex') 
    SentInference = SentimentInference(aspect_lex, opinion_lex)

# Use inference schema decorators and sample input/output to
# build the OpenAPI (Swagger) schema for the deployment
standard_sample_input = {'text': 'a sample input record containing some text' }
standard_sample_output = {"sentiment": {"sentence": "This place makes false booking prices, when you get there, they say they do not have the reservation for that day.", 
                                        "terms": [{"text": "hotels", "type": "AS", "polarity": "POS", "score": 1.0, "start": 300, "len": 6}, 
                                                  {"text": "nice", "type": "OP", "polarity": "POS", "score": 1.0, "start": 295, "len": 4}]}}
@input_schema('raw_data', StandardPythonParameterType(standard_sample_input))
@output_schema(StandardPythonParameterType(standard_sample_output))    
def run(raw_data):
    try:
        # Get the value of the 'text' field from the JSON input and perform inference
        input_txt = raw_data["text"]
        doc = SentInference.run(doc=input_txt)
        if doc is None:
            return None
        sentences = doc._sentences
        result = {"sentence": doc._doc_text}
        terms = []
        for sentence in sentences:
            for event in sentence._events:
                for x in event:
                    term = {"text": x._text, "type":x._type.value, "polarity": x._polarity.value, "score": x._score,"start": x._start,"len": x._len }
                    terms.append(term)
        result["terms"] = terms
        print("Success!")
        # Return the results to the client as a JSON document
        return {"sentiment": result}
    except Exception as e:
        result = str(e)
        # return error message back to the client
        print("Failure!")
        print(traceback.format_exc())
        return json.dumps({"error": result, "tb": traceback.format_exc()})
```

Per ulteriori informazioni sugli script di immissione, vedere [How and where to deploy](how-to-deploy-and-where.md).

## <a name="define-the-software-environment"></a>Definire l'ambiente software

La classe Environment viene usata per definire le dipendenze di Python per il servizio. Sono incluse le dipendenze richieste dal modello e dallo script di immissione. In questo esempio vengono installati i pacchetti dall'indice pypi regolare e da un repository GitHub. 

```python
from azureml.core.conda_dependencies import CondaDependencies 
from azureml.core import Environment

conda = None
pip = ["azureml-defaults", "azureml-monitoring", 
       "git+https://github.com/NervanaSystems/nlp-architect.git@absa", 'nlp-architect', 'inference-schema',
       "spacy==2.0.18"]

conda_deps = CondaDependencies.create(conda_packages=None, pip_packages=pip)

myenv = Environment(name='myenv')
myenv.python.conda_dependencies = conda_deps
```

Per altre informazioni sugli ambienti, vedere [creare e gestire ambienti per il training e la distribuzione](how-to-use-environments.md).

## <a name="define-the-deployment-configuration"></a>Definire la configurazione della distribuzione

La configurazione della distribuzione definisce l'ambiente di hosting del servizio Kubernetes di Azure usato per eseguire il servizio Web.

> [!TIP]
> Se non si è certi delle esigenze di memoria, CPU o GPU della distribuzione, è possibile usare la profilatura per apprendere queste informazioni. Per ulteriori informazioni, vedere [How and where to deploy a Model](how-to-deploy-and-where.md).

```python
from azureml.core.model import Model
from azureml.core.webservice import Webservice
from azureml.core.image import ContainerImage
from azureml.core.webservice import AksWebservice, Webservice

# If deploying to a cluster configured for dev/test, ensure that it was created with enough
# cores and memory to handle this deployment configuration. Note that memory is also used by
# things such as dependencies and AML components.

aks_config = AksWebservice.deploy_configuration(autoscale_enabled=True, 
                                                       autoscale_min_replicas=1, 
                                                       autoscale_max_replicas=3, 
                                                       autoscale_refresh_seconds=10, 
                                                       autoscale_target_utilization=70,
                                                       auth_enabled=True, 
                                                       cpu_cores=1, memory_gb=2, 
                                                       scoring_timeout_ms=5000, 
                                                       replica_max_concurrent_requests=2, 
                                                       max_request_wait_time=5000)
```

Per ulteriori informazioni, vedere la documentazione di riferimento per [AksService.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-).

## <a name="define-the-inference-configuration"></a>Definire la configurazione dell'inferenza

La configurazione dell'inferenza punta allo script di immissione e all'oggetto ambiente:

```python
from azureml.core.model import InferenceConfig
inf_config = InferenceConfig(entry_script='score.py', environment=myenv)
```

Per ulteriori informazioni, vedere la documentazione di riferimento per [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py&preserve-view=true).

## <a name="deploy-the-model"></a>Distribuire il modello

Distribuire il modello nel cluster AKS e attendere che crei il servizio. In questo esempio, due modelli registrati vengono caricati dal registro di sistema e distribuiti in AKS. Dopo la distribuzione, il `score.py` file nella distribuzione carica questi modelli e li usa per eseguire l'inferenza.

```python
from azureml.core.webservice import AksWebservice, Webservice

c_aspect_lex = Model(ws, 'hotel_aspect_lex')
c_opinion_lex = Model(ws, 'hotel_opinion_lex') 
service_name = "hotel-absa-v2"

aks_service = Model.deploy(workspace=ws,
                           name=service_name,
                           models=[c_aspect_lex, c_opinion_lex],
                           inference_config=inf_config,
                           deployment_config=aks_config,
                           deployment_target=aks_target,
                           overwrite=True)

aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
```

Per ulteriori informazioni, vedere la documentazione di riferimento per il [modello](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py&preserve-view=true).

## <a name="issue-a-sample-query-to-your-service"></a>Eseguire una query di esempio per il servizio

Nell'esempio seguente vengono usate le informazioni di distribuzione archiviate nella `aks_service` variabile dalla sezione di codice precedente. Usa questa variabile per recuperare l'URL di assegnazione dei punteggi e il token di autenticazione necessari per comunicare con il servizio:

```python
import requests
import json

primary, secondary = aks_service.get_keys()

# Test data
input_data = '{"raw_data": {"text": "This is a nice place for a relaxing evening out with friends. The owners seem pretty nice, too. I have been there a few times including last night. Recommend."}}'

# Since authentication was enabled for the deployment, set the authorization header.
headers = {'Content-Type':'application/json',  'Authorization':('Bearer '+ primary)} 

# Send the request and display the results
resp = requests.post(aks_service.scoring_uri, input_data, headers=headers)
print(resp.text)
```

Il risultato restituito dal servizio è simile al codice JSON seguente:

```json
{"sentiment": {"sentence": "This is a nice place for a relaxing evening out with friends. The owners seem pretty nice, too. I have been there a few times including last night. Recommend.", "terms": [{"text": "place", "type": "AS", "polarity": "POS", "score": 1.0, "start": 15, "len": 5}, {"text": "nice", "type": "OP", "polarity": "POS", "score": 1.0, "start": 10, "len": 4}]}}
```

## <a name="connect-to-cognitive-search"></a>Connetti a ricerca cognitiva

Per informazioni sull'uso di questo modello da ricerca cognitiva, vedere l'esercitazione [compilare e distribuire un'abilità personalizzata con Azure Machine Learning](../search/cognitive-search-tutorial-aml-custom-skill.md) .

## <a name="clean-up-the-resources"></a>Pulire le risorse

Se il cluster AKS è stato creato in modo specifico per questo esempio, eliminare le risorse al termine del test con ricerca cognitiva.

> [!IMPORTANT]
> Azure addebita la fatturazione in base al tempo di distribuzione del cluster AKS. Assicurarsi di pulirlo al termine dell'operazione.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>Passaggi successivi

* [Creazione e distribuzione di un'abilità personalizzata con Azure Machine Learning](../search/cognitive-search-tutorial-aml-custom-skill.md)
