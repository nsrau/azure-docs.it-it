---
title: Usare i blocchi appunti con Azure Sentinel per la ricerca della sicurezzaUse notebooks with Azure Sentinel for security hunting
description: Questo articolo descrive come usare i blocchi appunti con le funzionalità di caccia di Azure Sentinel.This article describes how to use notebooks with the Azure Sentinel hunting capabilities.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/25/2019
ms.openlocfilehash: 84b72a71ed2de910bce44b0c3f3309782f096680
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77581838"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>Usa i notebook Jupyter per cercare minacce alla sicurezza

La base di Azure Sentinel è l'archivio dati; combina query ad alte prestazioni, schema dinamico e scalabilità a volumi di dati di grandi dimensioni. Il portale di Azure e tutti gli strumenti di Azure Sentinel usano un'API comune per accedere a questo archivio dati. La stessa API è disponibile anche per strumenti esterni come notebook [Jupyter](https://jupyter.org/) e Python. Mentre molte attività comuni possono essere eseguite nel portale, Jupyter estende l'ambito di ciò che si può fare con questi dati. Combina la programmabilità completa con un'enorme raccolta di librerie per l'apprendimento automatico, la visualizzazione e l'analisi dei dati. Questi attributi rendono Jupyter uno strumento interessante per le indagini di sicurezza e la caccia.

![blocco appunti di esempio](./media/notebooks/sentinel-notebooks-map.png)

Abbiamo integrato l'esperienza Jupyter nel portale di Azure, semplificando la creazione e l'esecuzione di blocchi appunti per analizzare i dati. La libreria *Kqlmagic* fornisce l'associazione che consente di eseguire query da Azure Sentinel ed eseguirle direttamente all'interno di un blocco appunti. Le query utilizzano [Kusto Query Language](https://kusto.azurewebsites.net/docs/query/index.html). Diversi notebook, sviluppati da alcuni analisti della sicurezza di Microsoft, sono inclusi in un pacchetto con Azure Sentinel. Alcuni di questi notebook sono creati per uno scenario specifico e possono essere utilizzati così come sono. Altri sono concepiti come esempi per illustrare tecniche e funzionalità che è possibile copiare o adattare per l'uso nei propri blocchi appunti. Altri blocchi appunti possono anche essere importati dalla community di Azure Sentinel GitHub.Other notebooks may also be imported from the Azure Sentinel community GitHub.

L'esperienza Jupyter integrata usa [i blocchi appunti](https://notebooks.azure.com/) di Azure per archiviare, condividere ed eseguire blocchi appunti. You can also run these notebooks locally if you have a Python environment and Jupyter on your computer, or in other JupterHub environments such as Azure Databricks.

I notebook hanno due componenti:

- Interfaccia basata su browser in cui si immettono ed eseguono query e codice e in cui vengono visualizzati i risultati dell'esecuzione.
- Kernel *kernel* responsabile dell'analisi e dell'esecuzione del codice stesso. 

In Blocchi appunti di Azure, per impostazione predefinita, questo kernel viene eseguito in Archiviazione *e calcolo cloud gratuito*di Azure. Se i blocchi appunti includono visualizzazioni o modelli di apprendimento automatico complessi, è consigliabile usare risorse di calcolo più potenti e dedicate, ad esempio Macchine virtuali di [data science](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) (DSVM). I blocchi appunti dell'account vengono mantenuti privati a meno che non si scelga di condividerli.

I notebook di Azure Sentinel usano molte librerie Python popolari, ad esempio panda, matplotlib, bokeh e altri. Ci sono un gran numero di altri pacchetti Python tra cui scegliere, coprendo aree come:

- Visualizzazioni e grafica
- Trattamento e analisi dei dati
- Statistiche e calcolo numerico
- Apprendimento automatico e apprendimento profondo

Abbiamo anche rilasciato alcuni strumenti di sicurezza Open-source Jupyter in un pacchetto denominato [msticpy](https://github.com/Microsoft/msticpy/). Questo pacchetto viene utilizzato in molti dei notebook inclusi. Gli strumenti Msticpy sono progettati specificamente per aiutare a creare notebook per la caccia e l'indagine e stiamo lavorando attivamente su nuove funzionalità e miglioramenti.

I blocchi appunti iniziali includono:

- **Analisi guidata - Elabora avvisi**: Consente di valutare rapidamente gli avvisi analizzando l'attività sull'host o sugli host interessati.
- **Caccia guidata - Esplora host**di Windows: consente di esplorare l'attività dell'account, le esecuzioni di processi, l'attività di rete e altri eventi in un host.
- **Caccia guidata - Office365-Exploring**: Caccia alle attività sospette di Office 365 in più set di dati di Office 365.

Il [repository GitHub della community](https://github.com/Azure/Azure-Sentinel) di Azure Sentinel è il percorso per tutti i futuri blocchi appunti di Azure Sentinel creati da Microsoft o forniti dalla community.

Per usare i blocchi appunti, è necessario disporre di un account Blocchi appunti di Azure.To use the notebooks, you must have an Azure Notebooks account. Per altre informazioni, vedere [Guida introduttiva: Accedere e impostare un ID utente](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks) dalla documentazione di Azure Notebooks.For more information, see Quickstart: Sign in and set a user ID from the Azure Notebooks documentation. Per creare questo account, è possibile usare l'opzione **Iscriviti ad Azure blocchi appunti** dalla barra dei comandi in Azure **Sentinel - Blocchi appunti:**

> [!div class="mx-imgBorder"]
>![Iscriversi all'opzione Blocchi appunti di Azure](./media/notebooks/sentinel-azure-sign-up-azure-notebooks.png)

È possibile eseguire un blocco appunti direttamente da Azure Sentinel o clonare tutti i blocchi appunti di Azure Sentinel in un nuovo progetto Blocchi appunti di Azure.You can run a notebook direct from Azure Sentinel, or clone all the Azure Sentinel notebooks to a new Azure Notebooks project.

## <a name="run-a-notebook-from-azure-sentinel"></a>Eseguire un blocco appunti da Azure SentinelRun a notebook from Azure Sentinel
 
1. Dal portale di Azure passare a Blocchi appunti per la**gestione delle minacce** > di Azure Sentinel , in cui è possibile visualizzare i blocchi appunti forniti da Azure Sentinel.From the Azure portal, navigate to **Azure Sentinel** > Threat management**Notebooks**, where you can see notebooks that Azure Sentinel provides. 

2. Selezionare singoli blocchi appunti per leggerne le descrizioni, i tipi di dati necessari e le origini dati. Ad esempio:
    
    > [!div class="mx-imgBorder"]
    > ![lanciare il blocco appunti](./media/notebooks/sentinel-azure-notebooks-launch.png)

3. Selezionare il blocco appunti che si vuole usare e quindi selezionare **Avvia blocco appunti (anteprima)** per clonare e configurare il blocco appunti in un nuovo progetto Blocchi appunti di Azure che si connette all'area di lavoro di Azure Sentinel.Select the notebook you want to use, and then select Launch Notebook (Preview) to clone and configure the notebook into a new Azure Notebooks project that connects to your Azure Sentinel workspace. Al termine del processo, il blocco appunti verrà aperto all'interno di Blocchi appunti di Azure per l'esecuzione.

## <a name="clone-azure-sentinel-notebooks-to-a-new-azure-notebooks-project"></a>Clonare i blocchi appunti di Azure Sentinel in un nuovo progetto Blocchi appunti di AzureClone Azure Sentinel notebooks to a new Azure Notebooks project

Questa procedura consente di creare automaticamente un progetto Blocchi appunti di Azure, che contiene i blocchi appunti di Azure Sentinel.This procedure creates an Azure Notebooks project for you, which contains the Azure Sentinel notebooks. È quindi possibile eseguire i blocchi appunti così come sono oppure apportarvi modifiche ed eseguirli.

1. Dal portale di Azure passare a Blocchi appunti di**gestione delle minacce** > di Azure Sentinel e quindi selezionare Clona blocchi appunti dalla barra dei comandi:From the Azure portal, navigate to **Azure Sentinel** > Threat management**Notebooks** and then select **Clone Notebooks** from the command bar:
  
    > [!div class="mx-imgBorder"]
    >![Opzione Clona blocchi appunti](./media/notebooks/sentinel-azure-clone-notebooks.png)

2. Quando viene visualizzata la finestra di dialogo seguente, selezionare Importa per clonare il repository GitHub nel progetto Blocchi appunti di Azure.When the following dialog appears, select **Import** to clone the GitHub repo into your Azure Notebooks project. Se non si dispone di un account Blocchi di appunti di Azure esistente, verrà richiesto di crearne uno e di accedere.

   ![Importare un blocco appunti](./media/notebooks/sentinel-notebooks-clone.png)

3. Nella finestra di dialogo **Carica repository GitHub,** non selezionare **Clona in modo ricorsivo** perché questa opzione fa riferimento ai repository GitHub collegati. Per il nome del progetto, utilizzare il nome o il tipo predefinito in uno nuovo. Quindi fare clic su **Importa** per avviare la clonazione del contenuto GitHub, che può richiedere alcuni minuti per completare.

   ![Importare un blocco appunti](./media/notebooks/sentinel-create-project.png)

4. Aprire il progetto appena creato e quindi aprire la cartella **Blocchi appunti** per visualizzare i blocchi appunti. Ad esempio:

   ![Importa repository](./media/notebooks/sentinel-open-notebook1.png)

È quindi possibile eseguire i blocchi appunti dai blocchi appunti di Azure.You can then run the notebooks from Azure Notebooks. Per tornare a questi blocchi appunti da Azure Sentinel, selezionare **Vai ai blocchi appunti** dalla barra dei comandi in Azure **Sentinel - Blocchi appunti:**

> [!div class="mx-imgBorder"]
>![Passare all'opzione Blocchi appunti](./media/notebooks/sentinel-azure-to-go-notebooks.png)


## <a name="using-notebooks-to-hunt"></a>Uso dei blocchi appunti per la caccia

Ogni notebook ti guida attraverso i passaggi per effettuare una caccia o un'indagine. Le librerie e le altre dipendenze necessarie per il blocco appunti possono essere installate dal blocco appunti stesso o tramite una semplice procedura di configurazione. La configurazione che collega il progetto del blocco appunti alla sottoscrizione di Azure Sentinel viene automaticamente sottoposta a provisioning nei passaggi precedenti.

1. Se non si è già in Blocchi appunti di Azure, è possibile usare l'opzione **Vai ai blocchi appunti** dalla barra dei comandi in Azure **Sentinel - Blocchi appunti:**
    
    > [!div class="mx-imgBorder"]
    >![Passare all'opzione Blocchi appunti](./media/notebooks/sentinel-azure-to-go-notebooks.png)
    
    In Blocchi appunti di Azure selezionare **Progetti personali**, quindi il progetto che contiene i blocchi appunti di Azure Sentinel e infine la cartella **Blocchi appunti.**
    
2. Prima di aprire un blocco appunti, tenere presente che per impostazione predefinita è selezionata l'opzione Calcolo libero per eseguire i blocchi appunti:
    
   ![selezionare il blocco appunti](./media/notebooks/sentinel-open-notebook2.png)
    
    Se è stata configurata una Macchina virtuale di data science (DSVM) da utilizzare come illustrato nell'introduzione, selezionare DSVM ed eseguire l'autenticazione prima di aprire il primo blocco appunti. 

3. Selezionare un blocco appunti per aprirlo.
    
    La prima volta che si apre un blocco appunti, è possibile che venga richiesto di selezionare una versione del kernel. Se non viene richiesto, è possibile selezionare la versione del kernel da **Kernel** >  **Change kernel**e quindi selezionare una versione di almeno 3.6. La versione del kernel selezionata viene visualizzata in alto a destra nella finestra del notebook:
    
   ![selezionare il blocco appunti](./media/notebooks/sentinel-select-kernel.png)

4. Prima di apportare modifiche al blocco appunti scaricato, è consigliabile creare una copia del blocco appunti originale e lavorare sulla copia. A tale scopo, selezionare **File** > **Crea una copia**. L'utilizzo delle copie consente di eseguire in modo sicuro l'aggiornamento alle versioni future dei blocchi appunti senza sovrascrivere i dati.
    
    È ora possibile eseguire o modificare il blocco appunti selezionato.

Consigli:

- Per una rapida introduzione all'esecuzione di query sui dati in Azure Sentinel, esaminare il blocco appunti [GetStarted](https://github.com/Azure/Azure-Sentinel/blob/master/Notebooks/Get%20Started.ipynb) nella cartella **dei blocchi appunti** principale. 

- Sono disponibili altri blocchi appunti di esempio nella sottocartella **Blocchi di blocco dei blocchi appunti** di esempio. Questi blocchi appunti di esempio sono stati salvati con i dati, in modo che sia più facile visualizzare l'output desiderato. È consigliabile visualizzare questi blocchi appunti in [nbviewer](https://nbviewer.jupyter.org/). 

- La cartella **HowTos** contiene blocchi appunti descrittivi, ad esempio: Impostazione della versione predefinita di Python, configurazione di una dSVM, creazione di segnalibri di Azure Sentinel da un blocco appunti e altri argomenti.

I blocchi appunti forniti sono intesi sia come strumenti utili che come illustrazioni ed esempi di codice che è possibile utilizzare nello sviluppo di blocchi appunti personalizzati.

Accogliamo con favore il feedback, che si tratti di suggerimenti, richieste di funzionalità, blocchi appunti forniti, segnalazioni di bug o miglioramenti e aggiunte ai blocchi appunti esistenti. Passare a [GitHub della community](https://github.com/Azure/Azure-Sentinel) di Azure Sentinel per creare un problema o eseguire il fork e caricare un contributo.

## <a name="next-steps"></a>Passaggi successivi

In this article, you learned how to get started using Jupyter notebooks in Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- [Caccia proattivamente alle minacce](hunting.md)
- [Utilizzare i segnalibri per salvare informazioni interessanti durante la caccia](bookmarks.md)
