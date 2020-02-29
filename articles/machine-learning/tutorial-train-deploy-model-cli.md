---
title: Eseguire il training e distribuire modelli dall'interfaccia della riga di comando
titleSuffix: Azure Machine Learning
description: Informazioni su come usare l'estensione Machine Learning per l'interfaccia della riga di comando di Azure per eseguire il training, la registrazione e la distribuzione di un modello dalla riga di comando.
ms.author: larryfr
author: Blackmist
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: 36d6b0cac0321c989fecbc5751d71c744cd83292
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920944"
---
# <a name="tutorial-train-and-deploy-a-model-from-the-cli"></a>Esercitazione: eseguire il training e distribuire un modello dall'interfaccia della riga di comando
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In questa esercitazione si userà l'estensione di machine learning per l'interfaccia della riga di comando di Azure per eseguire il training, la registrazione e la distribuzione di un modello.

Gli script di training Python in questa esercitazione usano [Scikit-learn](https://scikit-learn.org/) per eseguire il training di un modello di base. L'obiettivo di questa esercitazione non è quello degli script o del modello, ma il processo di utilizzo dell'interfaccia della riga di comando per lavorare con Azure Machine Learning.

Si apprenderà a eseguire le operazioni seguenti:

> [!div class="checklist"]
> * Installare l'estensione di Machine Learning
> * Creare un'area di lavoro di Machine Learning di Azure
> * Creare la risorsa di calcolo utilizzata per eseguire il training del modello
> * Definire e registrare il set di dati usato per il training del modello
> * Avviare un'esecuzione di training
> * Registrare e scaricare un modello
> * Distribuire il modello come servizio Web
> * Assegnare punteggi ai dati tramite il servizio Web

## <a name="prerequisites"></a>Prerequisites

* Una sottoscrizione di Azure. Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

* Per usare i comandi dell'interfaccia della riga di comando in questo documento dall' **ambiente locale**, è necessaria l'interfaccia della riga di comando di [Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

    Se si usa il [Azure cloud Shell](https://azure.microsoft.com//features/cloud-shell/), l'interfaccia della riga di comando è accessibile tramite il browser e vive nel cloud.

## <a name="download-the-example-project"></a>Scaricare il progetto di esempio

Per questa esercitazione, scaricare il progetto [https://github.com/microsoft/MLOps](https://github.com/microsoft/MLOps) . I file nella directory `examples/cli-train-deploy` vengono usati nei passaggi di questa esercitazione.

Per ottenere una copia locale dei file, [scaricare un archivio zip](https://github.com/microsoft/MLOps/archive/master.zip)oppure usare il comando git seguente per clonare il repository:

```azurecli-interactive
git clone https://github.com/microsoft/MLOps.git
```

### <a name="training-files"></a>File di training

La directory `examples/cli-train-deploy` dal progetto contiene i file seguenti, che vengono utilizzati per il training di un modello:

* `.azureml\mnist.runconfig`: un file di __configurazione di esecuzione__ . Questo file definisce l'ambiente di runtime necessario per eseguire il training del modello. In questo esempio vengono inoltre montati i dati utilizzati per il training del modello nell'ambiente di training.
* `scripts\train.py`: lo script di training. Questo file addestra il modello.
* `scripts\utils.py`: file helper usato dallo script di training.
* `.azureml\conda_dependencies.yml`: definisce le dipendenze software necessarie per eseguire lo script di training.
* `dataset.json`: la definizione del set di dati. Utilizzato per registrare il set di dati MNIST nell'area di lavoro Azure Machine Learning.

### <a name="deployment-files"></a>File di distribuzione

Il repository contiene i file seguenti, che vengono usati per distribuire il modello sottoposto a training come servizio Web:

* `aciDeploymentConfig.yml`: un file di __configurazione della distribuzione__ . Questo file definisce l'ambiente di hosting necessario per il modello.
* `inferenceConfig.yml`: un file di __configurazione dell'inferenza__ . Questo file definisce l'ambiente software utilizzato dal servizio per assegnare un punteggio ai dati con il modello.
* `score.py`: uno script Python che accetta i dati in ingresso, li assegna un punteggio utilizzando il modello e quindi restituisce una risposta.
* `scoring-env.yml`: dipendenze conda necessarie per eseguire il modello e `score.py` script.
* `testdata.json`: un file di dati che può essere usato per testare il servizio Web distribuito.

## <a name="connect-to-your-azure-subscription"></a>Connettersi alla sottoscrizione di Azure

Sono disponibili diversi modi per eseguire l'autenticazione alla sottoscrizione di Azure dall'interfaccia della riga di comando. Il livello più semplice consiste nell'eseguire l'autenticazione interattiva tramite un browser. Per eseguire l'autenticazione in modo interattivo, aprire una riga di comando o un terminale e usare il comando seguente:

```azurecli-interactive
az login
```

Se l'interfaccia della riga di comando può aprire il browser predefinito, eseguirà questa operazione e caricherà una pagina di accesso. In caso contrario, è necessario aprire un browser e seguire le istruzioni nella riga di comando. Le istruzioni prevedono l'esplorazione [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e l'immissione di un codice di autorizzazione.

## <a name="install-the-machine-learning-extension"></a>Installare l'estensione di Machine Learning

Per installare l'estensione di Machine Learning, usare il comando seguente:

```azurecli-interactive
az extension add -n azure-cli-ml
```

Se viene ricevuto un messaggio che indica che l'estensione è già installata, usare il comando seguente per eseguire l'aggiornamento alla versione più recente:

```azurecli-interactive
az extension update -n azure-cli-ml
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse è un contenitore di base di risorse sulla piattaforma Azure. Quando si lavora con il Azure Machine Learning, il gruppo di risorse conterrà l'area di lavoro Azure Machine Learning. Conterrà anche altri servizi di Azure usati dall'area di lavoro. Ad esempio, se si esegue il training del modello usando una risorsa di calcolo basata sul cloud, tale risorsa viene creata nel gruppo di risorse.

Per __creare un nuovo gruppo di risorse__, usare il comando seguente. Sostituire `<resource-group-name>` con il nome da usare per questo gruppo di risorse. Sostituire `<location>` con l'area di Azure da usare per questo gruppo di risorse:

> [!TIP]
> È necessario selezionare un'area in cui è disponibile la Azure Machine Learning. Per informazioni, vedere [prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service).

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

La risposta da questo comando è simile al codice JSON seguente:

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

Per ulteriori informazioni sull'utilizzo dei gruppi di risorse, vedere [AZ Group](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest).

## <a name="create-a-workspace"></a>Creare un'area di lavoro

Per creare una nuova area di lavoro, usare il comando seguente. Sostituire `<workspace-name>` con il nome che si desidera utilizzare per questa area di lavoro. Sostituire `<resource-group-name>` con il nome del gruppo di risorse:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

L'output di questo comando è simile al codice JSON seguente:

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

## <a name="connect-local-project-to-workspace"></a>Connetti progetto locale all'area di lavoro

Da un terminale o da un prompt dei comandi usare i comandi seguenti per passare alla directory `cli-train-deploy`, quindi connettersi all'area di lavoro:

```azurecli-interactive
cd ~/MLOps/examples/cli-train-deploy
az ml folder attach -w <workspace-name> -g <resource-group-name>
```

L'output di questo comando è simile al codice JSON seguente:

```json
{
  "Experiment name": "model-training",
  "Project path": "/home/user/MLOps/examples/cli-train-deploy",
  "Resource group": "<resource-group-name>",
  "Subscription id": "<subscription-id>",
  "Workspace name": "<workspace-name>"
}
```

Questo comando crea un file di `.azureml/config.json`, che contiene le informazioni necessarie per connettersi all'area di lavoro. Il resto dei comandi `az ml` usati in questa esercitazione utilizzerà questo file, pertanto non è necessario aggiungere l'area di lavoro e il gruppo di risorse a tutti i comandi.

## <a name="create-the-compute-target-for-training"></a>Creare la destinazione di calcolo per il training

Questo esempio usa un cluster di calcolo Azure Machine Learning per eseguire il training del modello. Per creare un nuovo cluster di calcolo, usare il comando seguente:

```azurecli-interactive
az ml computetarget create amlcompute -n cpu-cluster --max-nodes 4 --vm-size Standard_D2_V2
```

L'output di questo comando è simile al codice JSON seguente:

```json
{
  "location": "<location>",
  "name": "cpu-cluster",
  "provisioningErrors": null,
  "provisioningState": "Succeeded"
}
```

Questo comando crea una nuova destinazione di calcolo denominata `cpu`, con un massimo di quattro nodi. Le dimensioni della macchina virtuale selezionate forniscono una macchina virtuale con una risorsa GPU. Per informazioni sulle dimensioni della macchina virtuale, vedere [tipi di VM e dimensioni].

> [!IMPORTANT]
> Il nome della destinazione di calcolo (in questo caso`cpu`) è importante; viene fatto riferimento al file `.azureml/mnist.runconfig` usato nella sezione successiva.

## <a name="define-the-dataset"></a>Definire il set di dati

Per eseguire il training di un modello, è possibile fornire i dati di training usando un set di dati. Per creare un DataSet dall'interfaccia della riga di comando, è necessario fornire un file di definizione del set di dati. Il file di `dataset.json` fornito nel repository crea un nuovo set di dati usando i dati MNIST. Il set di dati creato è denominato `mnist-dataset`.

Per registrare il set di dati utilizzando il file di `dataset.json`, utilizzare il comando seguente:

```azurecli-interactive
az ml dataset register -f dataset.json --skip-validation
```

L'output di questo comando è simile al codice JSON seguente:

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
> Copiare il valore della voce `id`, perché viene usato nella sezione successiva.

Per visualizzare un modello più completo per un set di dati, usare il comando seguente:
```azurecli-interactive
az ml dataset register --show-template
```

## <a name="reference-the-dataset"></a>Riferimento al set di dati

Per rendere disponibile il set di dati nell'ambiente di training, è necessario farvi riferimento dal file runconfig. Il file di `.azureml/mnist.runconfig` contiene le voci YAML seguenti:

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

Modificare il valore della voce `id` in modo che corrisponda al valore restituito quando è stato registrato il set di dati. Questo valore viene usato per caricare i dati nella destinazione di calcolo durante il training.

Questo YAML genera le seguenti azioni durante il training:

* Monta il set di dati (in base all'ID del set di dati) nell'ambiente di training e archivia il percorso del punto di montaggio nella variabile di ambiente `mnist`.
* Passa il percorso dei dati (punto di montaggio) all'interno dell'ambiente di training allo script utilizzando l'argomento `--data-folder`.

Il file runconfig contiene anche le informazioni usate per configurare l'ambiente usato dall'esecuzione del training. Se si esamina questo file, si noterà che fa riferimento alla destinazione di calcolo `cpu-compute` creata in precedenza. Elenca inoltre il numero di nodi da usare per il training (`"nodeCount": "4"`) e contiene una sezione di `"condaDependencies"` in cui sono elencati i pacchetti Python necessari per eseguire lo script di training.

> [!TIP]
> Sebbene sia possibile creare manualmente un file runconfig, quello in questo esempio è stato creato usando il file `generate-runconfig.py` incluso nel repository. Questo file Ottiene un riferimento al set di dati registrato, crea una configurazione di esecuzione a livello, quindi la Salva in modo permanente in un file.

Per altre informazioni sui file di configurazione di esecuzione, vedere [configurare e usare le destinazioni di calcolo per il training del modello](how-to-set-up-training-targets.md#create-run-configuration-and-submit-run-using-azure-machine-learning-cli). Per un riferimento JSON completo, vedere [runconfigschema. JSON](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json).

## <a name="submit-the-training-run"></a>Invia l'esecuzione del training

Per avviare un'esecuzione di training nella destinazione di calcolo `cpu-compute`, usare il comando seguente:

```azurecli-interactive
az ml run submit-script -c mnist -e myexperiment --source-directory scripts -t runoutput.json
```

Questo comando specifica un nome per l'esperimento (`myexperiment`). L'esperimento archivia le informazioni su questa esecuzione nell'area di lavoro.

Il parametro `-c mnist` specifica il file di `.azureml/mnist.runconfig`.

Il parametro `-t` archivia un riferimento a questa esecuzione in un file JSON e verrà usato nei passaggi successivi per la registrazione e il download del modello.

Durante il processo di esecuzione del training, trasmette le informazioni dalla sessione di training sulla risorsa di calcolo remota. Una parte delle informazioni è simile al testo seguente:

```text
Predict the test set
Accuracy is 0.9185
```

Questo testo viene registrato dallo script di training e visualizza l'accuratezza del modello. Altri modelli avranno metriche delle prestazioni diverse.

Se si esamina lo script di training, si noterà che viene usato anche il valore alfa quando archivia il modello sottoposto a training per `outputs/sklearn_mnist_model.pkl`.

Il modello è stato salvato nella directory `./outputs` nella destinazione di calcolo in cui è stato eseguito il training. In questo caso, l'istanza di calcolo Azure Machine Learning nel cloud di Azure. Il processo di training carica automaticamente il contenuto della directory `./outputs` dalla destinazione di calcolo in cui viene eseguito il training nell'area di lavoro di Azure Machine Learning. Viene archiviato come parte dell'esperimento (`myexperiment` in questo esempio).

## <a name="register-the-model"></a>Registrare il modello

Per registrare il modello direttamente dalla versione archiviata nell'esperimento, usare il comando seguente:

```azurecli-interactive
az ml model register -n mymodel -f runoutput.json --asset-path "outputs/sklearn_mnist_model.pkl" -t registeredmodel.json
```

Questo comando registra il file di `outputs/sklearn_mnist_model.pkl` creato dall'esecuzione del training come nuova registrazione del modello denominata `mymodel`. Il `--assets-path` fa riferimento a un percorso in un esperimento. In questo caso, le informazioni sull'esperimento e sull'esecuzione vengono caricate dal file di `runoutput.json` creato dal comando di training. Il `-t registeredmodel.json` crea un file JSON che fa riferimento al nuovo modello registrato creato da questo comando e viene usato da altri comandi dell'interfaccia della riga di comando che funzionano con i modelli registrati.

L'output di questo comando è simile al codice JSON seguente:

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

Prendere nota del numero di versione restituito per il modello. La versione viene incrementata ogni volta che si registra un nuovo modello con questo nome. Ad esempio, è possibile scaricare il modello e registrarlo da un file locale usando i comandi seguenti:

```azurecli-interactive
az ml model download -i "mymodel:1" -t .
az ml model register -n mymodel -p "sklearn_mnist_model.pkl"
```

Il primo comando Scarica il modello registrato nella directory corrente. Il nome del file è `sklearn_mnist_model.pkl`, ovvero il file a cui si fa riferimento durante la registrazione del modello. Il secondo comando registra il modello locale (`-p "sklearn_mnist_model.pkl"`) con lo stesso nome della registrazione precedente (`mymodel`). Questa volta, i dati JSON restituiti elencano la versione come 2.

## <a name="deploy-the-model"></a>Distribuire il modello

Per distribuire un modello, utilizzare il comando seguente:

```azurecli-interactive
az ml model deploy -n myservice -m "mymodel:1" --ic inferenceConfig.yml --dc aciDeploymentConfig.yml
```

> [!NOTE]
> È possibile che venga visualizzato un avviso che indica che non è stato possibile controllare l'esistenza di LocalWebservice. È possibile ignorare questo problema, poiché non si distribuisce un servizio Web locale.

Questo comando distribuisce un nuovo servizio denominato `myservice`, usando la versione 1 del modello registrato in precedenza.

Nel file `inferenceConfig.yml` vengono fornite informazioni su come utilizzare il modello per l'inferenza. Ad esempio, fa riferimento alla voce script (`score.py`) e alle dipendenze software. 

Per ulteriori informazioni sulla struttura di questo file, vedere lo [schema di configurazione dell'inferenza](reference-azure-machine-learning-cli.md#inference-configuration-schema). Per ulteriori informazioni sugli script di immissione, vedere [distribuire modelli con il Azure Machine Learning](how-to-deploy-and-where.md#prepare-deployment-artifacts).

Nella `aciDeploymentConfig.yml` viene descritto l'ambiente di distribuzione utilizzato per ospitare il servizio. La configurazione della distribuzione è specifica del tipo di calcolo usato per la distribuzione. In questo caso, viene usata un'istanza di contenitore di Azure. Per ulteriori informazioni, vedere lo [schema di configurazione della distribuzione](reference-azure-machine-learning-cli.md#deployment-configuration-schema).

Sono necessari alcuni minuti prima che il processo di distribuzione venga completato.

> [!TIP]
> In questo esempio vengono usate le istanze di contenitore di Azure. Le distribuzioni in ACI creano automaticamente la risorsa ACI necessaria. Se invece si esegue la distribuzione nel servizio Azure Kubernetes, è necessario creare in anticipo un cluster AKS e specificarlo come parte del comando `az ml model deploy`. Per un esempio di distribuzione in AKS, vedere [distribuire un modello in un cluster del servizio Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md).

Dopo alcuni minuti, vengono restituite informazioni simili al codice JSON seguente:

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

### <a name="the-scoring-uri"></a>URI di assegnazione dei punteggi

Il `scoringUri` restituito dalla distribuzione è l'endpoint REST per un modello distribuito come servizio Web. È anche possibile ottenere questo URI usando il comando seguente:

```azurecli-interactive
az ml service show -n myservice
```

Questo comando restituisce lo stesso documento JSON, inclusa la `scoringUri`.

L'endpoint REST può essere utilizzato per inviare dati al servizio. Per informazioni sulla creazione di un'applicazione client che invia dati al servizio, vedere [utilizzare un modello di Azure machine learning distribuito come servizio Web](how-to-consume-web-service.md)

### <a name="send-data-to-the-service"></a>Inviare dati al servizio

Sebbene sia possibile creare un'applicazione client per chiamare l'endpoint, l'interfaccia della riga di comando di Machine Learning fornisce un'utilità che può fungere da client di prova. Usare il comando seguente per inviare i dati nel file di `testdata.json` al servizio:

```azurecli-interactive
az ml service run -n myservice -d @testdata.json
```

> [!TIP]
> Se si usa PowerShell, usare invece il comando seguente:
>
> ```powershell
> az ml service run -n myservice -d `@testdata.json
> ```

La risposta dal comando è simile a `[ 3 ]`.

## <a name="clean-up-resources"></a>Pulire le risorse

> [!IMPORTANT]
> Le risorse create possono essere usate come prerequisiti per altre esercitazioni e procedure dettagliate per Azure Machine Learning.

### <a name="delete-deployed-service"></a>Elimina servizio distribuito

Se si prevede di continuare a utilizzare l'area di lavoro Azure Machine Learning, ma si desidera eliminare il servizio distribuito per ridurre i costi, utilizzare il comando seguente:

```azurecli-interactive
az ml service delete -n myservice
```

Questo comando restituisce un documento JSON che contiene il nome del servizio eliminato. Potrebbero essere necessari alcuni minuti prima che il servizio venga eliminato.

### <a name="delete-the-training-compute"></a>Eliminare il calcolo del training

Se si prevede di continuare a usare l'area di lavoro Azure Machine Learning, ma si vuole eliminare la destinazione di calcolo `cpu-compute` creata per il training, usare il comando seguente:

```azurecli-interactive
az ml computetarget delete -n cpu
```

Questo comando restituisce un documento JSON che contiene l'ID della destinazione di calcolo eliminata. Potrebbero essere necessari alcuni minuti prima che la destinazione di calcolo sia stata eliminata.

### <a name="delete-everything"></a>Eliminare tutto

Se non si prevede di usare le risorse create, eliminarle in modo da non incorrere in costi aggiuntivi.

Per eliminare il gruppo di risorse e tutte le risorse di Azure create in questo documento, usare il comando seguente. Sostituire `<resource-group-name>` con il nome del gruppo di risorse creato in precedenza:

```azurecli-interactive
az group delete -g <resource-group-name> -y
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione Azure Machine Learning è stata usata l'interfaccia della riga di comando di machine learning per le attività seguenti:

> [!div class="checklist"]
> * Installare l'estensione di Machine Learning
> * Creare un'area di lavoro di Machine Learning di Azure
> * Creare la risorsa di calcolo utilizzata per eseguire il training del modello
> * Definire e registrare il set di dati usato per il training del modello
> * Avviare un'esecuzione di training
> * Registrare e scaricare un modello
> * Distribuire il modello come servizio Web
> * Assegnare punteggi ai dati tramite il servizio Web

Per altre informazioni sull'uso dell'interfaccia della riga di comando, vedere [usare l'estensione CLI per Azure Machine Learning](reference-azure-machine-learning-cli.md).
