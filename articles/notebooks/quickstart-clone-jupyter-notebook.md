---
title: Clonare un notebook di Jupyter da GitHub con Azure Notebooks (anteprima)
description: Clonare rapidamente un notebook di Jupyter da un repository GitHub ed eseguirlo nell'account di Azure Notebooks.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 267e79e7d4bf108ac3b2c72d64cee5a07ba638be
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "87424478"
---
# <a name="quickstart-clone-a-notebook-in-azure-notebooks-preview"></a>Guida introduttiva: Clonare un notebook in Azure Notebooks (anteprima)

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

In questo argomento di avvio rapido si copia un notebook di Jupyter archiviato in GitHub in un account Azure Notebooks. 

I repository GitHub forniscono il controllo della versione e dell'archiviazione per i notebook di Jupyter. I collaboratori conservano le copie locali dei repository ed eseguono i notebook da tali copie. La clonazione di un notebook di Jupyter da GitHub nell'account Azure Notebooks crea una copia indipendente del notebook. Le modifiche vengono archiviate solo nell'account di Azure Notebooks e non influiscono sull'originale di GitHub. 

Dal momento che il clone di Azure Notebooks si trova nel cloud, è possibile condividerlo con altri collaboratori che non necessitano di copie locali o che hanno installato Jupyter nei propri computer. È anche possibile clonare un notebook semplicemente come punto di partenza per un progetto personalizzato o per ottenere file di dati. 

## <a name="prerequisites"></a>Prerequisiti
No.

## <a name="clone-azure-cognitive-services-notebooks"></a>Clonare notebook di Servizi cognitivi di Azure

1. Passare ad [Azure Notebooks](https://notebooks.azure.com) ed eseguire l'accesso. Per informazioni dettagliate, vedere [Avvio rapido: Accedere ad Azure Notebooks](quickstart-sign-in-azure-notebooks.md).

1. Dalla pagina del profilo pubblico selezionare **My Projects** (Progetti personali) nella parte superiore:

    ![Collegamento My Projects (Progetti personali) nella parte superiore della finestra del browser](media/quickstarts/my-projects-link.png)

1. Nella pagina **My Projects** (Progetti personali) selezionare il pulsante con la freccia verso l'alto (tasto di scelta rapida: U). Il pulsante viene visualizzato come **Upload GitHub Repo** (Carica repository GitHub) quando la finestra del browser è sufficientemente ampia):

    ![Comando Upload GitHub Repo (Carica repository GitHub) nella pagina My Projects (Progetti personali)](media/quickstarts/upload-github-repo-command.png)

1. Nella pagina **Upload GitHub Repository** (Carica repository GitHub) che viene visualizzata, immettere o impostare i dettagli seguenti e quindi selezionare **Import** (Importa):

   - **GitHub repository** (Repository GitHub): Microsoft/cognitive-services-notebooks (questo nome clona i notebook di Jupyter per Servizi cognitivi di Azure in [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks)).
   - **Clone recursively** (Clona in modo ricorsivo): (casella deselezionata)
   - **Project name** (Nome progetto): Cognitive Services Clone
   - **Project ID** (ID progetto): cognitive-services-clone
   - **Public** (Pubblico): (casella deselezionata)

     ![Popup di caricamento del repository GitHub per raccogliere le informazioni del repository](media/quickstarts/upload-github-repo-popup.png)

1. È necessario attendere il completamento del processo. La clonazione di un repository può richiedere alcuni minuti.

1. Al termine della clonazione, Azure Notebooks accede al nuovo progetto in cui è possibile visualizzare le copie di tutti i file.

    :::image type="content" source="media/quickstarts/completed-clone.png" alt-text="Visualizzazione di un clone completato." lightbox="media/quickstarts/completed-clone.png":::

## <a name="share-a-notebook"></a>Condividere un notebook

1. Per condividere la propria copia del progetto clonato, usare il controllo **Share** (Condividi) oppure ottenere un collegamento, ottenere il codice HTML o Markdown che contiene il collegamento o creare un messaggio di posta elettronica con il collegamento:

    ![Comando di condivisione del progetto](media/quickstarts/share-project-command.png)

1. Poiché è stata deselezionata l'opzione **Public** (Pubblico) durante la clonazione del progetto, il clone è privato. Per rendere pubblica la copia, selezionare **Project Settings** (Impostazioni progetto), impostare l'opzione **Public project** (Progetto pubblico) nella finestra popup e quindi selezionare **Salva**.

1. Selezionare un notebook nel progetto per eseguirlo. Ogni notebook nel repository di Servizi cognitivi di Azure, ad esempio, è un Avvio rapido indipendente. L'immagine seguente mostra il risultato dell'uso del notebook BingImageSearchAPI dopo l'aggiunta di una chiave di sottoscrizione API Servizi cognitivi e la modifica del termine di ricerca "cuccioli" con "conigli":

    ![Esecuzione del notebook di Jupyter clonato da GitHub](media/quickstarts/clone-notebook-result.png)

1. Dopo aver eseguito il notebook, selezionare **File** > **Close and halt** (Chiudi e arresta) per chiudere il notebook e la finestra del browser.

1. Per condividere un singolo notebook nel progetto, fare clic con il pulsante destro del mouse nel notebook e scegliere **Copy link** (Copia collegamento) (tasto di scelta rapida: y):

    ![Comando del menu di scelta rapida per copiare un collegamento in un singolo notebook](media/quickstarts/copy-link-to-individual-notebook.png)

1. Per modificare file diversi dai notebook, fare clic con il pulsante destro del mouse sul file nel progetto e scegliere **Edit file** (Modifica file) (tasto di scelta rapida: i). L'operazione predefinita **Run** (Esegui) (tasto di scelta rapida: r) mostra solo il contenuto del file e non consente la modifica.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: Creare ed eseguire un notebook di Jupyter per la regressione lineare](tutorial-create-run-jupyter-notebook.md)
