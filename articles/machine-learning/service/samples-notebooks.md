---
title: Notebook di Jupyter di esempio
titleSuffix: Azure Machine Learning service
description: Trovare e usare notebook di Jupyter di esempio per esplorare l'SDK per Python del servizio Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 05/29/2019
ms.custom: seodec18
ms.openlocfilehash: ea4d5a807c25ea0406b49dac8a83ef1a34e0e8b3
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66391759"
---
# <a name="use-jupyter-notebooks-to-explore-azure-machine-learning-service"></a>Usare i notebook di Jupyter per esplorare il servizio Azure Machine Learning

Il [repository di notebook di Azure Machine Learning](https://github.com/azure/machinelearningnotebooks) include gli esempi più recenti di Azure Machine Learning SDK per Python. Questi notebook di Juypter sono progettati per facilitare l'esplorazione dell'SDK e fungono da modelli per progetti di Machine Learning personalizzati.

Questo articolo illustra come accedere al repository dagli ambienti seguenti:

- [VM per notebook di Azure Machine Learning](#azure-machine-learning-notebook-vm)
- [Server notebook personale](#bring-your-own-jupyter-notebook-server)
- [Macchina virtuale di data science](#data-science-virtual-machine)
- [Azure Notebooks](#azure-notebooks)

> [!NOTE]
> Al termine della clonazione del repository, i notebook dell'esercitazione si troveranno nella cartella **tutorials** e i notebook specifici delle diverse funzionalità saranno disponibili nella cartella **how-to-use-azureml**.

## <a name="azure-machine-learning-notebook-vm"></a>VM per notebook di Azure Machine Learning

Il modo più semplice per iniziare a usare gli esempi consiste nel completare la [guida di avvio rapido per i notebook basati sul cloud](quickstart-run-cloud-notebook.md). Al termine, si avrà un server notebook dedicato in cui saranno precaricati l'SDK e il repository di esempio. Non saranno necessari download o installazioni.

## <a name="bring-your-own-jupyter-notebook-server"></a>Server Jupyter Notebook personale

Se si preferisce usare un server notebook personale per lo sviluppo locale, seguire questa procedura:

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

Queste istruzioni consentono di installare i pacchetti SDK di base necessari per i notebook della guida di avvio rapido e dell'esercitazione. Altri notebook di esempio potrebbero richiedere l'installazione di ulteriori componenti. Per altre informazioni, vedere [Installare Azure Machine Learning SDK per Python](https://docs.microsoft.com/python/api/overview/azure/ml/install).

## <a name="data-science-virtual-machine"></a>Macchina virtuale di data science

Una Data Science Virtual Machine (DSVM) è un'immagine di VM personalizzata creata specificamente per data science. Se si [crea una DSVM](how-to-configure-environment.md#dsvm), il server notebook e l'SDK vengono installati e configurati automaticamente. È tuttavia necessario creare un'area di lavoro e clonare il repository di esempio.

[!INCLUDE [aml-dsvm-server](../../../includes/aml-dsvm-server.md)]

## <a name="azure-notebooks"></a>Notebook di Azure

In [Azure Notebooks](https://notebooks.azure.com/) il server notebook e l'SDK vengono installati e configurati automaticamente. Azure Notebooks offre un ambiente notebook leggero e completamente gestito da esplorare.

Per accedere al repository di esempio in Azure Notebooks, passare all'area di lavoro di Azure Machine Learning tramite il [portale di Azure](https://portal.azure.com). Nella sezione **Panoramica** selezionare **Inizia a usare Azure Notebooks**.

## <a name="next-steps"></a>Passaggi successivi

Esplorare i [notebook di esempio](https://aka.ms/aml-notebooks) per scoprire le funzionalità offerte dal servizio Azure Machine Learning oppure provare queste esercitazioni:

- [Eseguire il training di un modello di classificazione delle immagini con il servizio Azure Machine Learning](tutorial-train-models-with-aml.md)

- [Preparare i dati e usare l'apprendimento automatico per eseguire il training di un modello di regressione con il set di dati relativo ai taxi di New York](tutorial-data-prep.md)