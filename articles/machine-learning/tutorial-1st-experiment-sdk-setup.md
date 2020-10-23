---
title: 'Esercitazione: Introduzione ai notebook di Jupyter (Python)'
titleSuffix: Azure Machine Learning
description: Configurazione per le esercitazioni sui notebook di Jupyter. Creare un'area di lavoro di Azure Machine Learning, clonare i notebook di Jupyter nell'area di lavoro e creare un'istanza di ambiente di calcolo in cui eseguire i notebook.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 02/10/2020
ms.custom: devx-track-python
ms.openlocfilehash: df8e4c2728bd7487520164553d26dfd42e38b647
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91841851"
---
# <a name="tutorial-get-started-with-azure-machine-learning-in-jupyter-notebooks"></a>Esercitazione: Introduzione al servizio Azure Machine Learning nei notebook di Jupyter

In questa esercitazione si completa la procedura per iniziare a usare Azure Machine Learning con i notebook di Jupyter in una [workstation gestita basata sul cloud (istanza di ambiente di calcolo)](concept-compute-instance.md). Questa esercitazione è propedeutica a tutte le altre esercitazioni su Jupyter Notebook.

In questa esercitazione:

> [!div class="checklist"]
> * Creare un'[area di lavoro di Azure Machine Learning](concept-workspace.md) da usare in altre esercitazioni su Jupyter Notebook.
> * Clonare il notebook delle esercitazioni nella cartella nell'area di lavoro.
> * Creare un'istanza di calcolo basata sul cloud con Azure Machine Learning Python SDK installato e preconfigurato.

Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

## <a name="create-a-workspace"></a>Creare un'area di lavoro

Un'area di lavoro di Machine Learning è una risorsa cloud fondamentale usata per eseguire gli esperimenti, il training e la distribuzione di modelli di Machine Learning. Collega la sottoscrizione e il gruppo di risorse di Azure a un oggetto di facile utilizzo nel servizio.

Per creare un'area di lavoro è necessario usare il portale di Azure, una console basata sul Web per la gestione delle risorse di Azure.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT]
> Prendere nota dell'*area di lavoro* e della *sottoscrizione*. Queste informazioni saranno necessarie per assicurarsi che l'esperimento venga creato nel posto giusto.

## <a name="run-a-notebook-in-your-workspace"></a><a name="azure"></a>Eseguire un notebook nell'area di lavoro

Azure Machine Learning include un server notebook cloud nell'area di lavoro per un'esperienza preconfigurata senza installazioni. Se si preferisce avere il controllo sull'ambiente, sui pacchetti e sulle dipendenze, usare il [proprio ambiente](tutorial-1st-experiment-sdk-setup-local.md).

 Seguire questo video oppure la procedura dettagliata seguente per clonare ed eseguire il notebook dell'esercitazione dall'area di lavoro.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4mTUr]

### <a name="clone-a-notebook-folder"></a>Clonare una cartella del notebook

Completare la configurazione dell'esperimento seguente ed eseguire i passaggi in Azure Machine Learning Studio. Questa interfaccia consolidata include gli strumenti di Machine Learning per eseguire scenari di data science per professionisti con tutti i livelli di competenze.

1. Accedere ad [Azure Machine Learning Studio](https://ml.azure.com/).

1. Selezionare la sottoscrizione e l'area di lavoro create.

1. Selezionare **Notebook** a sinistra.

1. Selezionare la scheda **Esempi** in alto.

1. Aprire la cartella **Python**.

1. Aprire la cartella con un numero di versione. Questo numero rappresenta la versione corrente di Python SDK.

1. Selezionare il pulsante **...** a destra della cartella **tutorials** e quindi scegliere **Clone** (Clona).

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/clone-tutorials.png" alt-text="Screenshot che mostra la clonazione della cartella di esercitazioni.":::

1. Viene visualizzato un elenco di cartelle che mostra ogni utente che accede all'area di lavoro. Selezionare la propria cartella per clonare la cartella **tutorials** al suo interno.

### <a name="open-the-cloned-notebook"></a><a name="open"></a>Aprire il notebook clonato

1. Aprire la cartella **tutorials** clonata nella sezione **User Files** (File utente).

    > [!IMPORTANT]
    > Nella cartella di **esempi** è possibile visualizzare i notebook ma non eseguirli. Per eseguire un notebook, assicurarsi di aprirne la versione clonata nella sezione **User Files** (File utente).
    
1. Selezionare il file **tutorial-1st-experiment-sdk-train.ipynb** nella cartella **tutorials/image-classification-mnist-data**.

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/expand-user-folder.png" alt-text="Screenshot che mostra la clonazione della cartella di esercitazioni.":::

1. Sulla barra superiore selezionare un'istanza di calcolo da usare per eseguire il notebook. Queste macchine virtuali (VM) sono preconfigurate con [tutti i componenti necessari per eseguire Azure Machine Learning](concept-compute-instance.md#contents).

1. Se non sono disponibili macchine virtuali, selezionare **+ Aggiungi** per creare una macchina virtuale per l'istanza di calcolo.

    1. Quando si crea una macchina virtuale, attenersi alle regole seguenti:
 
        + Il nome è obbligatorio e il campo non può essere vuoto.
        + Il nome deve essere univoco (senza distinzione tra maiuscole e minuscole) in tutte le istanze di calcolo esistenti nell'area di Azure dell'area di lavoro o dell'istanza di calcolo. Se il nome scelto non è univoco, si riceverà un avviso.
        + I caratteri validi sono lettere minuscole e maiuscole, numeri da 0 a 9 e trattino (-).
        + Il nome deve avere una lunghezza compresa tra 3 e 24 caratteri.
        + Il nome deve iniziare con una lettera, non un numero o un trattino.
        + Se si usa il trattino, deve essere seguito da almeno una lettera. Ad esempio, Test-, test-0, test-01 non sono validi, mentre test-a0, test-0a sono istanze valide.

    1. Selezionare le dimensioni della VM tra le opzioni disponibili. Per le esercitazioni, la macchina virtuale predefinita è una buona opzione.

    1. Selezionare quindi **Crea**. Per configurare la VM sono necessari circa 5 minuti.

1. Quando la VM è disponibile, viene visualizzata nella barra degli strumenti superiore. È ora possibile eseguire il notebook usando **Esegui tutto** sulla barra degli strumenti oppure premendo **MAIUSC+INVIO** nelle celle di codice del notebook.

Se sono presenti widget personalizzati oppure si preferisce usare Jupyter o JupyterLab, selezionare l'elenco a discesa **Jupyter** all'estrema destra. Quindi selezionare **Jupyter** o **JupyterLab**. Verrà visualizzata la nuova finestra del browser.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato un ambiente di sviluppo, è possibile proseguire con il training di un modello in un notebook di Jupyter.

> [!div class="nextstepaction"]
> [Esercitazione: Eseguire il training di modelli di classificazione delle immagini con dati MNIST e scikit-learn](tutorial-train-models-with-aml.md)

<a name="stop-compute-instance"></a> Se non si prevede di seguire subito le altre esercitazioni, arrestare la macchina virtuale del server notebook cloud quando non viene usata, per ridurre i costi.

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]
