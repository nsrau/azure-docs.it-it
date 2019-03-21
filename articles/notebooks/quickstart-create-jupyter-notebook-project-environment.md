---
title: Creare un progetto di Azure Notebooks con un ambiente personalizzato
description: Creare un nuovo progetto Azure Notebooks configurato con un set specifico di pacchetti e script di avvio installati.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: e049f591-27a7-440f-a1a3-c5bef25e8a28
ms.service: azure
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 35b5f1e29ae125dcac79c278578e900a73ffc7be
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57771387"
---
# <a name="quickstart-create-a-project-with-a-custom-environment"></a>Avvio rapido: Creare un progetto con un ambiente personalizzato

Un progetto in Azure Notebooks è una raccolta di file, ad esempio notebook, file di dati, documentazione, immagini e così via, inclusi in un ambiente configurabile con comandi specifici. Dato che l'ambiente viene definito con il progetto, l'utente che clona il progetto nel proprio account Azure Notebooks dispone di tutti i dati necessari per ricreare l'ambiente necessario.

## <a name="create-a-project"></a>Creare un progetto

1. Passare ad [Azure Notebooks](https://notebooks.azure.com) ed eseguire l'accesso. Per informazioni dettagliate, vedere [Avvio rapido: Accedere ad Azure Notebooks](quickstart-sign-in-azure-notebooks.md).

1. Dalla pagina del profilo pubblico selezionare **My Projects** (Progetti personali) nella parte superiore:

    ![Collegamento My Projects (Progetti personali) nella parte superiore della finestra del browser](media/quickstarts/my-projects-link.png)

1. Nella pagina **My Projects** (Progetti personali) selezionare **+ New Project** (+ Nuovo progetto) (tasto di scelta rapida: n); se la finestra del browser è di dimensioni ridotte, il pulsante potrebbe essere visualizzato solo come **+**:

    ![Comando New Project (Nuovo progetto) nella pagina My Projects (Progetti personali)](media/quickstarts/new-project-command.png)

1. Nella finestra popup **Create New Project** (Crea nuovo progetto) visualizzata immettere o impostare i dettagli seguenti e quindi selezionare **Create** (Crea):

    - **Project name** (Nome progetto): Progetto con un ambiente personalizzato
    - **Project ID** (ID progetto): project-custom-environment
    - **Public project** (Progetto pubblico): (disattivata)
    - **Create a README.md** (Crea un file README.md): (disattivata)

1. Dopo qualche istante Azure Notebooks passa al nuovo progetto. Per aggiungere un notebook al progetto, selezionare l'elenco a discesa **+ New** (+ Nuovo) (che potrebbe essere visualizzato solo come **+**) e quindi selezionare **Notebook**.

1. Assegnare un nome al notebook, ad esempio *Custom environment.ipynb*, selezionare **Python 3.6** per il linguaggio e quindi selezionare **New** (Nuovo).

## <a name="add-a-custom-setup-step"></a>Aggiungere un passaggio di installazione personalizzato

1. Nella pagina del progetto selezionare **Project Settings** (Impostazioni progetto).

    ![Comando Project Settings (Impostazioni progetto)](media/quickstarts/project-settings-command.png)

1. Nella finestra a comparsa **Project Settings** (Impostazioni progetto) selezionare la scheda **Environment** (Ambiente), quindi in **Environment Setup Steps** (Passaggi di configurazione dell'ambiente) selezionare **+ Add** (+ Aggiungi):

    ![Comando per aggiungere un nuovo passaggio di configurazione dell'ambiente](media/quickstarts/environment-add-command.png)

1. Il comando **+ Add** (+ Aggiungi) crea un passaggio definito da un'operazione e un file di destinazione selezionato tra i file del progetto. Sono supportate le operazioni seguenti:

    | Operazione | DESCRIZIONE |
    | --- | --- |
    | Requirements.txt | I progetti Python definiscono le dipendenze in un file requirements.txt. Con questa opzione, selezionare il file appropriato dall'elenco dei file del progetto e selezionare anche la versione di Python nell'elenco a discesa aggiuntivo visualizzato. Se necessario, selezionare **Cancel** (Annulla) per tornare al progetto, caricare o creare il file, quindi tornare alla scheda **Project Settings** > **Environment** (Impostazioni progetto - Ambiente) e creare un nuovo passaggio. Quando questo passaggio è attivo, se si esegue un notebook nel progetto viene eseguito automaticamente `pip install -r <file>`. |
    | Script della shell | Usare questa opzione per indicare uno script della shell bash (in genere un file con estensione *sh*) contenente tutti i comandi da eseguire per inizializzare l'ambiente. |
    | Environment.yml | Un progetto di Python che usa Conda per la gestione di un ambiente usa un file *environments.yml* per descrivere le dipendenze. In questa opzione, selezionare il file appropriato dall'elenco di file del progetto. |

1. Per rimuovere eventuali passaggi di installazione, selezionare la **X** a destra del passaggio.

1. Quando tutti i passaggi di installazione sono presenti, selezionare **Save** (Salva). Per annullare le modifiche selezionare **Cancel** (Annulla).

1. Per testare l'ambiente creare ed eseguire un nuovo notebook, quindi creare una cella di codice con istruzioni che dipendono da un pacchetto dell'ambiente, ad esempio con l'uso di un'istruzione Python `import`. Se l'istruzione ha esito positivo, il pacchetto necessario è stato installato correttamente nell'ambiente.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Manage and configure projects in Azure Notebooks](configure-manage-azure-notebooks-projects.md) (Gestire e configurare progetti in Azure Notebooks)

> [!div class="nextstepaction"]
> [Tutorial: create an run a Jupyter notebook to do linear regression](tutorial-create-run-jupyter-notebook.md) (Esercitazione: Creare ed eseguire un notebook di Jupyter per la regressione lineare)
