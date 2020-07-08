---
title: Usare i notebook con Azure Sentinel per la ricerca di sicurezza
description: Questo articolo descrive come usare i notebook con le funzionalità di ricerca di Azure Sentinel.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/25/2019
ms.openlocfilehash: bf63d5c8cb46fd791508af40dcefd7b39d4ba9de
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83652021"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>Usare i notebook Jupyter per ricercare minacce alla sicurezza

Azure Sentinel si basa sull'archivio dati e combina l'esecuzione di query con prestazioni elevate, lo schema dinamico e i ridimensionamenti ai volumi di dati di grandi dimensioni. Il portale di Azure e tutti gli strumenti di Azure Sentinel usano un'API comune per accedere a questo archivio dati. La stessa API è disponibile anche per strumenti esterni come Python e i notebook [Jupyter](https://jupyter.org/). Mentre è possibile eseguire nel portale molte attività comuni, Jupyter estende l'ambito delle operazioni che è possibile eseguire con questi dati. Combina la programmabilità completa con una vasta raccolta di librerie per l'apprendimento automatico, la visualizzazione e l'analisi dei dati. Questi attributi rendono Jupyter uno strumento efficace per l'analisi e la ricerca della sicurezza.

![notebook di esempio](./media/notebooks/sentinel-notebooks-map.png)

L'esperienza Jupyter è stata integrata nel portale di Azure al fine di semplificare la creazione e l'esecuzione di notebook per l'analisi dei dati. Tramite la libreria *Kqlmagic*, è possibile prendere le query da Azure Sentinel ed eseguirle direttamente in un notebook. Le query usano il [linguaggio di query Kusto](https://kusto.azurewebsites.net/docs/query/index.html). Diversi notebook, sviluppati da alcuni analisti della sicurezza di Microsoft, sono inclusi nel pacchetto di Azure Sentinel. Alcuni di questi notebook sono compilati per uno scenario specifico e possono essere usati così come sono. Altri sono intesi come esempi per illustrare le tecniche e le funzionalità che è possibile copiare o adattare per poterle usare nei propri notebook. È possibile anche importare altri notebook dal repository GitHub della community di Azure Sentinel.

L'esperienza Jupyter integrata usa [Azure Notebooks](https://notebooks.azure.com/) per archiviare, condividere ed eseguire i notebook. È anche possibile eseguire tali notebook in locale se si dispone di un ambiente Python e Jupyter sul proprio computer o in altri ambienti JupterHub, come Azure Databricks.

I notebook presentano due componenti:

- Un'interfaccia basata su browser in cui si immettono ed eseguono query e codice e in cui vengono visualizzati i risultati dell'esecuzione.
- Un *kernel* responsabile dell'analisi e dell'esecuzione del codice stesso. 

In Azure Notebooks, per impostazione predefinita, questo kernel viene eseguito nelle *risorse di calcolo e archiviazione cloud gratuite* di Azure. Se i notebook includono visualizzazioni o modelli di Machine Learning complessi, è consigliabile usare risorse di calcolo più potenti e dedicate, ad esempio [Data Science Virtual Machine](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) (DSVM). I notebook nell'account vengono mantenuti privati a meno che non si scelga di condividerli.

I notebook di Azure Sentinel usano molte delle librerie Python più diffuse, come pandas, matplotlib, bokeh e altre. Sono disponibili molti altri pacchetti Python tra cui scegliere, in riferimento ad aree quali:

- Visualizzazioni e grafica
- Elaborazione e analisi dei dati
- Statistiche e calcolo numerico
- Apprendimento automatico e Deep Learning

Sono stati rilasciati anche alcuni strumenti di sicurezza Jupyter open source in un pacchetto denominato [msticpy](https://github.com/Microsoft/msticpy/), usato in molti dei notebook inclusi. Gli strumenti msticpy sono progettati in modo specifico per semplificare la creazione di notebook per la ricerca e l'analisi, ed è in corso lo sviluppo di nuove funzionalità e miglioramenti.

I notebook iniziali includono:

- **Indagine guidata - Avvisi sui processi**: consente di valutare rapidamente gli avvisi analizzando l'attività negli host interessati.
- **Guided hunting - Windows host explorer** (Ricerca guidata - Esplora host di Windows): consente di esplorare l'attività dell'account, le esecuzioni dei processi, le attività di rete e altri eventi all'interno di un host.
- **Guided hunting - Office365-Exploring** (Ricerca guidata - Esplorazione Office365): consente di eseguire la ricerca di attività sospette di Office 365 in più set di dati di Office 365.

Tutti i futuri notebook di Azure Sentinel creati da Microsoft o forniti dalla community si troveranno nel [repository GitHub della community di Azure Sentinel](https://github.com/Azure/Azure-Sentinel).

Per usare i notebook, è necessario disporre di un account di Azure Notebooks. Per altre informazioni, vedere [Avvio rapido: Accedere e impostare un ID utente](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks) nella documentazione di Azure Notebooks. Per creare questo account, è possibile usare l'opzione **Iscrizione ad Azure Notebooks** dalla barra dei comandi in **Azure Sentinel - Notebook**:

> [!div class="mx-imgBorder"]
>![opzione Iscrizione ad Azure Notebooks](./media/notebooks/sentinel-azure-sign-up-azure-notebooks.png)

È possibile eseguire un notebook direttamente da Azure Sentinel oppure clonare tutti i notebook di Azure Sentinel in un nuovo progetto di Azure Notebooks.

## <a name="run-a-notebook-from-azure-sentinel"></a>Eseguire un notebook da Azure Sentinel
 
1. Dal portale di Azure passare ad **Azure Sentinel** > **Gestione delle minacce** > **Azure Notebooks**, in cui è possibile visualizzare i notebook forniti da Azure Sentinel. 

2. Selezionare singoli notebook per leggere le relative descrizioni, i tipi di dati e le origini dati richiesti. Ad esempio:
    
    > [!div class="mx-imgBorder"]
    > ![avvia notebook](./media/notebooks/sentinel-azure-notebooks-launch.png)

3. Selezionare il notebook che si vuole usare e quindi selezionare **Avvia Notebooks (anteprima)** per clonare e configurare il notebook in un nuovo progetto di Azure Notebooks che si connette all'area di lavoro di Azure Sentinel. Al termine del processo, il notebook viene aperto all'interno di Azure Notebooks per l'esecuzione.

## <a name="clone-azure-sentinel-notebooks-to-a-new-azure-notebooks-project"></a>Clonare i notebook di Azure Sentinel in un nuovo progetto di Azure Notebooks

Questa procedura crea automaticamente un progetto di Azure Notebooks contenente i notebook di Azure Sentinel. È quindi possibile eseguire i notebook così come sono o apportare modifiche prima di eseguirli.

1. Dal portale di Azure passare ad **Azure Sentinel** > **Gestione delle minacce** > **Azure Notebooks** e quindi selezionare **Clone Notebooks** (Clona notebook) dalla barra dei comandi:
  
    > [!div class="mx-imgBorder"]
    >![opzione Clona notebook](./media/notebooks/sentinel-azure-clone-notebooks.png)

2. Quando viene visualizzata la finestra di dialogo seguente, selezionare **Importa** per clonare il repository GitHub nel progetto di Azure Notebooks. Se non si dispone di un account di Azure Notebooks esistente, verrà richiesto di crearne uno ed effettuare l'accesso.

   ![Importa notebook](./media/notebooks/sentinel-notebooks-clone.png)

3. Nella finestra di dialogo **Upload GitHub Repository** (Carica repository GitHub), non selezionare **Clone recursively** (Clona in modo ricorsivo) perché questa opzione fa riferimento ai repository GitHub collegati. Per il nome del progetto, usare il nome predefinito o digitarne uno nuovo. Fare quindi clic su **Importa** per avviare la clonazione del contenuto GitHub, operazione che può richiedere alcuni minuti.

   ![Importa notebook](./media/notebooks/sentinel-create-project.png)

4. Aprire il progetto appena creato, quindi aprire la cartella **Azure Notebooks** per visualizzare i notebook. Ad esempio:

   ![Importa repository](./media/notebooks/sentinel-open-notebook1.png)

È quindi possibile eseguire i notebook da Azure Notebooks. Per tornare a questi notebook da Azure Sentinel, selezionare **Vai ai notebook** dalla barra dei comandi in **Azure Sentinel - Notebooks** (Azure Sentinel - Notebook):

> [!div class="mx-imgBorder"]
>![opzione Vai ai notebook](./media/notebooks/sentinel-azure-to-go-notebooks.png)


## <a name="using-notebooks-to-hunt"></a>Uso dei notebook per la ricerca

Ogni notebook illustra i passaggi a cui attenersi per eseguire una ricerca o un'indagine. Le librerie e altre dipendenze richieste dal notebook possono essere installate dal notebook stesso o tramite una semplice procedura di configurazione. La configurazione che associa il progetto notebook alla sottoscrizione di Azure Sentinel viene automaticamente fornita nei passaggi precedenti.

1. Se non si è già in Azure Notebooks, è possibile usare l'opzione **Vai ai notebook** dalla barra dei comandi in **Azure Sentinel - Notebooks** (Azure Sentinel - Notebook):
    
    > [!div class="mx-imgBorder"]
    >![opzione Vai ai notebook](./media/notebooks/sentinel-azure-to-go-notebooks.png)
    
    In Azure Notebooks selezionare **Progetti**, quindi il progetto contenente i notebook di Azure Sentinel e infine la cartella **Notebooks**.
    
2. Prima di aprire un notebook, tenere presente che, per impostazione predefinita, è selezionata l'opzione del calcolo gratuito per l'esecuzione dei notebook:
    
   ![seleziona notebook](./media/notebooks/sentinel-open-notebook2.png)
    
    Se sono state configurate le Data Science Virtual Machine (DSVM) da usare come illustrato nell'introduzione, selezionare la DSVM ed eseguire l'autenticazione prima di aprire il primo notebook. 

3. Selezionare un notebook per aprirlo.
    
    La prima volta che si apre un notebook potrebbe essere richiesto di selezionare una versione del kernel. Se non viene richiesto, è possibile selezionare la versione del kernel da **Kernel** >  **Change kernel** (Modifica kernel), quindi selezionare una versione a partire almeno dalla 3.6. La versione del kernel selezionata viene visualizzata in alto a destra nella finestra del notebook:
    
   ![seleziona notebook](./media/notebooks/sentinel-select-kernel.png)

4. Prima di apportare modifiche al notebook scaricato, è consigliabile creare una copia del notebook originale su cui lavorare. A tale scopo, selezionare **File** > **Crea una copia**. Quando si lavora sulle copie è possibile eseguire l'aggiornamento in modo sicuro alle versioni future dei notebook senza sovrascrivere i dati.
    
    A questo punto si è pronti per eseguire o modificare il notebook selezionato.

Consigli:

- Per una rapida introduzione all'esecuzione di query sui dati in Azure Sentinel, vedere il notebook [Getstarted](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/345cf9f7c8f6137f5af4593a3f9d7568acd6cbc2/DeprecatedNotebooks/Get%20Started.ipynb) nella cartella **Notebooks** principale. 

- Nella sottocartella **Sample-Notebooks** sono riportati altri notebook di esempio salvati con i dati, in modo che sia più semplice visualizzare l'output desiderato. È consigliabile visualizzare questi notebook in [nbviewer](https://nbviewer.jupyter.org/). 

- La cartella **HowTos** contiene notebook che descrivono, ad esempio, come: impostare la versione predefinita di Python, configurare una DSVM, creare segnalibri di Azure Sentinel da un notebook e altro ancora.

I notebook forniti sono intesi come strumenti utili e come illustrazioni ed esempi di codice che è possibile usare per lo sviluppo di notebook personali.

Qualsiasi feedback, suggerimento, richiesta di funzionalità, notebook con contributi, report sui bug, miglioramento e aggiunta ai notebook esistenti sarà ben accetto. Per creare un problema o per creare una copia tramite fork e caricare un contributo, visitare il [repository GitHub della community di Azure Sentinel](https://github.com/Azure/Azure-Sentinel).

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si sono apprese le attività iniziali da svolgere con i notebook Jupyter in Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- [Cercare le minacce in modo proattivo](hunting.md)
- [Usare i segnalibri per salvare informazioni utili durante la ricerca](bookmarks.md)
