---
title: Funzionalità di caccia usando i notebook in Sentinel di Azure | Microsoft Docs
description: Questo articolo descrive come usare i notebook con le funzionalità di ricerca di Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: rkarlin
ms.openlocfilehash: ba22cc3db0ca50a292ddef4d0d646f8578c15cd4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489174"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>Usare i notebook di Jupyter per cercare minacce per la sicurezza

La base di Azure Sentinel è l'archivio dati. combina le query a prestazioni elevate, lo schema dinamico e le scale a volumi di dati di grandi dimensioni. Il portale di Azure e tutti gli strumenti di Azure Sentinel usano un'API comune per accedere a questo archivio dati. La stessa API è disponibile anche per strumenti esterni, ad esempio [Jupyter](https://jupyter.org/) notebook e Python. Mentre molte attività comuni possono essere eseguite nel portale, Jupyter estende l'ambito delle operazioni che è possibile eseguire con questi dati. Combina la programmabilità completa con una vasta raccolta di librerie per l'apprendimento automatico, la visualizzazione e l'analisi dei dati. Questi attributi rendono Jupyter uno strumento accattivante per l'analisi della sicurezza e la ricerca.

![Notebook di esempio](./media/notebooks/sentinel-notebooks-map.png)

L'esperienza Jupyter è stata integrata nell'portale di Azure, semplificando la creazione e l'esecuzione di notebook per l'analisi dei dati. La libreria *Kqlmagic* fornisce l'associazione che consente di eseguire query da Azure Sentinel ed eseguirle direttamente all'interno di un notebook. Le query usano il [linguaggio di query kusto](https://kusto.azurewebsites.net/docs/query/index.html). Diversi notebook, sviluppati da alcuni analisti della sicurezza di Microsoft, sono inclusi in un pacchetto di Azure Sentinel. Alcuni di questi notebook sono compilati per uno scenario specifico e possono essere usati così come sono. Altri sono intesi come esempi per illustrare le tecniche e le funzionalità che è possibile copiare o adattare per l'uso nei propri notebook. Altri notebook possono anche essere importati dalla community di Azure Sentinel GitHub.

L'esperienza Jupyter integrata usa [Azure Notebooks](https://notebooks.azure.com/) per archiviare, condividere ed eseguire notebook. È anche possibile eseguire questi notebook localmente se si dispone di un ambiente Python e Jupyter nel computer o in altri ambienti JupterHub come Azure Databricks.

I notebook hanno due componenti:

- Interfaccia basata su browser in cui si immettono ed eseguono query e codice e in cui vengono visualizzati i risultati dell'esecuzione.
- *Kernel* responsabile dell'analisi e dell'esecuzione del codice stesso. 

In Azure Notebooks, per impostazione predefinita, questo kernel viene eseguito in risorse di *calcolo e archiviazione cloud gratuite*di Azure. Se i notebook includono modelli o visualizzazioni complesse di Machine Learning, è consigliabile usare risorse di calcolo più potenti e dedicate, ad esempio [macchine virtuali per l'analisi scientifica dei dati](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) (DSVM). I notebook nell'account vengono mantenuti privati a meno che non si scelga di condividerli.

I notebook di Sentinel di Azure usano molte librerie Python più diffuse, ad esempio Pandas, Matplotlib, bokeh e altri. Sono disponibili molti altri pacchetti Python tra cui scegliere, per aree quali:

- Visualizzazioni e grafica
- Elaborazione e analisi dei dati
- Statistiche e calcolo numerico
- Apprendimento automatico e apprendimento avanzato

Sono stati rilasciati anche alcuni strumenti di sicurezza Jupyter open source in un pacchetto denominato [msticpy](https://github.com/Microsoft/msticpy/). Questo pacchetto viene usato in molti dei notebook inclusi. Gli strumenti Msticpy sono progettati in modo specifico per semplificare la creazione di notebook per la ricerca e l'analisi e stiamo lavorando attivamente a nuove funzionalità e miglioramenti.

I notebook iniziali includono:

- **Analisi guidata-avvisi processo**: consente di valutare rapidamente gli avvisi analizzando l'attività nell'host o negli host interessati.
- **Ricerca guidata: Esplora host di Windows**: consente di esplorare l'attività dell'account, le esecuzioni di processi, le attività di rete e altri eventi in un host.
- **Caccia guidata-Office365-esplorazione**: ricerca di attività di Office 365 sospette in più set di dati di Office 365.

Il [repository GitHub della community di Azure Sentinel](https://github.com/Azure/Azure-Sentinel) è la località per tutti i futuri notebook di Azure Sentinel creati da Microsoft o che hanno contribuito alla community.

Per usare i notebook, è necessario disporre di un account Azure Notebooks. Per altre informazioni, vedere [Guida introduttiva: accedere e impostare un ID utente](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks) dalla documentazione Azure Notebooks. Per creare questo account, è possibile usare l'opzione **iscrizione per Azure Notebooks** dalla barra dei comandi in **Azure Sentinel-Notebooks**:

> [!div class="mx-imgBorder"]
>![iscriversi per Azure Notebooks opzione](./media/notebooks/sentinel-azure-sign-up-azure-notebooks.png)

## <a name="view-available-notebooks-from-azure-sentinel"></a>Visualizzare i notebook disponibili da Azure Sentinel
 
1. Dal portale di Azure passare ad **Azure Sentinel** > **Threat Management** > **Notebooks**, dove è possibile visualizzare i notebook forniti da Azure Sentinel. 

2. Selezionare singoli notebook per leggere le descrizioni, i tipi di dati e le origini dati richiesti. Ad esempio:
    
    > [!div class="mx-imgBorder"]
    > ![avviare il notebook](./media/notebooks/sentinel-azure-notebooks-nolaunch.png)

3. Selezionare **Launch notebook (avvia notebook** ) per esplorare i notebook nel [repository GitHub della community di Azure Sentinel](https://github.com/Azure/Azure-Sentinel).

Attualmente non è possibile avviare un notebook direttamente da Azure Sentinel. Usare invece la procedura seguente che illustra la clonazione dei notebook in GitHub in un progetto Azure Notebooks.

## <a name="clone-azure-sentinel-notebooks-to-a-new-azure-notebooks-project"></a>Clonare notebook di Sentinel di Azure in un nuovo progetto Azure Notebooks

Questa procedura crea automaticamente un progetto di Azure Notebooks, che contiene i notebook di Azure Sentinel. È quindi possibile eseguire i notebook così come sono o apportare modifiche e quindi eseguirli.

1. Dal portale di Azure passare ad **Azure Sentinel** > **gestione minacce** > **notebook** e quindi selezionare **clona notebook** dalla barra dei comandi:
  
    > [!div class="mx-imgBorder"]
    >opzione ![cloni Notebooks](./media/notebooks/sentinel-azure-clone-notebooks.png)

2. Quando viene visualizzata la finestra di dialogo seguente, selezionare **Importa** per clonare il repository GitHub nel progetto Azure Notebooks. Se non si dispone di un account Azure Notebooks esistente, verrà richiesto di crearne uno ed effettuare l'accesso.

   ![Importa notebook](./media/notebooks/sentinel-notebooks-clone.png)

3. Nella finestra di dialogo **carica repository GitHub** non selezionare Clona in modo **ricorsivo** perché questa opzione fa riferimento ai repository GitHub collegati. Per il nome del progetto, usare il nome predefinito o digitare un nuovo nome. Fare quindi clic su **Import (importa** ) per avviare la clonazione del contenuto github. l'operazione può richiedere alcuni minuti.

   ![Importa notebook](./media/notebooks/sentinel-create-project.png)

4. Aprire il progetto appena creato, quindi aprire la cartella **Notebooks (notebook** ) per visualizzare i notebook. Ad esempio:

   ![Importa repository](./media/notebooks/sentinel-open-notebook1.png)

È quindi possibile eseguire i notebook da Azure Notebooks. Per tornare a questi notebook da Azure Sentinel, selezionare **Vai ai notebook** dalla barra dei comandi in **Azure Sentinel-Notebooks**:

> [!div class="mx-imgBorder"]
>![accedere all'opzione Notebooks](./media/notebooks/sentinel-azure-to-go-notebooks.png)


## <a name="using-notebooks-to-hunt"></a>Uso di notebook per la ricerca

Ogni notebook illustra i passaggi per eseguire una ricerca o un'indagine. Le librerie e altre dipendenze necessarie per il notebook possono essere installate dal notebook stesso o tramite una semplice procedura di configurazione. La configurazione che associa il progetto notebook alla sottoscrizione di Azure Sentinel viene automaticamente fornita nei passaggi precedenti.

1. Se non si è già in Azure Notebooks, è possibile usare l'opzione **Vai ai notebook** dalla barra dei comandi in **Azure Sentinel-Notebooks**:
    
    > [!div class="mx-imgBorder"]
    >![accedere all'opzione Notebooks](./media/notebooks/sentinel-azure-to-go-notebooks.png)
    
    In Azure Notebooks selezionare **progetti personali**, quindi il progetto che contiene i notebook di Sentinel di Azure e infine la cartella **Notebooks** .
    
2. Prima di aprire un notebook, tenere presente che, per impostazione predefinita, è selezionata l'opzione calcolo gratuito per l'esecuzione dei notebook:
    
   ![Seleziona notebook](./media/notebooks/sentinel-open-notebook2.png)
    
    Se sono state configurate le macchine virtuali per l'analisi scientifica dei dati (DSVM) da usare come illustrato nell'introduzione, selezionare il DSVM ed eseguire l'autenticazione prima di aprire il primo notebook. 

3. Selezionare un notebook per aprirlo.
    
    La prima volta che si apre un notebook, potrebbe essere richiesto di selezionare una versione del kernel. Se non viene richiesto, è possibile selezionare la **versione del kernel dal kernel** >  **modificare il kernel**, quindi selezionare una versione di almeno 3,6. La versione del kernel selezionata viene visualizzata nella parte superiore destra della finestra del notebook:
    
   ![Seleziona notebook](./media/notebooks/sentinel-select-kernel.png)

4. Prima di apportare modifiche al notebook che è stato scaricato, è consigliabile creare una copia del notebook originale e utilizzare la copia. A tale scopo, selezionare **File** > **Crea una copia**. L'uso delle copie consente di eseguire l'aggiornamento in modo sicuro alle versioni future dei notebook senza sovrascrivere i dati.
    
    A questo punto si è pronti per eseguire o modificare il notebook selezionato.

Consigli:

- Per una rapida introduzione all'esecuzione di query sui dati in Sentinel di Azure, vedere il notebook [getstarted](https://github.com/Azure/Azure-Sentinel/blob/master/Notebooks/Get%20Started.ipynb) nella cartella principale **Notebooks** . 

- Ulteriori notebook di esempio sono disponibili nella sottocartella **Sample-Notebooks** . Questi notebook di esempio sono stati salvati con i dati, in modo che sia più semplice visualizzare l'output desiderato. È consigliabile visualizzare questi notebook in [nbviewer](https://nbviewer.jupyter.org/). 

- La cartella **HOWTO** contiene i notebook che descrivono, ad esempio: impostazione della versione predefinita di Python, configurazione di un DSVM, creazione di segnalibri di Azure Sentinel da un notebook e altri argomenti.

I notebook forniti sono intesi come strumenti utili e come illustrazioni ed esempi di codice che è possibile usare per lo sviluppo di notebook personali.

Commenti e suggerimenti, suggerimenti, richieste di funzionalità, notebook con contributi, report sui bug, miglioramenti e aggiunte ai notebook esistenti. Per creare un problema o un fork e caricare un contributo, visitare la [community di Azure Sentinel GitHub](https://github.com/Azure/Azure-Sentinel) .

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come iniziare a usare i notebook di Jupyter in Sentinel di Azure. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- [Cercare in modo proattivo le minacce](hunting.md)
- [Usare i segnalibri per salvare informazioni interessanti durante la ricerca](bookmarks.md)
