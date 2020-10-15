---
title: Esportare o eliminare i dati dell'area di lavoro
titleSuffix: Azure Machine Learning
description: Informazioni su come esportare o eliminare l'area di lavoro con Azure Machine Learning Studio, l'interfaccia della riga di comando, l'SDK e le API REST autenticate.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
author: lobrien
ms.author: laobri
ms.date: 04/24/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: e847f62c2ae3d1d68c39685a38b67e1d0ada8c2f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91251015"
---
# <a name="export-or-delete-your-machine-learning-service-workspace-data"></a>Esportare o eliminare i dati dell'area di lavoro nel servizio Machine Learning



In Azure Machine Learning, è possibile esportare o eliminare i dati dell'area di lavoro usando l'interfaccia grafica del portale o Python SDK. Questo articolo descrive entrambe le opzioni.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>Controllare i dati dell'area di lavoro

I dati del prodotto archiviati da Azure Machine Learning sono disponibili per l'esportazione e l'eliminazione. È possibile esportare ed eliminare usando Azure Machine Learning Studio, l'interfaccia della riga di comando e l'SDK. I dati di telemetria sono accessibili tramite il portale Privacy di Azure. 

In Azure Machine Learning i dati personali sono costituiti da informazioni utente nei documenti della cronologia di esecuzione. 

## <a name="delete-high-level-resources-using-the-portal"></a>Eliminare le risorse di alto livello usando il portale

Quando si crea un'area di lavoro, Azure crea una serie di risorse all'interno del gruppo di risorse:

- Area di lavoro stessa
- Un account di archiviazione
- Registro contenitori
- Un'istanza di Application Insights
- Un insieme di credenziali delle chiavi

Queste risorse possono essere eliminate selezionandola dall'elenco e scegliendo **Elimina** . 

:::image type="content" source="media/how-to-export-delete-data/delete-resource-group-resources.png" alt-text="Screenshot del portale con l'icona di eliminazione evidenziata":::

I documenti della cronologia di esecuzione, che possono contenere informazioni utente personali, vengono archiviati nell'account di archiviazione nell'archivio BLOB, nelle sottocartelle di `/azureml` . È possibile scaricare ed eliminare i dati dal portale.

:::image type="content" source="media/how-to-export-delete-data/storage-account-folders.png" alt-text="Screenshot del portale con l'icona di eliminazione evidenziata":::

## <a name="export-and-delete-machine-learning-resources-using-azure-machine-learning-studio"></a>Esportare ed eliminare risorse di Machine Learning con Azure Machine Learning Studio

Azure Machine Learning Studio offre una visualizzazione unificata delle risorse di Machine Learning, ad esempio notebook, set di impostazioni, modelli ed esperimenti. Azure Machine Learning Studio enfatizza la conservazione di un record dei dati e degli esperimenti. Le risorse di calcolo, ad esempio le pipeline e le risorse di calcolo, possono essere eliminate tramite il browser. Per queste risorse, passare alla risorsa in questione e scegliere **Elimina**. 

I set di dati possono essere annullati e gli esperimenti possono essere archiviati, ma queste operazioni non eliminano i dati. Per rimuovere completamente i dati, i set di dati e i dati di esecuzione devono essere eliminati a livello di archiviazione. L'eliminazione a livello di archiviazione viene eseguita tramite il portale, come descritto in precedenza.

È possibile scaricare gli artefatti di training da esecuzioni sperimentali con Studio. Scegliere l' **esperimento** ed **eseguirlo** a cui si è interessati. Scegliere **output + log** e passare agli elementi specifici che si desidera scaricare. Scegliere **...** e **scaricare**.

È possibile scaricare un modello registrato passando al **modello** desiderato e scegliendo **Scarica**. 

:::image type="contents" source="media/how-to-export-delete-data/model-download.png" alt-text="Screenshot del portale con l'icona di eliminazione evidenziata":::

## <a name="export-and-delete-resources-using-the-python-sdk"></a>Esportare ed eliminare risorse con Python SDK

È possibile scaricare gli output di una determinata esecuzione utilizzando: 

```python
# Retrieved from Azure Machine Learning web UI
run_id = 'aaaaaaaa-bbbb-cccc-dddd-0123456789AB'
experiment = ws.experiments['my-experiment']
run = next(run for run in ex.get_runs() if run.id == run_id)
metrics_output_port = run.get_pipeline_output('metrics_output')
model_output_port = run.get_pipeline_output('model_output')

metrics_output_port.download('.', show_progress=True)
model_output_port.download('.', show_progress=True)
```

Le risorse di Machine Learning seguenti possono essere eliminate usando Python SDK: 

| Type | Chiamata di funzione | Note | 
| --- | --- | --- |
| `Workspace` | [`delete`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true#&preserve-view=truedelete-delete-dependent-resources-false--no-wait-false-) | Usare `delete-dependent-resources` per eseguire il propagazione dell'eliminazione |
| `Model` | [`delete`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truedelete--) | | 
| `ComputeTarget` | [`delete`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py&preserve-view=true#&preserve-view=truedelete--) | |
| `WebService` | [`delete`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py&preserve-view=true) | | 

