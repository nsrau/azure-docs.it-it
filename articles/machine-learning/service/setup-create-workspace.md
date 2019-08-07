---
title: Creare un'area di lavoro
titleSuffix: Azure Machine Learning service
description: Usare il portale di Azure, l'SDK, un modello o l'interfaccia della riga di comando per creare l'area di lavoro del servizio Azure Machine Learning. Questa area di lavoro offre una posizione centralizzata per lavorare con tutti gli artefatti creati quando si usa Azure Machine Learning servizio.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sgilley
author: sdgilley
ms.date: 05/21/2019
ms.openlocfilehash: 4d689b51a53a27a0e85a52724752d959c4c2506d
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2019
ms.locfileid: "68775055"
---
# <a name="create-an-azure-machine-learning-service-workspace"></a>Creare un'area di lavoro del servizio Azure Machine Learning

Per usare Azure Machine Learning servizio, è necessaria un' [**area di lavoro del servizio Azure Machine Learning**](concept-workspace.md).  Questa area di lavoro è la risorsa di primo livello per il servizio e offre una posizione centralizzata per lavorare con tutti gli artefatti creati. 

Questo articolo illustra come creare un'area di lavoro usando uno dei metodi seguenti: 
* Interfaccia [portale di Azure](#portal)
* [SDK Azure Machine Learning per Python](#sdk)
* Modello di Azure Resource Manager
* INTERFACCIA della riga di comando [Azure Machine Learning](#cli)

L'area di lavoro creata usando la procedura descritta in può essere usata come prerequisito per altre esercitazioni e articoli sulle procedure.

Se si vuole usare uno script per configurare Machine Learning automatizzato in un ambiente Python locale, vedere l'articolo relativo ad [Azure/MachineLearningNotebooks GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) per istruzioni.  

Quando si crea un'area di lavoro, le seguenti risorse di Azure vengono aggiunte automaticamente (se sono disponibili a livello di area):
 
- [Registro Azure Container](https://azure.microsoft.com/services/container-registry/): Per ridurre al minimo i costi, il registro contenitori di contenitori viene **caricato Lazy** fino a quando non vengono create immagini di distribuzione
- [Archiviazione di Azure](https://azure.microsoft.com/services/storage/)
- [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
- [Insieme di credenziali chiave Azure](https://azure.microsoft.com/services/key-vault/)

>[!Note]
>Come con altri servizi di Azure, al servizio Machine Learning sono associati limiti e quote. [Informazioni sulle quote e come richiederne altre.](how-to-manage-quotas.md)


## <a name="prerequisites"></a>Prerequisiti
Per creare un'area di lavoro, è necessaria una sottoscrizione di Azure. Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare subito la [versione gratuita o a pagamento del servizio Azure Machine Learning](https://aka.ms/AMLFree).

## <a name="portal"></a>portale di Azure

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

Indipendentemente da come è stato creato, è possibile visualizzare l'area di lavoro nella [portale di Azure](https://portal.azure.com/).  Per informazioni dettagliate, vedere [visualizzare un'area di lavoro](how-to-manage-workspace.md#view) .

## <a name="sdk"></a>SDK Python

Creare l'area di lavoro usando Python SDK. Prima di tutto è necessario installare l'SDK.

> [!IMPORTANT]
> Ignorare l'installazione dell'SDK se si usa un Data Science Virtual Machine o un Azure Databricks di Azure.
> * Python SDK è preinstallato negli ambienti Azure Data Science Virtual Machine creati dopo il 27 settembre 2018. Ignorare l'installazione e iniziare con [creare un'area di lavoro con l'SDK](#sdk-create).
> * Nell'ambiente di Azure Databricks, usare invece la [procedura di installazione di Databricks](how-to-configure-environment.md#azure-databricks).

>[!NOTE]
> Usare queste istruzioni per installare e usare l'SDK dal computer locale. Per usare Jupyter in una macchina virtuale remota, configurare un desktop remoto o una sessione terminal X.

Prima di installare l'SDK, è consigliabile creare un ambiente Python isolato. Anche se in questo articolo si usa [Miniconda](https://docs.conda.io/en/latest/miniconda.html), si può usare la versione [Anaconda](https://www.anaconda.com/) completa installata o [Python virtualenv](https://virtualenv.pypa.io/en/stable/).

Le istruzioni riportate in questo articolo installeranno tutti i pacchetti necessari per eseguire la Guida introduttiva e i notebook dell'esercitazione.  Per gli altri notebook di esempio, può essere necessario installare altri componenti.  Per altre informazioni su questi componenti, vedere l'articolo su come [installare Azure Machine Learning SDK per Python](https://docs.microsoft.com/python/api/overview/azure/ml/install).

### <a name="install-miniconda"></a>Installare Miniconda

[Scaricare e installare Miniconda](https://docs.conda.io/en/latest/miniconda.html). Selezionare la versione 3.7 di Python da installare. Non selezionare la versione 2.x di Python.  

### <a name="create-an-isolated-python-environment"></a>Creare un ambiente Python isolato

1. Aprire prompt Anaconda, quindi creare un nuovo ambiente conda denominato *MyENV* e installare Python 3.6.5. Azure Machine Learning SDK funzionerà con Python 3.5.2 o versione successiva, ma i componenti automatizzati di apprendimento automatico non sono completamente operativi in Python 3.7.  Per creare l'ambiente possono essere necessari diversi minuti mentre vengono scaricati componenti e pacchetti. 

    ```shell
    conda create -n myenv python=3.6.5
    ```

1. Attivare l'ambiente.

    ```shell
    conda activate myenv
    ```

1. Abilitare i kernel ipython specifici dell'ambiente:

    ```shell
    conda install notebook ipykernel
    ```

    Quindi creare il kernel:

    ```shell
    ipython kernel install --user
    ```

### <a name="install-the-sdk"></a>Installare l'SDK

1. Nell'ambiente conda attivato installare i componenti di base di Machine Learning SDK con le capacità per notebook Jupyter. Il completamento dell'installazione richiede alcuni minuti a seconda della configurazione del computer.

    ```shell
    pip install --upgrade azureml-sdk[notebooks]
    ```

1. Per usare questo ambiente per le esercitazioni di Azure Machine Learning, installare questi pacchetti.

    ```shell
    conda install -y cython matplotlib pandas
    ```

1. Per usare questo ambiente per le esercitazioni di Azure Machine Learning, installare i componenti automatizzati di apprendimento automatico.

    ```shell
    pip install --upgrade azureml-sdk[automl]
    ```

> [!IMPORTANT]
> In alcuni strumenti da riga di comando può essere necessario aggiungere le virgolette, come indicato di seguito:
> *  'azureml-sdk[notebooks]'
> * 'azureml-sdk[automl]'
>

### <a name='sdk-create'></a>Creare un'area di lavoro con l'SDK

Creare l'area di lavoro in un'istanza di Jupyter Notebook con Python SDK.

1. Creare e/o passare alla directory da usare per le esercitazioni e la guida introduttiva.

1. Per avviare Jupyter Notebook, immettere questo comando:

    ```shell
    jupyter notebook
    ```

1. Nella finestra del browser creare un nuovo notebook usando il kernel `Python 3` predefinito. 

1. Per visualizzare la versione dell'SDK, immettere ed eseguire il codice Python seguente in una cella del notebook:

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=import)]

1. Trovare un valore per il parametro `<azure-subscription-id>` nell'[elenco delle sottoscrizioni nel portale di Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Usare qualsiasi sottoscrizione per la quale si ha il ruolo di proprietario o collaboratore. Per ulteriori informazioni sui ruoli, vedere l'articolo [gestire l'accesso a un Azure machine learning area di lavoro](how-to-assign-roles.md) .

   ```python
   from azureml.core import Workspace
   ws = Workspace.create(name='myworkspace',
                         subscription_id='<azure-subscription-id>', 
                         resource_group='myresourcegroup',
                         create_resource_group=True,
                         location='eastus2' 
                        )
   ```

   Quando si esegue il codice, è possibile che venga chiesto di accedere all'account di Azure. Dopo aver effettuato l'accesso, il token di autenticazione viene memorizzato nella cache locale.

1. Per visualizzare i dettagli dell'area di lavoro, ad esempio la risorsa di archiviazione, il registro contenitori e l'insieme di credenziali delle chiavi associati, immettere il codice seguente:

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=getDetails)]


### <a name="write-a-configuration-file"></a>Scrivere un file di configurazione

Salvare i dettagli dell'area di lavoro in un file di configurazione nella directory corrente. Questo file è denominato *. azureml/config. JSON*.  

Con questo file di configurazione dell'area di lavoro sarà possibile caricare più facilmente la stessa area di lavoro in un secondo momento. È possibile caricarla con altri notebook e script nella stessa directory o in una sottodirectory usando il codice `ws=Workspace.from_config()` . 

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=writeConfig)]

Questa chiamata API `write_config()` crea il file di configurazione nella directory corrente. Il file *. azureml/config. JSON* contiene quanto segue:

```json
{
    "subscription_id": "<azure-subscription-id>",
    "resource_group": "myresourcegroup",
    "workspace_name": "myworkspace"
}
```

> [!TIP]
> Per usare l'area di lavoro in script Python o Jupyter notebook che si trovano in altre directory, copiare questo file in tale directory. Il file può trovarsi nella stessa directory, in una sottodirectory denominata *. azureml*o in una directory padre.

## <a name="resource-manager-template"></a>Modello di Resource Manager

Per creare un'area di lavoro con un modello, vedere [creare un'area di lavoro del servizio Azure Machine Learning usando un modello](how-to-create-workspace-template.md)

<a name="cli"></a>
## <a name="command-line-interface"></a>Interfaccia della riga di comando

Per creare un'area di lavoro con l'interfaccia della riga di comando, vedere [usare l'estensione CLI per il servizio Azure Machine Learning](reference-azure-machine-learning-cli.md).

## <a name="clean-up-resources"></a>Pulire le risorse 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Passaggi successivi

* Indipendentemente da come è stato creato, è possibile visualizzare l'area di lavoro nella [portale di Azure](https://portal.azure.com/).  Per informazioni dettagliate, vedere [visualizzare un'area di lavoro](how-to-manage-workspace.md#view) .

* Provare l'area di lavoro con queste esercitazioni.

    * Esercitazione in due parti: [Configurare l'ambiente e l'area di lavoro](tutorial-1st-experiment-sdk-setup.md) ed eseguire [il training del primo modello](tutorial-1st-experiment-sdk-train.md).
    * Esercitazione in due parti: Eseguire il [Training](tutorial-train-models-with-aml.md) e [distribuire](tutorial-deploy-models-with-aml.md) una modalità di classificazione immagine.
    * Esercitazione in due parti: [Preparare i dati](tutorial-data-prep.md) e [usare Machine Learning automatizzato](tutorial-auto-train-models.md) per creare un modello di regressione.

* Altre informazioni su come [configurare un ambiente di sviluppo](how-to-configure-environment.md).

* Scopri di più su [Azure Machine Learning SDK per Python](https://aka.ms/aml-sdk).
