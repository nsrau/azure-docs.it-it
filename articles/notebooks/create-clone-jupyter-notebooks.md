---
title: Creare e clonare notebook di Jupyter in Azure
description: I progetti di Azure Notebooks gestiscono una raccolta di notebook e i file correlati, che è possibile creare ex novo o clonare da un'altra origine.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 9b6a49e2-1d71-4c0b-9e5d-16e059427e38
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2019
ms.author: kraigb
ms.openlocfilehash: 0ee0c7162e26b875c74796b6d5379b414981e2d5
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59282322"
---
# <a name="create-and-clone-projects"></a>Creare e clonare progetti

Azure Notebooks organizza i notebook di Jupyter e i file correlati in gruppi logici denominati *progetti*. Prima si crea un progetto come un contenitore e quindi si crea o si clona uno o più notebook all'interno di una cartella insieme ad altri file di progetto. (Questo processo è illustrato in questa [esercitazione](tutorial-create-run-jupyter-notebook.md).)

Un progetto gestisce anche i metadati e altre impostazioni di configurazione relative al server su cui sono in esecuzione i notebook, inclusi i passaggi di configurazione personalizzati e l'installazione dei pacchetti. Per altre informazioni, vedere [Gestire e configurare progetti](configure-manage-azure-notebooks-projects.md).

## <a name="use-the-my-projects-dashboard"></a>Usare il dashboard My Projects (Progetti personali)

Nel dashboard **My Projects** (Progetti personali) all'indirizzo `https://notebooks.azure.com/<userID>/projects` è possibile visualizzare, gestire e creare progetti:

[![Mdashboard di progetti y nei notebook di Azure](media/my-projects-dashboard.png)](media/my-projects-dashboard.png#lightbox)

Le operazioni che è possibile eseguire in un dashboard variano a seconda che si sia eseguito o meno l'accesso con l'account proprietario dell'ID utente:

| Comando | Disponibile per | DESCRIZIONE |
| --- | --- | --- |
| **Esegui** | Proprietario | Avvia il server di progetto e apre la cartella di progetto in Jupyter. In genere, prima si accede alla cartella del progetto e quindi si avvia un notebook da questa posizione. |
| **Download** | Chiunque | Scarica una copia del progetto selezionato come file ZIP. |
| **Condividi** | Chiunque | Visualizza la finestra popup di condivisione mediante la quale è possibile ottenere l'URL di un progetto, condividerlo sui social media, inviare un messaggio di posta elettronica con l'URL e ottenere il codice HTML o Markdown con un badge "notebook di avvio" (vedere [Ottenere un badge di avvio](#obtain-a-launch-badge)) con l'URL. |
| **Delete** | Proprietario | Elimina il progetto selezionato. Questa operazione non può essere annullata. |
| **Terminale** | Proprietario | Avvia il server del progetto e quindi apre una nuova finestra del browser con il terminale Bash per il server. |
| **+ Nuovo progetto** | Proprietario | Crea un nuovo progetto. Vedere [Creare un nuovo progetto](#create-a-new-project). |
| **Caricare i repository di GitHub** | Proprietario | Importa un progetto da GitHub. [Importare un progetto da GitHub](#import-a-project-from-github). |
| **Clone** | Chiunque | Copia un progetto selezionato nel proprio account. Se necessario, richiede di eseguire l'accesso. Vedere [Clonare un progetto](#clone-a-project). |

### <a name="obtain-a-launch-badge"></a>Ottenere un badge di avvio

Quando si usa il comando **Share** (Condividi) e si seleziona la scheda **Embed** (Incorpora), è possibile copiare il codice HTML o Markdown che crea un badge "notebook di avvio":

![Badge "Notebook di avvio"](https://notebooks.azure.com/launch.png)

Se non si dispone di un progetto di Azure Notebooks, è possibile creare un collegamento che consente di clonare direttamente da GitHub usando i modelli seguenti e inserendo il nome utente e i nomi dei repository appropriati:

```html
<a href="https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>"><img src="https://notebooks.azure.com/launch.png" /></a>
```

```markdown
[![Azure Notebooks](https://notebooks.azure.com/launch.png)](https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>)
```

## <a name="create-a-new-project"></a>Creare un nuovo progetto

Quando si usa il comando **+ New Project** (+ Nuovo progetto), Azure Notebooks consente di visualizzare una finestra popup **Create New Project** (Crea nuovo progetto). In questa finestra popup immettere le informazioni seguenti e quindi selezionare **Create** (Crea):

| Campo | DESCRIZIONE |
| --- | --- |
| Project name (Nome progetto) | Nome descrittivo del progetto usato da Azure Notebooks per scopi di visualizzazione. Ad esempio, "My Notebook Project". |
| ID progetto | Identificatore personalizzato che diventa parte dell'URL usato per condividere un progetto (il formato è `https://notebooks.azure.com/<user_id>/projects/<project_id>`). Questo ID può usare solo lettere, numeri e trattini, è limitato a 30 caratteri e non può essere un' [riservato ID progetto](#reserved-project-ids). Se si hanno subbi sul tipo di ID da usare, in genere si sceglie la versione in caratteri minuscoli del nome del progetto convertendo gli spazi in segni meno, ad esempio "my-notebook-project" (troncato, se necessario per rispettare i limiti di lunghezza). |
| Pubblico | Se impostato, consente a chiunque disponga del collegamento di accedere al progetto. Quando si crea un progetto privato, deselezionare questa opzione. |
| Initialize this project with a README (Inizializzare il progetto con un file Leggimi) | Se impostato, nel progetto viene creato un file *README.md* predefinito. Nel file *README.md* predefinito è possibile specificare la documentazione per il progetto, se lo si desidera. |

### <a name="reserved-project-ids"></a>Gli ID di progetto riservato

Le seguenti parole riservate non possono essere utilizzate da soli come progetto di ID. Queste parole riservate tuttavia, può essere usato come parte dell'ID di progetto più lungo.

| | | | | | |
| --- | --- | --- | --- | --- | --- |
| about | account | Amministrazione | api | blog | Classroom |
| content | dashboard | Esplorare | faq | help | html |
| home | import | library | management | Nuovo | taccuino |
| Notebook | pdf | preview | prezzi | Profilo | ricerca |
| status | support | test | | | |

Se si prova a usare una di queste parole come ID di progetto, il **Crea nuovo progetto** e **le impostazioni del progetto** indicano i popup, "id di libreria è un identificatore riservato".

Perché un ID progetto fa anche parte dell'URL di un progetto, il software di blocco ad potrebbe bloccare l'uso di determinate parole chiave, ad esempio "annuncio". In questi casi, usare un'altra parola nell'ID di progetto.

## <a name="import-a-project-from-github"></a>Importare un progetto da GitHub

È possibile importare facilmente un intero repository GitHub pubblico come un progetto, comprendente tutti i dati e i file *README.md*. Usare il comando **Upload GitHub Repo** (Carica repository GitHub), specificare i dettagli seguenti nella finestra popup e quindi selezionare **Import** (Importa):

| Campo | DESCRIZIONE |
| --- | --- |
| GitHub repository (Repository GitHub) | Nome del repository di origine in github.com. Ad esempio, per clonare i notebook di Jupyter per Servizi cognitivi di Azure all'indirizzo [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks), immettere "Microsoft/cognitive-services-notebooks".  |
| Clone recursively (Clona in modo ricorsivo) | I repository GitHub possono contenere più repository figlio. Impostare questa opzione se si vuole clonare il repository padre e tutti gli elementi figlio. Poiché un repository può avere molti figli, lasciare questa opzione deselezionata se non si è certi che sia necessaria. |
| Project name (Nome progetto) | Nome descrittivo del progetto usato da Azure Notebooks per scopi di visualizzazione. |
| ID progetto | Identificatore personalizzato che diventa parte dell'URL usato per condividere un progetto (il formato è `https://notebooks.azure.com/<user_id>/projects/<project_id>`). Questo ID può usare solo lettere, numeri e trattini, è limitato a 30 caratteri e non può essere un' [riservato ID progetto](#reserved-project-ids). Se si hanno subbi sul tipo di ID da usare, in genere si sceglie la versione in caratteri minuscoli del nome del progetto convertendo gli spazi in segni meno, ad esempio "my-notebook-project" (troncato, se necessario per rispettare i limiti di lunghezza). |
| Pubblico | Se impostato, consente a chiunque disponga del collegamento di accedere al progetto. Quando si crea un progetto privato, deselezionare questa opzione. |

L'importazione di un repository da GitHub importa anche la relativa cronologia. È possibile usare i comandi Git standard del terminale per eseguire il commit delle nuove modifiche, eseguire il pull delle modifiche da GitHub e così via.

## <a name="clone-a-project"></a>Clonare un progetto

La clonazione crea una copia di un progetto esistente nell'account personale, in cui è possibile eseguire e modificare qualsiasi notebook o file del progetto. È possibile usare la clonazione anche per creare copie di progetti personali in cui eseguire esperimenti o altre operazioni senza interferire con il progetto originale.

Per clonare un progetto:

1. Nel dashboard **My Projects** (Progetti personali) fare clic sul progetto desiderato e selezionare **Clone** (Clona) (tasto di scelta rapida: c).

    ![Comando Clone (Clona) nel menu di scelta rapida del progetto](media/clone-command.png)

1. Nella finestra popup **Clone Project** (Clona progetto) immettere un nome e un ID per il clone e specificare se il clone è pubblico. Queste impostazioni sono identiche a quelle di un [nuovo progetto](#create-a-new-project).

    ![Finestra popup Clone Project (Clona progetto)](media/clone-project.png)

1. Dopo aver selezionato il pulsante **Clone** (Clona), Azure Notebooks passa direttamente alla copia.

## <a name="next-steps"></a>Passaggi successivi

- [Scopri i notebook di esempio](azure-notebooks-samples.md)
- [Procedura: Configurare e gestire i progetti](configure-manage-azure-notebooks-projects.md)
- [Procedura: Installare pacchetti da un notebook](install-packages-jupyter-notebook.md)
- [Procedura: Eseguire una presentazione](present-jupyter-notebooks-slideshow.md)
- [Procedura: Usare i file di dati](work-with-project-data-files.md)
- [Procedura: Accedere alle risorse dati](access-data-resources-jupyter-notebooks.md)
- [Procedura: Usare Azure Machine Learning Services](use-machine-learning-services-jupyter-notebooks.md)
