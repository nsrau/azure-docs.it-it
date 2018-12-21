---
title: 'Guida introduttiva:  Per iniziare in Python'
titleSuffix: Azure Machine Learning service
description: Introduzione al servizio Azure Machine Learning in Python. Usare l’SDK Python per creare un'area di lavoro che rappresenta le fondamenta nel cloud per sperimentare, eseguire il training e distribuire modelli di Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: quickstart
ms.reviewer: sgilley
author: hning86
ms.author: haining
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 3ab55cec4b8483cf254ec3d9fe68521baca9cdf5
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2018
ms.locfileid: "53268512"
---
# <a name="quickstart-use-python-sdk-to-get-started-with-azure-machine-learning"></a>Guida introduttiva: Utilizzare l’SDK Python per iniziare a usare Azure Machine Learning

In questa guida introduttiva si usa Azure Machine Learning SDK per Python per creare e quindi usare un'[area di lavoro](concept-azure-machine-learning-architecture.md) del servizio Machine Learning. Questa area di lavoro rappresenta le fondamenta nel cloud per sperimentare, eseguire il training e distribuire modelli di Machine Learning con il servizio Machine Learning. In questa guida introduttiva si inizia configurando l'ambiente Python e il server notebook Jupyter. Per l'esecuzione senza installazione, vedere [Avvio rapido: Iniziare a usare Azure Machine Learning con il portale di Azure](quickstart-get-started.md).

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2G9N6]

In questa esercitazione si installa Python SDK e si eseguono queste operazioni:

* Creare un'area di lavoro nella sottoscrizione di Azure.
* Creare un file di configurazione per l'area di lavoro da usare successivamente in altri notebook e script.
* Scrivere codice per la registrazione di valori nell'area di lavoro.
* Visualizzare i valori registrati nell'area di lavoro.

In questa guida introduttiva si creano un'area di lavoro e un file di configurazione. È possibile usarli come prerequisito per altre esercitazioni e procedure dettagliate di Machine Learning. Come altri servizi di Azure, Machine Learning prevede limiti e quote. [Informazioni sulle quote e come richiederne altre.](how-to-manage-quotas.md)

Le risorse di Azure seguenti vengono aggiunte automaticamente all'area di lavoro quando sono disponibili a livello di area:
 
- [Registro contenitori di Azure](https://azure.microsoft.com/services/container-registry/)
- [Archiviazione di Azure](https://azure.microsoft.com/services/storage/)
- [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
- [Insieme di credenziali chiave Azure](https://azure.microsoft.com/services/key-vault/)

Se non si dispone di una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Accedere alla [versione gratuita o a pagamento del servizio Azure Machine Learning](http://aka.ms/AMLFree).

## <a name="install-the-sdk"></a>Installare l'SDK

>[!NOTE]
> Il codice in questo articolo richiede l’SDK Azure Machine Learning versione 1.0.2 o successiva. 

*Ignorare questa sezione se si usa una Data Science Virtual Machine creata dopo il 27 settembre 2018.* In queste DSVM Python SDK è preinstallato.

Prima di installare l'SDK, è consigliabile creare un ambiente Python isolato. In questa guida introduttiva si usa [Miniconda](https://conda.io/docs/user-guide/install/index.html), ma si può anche usare la versione [Anaconda](https://www.anaconda.com/) completa installata o [Python virtualenv](https://virtualenv.pypa.io/en/stable/).

### <a name="install-miniconda"></a>Installare Miniconda


[Scaricare](https://conda.io/miniconda.html) e installare Miniconda. Scegliere la versione 3.7 o successiva di Python. Non selezionare la versione 2.x di Python.

### <a name="create-an-isolated-python-environment"></a>Creare un ambiente Python isolato 

Aprire una finestra della riga di comando. Creare quindi un nuovo ambiente conda denominato `myenv` con Python 3.6.

```shell
conda create -n myenv -y Python=3.6
```

Attivare l'ambiente.

  ```shell
  conda activate myenv
  ```

### <a name="install-the-sdk"></a>Installare l'SDK

Installare l'SDK nell'ambiente conda attivato. Questo codice installa i componenti di base di Machine Learning SDK. Installa anche un server Jupyter Notebook nell'ambiente conda. Il completamento dell'installazione richiede alcuni minuti a seconda della configurazione del computer.

```sh
# install Jupyter
conda install nb_conda

# install the base SDK and Jupyter Notebook
pip install azureml-sdk[notebooks]

```

È anche possibile usare diverse parole chiave "aggiuntive" per installare i componenti aggiuntivi dell'SDK.

```sh
# install the base SDK and auto ml components
pip install azureml-sdk[automl]

# install the base SDK and model explainability component
pip install azureml-sdk[explain]

# install the base SDK and experimental components
pip install azureml-sdk[contrib]
```

Usare invece l'installazione in un ambiente Databricks.

```
# install the base SDK and automl components in Azure Databricks environment
# read more at: https://github.com/Azure/MachineLearningNotebooks/tree/master/databricks
pip install azureml-sdk[databricks]
```


## <a name="create-a-workspace"></a>Creare un'area di lavoro

Per avviare Jupyter Notebook, immettere questo comando.
```shell
jupyter notebook
```

Nella finestra del browser creare un nuovo notebook usando il kernel `Python 3` predefinito. 

Per visualizzare la versione SDK, immettere il codice Python seguente in una cella del notebook ed eseguirla.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=import)]

Creare un nuovo gruppo di risorse di Azure e una nuova area di lavoro.

Trovare un valore per `<azure-subscription-id>` nell'[elenco delle sottoscrizioni nel portale di Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Usare qualsiasi sottoscrizione per la quale si ha il ruolo di proprietario o collaboratore.

```python
from azureml.core import Workspace
ws = Workspace.create(name='myworkspace',
                      subscription_id='<azure-subscription-id>',    
                      resource_group='myresourcegroup',
                      create_resource_group=True,
                      location='eastus2' # or other supported Azure region  
                     )
```

L'esecuzione del codice precedente potrebbe attivare una nuova finestra del browser per l'accesso all'account Azure. Dopo aver effettuato l'accesso, il token di autenticazione viene memorizzato nella cache locale.

Per visualizzare i dettagli dell'area di lavoro, ad esempio l'archiviazione, il registro contenitori e l'insieme di credenziali delle chiavi associati, immettere il codice seguente.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=getDetails)]


## <a name="write-a-configuration-file"></a>Scrivere un file di configurazione

Salvare i dettagli dell'area di lavoro in un file di configurazione nella directory corrente. Questo file è denominato 'aml_config\config.json'.  

Questo file di configurazione dell'area di lavoro semplifica il successivo caricamento di questa stessa area di lavoro. Sara possibile caricarla con altri notebook e script nella stessa directory o in una sottodirectory. 

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=writeConfig)]


La chiamata API `write_config()` crea il file di configurazione nella directory corrente. Il file `config.json` contiene lo script seguente.

```json
{
    "subscription_id": "<azure-subscription-id>",
    "resource_group": "myresourcegroup",
    "workspace_name": "myworkspace"
}
```

## <a name="use-the-workspace"></a>Usare l'area di lavoro

Scrivere codice che usi le API di base dell'SDK per tenere traccia delle esecuzioni dell'esperimento.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=useWs)]


## <a name="view-logged-results"></a>Visualizzare i risultati registrati
Al termine è possibile visualizzare l'esecuzione dell'esperimento nel portale di Azure. Usare il codice seguente per stampare un URL per i risultati dell'ultima esecuzione.

```python
print(run.get_portal_url())
```

Usare il collegamento per visualizzare i valori registrati nel portale di Azure all'interno nel browser.

![Valori registrati nel portale](./media/quickstart-create-workspace-with-python/logged-values.png)

## <a name="clean-up-resources"></a>Pulire le risorse 
>[!IMPORTANT]
>Le risorse create possono essere usate come prerequisiti per altre esercitazioni e procedure dettagliate per Machine Learning.

Se non si prevede di usare le risorse create in questa sede, è consigliabile eliminarle per non incorrere in alcun addebito.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=delete)]


## <a name="next-steps"></a>Passaggi successivi

Sono state create le risorse necessarie per provare a usare e distribuire modelli. È stato anche eseguito codice in un notebook ed è stata esaminata la cronologia di esecuzione di quel codice nell'area di lavoro nel cloud.

Sono necessari alcuni altri pacchetti nell'ambiente per l'uso con le esercitazioni di Machine Learning.

1. Chiudere il notebook nel browser.
1. Nella finestra della riga di comando usare `Ctrl`+`C` per arrestare il server notebook.
1. Installare i pacchetti aggiuntivi.

    ```shell
    conda install -y cython matplotlib scikit-learn pandas numpy
    pip install azureml-sdk[automl]
    ```


Dopo aver installato questi pacchetti, seguire le esercitazioni per eseguire il training e la distribuzione di un modello. 

> [!div class="nextstepaction"]
> [Esercitazione: Eseguire il training di un modello per la classificazione di immagini](tutorial-train-models-with-aml.md)

È anche possibile esaminare [esempi più avanzati in GitHub](https://aka.ms/aml-notebooks).
