---
title: Le funzionalità di ricerca usando i notebook in Azure Sentinel Preview | Microsoft Docs
description: Questo articolo descrive come usare i notebook con le funzionalità di ricerca Sentinel di Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: ffe3ae5b6aa26d154928a74e51864a0574b82c68
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65228627"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>Usare i notebook di Jupyter per hunt per le minacce alla sicurezza

> [!IMPORTANT]
> Azure Sentinel è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Alla base di Azure Sentinel è l'archivio dati; combina l'esecuzione di query, lo schema dinamico e delle scale per i volumi di grandi quantità di dati ad alte prestazioni. Il portale di Azure Sentinel e tutti gli strumenti di Azure Sentinel usano un'API comune per accedere a questo archivio dati. La stessa API è disponibile anche per strumenti esterni, ad esempio [Jupyter](https://jupyter.org/) notebook e Python. Mentre molte attività comuni possono essere eseguite nel portale, Jupyter amplia l'ambito delle operazioni eseguibili con questi dati. Combina la programmabilità completa con una vasta raccolta di librerie per machine learning, visualizzazione, analisi dei dati e. Questi attributi rendono Jupyter uno strumento eccezionale per l'analisi di sicurezza e ricerca.

![Notebook di esempio](./media/notebooks/sentinel-nb-mapandtimeline.png)

Abbiamo abbiamo integrato l'esperienza di Jupyter nel portale di Azure Sentinel, rendendo più semplice per poter creare ed eseguire i notebook per analizzare i dati. Il *Kqlmagic* libreria fornisce l'associazione che consente di eseguire query da Sentinel Azure ed eseguirle direttamente all'interno di un notebook. Le query usano il [linguaggio di Query Kusto](https://kusto.azurewebsites.net/docs/query/index.html). Sono inclusi vari notebook, sviluppato da alcuni agli analisti della sicurezza di Microsoft, con Azure Sentinel. Alcuni di questi notebook viene compilati per uno scenario specifico e possono essere utilizzati come-è. Gli altri sono utili come esempi per illustrare tecniche e le funzionalità che è possibile copiare o adattare per l'uso nei tuoi notebook. Altri notebook possono anche essere importati dalla community di Azure Sentinel GitHub.

Usa l'esperienza integrata di Jupyter [Azure Notebooks](https://notebooks.azure.com/) per archiviare, condividere ed eseguire i notebook. È anche possibile eseguire questi notebook in locale (se si dispone di un ambiente di Python e Jupyter nel computer in uso) o in altri ambienti JupterHub, ad esempio Azure Databricks.

I notebook includono due componenti:

- l'interfaccia basata su browser è immettere ed eseguire query e il codice e vengono visualizzati i risultati dell'esecuzione.
- una *kernel* che è responsabile per l'analisi e l'esecuzione del codice stesso. 

Nel notebook di Azure, questo kernel viene eseguito in Azure *archiviazione e calcolo Cloud gratuito* per impostazione predefinita. Se i blocchi appunti includono modelli di apprendimento automatico complessi o visualizzazioni è consigliabile usare più potente e dedicato le risorse di calcolo, ad esempio [macchine virtuali Data Science](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) (DSVM). Notebook nell'account vengono mantenute private a meno che non si sceglie di condividerli.

I notebook di Azure Sentinel usano molte librerie Python più diffusi, ad esempio pandas, matplotlib, bokeh e ad altri utenti. Esistono molti altri pacchetti Python per cui scegliere, che regola aree quali:

- elementi grafici e visualizzazioni
- l'elaborazione dati e analisi
- le statistiche e l'elaborazione numerica
- Machine learning e deep learning

Sono state rilasciate anche alcuni strumenti di protezione open-source Jupyter in un pacchetto denominato [msticpy](https://github.com/Microsoft/msticpy/). Questo pacchetto viene usato in numerosi notebook incluso. Gli strumenti Msticpy sono progettati appositamente per consentirvi di con la creazione di notebook per la ricerca e indagini e stiamo lavorando attivamente per trovare i miglioramenti e nuove funzionalità.

I notebook iniziali includono:

- **PGO indagine - processo avvisi**: Consente di valutare rapidamente gli avvisi dall'analisi dell'attività su host interessati.
- **PGO caccia - Esplora host Windows**: Consente di esplorare attività dell'account, esecuzioni di processi, attività di rete e altri eventi in un host.  
- **PGO caccia - esplorazione di Office365**: Risposta di ricerca di attività sospette di Office 365 in più set di dati di Office 365.

Il [Sentinel Community repository GitHub Azure](https://github.com/Azure/Azure-Sentinel) è il percorso per qualsiasi notebook Azure Sentinel future sviluppato da Microsoft o fornito dalla community.

## <a name="run-a-notebook"></a>Eseguire un notebook

Nell'esempio seguente, creiamo un progetto Azure Notebooks dal portale di Azure Sentinel, popolamento del progetto con i notebook. Prima di usare questi notebook, è consigliabile creare una copia del notebook e sulla copia di lavoro. Operazioni su copie consente di aggiornare in modo sicuro le versioni future di notebook senza sovrascrivere i dati.

1. Nel portale di Azure Sentinel, fare clic su **notebook** nel menu di navigazione. Per creare un nuovo progetto Azure Notebooks, fare clic su **Clone Azure Sentinel Notebooks** o per aprire il notebook esistente progetti fare clic su **Vai ai notebook**.
  
   ![Selezionare i notebook](./media/notebooks/sentinel-az-notebooks-home.png)

2. Se si è scelto **Clone Azure Sentinel Notebooks** nel passaggio precedente, verrà visualizzata la finestra di dialogo seguente. Fare clic su **importazione** clonare il repository di GitHub nel progetto Azure Notebooks. Se non hai un account Azure Notebooks esistente, verrà richiesto per crearne uno e l'accesso.

   ![Notebook di importazione](./media/notebooks/sentinel-nb-signin-and-clone.png)

3. Quando si crea un nuovo progetto, è necessario denominare il progetto, usare il nome predefinito o il tipo in uno nuovo. Non selezionare la **in modo ricorsivo Clone** opzione - questa opzione si riferisce al repository GitHub collegato. Facendo clic su **importazione** avvia la clonazione del contenuto di GitHub, che può richiedere alcuni minuti.

   ![Notebook di importazione](./media/notebooks/sentinel-create-nb-project.png)

4. Aprire il **notebook** cartella per visualizzare i notebook. Ogni notebook illustra i passaggi per la realizzazione di una risposta o un'indagine. Le librerie e altre dipendenze necessarie per il blocco appunti possono essere installati dal Notebook stesso o tramite una procedura di configurazione semplice. Configurazione che consente di associare il progetto di notebook nuovamente alla sottoscrizione di Azure Sentinel viene eseguito automaticamente il provisioning nei passaggi precedenti. I notebook sono pronti per eseguire l'area di lavoro di Azure Sentinel Log Analitica.

   ![Importazione del repository](./media/notebooks/sentinel-open-notebook1.png)

5. Aprire un Notebook. Elaborazione gratuita viene selezionato per impostazione predefinita per eseguire i notebook (evidenziati). Se è stata configurata una macchina virtuale data SCIENCE per l'uso (vedere sopra), selezionare la macchina virtuale data SCIENCE ed eseguire l'autenticazione prima di aprire il primo notebook. Fare clic su un notebook per aprirlo.

   ![Selezionare i notebook](./media/notebooks/sentinel-open-notebook2.png)

6. Selezionare la versione di Python. Quando si apre un notebook, è possibile che venga chiesto di selezionare una versione del kernel. In caso contrario, selezionare il kernel da utilizzare come indicato di seguito. Python 3.6 o versioni successive deve essere il kernel selezionato (nella parte superiore destra della finestra del blocco appunti).

   ![Selezionare i notebook](./media/notebooks/sentinel-select-kernel.png)

Per una rapida introduzione alle query di dati in Azure Sentinel, esaminare i [GetStarted](https://github.com/Azure/Azure-Sentinel/blob/master/Notebooks/Get%20Started.ipynb) notebook nella cartella principale notebook. I notebook di esempio aggiuntivi sono reperibili nel **notebook di esempio** sottocartella. Il notebook di esempio sono stati salvati i dati, in modo che risulti più semplice visualizzare l'output previsto (è consigliabile visualizzarli nella [nbviewer](https://nbviewer.jupyter.org/)). Il **HowToProcedure** cartella contiene la descrizione, ad esempio notebook: impostazione Python versione predefinita, la configurazione di una macchina virtuale data SCIENCE, creazione Sentinel Azure inserisce un segnalibro da un notebook e altri argomenti.

Questi notebook devono entrambi gli strumenti utili e illustrazioni ed esempi di codice che è possibile usare lo sviluppo dei tuoi notebook.

Saremo lieti di ricevere commenti, se i suggerimenti, le richieste di funzionalità, ha contribuito con i notebook, segnalazioni di bug o i miglioramenti e aggiunte ai notebook esistente. Andare alla [Azure Sentinel Community GitHub](https://github.com/Azure/Azure-Sentinel) per creare un problema o un fork e carica un contributo.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si appreso come iniziare a usare i notebook di Jupyter in Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- [Cercare in modo proattivo le minacce](hunting.md)
- [Usare i segnalibri per salvare le informazioni interessante durante la ricerca](bookmarks.md)