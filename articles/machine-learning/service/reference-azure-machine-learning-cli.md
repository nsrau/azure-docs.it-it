---
title: Informazioni sull'estensione di Azure Machine Learning CLI
description: Informazioni sull'estensione di apprendimento automatico CLI di Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: reference
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 09/24/2018
ms.openlocfilehash: 45ed1867d6d151250340bb21450b4b0d9b00e993
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51243148"
---
# <a name="what-is-the-azure-machine-learning-cli"></a>Cos'è Azure Machine Learning CLI?

L'estensione dell'interfaccia della riga di comando (CLI) di Azure Machine Learning è destinata ai data scientist e agli sviluppatori che lavorano con il servizio di Azure Machine Learning. Consente di automatizzare velocemente i flussi di lavoro di apprendimento automatico e inserirli in produzione, ad esempio:
+ Eseguire gli esperimenti per creare modelli di apprendimento automatico

+ Registrare modelli di Machine Learning per l'uso da parte dei clienti

+ Creare un pacchetto, distribuire e monitorare il ciclo di vita dei modelli di Machine Learning

La CLI di machine learning in questione è un'estensione dell'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) ed è stato compilato sulla base dell'<a href="https://aka.ms/aml-sdk" target="_blank">SDK</a> basato su Python per il servizio Azure Machine Learning.

> [!NOTE]
> L'interfaccia della riga di comando è attualmente in anteprima e verrà aggiornata.

## <a name="installing-and-uninstalling"></a>Installazione e disinstallazione

È possibile installare l'interfaccia della riga di comando usando questo comando dall'anteprima dell'indice PyPi:
```AzureCLI
az extension add -s https://azuremlsdktestpypi.blob.core.windows.net/wheels/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1/azure_cli_ml-0.1.68-py2.py3-none-any.whl --pip-extra-index-urls  https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
```

È possibile eliminare l'interfaccia della riga di comando usando questo comando:
```AzureCLI
az extension remove -n azure-cli-ml
```

È possibile aggiornare l’interfaccia della riga di comando usando i passaggi precedenti **remove** e **add**.

## <a name="using-the-cli-vs-the-sdk"></a>Usare la CLI vs. l'SDK
L'interfaccia della riga di comando è più adatta all'automazione da un utente tipo dev-ops o come parte di un'integrazione continua e di una pipeline di recapito. È ottimizzata per gestire attività non frequenti e con alti parametri. 

Tra gli esempi sono inclusi:
- calcolo provisioning
- invio di un esperimento con parametri
- registrazione del modello, creazione di immagini
- distribuzione di servizi

Ai data scientist è consigliato usare l'SDK ML di Azure.

## <a name="common-machine-learning-cli-commands"></a>Comandi comuni di apprendimento automatico CLI
> [!NOTE]
> Si trovano [qui](https://github.com/Azure/MachineLearningNotebooks/tree/cli/cli) i file di esempio che è possibile usare per eseguire correttamente i comandi di seguito.

È possibile usare la vasta gamma di comandi `az ml` per interagire con il servizio in qualsiasi ambiente da riga di comando, incluso il cloud shell del portale di Azure.

Ecco un esempio di comandi comuni:

### <a name="workspace-creation--compute-setup"></a>Creazione dell'area di lavoro e configurazione di calcolo

+ Creare un'area di lavoro del servizio Azure Machine Learning, la risorsa di primo livello per l'apprendimento automatico.
   ```AzureCLI
   az ml workspace create -n myworkspace -g myresourcegroup
   ```

+ Impostare l'interfaccia della riga di comando per usare questa area di lavoro come impostazione predefinita.
   ```AzureCLI
   az configure --defaults aml_workspace=myworkspace group=myresourcegroup
   ```

+ Creare una macchina virtuale data science. È anche possibile creare cluster BatchAI per il training distribuito o cluster AKS per la distribuzione.
  ```AzureCLI
  az ml computetarget setup dsvm -n mydsvm
  ```

### <a name="experiment-submission"></a>Invio di esperimento
+ Allegare a un progetto (eseguire la configurazione) per inviare un esperimento. Viene utilizzato per tenere traccia delle esecuzioni dell'esperimento.
  ```AzureCLI
  az ml project attach --experiment-name myhistory
  ```

+ Inviare un esperimento nel servizio di Azure Machine Learning nella destinazione di calcolo di propria scelta. Questo esempio verrà eseguito nell'ambiente di calcolo locale. Assicurarsi che il file di ambiente Conda acquisisca le proprie dipendenze python.

  ```AzureCLI
  az ml run submit -c local train.py
  ```

+ Visualizzare un elenco degli esperimenti inviati.
```AzureCLI
az ml history list
```

### <a name="model-registration-image-ceation--deployment"></a>Registrazione del modello, creazione dell'immagine e distribuzione

+ Registrazione di un modello con Azure Machine Learning.
  ```AzureCLI
  az ml model register -n mymodel -m sklearn_regression_model.pkl
  ```

+ Creare un'immagine per contenere il modello di apprendimento automatico e dipendenze. 
  ```AzureCLI
  az ml image create container -n myimage -r python -m mymodel:1 -f score.py -c myenv.yml
  ```

+ Distribuire il modello del pacchetto anche nelle destinazioni ACI e AKS.
  ```AzureCLI
  az ml service create aci -n myaciservice --image-id myimage:1
  ```
    
## <a name="full-command-list"></a>Elenco comandi completo
È possibile trovare l'elenco completo dei comandi per l'estensione del CLI (e i relativi parametri supportati) atrraverso l'esecuzione ```az ml COMMANDNAME -h```. 
