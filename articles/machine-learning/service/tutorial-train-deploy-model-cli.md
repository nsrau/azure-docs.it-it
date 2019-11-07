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
ms.date: 09/12/2019
ms.openlocfilehash: 1854599956755716955a6e691c3266ac54ddafd9
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73581562"
---
# <a name="tutorial-train-and-deploy-a-model-from-the-cli"></a>Esercitazione: eseguire il training e distribuire un modello dall'interfaccia della riga di comando
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

In questa esercitazione si userà l'estensione di machine learning per l'interfaccia della riga di comando di Azure per eseguire il training, la registrazione e la distribuzione di un modello.

Gli script di training Python in questa esercitazione usano [Scikit-learn](https://scikit-learn.org/) per eseguire il training di un modello di base. L'obiettivo di questa esercitazione non è quello degli script o del modello, ma il processo di utilizzo dell'interfaccia della riga di comando per lavorare con Azure Machine Learning.

Si apprenderà a eseguire le operazioni seguenti:

> [!div class="checklist"]
> * Installare l'estensione di Machine Learning
> * Creare un'area di lavoro di Machine Learning di Azure
> * Creare la risorsa di calcolo utilizzata per eseguire il training del modello
> * Avviare un'esecuzione di training
> * Registrare e scaricare un modello
> * Distribuire il modello come servizio Web
> * Assegnare punteggi ai dati tramite il servizio Web

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si dispone di una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

* Per usare i comandi dell'interfaccia della riga di comando in questo documento dall' **ambiente locale**, è necessaria l'interfaccia della riga di comando di [Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

    Se si usa il [Azure cloud Shell](https://azure.microsoft.com//features/cloud-shell/), l'interfaccia della riga di comando è accessibile tramite il browser e vive nel cloud.

## <a name="download-the-example-project"></a>Scaricare il progetto di esempio

Per questa esercitazione, scaricare il progetto [https://github.com/microsoft/MLOps](https://github.com/microsoft/MLOps) . I file nelle directory `model-training` e `model-deployment` vengono usati nei passaggi di questa esercitazione.

Per ottenere una copia locale dei file, [scaricare un archivio zip](https://github.com/microsoft/MLOps/archive/master.zip)oppure usare il comando git seguente per clonare il repository:

```azurecli-interactive
git clone https://github.com/microsoft/MLOps.git
```

### <a name="training-files"></a>File di training

La directory `model-training` contiene i file seguenti, che vengono utilizzati per il training di un modello:

* `.azureml\sklearn.runconfig`: un file di __configurazione di esecuzione__ . Questo file definisce l'ambiente di runtime necessario per eseguire il training del modello.
* `train-sklearn.py`: lo script di training. Questo file addestra il modello.
* `mylib.py`: un modulo helper, usato da `train-sklearn.py`.
* `training-env.yml`: definisce le dipendenze software necessarie per eseguire lo script di training.

Lo script di training usa il set di dati del diabete fornito con Scikit-learn per eseguire il training di un modello.

### <a name="deployment-files"></a>File di distribuzione

La directory `model-deployment` contiene i file seguenti, che vengono usati per distribuire il modello sottoposto a training come servizio Web:

* `aciDeploymentConfig.yml`: un file di __configurazione della distribuzione__ . Questo file definisce l'ambiente di hosting necessario per il modello.
* `inferenceConfig.yml`: un file configuration__ di inferenza. Questo file definisce l'ambiente software utilizzato dal servizio per assegnare un punteggio ai dati con il modello.
* `score.py`: uno script Python che accetta i dati in ingresso, li assegna un punteggio utilizzando il modello e quindi restituisce una risposta.
* `scoring-env.yml`: dipendenze conda necessarie per eseguire il modello e `score.py` script.

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

Da un terminale o da un prompt dei comandi usare i comandi seguenti per passare alla directory `mlops`, quindi connettersi all'area di lavoro:

```azurecli-interactive
cd ~/mlops
az ml folder attach -w <workspace-name> -g <resource-group-name>
```

L'output di questo comando è simile al codice JSON seguente:

```json
{
  "Experiment name": "model-training",
  "Project path": "/Code/MLOps/model-training",
  "Resource group": "<resource-group-name>",
  "Subscription id": "<subscription-id>",
  "Workspace name": "<workspace-name>"
}
```

Questo comando crea un file di `.azureml/config.json`, che contiene le informazioni necessarie per connettersi all'area di lavoro. Il resto dei comandi `az ml` usati in questa esercitazione utilizzerà questo file, pertanto non è necessario aggiungere l'area di lavoro e il gruppo di risorse a tutti i comandi.

## <a name="create-the-compute-target-for-training"></a>Creare la destinazione di calcolo per il training

Questo esempio usa una macchina virtuale Azure Machine Learning notebook per eseguire il training del modello. Per creare una nuova macchina virtuale notebook, usare il comando seguente:

```azurecli-interactive
az ml computetarget create amlcompute -n cpu --max-nodes 4 --vm-size Standard_D2_V2
```

L'output di questo comando è simile al codice JSON seguente:

```json
{
  "location": "<location>",
  "name": "cpu",
  "provisioningErrors": null,
  "provisioningState": "Succeeded"
}
```

Questo comando crea una nuova destinazione di calcolo denominata `cpu`, con un massimo di quattro nodi. Le dimensioni della macchina virtuale selezionate forniscono una macchina virtuale con una risorsa GPU. Per informazioni sulle dimensioni della macchina virtuale, vedere [tipi di VM e dimensioni].

> [!IMPORTANT]
> Il nome della destinazione di calcolo (in questo caso`cpu`) è importante; viene fatto riferimento al file `.azureml/sklearn.runconfig` usato nella sezione successiva.

## <a name="submit-the-training-run"></a>Invia l'esecuzione del training

Per avviare un'esecuzione di training nella destinazione di calcolo `cpu`, passare alla directory `model-training` e quindi usare il comando seguente:

```azurecli-interactive
cd ~/mlops/model-training
az ml run submit-script -e myexperiment -c sklearn -d training-env.yml -t runoutput.json train-sklearn.py
```

Questo comando specifica un nome per l'esperimento (`myexperiment`). L'esperimento archivia le informazioni su questa esecuzione nell'area di lavoro.

Il parametro `-c sklearn` specifica il file di `.azureml/sklearn.runconfig`. Come indicato in precedenza, questo file contiene le informazioni usate per configurare l'ambiente usato dall'esecuzione del training. Se si esamina questo file, si noterà che fa riferimento alla destinazione di calcolo `cpu` creata in precedenza. Elenca inoltre il numero di nodi da usare per il training (`"nodeCount": "4"`) e contiene una sezione di `"condaDependenciees"` in cui sono elencati i pacchetti Python necessari per eseguire lo script di training.

Per altre informazioni sui file di configurazione di esecuzione, vedere [configurare e usare le destinazioni di calcolo per il training del modello](how-to-set-up-training-targets.md#create-run-configuration-and-submit-run-using-azure-machine-learning-cli)oppure fare riferimento a questo [file JSON](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json) per visualizzare lo schema completo per un runconfig.

Il parametro `-t` archivia un riferimento a questa esecuzione in un file JSON e verrà usato nei passaggi successivi per la registrazione e il download del modello.

Durante il processo di esecuzione del training, trasmette le informazioni dalla sessione di training sulla risorsa di calcolo remota. Una parte delle informazioni è simile al testo seguente:

```text
alpha is 0.80, and mse is 3340.02
alpha is 0.85, and mse is 3349.36
alpha is 0.90, and mse is 3359.09
alpha is 0.95, and mse is 3369.13


The experiment completed successfully. Finalizing run...
Cleaning up all outstanding Run operations, waiting 300.0 seconds
```

Questo testo viene registrato dallo script di training (`train-sklearn.py`) e visualizza due metriche delle prestazioni per questo modello. In questo caso, si vuole che il modello disponga del valore alfa massimo. Le metriche delle prestazioni sono specifiche del modello che si sta formando. Altri modelli avranno metriche delle prestazioni diverse.

Se si esaminano le `train-sklearn.py`, si noterà che viene usato anche il valore alfa quando archivia i modelli sottoposti a training per il file. In questo caso, vengono trainati diversi modelli. Quello con l'alfa più alto dovrebbe essere quello migliore. Esaminando l'output precedente e il codice, il modello con un Alpha di 0,95 è stato salvato come `./outputs/ridge_0.95.pkl`

Il modello è stato salvato nella directory `./outputs` nella destinazione di calcolo in cui è stato eseguito il training. In questo caso, la macchina virtuale Azure Machine Learning notebook nel cloud di Azure. Il processo di training carica automaticamente il contenuto della directory `./outputs` dalla destinazione di calcolo in cui viene eseguito il training nell'area di lavoro di Azure Machine Learning. Viene archiviato come parte dell'esperimento (`myexperiment` in questo esempio).

## <a name="register-the-model"></a>Registrare il modello

Per registrare il modello direttamente dalla versione archiviata nell'esperimento, usare il comando seguente:

```azurecli-interactive
az ml model register -n mymodel -f runoutput.json --asset-path "outputs/ridge_0.95.pkl" -t registeredmodel.json
```

Questo comando registra il file di `outputs/ridge_0.95.pkl` creato dall'esecuzione del training come nuova registrazione del modello denominata `mymodel`. Il `--assets-path` fa riferimento a un percorso in un esperimento. In questo caso, le informazioni sull'esperimento e sull'esecuzione vengono caricate dal file di `runoutput.json` creato dal comando di training. Il `-t registeredmodel.json` crea un file JSON che fa riferimento al nuovo modello registrato creato da questo comando e viene usato da altri comandi dell'interfaccia della riga di comando che funzionano con i modelli registrati.

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
az ml model register -n mymodel -p "ridge_0.95.pkl"
```

Il primo comando Scarica il modello registrato nella directory corrente. Il nome del file è `ridge_0.95.pkl`, ovvero il file a cui si fa riferimento durante la registrazione del modello. Il secondo comando registra il modello locale (`-p "ridge_0.95.pkl"`) con lo stesso nome della registrazione precedente (`mymodel`). Questa volta, i dati JSON restituiti elencano la versione come 2.

## <a name="deploy-the-model"></a>Distribuire il modello

Per distribuire un modello, passare alla directory `model-deployment` e quindi usare il comando seguente:

```azurecli-interactive
cd ~/mlops/model-deployment
az ml model deploy -n myservice -m "mymodel:1" --ic inferenceConfig.yml --dc aciDeploymentConfig.yml
```

È possibile che venga visualizzato il messaggio "non è stato possibile creare il client Docker". È possibile ignorare questo messaggio. L'interfaccia della riga di comando può distribuire un servizio Web in un contenitore Docker locale e verificare la presenza di Docker. In questo caso, non viene usata una distribuzione locale.

Questo comando distribuisce un nuovo servizio denominato `myservice`, usando la versione 1 del modello registrato in precedenza.

Il file di `inferenceConfig.yml` fornisce informazioni su come eseguire l'inferenza, ad esempio lo script di ingresso (`score.py`) e le dipendenze software. Per ulteriori informazioni sulla struttura di questo file, vedere lo [schema di configurazione dell'inferenza](reference-azure-machine-learning-cli.md#inference-configuration-schema). Per ulteriori informazioni sugli script di immissione, vedere [distribuire modelli con il Azure Machine Learning](how-to-deploy-and-where.md#prepare-to-deploy).

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

Sebbene sia possibile creare un'applicazione client per chiamare l'endpoint, l'interfaccia della riga di comando di Machine Learning fornisce un'utilità che può fungere da client di prova. Usare il comando seguente per inviare i dati di test al servizio:

```azurecli-interactive
az ml service run -n myservice -d '{"data":[[1,2,3,4,5,6,7,8,9,10]]}'
```

La risposta dal comando è simile a `[4684.920839774082]`.

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

Se si prevede di continuare a usare l'area di lavoro Azure Machine Learning, ma si vuole eliminare la destinazione di calcolo `cpu` creata per il training, usare il comando seguente:

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
> * Avviare un'esecuzione di training
> * Registrare e scaricare un modello
> * Distribuire il modello come servizio Web
> * Assegnare punteggi ai dati tramite il servizio Web

Per altre informazioni sull'uso dell'interfaccia della riga di comando, vedere [usare l'estensione CLI per Azure Machine Learning](reference-azure-machine-learning-cli.md).
