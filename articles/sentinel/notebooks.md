---
title: Funzionalità di caccia usando i notebook nell'anteprima di Azure Sentinel | Microsoft Docs
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
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 6372a7958caf108903321e5ee87ea6bf1a42271c
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68689555"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>Usare i notebook di Jupyter per cercare minacce per la sicurezza

> [!IMPORTANT]
> Azure Sentinel è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

La base di Azure Sentinel è l'archivio dati. combina le query a prestazioni elevate, lo schema dinamico e le scale a volumi di dati di grandi dimensioni. Il portale di Azure Sentinel e tutti gli strumenti di Azure Sentinel usano un'API comune per accedere a questo archivio dati. La stessa API è disponibile anche per strumenti esterni, ad esempio [Jupyter](https://jupyter.org/) notebook e Python. Mentre molte attività comuni possono essere eseguite nel portale, Jupyter estende l'ambito delle operazioni che è possibile eseguire con questi dati. Combina la programmabilità completa con una vasta raccolta di librerie per l'apprendimento automatico, la visualizzazione e l'analisi dei dati. Questi attributi rendono Jupyter uno strumento accattivante per l'analisi della sicurezza e la ricerca.

![Notebook di esempio](./media/notebooks/sentinel-notebooks-map.png)

L'esperienza Jupyter è stata integrata nel portale di Azure Sentinel, semplificando la creazione e l'esecuzione di notebook per l'analisi dei dati. La libreria *Kqlmagic* fornisce l'associazione che consente di eseguire query da Azure Sentinel ed eseguirle direttamente all'interno di un notebook. Le query usano il [linguaggio di query kusto](https://kusto.azurewebsites.net/docs/query/index.html). Diversi notebook, sviluppati da alcuni analisti della sicurezza di Microsoft, sono inclusi in un pacchetto di Azure Sentinel. Alcuni di questi notebook sono compilati per uno scenario specifico e possono essere usati così come sono. Altri sono intesi come esempi per illustrare le tecniche e le funzionalità che è possibile copiare o adattare per l'uso nei propri notebook. Altri notebook possono anche essere importati dalla community di Azure Sentinel GitHub.

L'esperienza Jupyter integrata usa [Azure Notebooks](https://notebooks.azure.com/) per archiviare, condividere ed eseguire notebook. È anche possibile eseguire questi notebook localmente (se si dispone di un ambiente Python e Jupyter nel computer) o in altri ambienti JupterHub come Azure Databricks.

I notebook hanno due componenti:

- interfaccia basata su browser in cui si immettono ed eseguono query e codice e in cui vengono visualizzati i risultati dell'esecuzione.
- *kernel* responsabile dell'analisi e dell'esecuzione del codice stesso. 

In Azure Notebooks, questo kernel viene eseguito in risorse di *calcolo e archiviazione cloud gratuite* di Azure per impostazione predefinita. Se i notebook includono modelli di machine learning complessi o visualizzazioni, è consigliabile usare risorse di calcolo più potenti e dedicate, ad esempio [macchine virtuali per l'analisi scientifica dei dati](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) (DSVM). I notebook nell'account vengono mantenuti privati a meno che non si scelga di condividerli.

I notebook di Sentinel di Azure usano molte librerie Python più diffuse, ad esempio Pandas, Matplotlib, bokeh e altri. È possibile scegliere tra un numero elevato di altri pacchetti Python, coprendo aree come le seguenti:

- Visualizzazioni e grafica
- elaborazione e analisi dei dati
- Statistiche e calcolo numerico
- apprendimento automatico e apprendimento avanzato

Sono stati rilasciati anche alcuni strumenti di sicurezza Jupyter open source in un pacchetto denominato [msticpy](https://github.com/Microsoft/msticpy/). Questo pacchetto viene usato in molti dei notebook inclusi. Gli strumenti Msticpy sono progettati in modo specifico per semplificare la creazione di notebook per la ricerca e l'analisi e stiamo lavorando attivamente a nuove funzionalità e miglioramenti.

I notebook iniziali includono:

- **Analisi guidata-elabora avvisi**: Consente di valutare rapidamente gli avvisi analizzando l'attività sugli host interessati.
- **Caccia guidata-Esplora host di Windows**: Consente di esplorare l'attività dell'account, le esecuzioni di processi, le attività di rete e altri eventi in un host.  
- **Caccia guidata-Office365-esplorazione**: Cercare attività sospette di Office 365 in più set di dati O365.

Il [repository GitHub della community di Azure Sentinel](https://github.com/Azure/Azure-Sentinel) è la località per tutti i futuri notebook di Azure Sentinel creati da Microsoft o che hanno contribuito alla community.

## <a name="run-a-notebook"></a>Eseguire un notebook

Nell'esempio seguente viene creato un progetto Azure Notebooks dal portale di Azure Sentinel, che popola il progetto con i notebook. Prima di usare questi notebook, è consigliabile creare una copia del notebook e lavorare sulla copia. L'uso delle copie consente di eseguire l'aggiornamento in modo sicuro alle versioni future dei notebook senza sovrascrivere i dati.

1. Nel portale di Azure Sentinel fare clic su **notebook** nel menu di navigazione. Per creare un nuovo progetto di Azure Notebooks, fare clic su **clona notebook di Azure Sentinel** o aprire i progetti notebook esistenti e fare clic su **Vai ai notebook**.
  
   ![Seleziona notebook](./media/notebooks/sentinel-azure-notebooks-home.png)

2. Se si sceglie **Clone Azure Sentinel Notebooks** nel passaggio precedente, verrà visualizzata la finestra di dialogo seguente. Fare clic su **Importa** per clonare il repository GitHub nel progetto Azure Notebooks. Se non si dispone di un account Azure Notebooks esistente, verrà richiesto di crearne uno ed effettuare l'accesso.

   ![Importa notebook](./media/notebooks/sentinel-notebooks-clone.png)

3. Quando si crea un nuovo progetto, è necessario assegnare un nome al progetto. utilizzare il nome predefinito o il tipo in un nuovo progetto. Non selezionare l'opzione **clona** in modo ricorsivo. questa opzione fa riferimento ai repository GitHub collegati. Facendo clic su **Importa** viene avviata la clonazione del contenuto GitHub, che può richiedere alcuni minuti per il completamento.

   ![Importa notebook](./media/notebooks/sentinel-create-project.png)

4. Aprire la cartella **notebook** per visualizzare i notebook. Ogni notebook illustra i passaggi per eseguire una ricerca o un'indagine. Le librerie e altre dipendenze necessarie per il notebook possono essere installate dal notebook stesso o tramite una semplice procedura di configurazione. La configurazione che associa il progetto notebook alla sottoscrizione di Azure Sentinel viene automaticamente fornita nei passaggi precedenti. I notebook sono pronti per l'esecuzione nell'area di lavoro di Azure Sentinel Log Analytics.

   ![Importa repository](./media/notebooks/sentinel-open-notebook1.png)

5. Aprire un notebook. Il calcolo gratuito è selezionato per impostazione predefinita per l'esecuzione dei notebook (evidenziato). Se è stato configurato un DSVM da usare (vedere sopra), selezionare il DSVM ed eseguire l'autenticazione prima di aprire il primo notebook. Fare clic su un notebook per aprirlo.

   ![Seleziona notebook](./media/notebooks/sentinel-open-notebook2.png)

6. Selezione della versione di Python. Quando si apre per la prima volta un notebook, è possibile che venga richiesto di selezionare una versione del kernel. In caso contrario, selezionare il kernel da usare come indicato di seguito. Python 3,6 o versione successiva deve essere il kernel selezionato (nella parte superiore destra della finestra del notebook).

   ![Seleziona notebook](./media/notebooks/sentinel-select-kernel.png)

Per una rapida introduzione all'esecuzione di query sui dati in Sentinel di Azure, vedere il notebook [getstarted](https://github.com/Azure/Azure-Sentinel/blob/master/Notebooks/Get%20Started.ipynb) nella cartella principale Notebooks. È possibile trovare notebook di esempio aggiuntivi nella sottocartella **Sample-Notebooks** . I notebook di esempio sono stati salvati con i dati, in modo che sia più semplice visualizzare l'output desiderato (si consiglia di visualizzarli in [nbviewer](https://nbviewer.jupyter.org/)). La cartella **HOWTO** contiene i notebook che descrivono, ad esempio: impostazione della versione predefinita di Python, configurazione di un DSVM, creazione di segnalibri di Azure Sentinel da un notebook e altri argomenti.

Questi notebook sono intesi come strumenti utili e come illustrazioni ed esempi di codice che è possibile usare per lo sviluppo di Notebook personalizzati.

Commenti e suggerimenti, suggerimenti, richieste di funzionalità, notebook con contributi, report sui bug, miglioramenti e aggiunte ai notebook esistenti. Per creare un problema o un fork e caricare un contributo, visitare la [community di Azure Sentinel GitHub](https://github.com/Azure/Azure-Sentinel) .

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come iniziare a usare i notebook di Jupyter in Sentinel di Azure. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- [Cercare in modo proattivo le minacce](hunting.md)
- [Usare i segnalibri per salvare informazioni interessanti durante la ricerca](bookmarks.md)
