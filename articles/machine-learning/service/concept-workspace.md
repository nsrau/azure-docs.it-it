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
ms.date: 11/04/2019
ms.openlocfilehash: 4fe405d4027535eb6b0211f7a3f997194b8819aa
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580757"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>Che cos'è un'area di lavoro Azure Machine Learning?

L'area di lavoro è la risorsa di primo livello per Azure Machine Learning, che fornisce una posizione centralizzata per lavorare con tutti gli artefatti creati quando si usa Azure Machine Learning.  L'area di lavoro mantiene una cronologia di tutte le esecuzioni di training, inclusi i log, le metriche, l'output e uno snapshot degli script. Queste informazioni consentono di determinare il training che produce il modello migliore.  

Dopo aver creato un modello, è necessario registrarlo con l'area di lavoro. È quindi possibile usare il modello registrato e gli script di assegnazione dei punteggi per eseguire la distribuzione in istanze di contenitore di Azure, servizio Azure Kubernetes o in un FPGA (Field-Programmable Gate Array) come endpoint HTTP basato su REST. È anche possibile distribuire il modello in un dispositivo Azure IoT Edge come modulo.

I prezzi e le funzionalità disponibili variano a seconda che sia selezionata l'opzione [Basic o Enterprise Edition](overview-what-is-azure-ml.md#sku) per l'area di lavoro. Selezionare l'edizione quando si [crea l'area di lavoro](#create-workspace).  È anche possibile [eseguire l'aggiornamento](#upgrade) da Basic a Enterprise Edition.

## <a name="taxonomy"></a>Tassonomia 

Nel seguente diagramma viene illustrata una tassonomia dell'area di lavoro:

[![Tassonomia dell'area di lavoro](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png)](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png#lightbox)

Il diagramma mostra i componenti seguenti di un'area di lavoro:

+ Un'area di lavoro può contenere Azure Machine Learning macchine virtuali notebook, risorse cloud configurate con l'ambiente Python necessario per eseguire Azure Machine Learning.
+ I [ruoli utente](how-to-assign-roles.md) consentono di condividere l'area di lavoro con altri utenti, team o progetti.
+ Le [destinazioni di calcolo](concept-azure-machine-learning-architecture.md#compute-targets) vengono usate per eseguire gli esperimenti.
+ Quando si crea l'area di lavoro, vengono create anche [le risorse associate](#resources) .
+ Gli [esperimenti](concept-azure-machine-learning-architecture.md#experiments) sono esecuzioni di training usate per compilare i modelli.  
+ Le [pipeline](concept-azure-machine-learning-architecture.md#ml-pipelines) sono flussi di lavoro riutilizzabili per il training e la ripetizione del training del modello.
+ [DataSet](concept-azure-machine-learning-architecture.md#datasets-and-datastores) facilita la gestione dei dati usati per il training del modello e la creazione della pipeline.
+ Quando si dispone di un modello che si desidera distribuire, viene creato un modello registrato.
+ Usare il modello registrato e uno script di assegnazione dei punteggi per creare un [endpoint di distribuzione](concept-azure-machine-learning-architecture.md#endpoints).

## <a name="tools-for-workspace-interaction"></a>Strumenti per l'interazione con l'area di lavoro

È possibile interagire con l'area di lavoro nei modi seguenti:

+ Sul Web:
    + [Azure Machine Learning Studio](https://ml.azure.com) 
    + [Progettazione Azure Machine Learning (anteprima)](concept-designer.md) -disponibile solo nelle aree di lavoro [Enterprise Edition](overview-what-is-azure-ml.md#sku) .
+ In qualsiasi ambiente Python con [Azure Machine Learning SDK per Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
+ In qualsiasi ambiente R con l' [SDK Azure Machine Learning per r](https://azure.github.io/azureml-sdk-for-r/reference/index.html).
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

| Attività di gestione dell'area di lavoro   | di Microsoft Azure              | Studio | SDK Python/R SDK       | CLI        |
|---------------------------|---------|---------|------------|------------|
| Creare un'area di lavoro        | **&check;**     | | **&check;** | **&check;** |
| Gestisci l'accesso all'area di lavoro    | **&check;**   || |  **&check;**    |
| Eseguire l'aggiornamento a Enterprise Edition    | **&check;** |  | |     |
| Creare e gestire risorse di calcolo    |   | **&check;** | **&check;** |  **&check;**   |
| Creare una VM notebook |   | **&check;** | |     |


## <a name='create-workspace'></a>Creare un'area di lavoro

Quando si crea un'area di lavoro, è necessario decidere se crearla con [Basic o Enterprise Edition](overview-what-is-azure-ml.md#sku). L'edizione determina le funzionalità disponibili nell'area di lavoro. Tra le altre funzionalità, Enterprise Edition consente di accedere a [Azure machine learning designer](concept-designer.md) e alla versione studio per la creazione di [esperimenti automatici di Machine Learning](tutorial-first-experiment-automated-ml.md).  Per informazioni dettagliate e informazioni sui prezzi, vedere [Azure Machine Learning prezzi](https://azure.microsoft.com/pricing/details/machine-learning/).

Esistono diversi modi per creare un'area di lavoro:  

* Usare il [portale di Azure](how-to-manage-workspace.md) per un'interfaccia di punto e clic per esaminare ogni passaggio.
* Usare [Azure Machine Learning SDK per Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) per creare un'area di lavoro in tempo reale da script Python o notebook di Jupiter
* Usare un [modello di Azure Resource Manager](how-to-create-workspace-template.md) o l'interfaccia della riga di comando di [Azure Machine Learning](reference-azure-machine-learning-cli.md) quando è necessario automatizzare o personalizzare la creazione con gli standard di sicurezza aziendali.
* Se si lavora in Visual Studio Code, utilizzare l' [estensione vs code](how-to-vscode-tools.md#get-started-with-azure-machine-learning-for-visual-studio-code).

## <a name="upgrade"></a>Eseguire l'aggiornamento a Enterprise Edition

È possibile [aggiornare l'area di lavoro da Basic a Enterprise Edition](how-to-manage-workspace.md#upgrade) usando portale di Azure. Non è possibile effettuare il downgrade di un'area di lavoro Enterprise Edition a un'area di lavoro Basic Edition. 

## <a name="resources"></a>Risorse associate

Quando si crea una nuova area di lavoro, vengono create automaticamente diverse risorse di Azure usate dall'area di lavoro:

+ [Azure container Registry](https://azure.microsoft.com/services/container-registry/): registra i contenitori Docker usati durante il training e quando si distribuisce un modello. Per ridurre al minimo i costi, il registro contenitori di contenitori viene **caricato Lazy** fino a quando non vengono create immagini di distribuzione
+ [Account di archiviazione di Azure](https://azure.microsoft.com/services/storage/): viene usato come archivio dati predefinito per l'area di lavoro.  I notebook di Jupyter usati con la macchina virtuale Azure Machine Learning notebook vengono archiviati anche qui.
+ [Applicazione Azure Insights](https://azure.microsoft.com/services/application-insights/): archivia le informazioni di monitoraggio relative ai modelli.
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): archivia i segreti usati dalle destinazioni di calcolo e altre informazioni riservate necessarie per l'area di lavoro.

> [!NOTE]
> Oltre alla creazione di nuove versioni, è possibile usare i servizi di Azure esistenti.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare Azure Machine Learning, vedere:

+ [Panoramica di Azure Machine Learning](overview-what-is-azure-ml.md)
+ [Creare un'area di lavoro](how-to-manage-workspace.md)
+ [Gestire un'area di lavoro](how-to-manage-workspace.md)
+ [Esercitazione: Introduzione alla creazione del primo esperimento ML con Python SDK](tutorial-1st-experiment-sdk-setup.md)
+ [Esercitazione: Introduzione a Azure Machine Learning con R SDK]( tutorial-1st-r-experiment.md)
+ [Esercitazione: creare il primo modello di classificazione con Machine Learning automatico](tutorial-first-experiment-automated-ml.md) (disponibile solo nelle aree di lavoro [Enterprise Edition](overview-what-is-azure-ml.md#sku) )
+ [Esercitazione: stimare il prezzo dell'automobile con la finestra di progettazione](tutorial-designer-automobile-price-train-score.md) (disponibile solo nelle aree di lavoro [Enterprise Edition](overview-what-is-azure-ml.md#sku) )
