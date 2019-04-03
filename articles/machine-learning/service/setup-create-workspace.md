---
title: Creare un'area di lavoro
titleSuffix: Azure Machine Learning service
description: Usare il portale di Azure, SDK, un modello o l'interfaccia della riga di comando per creare l'area di lavoro del servizio di Azure Machine Learning. Questa area di lavoro offre una posizione centralizzata per lavorare con tutti gli elementi creati quando si usa il servizio di Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sgilley
author: sdgilley
ms.date: 03/21/2019
ms.openlocfilehash: f417aef1fd1cc48a37399ff7a157a0e658bbbb02
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58879285"
---
# <a name="create-an-azure-machine-learning-service-workspace"></a>Creare un'area di lavoro del servizio di Azure Machine Learning

Per usare il servizio di Azure Machine Learning, è necessario un [ **dell'area di lavoro del servizio Azure Machine Learning**](concept-azure-machine-learning-architecture.md#workspace).  Questa area di lavoro è la risorsa di primo livello per il servizio e fornisce una posizione centralizzata per lavorare con tutti gli elementi creati. 

In questo articolo descrive come creare un'area di lavoro usando uno di questi metodi: 
* Il [portale di Azure](#portal) interfaccia
* Il [Azure Machine Learning SDK per Python](#sdk)
* Un modello Azure Resource Manager
* Il [Azure Machine Learning CLI](#cli)

L'area di lavoro creata usando la procedura-in utilizzabile come prerequisito per altre esercitazioni e procedure dettagliate. 

Quando si crea un'area di lavoro delle risorse di Azure seguenti vengono aggiunti automaticamente (se sono disponibili a livello di area):
 
- [Registro Azure Container](https://azure.microsoft.com/services/container-registry/)
- [Archiviazione di Azure](https://azure.microsoft.com/services/storage/)
- [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)

>[!Note]
>Come con altri servizi di Azure, al servizio Machine Learning sono associati limiti e quote. [Informazioni su quote e come richiedere informazioni.](how-to-manage-quotas.md)


## <a name="prerequisites"></a>Prerequisiti
Per creare un'area di lavoro, è necessaria una sottoscrizione di Azure. Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare subito la [versione gratuita o a pagamento del servizio Azure Machine Learning](https://aka.ms/AMLFree).

## <a name="portal"></a> Portale di Azure

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

Indipendentemente dal modo in cui è stato creato, è possibile visualizzare l'area di lavoro nel [portale di Azure](https://portal.azure.com/).  Visualizzare [consente di visualizzare un'area di lavoro](how-to-manage-workspace.md#view) per informazioni dettagliate.

## <a name="sdk"></a> Python SDK

Creare area di lavoro usando il SDK di Python. Prima di tutto è necessario installare il SDK.

> [!IMPORTANT]
> Installazione del SDK può essere ignorata se si usa una macchina virtuale di analisi scientifica dei dati di Azure o Azure Databricks.
> * Python SDK è preinstallato negli ambienti Azure Data Science Virtual Machine creati dopo il 27 settembre 2018. Ignorare l'installazione e che iniziano con [creare un'area di lavoro con il SDK](#sdk-create).
> * Nell'ambiente di Azure Databricks, usare invece la [procedura di installazione di Databricks](how-to-configure-environment.md#azure-databricks).

>[!NOTE]
> Usare queste istruzioni per installare e usare il SDK dal computer locale. Per usare Jupyter in una macchina virtuale remota, configurare un server remoto o desktop X sessione terminal.

Prima di installare l'SDK, è consigliabile creare un ambiente Python isolato. Anche se in questo articolo si usa [Miniconda](https://docs.conda.io/en/latest/miniconda.html), si può usare la versione [Anaconda](https://www.anaconda.com/) completa installata o [Python virtualenv](https://virtualenv.pypa.io/en/stable/).

Le istruzioni riportate in questo articolo installerà tutti i pacchetti che necessari per eseguire i notebook di Guida introduttiva e un'esercitazione.  Per gli altri notebook di esempio, può essere necessario installare altri componenti.  Per altre informazioni su questi componenti, vedere l'articolo su come [installare Azure Machine Learning SDK per Python](https://docs.microsoft.com/python/api/overview/azure/ml/install).

### <a name="install-miniconda"></a>Installare Miniconda

[Scaricare e installare Miniconda](https://docs.conda.io/en/latest/miniconda.html). Selezionare la versione 3.7 di Python da installare. Non selezionare la versione 2.x di Python.  

### <a name="create-an-isolated-python-environment"></a>Creare un ambiente Python isolato

1. Aprire una finestra della riga di comando, creare un nuovo ambiente conda denominato *myenv* e quindi installare Python 3.6.5. Azure Machine Learning SDK funzionerà con Python 3.5.2 o versione successiva, ma i componenti automatizzati di apprendimento automatico non sono completamente operativi in Python 3.7.  Per creare l'ambiente possono essere necessari diversi minuti mentre vengono scaricati componenti e pacchetti.

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

### <a name='sdk-create'></a> Creare un'area di lavoro con il SDK

Creare l'area di lavoro in un'istanza di Jupyter Notebook con Python SDK.

1. Creare e/o passare alla directory da usare per le esercitazioni e la guida introduttiva.

1. Per avviare Jupyter Notebook, immettere questo comando:

    ```shell
    jupyter notebook
    ```

1. Nella finestra del browser creare un nuovo notebook usando il kernel `Python 3` predefinito. 

1. Per visualizzare la versione dell'SDK, immettere ed eseguire il codice Python seguente in una cella del notebook:

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=import)]

1. Trovare un valore per il parametro `<azure-subscription-id>` nell'[elenco delle sottoscrizioni nel portale di Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Usare qualsiasi sottoscrizione per la quale si ha il ruolo di proprietario o collaboratore. Per altre informazioni sui ruoli, vedere [gestire l'accesso a un'area di lavoro di Azure Machine Learning](how-to-assign-roles.md) articolo.

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

Salvare i dettagli dell'area di lavoro in un file di configurazione nella directory corrente. Questo file è denominato *aml_config/config.json*.  

Con questo file di configurazione dell'area di lavoro sarà possibile caricare più facilmente la stessa area di lavoro in un secondo momento. È possibile caricarlo con altri notebook e script in una sottodirectory con il codice o nella stessa directory `ws=Workspace.from_config()` . 

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=writeConfig)]

Questa chiamata API `write_config()` crea il file di configurazione nella directory corrente. Il file *config.json* contiene quanto segue:

```json
{
    "subscription_id": "<azure-subscription-id>",
    "resource_group": "myresourcegroup",
    "workspace_name": "myworkspace"
}
```

> [!TIP]
> Per usare l'area di lavoro di script Python o i notebook di Jupyter disponibile in altre directory, copiare questo file in tale directory. Il file può essere nella stessa directory, una sottodirectory denominata *aml_config*, o in una directory padre.

## <a name="resource-manager-template"></a>Modello di Resource manager

Per creare un'area di lavoro con un modello, vedere [creare un'area di lavoro del servizio di Azure Machine Learning usando un modello](how-to-create-workspace-template.md)

## <a name="cli"></a>CLI

Per creare un'area di lavoro con l'interfaccia della riga di comando, vedere [usare l'estensione dell'interfaccia della riga per il servizio di Azure Machine Learning](reference-azure-machine-learning-cli.md).

## <a name="clean-up-resources"></a>Pulire le risorse 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Passaggi successivi

* Indipendentemente dal modo in cui è stato creato, è possibile visualizzare l'area di lavoro nel [portale di Azure](https://portal.azure.com/).  Visualizzare [consente di visualizzare un'area di lavoro](how-to-manage-workspace.md#view) per informazioni dettagliate.

* Provare l'area di lavoro con queste guide introduttive ed esercitazioni.

    * Guida introduttiva: [Eseguire i notebook di Jupyter nel cloud](quickstart-run-cloud-notebook.md).
    * Guida introduttiva: [Eseguire i notebook di Jupyter nel proprio server](quickstart-run-local-notebook.md).
    * Esercitazione in due parti: [Train](tutorial-train-models-with-aml.md) e [distribuire](tutorial-deploy-models-with-aml.md) una modalità di classificazione di immagini.
    * Esercitazione in due parti: [Preparazione dei dati](tutorial-data-prep.md) e [usare automatizzati di machine learning](tutorial-auto-train-models.md) per compilare un modello di regressione.

* Altre informazioni su come [configurare un ambiente di sviluppo](how-to-configure-environment.md).

* Altre informazioni sul [Azure Machine Learning SDK per Python](https://aka.ms/aml-sdk).
