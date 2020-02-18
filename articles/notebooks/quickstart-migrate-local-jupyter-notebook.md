---
title: Eseguire la migrazione di un notebook locale di Jupyter in Azure Notebooks (anteprima)
description: Trasferire rapidamente un notebook di Jupyter in Azure Notebooks (anteprima) dal computer locale o da un URL Web e quindi condividerlo per la collaborazione.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: baf05d7adb1340d712ff0fc87436d5bbac51bc8f
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064326"
---
# <a name="quickstart-migrate-a-local-jupyter-notebook-in-azure-notebooks-preview"></a>Avvio rapido: Eseguire la migrazione di un notebook locale di Jupyter in Azure Notebooks (anteprima)

Questo argomento di avvio rapido si apprenderà a eseguire la migrazione di un notebook di Jupyter dal computer locale o da un altro URL di file accessibile in Azure Notebooks. 

I notebook di Jupyter sul proprio computer sono accessibili solo dall'utente, che potrà condividerli, ma ai destinatari resterà una copia locale del notebook, in cui sarà difficile incorporare modifiche necessarie. Anche se si archiviano i notebook in un repository online condiviso come GitHub, ogni collaboratore dovrà avere un'installazione Jupyter locale con la stessa configurazione dell'utente.

Eseguendo la migrazione in Azure Notebooks dei notebook locali o basati su repository, è possibile condividerli immediatamente con i collaboratori, che necessiteranno solo di un browser per visualizzarli ed eseguirli. Se accedono ad Azure Notebooks, potranno anche apportare modifiche.

## <a name="prerequisites"></a>Prerequisites

- Un [notebook di Jupyter](https://jupyter-notebook.readthedocs.io) nel computer locale o in un altro URL di file accessibile. 

## <a name="create-a-project-on-azure-notebooks"></a>Creare un progetto in Azure Notebooks

Questo argomento di avvio rapido illustra la migrazione di un notebook dal computer locale o da un altro URL di file accessibile. Per migrare un notebook da un repository di GitHub, vedere [Avvio rapido: Clonare un notebook](quickstart-clone-jupyter-notebook.md).

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

1. Passare ad [Azure Notebooks](https://notebooks.azure.com) ed eseguire l'accesso. Per informazioni dettagliate, vedere [Avvio rapido: Accedere ad Azure Notebooks](quickstart-sign-in-azure-notebooks.md).

1. Dalla pagina del profilo pubblico selezionare **My Projects** (Progetti personali) nella parte superiore:

    ![Collegamento My Projects (Progetti personali) nella parte superiore della finestra del browser](media/quickstarts/my-projects-link.png)

1. Nella pagina **Progetti personali** selezionare **Nuovo progetto** (tasti di scelta rapida: n). Il pulsante può essere visualizzato solo come **+** se la finestra del browser è ridotta:

    ![Comando New Project (Nuovo progetto) nella pagina My Projects (Progetti personali)](media/quickstarts/new-project-command.png)

1. Nella finestra popup **Create New Project** (Crea nuovo progetto) visualizzata immettere i valori appropriati per il notebook di cui si sta eseguendo la migrazione nei campi **Project name**  (Nome progetto) e **Project ID** (ID progetto), deselezionare le opzioni **Public project** (Progetto pubblico) e **Create a README.md** (Crea un README.md) e quindi selezionare **Create** (Crea).

## <a name="upload-the-local-notebook"></a>Caricare il notebook locale

1. Nella pagina del progetto selezionare **Upload** (Carica), che potrebbe essere visualizzato sotto forma di freccia se la finestra del browser è di dimensioni ridotte, e quindi selezionare 1. Nella finestra popup visualizzata selezionare **From computer** (Da computer) se il notebook si trova nel file system locale o **From URL** (Da URL) se il notebook è disponibile online:

    ![Comando per caricare un notebook da un URL o da un computer locale](media/quickstarts/upload-from-computer-url-command.png)

   Anche in questo caso, se il notebook si trova in un repository di GitHub, seguire la procedura descritta in [Avvio rapido: Clonare un notebook](quickstart-clone-jupyter-notebook.md).

   - Se si usa l'opzione **From Computer** (Da computer), trascinare i file *.ipynb* nella finestra popup o selezionare **Choose Files** (Scegli file) e quindi passare ai file di dati da importare e selezionarli. Selezionare quindi **Carica**. A file caricati viene assegnato lo stesso nome dei file locali. Non è necessario caricare il contenuto delle cartelle *.ipynb_checkpoints*.

     ![Caricamento dalla finestra popup del computer](media/quickstarts/upload-from-computer-popup.png)

   - Se si usa l'opzione **From URL** (Da URL), immettere l'indirizzo dell'origine nel campo **File URL** (URL del file) e il nome file da assegnare al notebook nel progetto nel campo **File Name** (Nome file). Selezionare quindi **Carica**. Se si hanno più file con URL separati, usare il comando **Add file** (Aggiungi file) per verificare il primo URL immesso. Nella finestra popup verranno quindi visualizzati nuovi campi per un altro file.

     ![Caricamento dalla finestra popup dell'URL](media/quickstarts/upload-from-url-popup.png)

1. Aprire ed eseguire il notebook appena caricato per verificarne il contenuto e il funzionamento. Al termine, selezionare **File** > **Halt and close** (File > Interrompi e chiudi) per chiudere il notebook.

1. Per condividere un collegamento al notebook caricato, fare clic con il pulsante destro del mouse sul file nel progetto e selezionare **Copy Link** (Copia collegamento) (tasto di scelta rapida: y) e quindi incollare il collegamento nel messaggio appropriato. In alternativa, è possibile condividere l'intero progetto usando il controllo **Share** (Condividi) nella pagina del progetto.

1. Per modificare file diversi dai notebook, fare clic con il pulsante destro del mouse sul file nel progetto e scegliere **Edit file** (Modifica file) (tasto di scelta rapida: i). L'operazione predefinita **Run** (Esegui) (tasto di scelta rapida: r) mostra solo il contenuto del file e non consente la modifica.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: Creare ed eseguire un notebook di Jupyter per la regressione lineare](tutorial-create-run-jupyter-notebook.md)
