---
title: Distribuire modelli ML nel servizio Kubernetes
titleSuffix: Azure Machine Learning
description: Informazioni su come distribuire i modelli di Azure Machine Learning come servizio Web usando il servizio Azure Kubernetes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, contperfq1
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 09/01/2020
ms.openlocfilehash: fc9d38f2e34e0b01183441fd48e39976d18a50e6
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91328463"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Distribuire un modello in un cluster del servizio Kubernetes di Azure


Informazioni su come usare Azure Machine Learning per distribuire un modello come servizio Web in Azure Kubernetes Service (AKS). Il servizio Azure Kubernetes è ideale per distribuzioni di produzione su vasta scala. Usare il servizio Azure Kubernetes se sono necessarie una o più delle funzionalità seguenti:

- __Tempo di risposta rapido__
- __Scalabilità__ automatica del servizio distribuito
- __Logging__
- __Raccolta di dati del modello__
- __autenticazione__
- __Terminazione TLS__
- Opzioni di __accelerazione hardware__ quali GPU e FPGA (Field-Programmable Gate Array)

Quando si esegue la distribuzione nel servizio Azure Kubernetes, viene distribuito in un cluster AKS __connesso all'area di lavoro__. Per informazioni sulla connessione di un cluster AKS all'area di lavoro, vedere [creare e collegare un cluster del servizio Azure Kubernetes](how-to-create-attach-kubernetes.md).

> [!IMPORTANT]
> Si consiglia di eseguire il debug in locale prima della distribuzione nel servizio Web. Per ulteriori informazioni, vedere [debug in locale](https://docs.microsoft.com/azure/machine-learning/how-to-troubleshoot-deployment#debug-locally)
>
> È anche possibile fare riferimento ad Azure Machine Learning - [Eseguire la distribuzione a un notebook locale](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-to-local)

## <a name="prerequisites"></a>Prerequisiti

- Un'area di lavoro di Azure Machine Learning. Per altre informazioni, vedere [creare un'area di lavoro Azure Machine Learning](how-to-manage-workspace.md).

- Un modello di apprendimento automatico registrato nell'area di lavoro. Se non si dispone di un modello registrato, vedere [come e dove distribuire i modelli](how-to-deploy-and-where.md).

- Estensione dell'interfaccia della riga [di comando di Azure per il servizio Machine Learning](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)o l' [estensione di Visual Studio code Azure Machine Learning](tutorial-setup-vscode-extension.md).

- I frammenti di codice __Python__ in questo articolo presuppongono che siano impostate le variabili seguenti:

    * `ws` -Impostare sull'area di lavoro.
    * `model` : Impostare sul modello registrato.
    * `inference_config` -Impostare sulla configurazione di inferenza per il modello.

    Per ulteriori informazioni sull'impostazione di queste variabili, vedere [come e dove distribuire i modelli](how-to-deploy-and-where.md).

- I frammenti di codice dell' __interfaccia__ della riga di comando in questo articolo presuppongono che sia stato creato un `inferenceconfig.json` documento. Per ulteriori informazioni sulla creazione di questo documento, vedere [come e dove distribuire i modelli](how-to-deploy-and-where.md).

- Un cluster del servizio Azure Kubernetes connesso all'area di lavoro. Per altre informazioni, vedere [creare e alleghi un cluster del servizio Azure Kubernetes](how-to-create-attach-kubernetes.md).

    - Se si desidera distribuire modelli a nodi GPU o a nodi FPGA (o a qualsiasi SKU specifico), è necessario creare un cluster con lo SKU specifico. Non è disponibile alcun supporto per la creazione di un pool di nodi secondari in un cluster esistente e la distribuzione di modelli nel pool di nodi secondari.

## <a name="understand-the-deployment-processes"></a>Informazioni sui processi di distribuzione

Il termine "distribuzione" viene usato sia in Kubernetes che in Azure Machine Learning. "Distribuzione" ha significati diversi in questi due contesti. In Kubernetes, `Deployment` è un'entità concreta, specificata con un file YAML dichiarativo. Un Kubernetes `Deployment` ha un ciclo di vita definito e relazioni concrete con altre entità Kubernetes, ad esempio `Pods` e `ReplicaSets` . Per informazioni su Kubernetes da documenti e video, vedere informazioni su [Kubernetes](https://aka.ms/k8slearning).

In Azure Machine Learning, "distribuzione" viene usato nel senso più generale di rendere disponibile e pulire le risorse del progetto. I passaggi che Azure Machine Learning considera parte della distribuzione sono:

1. Comprime i file nella cartella del progetto, ignorando quelli specificati in. amlignore o. gitignore
1. Scalabilità verticale del cluster di elaborazione (correlato a Kubernetes)
1. Compilazione o download di dockerfile nel nodo di calcolo (correlato a Kubernetes)
    1. Il sistema calcola un hash di: 
        - Immagine di base 
        - Passaggi personalizzati di Docker (vedere [distribuire un modello usando un'immagine di base Docker personalizzata](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-custom-docker-image))
        - YAML per la definizione conda (vedere [creare & usare gli ambienti software in Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-use-environments))
    1. Il sistema usa questo hash come chiave in una ricerca dell'area di lavoro Azure Container Registry (ACR)
    1. Se non viene trovato, viene cercata una corrispondenza nell'ACR globale
    1. Se non viene trovato, il sistema compila una nuova immagine, che verrà memorizzata nella cache e inserita nell'area di lavoro ACR.
1. Download del file di progetto compresso nell'archiviazione temporanea nel nodo di calcolo
1. Decompressione del file di progetto
1. Nodo di calcolo in esecuzione `python <entry script> <arguments>`
1. Salvataggio dei log, dei file di modello e di altri file scritti nell' `./outputs` account di archiviazione associato all'area di lavoro
1. Riduzione delle prestazioni di calcolo, inclusa la rimozione dell'archiviazione temporanea (in relazione a Kubernetes)

### <a name="azure-ml-router"></a>Router Azure ML

Il componente front-end (azureml-Fe) che instrada le richieste di inferenza in ingresso ai servizi distribuiti viene ridimensionato automaticamente in base alle esigenze. Il ridimensionamento di azureml-Fe è basato sullo scopo e sulle dimensioni del cluster AKS (numero di nodi). Lo scopo e i nodi del cluster vengono configurati quando si [Crea o si connette un cluster AKS](how-to-create-attach-kubernetes.md). È disponibile un servizio azureml-Fe per ogni cluster, che potrebbe essere in esecuzione su più POD.

> [!IMPORTANT]
> Quando si usa un cluster configurato come __dev-test__, lo scaler automatico è **disabilitato**.

Azureml-Fe scala sia verso l'alto (verticalmente) per usare più core, sia orizzontalmente per usare più baccelli. Quando si decide di eseguire la scalabilità verticale, viene usato il tempo necessario per instradare le richieste di inferenza in ingresso. Se questo tempo supera la soglia, viene eseguita una scalabilità verticale. Se il tempo di instradamento delle richieste in ingresso continua a superare la soglia, si verificherà una scalabilità orizzontale.

Quando si esegue il ridimensionamento in basso e in, viene utilizzato l'utilizzo della CPU. Se viene raggiunta la soglia di utilizzo della CPU, il front-end verrà prima ridimensionato. Se l'utilizzo della CPU scende alla soglia di riduzione delle prestazioni, viene eseguita un'operazione di riduzione del livello. La scalabilità verticale e orizzontale si verifica solo se sono disponibili risorse cluster sufficienti.

## <a name="deploy-to-aks"></a>Distribuire in servizio Azure Kubernetes

Per distribuire un modello nel servizio Azure Kubernetes, creare una __configurazione di distribuzione__ che descriva le risorse di calcolo necessarie. Ad esempio, numero di core e memoria. È inoltre necessaria una __configurazione di inferenza__, che descrive l'ambiente necessario per ospitare il modello e il servizio Web. Per ulteriori informazioni sulla creazione della configurazione di inferenza, vedere [come e dove distribuire i modelli](how-to-deploy-and-where.md).

> [!NOTE]
> Il numero di modelli da distribuire è limitato a 1.000 modelli per distribuzione (per contenitore).

<a id="using-the-cli"></a>

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.webservice import AksWebservice, Webservice
from azureml.core.model import Model

aks_target = AksCompute(ws,"myaks")
# If deploying to a cluster configured for dev/test, ensure that it was created with enough
# cores and memory to handle this deployment configuration. Note that memory is also used by
# things such as dependencies and AML components.
deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config, aks_target)
service.wait_for_deployment(show_output = True)
print(service.state)
print(service.get_logs())
```

Per ulteriori informazioni sulle classi, i metodi e i parametri utilizzati in questo esempio, vedere i documenti di riferimento seguenti:

* [AksCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute?view=azure-ml-py&preserve-view=true)
* [AksWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py&preserve-view=true)
* [Model. deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [WebService. wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truewait-for-deployment-show-output-false-)

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per eseguire la distribuzione usando l'interfaccia della riga di comando, usare il comando seguente. Sostituire `myaks` con il nome della destinazione di calcolo AKS. Sostituire `mymodel:1` con il nome e la versione del modello registrato. Sostituire `myservice` con il nome da assegnare al servizio:

```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

Per ulteriori informazioni, vedere il riferimento [AZ ml Model deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-model-deploy) .

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Per informazioni sull'uso di VS Code, vedere [Deploy to AKS by the vs code Extension](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model).

> [!IMPORTANT]
> Per la distribuzione tramite VS Code è necessario che il cluster AKS venga creato o collegato all'area di lavoro in anticipo.

---

### <a name="autoscaling"></a>Scalabilità automatica

Il componente che gestisce la scalabilità automatica per le distribuzioni di modelli di Azure ML è azureml-Fe, che è un router di richiesta intelligente. Poiché tutte le richieste di inferenza passano attraverso di essa, sono presenti i dati necessari per ridimensionare automaticamente i modelli distribuiti.

> [!IMPORTANT]
> * Non **abilitare Kubernetes Horizontal Pod AutoScaler (hPa) per le distribuzioni di modelli**. Questa operazione causerebbe la competizione tra i due componenti di ridimensionamento automatico. Azureml-Fe è progettato per la scalabilità automatica dei modelli distribuiti da Azure ML, in cui HPA dovrebbe indovinare o approssimarsi all'utilizzo del modello da una metrica generica, ad esempio l'utilizzo della CPU o una configurazione della metrica personalizzata.
> 
> * **Azureml-Fe non ridimensiona il numero di nodi in un cluster AKS**, perché ciò potrebbe causare un aumento imprevisto dei costi. Ridimensiona invece **il numero di repliche per il modello** all'interno dei limiti del cluster fisico. Se è necessario ridimensionare il numero di nodi all'interno del cluster, è possibile ridimensionare manualmente il cluster o [configurare il servizio di scalabilità automatica del cluster AKS](/azure/aks/cluster-autoscaler).

La scalabilità automatica può essere controllata impostando `autoscale_target_utilization` , `autoscale_min_replicas` e `autoscale_max_replicas` per il servizio Web AKS. Nell'esempio seguente viene illustrato come abilitare la scalabilità automatica:

```python
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=True, 
                                                autoscale_target_utilization=30,
                                                autoscale_min_replicas=1,
                                                autoscale_max_replicas=4)
```

Le decisioni per la scalabilità verticale e orizzontale sono basate sull'utilizzo delle repliche del contenitore correnti. Il numero di repliche occupate (elaborazione di una richiesta) divise per il numero totale di repliche correnti è l'utilizzo corrente. Se questo numero è superiore a `autoscale_target_utilization` , vengono create altre repliche. Se è inferiore, le repliche vengono ridotte. Per impostazione predefinita, l'utilizzo della destinazione è pari al 70%.

Le decisioni di aggiunta di repliche sono ansiose e veloci (circa 1 secondo). Le decisioni di rimozione delle repliche sono conservative (circa 1 minuto).

È possibile calcolare le repliche richieste usando il codice seguente:

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

Per ulteriori informazioni sull'impostazione di `autoscale_target_utilization` , `autoscale_max_replicas` e `autoscale_min_replicas` , vedere il riferimento al modulo [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py&preserve-view=true) .

## <a name="deploy-models-to-aks-using-controlled-rollout-preview"></a>Distribuire modelli in AKS usando l'implementazione controllata (anteprima)

Analizzare e innalzare di livello le versioni del modello in modo controllato usando gli endpoint. È possibile distribuire fino a sei versioni dietro un singolo endpoint. Gli endpoint offrono le funzionalità seguenti:

* Configurare la __percentuale di assegnazione del punteggio al traffico inviato a ogni endpoint__. Ad esempio, instrada il 20% del traffico all'endpoint "test" e il 80% a "produzione".

    > [!NOTE]
    > Se non si tiene conto del 100% del traffico, qualsiasi percentuale rimanente viene instradata alla versione dell'endpoint __predefinito__ . Se, ad esempio, si configura la versione endpoint ' test ' per ottenere il 10% del traffico è prod ' per il 30%, il 60% rimanente verrà inviato alla versione dell'endpoint predefinito.
    >
    > La prima versione dell'endpoint creata viene automaticamente configurata come predefinita. È possibile modificare questo valore impostando `is_default=True` quando si crea o si aggiorna una versione dell'endpoint.
     
* Contrassegnare una versione dell'endpoint come __controllo__ o __trattamento__. Ad esempio, la versione corrente dell'endpoint di produzione potrebbe essere il controllo, mentre i nuovi modelli potenziali vengono distribuiti come versioni di trattamento. Dopo aver valutato le prestazioni delle versioni di trattamento, se una supera il controllo corrente, è possibile che venga innalzata al nuovo ambiente di produzione/controllo.

    > [!NOTE]
    > È possibile avere __un__ solo controllo. È possibile avere più trattamenti.

È possibile abilitare App Insights per visualizzare le metriche operative degli endpoint e delle versioni distribuite.

### <a name="create-an-endpoint"></a>Creare un endpoint
Quando si è pronti per distribuire i modelli, creare un endpoint di assegnazione dei punteggi e distribuire la prima versione. Nell'esempio seguente viene illustrato come distribuire e creare l'endpoint utilizzando l'SDK. La prima distribuzione verrà definita come versione predefinita, il che significa che il percentile di traffico non specificato in tutte le versioni passerà alla versione predefinita.  

> [!TIP]
> Nell'esempio seguente la configurazione imposta la versione iniziale dell'endpoint per gestire il 20% del traffico. Poiché si tratta del primo endpoint, si tratta anche della versione predefinita. Poiché non sono disponibili altre versioni per l'altro 80% del traffico, viene indirizzato anche al valore predefinito. Fino a quando non vengono distribuite altre versioni che accettano una percentuale di traffico, questo riceve effettivamente il 100% del traffico.

```python
import azureml.core,
from azureml.core.webservice import AksEndpoint
from azureml.core.compute import AksCompute
from azureml.core.compute import ComputeTarget
# select a created compute
compute = ComputeTarget(ws, 'myaks')
namespace_name= endpointnamespace
# define the endpoint and version name
endpoint_name = "mynewendpoint"
version_name= "versiona"
# create the deployment config and define the scoring traffic percentile for the first deployment
endpoint_deployment_config = AksEndpoint.deploy_configuration(cpu_cores = 0.1, memory_gb = 0.2,
                                                              enable_app_insights = True,
                                                              tags = {'sckitlearn':'demo'},
                                                              description = "testing versions",
                                                              version_name = version_name,
                                                              traffic_percentile = 20)
 # deploy the model and endpoint
 endpoint = Model.deploy(ws, endpoint_name, [model], inference_config, endpoint_deployment_config, compute)
 # Wait for he process to complete
 endpoint.wait_for_deployment(True)
 ```

### <a name="update-and-add-versions-to-an-endpoint"></a>Aggiornare e aggiungere versioni a un endpoint

Aggiungere un'altra versione all'endpoint e configurare il punteggio del traffico di assegnazione dei punteggi per la versione. Esistono due tipi di versioni, un controllo e una versione di trattamento. Possono essere disponibili più versioni di trattamento che consentono di eseguire il confronto con una sola versione del controllo.

> [!TIP]
> La seconda versione, creata dal frammento di codice seguente, accetta il 10% del traffico. La prima versione è configurata per il 20%, quindi solo il 30% del traffico viene configurato per versioni specifiche. Il 70% rimanente viene inviato alla prima versione dell'endpoint, perché è anche la versione predefinita.

 ```python
from azureml.core.webservice import AksEndpoint

# add another model deployment to the same endpoint as above
version_name_add = "versionb"
endpoint.create_version(version_name = version_name_add,
                        inference_config=inference_config,
                        models=[model],
                        tags = {'modelVersion':'b'},
                        description = "my second version",
                        traffic_percentile = 10)
endpoint.wait_for_deployment(True)
```

Aggiornare le versioni esistenti o eliminarle in un endpoint. È possibile modificare il tipo predefinito, il tipo di controllo e il percentile del traffico della versione. Nell'esempio seguente, la seconda versione aumenta il traffico al 40% ed è ora il valore predefinito.

> [!TIP]
> Dopo il frammento di codice seguente, la seconda versione è ora predefinita. È ora configurato per il 40%, mentre la versione originale è ancora configurata per il 20%. Ciò significa che il 40% del traffico non è contabilizzato dalle configurazioni della versione. Il traffico rimanente verrà indirizzato alla seconda versione, perché ora è il valore predefinito. Riceve effettivamente il 80% del traffico.

 ```python
from azureml.core.webservice import AksEndpoint

# update the version's scoring traffic percentage and if it is a default or control type
endpoint.update_version(version_name=endpoint.versions["versionb"].name,
                        description="my second version update",
                        traffic_percentile=40,
                        is_default=True,
                        is_control_version_type=True)
# Wait for the process to complete before deleting
endpoint.wait_for_deployment(true)
# delete a version in an endpoint
endpoint.delete_version(version_name="versionb")

```

## <a name="web-service-authentication"></a>Autenticazione del servizio Web

Quando si esegue la distribuzione nel servizio Azure Kubernetes, l'autenticazione __basata su chiavi__ è abilitata per impostazione predefinita. È anche possibile abilitare l'autenticazione __basata su token__ . Per l'autenticazione basata su token è necessario che i client usino un account Azure Active Directory per richiedere un token di autenticazione, usato per eseguire richieste al servizio distribuito.

Per __disabilitare__ l'autenticazione, impostare il `auth_enabled=False` parametro durante la creazione della configurazione di distribuzione. Nell'esempio seguente viene disabilitata l'autenticazione tramite l'SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, auth_enabled=False)
```

Per informazioni sull'autenticazione da un'applicazione client, vedere la pagina relativa all' [utilizzo di un modello di Azure machine learning distribuito come servizio Web](how-to-consume-web-service.md).

### <a name="authentication-with-keys"></a>Autenticazione con chiavi

Se è abilitata l'autenticazione della chiave, è possibile usare il `get_keys` metodo per recuperare una chiave di autenticazione primaria e secondaria:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Se è necessario rigenerare una chiave, usare [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py&preserve-view=true)

### <a name="authentication-with-tokens"></a>Autenticazione con token

Per abilitare l'autenticazione del token, impostare il `token_auth_enabled=True` parametro durante la creazione o l'aggiornamento di una distribuzione. Nell'esempio seguente viene abilitata l'autenticazione del token tramite l'SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, token_auth_enabled=True)
```

Se l'autenticazione basata su token è abilitata, è possibile usare il `get_token` metodo per recuperare un token JWT e l'ora di scadenza del token:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Sarà necessario richiedere un nuovo token dopo l'ora del token `refresh_by` .
>
> Microsoft consiglia di creare l'area di lavoro di Azure Machine Learning nella stessa area del cluster del servizio Azure Kubernetes. Per eseguire l'autenticazione con un token, il servizio Web effettuerà una chiamata all'area in cui viene creata l'area di lavoro Azure Machine Learning. Se l'area dell'area di lavoro non è disponibile, non sarà possibile recuperare un token per il servizio Web, anche se il cluster si trova in un'area diversa da quella dell'area di lavoro. In questo modo, l'autenticazione basata su token risulta non disponibile fino a quando l'area dell'area di lavoro non sarà nuovamente disponibile. Inoltre, maggiore è la distanza tra l'area del cluster e l'area dell'area di lavoro, più tempo sarà necessario per recuperare un token.
>
> Per recuperare un token, è necessario usare l'SDK Azure Machine Learning o il comando [AZ ml Service Get-Access-token](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-service-get-access-token) .


### <a name="vulnerability-scanning"></a>Analisi delle vulnerabilità

Il Centro sicurezza di Azure fornisce la gestione unificata della sicurezza e la protezione avanzata dalle minacce per carichi di lavoro cloud ibridi. È consigliabile consentire al centro sicurezza di Azure di analizzare le risorse e seguire le indicazioni. Per altre informazioni, vedere [integrazione dei servizi Kubernetes di Azure con il Centro sicurezza](https://docs.microsoft.com/azure/security-center/azure-kubernetes-service-integration).

## <a name="next-steps"></a>Passaggi successivi

* [Proteggere l'ambiente inferencing con la rete virtuale di Azure](how-to-secure-inferencing-vnet.md)
* [Come distribuire un modello usando un'immagine Docker personalizzata](how-to-deploy-custom-docker-image.md)
* [Risoluzione dei problemi di distribuzione](how-to-troubleshoot-deployment.md)
* [Aggiornare un servizio Web](how-to-deploy-update-web-service.md)
* [Usare TLS per proteggere un servizio Web tramite Azure Machine Learning](how-to-secure-web-service.md)
* [Usare un modello di Machine Learning distribuito come servizio Web](how-to-consume-web-service.md)
* [Monitorare i modelli di Azure Machine Learning con Application Insights](how-to-enable-app-insights.md)
* [Raccogliere i dati per i modelli nell'ambiente di produzione](how-to-enable-data-collection.md)
