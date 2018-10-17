---
title: "Guida introduttiva: Usare Python SDK per creare un'area di lavoro del servizio Machine Learning - Azure Machine Learning"
description: Introduzione ad Azure Machine Learning.  Installare Python SDK e usarlo per creare un'area di lavoro. Questa area di lavoro rappresenta le fondamenta nel cloud per sperimentare, eseguire il training e distribuire modelli di Machine Learning con il servizio Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: quickstart
ms.reviewer: sgilley
author: hning86
ms.author: haining
ms.date: 09/24/2018
ms.openlocfilehash: ee24c1797d0f52d2529ed583a0cfe90cc9e27035
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2018
ms.locfileid: "49067761"
---
# <a name="quickstart-use-python-to-get-started-with-azure-machine-learning"></a>Guida introduttiva: Iniziare a usare Azure Machine Learning con Python

In questa guida introduttiva si userà Azure Machine Learning SDK per Python per creare e quindi usare un'[area di lavoro](concept-azure-machine-learning-architecture.md) del servizio Machine Learning. Questa area di lavoro rappresenta l'elemento fondamentale per sperimentare, eseguire il training e distribuire modelli di Machine Learning nel cloud con il servizio Azure Machine Learning.

In questa esercitazione si installerà Python SDK e si eseguiranno queste operazioni:
* Creare un'area di lavoro nella sottoscrizione di Azure
* Creare un file di configurazione per l'area di lavoro da usare successivamente in altri notebook e script
* Scrivere codice per la registrazione di valori nell'area di lavoro
* Visualizzare i valori registrati nell'area di lavoro

L'area di lavoro e il file di configurazione creati in questa guida introduttiva possono essere usati come prerequisiti per altre esercitazioni e procedure dettagliate di Azure Machine Learning. Come altri servizi di Azure, il servizio Azure Machine Learning prevede limiti e quote. [Informazioni sulle quote e come richiederne altre.](how-to-manage-quotas.md)

Per comodità, le risorse di Azure seguenti vengono aggiunte automaticamente all'area di lavoro quando sono disponibili a livello di area: [registro contenitori](https://azure.microsoft.com/services/container-registry/), [risorsa di archiviazione](https://azure.microsoft.com/services/storage/), [Application Insights](https://azure.microsoft.com/services/application-insights/) e [insieme di credenziali delle chiavi](https://azure.microsoft.com/services/key-vault/).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.


##  <a name="install-the-sdk"></a>Installare l'SDK

**Ignorare questa sezione se si usa** una Data Science Virtual Machine (DSVM) creata dopo il 27 settembre 2018, perché Python SDK è già installato in queste DSVM.

Prima di installare l'SDK, è consigliabile creare un ambiente Python isolato. In questa guida introduttiva si usa [Miniconda](https://conda.io/docs/user-guide/install/index.html), ma si può anche usare la versione [Anaconda](https://www.anaconda.com/) completa installata o [Python virtualenv](https://virtualenv.pypa.io/en/stable/).

### <a name="install-miniconda"></a>Installare Miniconda


[Scaricare](https://conda.io/miniconda.html) e installare Miniconda. Scegliere la versione 3.7 o successiva di Python. Non scegliere la versione 2.x di Python.

### <a name="create-an-isolated-python-environment"></a>Creare un ambiente Python isolato 

Avviare una finestra della riga di comando e creare un nuovo ambiente conda denominato `myenv` con Python 3.6.

```sh
conda create -n myenv -y Python=3.6
```

Attivare l'ambiente.

  ```sh
  conda activate myenv
  ```

### <a name="install-the-sdk"></a>Installare l'SDK

Installare l'SDK nell'ambiente conda attivato. Questo codice installa i componenti principali di Azure Machine Learning SDK e un server Jupyter Notebook nell'ambiente `myenv` conda.  L'installazione richiede **circa 4 minuti**.

```sh
pip install azureml-sdk[notebooks]
```

## <a name="create-a-workspace"></a>Creare un'area di lavoro

Avviare Jupyter Notebook digitando questo comando.
```sh
jupyter notebook
```

Nella finestra del browser, creare un nuovo notebook usando il kernel `Python 3` predefinito. 

Visualizzare la versione SDK digitando il codice Python seguente in una cella del notebook ed eseguirla.

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

L'esecuzione del codice precedente potrebbe attivare una nuova finestra del browser per l'accesso all'account Azure. Dopo aver effettuato l'accesso, il token di autenticazione verrà memorizzato nella cache locale.

Per visualizzare i dettagli dell'area di lavoro, inclusi l'archiviazione, il registro contenitori e l'insieme di credenziali delle chiavi associati, digitare:

```python
ws.get_details()
```

## <a name="write-a-configuration-file"></a>Scrivere un file di configurazione

Salvare i dettagli dell'area di lavoro in un file di configurazione nella directory corrente. Questo file è denominato 'aml_config\config.json'.  

Questo file di configurazione dell'area di lavoro semplifica il successivo caricamento di questa stessa area di lavoro con altri notebook e script nella stessa directory o in una sottodirectory. 

```python
# Create the configuration file.
ws.write_config()

# Use this code to load the workspace from 
# other scripts and notebooks in this directory.
# ws = Workspace.from_config()
```

La chiamata API `write_config()` crea il file di configurazione nella directory corrente. Il file `config.json` contiene quanto segue:

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

# create a new experiemnt
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
>Le risorse create possono essere usate come prerequisiti per altre esercitazioni e procedure dettagliate per Azure Machine Learning.

Se non si intende usarle, eliminare le risorse create con questa guida introduttiva per evitare addebiti.

```python
ws.delete(delete_dependent_resources=True)
```

## <a name="next-steps"></a>Passaggi successivi

Sono state create le risorse necessarie per iniziare a sperimentare e distribuire modelli. È stato anche eseguito codice in un notebook ed è stata esaminata la cronologia di esecuzione di quel codice nell'area di lavoro nel cloud.

Saranno necessari alcuni altri pacchetti nell'ambiente per l'uso con le esercitazioni di Azure Machine Learning:

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
