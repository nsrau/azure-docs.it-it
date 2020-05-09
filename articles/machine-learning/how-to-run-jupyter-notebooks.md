---
title: Come eseguire Jupyter notebook nell'area di lavoro
titleSuffix: Azure Machine Learning
description: Informazioni su come eseguire un Jupyter Notebook senza uscire dall'area di lavoro in Azure Machine Learning Studio.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 67da2cb31d59838bb3ad2b964530d85d8be9be4c
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82783656"
---
# <a name="how-to-run-jupyter-notebooks-in-your-workspace"></a>Come eseguire Jupyter notebook nell'area di lavoro
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Informazioni su come eseguire i notebook di Jupyter direttamente nell'area di lavoro in Azure Machine Learning Studio. Sebbene sia possibile avviare [Jupyter](https://jupyter.org/) o [JupyterLab](https://jupyterlab.readthedocs.io), è anche possibile modificare ed eseguire i notebook senza uscire dall'area di lavoro.

Vedere come è possibile:

* Creare notebook di Jupyter nell'area di lavoro
* Eseguire un esperimento da un notebook
* Modificare l'ambiente del notebook
* Trovare i dettagli delle istanze di calcolo usate per eseguire i notebook

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://aka.ms/AMLFree) prima di iniziare.
* Un'area di lavoro di Machine Learning. Vedere [Creare un'area di lavoro di Azure Machine Learning](how-to-manage-workspace.md).

## <a name="create-notebooks"></a><a name="create"></a>Creare notebook

Nell'area di lavoro Azure Machine Learning creare un nuovo notebook di Jupyter e iniziare a lavorare. Il notebook appena creato viene archiviato nell'archivio dell'area di lavoro predefinito. Questo notebook può essere condiviso con chiunque abbia accesso all'area di lavoro. 

Per creare un nuovo notebook: 

1. Aprire l'area di lavoro in [Azure Machine Learning Studio](https://ml.azure.com).
1. Sul lato sinistro selezionare **Notebooks (blocchi appunti**). 
1. Selezionare l'icona **Crea nuovo file** sopra l'elenco **file utente** nella sezione **file personali** .

    :::image type="content" source="media/how-to-run-jupyter-notebooks/create-new-file.png" alt-text="Crea nuovo file":::

1. Assegnare un nome al file. 
1. Per Jupyter Notebook file, selezionare **Python notebook** come tipo di file.
1. Selezionare una directory di file.
1. Selezionare **Crea**.

> [!TIP]
> È anche possibile creare file di testo.  Selezionare **testo** come tipo di file e aggiungere l'estensione al nome (ad esempio, MyFile.py o MyFile. txt)  

È anche possibile caricare cartelle e file, inclusi i notebook, con gli strumenti disponibili nella parte superiore della pagina dei notebook.  I notebook e la maggior parte dei tipi di file di testo vengono visualizzati nella sezione anteprima.  Non sono disponibili anteprime per la maggior parte degli altri tipi di file.

### <a name="clone-samples"></a>Esempi di clonazione

L'area di lavoro contiene una cartella **Samples** con notebook progettati per facilitare l'esplorazione dell'SDK e fungere da esempio per i propri progetti di machine learning.  È possibile clonare questi notebook nella propria cartella nel contenitore di archiviazione dell'area di lavoro.  

Per un esempio, vedere [esercitazione: creare il primo esperimento ml](tutorial-1st-experiment-sdk-setup.md#azure).

### <a name="a-nameterminal-use-files-from-git-and-version-my-files"></a><a name="terminal">Usare i file da git e versione file personali

È possibile accedere a tutte le operazioni git usando una finestra del terminale. Tutti i file e le cartelle git verranno archiviati nell'area di lavoro file system.

> [!NOTE]
> Aggiungere i file e le cartelle in un punto qualsiasi della cartella **~/cloudfiles/code/Users** in modo che siano visibili in tutti gli ambienti Jupyter.

Per accedere al terminale:

1. Aprire l'area di lavoro in [Azure Machine Learning Studio](https://ml.azure.com).
1. Sul lato sinistro selezionare **Notebooks (blocchi appunti**).
1. Selezionare un notebook che si trova nella sezione **file utente** sul lato sinistro.  Se non sono presenti notebook, creare prima di tutto [un notebook](#create)
1. Selezionare una destinazione di **calcolo** o crearne una nuova e attendere che sia in esecuzione.
1. Selezionare l'icona **Apri terminale** .

    :::image type="content" source="media/how-to-run-jupyter-notebooks/open-terminal.png" alt-text="Aprire il terminale":::

1. Se l'icona non viene visualizzata, selezionare il **...** a destra della destinazione di calcolo e quindi selezionare **Apri terminale** .

    :::image type="content" source="media/how-to-run-jupyter-notebooks/alt-open-terminal.png" alt-text="Apri terminale da...":::


Altre informazioni sulla [clonazione di repository git nell'area di lavoro file System](concept-train-model-git-integration.md#clone-git-repositories-into-your-workspace-file-system).


### <a name="share-notebooks-and-other-files"></a>Condividi notebook e altri file

Copiare e incollare l'URL per condividere un notebook o un file.  Solo gli altri utenti dell'area di lavoro saranno in grado di accedere a questo URL.  Altre informazioni sulla [concessione dell'accesso all'area di lavoro](how-to-assign-roles.md).

## <a name="edit-a-notebook"></a>Modificare un notebook

Per modificare un notebook, aprire tutti i notebook presenti nella sezione **file utente** dell'area di lavoro. Fare clic sulla cella che si desidera modificare. 

Quando è in esecuzione un'istanza di calcolo in esecuzione, è anche possibile usare il completamento del codice, alimentato da [IntelliSense](https://code.visualstudio.com/docs/editor/intellisense), in qualsiasi notebook di Python.

È anche possibile avviare Jupyter o JupyterLab dalla barra degli strumenti del notebook.  Azure Machine Learning non fornisce aggiornamenti e corregge i bug da Jupyter o JupyterLab perché sono prodotti open source al di fuori del limite di supporto tecnico Microsoft.

### <a name="useful-keyboard-shortcuts"></a>Tasti di scelta rapida utili

|Tastiera  |Azione  |
|---------|---------|
|MAIUSC+INVIO     |  Eseguire una cella       |
|CTRL + M (Windows)     |  Abilitare/disabilitare la cattura di schede nel notebook.       |
|CTRL + MAIUSC + M (Mac & Linux)     |    Abilitare/disabilitare la cattura di schede nel notebook.     |
|Tab (quando il trap di tabulazione è abilitato) | Aggiungere un carattere ' \t ' (rientro)
|Tab (quando il trap di tabulazione è disabilitato) | Imposta lo stato attivo sul successivo elemento attivabile (Elimina pulsante cella, Esegui pulsante e così via)

## <a name="delete-a-notebook"></a>Eliminare un notebook

*Non è possibile* eliminare i notebook degli **esempi** .  Questi notebook fanno parte di studio e vengono aggiornati ogni volta che viene pubblicato un nuovo SDK.  

È *possibile* eliminare **i notebook dei file utente** nei modi seguenti:

* In Studio selezionare il **...** alla fine di una cartella o di un file.  Assicurarsi di usare un browser supportato (Microsoft Edge, Chrome o Firefox).
* Da qualsiasi barra degli strumenti del notebook selezionare [**Apri terminale**](#terminal) per accedere alla finestra del terminale per l'istanza di calcolo.
* In Jupyter o JupyterLab con i relativi strumenti.

## <a name="run-an-experiment"></a>Eseguire un esperimento

Per eseguire un esperimento da un notebook, è prima di tutto necessario connettersi a un' [istanza di calcolo](concept-compute-instance.md)in esecuzione. Se non si dispone di un'istanza di calcolo, attenersi alla procedura seguente per crearne una: 

1. Selezionare **+** sulla barra degli strumenti del notebook. 
2. Denominare il calcolo e scegliere le **dimensioni della macchina virtuale**. 
3. Selezionare **Crea**.
4. L'istanza di calcolo è connessa automaticamente al notebook ed è ora possibile eseguire le celle.

È possibile visualizzare e usare solo le istanze di calcolo create.  I **file utente** vengono archiviati separatamente dalla macchina virtuale e sono condivisi tra tutte le istanze di calcolo nell'area di lavoro.

### <a name="view-logs-and-output"></a>Visualizzare i log e l'output

Usare i [widget del notebook](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) per visualizzare lo stato di avanzamento dell'esecuzione e dei log. Un widget è asincrono e fornisce aggiornamenti fino al termine del training. I widget Azure Machine Learning sono supportati anche in Jupyter e JupterLab.

## <a name="change-the-notebook-environment"></a>Modificare l'ambiente del notebook

La barra degli strumenti del notebook consente di modificare l'ambiente in cui viene eseguito il notebook.  

Queste azioni non modificheranno lo stato del notebook o i valori di tutte le variabili nel notebook:

|Azione  |Risultato  |
|---------|---------| --------|
|Arrestare il kernel     |  Arresta tutte le celle in esecuzione. Quando si esegue una cella, il kernel viene riavviato automaticamente. |
|Passare a un'altra sezione dell'area di lavoro     |     Le celle in esecuzione sono arrestate. |

Queste azioni reimposteranno lo stato del notebook e ripristineranno tutte le variabili nel notebook.

|Azione  |Risultato  |
|---------|---------| --------|
| Modificare il kernel | Notebook usa il nuovo kernel |
| Cambia calcolo    |     Notebook usa automaticamente il nuovo calcolo. |
| Reimposta calcolo | Viene riavviato quando si tenta di eseguire una cella |
| Arresta calcolo     |    Non vengono eseguite celle  |
| Apri Notebook in Jupyter o JupyterLab     |    Notebook aperto in una nuova scheda.  |

### <a name="add-new-kernels"></a>Aggiungi nuovi kernel

Il notebook troverà automaticamente tutti i kernel Jupyter installati nell'istanza di calcolo connessa.  Per aggiungere un kernel all'istanza di calcolo:

1. Selezionare [**Apri terminale**](#terminal) sulla barra degli strumenti del notebook.
1. Usare la finestra del terminale per creare un nuovo ambiente.
1. Attivare l'ambiente.  Ad esempio, dopo aver `newenv`creato:

    ```shell
    source activate newenv
    python -m ipykernel install --user --name newenv --display-name "Python (newenv)"
    ```

È possibile installare tutti i [kernel Jupyter disponibili](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels) .

### <a name="status-indicators"></a>Indicatori di stato

Un indicatore accanto all'elenco a discesa **calcolo** ne mostra lo stato.  Lo stato viene visualizzato anche nell'elenco a discesa.  

|Colore |Stato calcolo |
|---------|---------| 
| Green | Esecuzione del calcolo |
| Rosso |Calcolo non riuscito | 
| Nero | Calcolo arrestato |
|  Blu chiaro |Calcolo della creazione, avvio, riavvio, configurazione |
|  Grigio |Calcolo eliminazione, arresto |

Un indicatore accanto all'elenco a discesa del **kernel** ne mostra lo stato.

|Colore |Stato del kernel |
|---------|---------|
|  Green |Kernel connesso, inattivo, occupato|
|  Grigio |Kernel non connesso |

## <a name="find-compute-details"></a>Trova dettagli di calcolo 

Per informazioni dettagliate sulle istanze di calcolo, vedere la pagina **calcolo** in [Studio](https://ml.azure.com).

## <a name="next-steps"></a>Passaggi successivi

* [Esegui il primo esperimento](tutorial-1st-experiment-sdk-train.md)
* [Eseguire il backup dell'archiviazione file con gli snapshot](../storage/files/storage-snapshots-files.md)
