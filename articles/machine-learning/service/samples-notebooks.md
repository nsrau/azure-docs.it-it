---
title: Notebook di Jupyter di esempio
titleSuffix: Azure Machine Learning service
description: Come trovare e usare notebook di Jupyter di esempio per esplorare il servizio Azure Machine Learning in Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: b597d6efa87aa2811ce42f3315698acfa17426b2
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2019
ms.locfileid: "59548589"
---
# <a name="use-jupyter-notebooks-to-explore-azure-machine-learning-service"></a>Usare i notebook di Jupyter per esplorare il servizio Azure Machine Learning

Per praticità abbiamo sviluppato una serie di notebook Jupyter Python da usare per esplorare il servizio Azure Machine Learning. 

Questo articolo spiega come usare il servizio con la documentazione disponibile su questo sito e come personalizzare i notebook in base alla propria situazione. 

Usare uno dei percorsi seguenti per eseguire un server notebook con questi esempi di notebook.  Quando il server è in esecuzione, cercare i notebook dell'esercitazione nella cartella **esercitazioni** oppure esplorare le differenti funzionalità in nella cartella **how-to-use-azureml**.


## <a name="try-azure-notebooks-free-jupyter-notebooks-in-the-cloud"></a>Provare Azure Notebooks: notebook di Jupyter gratuiti nel cloud

Iniziare a usare Azure Notebooks è molto semplice. [Azure Machine Learning SDK per Python](https://aka.ms/aml-sdk) è già installato e configurato in [Azure Notebooks](https://notebooks.azure.com/). L'installazione e gli aggiornamenti futuri vengono gestiti automaticamente tramite i servizi di Azure.
  
[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]


## <a name="use-a-data-science-virtual-machine-dsvm"></a>Usare un ambiente Data Science Virtual Machine (DSVM)

[Azure Machine Learning SDK per Python](https://aka.ms/aml-sdk) e il server notebook sono già installati e configurati in un ambiente DSVM. 

Dopo aver [creato una DSVM](how-to-configure-environment.md#dsvm), seguire questa procedura nella DSVM per eseguire i notebook.

[!INCLUDE [aml-dsvm-server](../../../includes/aml-dsvm-server.md)]


## <a name="use-your-own-jupyter-notebook-server"></a>Usare il server notebook Jupyter personale

Usare questa proceduta per creare un server notebook Jupyter locale nel computer in uso.

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

Le istruzioni di questa guida introduttiva descrivono come installare i pacchetti necessari per eseguire i notebook della guida introduttiva e dell'esercitazione.  Per gli altri notebook di esempio, può essere necessario installare altri componenti.  Per altre informazioni su questi componenti, vedere [Install the Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/install) (Installare Azure Machine Learning SDK per Python).

## <a name="next-steps"></a>Passaggi successivi

Esplorare il [repository dei notebook GitHub per il servizio Azure Machine Learning](https://aka.ms/aml-notebooks)

Provare queste esercitazioni:
+ [Eseguire il training di un modello di classificazione delle immagini con il servizio Azure Machine Learning](tutorial-train-models-with-aml.md)

+ [Preparare i dati e usare l'apprendimento automatico per eseguire il training di un modello di regressione con il set di dati relativo ai taxi di New York](tutorial-data-prep.md)
