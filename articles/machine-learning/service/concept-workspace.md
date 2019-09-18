---
title: Che cos'è un'area di lavoro
titleSuffix: Azure Machine Learning
description: L'area di lavoro è la risorsa di primo livello per Azure Machine Learning. Mantiene una cronologia di tutte le esecuzioni di training, inclusi i log, le metriche, l'output e uno snapshot degli script. Queste informazioni vengono utilizzate per determinare quale esecuzione di training produce il modello migliore
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 08/06/2019
ms.openlocfilehash: 84aad0f1a797bb7eccf1872bd291e72ecefa7eee
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71035332"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>Che cos'è un'area di lavoro Azure Machine Learning?

L'area di lavoro è la risorsa di primo livello per Azure Machine Learning, che fornisce una posizione centralizzata per lavorare con tutti gli artefatti creati quando si usa Azure Machine Learning.  L'area di lavoro mantiene una cronologia di tutte le esecuzioni di training, inclusi i log, le metriche, l'output e uno snapshot degli script. Queste informazioni consentono di determinare il training che produce il modello migliore.  

Dopo aver creato un modello, è necessario registrarlo con l'area di lavoro. È quindi possibile usare il modello registrato e gli script di assegnazione dei punteggi per eseguire la distribuzione in istanze di contenitore di Azure, servizio Azure Kubernetes o in un FPGA (Field-Programmable Gate Array) come endpoint HTTP basato su REST. È anche possibile distribuire il modello in un dispositivo Azure IoT Edge come modulo.

## <a name="taxonomy"></a>Tassonomia 

Nel seguente diagramma viene illustrata una tassonomia dell'area di lavoro:

[![Tassonomia dell'area di lavoro](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png)](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png#lightbox)

Il diagramma mostra i componenti seguenti di un'area di lavoro:

+ Un'area di lavoro può contenere [macchine virtuali notebook](tutorial-1st-experiment-sdk-setup.md), risorse cloud configurate con l'ambiente Python necessario per eseguire Azure Machine Learning.
+ I [ruoli utente](how-to-assign-roles.md) consentono di condividere l'area di lavoro con altri utenti, team o progetti.
+ Le [destinazioni di calcolo](concept-azure-machine-learning-architecture.md#compute-targets) vengono usate per eseguire gli esperimenti.
+ Quando si crea l'area di lavoro, vengono create anche [le risorse associate](#resources) .
+ Gli [esperimenti](concept-azure-machine-learning-architecture.md#experiments) sono esecuzioni di training usate per compilare i modelli.  È possibile creare ed eseguire esperimenti con
    + [SDK Azure Machine Learning per Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
    + La sezione [esperimenti automatici di Machine Learning (anteprima)](how-to-create-portal-experiments.md) nell'portale di Azure o nella pagina di destinazione dell'area di lavoro (anteprima).
    + [Interfaccia visiva (anteprima)](ui-concept-visual-interface.md).
+ Le [pipeline](concept-azure-machine-learning-architecture.md#ml-pipelines) sono flussi di lavoro riutilizzabili per il training e la ripetizione del training del modello.
+ [DataSet](concept-azure-machine-learning-architecture.md#datasets-and-datastores) facilita la gestione dei dati usati per il training del modello e la creazione della pipeline.
+ Quando si dispone di un modello che si desidera distribuire, viene creato un modello registrato.
+ Usare il modello registrato e uno script di assegnazione dei punteggi per creare una [distribuzione](concept-azure-machine-learning-architecture.md#deployment).

## <a name="tools-for-workspace-interaction"></a>Strumenti per l'interazione con l'area di lavoro

È possibile interagire con l'area di lavoro nei modi seguenti:

+ Sul Web:
    + Il[portale di Azure](https://portal.azure.com)
    + [Pagina di destinazione dell'area di lavoro (anteprima)](https://ml.azure.com)
    + [Interfaccia visiva (anteprima)](ui-concept-visual-interface.md)
+ In Python con Azure Machine Learning [SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
+ Dalla riga di comando usando l' [estensione CLI](https://docs.microsoft.com/azure/machine-learning/service/reference-azure-machine-learning-cli) Azure Machine Learning

## <a name="machine-learning-with-a-workspace"></a>Machine Learning con un'area di lavoro

Le attività di Machine Learning leggono e/o scrivono elementi nell'area di lavoro. 

+ Eseguire un esperimento per eseguire il training di un esperimento di scrittura di modelli esecuzione di risultati nell'area di lavoro.
+ Usare il Machine Learning automatico per eseguire il training di un modello: scrive i risultati del training nell'area di lavoro.
+ Registrare un modello nell'area di lavoro.
+ Distribuire un modello: usa il modello registrato per creare una distribuzione.
+ Creazione ed esecuzione di flussi di lavoro riutilizzabili.
+ Visualizzazione di elementi di Machine Learning, ad esempio esperimenti, pipeline, modelli e distribuzioni.
+ Rilevare e monitorare i modelli.




## <a name="workspace-management"></a>Gestione dell'area di lavoro

È inoltre possibile eseguire le attività di gestione dell'area di lavoro seguenti:

| Attività di gestione dell'area di lavoro   | Portale              | SDK        | Interfaccia della riga di comando        |
|---------------------------|------------------|------------|------------|
| Creare un'area di lavoro        | **&check;**     | **&check;** | **&check;** |
| Creare e gestire risorse di calcolo    | **&check;**   | **&check;** |  **&check;**   |
| Gestisci l'accesso all'area di lavoro    | **&check;**   | |  **&check;**    |
| Creare una macchina virtuale per notebook | **&check;**   | |     |

### <a name='create-workspace'></a>Creare un'area di lavoro

Esistono diversi modi per creare un'area di lavoro.

* Usare il [portale di Azure](how-to-manage-workspace.md) per un'interfaccia di punto e clic per esaminare ogni passaggio.
* Usare [Azure Machine Learning SDK per Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) per creare un'area di lavoro in tempo reale da script Python o notebook di Jupiter
* Usare un [modello di Azure Resource Manager](how-to-create-workspace-template.md) o l'interfaccia della riga di comando di [Azure Machine Learning](reference-azure-machine-learning-cli.md) quando è necessario automatizzare o personalizzare la creazione con gli standard di sicurezza aziendali.
* Se si lavora in Visual Studio Code, utilizzare l' [estensione vs code](how-to-vscode-tools.md#get-started-with-azure-machine-learning).

## <a name="resources"></a>Risorse associate

Quando si crea una nuova area di lavoro, vengono create automaticamente diverse risorse di Azure usate dall'area di lavoro:

+ [Registro Azure Container](https://azure.microsoft.com/services/container-registry/): Registra i contenitori docker usati durante il training e quando si distribuisce un modello. Per ridurre al minimo i costi, il registro contenitori di contenitori viene **caricato Lazy** fino a quando non vengono create immagini di distribuzione
+ [Account di archiviazione di Azure](https://azure.microsoft.com/services/storage/): usato come archivio dati predefinito per l'area di lavoro.  I notebook di Jupyter usati con le VM del notebook vengono archiviati anche qui.
+ [Azure Application Insights](https://azure.microsoft.com/services/application-insights/): archivia le informazioni di monitoraggio sui modelli.
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): conserva segreti usati dalle destinazioni di calcolo e altre informazioni riservate richieste dall'area di lavoro.

> [!NOTE]
> Oltre alla creazione di nuove versioni, è possibile usare i servizi di Azure esistenti.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare Azure Machine Learning, vedere:

+ [Panoramica di Azure Machine Learning](overview-what-is-azure-ml.md)
+ [Creare un'area di lavoro](how-to-manage-workspace.md)
+ [Gestire un'area di lavoro](how-to-manage-workspace.md)
+ [Esercitazione: Eseguire il training di un modello](tutorial-train-models-with-aml.md)
