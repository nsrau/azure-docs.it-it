---
title: Come eseguire Jupyter Notebook nell'area di lavoro
titleSuffix: Azure Machine Learning
description: Informazioni su come eseguire Jupyter Notebook senza uscire dall'area di lavoro in Azure Machine Learning Studio.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 06/27/2020
ms.openlocfilehash: d253699199617489947dd95a44d9bcd1eff17334
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93325409"
---
# <a name="how-to-run-jupyter-notebooks-in-your-workspace"></a>Come eseguire Jupyter Notebook nell'area di lavoro


Di seguito viene descritto come eseguire Jupyter Notebook direttamente nell'area di lavoro in Azure Machine Learning Studio. Sebbene sia possibile avviare [Jupyter](https://jupyter.org/) or [JupyterLab](https://jupyterlab.readthedocs.io), è anche possibile modificare ed eseguire i notebook senza uscire dall'area di lavoro.

Verrà descritto come:

* Creare Jupyter Notebook nell'area di lavoro
* Eseguire un esperimento da un notebook
* Modificare l'ambiente del notebook
* Trovare i dettagli delle istanze di calcolo usate per eseguire i notebook

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://aka.ms/AMLFree) prima di iniziare.
* Un'area di lavoro di Machine Learning. Vedere [Creare un'area di lavoro di Azure Machine Learning](how-to-manage-workspace.md).

## <a name="create-notebooks"></a><a name="create"></a> Creare notebook

Nell'area di lavoro di Azure Machine Learning creare un nuovo Jupyter Notebook e iniziare a lavorare. Il notebook appena creato viene archiviato nella risorsa di archiviazione dell'area di lavoro predefinita. Questo notebook può essere condiviso con chiunque abbia accesso all'area di lavoro. 

Per creare un nuovo notebook: 

1. Aprire l'area di lavoro in [Azure Machine Learning Studio](https://ml.azure.com).
1. A sinistra selezionare **Notebooks** (Notebook). 
1. Selezionare l'icona  **Create new file** (Crea nuovo file) sopra l'elenco **User files** (File utente) nella sezione **My files** (File personali).

    :::image type="content" source="media/how-to-run-jupyter-notebooks/create-new-file.png" alt-text="Create new file"::: (Crea nuovo file)

1. Assegnare un nome al file. 
1. Per Jupyter Notebook file, selezionare **notebook** come tipo di file.
1. Selezionare una directory di file.
1. Selezionare **Create** (Crea).

È anche possibile creare file di testo.  Selezionare **Text** (Testo) come tipo di file e aggiungere l'estensione al nome (ad esempio, myfile.py o myfile.txt)  

È anche possibile caricare cartelle e file, inclusi i notebook, con gli strumenti disponibili nella parte superiore della pagina Notebooks (Notebook).  I notebook e la maggior parte dei tipi di file di testo vengono visualizzati nella sezione di anteprima.  Non sono disponibili anteprime per la maggior parte degli altri tipi di file.

> [!IMPORTANT]
> Il contenuto di notebook e script può potenzialmente leggere i dati delle sessioni e accedere ai dati senza l'organizzazione in Azure.  Caricare solo file da origini attendibili. Per altre informazioni, vedere [procedure consigliate per il codice protetto](concept-secure-code-best-practice.md#azure-ml-studio-notebooks).

### <a name="clone-samples"></a>Clonare gli esempi

L'area di lavoro contiene una cartella **Samples** (Esempi) con notebook progettati per facilitare l'esplorazione dell'SDK ed essere usati come esempi per i propri progetti di Machine Learning.  È possibile clonare questi notebook nella propria cartella nel contenitore di archiviazione dell'area di lavoro.  

Per un esempio, vedere [Esercitazione: Creare il primo esperimento di Machine Learning](tutorial-1st-experiment-sdk-setup.md#azure).

### <a name="use-files-from-git-and-version-my-files"></a><a name="terminal"></a> Usare i file GIT e controllare le versioni dei file personali

È possibile accedere a tutte le operazioni di GIT usando una finestra del terminale. Tutti i file e le cartelle GIT verranno archiviati nel file system dell'area di lavoro.

> [!NOTE]
> Aggiungere i file e le cartelle in un punto qualsiasi della cartella **~/cloudfiles/code/Users** in modo che siano visibili in tutti gli ambienti Jupyter.

Per accedere al terminale:

1. Aprire l'area di lavoro in [Azure Machine Learning Studio](https://ml.azure.com).
1. A sinistra selezionare **Notebooks** (Notebook).
1. Selezionare un notebook che si trova nella sezione **User files** (File utente) sul lato sinistro.  Se non sono presenti notebook, prima [creare un notebook](#create)
1. Selezionare una destinazione di **calcolo** o crearne una nuova e attendere che sia in esecuzione.
1. Selezionare l'icona **Open terminal** (Apri terminale).

    :::image type="content" source="media/how-to-run-jupyter-notebooks/open-terminal.png" alt-text="Aprire il terminale":::

1. Se l'icona non viene visualizzata, selezionare il **...** a destra della destinazione di calcolo e quindi selezionare **Apri terminale**.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/alt-open-terminal.png" alt-text="Open terminal"::: (Apri terminale)


Vedere altre informazioni su come [clonare i repository GIT nel file system dell'area di lavoro](concept-train-model-git-integration.md#clone-git-repositories-into-your-workspace-file-system).

### <a name="copy-and-paste-in-terminal"></a>Copiare e incollare nel terminale

> * Windows: `Ctrl-Insert` per copiare e usare `Ctrl-Shift-v` o `Shift-Insert` per incollare.
> * Mac OS: `Cmd-c` per copiare e `Cmd-v` per incollare.
> * FireFox o Internet Explorer potrebbero non supportare correttamente le autorizzazioni per gli appunti.

### <a name="share-notebooks-and-other-files"></a>Condividere notebook e altri file

Copiare e incollare l'URL per condividere un notebook o un file.  Solo gli altri utenti dell'area di lavoro possono accedere a questo URL.  Vedere altre informazioni su come [concedere l'accesso all'area di lavoro](how-to-assign-roles.md).

## <a name="edit-a-notebook"></a>Modificare un notebook

Per modificare un notebook, aprire un notebook nella sezione **User files** (File utente) dell'area di lavoro. Fare clic sulla cella che si vuole modificare. 

È possibile modificare il notebook senza connettersi a un'istanza di calcolo.  Quando si desidera eseguire le celle nel notebook, selezionare o creare un'istanza di calcolo.  Se si seleziona un'istanza di calcolo arrestata, questa verrà avviata automaticamente quando si esegue la prima cella.

Quando un'istanza di calcolo è in esecuzione, è anche possibile usare il completamento del codice, alimentato da [IntelliSense](https://code.visualstudio.com/docs/editor/intellisense), in qualsiasi notebook di Python.

È anche possibile avviare Jupyter o JupyterLab dalla barra degli strumenti del notebook.  Azure Machine Learning non offre aggiornamenti e correzioni di bug di Jupyter o JupyterLab poiché sono prodotti open source non coperti dal supporto tecnico Microsoft.

### <a name="focus-mode"></a>Modalità messa a fuoco

Usare la modalità messa a fuoco per espandere la visualizzazione corrente in modo da potersi concentrare sulle schede attive. La modalità messa a fuoco nasconde Esplora file dei notebook.

1. Nella barra degli strumenti della finestra del terminale selezionare **modalità messa a fuoco** per attivare la modalità messa a fuoco. A seconda della larghezza della finestra, è possibile che si trovi nella voce di menu **..** . nella barra degli strumenti.
1. In modalità messa a fuoco tornare alla visualizzazione standard selezionando **visualizzazione standard**.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/focusmode.gif" alt-text="Attiva/Nascondi modalità messa a fuoco/visualizzazione standard":::


### <a name="use-intellisense"></a>Usare IntelliSense

[IntelliSense](https://code.visualstudio.com/docs/editor/intellisense) è un supporto per il completamento del codice che include numerose funzionalità: Elenca membri, informazioni sul parametro, informazioni rapide e completa parola. Queste funzionalità consentono di acquisire altre informazioni sul codice in uso, tengono traccia dei parametri che si digitano e aggiungono le chiamate a proprietà e metodi con poche sequenze di tasti.  

Quando si digita il codice, premere CTRL + barra spaziatrice per attivare IntelliSense.

### <a name="clean-your-notebook-preview"></a>Pulire il notebook (anteprima)

> [!IMPORTANT]
> La funzionalità di raccolta è attualmente disponibile in anteprima pubblica.
> La versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Nel corso della creazione di un notebook, in genere si finisce con le celle usate per l'esplorazione o il debug dei dati. La funzionalità di *raccolta* consente di creare un notebook pulito senza queste celle estranee.

1. Eseguire tutte le celle del notebook.
1. Selezionare la cella che contiene il codice che si desidera venga eseguito dal nuovo notebook. Ad esempio, il codice che invia un esperimento o forse il codice che registra un modello.
1. Selezionare l'icona **gather** visualizzata sulla barra degli strumenti della cella.
    :::image type="content" source="media/how-to-run-jupyter-notebooks/gather.png" alt-text="Screenshot: selezionare l'icona di raccolta":::
1. Immettere il nome del nuovo notebook "raccolto".  

Il nuovo notebook contiene solo celle di codice, con tutte le celle necessarie per produrre gli stessi risultati della cella selezionata per la raccolta.

### <a name="save-and-checkpoint-a-notebook"></a>Salvare e Checkpoint un notebook

Azure Machine Learning crea un file del checkpoint quando si crea un file *ipynb* .

Nella barra degli strumenti del notebook selezionare il menu e quindi **file &gt; Salva e Checkpoint** per salvare manualmente il notebook. verrà aggiunto un file del checkpoint associato al notebook.

:::image type="content" source="media/how-to-run-jupyter-notebooks/file-save.png" alt-text="Screenshot dello strumento Salva nella barra degli strumenti del notebook":::

Ogni notebook viene salvato in automatico ogni 30 secondi. Salvataggio automatico aggiorna solo il file *ipynb* iniziale, non il file del checkpoint.
 
Selezionare **Checkpoint** dal menu notebook per creare un checkpoint denominato e ripristinare il blocco appunti a un checkpoint salvato.


### <a name="useful-keyboard-shortcuts"></a>Tasti di scelta rapida

|Tastiera  |Azione  |
|---------|---------|
|MAIUSC+INVIO     |  Eseguire una cella       |
|CTRL+SPAZIO | Attiva IntelliSense |
|CTRL+M (Windows)     |  Abilitare/disabilitare il trapping delle schede nel notebook.       |
|CTRL+MAIUSC+M (Mac e Linux)     |    Abilitare/disabilitare il trapping tramite TAB nel notebook.     |
|TAB (quando il trapping tramite TAB è abilitato) | Aggiungere un carattere '\t' (rientro)
|TAB (quando il trapping tramite TAB è disabilitato) | Passare lo stato attivo al successivo elemento attivabile (pulsante Elimina cella, pulsante Esegui e così via)

## <a name="delete-a-notebook"></a>Eliminare un notebook

*Non è possibile* eliminare i notebook della cartella **Samples** (Esempi).  Questi notebook fanno parte di Studio e vengono aggiornati ogni volta che viene pubblicato un nuovo SDK.  

*È possibile* eliminare i notebook della cartella **User files** (File utente) in uno dei modi seguenti:

* In Studio selezionare **...** alla fine di una cartella o un file.  Assicurarsi di usare un browser supportato (Microsoft Edge, Chrome o Firefox).
* Dalla barra degli strumenti di un notebook selezionare [**Open terminal**](#terminal) (Apri terminale) per accedere alla finestra del terminale per l'istanza di calcolo.
* In Jupyter o JupyterLab con i relativi strumenti.

## <a name="run-an-experiment"></a>Eseguire un esperimento

Per eseguire un esperimento da un notebook, connettersi prima a un'[istanza di calcolo](concept-compute-instance.md) in esecuzione. Se non è presente un'istanza di calcolo, seguire questa procedura per crearne una: 

1. Selezionare **+** nella barra degli strumenti del notebook. 
2. Assegnare un nome al calcolo e scegliere le **dimensioni della macchina virtuale**. 
3. Selezionare **Create** (Crea).
4. L'istanza di calcolo viene connessa automaticamente al notebook ed è ora possibile eseguire le celle.

È possibile visualizzare e usare solo le istanze di calcolo create.  I file della cartella **User files** (File utente) vengono archiviati separatamente dalla macchina virtuale e sono condivisi tra tutte le istanze di calcolo nell'area di lavoro.

### <a name="view-logs-and-output"></a>Visualizzare i log e l'output

Usare i [widget del notebook](/python/api/azureml-widgets/azureml.widgets?preserve-view=true&view=azure-ml-py) per visualizzare lo stato di avanzamento dell'esecuzione e i log. Il widget è asincrono e offre gli aggiornamenti fino al termine del training. I widget di Azure Machine Learning sono supportati anche in Jupyter e JupterLab.

## <a name="change-the-notebook-environment"></a>Modificare l'ambiente del notebook

La barra degli strumenti del notebook consente di modificare l'ambiente in cui viene eseguito il notebook.  

Queste azioni non modificheranno lo stato del notebook o i valori delle variabili nel notebook:

|Azione  |Risultato  |
|---------|---------| --------|
|Arrestare il kernel     |  Vengono arrestate tutte le celle in esecuzione. Quando si esegue una cella, il kernel viene riavviato automaticamente. |
|Passare a un'altra sezione dell'area di lavoro     |     Vengono arrestate le celle in esecuzione. |

Queste azioni reimposteranno lo stato del notebook e ripristineranno tutte le variabili nel notebook.

|Azione  |Risultato  |
|---------|---------| --------|
| Cambiare il kernel | Il notebook usa il nuovo kernel |
| Cambiare l'ambiente di calcolo    |     Il notebook usa automaticamente il nuovo calcolo. |
| Reimpostare il calcolo | Viene riavviato quando si tenta di eseguire una cella |
| Arrestare il calcolo     |    Non vengono eseguite celle  |
| Aprire il notebook in Jupyter o JupyterLab     |    Il notebook viene aperto in una nuova scheda.  |

### <a name="add-new-kernels"></a>Aggiungere nuovi kernel

Il notebook rileverà automaticamente tutti i kernel Jupyter installati nell'istanza di calcolo connessa.  Per aggiungere un kernel all'istanza di calcolo:

1. Selezionare [**Open terminal**](#terminal) (Apri terminale) nella barra degli strumenti del notebook.
1. Usare la finestra del terminale per creare un nuovo ambiente.  Il codice seguente, ad esempio, crea `newenv` :
    ```shell
    conda create -y --name newenv
    ```
1. Attivare l'ambiente.  Ad esempio, dopo aver creato `newenv`:

    ```shell
    conda activate newenv
    ```
1. Installare il pacchetto PIP e ipykernel nel nuovo ambiente e creare un kernel per la conda ENV

    ```shell
    conda install -y pip
    conda install -y ipykernel
    python -m ipykernel install --user --name newenv --display-name "Python (newenv)"
    ```

> [!NOTE]
> Per la gestione dei pacchetti in un notebook, usare le funzioni **% PIP** o **% conda** Magic per installare automaticamente i pacchetti nel **kernel attualmente in esecuzione** , anziché **! PIP** o **! conda** , che fa riferimento a tutti i pacchetti (inclusi i pacchetti all'esterno del kernel attualmente in esecuzione)

È possibile installare uno dei [kernel Jupyter disponibili](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels).

### <a name="status-indicators"></a>Indicatori di stato

Un indicatore accanto all'elenco a discesa **Compute** (Calcolo) mostra lo stato del calcolo.  Lo stato è visualizzato anche nell'elenco a discesa.  

|Colore |Stato del calcolo |
|---------|---------| 
| Green | Esecuzione del calcolo |
| Rosso |Calcolo non riuscito | 
| Nero | Calcolo arrestato |
|  Blu chiaro |Creazione, avvio, riavvio, configurazione del calcolo |
|  Grigio |Eliminazione, arresto del calcolo |

Un indicatore accanto all'elenco a discesa **Kernel** mostra lo stato del kernel.

|Colore |Stato del kernel |
|---------|---------|
|  Green |Kernel connesso, inattivo, occupato|
|  Grigio |Kernel non connesso |

## <a name="find-compute-details"></a>Dettagli del calcolo 

I dettagli sulle istanze di calcolo sono disponibili nella pagina **Compute** (Calcolo) in [Studio](https://ml.azure.com).

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire il primo esperimento](tutorial-1st-experiment-sdk-train.md)
* [Eseguire il backup dell'archiviazione file con gli snapshot](../storage/files/storage-snapshots-files.md)