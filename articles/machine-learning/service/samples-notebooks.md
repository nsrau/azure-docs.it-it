---
title: Esercitazioni sul servizio Azure Machine Learning in notebook di Jupyter
description: Come trovare e usare notebook di Jupyter di esempio per esplorare il servizio Azure Machine Learning in Python.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 12/04/2018
ms.openlocfilehash: 5ec010d6e0539e9ba316b48dc02110dc19e4b13e
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52883804"
---
# <a name="use-jupyter-notebooks-to-explore-azure-machine-learning-service"></a>Usare i notebook di Jupyter per esplorare il servizio Azure Machine Learning


Per praticità abbiamo sviluppato una serie di notebook Jupyter Python da usare per esplorare il servizio Azure Machine Learning. 

Questo articolo spiega come usare il servizio con la documentazione disponibile su questo sito e come personalizzare i notebook in base alla propria situazione. 

## <a name="prerequisite"></a>Prerequisito

Completare la [Guida introduttiva: Iniziare a usare Azure Machine Learning con il portale di Azure](quickstart-get-started.md) per creare un'area di lavoro e avviare Azure Notebooks.

## <a name="try-azure-notebooks-free-jupyter-notebooks-in-the-cloud"></a>Provare Azure Notebooks: notebook di Jupyter gratuiti nel cloud

Iniziare a usare Azure Notebooks è molto semplice. [Azure Machine Learning SDK per Python](https://aka.ms/aml-sdk) è già installato e configurato in Azure Notebooks. L'installazione e gli aggiornamenti futuri vengono gestiti automaticamente tramite i servizi di Azure.
  
+ Per eseguire i **notebook di esercitazione di base**:
  1. Passare ad [Azure Notebooks](https://notebooks.azure.com/).
    
  1. Trovare la cartella **tutorials** nella libreria **Getting Started** creata durante l'avvio rapido relativo ai prerequisiti.
    
  1. Aprire il notebook che si vuole eseguire.
    
+ Per eseguire **altri notebook**:

  1. [Importare i notebook di esempio](https://aka.ms/aml-clone-azure-notebooks) in Azure Notebooks.

  1. Aggiungere un file di configurazione dell'area di lavoro alla libreria usando uno di questi metodi:
     + Copiare il file **config.json** dalla libreria **Getting Started** nella nuova libreria clonata.

     + Creare una nuova area di lavoro usando il codice incluso in [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb).
    
  1. Aprire il notebook che si vuole eseguire.     


## <a name="use-a-data-science-virtual-machine-dsvm"></a>Usare un ambiente Data Science Virtual Machine (DSVM)

[Azure Machine Learning SDK per Python](https://aka.ms/aml-sdk) e il server notebook sono già installati e configurati in un ambiente DSVM. Usare questa procedura per eseguire i notebook.

1. [Creare un DSVM](how-to-configure-environment.md#dsvm).

1. Clonare il [repository GitHub](https://aka.ms/aml-notebooks).

1. Aggiungere un file di configurazione dell'area di lavoro alla libreria usando uno di questi metodi:
    * Copiare il file **aml_config\config.json** creato usando l'avvio rapido relativo ai prerequisiti nella directory clonata.

    * Creare una nuova area di lavoro usando il codice incluso in [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb).

1. Avviare il server notebook dalla directory clonata.

## <a name="use-your-own-jupyter-notebook-server"></a>Usare il server notebook Jupyter personale

Usare questa proceduta per creare un server notebook Jupyter locale nel computer in uso.

1. Assicurarsi di aver completato l'avvio rapido relativo ai prerequisiti, in cui sono stati installati gli Azure Machine Learning SDK.

1. Clonare il [repository GitHub](https://aka.ms/aml-notebooks).

1. Aggiungere un file di configurazione dell'area di lavoro alla libreria usando uno di questi metodi:
    * Copiare il file **aml_config\config.json** creato usando l'avvio rapido relativo ai prerequisiti nella directory clonata.
    
    * Creare una nuova area di lavoro usando il codice incluso in [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb).

1. Avviare il server notebook dalla directory clonata.

1. Passare alla cartella che contiene il notebook.

1. Aprire il notebook.

<a name="auto"></a>

## <a name="automated-ml-setup"></a>Configurazione automatizzata di ML 

**Questa procedura è valida solo per i notebook inclusi nella cartella `automated-machine-learning`.**

Oltre a usare una qualsiasi delle opzioni descritte sopra, è anche possibile installare l'ambiente e creare un'area di lavoro allo stesso tempo seguendo le istruzioni seguenti. 

1. Installare [Mini-conda](https://conda.io/miniconda.html). Scegliere la versione 3.7 o una versione successiva. Seguire le istruzioni di installazione. 
   >[!NOTE]
   >È possibile usare un conda esistente purché nella versione 4.4.10 o successiva. Usare `conda -V` per visualizzare la versione. È possibile aggiornare una versione di conda con il comando `conda update conda`. Non è necessario installare specificamente mini-conda.

1. Scaricare i notebook da [Github](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning
) in formato ZIP ed estrarre il contenuto in una directory locale. I notebook per l'apprendimento automatico si trovano nella cartella `how-to-use-azureml/automated-machine-learning`.

1. Configurare un nuovo ambiente Conda. 
   1. Aprire un prompt dei comandi di Conda nel computer locale.
   
   1. Passare ai file estratti nel computer locale.
   
   1. Aprire la cartella `automated-machine-learning`.
   
   1. Eseguire `automl_setup.cmd` nella finestra del prompt dei comandi di Conda per Windows o il file `.sh` relativo al sistema operativo in uso. L'esecuzione può richiedere circa 10 minuti.

      Lo script di configurazione:
      + Crea un nuovo ambiente Conda
      + Installa i pacchetti necessari
      + Configura il widget
      + Avvia un notebook di Jupyter
      
      Lo script accetta il nome dell'ambiente Conda come parametro facoltativo. Il nome dell'ambiente Conda predefinito è `azure_automl`. Il comando esatto dipende dal sistema operativo. 
      
      Dopo il completamento dello script, nel browser verrà aperta la home page di un notebook Jupyter.

1. Passare al percorso in cui sono stati salvati i notebook. 

1. Aprire la cartella automated-machine-learning, quindi aprire il notebook `configuration.ipynb`. 

1. Eseguire le celle nel notebook per registrare il provider di risorse di Machine Learning Services e creare un'area di lavoro.

A questo punto è possibile aprire ed eseguire i notebook salvati nel computer locale.


## <a name="next-steps"></a>Passaggi successivi

Esplorare il [repository dei notebook GitHub per il servizio Azure Machine Learning](https://aka.ms/aml-notebooks)

Provare queste esercitazioni:
+ [Eseguire il training di un modello di classificazione delle immagini con il servizio Azure Machine Learning](tutorial-train-models-with-aml.md)

+ [Preparare i dati e usare l'apprendimento automatico per eseguire il training di un modello di regressione con il set di dati relativo ai taxi di New York](tutorial-data-prep.md)
