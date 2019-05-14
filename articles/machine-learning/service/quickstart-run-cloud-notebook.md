---
title: 'Avvio rapido: Eseguire un notebook nel cloud'
titleSuffix: Azure Machine Learning service
description: Introduzione al servizio Azure Machine Learning. Usare un server notebook gestito nel cloud per provare l'area di lavoro.  L'area di lavoro rappresenta l'elemento fondamentale nel cloud per eseguire esperimenti, training e distribuzione di modelli di Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
author: sdgilley
ms.author: sgilley
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 1a48f8620fb99f1cf8787dabc738d328a796d093
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65510623"
---
# <a name="quickstart-use-a-cloud-based-notebook-server-to-get-started-with-azure-machine-learning"></a>Avvio rapido: Usare un server notebook basato sul cloud per iniziare a usare Azure Machine Learning

Creare un server notebook basato sul cloud, quindi usarlo.  In questa guida di avvio rapido si esegue codice Python che registra i valori nell'[area di lavoro del servizio Azure Machine Learning](concept-azure-machine-learning-architecture.md). L'area di lavoro rappresenta l'elemento fondamentale nel cloud per eseguire esperimenti, training e distribuzione di modelli di Machine Learning con il servizio Machine Learning. 

Questa guida di avvio rapido illustra come creare una risorsa cloud nell'area di lavoro di Azure Machine Learning configurata con l'ambiente Python necessario per l'esecuzione di Azure Machine Learning. Per usare invece il proprio ambiente, vedere [Avvio rapido: Usare il proprio server notebook per iniziare a usare Azure Machine Learning](quickstart-run-local-notebook.md).  

In questo argomento di avvio rapido si eseguono le operazioni seguenti:

* Creare un nuovo server notebook basato sul cloud nell'area di lavoro.
* Avviare l'interfaccia Web di Jupyter.
* Aprire un notebook che contiene codice per stimare gli errori di pi greco e registra gli errori a ogni iterazione.
* Eseguire il notebook.
* Visualizzare i valori di errore registrati nell'area di lavoro. Questo esempio mostra come l'area di lavoro consente di tenere traccia delle informazioni generate in uno script.

Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Accedere alla [versione gratuita o a pagamento del servizio Azure Machine Learning](https://aka.ms/AMLFree).

## <a name="create-a-workspace"></a>Creare un'area di lavoro

Se è già disponibile un'area di lavoro del servizio Azure Machine Learning, passare alla [sezione successiva](#create-a-cloud-based-notebook-server). Altrimenti crearne una adesso.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="create-a-cloud-based-notebook-server"></a>Creare un server notebook basato sul cloud

 Nell'area di lavoro viene creata una risorsa cloud per iniziare a usare i notebook di Jupyter. Questa risorsa offre una piattaforma basata sul cloud preconfigurata con tutto quanto necessario per eseguire il servizio Azure Machine Learning.

1. Aprire l'area di lavoro nel [portale di Azure](https://portal.azure.com/).  Se non si sa come individuare l'area di lavoro nel portale, vedere l'articolo su [come trovare l'area di lavoro](how-to-manage-workspace.md#view).

1. Nella pagina dell'area di lavoro nel portale di Azure, selezionare **Macchine virtuali per notebook** a sinistra.

1. Selezionare **+Nuova** per creare una nuova macchina virtuale per notebook.

     ![Selezionare Nuova macchina virtuale](./media/quickstart-run-cloud-notebook/add-workstation.png)

1. Specificare un nome per la macchina virtuale. Selezionare quindi **Crea**.

    > [!NOTE]
    > La lunghezza del nome della macchina virtuale per notebook deve essere compresa tra 2 e 16 caratteri. I caratteri validi sono lettere, cifre e il carattere -.  Il nome della deve inoltre essere univoco nell'intera sottoscrizione di Azure.

    ![Creare una nuova VM](media/quickstart-run-cloud-notebook/create-new-workstation.png)

1. Attendere 4-5 minuti circa, fino a quando lo stato cambia in **In esecuzione**.

## <a name="launch-jupyter-web-interface"></a>Avviare l'interfaccia Web di Jupyter

Dopo che la macchina virtuale è in esecuzione, usare la sezione **Macchine virtuali per notebook** per avviare l'interfaccia Web di Jupyter.

1. Selezionare **Jupyter** nella colonna **URI** relativa alla macchina virtuale.  

    ![Avviare il server Jupyter Notebook](./media/quickstart-run-cloud-notebook/start-server.png)

    Il collegamento consente di avviare il server del notebook e di aprire la pagina Web del notebook Jupyter in una nuova scheda del browser.  Questo collegamento funziona solo per l'utente che crea la macchina virtuale.

1. Nella pagina Web del notebook di Jupyter, il nomecartella nella parte superiore è il nome utente.  Selezionare questa cartella.

1. Gli esempi nomecartella includono un numero di versione, ad esempio **esempi-1.0.33.1**.  Selezionare la cartella degli esempi.

1. Selezionare l'**avvio rapido**del notebook.

## <a name="run-the-notebook"></a>Eseguire il notebook

Eseguire un notebook che stima il pi greco e registra l'errore nell'area di lavoro.

1. Selezionare **01.run experiment.ipynb** per aprire il notebook.

1. Fare clic sulla prima cella del codice e selezionare **Run** (Esegui).

    > [!NOTE]
    > Le celle del codice sono precedute da parentesi quadre. Se le parentesi quadre sono vuote (__[  ]__), il codice non è stato eseguito. Durante l'esecuzione del codice, viene visualizzato un asterisco (__[*]__). Al termine dell'esecuzione del codice, viene visualizzato un numero **[1]**,  che indica l'ordine di esecuzione delle celle.
    >
    > Usare il tasto di scelta rapida **MAIUSC+INVIO** per eseguire una cella.

    ![Eseguire la prima cella di codice](media/quickstart-run-cloud-notebook/cell1.png)

1. Eseguire la seconda cella di codice. Se vengono visualizzate istruzioni per l'autenticazione, copiare il codice e fare clic sul collegamento per eseguire l'accesso. Dopo l'accesso, il browser memorizzerà questa impostazione.  

    ![Autentica](media/quickstart-run-cloud-notebook/authenticate.png)

1. Al termine, viene visualizzato il numero di cella __[2]__.  Se è stato necessario eseguire l'accesso, verrà visualizzato un messaggio di stato di autenticazione riuscita.   Se non è stato necessario eseguire l'accesso, non verrà visualizzato alcun output per questa cella, eccetto il numero che indica che la cella è stata eseguita correttamente.

    ![Messaggio di operazione riuscita](media/quickstart-run-cloud-notebook/success.png)

1. Eseguire le celle di codice rimanenti.  Al termine dell'esecuzione di ogni singola cella, verrà visualizzato il relativo numero di cella. Solo con l'ultima cella viene visualizzato l'eventuale altro output.  

    In più punti della cella di codice più estesa viene visualizzato `run.log`. Ogni occorrenza di `run.log` aggiunge il relativo valore all'area di lavoro.

## <a name="view-logged-values"></a>Visualizzare i valori registrati

1. L'output della cella `run` contiene un collegamento al portale di Azure per visualizzare i risultati dell'esperimento nell'area di lavoro.

    ![Visualizzare gli esperimenti](./media/quickstart-run-cloud-notebook/view-exp.png)

1. Fare cli sul **collegamento al portale di Azure** per visualizzare le informazioni sull'esecuzione nell'area di lavoro.  Questo collegamento apre l'area di lavoro nel portale di Azure.

1. I tracciati dei valori registrati visualizzati sono stati creati automaticamente nell'area di lavoro. Ogni volta che si registrano più valori con lo stesso parametro name, viene generato automaticamente un tracciato.

   ![Visualizzare la cronologia](./media/quickstart-run-cloud-notebook/web-results.png)

Poiché il codice per approssimare Pi usa valori casuali, i tracciati mostreranno valori diversi.  

## <a name="clean-up-resources"></a>Pulire le risorse

### <a name="stop-the-notebook-vm"></a>Arrestare la macchina virtuale per notebook

Arrestare la macchina virtuale per notebook quando non viene usata per ridurre i costi.  

1. Nell'area di lavoro selezionare **Macchine virtuali per notebook**.

   ![Arrestare il server di macchine virtuali](./media/quickstart-run-cloud-notebook/stop-server.png)

1. Selezionare la macchina virtuale dall'elenco.

1. Selezionare **Arresta**.

1. Quando si è pronti a usare di nuovo il server, selezionare **Avvia**.

### <a name="delete-everything"></a>Eliminare tutto

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

È anche possibile mantenere il gruppo di risorse ma eliminare una singola area di lavoro. Visualizzare le proprietà dell'area di lavoro e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido sono state completate le attività seguenti:

* Creare una macchina virtuale per notebook.
* Avviare l'interfaccia Web di Jupyter.
* Aprire un notebook che contiene codice per stimare gli errori di pi greco e registra gli errori a ogni iterazione.
* Eseguire il notebook.
* Visualizzare i valori di errore registrati nell'area di lavoro.  Questo esempio mostra come l'area di lavoro consente di tenere traccia delle informazioni generate in uno script. 

Nella pagina Web di Jupyter Notebook, esplorare altri notebook nella cartella degli esempi per altre informazioni sul servizio Azure Machine Learning.

Per un'esperienza approfondita del flusso di lavoro, seguire le esercitazioni di Machine Learning riguardanti il training e la distribuzione di un modello:  

> [!div class="nextstepaction"]
> [Esercitazione: Eseguire il training di un modello per la classificazione di immagini](tutorial-train-models-with-aml.md)
