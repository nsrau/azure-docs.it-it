---
title: Che cos'è un'area di lavoro
titleSuffix: Azure Machine Learning service
description: L'area di lavoro è la risorsa di primo livello per il servizio Azure Machine Learning. Mantiene una cronologia di tutte le esecuzioni di training, inclusi i log, metriche, output e uno snapshot degli script. Usare queste informazioni per determinare quali Esegui training produce il modello migliore
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/21/2019
ms.openlocfilehash: 2f3d9eeca1404fcae121ae5fead222cbde4037b1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67059255"
---
# <a name="what-is-an-azure-machine-learning-service-workspace"></a>Che cos'è un'area di lavoro del servizio di Azure Machine Learning?

L'area di lavoro è la risorsa di primo livello per il servizio di Azure Machine Learning, che fornisce una posizione centralizzata per lavorare con tutti gli elementi creati quando si usa il servizio di Azure Machine Learning.  L'area di lavoro mantiene una cronologia di tutte le esecuzioni di training, inclusi i log, metriche, output e uno snapshot degli script. Queste informazioni consentono di determinare il training che produce il modello migliore.  

Dopo aver creato un modello, ad esempio, registrarlo con l'area di lavoro. Utilizzare quindi il modello registrato e gli script di assegnazione dei punteggi da distribuire in istanze di contenitore di Azure, Azure Kubernetes Service, o in una matrice di campo-programmable gate (FPGA) come un endpoint HTTP basato su REST. È anche possibile distribuire il modello in un dispositivo Azure IoT Edge come modulo.

## <a name="taxonomy"></a>Tassonomia 

Nel seguente diagramma viene illustrata una tassonomia dell'area di lavoro:

[![Tassonomia dell'area di lavoro](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png)](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png#lightbox)

Il diagramma mostra i seguenti componenti di un'area di lavoro:

+ Un'area di lavoro può contenere [macchine virtuali di Notebook](quickstart-run-cloud-notebook.md), configurate con l'ambiente di Python necessario per eseguire Azure Machine Learning di risorse cloud.
+ [I ruoli utente](how-to-assign-roles.md) consentono di condividere l'area di lavoro con altri utenti, i team o i progetti.
+ [Le destinazioni di calcolo](concept-azure-machine-learning-architecture.md#compute-targets) vengono usate per eseguire gli esperimenti.
+ Quando si crea l'area di lavoro [le risorse associate](#resources) vengono anche creati automaticamente.
+ [Gli esperimenti](concept-azure-machine-learning-architecture.md#experiments) sono esecuzioni di training che consente di creare modelli.  È possibile creare ed eseguire esperimenti con
    + Il [di Azure Machine Learning SDK per Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
    + Il [automatizzata esperimenti di machine learning (anteprima)](how-to-create-portal-experiments.md) sezione nel portale di Azure.
    + Il [interfaccia visiva (anteprima)](ui-concept-visual-interface.md).
+ [Le pipeline](concept-azure-machine-learning-architecture.md#ml-pipelines) sono flussi di lavoro riutilizzabile di training e di ripetizione del training del modello.
+ [I set di dati](concept-azure-machine-learning-architecture.md#datasets-and-datastores) aiuto nella gestione dei dati è usare per la creazione di pipeline e training di modelli.
+ Dopo aver creato un modello da distribuire, si crea un modello registrato.
+ Usare il modello registrato e uno script di assegnazione dei punteggi per creare un [distribuzione](concept-azure-machine-learning-architecture.md#deployment).

## <a name="tools-for-workspace-interaction"></a>Strumenti per l'interazione dell'area di lavoro

È possibile interagire con l'area di lavoro nei modi seguenti:

+ Sul web:
    + Il[portale di Azure](https://portal.azure.com)
    + Il [interfaccia visiva (anteprima)](ui-concept-visual-interface.md)
+ In Python usando Azure Machine Learning [SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
+ Nella riga di comando usando Azure Machine Learning [estensione dell'interfaccia della riga](https://docs.microsoft.com/azure/machine-learning/service/reference-azure-machine-learning-cli)

## <a name="machine-learning-with-a-workspace"></a>Machine learning con un'area di lavoro

Attività di Machine learning di lettura e/o scrivere gli elementi nell'area di lavoro. 

+ Eseguire un esperimento per eseguire il training di un modello - scritture sperimentare i risultati dell'esecuzione nell'area di lavoro.
+ Usare automatizzati di Machine Learning per addestrare un modello - scrive i risultati di formazione all'area di lavoro.
+ Registrare un modello nell'area di lavoro.
+ Distribuire un modello: Usa il modello registrato per creare una distribuzione.
+ Creare ed eseguire flussi di lavoro riutilizzabili.
+ Visualizzazione apprendimento degli elementi, ad esempio gli esperimenti, pipeline, modelli, le distribuzioni.
+ Modelli di traccia e monitoraggio.

## <a name="workspace-management"></a>Gestione dell'area di lavoro

È anche possibile eseguire le attività di gestione dell'area di lavoro seguenti:

| Attività di gestione dell'area di lavoro   | Portale              | SDK        | CLI        |
|---------------------------|------------------|------------|------------|
| Creare un'area di lavoro        | **&check;**     | **&check;** | **&check;** |
| Creare e gestire le risorse di calcolo    | **&check;**   | **&check;** |  **&check;**   |
| Gestire l'accesso dell'area di lavoro    | **&check;**   | |  **&check;**    |
| Creare una macchina virtuale per notebook | **&check;**   | |     |

Introduzione al servizio [creazione di un'area di lavoro](setup-create-workspace.md).

## <a name="resources"></a> Risorse associate

Quando si crea una nuova area di lavoro, vengono create automaticamente diverse risorse di Azure usate dall'area di lavoro:

+ [Registro Azure Container](https://azure.microsoft.com/services/container-registry/): Registra i contenitori docker usati durante il training e quando si distribuisce un modello. Per ridurre al minimo i costi, registro contenitori di AZURE viene **lazy caricato** finché non vengono create le immagini di distribuzione.
+ [Account di archiviazione di Azure](https://azure.microsoft.com/services/storage/): usato come archivio dati predefinito per l'area di lavoro.
+ [Azure Application Insights](https://azure.microsoft.com/services/application-insights/): archivia le informazioni di monitoraggio sui modelli.
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): conserva segreti usati dalle destinazioni di calcolo e altre informazioni riservate richieste dall'area di lavoro.

> [!NOTE]
> Oltre alla creazione di nuove versioni, è possibile usare i servizi di Azure esistenti.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare con il servizio Azure Machine Learning, vedere:

+ [Panoramica del servizio Azure Machine Learning](overview-what-is-azure-ml.md)
+ [Creare un'area di lavoro](setup-create-workspace.md)
+ [Gestire un'area di lavoro](how-to-manage-workspace.md)
+ [Esercitazione: Eseguire il training di un modello](tutorial-train-models-with-aml.md)
