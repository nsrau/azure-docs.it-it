---
title: Clonare un notebook di Jupyter da GitHub con Azure Notebooks
description: Clonare rapidamente un notebook di Jupyter da un repository GitHub ed eseguirlo nell'account di Azure Notebooks.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: d7122b78-6daa-4bea-883b-ff832cfecef3
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 30625423553b71e848d27d047d4b7bc3add6eaff
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59265152"
---
# <a name="quickstart-clone-a-notebook"></a>Avvio rapido: Clonare un notebook

Molti data scientist e sviluppatori archiviano i notebook in [repository GitHub](https://github.com), un servizio gratuito che fornisce il controllo delle versioni e risorse di archiviazione per molti diversi tipi di progetto. GitHub viene spesso usato come strumento per collaborare ai notebook di Jupyter che vengono eseguiti in locale. In questi casi, ogni collaboratore gestisce una copia locale del repository ed esegue i notebook da tale copia.

La clonazione consente invece di creare una copia di un notebook di GitHub nell'account di Azure Notebooks. Questo clone è indipendente dal relativo repository originale. Le modifiche vengono archiviate solo nell'account di Azure Notebooks e non influiscono sull'originale. Poiché il clone si trova nel cloud, è possibile condividere il progetto con altri collaboratori che non necessitano di copie locali o che dispongono addirittura di Jupyter installato ne propri computer. È anche possibile clonare un notebook semplicemente come punto di partenza per un progetto personalizzato o per ottenere file di dati.

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

    [![](media/quickstarts/completed-clone.png "Visualizzazione di un clone completo")](media/quickstarts/completed-clone.png#lightbox)

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
> [Esercitazione: creare un'esecuzione di un notebook di Jupyter per eseguire la regressione lineare](tutorial-create-run-jupyter-notebook.md)
