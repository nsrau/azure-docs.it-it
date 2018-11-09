---
title: "Guida introduttiva: Usare Python SDK per creare un'area di lavoro del servizio Machine Learning - Azure Machine Learning"
description: Introduzione ad Azure Machine Learning. Installare Python SDK e usarlo per creare un'area di lavoro. Questa area di lavoro rappresenta le fondamenta nel cloud per sperimentare, eseguire il training e distribuire modelli di Machine Learning con il servizio Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: quickstart
ms.reviewer: sgilley
author: hning86
ms.author: haining
ms.date: 09/24/2018
ms.openlocfilehash: e8ebfbfe1d12af892208f67e67c69f25631acb28
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2018
ms.locfileid: "50158840"
---
# <a name="quickstart-use-python-to-get-started-with-azure-machine-learning"></a>Guida introduttiva: Iniziare a usare Azure Machine Learning con Python

In questa guida introduttiva si usa Azure Machine Learning SDK per Python per creare e quindi usare un'[area di lavoro](concept-azure-machine-learning-architecture.md) del servizio Machine Learning. Questa area di lavoro rappresenta le fondamenta nel cloud per sperimentare, eseguire il training e distribuire modelli di Machine Learning con il servizio Machine Learning. In questa guida introduttiva si inizia configurando l'ambiente Python e il server notebook Jupyter. Per l'esecuzione senza installazione, vedere [Guida introduttiva: Iniziare a usare Azure Machine Learning con il portale di Azure](quickstart-get-started.md).


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

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.


## <a name="install-the-sdk"></a>Installare l'SDK

*Ignorare questa sezione se si usa una Data Science Virtual Machine creata dopo il 27 settembre 2018.* In queste DSVM Python SDK è preinstallato.

Prima di installare l'SDK, è consigliabile creare un ambiente Python isolato. In questa guida introduttiva si usa [Miniconda](https://conda.io/docs/user-guide/install/index.html), ma si può anche usare la versione [Anaconda](https://www.anaconda.com/) completa installata o [Python virtualenv](https://virtualenv.pypa.io/en/stable/).

### <a name="install-miniconda"></a>Installare Miniconda


[Scaricare](https://conda.io/miniconda.html) e installare Miniconda. Scegliere la versione 3.7 o successiva di Python. Non selezionare la versione 2.x di Python.

### <a name="create-an-isolated-python-environment"></a>Creare un ambiente Python isolato 

Aprire una finestra della riga di comando. Creare quindi un nuovo ambiente conda denominato `myenv` con Python 3.6.

```sh
conda create -n myenv -y Python=3.6
```

Attivare l'ambiente.

  ```sh
  conda activate myenv
  ```

### <a name="install-the-sdk"></a>Installare l'SDK

Installare l'SDK nell'ambiente conda attivato. Questo codice installa i componenti di base di Machine Learning SDK. Installa inoltre un server Jupyter Notebook nell'ambiente `myenv` conda. L'installazione richiede **circa quattro minuti**.

```sh
pip install azureml-sdk[notebooks]
```

## <a name="create-a-workspace"></a>Creare un'area di lavoro

Per avviare Jupyter Notebook, immettere questo comando.
```sh
jupyter notebook
```

Nella finestra del browser creare un nuovo notebook usando il kernel `Python 3` predefinito. 

Per visualizzare la versione SDK, immettere il codice Python seguente in una cella del notebook ed eseguirla.

```python
import azureml.core
print(azureml.core.VERSION)
```

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

```python
ws.get_details()
```

## <a name="write-a-configuration-file"></a>Scrivere un file di configurazione

Salvare i dettagli dell'area di lavoro in un file di configurazione nella directory corrente. Questo file è denominato 'aml_config\config.json'.  

Questo file di configurazione dell'area di lavoro semplifica il successivo caricamento di questa stessa area di lavoro. Sara possibile caricarla con altri notebook e script nella stessa directory o in una sottodirectory. 

```python
# Create the configuration file.
ws.write_config()

# Use this code to load the workspace from 
# other scripts and notebooks in this directory.
# ws = Workspace.from_config()
```

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

```python
from azureml.core import Experiment

# create a new experiment
exp = Experiment(workspace=ws, name='myexp')

# start a run
run = exp.start_logging()

# log a number
run.log('my magic number', 42)

# log a list (Fibonacci numbers)
run.log_list('my list', [1, 1, 2, 3, 5, 8, 13, 21, 34, 55]) 

# finish the run
run.complete()
```

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

```python
ws.delete(delete_dependent_resources=True)
```

## <a name="next-steps"></a>Passaggi successivi

Sono state create le risorse necessarie per provare a usare e distribuire modelli. È stato anche eseguito codice in un notebook ed è stata esaminata la cronologia di esecuzione di quel codice nell'area di lavoro nel cloud.

Sono necessari alcuni altri pacchetti nell'ambiente per l'uso con le esercitazioni di Machine Learning.

1. Chiudere il notebook nel browser.
1. Nella finestra della riga di comando usare `Ctrl`+`C` per arrestare il server notebook.
1. Installare i pacchetti aggiuntivi.

    ```sh
    conda install -y cython matplotlib scikit-learn pandas numpy
    pip install azureml-sdk[automl]
    ```

Dopo aver installato questi pacchetti, seguire le esercitazioni per eseguire il training e la distribuzione di un modello. 

> [!div class="nextstepaction"]
> [Esercitazione: Eseguire il training di un modello per la classificazione di immagini](tutorial-train-models-with-aml.md)

È anche possibile esaminare [esempi più avanzati in GitHub](https://aka.ms/aml-notebooks).
