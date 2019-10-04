---
title: Notebook di Jupyter di esempio
titleSuffix: Azure Machine Learning
description: Trovare e usare notebook Jupyter di esempio per esplorare Azure Machine Learning SDK per Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 07/31/2019
ms.custom: seodec18
ms.openlocfilehash: dd93e4e621a4fd474283f788e2cf67b305a1be3d
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2019
ms.locfileid: "70997073"
---
# <a name="explore-azure-machine-learning-with-jupyter-notebooks"></a>Esplorare Azure Machine Learning con notebook Jupyter

Il [repository di notebook di esempio di Azure Machine Learning](https://github.com/azure/machinelearningnotebooks) include gli esempi più recenti per Azure Machine Learning SDK per Python. Questi notebook di Juypter sono progettati per facilitare l'esplorazione dell'SDK e fungono da modelli per progetti di Machine Learning personalizzati.

Questo articolo illustra come accedere al repository dagli ambienti seguenti:

- [VM per notebook di Azure Machine Learning](#notebookvm)
- [Server notebook personale](#byo)
- [Macchina virtuale di data science](#dsvm)

> [!NOTE]
> Al termine della clonazione del repository, i notebook dell'esercitazione si troveranno nella cartella **tutorials** e i notebook specifici delle diverse funzionalità saranno disponibili nella cartella **how-to-use-azureml**.

<a name="notebookvm"></a>
## <a name="get-samples-on-azure-machine-learning-notebook-vm"></a>Ottenere esempi sulla macchina virtuale per notebook di Azure Machine Learning

Il modo più semplice per iniziare a usare gli esempi consiste nel completare [Esercitazione: Configurare l'ambiente e l'area di lavoro](tutorial-1st-experiment-sdk-setup.md). Al termine, si avrà un server notebook dedicato in cui saranno precaricati l'SDK e il repository di esempio. Non saranno necessari download o installazioni.

<a name="byo"></a>

## <a name="get-samples-on-your-notebook-server"></a>Ottenere esempi sul server notebook

Se si preferisce usare un server notebook personale per lo sviluppo locale, seguire questa procedura:

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

Queste istruzioni consentono di installare i pacchetti SDK di base necessari per i notebook della guida di avvio rapido e dell'esercitazione. Altri notebook di esempio potrebbero richiedere l'installazione di ulteriori componenti. Per altre informazioni, vedere [Installare Azure Machine Learning SDK per Python](https://docs.microsoft.com/python/api/overview/azure/ml/install).

<a name="dsvm"></a>
## <a name="get-samples-on-dsvm"></a>Ottenere esempi su DSVM

Una Data Science Virtual Machine (DSVM) è un'immagine di VM personalizzata creata specificamente per data science. Se si [crea una DSVM](how-to-configure-environment.md#dsvm), il server notebook e l'SDK vengono installati e configurati automaticamente. È tuttavia necessario creare un'area di lavoro e clonare il repository di esempio.

[!INCLUDE [aml-dsvm-server](../../../includes/aml-dsvm-server.md)]

## <a name="next-steps"></a>Passaggi successivi

Esplorare i [notebook di esempio](https://aka.ms/aml-notebooks) per scoprire le funzionalità offerte da Azure Machine Learning oppure provare queste esercitazioni:

- [Eseguire il training di un modello di classificazione delle immagini con il servizio Azure Machine Learning](tutorial-train-models-with-aml.md)

- [Preparare i dati e usare l'apprendimento automatico per eseguire il training di un modello di regressione con il set di dati relativo ai taxi di New York](tutorial-auto-train-models.md)
