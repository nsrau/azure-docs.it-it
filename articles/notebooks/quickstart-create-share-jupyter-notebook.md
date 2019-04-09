---
title: Creare e condividere un notebook di Jupyter in Azure
description: Creare rapidamente ed eseguire un notebook di Jupyter in Azure Notebooks e quindi condividerlo con altri utenti.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 5add60ad-0b4b-4fd5-adf5-eb50ce072d00
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: b1618e2ac997445606ce98fc72a1ec35ca1280be
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59273669"
---
# <a name="quickstart-create-and-share-a-notebook"></a>Avvio rapido: Creare e condividere un notebook

1. Passare ad [Azure Notebooks](https://notebooks.azure.com) ed eseguire l'accesso. Per informazioni dettagliate, vedere [Avvio rapido: Accedere ad Azure Notebooks](quickstart-sign-in-azure-notebooks.md).

1. Dalla pagina del profilo pubblico selezionare **My Projects** (Progetti personali) nella parte superiore:

    ![Collegamento My Projects (Progetti personali) nella parte superiore della finestra del browser](media/quickstarts/my-projects-link.png)

1. Nella pagina **My Projects** (Progetti personali) selezionare **+ New Project** (+ Nuovo progetto) (tasto di scelta rapida: n); se la finestra del browser è di dimensioni ridotte, il pulsante potrebbe essere visualizzato solo come **+**:

    ![Comando New Project (Nuovo progetto) nella pagina My Projects (Progetti personali)](media/quickstarts/new-project-command.png)

1. Nella finestra popup **Create New Project** (Crea nuovo progetto) visualizzata immettere o impostare i dettagli seguenti e quindi selezionare **Create** (Crea):

   - **Nome progetto**: Hello World in Python
   - **ID progetto**: hello-world-python
   - **Public project** (Progetto pubblico): (disattivata)
   - **Create a README.md** (Crea un file README.md): (disattivata)

     ![Finestra popup New Project (Nuovo progetto) con i dettagli inseriti](media/quickstarts/new-project-popup.png)

1. Dopo qualche istante, Azure Notebooks passa al nuovo progetto. Per aggiungere un notebook al progetto, selezionare l'elenco a discesa **+ New** (+ Nuovo) (che potrebbe essere visualizzato solo come **+**) e quindi selezionare **Notebook**:

    [![](media/quickstarts/empty-project-new-notebook-button.png "Un nuovo progetto vuoto e aggiungere il comando notebook")](media/quickstarts/empty-project-new-notebook-button.png#lightbox)

1. Nella finestra popup **Create New Notebook** (Crea nuovo notebook) visualizzata immettere un nome file per il notebook, ad esempio *HelloWorldInPython.ipynb* (*.ipynb* significa notebook IronPython - Jupyter) e selezionare **Python 3.6** per la lingua (chiamato anche il *kernel*):

    ![Finestra popup per la creazione di un nuovo notebook](media/quickstarts/new-notebook-popup.png)

1. Selezionare **New** (Nuovo) per completare la creazione del notebook, che viene visualizzato nell'elenco di file del progetto:

    ![Nuovo notebook visualizzato nell'elenco di file del progetto](media/quickstarts/new-notebook-created.png)

## <a name="run-the-notebook"></a>Eseguire il notebook

1. Selezionare il nuovo notebook per essere eseguito nell'editor; il kernel selezionato (in questo esempio, Python 3.6) viene attivato automaticamente per il notebook:

    ![Visualizzazione di un nuovo notebook in Azure Notebooks](media/quickstarts/create-notebook-first-open.png)

1. Per impostazione predefinita, il notebook ha una cella di codice vuota. Per modificare il tipo di cella in **Markdown**, usare l'elenco a discesa relativo al tipo di cella e selezionare **Markdown**:

    ![Modifica del tipo di cella in un nuovo notebook](media/quickstarts/create-notebook-cell-type.png)

1. Immettere o incollare il testo di intestazione seguente nella cella:

    ```markdown
    # Hello World in Python
    ```

1. Poiché si sta modificando una cella Markdown, il testo viene visualizzato come intestazione con il simbolo "#". Per eseguire il rendering del testo Markdown in formato HTML, selezionare il pulsante **Esegui**. Azure Notebooks crea automaticamente una nuova cella di codice:

    ![Il pulsante Esegui per una cella e il testo Markdown sottoposto a rendering](media/quickstarts/run-cell-markdown-render.png)

1. Nella cella di codice immettere il codice Python seguente:

    ```python
    from datetime import datetime

    now = datetime.now()
    msg = "Hello, Azure Notebooks! Today is %s"  % now.strftime("%A, %d %B, %Y")
    print(msg)
    ```

1. Selezionare **Run** (Esegui) (tasto di scelta rapida: MAIUSC+INVIO) per eseguire il codice. Sotto la cella viene visualizzato un output positivo simile al seguente:

    ```output
    Hello, Azure Notebooks! Today is Thursday, 15 November, 2018
    ```

1. Selezionare l'icona di salvataggio per salvare l'operazione:

    ![Icona di salvataggio nella barra degli strumenti del notebook di Jupyter](media/quickstarts/hello-results-save-icon.png)

1. Selezionare il **File** > **Chiudi e Interrompi** comando di menu per arrestare il server e chiudere la finestra del browser.

## <a name="share-the-notebook"></a>Condividere il notebook

Per condividere il notebook, tornare alla pagina del progetto (se necessario), fare clic sul file del notebook, selezionare **Copy Link** (Copia collegamento) (tasto di scelta rapida: y) e incollare il collegamento in un messaggio appropriato (posta elettronica, messaggistica immediata e così via).

Nella pagina del progetto è anche possibile usare il menu **Share** (Condividi) per ottenere un collegamento, creare un messaggio di posta elettronica con il collegamento oppure ottenere il codice di incorporamento HTML o Markdown:

![Comando di condivisione del progetto](media/quickstarts/share-project-command.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: creare un'esecuzione di un notebook di Jupyter per eseguire la regressione lineare](tutorial-create-run-jupyter-notebook.md)
