---
title: 'Esercitazione: Introduzione a Machine Learning - Python'
titleSuffix: Azure Machine Learning
description: Questa esercitazione illustra come iniziare a usare Azure Machine Learning SDK per Python in esecuzione nell'ambiente di sviluppo personale.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: 2e01721b4b414455b47a394087192696e1ecb025
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92892735"
---
# <a name="tutorial-get-started-with-azure-machine-learning-in-your-development-environment-part-1-of-4"></a>Esercitazione: Introduzione ad Azure Machine Learning nel proprio ambiente di sviluppo (parte 1 di 4)

In questa *serie di quattro esercitazioni* verranno illustrati i concetti fondamentali su Azure Machine Learning e verranno completate attività di Machine Learning per Python basate su processi nella piattaforma cloud di Azure. 

Nella parte 1 di questa serie di esercitazioni si apprenderà come:

> [!div class="checklist"]
> * Installare Azure Machine Learning SDK.
> * Configurare la struttura della directory per il codice.
> * Creare un'area di lavoro di Azure Machine Learning.
> * Configurare l'ambiente di sviluppo locale.
> * Configurare un cluster di elaborazione.

> [!NOTE]
> Questa serie di esercitazioni illustra i concetti relativi ad Azure Machine Learning più inerenti alle attività di Machine Learning *basate su processi* di Python a elevato utilizzo di calcolo e/o che richiedono riproducibilità. Se si è più interessati a un flusso di lavoro esplorativo, usare invece [Jupyter o RStudio in un'istanza di ambiente di calcolo di Azure Machine Learning](tutorial-1st-experiment-sdk-setup.md).

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare [Azure Machine Learning](https://aka.ms/AMLFree).
- Familiarità con i [concetti di Machine Learning](concept-azure-machine-learning-architecture.md) e di Python. Gli esempi includono ambienti, training e assegnazione di punteggi.
- Ambiente di sviluppo locale, come Visual Studio Code, Jupyter o PyCharm.
- Python (versione 3.5-3.7).


## <a name="install-the-azure-machine-learning-sdk"></a>Installare Azure Machine Learning SDK

In questa esercitazione viene usato Azure Machine Learning SDK per Python.

Per configurare un ambiente Python da usare nell'intera esercitazione, è possibile usare gli strumenti con cui si ha più familiarità, ad esempio Conda e pip. Installare Azure Machine Learning SDK per Python nell'ambiente Python tramite pip:

```bash
pip install azureml-sdk
```

## <a name="create-a-directory-structure-for-code"></a>Creare una struttura della directory per il codice
Per questa esercitazione è consigliabile configurare la semplice struttura della directory corrente:

```markdown
tutorial
└──.azureml
```

- `tutorial`: directory di primo livello del progetto.
- `.azureml`: sottodirectory nascosta per l'archiviazione dei file di configurazione di Azure Machine Learning.

## <a name="create-an-azure-machine-learning-workspace"></a>Creare un'area di lavoro di Machine Learning di Azure

Un'area di lavoro è una risorsa di primo livello per Azure Machine Learning e costituisce una posizione centralizzata per:

- Gestire le risorse, ad esempio quelle di calcolo.
- Archiviare asset come notebook, ambienti, set di dati, pipeline, modelli ed endpoint.
- Collaborare con altri membri del team.

Nella directory di primo livello `tutorial` aggiungere un nuovo file di Python denominato `01-create-workspace.py` usando il codice seguente. Adattare i parametri (nome, ID sottoscrizione, gruppo di risorse e [località](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)) in base alle preferenze.

È possibile eseguire il codice in una sessione interattiva o come file di Python.

>[!NOTE]
> Se si usa un ambiente di sviluppo locale (ad esempio il proprio computer), la prima volta che si esegue il codice seguente viene chiesto di eseguire l'autenticazione con l'area di lavoro usando un *codice del dispositivo*. Segui le istruzioni visualizzate.

```python
# tutorial/01-create-workspace.py
from azureml.core import Workspace

ws = Workspace.create(name='<my_workspace_name>', # provide a name for your workspace
                      subscription_id='<azure-subscription-id>', # provide your subscription ID
                      resource_group='<myresourcegroup>', # provide a resource group name
                      create_resource_group=True,
                      location='<NAME_OF_REGION>') # For example: 'westeurope' or 'eastus2' or 'westus2' or 'southeastasia'.

# write out the workspace details to a configuration file: .azureml/config.json
ws.write_config(path='.azureml')
```

Eseguire questo codice dalla directory `tutorial`:

```bash
cd <path/to/tutorial>
python ./01-create-workspace.py
```

> [!TIP]
> Se l'esecuzione di questo codice genera un messaggio di errore che indica che non si ha accesso alla sottoscrizione, vedere [Creare un'area di lavoro](how-to-manage-workspace.md?tab=python#create-multi-tenant) per informazioni sulle opzioni di autenticazione.


Dopo la corretta esecuzione di *01-create-workspace.py* , la struttura di cartelle sarà simile alla seguente:

```markdown
tutorial
└──.azureml
|  └──config.json
└──01-create-workspace.py
```

Il file `.azureml/config.json` contiene i metadati necessari per connettersi all'area di lavoro di Azure Machine Learning. In particolare, contiene l'ID sottoscrizione, il gruppo di risorse e il nome dell'area di lavoro. 

> [!NOTE]
> Il contenuto di `config.json` non è costituito da segreti. È possibile condividere tranquillamente questi dettagli.
>
> L'autenticazione è tuttavia necessaria per interagire con l'area di lavoro Azure Machine Learning.

## <a name="create-an-azure-machine-learning-compute-cluster"></a>Creare un cluster di elaborazione di Azure Machine Learning

Creare uno script di Python denominato `02-create-compute.py` nella directory di primo livello `tutorial`. Popolarlo con il codice seguente per creare un cluster di elaborazione di Azure Machine Learning che verrà automaticamente dimensionato tra zero e quattro nodi:

```python
# tutorial/02-create-compute.py
from azureml.core import Workspace
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

ws = Workspace.from_config() # This automatically looks for a directory .azureml

# Choose a name for your CPU cluster
cpu_cluster_name = "cpu-cluster"

# Verify that the cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                           idle_seconds_before_scaledown=2400)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)
```

Eseguire il file di Python:

```bash
python ./02-create-compute.py
```


> [!NOTE]
> Una volta creato il cluster, risulteranno 0 nodi sottoposti a provisioning. Il cluster *non* comporta l'addebito di costi finché non si invia un processo. Le prestazioni di questo cluster verranno ridotte dopo che sarà rimasto inattivo per 2.400 secondi (40 minuti).

La struttura di cartelle ora sarà simile alla seguente:

```bash
tutorial
└──.azureml
|  └──config.json
└──01-create-workspace.py
└──02-create-compute.py
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state eseguite le operazioni seguenti:

- Creazione di un'area di lavoro di Azure Machine Learning
- Configurazione dell'ambiente di sviluppo locale.
- Creazione di un cluster di elaborazione di Azure Machine Learning.

Nelle altre parti dell'esercitazione si apprenderà come:

* Parte 2. Eseguire il codice nel cloud usando Azure Machine Learning SDK per Python.
* Parte 3. Gestire l'ambiente Python usato per il training del modello.
* Parte 4. Caricare i dati in Azure e utilizzarli per il training.

Proseguire con l'esercitazione successiva, che illustra come inviare uno script al cluster di elaborazione di Azure Machine Learning.

> [!div class="nextstepaction"]
> [Esercitazione: Eseguire uno script "Hello World!" di Python in Azure](tutorial-1st-experiment-hello-world.md)
