---
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: include
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: 38b346b4296ea72f964717220adf762e8d0d6d91
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542790"
---
## <a name="prerequisites"></a>Prerequisiti

- Un'area di lavoro di Azure Machine Learning. Per altre informazioni, vedere [creare un'area di lavoro Azure Machine Learning](../articles/machine-learning/how-to-manage-workspace.md).
- Un modello. Se non si dispone di un modello sottoposto a training, è possibile utilizzare i file di modello e di dipendenza forniti in [questa esercitazione](https://aka.ms/azml-deploy-cloud).
- [Estensione dell'interfaccia della riga di comando di Azure (CLI) per il servizio Machine Learning](../articles/machine-learning/reference-azure-machine-learning-cli.md)


## <a name="connect-to-your-workspace"></a>Connettersi all'area di lavoro

Seguire le istruzioni nella documentazione dell'interfaccia della riga di comando di Azure per [impostare il contesto della sottoscrizione](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription).

Eseguire quindi le operazioni seguenti:

```azurecli-interactive
az ml workspace list --resource-group=<my resource group>
```

per visualizzare le aree di lavoro a cui si ha accesso.

## <a name="register-your-model"></a><a id="registermodel"></a>Registrare il modello

Un modello registrato è un contenitore logico per uno o più file che costituiscono il modello. Se, ad esempio, si dispone di un modello archiviato in più file, è possibile registrarli come singolo modello nell'area di lavoro. Dopo aver registrato i file, è possibile scaricare o distribuire il modello registrato e ricevere tutti i file registrati.

> [!TIP]
> Quando si registra un modello, si specifica il percorso di una posizione cloud (da un'esecuzione di training) o di una directory locale. Questo percorso consente di individuare solo i file da caricare come parte del processo di registrazione. Non è necessario che corrisponda al percorso utilizzato nello script di immissione. Per altre informazioni, vedere [individuare i file di modello nello script di immissione](../articles/machine-learning/how-to-deploy-advanced-entry-script.md#load-registered-models).

I modelli di apprendimento automatico vengono registrati nell'area di lavoro Azure Machine Learning. Il modello può provenire da Azure Machine Learning o da un'altra posizione. Quando si registra un modello, è possibile specificare facoltativamente i metadati relativi al modello. I `tags` `properties` dizionari e applicati a una registrazione del modello possono quindi essere utilizzati per filtrare i modelli.

Negli esempi seguenti viene illustrato come registrare un modello.

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Registrare un modello da un'esecuzione di training di Azure ML

```azurecli-interactive
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid --tag area=mnist
```

[!INCLUDE [install extension](machine-learning-service-install-extension.md)]

Il `--asset-path` parametro fa riferimento alla posizione cloud del modello. In questo esempio viene usato il percorso di un singolo file. Per includere più file nella registrazione del modello, impostare sul `--asset-path` percorso di una cartella che contiene i file.

### <a name="register-a-model-from-a-local-file"></a>Registrare un modello da un file locale

```azurecli-interactive
az ml model register -n onnx_mnist -p mnist/model.onnx
```

Per includere più file nella registrazione del modello, impostare sul `-p` percorso di una cartella che contiene i file.

Per ulteriori informazioni su `az ml model register` , consultare la [documentazione di riferimento](/cli/azure/ext/azure-cli-ml/ml/model).

## <a name="define-an-entry-script"></a>Definire uno script di immissione

[!INCLUDE [write entry script](machine-learning-entry-script.md)]

## <a name="define-an-inference-configuration"></a>Definire una configurazione di inferenza

[!INCLUDE [inference config](machine-learning-service-inference-config.md)]

Il comando seguente illustra come distribuire un modello usando l'interfaccia della riga di comando:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

In questo esempio, la configurazione specifica le impostazioni seguenti:

* Che il modello richieda Python
* [Script di immissione](#define-an-entry-script), usato per gestire le richieste Web inviate al servizio distribuito
* File conda che descrive i pacchetti Python necessari per l'inferenza

Per informazioni sull'uso di un'immagine Docker personalizzata con una configurazione di inferenza, vedere [come distribuire un modello usando un'immagine Docker personalizzata](../articles/machine-learning/how-to-deploy-custom-docker-image.md).

## <a name="choose-a-compute-target"></a>Scegliere una destinazione di calcolo

[!INCLUDE [aml-compute-target-deploy](aml-compute-target-deploy.md)]

## <a name="define-a-deployment-configuration"></a>Definire una configurazione di distribuzione

Le opzioni disponibili per una configurazione di distribuzione variano a seconda della destinazione di calcolo scelta.

[!INCLUDE [aml-local-deploy-config](machine-learning-service-local-deploy-config.md)]

Per ulteriori informazioni, vedere la documentazione [AZ ml Model deploy](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-deploy) .

## <a name="deploy-your-model"></a>Distribuire il modello

A questo punto è possibile distribuire il modello.

### <a name="using-a-registered-model"></a>Uso di un modello registrato

Se il modello è stato registrato nell'area di lavoro Azure Machine Learning, sostituire "modello: 1" con il nome del modello e il relativo numero di versione.

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

### <a name="using-a-local-model"></a>Uso di un modello locale

Se si preferisce non registrare il modello, è possibile passare il parametro "sourceDirectory" nella inferenceconfig.jssu per specificare una directory locale da cui distribuire il modello.

```azurecli-interactive
az ml model deploy --ic inferenceconfig.json --dc deploymentconfig.json
```
## <a name="delete-resources"></a>Eliminare le risorse

Per eliminare un WebService distribuito, usare `az ml service <name of webservice>` .

Per eliminare un modello registrato dall'area di lavoro, usare`az ml model delete <model id>`

Altre informazioni sull' [eliminazione di un](/cli/azure/ext/azure-cli-ml/ml/service#ext-azure-cli-ml-az-ml-service-delete) servizio Web e sull' [eliminazione di un modello](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-delete)