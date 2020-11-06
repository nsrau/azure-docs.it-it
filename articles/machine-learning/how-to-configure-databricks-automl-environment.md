---
title: Sviluppare con autoML & Azure Databricks
titleSuffix: Azure Machine Learning
description: Informazioni su come configurare un ambiente di sviluppo in Azure Machine Learning e Azure Databricks. Usare gli SDK di Azure ML per databricks e databricks con autoML.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.date: 10/21/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: ef8ee7718aabb443fda6cd7b276ee53472261913
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424349"
---
# <a name="set-up-a-development-environment-with-azure-databricks-and-automl-in-azure-machine-learning"></a>Configurare un ambiente di sviluppo con Azure Databricks e autoML in Azure Machine Learning 

Informazioni su come configurare un ambiente di sviluppo in Azure Machine Learning che usa Azure Databricks e la ML automatizzata.

Azure Databricks è ideale per l'esecuzione di flussi di lavoro di apprendimento automatico su larga scala sulla piattaforma Apache Spark scalabile nel cloud di Azure. Fornisce un ambiente di collaborazione basato su notebook con un cluster di calcolo basato su CPU o GPU.

Per informazioni su altri ambienti di sviluppo di Machine Learning, vedere [configurare l'ambiente di sviluppo Python](how-to-configure-environment.md).


## <a name="prerequisite"></a>Prerequisito

Area di lavoro Azure Machine Learning. Se non si dispone di un, è possibile creare un'area di lavoro Azure Machine Learning tramite i modelli [portale di Azure](how-to-manage-workspace.md), interfaccia della riga di comando di [Azure](how-to-manage-workspace-cli.md#create-a-workspace)e [Azure Resource Manager](how-to-create-workspace-template.md).


## <a name="azure-databricks-with-azure-machine-learning-and-automl"></a>Azure Databricks con Azure Machine Learning e autoML

Azure Databricks si integra con Azure Machine Learning e con le relative funzionalità autoML. 

È possibile usare Azure Databricks:

+ Per eseguire il training di un modello usando Spark MLlib e distribuire il modello in ACI/AKS.
+ Con le funzionalità [automatiche di Machine Learning](concept-automated-ml.md) con Azure ml SDK.
+ Come destinazione di calcolo da una [pipeline Azure Machine Learning](concept-ml-pipelines.md).

## <a name="set-up-a-databricks-cluster"></a>Configurare un cluster databricks

Creare un [cluster databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal). Alcune impostazioni si applicano solo se si installa l'SDK per Machine Learning automatico in databricks.

**La creazione del cluster richiede alcuni minuti.**

Usa queste impostazioni:

| Impostazione |Si applica a| Valore |
|----|---|---|
| Cluster Name |always| nomecluster |
| Versione Databricks Runtime |always| Runtime 7,1 (scala 2,21, Spark 3.0.0)-non ML|
| Versione Python |always| 3 |
| Tipo di lavoro <br>(determina il numero massimo di iterazioni simultanee) |Funzionalità automatiche di Machine Learning<br>only| È preferibile una macchina virtuale ottimizzata per la memoria |
| Ruoli di lavoro |always| almeno 2 |
| Enable Autoscaling (Abilita la scalabilità automatica) |Funzionalità automatiche di Machine Learning<br>only| Deselezionare |

Attendere che il cluster sia in esecuzione prima di proseguire.

## <a name="add-the-azure-ml-sdk-to-databricks"></a>Aggiungere Azure ML SDK a databricks

Quando il cluster è in esecuzione, [creare una libreria](https://docs.databricks.com/user-guide/libraries.html#create-a-library) per alleghi il pacchetto SDK Azure Machine Learning appropriato al cluster. 

Per usare la funzionalità di Machine Learning automatica, passare ad [aggiungere Azure ml SDK con AutoML](#add-the-azure-ml-sdk-with-automl-to-databricks).


1. Fare clic con il pulsante destro del mouse sulla cartella dell'area di lavoro corrente in cui si desidera archiviare la libreria. Selezionare **Crea**  >  **libreria**.
    
    > [!TIP]
    > Se si dispone di una versione precedente dell'SDK, deselezionarla dalle librerie installate del cluster e spostarla nel Cestino. Installare la nuova versione dell'SDK e riavviare il cluster. Se si verifica un problema dopo il riavvio, scollegare e riconnettere il cluster.

1. Scegliere l'opzione seguente (non sono supportate altre installazioni SDK)

   |&nbsp;Funzionalità aggiuntive del pacchetto SDK &nbsp;|Source (Sorgente)|&nbsp;Nome pypi&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |Per databricks| Caricare Python Egg o PyPI | azureml-sdk[databricks]|

   > [!WARNING]
   > Non è possibile installare altri SDK aggiuntivi. Scegliere solo l' `databricks` opzione [].

   * Non selezionare **Connetti automaticamente a tutti i cluster**.
   * Selezionare  **Connetti** accanto al nome del cluster.

1. Consente di monitorare gli errori fino a quando lo stato non diventa **collegato** , operazione che può richiedere alcuni minuti.  Se questo passaggio ha esito negativo:

   Provare a riavviare il cluster per:
   1. Nel riquadro a sinistra selezionare **Cluster**.
   1. Nella tabella selezionare il nome del cluster.
   1. Nella scheda **Librerie** selezionare **Riavvia**.

   Una corretta installazione avrà un aspetto simile al seguente: 

  ![SDK di Azure Machine Learning per databricks](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg) 

## <a name="add-the-azure-ml-sdk-with-automl-to-databricks"></a>Aggiungere Azure ML SDK con AutoML a databricks
Se il cluster è stato creato con Databricks Runtime 7,1 o versione successiva ( *non* ml), eseguire il comando seguente nella prima cella del notebook per installare AML SDK.

```
%pip install --upgrade --force-reinstall -r https://aka.ms/automl_linux_requirements.txt
```
Per Databricks Runtime 7,0 e versioni precedenti, installare Azure Machine Learning SDK usando lo [script init](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks/automl/README.md).

### <a name="automl-config-settings"></a>Impostazioni di configurazione di AutoML

In AutoML config, quando si usa Azure Databricks aggiungere i parametri seguenti:

- ```max_concurrent_iterations``` si basa sul numero di nodi del ruolo di lavoro nel cluster.
- ```spark_context=sc``` si basa sul contesto Spark predefinito.

## <a name="ml-notebooks-that-work-with-azure-databricks"></a>Notebook ML che funzionano con Azure Databricks

Per provarlo:
+ Sebbene siano disponibili molti notebook di esempio, **solo [questi notebook di esempio](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) funzionano con Azure Databricks.**

+ Importare questi esempi direttamente dall'area di lavoro. Vedere di seguito: ![ selezionare Importa ](./media/how-to-configure-environment/azure-db-screenshot.png)
 ![ Pannello di importazione](./media/how-to-configure-environment/azure-db-import.png)

+ Informazioni su come [creare una pipeline con databricks come calcolo di training](how-to-create-your-first-pipeline.md).

## <a name="next-steps"></a>Passaggi successivi

- Eseguire il [training di un modello](tutorial-train-models-with-aml.md) su Azure Machine Learning con il set di dati MNIST.
- Vedere le informazioni [di riferimento su Azure Machine Learning SDK per Python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py).
