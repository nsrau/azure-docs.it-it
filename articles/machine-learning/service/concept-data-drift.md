---
title: Sfasamento di dati di monitoraggio (anteprima)
titleSuffix: Azure Machine Learning service
description: Informazioni su come servizio di Azure Machine Learning è possibile monitorare per lo sfasamento di dati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cody-dkdc
ms.author: copeters
ms.date: 06/20/2019
ms.openlocfilehash: a03e3124647869e7148f271810bb523986a851c6
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442376"
---
# <a name="what-is-data-drift-monitoring-preview"></a>Che cos'è data deviano monitoring (anteprima)?

Lo sfasamento di dati è la modifica nella distribuzione dei dati. Nel contesto di machine learning, sottoposto a training modelli di machine learning verifichi le prestazioni della stima con funzionalità ridotte a causa di deviazione. Monitoraggio sfasamento tra dati di training e dati usati per l'esecuzione di stime può aiutare a rilevare problemi di prestazioni.

Sono solo tanto quanto i dati usati per il training dei modelli di Machine learning. Distribuzione dei modelli nell'ambiente di produzione senza monitoraggio delle prestazioni può causare effetti negativi e non rilevati. Con monitoraggio sfasamento dei dati, è possibile rilevare e adattarsi a sfasamento di dati. 

## <a name="when-to-monitor-for-data-drift"></a>Quando esegue il monitoraggio sfasamento di dati?

È possibile monitorare le metriche includono:

+ Grandezza di deviazione (sfasamento del coefficiente)
+ Causa della deviazione (contributo di deviazione dalla funzionalità)
+ Metriche di distanza (Wasserstein, energia e così via)

Con questo monitoraggio, avvisi o le azioni da impostare quando viene rilevato uno sfasamento e il data scientist possono ricercare la causa principale del problema. 

Se si ritiene che i dati di input per il modello distribuito potrebbero cambiare, è consigliabile usare il rilevamento di deviazione di dati.

## <a name="how-data-drift-is-monitored-in-azure-machine-learning-service"></a>Modo in cui viene monitorato lo sfasamento di dati nel servizio Azure Machine Learning

Usando **servizio di Azure Machine Learning**, lo sfasamento di dati viene monitorato tramite i set di dati o le distribuzioni. Per il monitoraggio sfasamento di dati, viene specificato un set di dati della linea di base - generalmente il training set di dati per un modello. Un secondo set di dati - in genere i dati di input modello raccolti da una distribuzione - è testata con il set di dati della linea di base. Entrambi i set di dati [profilate](how-to-explore-prepare-data.md#explore-with-summary-statistics) e servizio di monitoraggio dello sfasamento di input per i dati. Viene eseguito il training di un modello di machine learning per rilevare le differenze tra due set di dati. Le prestazioni del modello viene convertita nel coefficiente di deviazione, che misura la grandezza di deviazione tra due set di dati. Usando [interpretazione del modello](machine-learning-interpretability-explainability.md) vengono calcolate le funzionalità che hanno contribuito al coefficiente di deviazione. Dal profilo del set di dati, vengano tenuta traccia informazioni statistiche su ognuna delle funzionalità. 

## <a name="data-drift-metric-output"></a>Output lo sfasamento dei dati di metrica

Esistono diversi modi per visualizzare le metriche di deviazione:

* Usare il widget di Jupyter.
* Usare la `get_metrics()` funzione in qualsiasi `datadriftRun` oggetto.
* Visualizzare le metriche nel portale di Azure nel modello

In ogni iterazione di esecuzione per un'attività di sfasamento di dati vengono salvate le metriche seguenti:

|Metrica|Descrizione|
--|--|
wasserstein_distance|Distanza statistica definita per la distribuzione numerica unidimensionale.|
energy_distance|Distanza statistica definita per la distribuzione numerica unidimensionale.|
datadrift_coefficient|Formalmente Matthews coefficiente di correlazione, un numero reale compreso tra -1 e 1. Nel contesto di deviazione, 0 non indica nessun orientamenti e 1 indica che lo sfasamento massimo.|
datadrift_contribution|Importanza delle caratteristiche delle funzionalità contributo dello sfasamento.|

## <a name="next-steps"></a>Passaggi successivi

Vedere gli esempi e informazioni su come monitorare la deviazione della data:

+ [Informazioni su come monitorare lo sfasamento di dati in modelli distribuiti tramite Azure Kubernetes Service (AKS)](how-to-monitor-data-drift.md)
+ Provare [esempi di Notebook di Jupyter](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/data-drift/)