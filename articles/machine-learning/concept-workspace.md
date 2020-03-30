---
title: Che cos'è un'area di lavoro
titleSuffix: Azure Machine Learning
description: The workspace is the top-level resource for Azure Machine Learning. Mantiene una cronologia di tutte le esecuzioni di training, inclusi log, metriche, output e uno snapshot degli script. Queste informazioni consentono di determinare quale corsa di training produce il modello migliore
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 12/27/2019
ms.openlocfilehash: 03bc49c24e3c2d32e97f3e5e03bd39da63428a6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77505569"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>Che cos'è un'area di lavoro di Azure Machine Learning?

The workspace is the top-level resource for Azure Machine Learning, providing a centralized place to work with all the artifacts you create when you use Azure Machine Learning.  L'area di lavoro mantiene una cronologia di tutte le esecuzioni di training, inclusi log, metriche, output e uno snapshot degli script. Queste informazioni consentono di determinare il training che produce il modello migliore.  

Una volta che hai un modello che ti piace, lo registri con l'area di lavoro. È quindi possibile usare il modello registrato e gli script di assegnazione del punteggio alle istanze del contenitore di Azure, al servizio Azure Kubernetes o a un'FPGA (Field-programmable Gate Array) come endpoint HTTP basato su REST. È anche possibile distribuire il modello in un dispositivo Azure IoT Edge come modulo.

I prezzi e le funzionalità disponibili dipendono dall'opzione [Basic o Enterprise Edition](overview-what-is-azure-ml.md#sku) per l'area di lavoro. L'edizione viene selezionata quando si [crea l'area di lavoro](#create-workspace).  È inoltre possibile [eseguire l'aggiornamento](#upgrade) da Basic a Enterprise Edition.

## <a name="taxonomy"></a>Tassonomia 

Nel seguente diagramma viene illustrata una tassonomia dell'area di lavoro:

[![Tassonomia dell'area di lavoro](./media/concept-workspace/azure-machine-learning-taxonomy.png)](./media/concept-workspace/azure-machine-learning-taxonomy.png#lightbox)

Il diagramma mostra i seguenti componenti di un'area di lavoro:

+ Un'area di lavoro può contenere istanze di calcolo di Azure Machine Learning, risorse cloud configurate con l'ambiente Python necessario per eseguire Azure Machine Learning.A workspace can contain [Azure Machine Learning compute instances,](concept-compute-instance.md)cloud resources configured with the Python environment necessary to run Azure Machine Learning.

+ [I ruoli utente](how-to-assign-roles.md) consentono di condividere l'area di lavoro con altri utenti, team o progetti.
+ [Le destinazioni](concept-azure-machine-learning-architecture.md#compute-targets) di calcolo vengono usate per eseguire gli esperimenti.
+ Quando si crea l'area di lavoro, vengono create automaticamente anche [le risorse associate.](#resources)
+ [Gli esperimenti](concept-azure-machine-learning-architecture.md#experiments) sono corse di formazione che usi per creare i tuoi modelli.  
+ [Le pipeline](concept-azure-machine-learning-architecture.md#ml-pipelines) sono flussi di lavoro riutilizzabili per il training e la riqualificazione del modello.
+ I set di dati facilitano la gestione dei dati [utilizzati](concept-azure-machine-learning-architecture.md#datasets-and-datastores) per il training del modello e la creazione di pipeline.
+ Dopo aver creato un modello da distribuire, si crea un modello registrato.
+ Usare il modello registrato e uno script di assegnazione del punteggio per creare un endpoint di [distribuzione.](concept-azure-machine-learning-architecture.md#endpoints)

## <a name="tools-for-workspace-interaction"></a>Strumenti per l'interazione dell'area di lavoro

È possibile interagire con l'area di lavoro nei modi seguenti:

+ Sul web:
    + [Studio di Azure Machine Learning](https://ml.azure.com) 
    + Finestra di [progettazione di Azure Machine Learning (anteprima)](concept-designer.md) - Disponibile solo nelle aree di lavoro [Enterprise Edition.Azure](overview-what-is-azure-ml.md#sku) Machine Learning designer (preview) - Disponibile only in Enterprise edition workspaces.
+ In qualsiasi ambiente Python con [Azure Machine Learning SDK per Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
+ In qualsiasi ambiente R con [Azure Machine Learning SDK per R](https://azure.github.io/azureml-sdk-for-r/reference/index.html).
+ Nella riga di comando con [l'estensione dell'interfaccia della riga](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli) di comando di Azure Machine Learning

## <a name="machine-learning-with-a-workspace"></a>Machine learning con un'area di lavoro

Le attività di apprendimento automatico leggono e/o scrivono elementi nell'area di lavoro.

+ Eseguire un esperimento per eseguire il training di un modello: scrive i risultati dell'esecuzione dell'esperimento nell'area di lavoro.
+ Usare la mL automatizzata per eseguire il training di un modello: consente di scrivere i risultati del training nell'area di lavoro.
+ Registrare un modello nell'area di lavoro.
+ Distribuire un modello: usa il modello registrato per creare una distribuzione.
+ Creare ed eseguire flussi di lavoro riutilizzabili.
+ Visualizzare gli elementi di Apprendimento automatico, ad esempio esperimenti, pipeline, modelli, distribuzioni.
+ Tracciare e monitorare i modelli.

## <a name="workspace-management"></a>Gestione dell'area di lavoro

È inoltre possibile eseguire le attività di gestione dell'area di lavoro seguenti:You can also perform the following workspace management tasks:

| Attività di gestione dell'area di lavoro   | Portale              | Studio | Python SDK / R SDK       | CLI        |
|---------------------------|---------|---------|------------|------------|
| Creare un'area di lavoro        | **&check;**     | | **&check;** | **&check;** |
| Gestire l'accesso all'area di lavoro    | **&check;**   || |  **&check;**    |
| Eseguire l'aggiornamento all'edizione Enterprise    | **&check;** | **&check;**  | |     |
| Creare e gestire risorse di calcolo    | **&check;**   | **&check;** | **&check;** |  **&check;**   |
| Creare una macchina virtuale del blocco appuntiCreate a Notebook VM |   | **&check;** | |     |

> [!WARNING]
> Lo spostamento dell'area di lavoro di Azure Machine Learning in una sottoscrizione diversa o lo spostamento della sottoscrizione proprietaria in un nuovo tenant non sono supportati. Questa operazione può causare errori.

## <a name="create-a-workspace"></a><a name='create-workspace'></a>Creare un'area di lavoro

Quando si crea un'area di lavoro, si decide se crearla con [Basic o Enterprise Edition](overview-what-is-azure-ml.md#sku). L'edizione determina le funzionalità disponibili nell'area di lavoro. Tra le altre funzionalità, l'edizione Enterprise consente di accedere a [Progettazione Azure Machine Learning](concept-designer.md) e alla versione studio per la creazione di [esperimenti automatizzati](tutorial-first-experiment-automated-ml.md)di apprendimento automatico.  Per altre informazioni dettagliate e sui prezzi, vedere Prezzi di Azure Machine Learning.For more details and pricing information, see [Azure Machine Learning pricing.](https://azure.microsoft.com/pricing/details/machine-learning/)

Esistono diversi modi per creare un'area di lavoro:There are multiple ways to create a workspace:  

* Usare il portale di [Azure](how-to-manage-workspace.md) per un'interfaccia point-and-click per illustrare in questo passaggio l'utente.
* Usare [Azure Machine Learning SDK per Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) per creare un'area di lavoro in tempo reale da script Python o blocchi appunti di GioveUse the Azure Machine Learning SDK for Python to create a workspace in the fly from Python scripts or Jupiter notebooks
* Usare un modello di [Azure Resource Manager](how-to-create-workspace-template.md) o l'interfaccia della riga di comando di Azure Machine [Learning](reference-azure-machine-learning-cli.md) quando è necessario automatizzare o personalizzare la creazione con gli standard di sicurezza aziendali.
* Se si lavora in Visual Studio Code, utilizzare [l'estensione di codice VS](tutorial-setup-vscode-extension.md).

> [!NOTE]
> Il nome dell'area di lavoro non fa distinzione tra maiuscole e minuscole.

## <a name="upgrade-to-enterprise-edition"></a><a name="upgrade"></a>Eseguire l'aggiornamento all'edizione Enterprise

È possibile [aggiornare l'area di lavoro da Basic a Enterprise Edition](how-to-manage-workspace.md#upgrade) tramite il portale di Azure.You can upgrade your workspace from Basic to Enterprise edition using Azure portal. Non è possibile eseguire il downgrade di un'area di lavoro Enterprise Edition a un'area di lavoro edizione Basic. 

## <a name="associated-resources"></a><a name="resources"></a>Risorse associate

Quando si crea una nuova area di lavoro, vengono create automaticamente diverse risorse di Azure usate dall'area di lavoro:

+ [Registro contenitori](https://azure.microsoft.com/services/container-registry/)di Azure: registra i contenitori docker utilizzati durante il training e quando si distribuisce un modello. Per ridurre al minimo i costi, l'ACR viene **caricato in modo lazy** fino alla creazione delle immagini di distribuzione.
+ [Account di Archiviazione di Azure:](https://azure.microsoft.com/services/storage/)viene usato come archivio dati predefinito per l'area di lavoro.  Anche i blocchi appunti di Jupyter usati con le istanze di calcolo di Azure Machine Learning vengono archiviati anche qui.
+ [Azure Application Insights:](https://azure.microsoft.com/services/application-insights/)archivia le informazioni di monitoraggio sui modelli.
+ [Insieme di credenziali delle](https://azure.microsoft.com/services/key-vault/)chiavi di Azure: archivia i segreti usati dalle destinazioni di calcolo e altre informazioni riservate necessarie per l'area di lavoro.

> [!NOTE]
> Oltre alla creazione di nuove versioni, è possibile usare i servizi di Azure esistenti.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare Azure Machine Learning, vedere:To get started with Azure Machine Learning, see:

+ [Panoramica di Azure Machine LearningAzure Machine Learning overview](overview-what-is-azure-ml.md)
+ [Creare un'area di lavoro](how-to-manage-workspace.md)
+ [Gestire un'area di lavoro](how-to-manage-workspace.md)
+ [Esercitazione: Iniziare a creare il primo esperimento Di ML con Python SDK](tutorial-1st-experiment-sdk-setup.md)
+ [Esercitazione: Introduzione ad Azure Machine Learning con R SDKTutorial: Get started with Azure Machine Learning with the R SDK](tutorial-1st-r-experiment.md)
+ [Esercitazione: Creare il primo modello di classificazione con l'apprendimento automatico automatizzato](tutorial-first-experiment-automated-ml.md) (disponibile solo nelle aree di lavoro enterprise Edition)Tutorial: Create your first classification model with automated machine learning (Available only in [Enterprise edition](overview-what-is-azure-ml.md#sku) workspaces)
+ [Esercitazione: Prevedere il prezzo dell'automobile con il progettista](tutorial-designer-automobile-price-train-score.md) (disponibile solo nelle aree di lavoro [Enterprise Edition)](overview-what-is-azure-ml.md#sku)
