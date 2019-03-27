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
ms.openlocfilehash: 961983aad0775f9b9d728269e8a57137ff508f02
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57451791"
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

<a name="automated-ml-setup"></a>

## <a name="automated-machine-learning-setup"></a>Configurazione automatizzata di Machine Learning 

_Questi passaggi si applicano solo ai notebook nella cartella **how-to-use-azureml/automated-machine-learning**._

Oltre a usare una qualsiasi delle opzioni descritte sopra, è anche possibile installare l'ambiente e creare un'area di lavoro allo stesso tempo seguendo le istruzioni seguenti. 

1. Installare [Mini-conda](https://conda.io/miniconda.html). Scegliere la versione 3.7 o una versione successiva. Seguire le istruzioni di installazione. 
   >[!NOTE]
   >È possibile usare un conda esistente purché nella versione 4.4.10 o successiva. Usare `conda -V` per visualizzare la versione. È possibile aggiornare una versione di conda con il comando `conda update conda`. Non è necessario installare specificamente mini-conda.

1. Scaricare i notebook da [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning
) in formato ZIP ed estrarre il contenuto in una directory locale. I notebook per l'apprendimento automatico si trovano nella cartella `how-to-use-azureml/automated-machine-learning`.

1. Configurare un nuovo ambiente Conda. 
   1. Aprire un prompt dei comandi di Conda nel computer locale.
   
   1. Passare ai file estratti nel computer locale.
   
   1. Aprire la cartella **automated-machine-learning**.
   
   1. Eseguire `automl_setup.cmd` nella finestra del prompt dei comandi di Conda per Windows o il file `.sh` relativo al sistema operativo in uso. L'esecuzione può richiedere circa 10 minuti.

      Lo script di configurazione:
      + Crea un nuovo ambiente Conda
      + Installa i pacchetti necessari
      + Configura il widget
      + Avvia un notebook di Jupyter
      
   >[!NOTE]
   > Lo script accetta il nome dell'ambiente Conda come parametro facoltativo. Il nome dell'ambiente Conda predefinito è `azure_automl`. Il comando esatto dipende dal sistema operativo. Ciò è utile se si crea un nuovo ambiente o si esegue l’aggiornamento a una nuova versione. Ad esempio è possibile usare "automl_setup.cmd azure_automl_sandbox" per creare un nome ambiente azure_automl_sandbox. 
      
1. Dopo il completamento dello script, nel browser verrà aperta la home page di un notebook Jupyter.

1. Passare al percorso in cui sono stati salvati i notebook. 

1. Aprire la cartella automated-machine-learning, quindi aprire il notebook **configuration.ipynb**. 

1. Eseguire le celle nel notebook per registrare il provider di risorse di Machine Learning Services e creare un'area di lavoro.

A questo punto è possibile aprire ed eseguire i notebook salvati nel computer locale.


## <a name="next-steps"></a>Passaggi successivi

Esplorare il [repository dei notebook GitHub per il servizio Azure Machine Learning](https://aka.ms/aml-notebooks)

Provare queste esercitazioni:
+ [Eseguire il training di un modello di classificazione delle immagini con il servizio Azure Machine Learning](tutorial-train-models-with-aml.md)

+ [Preparare i dati e usare l'apprendimento automatico per eseguire il training di un modello di regressione con il set di dati relativo ai taxi di New York](tutorial-data-prep.md)
