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
ms.date: 09/06/2020
ms.openlocfilehash: d5d182276cd77493be5184503a1afc47934bf8ea
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96344194"
---
# <a name="use-jupyter-notebook-to-hunt-for-security-threats"></a>Usare Jupyter Notebook per cercare minacce per la sicurezza

La base di Azure Sentinel è l'archivio dati. combina query a prestazioni elevate, schemi dinamici e scalabilità a volumi di dati di grandi dimensioni. Il portale di Azure e tutti gli strumenti di Azure Sentinel usano un'API comune per accedere a questo archivio dati. La stessa API è disponibile anche per strumenti esterni come Python e i notebook [Jupyter](https://jupyter.org/). Mentre è possibile eseguire nel portale molte attività comuni, Jupyter estende l'ambito delle operazioni che è possibile eseguire con questi dati. Combina la programmabilità completa con una vasta raccolta di librerie per l'apprendimento automatico, la visualizzazione e l'analisi dei dati. Questi attributi rendono Jupyter uno strumento efficace per l'analisi e la ricerca della sicurezza.

![notebook di esempio](./media/notebooks/sentinel-notebooks-map.png)

L'esperienza Jupyter è stata integrata nel portale di Azure al fine di semplificare la creazione e l'esecuzione di notebook per l'analisi dei dati. Tramite la libreria *Kqlmagic*, è possibile prendere le query da Azure Sentinel ed eseguirle direttamente in un notebook. Le query usano il [linguaggio di query Kusto](https://kusto.azurewebsites.net/docs/query/index.html). Diversi notebook, sviluppati da alcuni analisti della sicurezza di Microsoft, sono inclusi nel pacchetto di Azure Sentinel. Alcuni di questi notebook sono compilati per uno scenario specifico e possono essere usati così come sono. Altri sono intesi come esempi per illustrare le tecniche e le funzionalità che è possibile copiare o adattare per poterle usare nei propri notebook. Altri notebook possono anche essere importati dalla community di Azure Sentinel GitHub.

L'esperienza Jupyter integrata usa [Azure Notebooks](https://notebooks.azure.com/) per archiviare, condividere ed eseguire i notebook. È anche possibile eseguire tali notebook in locale se si dispone di un ambiente Python e Jupyter sul proprio computer o in altri ambienti JupterHub, come Azure Databricks.

I notebook presentano due componenti:

- Un'interfaccia basata su browser in cui si immettono ed eseguono query e codice e in cui vengono visualizzati i risultati dell'esecuzione.
- Un *kernel* responsabile dell'analisi e dell'esecuzione del codice stesso.

Il kernel di Azure Sentinel notebook viene eseguito in una macchina virtuale (VM) di Azure. Sono disponibili diverse opzioni di gestione delle licenze per sfruttare macchine virtuali più potenti se i notebook includono modelli di apprendimento automatico complessi.

I notebook di Azure Sentinel usano molte delle librerie Python più diffuse, come pandas, matplotlib, bokeh e altre. Sono disponibili molti altri pacchetti Python tra cui scegliere, in riferimento ad aree quali:

- Visualizzazioni e grafica
- Elaborazione e analisi dei dati
- Statistiche e calcolo numerico
- Apprendimento automatico e Deep Learning

Sono stati rilasciati anche alcuni strumenti di sicurezza Jupyter open source in un pacchetto denominato [msticpy](https://github.com/Microsoft/msticpy/), usato in molti dei notebook inclusi. Gli strumenti msticpy sono progettati in modo specifico per semplificare la creazione di notebook per la ricerca e l'analisi, ed è in corso lo sviluppo di nuove funzionalità e miglioramenti.

Tutti i futuri notebook di Azure Sentinel creati da Microsoft o forniti dalla community si troveranno nel [repository GitHub della community di Azure Sentinel](https://github.com/Azure/Azure-Sentinel).

Per usare i notebook, è necessario innanzitutto creare un'area di lavoro di Azure Machine Learning (ML).

## <a name="create-an-azure-ml-workspace"></a>Creare un'area di lavoro di Azure ML

1. Dal portale di Azure passare ad **Azure Sentinel**  >  **Threat Management**  >  **Notebooks** e quindi selezionare **Launch notebook**.

    > [!div class="mx-imgBorder"]
    > ![avviare notebook per avviare l'area di lavoro di Azure ml](./media/notebooks/sentinel-notebooks-launch.png)

1. In **area di lavoro AzureML** selezionare **Crea nuovo**.

    > [!div class="mx-imgBorder"]
    > ![Crea area di lavoro](./media/notebooks/sentinel-notebooks-azureml-create.png)

1. Nella pagina **Machine Learning** specificare le informazioni seguenti e quindi selezionare **Verifica + crea**.

    |Campo|Descrizione|
    |--|--|
    |Subscription|Selezionare la sottoscrizione di Azure da usare.|
    |Resource group|Usare un gruppo di risorse esistente nella sottoscrizione oppure immettere un nome per creare un nuovo gruppo di risorse. Un gruppo di risorse include risorse correlate per una soluzione Azure. In questo esempio viene usato **AzureMLRG**.|
    |Nome dell'area di lavoro|Immettere un nome univoco che identifichi l'area di lavoro. In questo esempio viene usato **testworkspace1**. I nomi devono essere univoci all'interno del gruppo di risorse. Usare un nome facile da ricordare e da distinguere dai nomi delle aree di lavoro create da altri utenti.|
    |Region|Selezionare la località più vicina agli utenti e alle risorse di dati per creare l'area di lavoro.|
    |Edizione dell'area di lavoro|In questo esempio selezionare **Basic** come tipo di area di lavoro. Il tipo di area di lavoro (Basic & Enterprise) determina le funzionalità a cui è possibile accedere e i prezzi.|

    > [!div class="mx-imgBorder"]
    > ![specificare i dettagli dell'area di lavoro](./media/notebooks/sentinel-notebooks-azureml-basics.png)

1. Esaminare le informazioni, verificare che siano corrette e quindi selezionare **Crea** per avviare la distribuzione dell'area di lavoro.

    > [!div class="mx-imgBorder"]
    > ![esaminare i dettagli dell'area di lavoro](./media/notebooks/sentinel-notebooks-azureml-review.png)

    Possono essere necessari alcuni minuti per creare l'area di lavoro nel cloud durante la quale la pagina **Panoramica** Visualizza lo stato di distribuzione corrente.

    > [!div class="mx-imgBorder"]
    > ![distribuzione dell'area di lavoro](./media/notebooks/sentinel-notebooks-azureml-deploy.png)

Una volta completata la distribuzione, è possibile avviare notebook nella nuova area di lavoro di Azure ML.

> [!div class="mx-imgBorder"]
> ![distribuzione dell'area di lavoro completata](./media/notebooks/sentinel-notebooks-azureml-complete.png)

## <a name="launch-a-notebook-using-your-azure-ml-workspace"></a>Avviare un notebook usando l'area di lavoro di Azure ML

1. Dal portale di Azure passare ad **Azure Sentinel** > **Gestione delle minacce** > **Azure Notebooks**, in cui è possibile visualizzare i notebook forniti da Azure Sentinel.

    > [!TIP]
    > Selezionare **guide & feedback** per aprire un riquadro con ulteriori informazioni e indicazioni sui notebook.
    > ![Visualizza guide notebook](./media/notebooks/sentinel-azure-notebooks-guides.png)

1. Selezionare singoli notebook per visualizzare le descrizioni, i tipi di dati e le origini dati richiesti.

    > [!div class="mx-imgBorder"]
    > ![Visualizza i dettagli del notebook](./media/notebooks/sentinel-azure-notebooks-view.png)

1. Selezionare il notebook che si vuole usare e quindi selezionare **Avvia notebook** per clonare e configurare il notebook in un nuovo progetto Azure Notebooks che si connette all'area di lavoro di Azure Sentinel. Al termine del processo, il notebook viene aperto all'interno di Azure Notebooks per l'esecuzione.

    > [!div class="mx-imgBorder"]
    > ![Seleziona notebook](./media/notebooks/sentinel-azure-notebooks-select.png)

1. In area di lavoro AzureML selezionare l'area di lavoro di Azure ML e quindi fare clic su **Avvia**.

    > [!div class="mx-imgBorder"]
    > ![avvia notebook](./media/notebooks/sentinel-azure-notebooks-launch.png)

1. Selezionare un'istanza di calcolo. Se non si dispone di un'istanza di calcolo, procedere come segue:
    1. Selezionare il segno più (+) per avviare la procedura guidata **nuova istanza di calcolo** .

        > [!div class="mx-imgBorder"]
        > ![avvio della creazione guidata istanza di calcolo](./media/notebooks/sentinel-azure-notebooks-compute-wizard.png)

    1. Nella pagina **nuova istanza di calcolo** fornire le informazioni necessarie e quindi selezionare **Crea**.

        > [!div class="mx-imgBorder"]
        > ![creare un'istanza di calcolo](./media/notebooks/sentinel-azure-notebooks-compute-create.png)

1. Una volta creato il server notebook, all'interno di ogni cella selezionare l'icona Esegui per eseguire il codice nei notebook.

    > [!div class="mx-imgBorder"]
    > ![Esegui notebook](./media/notebooks/sentinel-azure-notebooks-run.png)

Consigli:

- Per una rapida introduzione all'esecuzione di query sui dati in Sentinel di Azure, vedere la [Introduzione con i notebook di Azure ml e](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/A%20Getting%20Started%20Guide%20For%20Azure%20Sentinel%20ML%20Notebooks.ipynb) la Guida di Azure Sentinel.

- Ulteriori notebook di esempio sono disponibili nella sottocartella GitHub di [**esempio-Notebooks**](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/Sample-Notebooks) . salvati con i dati, in modo che sia più semplice visualizzare l'output desiderato. È consigliabile visualizzare questi notebook in [nbviewer](https://nbviewer.jupyter.org/).

- La sottocartella [**howtos**](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/HowTos) github contiene i notebook che descrivono, ad esempio: impostazione della versione predefinita di Python, configurazione di un DSVM, creazione di segnalibri di Azure Sentinel da un notebook e altri argomenti.

I notebook forniti sono intesi come strumenti utili e come illustrazioni ed esempi di codice che è possibile usare per lo sviluppo di notebook personali.

Qualsiasi feedback, suggerimento, richiesta di funzionalità, notebook con contributi, report sui bug, miglioramento e aggiunta ai notebook esistenti sarà ben accetto. Per creare un problema o per creare una copia tramite fork e caricare un contributo, visitare il [repository GitHub della community di Azure Sentinel](https://github.com/Azure/Azure-Sentinel).

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come iniziare a usare Jupyter Notebook in Sentinel di Azure. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- [Cercare le minacce in modo proattivo](hunting.md)
- [Usare i segnalibri per salvare informazioni utili durante la ricerca](bookmarks.md)
