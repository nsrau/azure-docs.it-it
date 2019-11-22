---
title: Creare e clonare notebook di Jupyter in Azure
description: I progetti di Azure Notebooks gestiscono una raccolta di notebook e i file correlati, che è possibile creare ex novo o clonare da un'altra origine.
ms.topic: article
ms.date: 02/25/2019
ms.openlocfilehash: fc5425312637710f0b9f94493b8cfb4a48582236
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277569"
---
# <a name="create-and-clone-projects"></a>Creare e clonare progetti

Azure Notebooks organizza i notebook di Jupyter e i file correlati in gruppi logici denominati *progetti*. Prima si crea un progetto come un contenitore e quindi si crea o si clona uno o più notebook all'interno di una cartella insieme ad altri file di progetto. (Questo processo è illustrato in questa [esercitazione](tutorial-create-run-jupyter-notebook.md).)

Un progetto gestisce anche i metadati e altre impostazioni di configurazione relative al server su cui sono in esecuzione i notebook, inclusi i passaggi di configurazione personalizzati e l'installazione dei pacchetti. Per altre informazioni, vedere [Gestire e configurare progetti](configure-manage-azure-notebooks-projects.md).

## <a name="use-the-my-projects-dashboard"></a>Usare il dashboard My Projects (Progetti personali)

Nel dashboard **My Projects** (Progetti personali) all'indirizzo `https://notebooks.azure.com/<userID>/projects` è possibile visualizzare, gestire e creare progetti:

[![Dashboard My Projects (Progetti personali) in Azure Notebooks](media/my-projects-dashboard.png)](media/my-projects-dashboard.png#lightbox)

Le operazioni che è possibile eseguire in un dashboard variano a seconda che si sia eseguito o meno l'accesso con l'account proprietario dell'ID utente:

| Comando | Disponibile per | DESCRIZIONE |
| --- | --- | --- |
| **Run** | Proprietario | Avvia il server di progetto e apre la cartella di progetto in Jupyter. In genere, prima si accede alla cartella del progetto e quindi si avvia un notebook da questa posizione. |
| **Scaricare** | Chiunque | Scarica una copia del progetto selezionato come file ZIP. |
| **Share** (Condividi) | Chiunque | Visualizza la finestra popup di condivisione mediante la quale è possibile ottenere l'URL di un progetto, condividerlo sui social media, inviare un messaggio di posta elettronica con l'URL e ottenere il codice HTML o Markdown con un badge "notebook di avvio" (vedere [Ottenere un badge di avvio](#obtain-a-launch-badge)) con l'URL. |
| **Eliminazione** | Proprietario | Elimina il progetto selezionato. Questa operazione non può essere annullata. |
| **Terminale** | Proprietario | Avvia il server del progetto e quindi apre una nuova finestra del browser con il terminale Bash per il server. |
| **+ New Project** (Nuovo progetto) | Proprietario | Crea un nuovo progetto. Vedere [Creare un nuovo progetto](#create-a-new-project). |
| **Upload GitHub Repo** (Carica repository GitHub) | Proprietario | Importa un progetto da GitHub. [Importare un progetto da GitHub](#import-a-project-from-github). |
| **Clone** (Clona) | Chiunque | Copia un progetto selezionato nel proprio account. Se necessario, richiede di eseguire l'accesso. Vedere [Clonare un progetto](#clone-a-project). |

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
| ID progetto | Identificatore personalizzato che diventa parte dell'URL usato per condividere un progetto (il formato è `https://notebooks.azure.com/<user_id>/projects/<project_id>`). Questo ID può utilizzare solo lettere, numeri e trattini, è limitato a 30 caratteri e non può essere un [ID di progetto riservato](#reserved-project-ids). Se si hanno subbi sul tipo di ID da usare, in genere si sceglie la versione in caratteri minuscoli del nome del progetto convertendo gli spazi in segni meno, ad esempio "my-notebook-project" (troncato, se necessario per rispettare i limiti di lunghezza). |
| Pubblico | Se impostato, consente a chiunque disponga del collegamento di accedere al progetto. Quando si crea un progetto privato, deselezionare questa opzione. |
| Initialize this project with a README (Inizializzare il progetto con un file Leggimi) | Se impostato, nel progetto viene creato un file *README.md* predefinito. Nel file *README.md* predefinito è possibile specificare la documentazione per il progetto, se lo si desidera. |

### <a name="reserved-project-ids"></a>ID progetto riservati

Le seguenti parole riservate non possono essere utilizzate da soli come ID progetto. Queste parole riservate possono tuttavia essere usate come parte degli ID di progetto più lunghi.

| | | | | | |
| --- | --- | --- | --- | --- | --- |
| info | account | amministrazione | api | Blog | Aula |
| content | dashboard | esplorare | Domande frequenti | help | html |
| home | import | library | management | nuovo | Notebook |
| Notebooks | PDF | preview | prezzi | Profilo | ricerca |
| status | support | Test | | | |

Se si tenta di usare una di queste parole come ID progetto, i popup **Crea nuovo progetto** e **Impostazioni progetto** indicano che "ID libreria è un identificatore riservato".

Poiché un ID progetto fa anche parte dell'URL di un progetto, il software ad Blocker potrebbe bloccare l'uso di determinate parole chiave, ad esempio "annuncio". In questi casi, usare una parola diversa nell'ID progetto.

## <a name="import-a-project-from-github"></a>Importare un progetto da GitHub

È possibile importare facilmente un intero repository GitHub pubblico come un progetto, comprendente tutti i dati e i file *README.md*. Usare il comando **Upload GitHub Repo** (Carica repository GitHub), specificare i dettagli seguenti nella finestra popup e quindi selezionare **Import** (Importa):

| Campo | DESCRIZIONE |
| --- | --- |
| GitHub repository (Repository GitHub) | Nome del repository di origine in github.com. Ad esempio, per clonare i notebook di Jupyter per Servizi cognitivi di Azure all'indirizzo [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks), immettere "Microsoft/cognitive-services-notebooks".  |
| Clone recursively (Clona in modo ricorsivo) | I repository GitHub possono contenere più repository figlio. Impostare questa opzione se si vuole clonare il repository padre e tutti gli elementi figlio. Poiché un repository può avere molti figli, lasciare questa opzione deselezionata se non si è certi che sia necessaria. |
| Project name (Nome progetto) | Nome descrittivo del progetto usato da Azure Notebooks per scopi di visualizzazione. |
| ID progetto | Identificatore personalizzato che diventa parte dell'URL usato per condividere un progetto (il formato è `https://notebooks.azure.com/<user_id>/projects/<project_id>`). Questo ID può utilizzare solo lettere, numeri e trattini, è limitato a 30 caratteri e non può essere un [ID di progetto riservato](#reserved-project-ids). Se si hanno subbi sul tipo di ID da usare, in genere si sceglie la versione in caratteri minuscoli del nome del progetto convertendo gli spazi in segni meno, ad esempio "my-notebook-project" (troncato, se necessario per rispettare i limiti di lunghezza). |
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

- [Explore sample notebooks (Esplorare i notebook di esempio)](azure-notebooks-samples.md)
- [Procedura: configurare e gestire i progetti](configure-manage-azure-notebooks-projects.md)
- [Procedura: installare pacchetti dall'interno di un notebook](install-packages-jupyter-notebook.md)
- [Procedura: presentare una presentazione](present-jupyter-notebooks-slideshow.md)
- [Procedura: utilizzare file di dati](work-with-project-data-files.md)
- [Procedura: accedere alle risorse di dati](access-data-resources-jupyter-notebooks.md)
- [Procedura: utilizzare Azure Machine Learning](use-machine-learning-services-jupyter-notebooks.md)
