---
title: Distribuire modelli ML nel servizio Kubernetes
titleSuffix: Azure Machine Learning
description: Informazioni su come distribuire i modelli di Azure Machine Learning come servizio Web usando il servizio Azure Kubernetes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/23/2020
ms.openlocfilehash: 03477fa46aaec04c0563ed38b085605dce5b87a1
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2020
ms.locfileid: "88751734"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Distribuire un modello in un cluster del servizio Kubernetes di Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Informazioni su come usare Azure Machine Learning per distribuire un modello come servizio Web in Azure Kubernetes Service (AKS). Il servizio Azure Kubernetes è ideale per distribuzioni di produzione su vasta scala. Usare il servizio Azure Kubernetes se sono necessarie una o più delle funzionalità seguenti:

- __Tempo di risposta rapido__.
- __Scalabilità__ automatica del servizio distribuito.
- Opzioni di __accelerazione hardware__ quali GPU e FPGA (Field-Programmable Gate Array).

> [!IMPORTANT]
> Il ridimensionamento del cluster non viene fornito tramite il Azure Machine Learning SDK. Per ulteriori informazioni sul ridimensionamento dei nodi in un cluster AKS, vedere 
- [Ridimensionare manualmente il numero di nodi in un cluster AKS](../aks/scale-cluster.md)
- [Configurare la scalabilità automatica del cluster in AKS](../aks/cluster-autoscaler.md)

Quando si esegue la distribuzione nel servizio Azure Kubernetes, viene distribuito in un cluster AKS __connesso all'area di lavoro__. Esistono due modi per connettere un cluster AKS all'area di lavoro:

* Creare il cluster AKS usando il Azure Machine Learning SDK, l'interfaccia della riga di comando di Machine Learning o [Azure Machine Learning Studio](https://ml.azure.com). Questo processo connette automaticamente il cluster all'area di lavoro.
* Alleghi un cluster AKS esistente all'area di lavoro Azure Machine Learning. È possibile collegare un cluster usando il Azure Machine Learning SDK, l'interfaccia della riga di comando di Machine Learning o Azure Machine Learning Studio.

Il cluster AKS e l'area di lavoro di AML possono trovarsi in gruppi di risorse diversi.

> [!IMPORTANT]
> Il processo di creazione o allegato è un'attività una sola volta. Quando un cluster AKS è connesso all'area di lavoro, è possibile usarlo per le distribuzioni. È possibile scollegare o eliminare il cluster AKS se non è più necessario. Una volta scollegato o eliminato, non sarà più possibile eseguire la distribuzione nel cluster.

> [!IMPORTANT]
> Si consiglia di eseguire il debug in locale prima della distribuzione nel servizio Web. Per ulteriori informazioni, vedere [debug in locale](https://docs.microsoft.com/azure/machine-learning/how-to-troubleshoot-deployment#debug-locally)
>
> È anche possibile fare riferimento ad Azure Machine Learning - [Eseguire la distribuzione a un notebook locale](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-to-local)

## <a name="prerequisites"></a>Prerequisiti

- Un'area di lavoro di Azure Machine Learning. Per altre informazioni, vedere [creare un'area di lavoro Azure Machine Learning](how-to-manage-workspace.md).

- Un modello di apprendimento automatico registrato nell'area di lavoro. Se non si dispone di un modello registrato, vedere [come e dove distribuire i modelli](how-to-deploy-and-where.md).

- Estensione dell'interfaccia della riga [di comando di Azure per il servizio Machine Learning](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)o l' [estensione di Visual Studio code Azure Machine Learning](tutorial-setup-vscode-extension.md).

- I frammenti di codice __Python__ in questo articolo presuppongono che siano impostate le variabili seguenti:

    * `ws` -Impostare sull'area di lavoro.
    * `model` : Impostare sul modello registrato.
    * `inference_config` -Impostare sulla configurazione di inferenza per il modello.

    Per ulteriori informazioni sull'impostazione di queste variabili, vedere [come e dove distribuire i modelli](how-to-deploy-and-where.md).

- I frammenti di codice dell' __interfaccia__ della riga di comando in questo articolo presuppongono che sia stato creato un `inferenceconfig.json` documento. Per ulteriori informazioni sulla creazione di questo documento, vedere [come e dove distribuire i modelli](how-to-deploy-and-where.md).

- Se è necessario un Load Balancer Standard (SLB) distribuito nel cluster anziché una Load Balancer di base (BLB), creare un cluster nel portale AKS/CLI/SDK e quindi collegarlo all'area di lavoro AML.

- Se si dispone di un criterio di Azure che limita la creazione di indirizzi IP pubblici, la creazione del cluster AKS avrà esito negativo. AKS richiede un indirizzo IP pubblico per il [traffico in uscita](https://docs.microsoft.com/azure/aks/limit-egress-traffic). Questo articolo fornisce anche indicazioni per il blocco del traffico in uscita dal cluster tramite l'indirizzo IP pubblico, ad eccezione di alcuni FQDN. Esistono due modi per abilitare un IP pubblico:
  - Il cluster può usare l'indirizzo IP pubblico creato per impostazione predefinita con BLB o SLB oppure
  - Il cluster può essere creato senza un indirizzo IP pubblico e un indirizzo IP pubblico viene configurato con un firewall con una route definita dall'utente, come descritto [qui](https://docs.microsoft.com/azure/aks/egress-outboundtype) 
  
  Il piano di controllo AML non comunica con questo indirizzo IP pubblico. Comunica con il piano di controllo AKS per le distribuzioni. 

- Se si connette un cluster AKS per cui è [abilitato un intervallo di indirizzi IP autorizzati per accedere al server API](https://docs.microsoft.com/azure/aks/api-server-authorized-ip-ranges), abilitare gli intervalli di indirizzi IP del piano di AML per il cluster AKS. Il piano di controllo AML viene distribuito tra le aree abbinate e distribuisce i pod di inferenza nel cluster AKS. Senza l'accesso al server API, non è possibile distribuire i pod di inferenza. Usare gli [intervalli IP](https://www.microsoft.com/en-us/download/confirmation.aspx?id=56519) per entrambe le [aree abbinate]( https://docs.microsoft.com/azure/best-practices-availability-paired-regions) quando si abilitano gli intervalli IP in un cluster AKS.


  Gli intervalli IP Authroized funzionano solo con Load Balancer Standard.
 
 - Il nome di calcolo deve essere univoco all'interno di un'area di lavoro
   - Il nome è obbligatorio e deve avere una lunghezza compresa tra 3 e 24 caratteri.
   - I caratteri validi sono lettere maiuscole e minuscole, cifre e il carattere.
   - Il nome deve iniziare con una lettera
   - Il nome deve essere univoco in tutti i calcoli esistenti all'interno di un'area di Azure. Se il nome scelto non è univoco, verrà visualizzato un avviso
   
 - Se si desidera distribuire modelli a nodi GPU o a nodi FPGA (o a qualsiasi SKU specifico), è necessario creare un cluster con lo SKU specifico. Non è disponibile alcun supporto per la creazione di un pool di nodi secondari in un cluster esistente e la distribuzione di modelli nel pool di nodi secondari.

## <a name="create-a-new-aks-cluster"></a>Creare un nuovo cluster AKS

**Tempo stimato**: circa 10 minuti.

La creazione o il fissaggio di un cluster AKS è un processo di tipo One-Time per l'area di lavoro. È possibile riutilizzare questo cluster per più distribuzioni. Se si elimina il cluster o il gruppo di risorse che lo contiene, è necessario creare un nuovo cluster la volta successiva che è necessario distribuire. È possibile collegare più cluster AKS all'area di lavoro.
 
Azure Machine Learning supporta ora l'uso di un servizio Azure Kubernetes con collegamento privato abilitato.
Per creare un cluster AKS privato, seguire i documenti [qui](https://docs.microsoft.com/azure/aks/private-clusters)

> [!TIP]
> Se si vuole proteggere il cluster AKS con una rete virtuale di Azure, è necessario creare prima la rete virtuale. Per altre informazioni, vedere la pagina relativa a [sperimentazione e inferenza sicure con rete virtuale di Azure](how-to-enable-virtual-network.md#aksvnet).

Se si vuole creare un cluster AKS per __lo sviluppo__, la __convalida__e il __test__ anziché l'ambiente di produzione, è possibile specificare lo __scopo del cluster__ per il __test__di sviluppo.

> [!WARNING]
> Se si imposta `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST` , il cluster creato non è adatto per il traffico a livello di produzione e può aumentare i tempi di inferenza. Anche i cluster di sviluppo/test non garantiscono la tolleranza di errore. Si consiglia almeno 2 CPU virtuali per i cluster di sviluppo/test.

Gli esempi seguenti illustrano come creare un nuovo cluster AKS usando l'SDK e l'interfaccia della riga di comando:

**Uso dell'SDK**

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()
# Example configuration to use an existing virtual network
# prov_config.vnet_name = "mynetwork"
# prov_config.vnet_resourcegroup_name = "mygroup"
# prov_config.subnet_name = "default"
# prov_config.service_cidr = "10.0.0.0/16"
# prov_config.dns_service_ip = "10.0.0.10"
# prov_config.docker_bridge_cidr = "172.17.0.1/16"

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

> [!IMPORTANT]
> Per [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py) , se si scelgono valori personalizzati per `agent_count` e `vm_size` e `cluster_purpose` non è `DEV_TEST` , è necessario assicurarsi che `agent_count` moltiplicato per `vm_size` sia maggiore o uguale a 12 CPU virtuali. Se, ad esempio, si usa un `vm_size` di "Standard_D3_v2", che ha 4 CPU virtuali, è necessario scegliere una `agent_count` di 3 o una versione successiva.
>
> Il Azure Machine Learning SDK non fornisce il supporto per il ridimensionamento di un cluster AKS. Per ridimensionare i nodi del cluster, usare l'interfaccia utente per il cluster AKS in Azure Machine Learning Studio. È possibile modificare solo il numero di nodi, non le dimensioni della macchina virtuale del cluster.

Per ulteriori informazioni sulle classi, i metodi e i parametri utilizzati in questo esempio, vedere i documenti di riferimento seguenti:

* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute. provisioning_configuration](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget. Create](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [ComputeTarget. wait_for_completion](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#wait-for-completion-show-output-false-)

**Uso dell'interfaccia della riga di comando**

```azurecli
az ml computetarget create aks -n myaks
```

Per ulteriori informazioni, vedere il riferimento [AZ ml computetarget create AKS](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-aks) .

## <a name="attach-an-existing-aks-cluster"></a>Alleghi un cluster AKS esistente

**Tempo stimato:** Circa 5 minuti.

Se si dispone già del cluster AKS nella sottoscrizione di Azure ed è la versione 1,17 o una versione precedente, è possibile usarla per distribuire l'immagine.

> [!TIP]
> Il cluster AKS esistente può trovarsi in un'area di Azure diversa dall'area di lavoro Azure Machine Learning.
>
> Se si vuole proteggere il cluster AKS con una rete virtuale di Azure, è necessario creare prima la rete virtuale. Per altre informazioni, vedere la pagina relativa a [sperimentazione e inferenza sicure con rete virtuale di Azure](how-to-enable-virtual-network.md#aksvnet).

Quando si connette un cluster AKS a un'area di lavoro, è possibile definire il modo in cui si userà il cluster impostando il `cluster_purpose` parametro.

Se non si imposta il `cluster_purpose` parametro, o non si imposta `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD` , il cluster deve avere almeno 12 CPU virtuali disponibili.

Se si imposta `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST` , il cluster non deve avere 12 CPU virtuali. Per lo sviluppo e il test sono consigliate almeno 2 CPU virtuali. Tuttavia, un cluster configurato per lo sviluppo/test non è adatto per il traffico a livello di produzione e può aumentare i tempi di inferenza. Anche i cluster di sviluppo/test non garantiscono la tolleranza di errore.

> [!WARNING]
> Non creare più allegati simultanei nello stesso cluster AKS dall'area di lavoro. Ad esempio, se si connette un cluster AKS a un'area di lavoro usando due nomi diversi. Ogni nuovo allegato interromperà gli allegati esistenti precedenti.
>
> Se si vuole ricollegare un cluster AKS, ad esempio per modificare TLS o altre impostazioni di configurazione del cluster, è necessario innanzitutto rimuovere l'allegato esistente usando [AksCompute. Detach ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#detach--).

Per altre informazioni sulla creazione di un cluster AKS con l'interfaccia della riga di comando di Azure o il portale, vedere gli articoli seguenti:

* [Creare un cluster del servizio Azure Kubernetes (interfaccia della riga di comando)](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [Creare un cluster AKS (portale)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)
* [Creare un cluster AKS (modello ARM nei modelli di avvio rapido di Azure)](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aks-azml-targetcompute)

Gli esempi seguenti illustrano come alleghi un cluster AKS esistente all'area di lavoro:

**Uso dell'SDK**

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'myexistingcluster'

# Attach the cluster to your workgroup. If the cluster has less than 12 virtual CPUs, use the following instead:
# attach_config = AksCompute.attach_configuration(resource_group = resource_group,
#                                         cluster_name = cluster_name,
#                                         cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'myaks', attach_config)

# Wait for the attach process to complete
aks_target.wait_for_completion(show_output = True)
```

Per ulteriori informazioni sulle classi, i metodi e i parametri utilizzati in questo esempio, vedere i documenti di riferimento seguenti:

* [AksCompute. attach_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute. Connetti](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#attach-workspace--name--attach-configuration-)

**Uso dell'interfaccia della riga di comando**

Per alleghi un cluster esistente usando l'interfaccia della riga di comando, è necessario ottenere l'ID risorsa del cluster esistente. Per ottenere questo valore, utilizzare il comando seguente. Sostituire `myexistingcluster` con il nome del cluster AKS. Sostituire `myresourcegroup` con il gruppo di risorse che contiene il cluster:

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

Il comando restituisce un valore simile al testo seguente:

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

Per alleghi il cluster esistente all'area di lavoro, usare il comando seguente. Sostituire `aksresourceid` con il valore restituito dal comando precedente. Sostituire `myresourcegroup` con il gruppo di risorse che contiene l'area di lavoro. Sostituire `myworkspace` con il nome dell'area di lavoro.

```azurecli
az ml computetarget attach aks -n myaks -i aksresourceid -g myresourcegroup -w myworkspace
```

Per ulteriori informazioni, vedere la Guida di riferimento al comando [AZ ml computetarget per l'associazione AKS](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks) .

## <a name="deploy-to-aks"></a>Distribuire in servizio Azure Kubernetes

Per distribuire un modello nel servizio Azure Kubernetes, creare una __configurazione di distribuzione__ che descriva le risorse di calcolo necessarie. Ad esempio, numero di core e memoria. È inoltre necessaria una __configurazione di inferenza__, che descrive l'ambiente necessario per ospitare il modello e il servizio Web. Per ulteriori informazioni sulla creazione della configurazione di inferenza, vedere [come e dove distribuire i modelli](how-to-deploy-and-where.md).

### <a name="using-the-sdk"></a>Uso dell'SDK

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

* [AksCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute?view=azure-ml-py)
* [AksWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)
* [Model. deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [WebService. wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>Uso dell'interfaccia della riga di comando

Per eseguire la distribuzione usando l'interfaccia della riga di comando, usare il comando seguente. Sostituire `myaks` con il nome della destinazione di calcolo AKS. Sostituire `mymodel:1` con il nome e la versione del modello registrato. Sostituire `myservice` con il nome da assegnare al servizio:

```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

Per ulteriori informazioni, vedere il riferimento [AZ ml Model deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) .

### <a name="using-vs-code"></a>Uso di VS Code

Per informazioni sull'uso di VS Code, vedere [Deploy to AKS by the vs code Extension](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model).

> [!IMPORTANT]
> Per la distribuzione tramite VS Code è necessario che il cluster AKS venga creato o collegato all'area di lavoro in anticipo.

### <a name="understand-the-deployment-processes"></a>Informazioni sui processi di distribuzione

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
    1. Se non viene trovato, il sistema compila una nuova immagine (che verrà memorizzata nella cache e registrata con l'area di lavoro ACR)
1. Download del file di progetto compresso nell'archiviazione temporanea nel nodo di calcolo
1. Decompressione del file di progetto
1. Nodo di calcolo in esecuzione `python <entry script> <arguments>`
1. Salvataggio dei log, dei file di modello e di altri file scritti nell' `./outputs` account di archiviazione associato all'area di lavoro
1. Riduzione delle prestazioni di calcolo, inclusa la rimozione dell'archiviazione temporanea (in relazione a Kubernetes)

Quando si usa AKS, la scalabilità verso l'alto e verso il basso del calcolo viene controllata da Kubernetes, usando la dockerfile compilata o rilevata come descritto in precedenza. 

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
> Se è necessario rigenerare una chiave, usare [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)

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
> Per recuperare un token, è necessario usare l'SDK Azure Machine Learning o il comando [AZ ml Service Get-Access-token](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-get-access-token) .

## <a name="next-steps"></a>Passaggi successivi

* [Sperimentazione e inferenza sicure in una rete virtuale](how-to-enable-virtual-network.md)
* [Come distribuire un modello usando un'immagine Docker personalizzata](how-to-deploy-custom-docker-image.md)
* [Risoluzione dei problemi di distribuzione](how-to-troubleshoot-deployment.md)
* [Aggiornare un servizio Web](how-to-deploy-update-web-service.md)
* [Usare TLS per proteggere un servizio Web tramite Azure Machine Learning](how-to-secure-web-service.md)
* [Usare un modello di Machine Learning distribuito come servizio Web](how-to-consume-web-service.md)
* [Monitorare i modelli di Azure Machine Learning con Application Insights](how-to-enable-app-insights.md)
* [Raccogliere i dati per i modelli nell'ambiente di produzione](how-to-enable-data-collection.md)
