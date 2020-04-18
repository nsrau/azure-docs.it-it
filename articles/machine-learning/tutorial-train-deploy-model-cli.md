---
title: Addestrare e distribuire modelli dalla CLI
titleSuffix: Azure Machine Learning
description: Informazioni su come usare l'estensione di Machine Learning per l'interfaccia della riga di comando di Azure per eseguire il training, registrare e distribuire un modello dalla riga di comando.
ms.author: larryfr
author: Blackmist
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: 1cafc311c842cd5bc17fefe34eacbdfc99b7147a
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617724"
---
# <a name="tutorial-train-and-deploy-a-model-from-the-cli"></a>Esercitazione: Eseguire il training e distribuire un modello dalla riga di comandoTutorial: Train and deploy a model from the CLI
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In questa esercitazione si usa l'estensione di Machine Learning per l'interfaccia della riga di comando di Azure per eseguire il training, registrare e distribuire un modello.

Gli script di training Python in questa esercitazione usano [scikit-learn](https://scikit-learn.org/) per eseguire il training di un modello di base. The focus of this tutorial is not on the scripts or the model, but the process of using the CLI to work with Azure Machine Learning.

Si apprenderà a eseguire le operazioni seguenti:

> [!div class="checklist"]
> * Installare l'estensione di apprendimento automaticoInstall the machine learning extension
> * Creare un'area di lavoro di Machine Learning di Azure
> * Creare la risorsa di calcolo utilizzata per eseguire il training del modelloCreate the compute resource used to train the model
> * Definire e registrare il set di dati usato per eseguire il training del modelloDefine and register the dataset used to train the model
> * Avviare un'esecuzione di formazione
> * Registrare e scaricare un modello
> * Distribuire il modello come servizio Web
> * Dati di punteggio utilizzando il servizio Web

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

* Per usare i comandi dell'interfaccia della riga di comando in questo documento **dall'ambiente locale,** è necessario l'interfaccia della riga di comando di [Azure.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

    Se si usa [Azure Cloud Shell,](https://azure.microsoft.com//features/cloud-shell/)l'interfaccia della riga di comando avviene tramite il browser e si trova nel cloud.

## <a name="download-the-example-project"></a>Scarica il progetto di esempio

Per questa esercitazione, scaricare il [https://github.com/microsoft/MLOps](https://github.com/microsoft/MLOps) progetto. I file `examples/cli-train-deploy` nella directory vengono utilizzati dai passaggi descritti in questa esercitazione.

Per ottenere una copia locale dei file, [scaricare un archivio .zip](https://github.com/microsoft/MLOps/archive/master.zip)oppure utilizzare il seguente comando Git per clonare il repository:

```azurecli-interactive
git clone https://github.com/microsoft/MLOps.git
```

### <a name="training-files"></a>File di formazione

La `examples/cli-train-deploy` directory del progetto contiene i seguenti file, che vengono utilizzati durante il training di un modello:

* `.azureml\mnist.runconfig`: file di __configurazione di esecuzione.__ Questo file definisce l'ambiente di runtime necessario per eseguire il training del modello. In questo esempio vengono montati anche i dati utilizzati per eseguire il training del modello nell'ambiente di training.
* `scripts\train.py`: lo script di formazione. Questo file viene eseguito il training del modello.
* `scripts\utils.py`: file helper utilizzato dallo script di training.
* `.azureml\conda_dependencies.yml`: definisce le dipendenze software necessarie per eseguire lo script di training.
* `dataset.json`: la definizione del set di dati. Utilizzato per registrare il set di dati MNIST nell'area di lavoro di Azure Machine Learning.Used to register the MNIST dataset in the Azure Machine Learning workspace.

### <a name="deployment-files"></a>File di distribuzione

Il repository contiene i file seguenti, utilizzati per distribuire il modello sottoposto a training come servizio Web:

* `aciDeploymentConfig.yml`: file di configurazione della __distribuzione.__ Questo file definisce l'ambiente di hosting necessario per il modello.
* `inferenceConfig.json`: file di __configurazione dell'inferenza.__ Questo file definisce l'ambiente software utilizzato dal servizio per assegnare un punteggio ai dati con il modello.
* `score.py`: script pitone che accetta i dati in ingresso, li valuta utilizzando il modello e quindi restituisce una risposta.
* `scoring-env.yml`: dipendenze conda necessarie per `score.py` eseguire il modello e lo script.
* `testdata.json`: file di dati che può essere utilizzato per testare il servizio Web distribuito.

## <a name="connect-to-your-azure-subscription"></a>Connettersi alla sottoscrizione di Azure

Esistono diversi modi per eseguire l'autenticazione alla sottoscrizione di Azure dall'interfaccia della riga di comando. Il più semplice è quello di autenticare in modo interattivo utilizzando un browser. Per eseguire l'autenticazione interattiva, aprire una riga di comando o un terminale e utilizzare il comando seguente:

```azurecli-interactive
az login
```

Se l'interfaccia della riga di comando può aprire il browser predefinito, eseguirà questa operazione e caricherà una pagina di accesso. In caso contrario, è necessario aprire un browser e seguire le istruzioni nella riga di comando. Le istruzioni prevedono la navigazione [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e l'immissione di un codice di autorizzazione.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

## <a name="install-the-machine-learning-extension"></a>Installare l'estensione di apprendimento automaticoInstall the machine learning extension

Per installare l'estensione di apprendimento automatico, usare il comando seguente:To install the machine learning extension, use the following command:

```azurecli-interactive
az extension add -n azure-cli-ml
```

Se viene visualizzato un messaggio che indica che l'estensione è già installata, utilizzare il comando seguente per eseguire l'aggiornamento alla versione più recente:

```azurecli-interactive
az extension update -n azure-cli-ml
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse è un contenitore di base di risorse nella piattaforma Azure.A resource group is a basic container of resources on the Azure platform. Quando si lavora con Azure Machine Learning, il gruppo di risorse conterrà l'area di lavoro di Azure Machine Learning.When working with the Azure Machine Learning, the resource group will contain your Azure Machine Learning workspace. Conterrà anche altri servizi di Azure usati dall'area di lavoro. Ad esempio, se si esegue il training del modello usando una risorsa di calcolo basata su cloud, tale risorsa viene creata nel gruppo di risorse.

Per __creare un nuovo gruppo di risorse,__ utilizzare il comando seguente. Sostituire `<resource-group-name>` con il nome da usare per questo gruppo di risorse. Sostituire `<location>` con l'area di Azure da usare per questo gruppo di risorse:Replace with the Azure region to use for this resource group:

> [!TIP]
> È necessario selezionare un'area in cui è disponibile Azure Machine Learning.You should select a region where the Azure Machine Learning is available. Per informazioni, consultate [Prodotti disponibili per regione.](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

La risposta da questo comando è simile al seguente JSON:

```json
{
  "id": "/subscriptions/<subscription-GUID>/resourceGroups/<resourcegroupname>",
  "location": "<location>",
  "managedBy": null,
  "name": "<resource-group-name>",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

Per altre informazioni sull'uso dei gruppi di risorse, vedere [az group](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest).

## <a name="create-a-workspace"></a>Creare un'area di lavoro

Per creare una nuova area di lavoro, utilizzare il comando seguente. Sostituire `<workspace-name>` con il nome che si desidera utilizzare per l'area di lavoro. Sostituire `<resource-group-name>` con il nome del gruppo di risorse:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

L'output di questo comando è simile al seguente JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

## <a name="connect-local-project-to-workspace"></a>Connettere il progetto locale all'area di lavoro

Da un terminale o da un prompt `cli-train-deploy` dei comandi, utilizzare i seguenti comandi per passare alla directory, quindi connettersi all'area di lavoro:

```azurecli-interactive
cd ~/MLOps/examples/cli-train-deploy
az ml folder attach -w <workspace-name> -g <resource-group-name>
```

L'output di questo comando è simile al seguente JSON:

```json
{
  "Experiment name": "model-training",
  "Project path": "/home/user/MLOps/examples/cli-train-deploy",
  "Resource group": "<resource-group-name>",
  "Subscription id": "<subscription-id>",
  "Workspace name": "<workspace-name>"
}
```

Questo comando `.azureml/config.json` crea un file che contiene le informazioni necessarie per connettersi all'area di lavoro. Il resto `az ml` dei comandi usati in questa esercitazione userà questo file, pertanto non è necessario aggiungere l'area di lavoro e il gruppo di risorse a tutti i comandi.

## <a name="create-the-compute-target-for-training"></a>Creare la destinazione di calcolo per il trainingCreate the compute target for training

Questo esempio usa un cluster di calcolo di Azure Machine Learning per eseguire il training del modello. Per creare un nuovo cluster di calcolo, utilizzare il comando seguente:To create a new compute cluster, use the following command:

```azurecli-interactive
az ml computetarget create amlcompute -n cpu-cluster --max-nodes 4 --vm-size Standard_D2_V2
```

L'output di questo comando è simile al seguente JSON:

```json
{
  "location": "<location>",
  "name": "cpu-cluster",
  "provisioningErrors": null,
  "provisioningState": "Succeeded"
}
```

Questo comando crea una `cpu-cluster`nuova destinazione di calcolo denominata , con un massimo di quattro nodi. La dimensione della macchina virtuale selezionata fornisce una macchina virtuale con una risorsa GPU. Per informazioni sulle dimensioni della macchina virtuale, vedere [Tipi e dimensioni della macchina virtuale].

> [!IMPORTANT]
> Il nome della destinazione`cpu-cluster` di calcolo (in questo caso) è importante; vi viene fatto `.azureml/mnist.runconfig` riferimento dal file utilizzato nella sezione successiva.

## <a name="define-the-dataset"></a>Definire il set di datiDefine the dataset

Per eseguire il training di un modello, è possibile fornire i dati di training usando un set di dati. Per creare un set di dati dall'interfaccia della riga di comando, è necessario fornire un file di definizione del set di dati. Il `dataset.json` file fornito nel repository crea un nuovo set di dati utilizzando i dati MNIST. Il dataset creato `mnist-dataset`è denominato .

Per registrare il `dataset.json` set di dati utilizzando il file, utilizzare il comando seguente:

```azurecli-interactive
az ml dataset register -f dataset.json --skip-validation
```

L'output di questo comando è simile al seguente JSON:

```json
{
  "definition": [
    "GetFiles"
  ],
  "registration": {
    "description": "mnist dataset",
    "id": "a13a4034-02d1-40bd-8107-b5d591a464b7",
    "name": "mnist-dataset",
    "tags": {
      "sample-tag": "mnist"
    },
    "version": 1,
    "workspace": "Workspace.create(name='myworkspace', subscription_id='mysubscriptionid', resource_group='myresourcegroup')"
  },
  "source": [
    "http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz"
  ]
}
```

> [!IMPORTANT]
> Copiare il `id` valore della voce, come viene utilizzato nella sezione successiva.

Per visualizzare un modello più completo per un set di dati, usare il comando seguente:To see a more comprehensive template for a dataset, use the following command:

```azurecli-interactive
az ml dataset register --show-template
```

## <a name="reference-the-dataset"></a>Fare riferimento al set di datiReference the dataset

Per rendere il set di dati disponibile nell'ambiente di training, è necessario farvi riferimento dal file runconfig. Il `.azureml/mnist.runconfig` file contiene le seguenti voci YAML:

```yaml
# The arguments to the script file.
arguments:
- --data-folder
- DatasetConsumptionConfig:mnist

.....

# The configuration details for data.
data:
  mnist:
# Data Location
    dataLocation:
# the Dataset used for this run.
      dataset:
# Id of the dataset.
        id: a13a4034-02d1-40bd-8107-b5d591a464b7
# the DataPath used for this run.
      datapath:
# Whether to create new folder.
    createOutputDirectories: false
# The mode to handle
    mechanism: mount
# Point where the data is download or mount or upload.
    environmentVariableName: mnist
# relative path where the data is download or mount or upload.
    pathOnCompute:
# Whether to overwrite the data if existing.
    overwrite: false
```

Modificare il valore `id` della voce in modo che corrisponda al valore restituito durante la registrazione del set di dati. Questo valore viene usato per caricare i dati nella destinazione di calcolo durante il training.

Questo YAML si traduce nelle seguenti azioni durante l'allenamento:

* Monta il set di dati (in base all'ID del set di dati) `mnist` nell'ambiente di training e archivia il percorso del punto di montaggio nella variabile di ambiente.
* Passa la posizione dei dati (punto di montaggio) `--data-folder` all'interno dell'ambiente di training allo script usando l'argomento .

Il file runconfig contiene inoltre informazioni utilizzate per configurare l'ambiente utilizzato dall'esecuzione del training. Se si esamina questo file, si noterà che fa riferimento alla `cpu-compute` destinazione di calcolo creata in precedenza. Elenca inoltre il numero di nodi`"nodeCount": "4"`da utilizzare `"condaDependencies"` durante il training ( ) e contiene una sezione che elenca i pacchetti Python necessari per eseguire lo script di training.

> [!TIP]
> Sebbene sia possibile creare manualmente un file runconfig, quello `generate-runconfig.py` in questo esempio è stato creato utilizzando il file incluso nel repository. Questo file ottiene un riferimento al set di dati registrato, crea una configurazione eseguita a livello di codice e quindi lo rende persistente in file.

Per ulteriori informazioni sui file di configurazione di esecuzione, vedere [Impostare e utilizzare le destinazioni](how-to-set-up-training-targets.md#create-run-configuration-and-submit-run-using-azure-machine-learning-cli)di calcolo per il training del modello. Per un riferimento JSON completo, vedere [runconfigschema.json](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json).

## <a name="submit-the-training-run"></a>Inviare l'esecuzione della formazione

Per avviare un'esecuzione di training nella destinazione di calcolo, usare il comando seguente:To start a training run on the `cpu-cluster` compute target, use the following command:

```azurecli-interactive
az ml run submit-script -c mnist -e myexperiment --source-directory scripts -t runoutput.json
```

Questo comando specifica un nome`myexperiment`per l'esperimento ( ). L'esperimento archivia le informazioni su questa esecuzione nell'area di lavoro.

Il `-c mnist` parametro `.azureml/mnist.runconfig` specifica il file.

Il `-t` parametro archivia un riferimento a questa esecuzione in un file JSON e verrà usato nei passaggi successivi per registrare e scaricare il modello.

Durante i processi di esecuzione del training, trasmette le informazioni dalla sessione di training sulla risorsa di calcolo remota. Parte delle informazioni è simile al testo seguente:

```output
Predict the test set
Accuracy is 0.9185
```

Questo testo viene registrato dallo script di training e visualizza l'accuratezza del modello. Altri modelli avranno metriche delle prestazioni diverse.

Se si esamina lo script di training, si noterà che utilizza anche `outputs/sklearn_mnist_model.pkl`il valore alfa quando archivia il modello sottoposto a training in .

Il modello è `./outputs` stato salvato nella directory nella destinazione di calcolo in cui è stato eseguito il training. In questo caso, l'istanza di Azure Machine Learning Compute nel cloud di Azure.In this case, the Azure Machine Learning Compute instance in the Azure cloud. Il processo di training carica `./outputs` automaticamente il contenuto della directory dalla destinazione di calcolo in cui viene eseguito il training nell'area di lavoro di Azure Machine Learning.The training process automatically uploads the contents of the directory from the compute target where training occurs to your Azure Machine Learning workspace. Viene archiviato come parte dell'esperimento (in`myexperiment` questo esempio).

## <a name="register-the-model"></a>Registrare il modello

Per registrare il modello direttamente dalla versione archiviata nell'esperimento, utilizzare il comando seguente:

```azurecli-interactive
az ml model register -n mymodel -f runoutput.json --asset-path "outputs/sklearn_mnist_model.pkl" -t registeredmodel.json
```

Questo comando registra `outputs/sklearn_mnist_model.pkl` il file creato dall'esecuzione del `mymodel`training come nuova registrazione del modello denominato . I `--assets-path` riferimenti a un percorso in un esperimento. In questo caso, le informazioni sull'esperimento e sull'esecuzione `runoutput.json` vengono caricate dal file creato dal comando training. L'oggetto `-t registeredmodel.json` crea un file JSON che fa riferimento al nuovo modello registrato creato da questo comando e viene usato da altri comandi dell'interfaccia della riga di comando che funzionano con i modelli registrati.

L'output di questo comando è simile al seguente JSON:

```json
{
  "createdTime": "2019-09-19T15:25:32.411572+00:00",
  "description": "",
  "experimentName": "myexperiment",
  "framework": "Custom",
  "frameworkVersion": null,
  "id": "mymodel:1",
  "name": "mymodel",
  "properties": "",
  "runId": "myexperiment_1568906070_5874522d",
  "tags": "",
  "version": 1
}
```

### <a name="model-versioning"></a>Gestione della versione dei modelli

Prendere nota del numero di versione restituito per il modello. La versione viene incrementata ogni volta che si registra un nuovo modello con questo nome. Ad esempio, è possibile scaricare il modello e registrarlo da un file locale utilizzando i seguenti comandi:

```azurecli-interactive
az ml model download -i "mymodel:1" -t .
az ml model register -n mymodel -p "sklearn_mnist_model.pkl"
```

Il primo comando scarica il modello registrato nella directory corrente. Il nome `sklearn_mnist_model.pkl`del file è , ovvero il file a cui si fa riferimento al momento della registrazione del modello. Il secondo comando registra il`-p "sklearn_mnist_model.pkl"`modello locale ( ) con`mymodel`lo stesso nome della registrazione precedente ( ). Questa volta, i dati JSON restituiti elenca novano la versione come 2.This time, the JSON data returned lists the version as 2.

## <a name="deploy-the-model"></a>Distribuire il modello

Per distribuire un modello, utilizzare il comando seguente:To deploy a model, use the following command:

```azurecli-interactive
az ml model deploy -n myservice -m "mymodel:1" --ic inferenceConfig.json --dc aciDeploymentConfig.yml
```

> [!NOTE]
> È possibile che venga visualizzato un avviso relativo a "Impossibile controllare l'esistenza di LocalWebservice" o "Impossibile creare il client Docker". È possibile ignorare questa situazione, in quanto non si distribuisce un servizio Web locale.

Questo comando consente di `myservice`distribuire un nuovo servizio denominato , utilizzando la versione 1 del modello registrato in precedenza.

Il `inferenceConfig.yml` file fornisce informazioni su come utilizzare il modello per l'inferenza. Ad esempio, fa riferimento`score.py`allo script di immissione ( ) e alle dipendenze software.

Per ulteriori informazioni sulla struttura di questo file, vedere lo schema di [configurazione di inferenza](reference-azure-machine-learning-cli.md#inference-configuration-schema). Per altre informazioni sugli script di immissione, vedere Distribuire modelli con Azure Machine Learning.For more information on entry scripts, see [Deploy models with the Azure Machine Learning](how-to-deploy-and-where.md#prepare-to-deploy).

Descrive `aciDeploymentConfig.yml` l'ambiente di distribuzione utilizzato per ospitare il servizio. La configurazione di distribuzione è specifica del tipo di calcolo utilizzato per la distribuzione. In questo caso, viene usata un'istanza del contenitore di Azure.In this case, an Azure Container Instance is used. Per ulteriori informazioni, vedere lo [schema di configurazione della distribuzione](reference-azure-machine-learning-cli.md#deployment-configuration-schema).

Ci vorranno alcuni minuti prima del completamento del processo di distribuzione.

> [!TIP]
> In questo esempio vengono usate istanze del contenitore di Azure.In this example, Azure Container Instances is used. Le distribuzioni di ACI creano automaticamente la risorsa ACI necessaria. Se si dovesse invece eseguire la distribuzione nel servizio Azure Kubernetes, è necessario `az ml model deploy` creare un cluster AKS in anticipo e specificarlo come parte del comando. Per un esempio di distribuzione in AKS, vedere Distribuire un modello in un cluster di [servizi Azure Kubernetes.](how-to-deploy-azure-kubernetes-service.md)

Dopo alcuni minuti, vengono restituite informazioni simili al codice JSON seguente:After several minutes, information similar to the following JSON is returned:

```json
ACI service creation operation finished, operation "Succeeded"
{
  "computeType": "ACI",
  {...ommitted for space...}
  "runtimeType": null,
  "scoringUri": "http://6c061467-4e44-4f05-9db5-9f9a22ef7a5d.eastus2.azurecontainer.io/score",
  "state": "Healthy",
  "tags": "",
  "updatedAt": "2019-09-19T18:22:32.227401+00:00"
}
```

### <a name="the-scoring-uri"></a>URI di punteggio

Il `scoringUri` restituito dalla distribuzione è l'endpoint REST per un modello distribuito come servizio Web.The returned from the deployment is the REST endpoint for a model deployed as a web service. È anche possibile ottenere questo URI utilizzando il comando seguente:You can also get this URI by using the following command:

```azurecli-interactive
az ml service show -n myservice
```

Questo comando restituisce lo stesso `scoringUri`documento JSON, incluso il file .

L'endpoint REST può essere usato per inviare dati al servizio. Per informazioni sulla creazione di un'applicazione client che invia dati al servizio, vedere Usare un modello di [Azure Machine Learning distribuito come servizio WebFor](how-to-consume-web-service.md) information on creating a client application that sends data to the service, see Consume an Azure Machine Learning model deployed as a web service

### <a name="send-data-to-the-service"></a>Inviare dati al servizio

Sebbene sia possibile creare un'applicazione client per chiamare l'endpoint, l'interfaccia della riga di comando di Machine Learning fornisce un'utilità che può fungere da client di test. Utilizzare il comando seguente per `testdata.json` inviare dati nel file al servizio:

```azurecli-interactive
az ml service run -n myservice -d @testdata.json
```

> [!TIP]
> Se si usa PowerShell, usare invece il comando seguente:If you use PowerShell, use the following command instead:
>
> ```azurecli-interactive
> az ml service run -n myservice -d `@testdata.json
> ```

La risposta del comando `[ 3 ]`è simile a .

## <a name="clean-up-resources"></a>Pulire le risorse

> [!IMPORTANT]
> Le risorse create possono essere usate come prerequisiti per altre esercitazioni e procedure dettagliate per Azure Machine Learning.

### <a name="delete-deployed-service"></a>Elimina servizio distribuito

Se si prevede di continuare a usare l'area di lavoro di Azure Machine Learning, ma si vuole eliminare il servizio distribuito per ridurre i costi, usare il comando seguente:If you plan to continue to use the Azure Machine Learning workspace, but want to get rid of the deployed service to reduce costs, use the following command:

```azurecli-interactive
az ml service delete -n myservice
```

Questo comando restituisce un documento JSON che contiene il nome del servizio eliminato. Potrebbero essere alcuni minuti prima che il servizio venga eliminato.

### <a name="delete-the-training-compute"></a>Eliminare il calcolo del trainingDelete the training compute

Se si prevede di continuare a usare l'area di `cpu-cluster` lavoro di Azure Machine Learning, ma si vuole eliminare la destinazione di calcolo creata per il training, usare il comando seguente:If you plan to continue to use the Azure Machine Learning workspace, but want to get rid of the compute target created for training, use the following command:

```azurecli-interactive
az ml computetarget delete -n cpu-cluster
```

Questo comando restituisce un documento JSON che contiene l'ID della destinazione di calcolo eliminata. Potrebbero essere stati alcuni minuti prima che la destinazione di calcolo sia stata eliminata.

### <a name="delete-everything"></a>Eliminare tutto

Se non prevedi di utilizzare le risorse che hai creato, eliminale in modo da non incorrere in costi aggiuntivi.

Per eliminare il gruppo di risorse e tutte le risorse di Azure create in questo documento, usare il comando seguente. Sostituire `<resource-group-name>` con il nome del gruppo di risorse creato in precedenza:

```azurecli-interactive
az group delete -g <resource-group-name> -y
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione di Azure Machine Learning è stata usata l'interfaccia della riga di comando per l'apprendimento automatico per le attività seguenti:In this Azure Machine Learning tutorial, you used the Machine learning CLI for the following tasks:

> [!div class="checklist"]
> * Installare l'estensione di apprendimento automaticoInstall the machine learning extension
> * Creare un'area di lavoro di Machine Learning di Azure
> * Creare la risorsa di calcolo utilizzata per eseguire il training del modelloCreate the compute resource used to train the model
> * Definire e registrare il set di dati usato per eseguire il training del modelloDefine and register the dataset used to train the model
> * Avviare un'esecuzione di formazione
> * Registrare e scaricare un modello
> * Distribuire il modello come servizio Web
> * Dati di punteggio utilizzando il servizio Web

Per altre informazioni sull'uso dell'interfaccia della riga di comando, vedere [Usare l'estensione DELL'interfaccia della riga](reference-azure-machine-learning-cli.md)di comando per Azure Machine Learning.For more information on using the CLI, see Use the CLI extension for Azure Machine Learning .
