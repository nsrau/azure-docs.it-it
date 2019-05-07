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
ms.openlocfilehash: cd88fd85ce6d18287c700a54e42b6237a42ea5c9
ms.sourcegitcommit: eea74d11a6d6ea6d187e90e368e70e46b76cd2aa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/03/2019
ms.locfileid: "65035370"
---
# <a name="use-jupyter-notebooks-to-explore-azure-machine-learning-service"></a>Usare i notebook di Jupyter per esplorare il servizio Azure Machine Learning

Per praticità abbiamo sviluppato una serie di notebook Jupyter Python da usare per esplorare il servizio Azure Machine Learning. 

Questo articolo spiega come usare il servizio con la documentazione disponibile su questo sito e come personalizzare i notebook in base alla propria situazione. 

Usare uno dei percorsi seguenti per eseguire un server notebook con questi esempi di notebook.  Quando il server è in esecuzione, cercare i notebook dell'esercitazione nella cartella **esercitazioni** oppure esplorare le differenti funzionalità in nella cartella **how-to-use-azureml**.

## <a name="a-managed-cloud-notebook-server"></a>Un server notebook cloud gestito

Iniziare a usare un server notebook basato sul cloud è semplice. Al termine della creazione di questa risorsa cloud, il notebook di esempio e [Azure Machine Learning SDK per Python](https://aka.ms/aml-sdk) sono già installati e configurati.  

[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]

* Gli esempi sono disponibili nella pagina Web del notebook.

## <a name="a-data-science-virtual-machine-dsvm"></a>Data Science Virtual Machine (DSVM)

[Azure Machine Learning SDK per Python](https://aka.ms/aml-sdk) e il server notebook sono già installati e configurati in un ambiente DSVM. 

Dopo aver [creato una DSVM](how-to-configure-environment.md#dsvm), seguire questa procedura nella DSVM per eseguire i notebook.

[!INCLUDE [aml-dsvm-server](../../../includes/aml-dsvm-server.md)]

## <a name="your-own-jupyter-notebook-server"></a>Server Jupyter Notebook personale

Usare questa proceduta per creare un server notebook Jupyter locale nel computer in uso.

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

Le istruzioni di installazione descrivono come installare i pacchetti necessari per eseguire i notebook della guida di avvio rapido e dell'esercitazione.  Per gli altri notebook di esempio, può essere necessario installare altri componenti.  Per altre informazioni su questi componenti, vedere [Install the Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/install) (Installare Azure Machine Learning SDK per Python).

## <a name="azure-notebooks"></a>Notebook di Azure

I notebook di esempio e [Azure Machine Learning SDK per Python](https://aka.ms/aml-sdk) sono già installati e configurati in [Azure Notebooks](https://notebooks.azure.com/). L'installazione e gli aggiornamenti futuri vengono gestiti automaticamente tramite i servizi di Azure.

Per iniziare a usare Azure Notebooks, usare il [portale di Azure](https://portal.azure.com).  Aprire l'area di lavoro e nella sezione **Panoramica** selezionare **Inizia a usare Azure Notebooks**.

## <a name="next-steps"></a>Passaggi successivi

+ Esplorare i notebook di esempio per il servizio Azure Machine Learning in questo repository GitHub: https://aka.ms/aml-notebooks

Provare queste esercitazioni:
+ [Eseguire il training di un modello di classificazione delle immagini con il servizio Azure Machine Learning](tutorial-train-models-with-aml.md)

+ [Preparare i dati e usare l'apprendimento automatico per eseguire il training di un modello di regressione con il set di dati relativo ai taxi di New York](tutorial-data-prep.md)