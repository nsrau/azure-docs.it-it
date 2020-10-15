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
ms.date: 09/22/2020
ms.openlocfilehash: dccdbcb99b32a922d76b29e5896cdf99290758d0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91302452"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>Che cos'è un'area di lavoro Azure Machine Learning?

L'area di lavoro è la risorsa di primo livello per Azure Machine Learning, che fornisce una posizione centralizzata per lavorare con tutti gli artefatti creati quando si usa Azure Machine Learning.  L'area di lavoro mantiene una cronologia di tutte le esecuzioni di training, inclusi i log, le metriche, l'output e uno snapshot degli script. Queste informazioni consentono di determinare il training che produce il modello migliore.  

Dopo aver creato un modello, è necessario registrarlo con l'area di lavoro. È quindi possibile usare il modello registrato e gli script di assegnazione dei punteggi per eseguire la distribuzione in istanze di contenitore di Azure, servizio Azure Kubernetes o in un FPGA (Field-Programmable Gate Array) come endpoint HTTP basato su REST. È anche possibile distribuire il modello in un dispositivo Azure IoT Edge come modulo.

## <a name="taxonomy"></a>Tassonomia 

Nel seguente diagramma viene illustrata una tassonomia dell'area di lavoro:

[![Tassonomia area di lavoro](./media/concept-workspace/azure-machine-learning-taxonomy.png)](./media/concept-workspace/azure-machine-learning-taxonomy.png#lightbox)

Il diagramma mostra i componenti seguenti di un'area di lavoro:

+ Un'area di lavoro può contenere [Azure Machine Learning istanze di calcolo](concept-compute-instance.md), risorse cloud configurate con l'ambiente Python necessario per eseguire Azure Machine Learning.

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

> [!IMPORTANT]
> Gli strumenti contrassegnati (anteprima) di seguito sono attualmente disponibili in anteprima pubblica.
> La versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

+ Sul Web:
    + [Azure Machine Learning Studio ](https://ml.azure.com) 
    + [Finestra di progettazione di Azure Machine Learning](concept-designer.md) 
+ In qualsiasi ambiente Python con [Azure Machine Learning SDK per Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true).
+ In qualsiasi ambiente R con [Azure Machine Learning SDK per r (anteprima)](https://azure.github.io/azureml-sdk-for-r/reference/index.html).
+ Dalla riga di comando usando l' [estensione CLI](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli) Azure Machine Learning
+ [Estensione Azure Machine Learning VS Code](how-to-manage-resources-vscode.md#workspaces)


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

| Attività di gestione dell'area di lavoro   | Portale              | Studio | SDK Python/R SDK       | CLI        | Visual Studio Code
|---------------------------|---------|---------|------------|------------|------------|
| Creare un'area di lavoro        | **&check;**     | | **&check;** | **&check;** | **&check;** |
| Gestisci l'accesso all'area di lavoro    | **&check;**   || |  **&check;**    ||
| Creare e gestire risorse di calcolo    | **&check;**   | **&check;** | **&check;** |  **&check;**   ||
| Creare una VM notebook |   | **&check;** | |     ||

> [!WARNING]
> Lo spostamento dell’area di lavoro di Azure Machine Learning in una diversa sottoscrizione o della sottoscrizione proprietaria su un nuovo tenant non è supportato in quanto ciò può provocare errori.

## <a name="create-a-workspace"></a><a name='create-workspace'></a> Creare un'area di lavoro

Esistono diversi modi per creare un'area di lavoro:  

* Usare il [portale di Azure](how-to-manage-workspace.md) per un'interfaccia di punto e clic per esaminare ogni passaggio.
* Usare [Azure Machine Learning SDK per Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true#&preserve-view=trueworkspace) per creare un'area di lavoro in tempo reale da script Python o notebook di Jupiter
* Usare un [modello di Azure Resource Manager](how-to-create-workspace-template.md) o l'interfaccia della riga di comando di [Azure Machine Learning](reference-azure-machine-learning-cli.md) quando è necessario automatizzare o personalizzare la creazione con gli standard di sicurezza aziendali.
* Se si lavora in Visual Studio Code, utilizzare l' [estensione vs code](how-to-manage-resources-vscode.md#create-a-workspace).

> [!NOTE]
> Il nome dell'area di lavoro non rileva la distinzione tra maiuscole e minuscole.

## <a name="associated-resources"></a><a name="resources"></a> Risorse associate

Quando si crea una nuova area di lavoro, vengono create automaticamente diverse risorse di Azure usate dall'area di lavoro:

+ [Account di archiviazione di Azure](https://azure.microsoft.com/services/storage/): viene usato come archivio dati predefinito per l'area di lavoro.  I notebook di Jupyter usati con le istanze di calcolo Azure Machine Learning vengono archiviati anche qui. 
  
  > [!IMPORTANT]
  > Per impostazione predefinita, l'account di archiviazione è un account per utilizzo generico V1. È possibile [eseguire l'aggiornamento a utilizzo generico V2](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) dopo che l'area di lavoro è stata creata. Non abilitare lo spazio dei nomi gerarchico nell'account di archiviazione dopo l'aggiornamento a utilizzo generico V2.

  Per usare un account di archiviazione di Azure esistente, non può essere un account Premium (Premium_LRS e Premium_GRS). Non può inoltre avere uno spazio dei nomi gerarchico (usato con Azure Data Lake Storage Gen2). Con l'account di archiviazione _predefinito_ dell'area di lavoro non è supportato alcun spazio dei nomi di archiviazione Premium o gerarchico. È possibile usare archiviazione Premium o uno spazio dei nomi gerarchico con account di archiviazione _non predefiniti_ .
  
+ [Azure container Registry](https://azure.microsoft.com/services/container-registry/): registra i contenitori Docker usati durante il training e quando si distribuisce un modello. Per ridurre al minimo i costi, il registro contenitori di contenitori viene **caricato Lazy** fino a quando non vengono create immagini di distribuzione

+ [Applicazione Azure Insights](https://azure.microsoft.com/services/application-insights/): archivia le informazioni di monitoraggio relative ai modelli.

+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): archivia i segreti usati dalle destinazioni di calcolo e altre informazioni riservate necessarie per l'area di lavoro.

> [!NOTE]
> Oltre alla creazione di nuove versioni, è possibile usare i servizi di Azure esistenti.

<a name="wheres-enterprise"></a>

## <a name="what-happened-to-enterprise-edition"></a>Cosa è successo a Enterprise Edition

Al 2020 settembre, tutte le funzionalità disponibili nelle aree di lavoro Enterprise Edition sono ora disponibili anche nelle aree di lavoro Basic Edition. Non è più possibile creare nuove aree di lavoro aziendali.  Tutte le chiamate a SDK, CLI o Azure Resource Manager che usano il parametro continueranno `sku` a funzionare, ma verrà eseguito il provisioning di un'area di lavoro di base.

A partire dal 21 dicembre, tutte le aree di lavoro Enterprise Edition verranno automaticamente impostate su Basic Edition, che ha le stesse funzionalità. Durante questo processo non si verificheranno tempi di inattività. Il 1 ° gennaio 2021, Enterprise Edition verrà ritirato formalmente. 

In entrambe le edizioni, i clienti sono responsabili dei costi delle risorse di Azure utilizzate e non dovranno pagare costi aggiuntivi per Azure Machine Learning. Per ulteriori informazioni, fare riferimento alla [pagina dei prezzi Azure Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/) .

## <a name="next-steps"></a>Passaggi successivi

Per un'introduzione ad Azure Machine Learning, vedere:

+ [Panoramica di Azure Machine Learning](overview-what-is-azure-ml.md)
+ [Creare un'area di lavoro](how-to-manage-workspace.md)
+ [Gestire un'area di lavoro](how-to-manage-workspace.md)
+ [Esercitazione: Introduzione alla creazione del primo esperimento di Machine Learning con Python SDK](tutorial-1st-experiment-sdk-setup.md)
+ [Esercitazione: Introduzione a Azure Machine Learning con R SDK](tutorial-1st-r-experiment.md)
+ [Esercitazione: Creare il primo modello di classificazione con apprendimento automatico](tutorial-first-experiment-automated-ml.md) 
+ [Esercitazione: Stimare il prezzo di un'automobile con la finestra di progettazione](tutorial-designer-automobile-price-train-score.md)
