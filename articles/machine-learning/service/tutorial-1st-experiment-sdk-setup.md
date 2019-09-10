---
title: 'Esercitazione: Creare il primo esperimento di Machine Learning: Configurazione'
titleSuffix: Azure Machine Learning service
description: In questa serie di esercitazioni si completeranno le procedure end-to-end per iniziare a usare Azure Machine Learning SDK per Python eseguito in Jupyter Notebook.  La prima parte illustra la creazione di un ambiente server notebook cloud, nonché la creazione di un'area di lavoro per gestire gli esperimenti e i modelli di Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 08/28/2019
ms.openlocfilehash: f1003324e9f4b3762b5d8eca703af4a1fbd4613a
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70308808"
---
# <a name="tutorial-get-started-creating-your-first-ml-experiment-with-the-python-sdk"></a>Esercitazione: Introduzione alla creazione del primo esperimento di Machine Learning con Python SDK

In questa esercitazione si completeranno le procedure end-to-end per iniziare a usare Azure Machine Learning SDK per Python eseguito in Jupyter Notebook. Questa esercitazione, la **prima di una serie di esercitazioni in due parti**, illustra l'installazione e la configurazione dell'ambiente Python, nonché la creazione di un'area di lavoro per gestire gli esperimenti e i modelli di Machine Learning. Su questa base, nella [**seconda parte**](tutorial-1st-experiment-sdk-train.md) viene eseguito il training di più modelli di Machine Learning e viene presentato il processo di gestione dei modelli sia con il portale di Azure che con l'SDK.

In questa esercitazione:

> [!div class="checklist"]
> * Creare un'[area di lavoro di Azure Machine Learning](concept-workspace.md) da usare nell'esercitazione successiva.
> * Creare una macchina virtuale Jupyter Notebook basata sul cloud con Azure Machine Learning SDK per Python installato e preconfigurato.

Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Accedere alla [versione gratuita o a pagamento del servizio Azure Machine Learning](https://aka.ms/AMLFree).

## <a name="create-a-workspace"></a>Creare un'area di lavoro

Un'area di lavoro di Machine Learning è una risorsa cloud fondamentale usata per eseguire gli esperimenti, il training e la distribuzione di modelli di Machine Learning. Collega la sottoscrizione e il gruppo di risorse di Azure a un oggetto di facile utilizzo nell'SDK. Se è già disponibile un'area di lavoro del servizio Azure Machine Learning, passare alla [sezione successiva](#azure). Altrimenti crearne una adesso.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="azure"></a>Creare un server notebook cloud

Questo esempio usa il server notebook cloud nell'area di lavoro per un'esperienza preconfigurata senza installazioni. Se si preferisce avere il controllo sull'ambiente, sui pacchetti e sulle dipendenze, usare il [proprio ambiente](how-to-configure-environment.md#local).

Nell'area di lavoro viene creata una risorsa cloud per iniziare a usare i notebook di Jupyter. Questa risorsa è una macchina virtuale Linux basata sul cloud preconfigurata con tutto quanto necessario per eseguire il servizio Azure Machine Learning.

1. Aprire l'area di lavoro nel [portale di Azure](https://portal.azure.com/).  Se non si sa come individuare l'area di lavoro nel portale, vedere l'articolo su [come trovare l'area di lavoro](how-to-manage-workspace.md#view).

1. Nella pagina dell'area di lavoro nel portale di Azure, selezionare **Macchine virtuali per notebook** a sinistra.

1. Selezionare **+Nuova** per creare una nuova macchina virtuale per notebook.

     ![Selezionare Nuova macchina virtuale](./media/tutorial-1st-experiment-sdk-setup/add-workstation.png)

1. Specificare un nome per la macchina virtuale. 
   + La lunghezza del nome della macchina virtuale per notebook deve essere compresa tra 2 e 16 caratteri. I caratteri validi sono lettere, cifre e il carattere -.  
   + Il nome della deve inoltre essere univoco nell'intera sottoscrizione di Azure.

1. Selezionare quindi **Crea**. La configurazione della macchina virtuale può richiedere qualche secondo.

1. Attendere finché lo stato non diventa **In esecuzione**.
   Dopo che la macchina virtuale è in esecuzione, usare la sezione **Macchine virtuali per notebook** per avviare l'interfaccia Web di Jupyter.

1. Selezionare **Jupyter** nella colonna **URI** relativa alla macchina virtuale.

    ![Avviare il server Jupyter Notebook](./media/tutorial-1st-experiment-sdk-setup/start-server.png)

   Il collegamento consente di avviare il server del notebook e di aprire la pagina Web del notebook Jupyter in una nuova scheda del browser.  Questo collegamento funziona solo per l'utente che crea la macchina virtuale. Ogni utente dell'area di lavoro deve creare la propria macchina virtuale.


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state completate le attività seguenti:

* Creazione di un'area di lavoro del servizio Azure Machine Learning
* Creazione e configurazione di un server notebook cloud nell'area di lavoro

Nella **seconda parte** dell'esercitazione viene eseguito il codice in `tutorial-1st-experiment-sdk-train.ipynb` per il training di un modello di Machine Learning. 

> [!div class="nextstepaction"]
> [Esercitazione: Eseguire il training del primo modello](tutorial-1st-experiment-sdk-train.md)

> [!IMPORTANT]
> Se non si prevede di seguire la parte 2 di questa esercitazione oppure altre esercitazioni, è consigliabile [arrestare la VM del server notebook cloud](tutorial-1st-experiment-sdk-train.md#clean-up-resources) quando non viene usata per ridurre i costi.
