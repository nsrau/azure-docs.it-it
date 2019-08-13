---
title: "Esercitazione: Configurare l'ambiente e l'area di lavoro"
titleSuffix: Azure Machine Learning service
description: In questa serie di esercitazioni si completeranno le procedure end-to-end per iniziare a usare Azure Machine Learning SDK per Python eseguito in Jupyter Notebook.  La prima parte illustra la creazione di un ambiente server notebook cloud, nonché la creazione di un'area di lavoro per gestire gli esperimenti e i modelli di Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 07/20/2019
ms.openlocfilehash: b3fdcfbb9869c0199c91f5bbc6ca1f02fec996cf
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68828641"
---
# <a name="tutorial-setup-environment-and-workspace"></a>Esercitazione: Configurare l'ambiente e l'area di lavoro

In questa esercitazione si completeranno le procedure end-to-end per iniziare a usare Azure Machine Learning SDK per Python eseguito in Jupyter Notebook. Questa esercitazione, la **prima di una serie di esercitazioni in due parti**, illustra l'installazione e la configurazione dell'ambiente Python, nonché la creazione di un'area di lavoro per gestire gli esperimenti e i modelli di Machine Learning. Su questa base, nella [**seconda parte**](tutorial-1st-experiment-sdk-train.md) viene eseguito il training di più modelli di Machine Learning e viene presentato il processo di gestione dei modelli sia con il portale di Azure che con l'SDK.

In questa esercitazione:

> [!div class="checklist"]
> * Creare un'area di lavoro di Machine Learning da usare nell'esercitazione successiva.
> * Creare una macchina virtuale per notebook, un server Jupyter Notebook basato sul cloud con Azure Machine Learning SDK per Python preinstallato e preconfigurato.

## <a name="prerequisites"></a>Prerequisiti

L'unico prerequisito per questa esercitazione è una sottoscrizione di Azure. Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Accedere alla [versione gratuita o a pagamento del servizio Azure Machine Learning](https://aka.ms/AMLFree).

## <a name="create-a-workspace"></a>Creare un'area di lavoro

Un'area di lavoro è una risorsa cloud fondamentale usata per eseguire gli esperimenti, il training e la distribuzione di modelli di Machine Learning. Collega la sottoscrizione e il gruppo di risorse di Azure a un oggetto di facile utilizzo nell'SDK. Se è già disponibile un'area di lavoro del servizio Azure Machine Learning, passare alla [sezione successiva](#azure). Altrimenti crearne una adesso.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="azure"></a>Creare un server notebook cloud

Questo esempio usa il server notebook cloud nell'area di lavoro per un'esperienza preconfigurata senza installazioni. Se si preferisce avere il controllo sull'ambiente, sui pacchetti e sulle dipendenze, usare il [proprio ambiente](how-to-configure-environment.md#local).

Nell'area di lavoro viene creata una risorsa cloud per iniziare a usare i notebook di Jupyter. Questa risorsa è una macchina virtuale Linux basata sul cloud preconfigurata con tutto quanto necessario per eseguire il servizio Azure Machine Learning.

1. Aprire l'area di lavoro nel [portale di Azure](https://portal.azure.com/).  Se non si sa come individuare l'area di lavoro nel portale, vedere l'articolo su [come trovare l'area di lavoro](how-to-manage-workspace.md#view).

1. Nella pagina dell'area di lavoro nel portale di Azure, selezionare **Macchine virtuali per notebook** a sinistra.

1. Selezionare **+Nuova** per creare una nuova macchina virtuale per notebook.

     ![Selezionare Nuova macchina virtuale](./media/tutorial-1st-experiment-sdk-setup/add-workstation.png)

1. Specificare un nome per la macchina virtuale. Selezionare quindi **Crea**.

    > [!NOTE]
    > La lunghezza del nome della macchina virtuale per notebook deve essere compresa tra 2 e 16 caratteri. I caratteri validi sono lettere, cifre e il carattere -.  Il nome della deve inoltre essere univoco nell'intera sottoscrizione di Azure.

1. Attendere finché lo stato non diventa **In esecuzione**.

### <a name="launch-jupyter-web-interface"></a>Avviare l'interfaccia Web di Jupyter

Dopo che la macchina virtuale è in esecuzione, usare la sezione **Macchine virtuali per notebook** per avviare l'interfaccia Web di Jupyter.

1. Selezionare **Jupyter** nella colonna **URI** relativa alla macchina virtuale.

    ![Avviare il server Jupyter Notebook](./media/tutorial-1st-experiment-sdk-setup/start-server.png)

    Il collegamento consente di avviare il server del notebook e di aprire la pagina Web del notebook Jupyter in una nuova scheda del browser.  Questo collegamento funziona solo per l'utente che crea la macchina virtuale. Ogni utente dell'area di lavoro deve creare la propria macchina virtuale.

1. Nella pagina Web del notebook di Jupyter, il nomecartella nella parte superiore è il nome utente.  Selezionare questa cartella.

    > [!TIP]
    > Questa cartella si trova nel [contenitore di archiviazione](concept-workspace.md#resources) nell'area di lavoro anziché sulla macchina virtuale per notebook stessa.  È possibile eliminare la macchina virtuale per notebook e mantenere comunque tutto il lavoro.  Quando in un secondo momento si creerà una nuova macchina virtuale per notebook, verrà caricata la stessa cartella. Se si condivide l'area di lavoro con altri utenti, questi potranno visualizzare la cartella e si potrà visualizzare i rispettivi elementi.

1. Aprire la sottodirectory `samples-*` e quindi `tutorials/tutorial-1st-experiment-sdk-train.ipynb` per eseguire la **seconda parte** dell'esercitazione.

## <a name="end"></a> Eseguire la pulizia delle risorse

Se si intende continuare con la **seconda parte** dell'esercitazione, non completare questa sezione.

### <a name="stop-the-notebook-vm"></a>Arrestare la macchina virtuale per notebook

Se si è usato un server notebook cloud, per ridurre i costi arrestare la macchina virtuale per notebook quando non viene usata.

1. Nell'area di lavoro selezionare **Macchine virtuali per notebook**.

   ![Arrestare il server di macchine virtuali](./media/tutorial-1st-experiment-sdk-setup/stop-server.png)

1. Selezionare la macchina virtuale dall'elenco.

1. Selezionare **Arresta**.

1. Quando si è pronti a usare di nuovo il server, selezionare **Avvia**.

### <a name="delete-everything"></a>Eliminare tutto

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

È anche possibile mantenere il gruppo di risorse ma eliminare una singola area di lavoro. Visualizzare le proprietà dell'area di lavoro e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state completate le attività seguenti:

* Creazione di un'area di lavoro del servizio Azure Machine Learning
* Creazione e configurazione di un server notebook cloud nell'area di lavoro

Continuare con la **seconda parte** di questa esercitazione per eseguire il training di un semplice modello di Machine Learning.

> [!div class="nextstepaction"]
> [Esercitazione: Eseguire il training del primo modello](tutorial-1st-experiment-sdk-train.md)
