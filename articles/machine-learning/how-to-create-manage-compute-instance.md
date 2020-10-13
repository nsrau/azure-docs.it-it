---
title: Creare e gestire un'istanza di calcolo
titleSuffix: Azure Machine Learning
description: Informazioni su come creare e gestire un'istanza di calcolo nell'area di lavoro Azure Machine Learning. Usare l'istanza di calcolo come ambiente di sviluppo o a scopo di formazione e inferenza per sviluppo/test.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: sgilley
author: sdgilley
ms.reviewer: sgilley
ms.date: 10/02/2020
ms.openlocfilehash: 3b5698c782b691dd8ae91913115db184fc83a2eb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91756620"
---
# <a name="create-and-manage-an-azure-machine-learning-compute-instance"></a>Creare e gestire un'istanza di calcolo Azure Machine Learning

Informazioni su come creare e gestire un' [istanza di calcolo](concept-compute-instance.md) nell'area di lavoro Azure Machine Learning.

Usare un'istanza di calcolo come ambiente di sviluppo completamente configurato e gestito nel cloud. Per lo sviluppo e il test, è anche possibile usare l'istanza come [destinazione di calcolo](concept-compute-target.md#train) per il training o per una destinazione di [inferenza](concept-compute-target.md#deploy).   Un'istanza di calcolo può eseguire più processi in parallelo e dispone di una coda di processi. Come ambiente di sviluppo, un'istanza di calcolo non può essere condivisa con altri utenti nell'area di lavoro.

In questo articolo vengono illustrate le operazioni seguenti:

* Creare un'istanza di ambiente di calcolo 
* Gestire (avviare, arrestare, riavviare, eliminare) un'istanza di calcolo
* Accedere alla finestra del terminale 
* Installare pacchetti R o Python
* Creare nuovi ambienti o kernel Jupyter

Le istanze di calcolo possono eseguire processi in modo sicuro in un [ambiente di rete virtuale](how-to-secure-training-vnet.md), senza richiedere alle aziende di aprire porte SSH. Il processo viene eseguito in un ambiente in contenitori e inserisce le dipendenze del modello in un contenitore docker. 

## <a name="prerequisites"></a>Prerequisiti

* Un'area di lavoro di Azure Machine Learning. Per altre informazioni, vedere [creare un'area di lavoro Azure Machine Learning](how-to-manage-workspace.md).

* Estensione dell'interfaccia della riga [di comando di Azure per il servizio Machine Learning](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)o l' [estensione di Visual Studio code Azure Machine Learning](tutorial-setup-vscode-extension.md).

## <a name="create"></a>Crea

**Tempo stimato**: circa 5 minuti.

La creazione di un'istanza di calcolo è un processo una volta per l'area di lavoro. È possibile riutilizzare questo calcolo come una workstation di sviluppo o come destinazione di calcolo per il training. È possibile avere più istanze di calcolo collegate all'area di lavoro.

I core dedicati per area per la quota della famiglia di VM e la quota di area totale, applicabile alla creazione dell'istanza di calcolo, sono unificati e condivisi con Azure Machine Learning quota del cluster di calcolo di training. Se si arresta l'istanza di calcolo, la quota non viene rilasciata per garantire che sia possibile riavviare l'istanza di calcolo. Si noti che non è possibile modificare le dimensioni della macchina virtuale dell'istanza di calcolo dopo che è stata creata.

Nell'esempio seguente viene illustrato come creare un'istanza di calcolo:

# <a name="python"></a>[Python](#tab/python)

```python
import datetime
import time

from azureml.core.compute import ComputeTarget, ComputeInstance
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your instance
# Compute instance name should be unique across the azure region
compute_name = "ci{}".format(ws._workspace_id)[:10]

# Verify that instance does not exist already
try:
    instance = ComputeInstance(workspace=ws, name=compute_name)
    print('Found existing instance, use it.')
except ComputeTargetException:
    compute_config = ComputeInstance.provisioning_configuration(
        vm_size='STANDARD_D3_V2',
        ssh_public_access=False,
        # vnet_resourcegroup_name='<my-resource-group>',
        # vnet_name='<my-vnet-name>',
        # subnet_name='default',
        # admin_user_ssh_public_key='<my-sshkey>'
    )
    instance = ComputeInstance.create(ws, compute_name, compute_config)
    instance.wait_for_completion(show_output=True)
```

Per ulteriori informazioni sulle classi, i metodi e i parametri utilizzati in questo esempio, vedere i documenti di riferimento seguenti:

* [Classe ComputeInstance](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computeinstance.computeinstance?view=azure-ml-py&preserve-view=true)
* [ComputeTarget. Create](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py&preserve-view=true#create-workspace--name--provisioning-configuration-)
* [ComputeInstance.wait_for_completion](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computeinstance(class)?view=azure-ml-py&preserve-view=true#wait-for-completion-show-output-false--is-delete-operation-false-)


# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az ml computetarget create computeinstance  -n instance -s "STANDARD_D3_V2" -v
```

Per ulteriori informazioni, vedere il riferimento [AZ ml computetarget create computeinstance](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest&preserve-view=true#ext_azure_cli_ml_az_ml_computetarget_create_computeinstance) .

# <a name="studio"></a>[Studio](#tab/azure-studio)

Nell'area di lavoro in Azure Machine Learning Studio creare una nuova istanza di calcolo dalla sezione **calcolo** o nella sezione **notebook** quando si è pronti per eseguire uno dei notebook.

Per informazioni sulla creazione di un'istanza di calcolo in studio, vedere [creare destinazioni di calcolo in Azure Machine Learning Studio](how-to-create-attach-compute-studio.md#compute-instance).

---

È anche possibile creare un'istanza di calcolo con un [modello di Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance). 

### <a name="create-on-behalf-of-preview"></a>Crea per conto di (anteprima)

In qualità di amministratore, è possibile creare un'istanza di calcolo per conto di un data scientist a cui assegnare l'istanza:
* [Azure Resource Manager modello](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance).  Per informazioni dettagliate su come trovare TenantID e ObjectID necessari in questo modello, vedere [trovare ID oggetto Identity per la configurazione dell'autenticazione](../healthcare-apis/find-identity-object-ids.md).  È anche possibile trovare questi valori nel portale Azure Active Directory.
* API REST

Per i data scientist per cui si crea l'istanza di calcolo sono necessarie le seguenti autorizzazioni di [controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md) : 
* *Microsoft. MachineLearningServices/Workspaces/Computes/Start/Action*
* *Microsoft. MachineLearningServices/Workspaces/Computes/Stop/Action*
* *Microsoft. MachineLearningServices/Workspaces/Computes/restart/Action*
* *Microsoft. MachineLearningServices/Workspaces/Computes/applicationaccess/Action*

Il data scientist può avviare, arrestare e riavviare l'istanza di calcolo. Possono usare l'istanza di calcolo per:
* Jupyter
* JupyterLab
* RStudio
* Notebook integrati

## <a name="manage"></a>Gestione

Avviare, arrestare, riavviare ed eliminare un'istanza di calcolo. Un'istanza di calcolo non viene ridimensionata automaticamente, quindi assicurarsi di arrestare la risorsa per evitare addebiti continui.

# <a name="python"></a>[Python](#tab/python)

Negli esempi seguenti il nome dell'istanza di calcolo è **instance**

* Ottenere lo stato

    ```python
    # get_status() gets the latest status of the ComputeInstance target
    instance.get_status()
    ```

* Interrompere

    ```python
    # stop() is used to stop the ComputeInstance
    # Stopping ComputeInstance will stop the billing meter and persist the state on the disk.
    # Available Quota will not be changed with this operation.
    instance.stop(wait_for_completion=True, show_output=True)
    ```

* Avvio

    ```python
    # start() is used to start the ComputeInstance if it is in stopped state
    instance.start(wait_for_completion=True, show_output=True)
    ```

* Riavvia

    ```python
    # restart() is used to restart the ComputeInstance
    instance.restart(wait_for_completion=True, show_output=True)
    ```

* Elimina

    ```python
    # delete() is used to delete the ComputeInstance target. Useful if you want to re-use the compute name 
    instance.delete(wait_for_completion=True, show_output=True)
    ```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Negli esempi seguenti il nome dell'istanza di calcolo è **instance**

* Interrompere

    ```azurecli-interactive
    az ml computetarget stop computeinstance -n instance -v
    ```

    Per ulteriori informazioni, vedere [AZ ml computetarget stop computeinstance](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-computetarget-computeinstance-stop).

* Avvio 

    ```azurecli-interactive
    az ml computetarget start computeinstance -n instance -v
    ```

    Per altre informazioni, vedere [AZ ml computetarget Start computeinstance](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-computetarget-computeinstance-start).

* Riavvia 

    ```azurecli-interactive
    az ml computetarget restart computeinstance -n instance -v
    ```

    Per altre informazioni, vedere [AZ ml computetarget restart computeinstance](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-computetarget-computeinstance-restart).

* Elimina

    ```azurecli-interactive
    az ml computetarget delete -n instance -v
    ```

    Per ulteriori informazioni, vedere [AZ ml computetarget Delete computeinstance](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-computetarget-delete).

# <a name="studio"></a>[Studio](#tab/azure-studio)

Nell'area di lavoro in Azure Machine Learning Studio selezionare **Calcolo**, quindi selezionare **Istanza di calcolo** nella parte superiore.

![Gestire un'istanza di calcolo](./media/concept-compute-instance/manage-compute-instance.png)

È possibile eseguire queste operazione:

* Creare una nuova istanza di calcolo 
* Aggiornare la scheda istanze di calcolo.
* Avviare, arrestare e riavviare un'istanza di calcolo.  Si paga per l'istanza ogni volta che viene eseguito. Arrestare l'istanza di calcolo quando non viene usata per ridurre i costi. L'arresto di un'istanza di calcolo la dealloca. e quindi riavviarla quando è necessario.
* Eliminare un'istanza di calcolo.
* Filtrare l'elenco delle istanze di calcolo in modo da visualizzare solo quelle create.

Per ogni istanza di calcolo nell'area di lavoro creata (o creata automaticamente), è possibile:

* Accedere a Jupyter, JupyterLab, RStudio nell'istanza di calcolo
* Accedere con SSH all'istanza di calcolo. L'accesso SSH è disabilitato per impostazione predefinita, ma può essere abilitato al momento della creazione dell'istanza di calcolo. L'accesso SSH avviene tramite il meccanismo di chiave pubblica/privata. Nella scheda sono disponibili i dettagli per la connessione SSH, ad esempio indirizzo IP, nome utente e numero di porta.
* Ottenere informazioni dettagliate su un'istanza di calcolo specifica, ad esempio l'indirizzo IP e l'area.

---

Il [controllo degli accessi in base al ruolo](/azure/role-based-access-control/overview) consente di controllare quali utenti nell'area di lavoro possono creare, eliminare, avviare, arrestare e riavviare un'istanza di calcolo. Tutti gli utenti con il ruolo collaboratore e proprietario dell'area di lavoro possono creare, eliminare, avviare, arrestare e riavviare le istanze di calcolo nell'area di lavoro. Tuttavia, solo l'autore di un'istanza di calcolo specifica o l'utente assegnato se è stato creato per loro conto, può accedere a Jupyter, JupyterLab e RStudio in tale istanza di calcolo. Un'istanza di calcolo è dedicata a un singolo utente che ha accesso alla radice e può eseguire il terminale in tramite Jupyter/JupyterLab/RStudio. L'istanza di calcolo disporrà di un accesso utente singolo e tutte le azioni useranno l'identità dell'utente per il RBAC e l'attribuzione delle esecuzioni dell'esperimento. L'accesso SSH viene controllato tramite il meccanismo di chiave pubblica/privata.

Queste azioni possono essere controllate da RBAC:
* *Microsoft.MachineLearningServices/workspaces/computes/read*
* *Microsoft.MachineLearningServices/workspaces/computes/write*
* *Microsoft.MachineLearningServices/workspaces/computes/delete*
* *Microsoft. MachineLearningServices/Workspaces/Computes/Start/Action*
* *Microsoft. MachineLearningServices/Workspaces/Computes/Stop/Action*
* *Microsoft. MachineLearningServices/Workspaces/Computes/restart/Action*


## <a name="access-the-terminal-window"></a>Accedere alla finestra del terminale

Aprire la finestra del terminale dell'istanza di calcolo nei modi seguenti:

* RStudio: selezionare la scheda **Terminal** (Terminale) in alto a sinistra.
* Jupyter Lab:  selezionare il riquadro **Terminal** (Terminale) nell'intestazione **Other** (Altro) nella scheda Launcher (Utilità di avvio).
* Jupyter:  selezionare **New>Terminal** (Nuovo>Terminale) in alto a destra nella scheda File.
* Eseguire SSH nel computer, se è stato abilitato l'accesso SSH quando è stata creata l'istanza di calcolo.

Usare la finestra del terminale per installare i pacchetti e creare kernel aggiuntivi.

## <a name="install-packages"></a>Installare i pacchetti

È possibile installare i pacchetti direttamente in Jupyter Notebook o RStudio:

* RStudio: usare la scheda **Packages** (Pacchetti) in basso a sinistra o la scheda **Console** in alto a sinistra.  
* Python: aggiungere il codice di installazione ed eseguire in una cella Jupyter Notebook.

In alternativa, è possibile eseguire l'installazione da una finestra del terminale. Installare i pacchetti Python nell'ambiente **python 3,6-AzureML** .  Installare i pacchetti R nell'ambiente **R**.

## <a name="add-new-kernels"></a>Aggiungere nuovi kernel

> [!WARNING]
>  Durante la personalizzazione dell'istanza di calcolo, assicurarsi di non eliminare l'ambiente **azureml_py36** conda o il kernel **Python 3,6-azureml** . Questa operazione è necessaria per la funzionalità Jupyter/JupyterLab

Per aggiungere un nuovo kernel Jupyter all'istanza di calcolo:

1. Creare un nuovo terminale da Jupyter, JupyterLab o dal riquadro notebook o SSH nell'istanza di calcolo
2. Usare la finestra del terminale per creare un nuovo ambiente.  Il codice seguente, ad esempio, crea `newenv` :

    ```shell
    conda create --name newenv
    ```

3. Attivare l'ambiente.  Ad esempio, dopo aver creato `newenv`:

    ```shell
    conda activate newenv
    ```

4. Installare il pacchetto PIP e ipykernel nel nuovo ambiente e creare un kernel per la conda ENV

    ```shell
    conda install pip
    conda install ipykernel
    python -m ipykernel install --user --name newenv --display-name "Python (newenv)"
    ```

È possibile installare uno dei [kernel Jupyter disponibili](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels).



## <a name="next-steps"></a>Passaggi successivi

* [Inviare un'esecuzione di training](how-to-set-up-training-targets.md) 
