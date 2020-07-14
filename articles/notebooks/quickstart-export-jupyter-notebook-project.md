---
title: Esportare un progetto Jupyter Notebook da Azure Notebooks (anteprima)
description: Esportare rapidamente un progetto Jupyter Notebook.
ms.topic: quickstart
ms.date: 06/29/2020
ms.openlocfilehash: ad37db7e9bdb1251a3e62bd567960979f556b489
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/01/2020
ms.locfileid: "85832033"
---
# <a name="quickstart-export-a-jupyter-notebook-project-in-azure-notebooks-preview"></a>Avvio rapido: Esportare un progetto Jupyter Notebook in Azure Notebooks (anteprima)

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Questa guida di avvio rapido illustra come scaricare un progetto Azure Notebooks per l'uso in altre soluzioni Jupyter Notebook. 

## <a name="prerequisites"></a>Prerequisiti
Progetto Azure Notebooks esistente.

## <a name="export-an-azure-notebooks-project"></a>Esportare un progetto Azure Notebooks

1. Passare ad [Azure Notebooks](https://notebooks.azure.com) ed eseguire l'accesso. Per informazioni dettagliate, vedere [Avvio rapido: Accedere ad Azure Notebooks](quickstart-sign-in-azure-notebooks.md).

1. Dalla pagina del profilo pubblico selezionare **My Projects** (Progetti personali) nella parte superiore:

    ![Collegamento My Projects (Progetti personali) nella parte superiore della finestra del browser](media/quickstarts/my-projects-link.png)

1. Selezionare un progetto.
1. Fare clic sul pulsante "Download" per attivare il download di un file ZIP contenente tutti i file di progetto.
1. In alternativa, da una pagina specifica del progetto, fare clic sul pulsante "Download progetto" per scaricare tutti i file di un progetto specifico.

Dopo aver scaricato i file di progetto, è possibile usarli con altre soluzioni Jupyter Notebook. Alcune opzioni descritte nelle sezioni seguenti includono: 
- [Visual Studio Code](#use-notebooks-in-visual-studio-code)
- [Visual Studio Codespaces](#use-notebooks-in-visual-studio-codespaces)
- [Azure Machine Learning](#use-notebooks-with-azure-machine-learning)
- [Azure Lab Services](#use-azure-lab-services)
- [GitHub](#use-github)

## <a name="create-an-environment-for-notebooks"></a>Creare un ambiente per i notebook

Se si vuole creare un ambiente corrispondente a quello di Azure Notebooks (anteprima), è possibile usare il file di script fornito in GitHub.

1. Passare al [repository GitHub](https://github.com/microsoft/AzureNotebooks) di Azure Notebooks o [accedere direttamente alla cartella dell'ambiente](https://aka.ms/aznbrequirementstxt).
1. Da un prompt dei comandi passare alla directory che si vuole usare per i progetti.
1. Scaricare il contenuto della cartella dell'ambiente e seguire le istruzioni del file README per installare le dipendenze del pacchetto Azure Notebooks.


## <a name="use-notebooks-in-visual-studio-code"></a>Usare i notebook in Visual Studio Code

[VS Code](https://code.visualstudio.com/) è un editor di codice gratuito che può essere usato in locale o connesso a una risorsa di calcolo remota. In combinazione con l'estensione Python, offre un ambiente completo per lo sviluppo Python, inclusa un'esperienza nativa avanzata per l'uso di Jupyter Notebook. 

![Supporto di VS Code per Jupyter Notebook](media/vs-code-jupyter-notebook.png)

Dopo aver [scaricato](#export-an-azure-notebooks-project) i file di progetto, è possibile usarli con VS Code. Per indicazioni su come usare VS Code con Jupyter Notebook, vedere le esercitazioni [Uso di Jupyter Notebook in Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support) e [Data Science in Visual Studio Code](https://code.visualstudio.com/docs/python/data-science-tutorial).

È anche possibile usare lo [script dell'ambiente Azure Notebooks](#create-an-environment-for-notebooks) con Visual Studio Code per creare un ambiente corrispondente a quello di Azure Notebooks (anteprima).

## <a name="use-notebooks-in-visual-studio-codespaces"></a>Usare i notebook in Visual Studio Codespaces

Visual Studio Codespaces offre ambienti ospitati nel cloud in cui è possibile modificare i notebook usando Visual Studio Code o il Web browser. Offre la stessa esperienza Jupyter di VS Code, ma senza la necessità di installare nulla sul dispositivo. Se non si vuole configurare un ambiente locale e si preferisce una soluzione basata sul cloud, la creazione di un codespace è un'ottima opzione. Attività iniziali

1. [Scaricare](#export-an-azure-notebooks-project) i file di progetto.
1. [Creare un repository GitHub](https://help.github.com/github/getting-started-with-github/create-a-repo) per archiviare i notebook. 
1. [Aggiungere i file](https://help.github.com/github/managing-files-in-a-repository/adding-a-file-to-a-repository) al repository.
1. Configurare Visual Studio Codespaces nel [browser](https://docs.microsoft.com/visualstudio/online/how-to/browser), in [Visual Studio](https://docs.microsoft.com/visualstudio/online/how-to/vside) o in [Visual Studio Code](https://docs.microsoft.com/visualstudio/online/how-to/vscode).

## <a name="use-notebooks-with-azure-machine-learning"></a>Usare i notebook con Azure Machine Learning

Azure Machine Learning offre una piattaforma di Machine Learning end-to-end per consentire agli utenti di compilare e distribuire modelli più velocemente in Azure. Azure ML consente di eseguire Jupyter Notebook in una macchina virtuale o in un ambiente di calcolo cluster condiviso. Se si necessita di una soluzione basata sul cloud per il carico di lavoro ML con verifica esperimenti, gestione del set di dati e altre funzionalità, è consigliabile usare Azure Machine Learning. Per iniziare a usare Azure ML:

1. [Scaricare](#export-an-azure-notebooks-project) i file di progetto.
1. [Creare un'area di lavoro](../machine-learning/how-to-manage-workspace.md) nel portale di Azure.

   ![Creare un'area di lavoro](../machine-learning/media/how-to-manage-workspace/create-workspace.gif)
 
1. Aprire [Azure Studio (anteprima)](https://ml.azure.com/).
1. Usando la barra di spostamento a sinistra, selezionare **Notebook**.
1. Fare clic sul pulsante **Carica file** e caricare i file di progetto scaricati da Azure Notebooks.

Per altre informazioni su Azure ML e sull'esecuzione di Jupyter Notebook, è possibile vedere la [documentazione](../machine-learning/how-to-run-jupyter-notebooks.md) o provare il modulo [Introduzione a Machine Learning](https://docs.microsoft.com/learn/modules/intro-to-azure-machine-learning-service/) in Microsoft Learn.


## <a name="use-azure-lab-services"></a>Usare Azure Lab Services

[Azure Lab Services](https://azure.microsoft.com/services/lab-services/) consente ai docenti di configurare facilmente e fornire l'accesso su richiesta a macchine virtuali preconfigurate per un'intera classe. Se si sta cercando una soluzione per usare Jupyter Notebook e le risorse di calcolo cloud in un ambiente di classe personalizzato, Lab Services è un'ottima opzione.

![image](../lab-services/media/tutorial-setup-classroom-lab/new-lab-button.png)

 Dopo aver [scaricato](#export-an-azure-notebooks-project) i file di progetto, è possibile usarli con Azure Lab Services. Per indicazioni su come configurare un Lab, vedere [Configurare un lab per data science con Python e Jupyter Notebook](../lab-services/class-type-jupyter-notebook.md)

## <a name="use-github"></a>Usare GitHub

GitHub offre un modo gratuito basato sul controllo del codice sorgente per archiviare notebook (e altri file), condividere i notebook con altri utenti e collaborare in modo efficace. Se si sta cercando una soluzione per condividere i progetti e collaborare con altri utenti, GitHub è un'ottima opzione e può essere combinato con [Visual Studio Codespaces](#use-notebooks-in-visual-studio-codespaces) per un'esperienza di sviluppo ottimale. Per iniziare a usare GitHub

1. [Scaricare](#export-an-azure-notebooks-project) i file di progetto.
1. [Creare un repository GitHub](https://help.github.com/github/getting-started-with-github/create-a-repo) per archiviare i notebook. 
1. [Aggiungere i file](https://help.github.com/github/managing-files-in-a-repository/adding-a-file-to-a-repository) al repository.

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni su Python in Visual Studio Code](https://code.visualstudio.com/docs/python/python-tutorial)
- [Informazioni su Azure Machine Learning Jupyter Notebook](../machine-learning/how-to-run-jupyter-notebooks.md)
- [Informazioni su Visual Studio Codespaces](https://visualstudio.microsoft.com/services/visual-studio-codespaces/)
- [Informazioni su Azure Lab Services](https://azure.microsoft.com/services/lab-services/)
- [Informazioni su GitHub](https://help.github.com/github/getting-started-with-github/)