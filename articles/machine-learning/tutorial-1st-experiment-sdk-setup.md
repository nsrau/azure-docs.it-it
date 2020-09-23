---
title: 'Esercitazione: Introduzione ai notebook di Jupyter (Python)'
titleSuffix: Azure Machine Learning
description: Configurazione per le esercitazioni sui notebook di Jupyter.  Creare un'area di lavoro di Azure Machine Learning, clonare i notebook di Jupyter nell'area di lavoro e creare un'istanza di ambiente di calcolo in cui eseguire i notebook.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 02/10/2020
ms.custom: devx-track-python
ms.openlocfilehash: de52013628f5d02bedcf72a99e0fad25cabe5d8f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90896884"
---
# <a name="tutorial-get-started-with-azure-machine-learning-in-jupyter-notebooks"></a>Esercitazione: Introduzione al servizio Azure Machine Learning nei notebook di Jupyter

In questa esercitazione si completa la procedura per iniziare a usare Azure Machine Learning con i notebook di Jupyter in una [workstation gestita basata sul cloud (istanza di ambiente di calcolo)](concept-compute-instance.md). Questa esercitazione è propedeutica a tutte le altre esercitazioni su Jupyter Notebook.

In questa esercitazione:

> [!div class="checklist"]
> * Creare un'[area di lavoro di Azure Machine Learning](concept-workspace.md) da usare nelle altre esercitazioni su Jupyter Notebook.
> * Clonare il notebook delle esercitazioni nella cartella nell'area di lavoro.
> * Creare un'istanza di calcolo basata sul cloud con Azure Machine Learning Python SDK installato e preconfigurato.

Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

## <a name="create-a-workspace"></a>Creare un'area di lavoro

Un'area di lavoro di Machine Learning è una risorsa cloud fondamentale usata per eseguire gli esperimenti, il training e la distribuzione di modelli di Machine Learning. Collega la sottoscrizione e il gruppo di risorse di Azure a un oggetto di facile utilizzo nel servizio.

Per creare un'area di lavoro è necessario usare il portale di Azure, una console basata sul Web per la gestione delle risorse di Azure.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT]
> Prendere nota dell'**area di lavoro** e della **sottoscrizione**. Si tratta di informazioni necessarie per assicurarsi di creare l'esperimento nel posto giusto. 

## <a name="run-notebook-in-your-workspace"></a><a name="azure"></a>Eseguire il notebook nell'area di lavoro

Azure Machine Learning include un server notebook cloud nell'area di lavoro per un'esperienza preconfigurata senza installazioni. Se si preferisce avere il controllo sull'ambiente, sui pacchetti e sulle dipendenze, usare il [proprio ambiente](tutorial-1st-experiment-sdk-setup-local.md).

 Seguire questo video oppure usare la procedura dettagliata seguente per clonare ed eseguire il notebook dell'esercitazione dall'area di lavoro.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4mTUr]

### <a name="clone-a-notebook-folder"></a>Clonare una cartella del notebook

Completare i passaggi seguenti di configurazione ed esecuzione dell'esperimento in Azure Machine Learning Studio, un'interfaccia consolidata che include strumenti di Machine Learning per l'esecuzione di scenari di data science per esperti della materia di qualsiasi livello di competenza.

1. Accedere ad [Azure Machine Learning Studio](https://ml.azure.com/).

1. Selezionare la sottoscrizione e l'area di lavoro create.

1. Selezionare **Notebook** a sinistra.

1. Selezionare la scheda **Esempi** nella parte superiore.

1. Aprire la cartella **Python**.

1. Aprire la cartella con un numero di versione.  Questo numero rappresenta la versione corrente di Python SDK.

1. Selezionare **"..."** a destra della cartella **tutorials** e quindi scegliere **Clone** (Clona).

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/clone-tutorials.png" alt-text="Clonare la cartella tutorials":::

1. Viene visualizzato un elenco di cartelle che mostra ogni utente che accede all'area di lavoro.  Selezionare la propria cartella per clonare la cartella **tutorials** al suo interno.

### <a name="open-the-cloned-notebook"></a><a name="open"></a>Aprire il notebook clonato

1. Aprire la cartella **tutorials** appena clonata nella sezione **File utente**.

    > [!IMPORTANT]
    > Nella cartella di **esempi** è possibile visualizzare i notebook ma non eseguirli.  Per eseguire un notebook, assicurarsi di aprirne la versione clonata nella sezione **User Files** (File utente).
    
1. Selezionare il file **tutorial-1st-experiment-sdk-train.ipynb** nella cartella **tutorials/image-classification-mnist-data**.

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/expand-user-folder.png" alt-text="Aprire la cartella tutorials":::

1. Sulla barra superiore selezionare un'istanza di calcolo da usare per eseguire il notebook. Queste macchine virtuali sono preconfigurate con [tutti i componenti necessari per eseguire Azure Machine Learning](concept-compute-instance.md#contents).

1. Se non sono disponibili macchine virtuali, selezionare **+ Aggiungi** per creare una macchina virtuale per l'istanza di calcolo. 

    1. Quando si crea una macchina virtuale, attenersi alle regole seguenti:  
        + Il nome è obbligatorio e non può essere vuoto.
        + Il nome deve essere univoco (senza distinzione tra maiuscole e minuscole) in tutte le istanze di calcolo esistenti nell'area di Azure dell'istanza dell'area di lavoro o di calcolo. Si riceverà un avviso se il nome scelto non è univoco.
        + I caratteri validi includono lettere minuscole e maiuscole, numeri (da 0 a 9) e il trattino (-).
        + La lunghezza del nome deve essere compresa tra 3 e 24 caratteri.
        + Il nome deve iniziare con una lettera (non un numero o un trattino).
        + Se si usa il trattino, deve essere seguito da almeno una lettera. Esempio: Test-, test-0, test-01 non sono validi, mentre test-a0, test-0a sono istanze valide.

    1.  Selezionare le dimensioni della macchina virtuale dalle opzioni disponibili. Per le esercitazioni, la macchina virtuale predefinita è una buona opzione.

    1. Selezionare quindi **Crea**. Per configurare la VM sono necessari circa 5 minuti.

1. Quando la VM è disponibile, verrà visualizzata sulla barra degli strumenti superiore.  È ora possibile eseguire il notebook usando **Esegui tutto** sulla barra degli strumenti oppure premendo **MAIUSC + INVIO** nelle celle di codice del notebook.

Se sono presenti widget personalizzati o si preferisce usare Jupyter/JupyterLab, selezionare l'elenco a discesa **Jupyter** all'estrema destra, quindi selezionare **Jupyter** o **JupyterLab**. Verrà visualizzata una nuova finestra del browser.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato un ambiente di sviluppo, è possibile proseguire con il training di un modello in un notebook di Jupyter:

> [!div class="nextstepaction"]
> [Esercitazione: Eseguire il training di modelli di classificazione delle immagini con dati MNIST e scikit-learn](tutorial-train-models-with-aml.md)

<a name="stop-compute-instance"></a> Se non si prevede di seguire subito le altre esercitazioni, è consigliabile arrestare la macchina virtuale del server notebook cloud quando non viene usata, per ridurre i costi:

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]
