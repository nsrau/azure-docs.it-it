---
title: Panoramica di Azure Notebooks
description: Eseguire i notebook di Jupyter nel cloud usando il servizio Azure Notebooks gratuito, in cui non è necessaria alcuna installazione o configurazione.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 9cea5a8e-c52d-4bdc-9e4a-cecdc1ad02c1
ms.service: azure
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 01/11/2019
ms.author: kraigb
ms.openlocfilehash: 9916b75a15098acbafc1cb1f6d44d948cf6de851
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57777720"
---
# <a name="overview-of-azure-notebooks"></a>Panoramica di Azure Notebooks

Azure Notebooks è un servizio ospitato gratuito che consente di sviluppare ed eseguire notebook di Jupyter nel cloud senza alcuna installazione. [Jupyter](https://jupyter.org/) (in precedenza noto come IPython) è un progetto open source che consente di combinare facilmente testo in formato Markdown, codice eseguibile, dati persistenti, grafica e visualizzazioni in un'unica area di disegno condivisibile, il *notebook* (immagine per gentile concessione di jupyter.org):

[![Esempi di notebook di Jupyter](https://jupyter.org/assets/jupyterpreview.png)](https://jupyter.org/assets/jupyterpreview.png#lightbox)

Grazie a questa combinazione avanzata di codice, grafica e testo esplicativo, Jupyter è molto diffuso per diversi scopi, tra cui istruzione di data science, pulizia e trasformazione dei dati, simulazione numerica, modellazione statistica e sviluppo di modelli di Machine learning.

## <a name="hassle-free-experience"></a>Esperienza senza problemi

Azure Notebooks consente di iniziare rapidamente a creare prototipi, eseguire operazioni relative a data science e ricerca accademica oppure imparare a programmare in Python:

- Un data scientist ha accesso immediato a un ambiente Anaconda completo senza alcuna installazione.
- Un insegnante può fornire un pratico ambiente Python agli studenti.
- Un moderatore può tenere un discorso o un webinar senza chiedere ai partecipanti di dedicare 45 minuti all'installazione del software.
- Uno sviluppatore o un appassionato può usare i notebook come blocco appunti rapido per il codice.

I notebook diventano ancora più efficaci quando gli utenti possono usarli per collaborare tramite un servizio cloud accessibile dal browser, ad esempio Azure Notebooks (in anteprima). Nel cloud gli utenti non devono installare Jupyter in locale o preoccuparsi della gestione di un ambiente. Il cloud facilita anche la condivisione dei notebook (e dei file di dati associati) con altri utenti autorizzati, evitando le complicazioni dovute alla condivisione dei notebook con strumenti esterni, ad esempio i repository del controllo del codice sorgente. Con Azure Notebooks, gli utenti possono anche copiare (o "clonare") i notebook nel proprio account per eseguire modifiche o sperimentazioni, il che è particolarmente utile per scopi di istruzione.

Poiché Azure Notebooks è una piattaforma per la creazione, l'esecuzione e la condivisione di codice, è possibile usarla per molti scenari diversi:

- Imparare un nuovo linguaggio di programmazione. Provare una delle [esercitazioni iniziali](https://notebooks.azure.com/Microsoft/projects/samples/html/Introduction%20to%20Python.ipynb)
- Scoprire la data science. Leggere il [libro di Jake VanderPlas](https://notebooks.azure.com/jakevdp/projects/PythonDataScienceHandbook)
- [Tenere un corso](https://notebooks.azure.com/garth-wells/projects/CUED-IA-Computing-Michaelmas) a centinaia di studenti
- Condurre un webinar online o durante una conferenza senza perdere tempo per l'installazione 
- Consentire agli utenti di GitHub di caricare ed eseguire direttamente i notebook [creando una notifica di avvio di GitHub](https://notebooks.azure.com/help/projects/sharing/create-a-github-badge)
- Creare [presentazioni di PowerPoint](https://notebooks.azure.com/help/jupyter-notebooks/slides) con diapositive contenenti codice eseguibile.

In breve, Azure Notebook consente di svolgere il proprio lavoro in modo più efficiente e produttivo.

> [!Note]
> Per altre informazioni su Jupyter, vedere [jupyter.org](https://jupyter.org/) e la [documentazione di Jupyter](https://jupyter-notebook.readthedocs.io/en/latest/).

## <a name="pricing-and-quotas"></a>Prezzi e quote

Azure Notebooks è un servizio gratuito, ma ogni progetto è limitato a 4 GB di memoria e 1 GB di dati per evitarne l'uso improprio. Gli utenti legittimi che superano questi limiti visualizzano una richiesta Captcha per continuare l'esecuzione dei notebook.

Per rilasciare tutti i limiti, accedere ad Azure Notebooks con un account che usa Azure Active Directory, ad esempio un account aziendale. Se tale account è associato a una sottoscrizione di Azure, è possibile connettersi a qualsiasi istanza di Azure Data Science Virtual Machine nell'ambito di tale sottoscrizione. Per altre informazioni, vedere [Gestire e configurare progetti - Livello di calcolo](configure-manage-azure-notebooks-projects.md#compute-tier).

## <a name="available-kernels-and-environments"></a>Ambienti e kernel disponibili

Per ogni notebook, si seleziona il kernel (ovvero l'ambiente di runtime) usato per eseguire tutte le celle di codice. Azure Notebooks supporta i kernel seguenti:

- Python 2.7 + Anaconda2-5.3.0
- Python 3.6 + Anaconda3-5.3.0
- Python 3.5 + Anaconda3-4.2.0 (sarà deprecato)
- R 3.4.1 + Microsoft R Open 3.4.1
- F# 4.1.9

Azure Notebooks include anche pacchetti aggiuntivi oltre alle distribuzioni di base. I kernel Python, ad esempio, includono le librerie numpy, pandas, scikit-learn, matplotlib e bokeh.

È anche possibile personalizzare un progetto per creare un ambiente per tutti i notebook in tale progetto. Per ulteriori informazioni, consultare [Avvio rapido: Creare un progetto con un ambiente personalizzato](quickstart-create-jupyter-notebook-project-environment.md).

Oltre alle distribuzioni di base, in Azure Notebooks sono preinstallati numerosi pacchetti aggiuntivi utili ai data scientist. È anche possibile installare pacchetti personalizzati usando il processo tipico per ogni linguaggio.

## <a name="pre-configured-jupyter-extensions"></a>Estensioni di Jupyter preconfigurate

Azure Notebooks è preconfigurato con le estensioni di Jupyter seguenti:

- [RISE](https://github.com/damianavila/RISE): estensione per le presentazioni di Jupyter (nota anche come live_reveal). Per altre informazioni, vedere [Run a notebook slideshow](present-jupyter-notebooks-slideshow.md) (Eseguire una presentazione di notebook).
- [JupyterLab](https://github.com/jupyterlab/jupyterlab): ambiente di calcolo completo per usare i notebook di Jupyter.
- [Altair](https://github.com/ellisonbg/altair): libreria di visualizzazione statistica dichiarativa per Python.
- [BQPlot](https://github.com/bloomberg/bqplot): framework di tracciato interattivo per Jupyter Notebooks.
- [IpyWidgets](https://github.com/jupyter-widgets/ipywidgets): widget HTML interattivi per Jupyter Notebooks.

## <a name="issues-and-getting-help"></a>Problemi e supporto

Poiché Azure Notebooks è ancora in anteprima, potrebbero verificarsi interruzioni temporanee del servizio che possono essere più di frequenti o più durature rispetto ad altri servizi di Azure. Alcune funzionalità potrebbero essere incomplete o contenere bug.

Al momento, non è consigliabile usare l'anteprima di Azure Notebooks per applicazioni critiche o per notebook e dati sensibili.

Per domande su Azure Notebooks, segnalare un problema nel [repository GitHub](https://github.com/Microsoft/AzureNotebooks/issues).

## <a name="next-steps"></a>Passaggi successivi  

- [Explore sample notebooks (Esplorare i notebook di esempio)](azure-notebooks-samples.md)

- Argomenti di avvio rapido:

  - [Creare e condividere un notebook](quickstart-create-share-jupyter-notebook.md)
  - [Clonare un notebook](quickstart-clone-jupyter-notebook.md)
  - [Eseguire la migrazione di un notebook di Jupyter locale](quickstart-migrate-local-jupyter-notebook.md)
  - [Usare un ambiente personalizzato](quickstart-create-jupyter-notebook-project-environment.md)
  - [Accedere e impostare un ID utente](quickstart-sign-in-azure-notebooks.md)

- Esercitazioni:

  - [Creare ed eseguire un notebook](tutorial-create-run-jupyter-notebook.md  )

- Procedure dettagliate:
  
  - [Creare e clonare progetti](create-clone-jupyter-notebooks.md)
  - [Configure and manage projects (Configurare e gestire i progetti)](configure-manage-azure-notebooks-projects.md)
  - [Install packages from within a notebook (Installare pacchetti da un notebook)](install-packages-jupyter-notebook.md)
  - [Eseguire una presentazione](present-jupyter-notebooks-slideshow.md)
  - [Usare i file di dati](work-with-project-data-files.md)
  - [Accedere alle risorse dati](access-data-resources-jupyter-notebooks.md)
  - [Usare Azure Machine Learning Services](use-machine-learning-services-jupyter-notebooks.md)
