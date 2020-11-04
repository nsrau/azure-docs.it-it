---
title: Creare ed eseguire il training dei modelli
titleSuffix: Azure Machine Learning
description: Informazioni su come eseguire il training dei modelli con Azure Machine Learning, tra cui l'uso di Framework diffusi come Scikit-learn, TensorFlow e PyTorch. Le pipeline di Machine Learning semplificano la pianificazione delle esecuzioni automatiche, l'uso di ambienti di calcolo eterogenei e il riutilizzo di parti del flusso di lavoro. Le configurazioni di esecuzione forniscono un controllo granulare sulle destinazioni di calcolo in cui viene eseguito il processo di training.
services: machine-learning
ms.service: machine-learning
author: Blackmist
ms.author: larryfr
ms.subservice: core
ms.topic: conceptual
ms.date: 05/13/2020
ms.custom: devx-track-python
ms.openlocfilehash: 8888393cdbc738525b89ace1cf6f5864b7aa3b6e
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93324811"
---
# <a name="train-models-with-azure-machine-learning"></a>Eseguire il training di modelli con Azure Machine Learning

Azure Machine Learning offre diversi modi per eseguire il training dei modelli, dalle soluzioni Code-First che usano l'SDK a soluzioni di basso livello, ad esempio Machine Learning automatico e la finestra di progettazione visiva. Usare l'elenco seguente per determinare il metodo di training più adatto:

+ [Azure Machine Learning SDK per Python](#python-sdk): Python SDK offre diversi metodi per eseguire il training dei modelli, ognuno con diverse capacità.

    | Metodo di training | Descrizione |
    | ----- | ----- |
    | [Configurazione di esecuzione](#run-configuration) | Un **modo tipico per** eseguire il training di modelli consiste nell'usare uno script di training ed eseguire la configurazione. La configurazione di esecuzione offre le informazioni necessarie per configurare l'ambiente di training usato per il training del modello. È possibile specificare lo script di training, la destinazione di calcolo e l'ambiente Azure ML nella configurazione di esecuzione ed eseguire un processo di training. |
    | [Machine Learning automatizzato](#automated-machine-learning) | Il Machine Learning automatizzato consente di **eseguire il training dei modelli senza conoscenze estese di data science o programmazione**. Per gli utenti con esperienza in data science e programmazione, fornisce un metodo per risparmiare tempo e risorse automatizzando la selezione dell'algoritmo e l'ottimizzazione degli iperparametri. Quando si usa Machine Learning automatizzato non è necessario definire una configurazione di esecuzione. |
    | [Pipeline di Machine Learning](#machine-learning-pipeline) | Le pipeline non costituiscono un metodo di training diverso, bensì un **modo per definire un flusso di lavoro attraverso passaggi modulari e riutilizzabili** che possono includere il training come parte del flusso di lavoro. Le pipeline di Machine Learning supportano l'uso di Machine Learning automatizzate e la configurazione per eseguire il training di modelli. Poiché le pipeline non sono incentrate specificatamente sul training, i motivi per l'uso di una pipeline sono più vasti rispetto agli altri metodi di training. In genere è possibile usare una pipeline nel caso in cui:<br>* Si desideri **pianificare processi automatici** come processi di training con esecuzione prolungata o preparazione dei dati.<br>* Si usino **più passaggi** coordinati nelle varie risorse di calcolo e posizioni di archiviazione.<br>* Si usi la pipeline come **modello riutilizzabile** per scenari specifici, come la riesecuzione del training o il punteggio batch.<br>* **Tracciamento e versione di origini dati, input e output** per il flusso di lavoro.<br>* Il flusso di lavoro è **implementato da diversi team che lavorano su specifici passaggi in modo indipendente**. I passaggi possono quindi essere uniti in una pipeline per implementare il flusso di lavoro. |

+ [SDK di Azure Machine Learning per r (anteprima)](#r-sdk-preview): l'SDK per r usa il pacchetto reticolare per l'associazione all'SDK Python di Azure Machine Learning. In questo modo è possibile accedere agli oggetti e ai metodi principali implementati in Python SDK da qualsiasi ambiente R.

+ **Designer** : Azure Machine Learning Designer fornisce un semplice punto di ingresso in machine learning per la creazione di modelli di prova o per gli utenti con poca esperienza di codifica. Consente di eseguire il training dei modelli sfruttando un'interfaccia utente basata sul Web con trascinamento della selezione. È possibile usare il codice Python come parte della progettazione o eseguire il training dei modelli senza scrivere alcun codice.

+ **Interfaccia della riga di comando** : L'interfaccia della riga di comando di Machine Learning fornisce i comandi per le attività comuni con Azure Machine Learning e viene spesso usata per **attività di script e automatizzazione**. Ad esempio, dopo aver creato uno script o una pipeline di training, è possibile usare l'interfaccia della riga di comando per avviare l'esecuzione del training in base a una pianificazione o quando vengono aggiornati i file di dati usati per il training. Per i modelli di training, fornisce i comandi che inviano processi di training. Consente di inviare processi usando le configurazioni di esecuzione o le pipeline.

Ognuno di questi metodi consente l'uso di diversi tipi di risorse di calcolo per il training. Complessivamente, queste risorse vengono definite [__destinazioni di calcolo__](concept-azure-machine-learning-architecture.md#compute-targets). Una destinazione di calcolo può essere un computer locale o una risorsa cloud, come un ambiente di calcolo di Azure Machine Learning, Azure HDInsight o una macchina virtuale remota.

## <a name="python-sdk"></a>Python SDK

Azure Machine Learning SDK per Python consente di creare ed eseguire flussi di lavoro di Machine Learning con Azure Machine Learning. È possibile interagire con il servizio da una sessione interattiva di Python, notebook Jupyter, Visual Studio Code o altro IDE.

* [Informazioni su Azure Machine Learning SDK per Python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)
* [Installare/aggiornare l'SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)
* [Configurare un ambiente di sviluppo per Azure Machine Learning](how-to-configure-environment.md)

### <a name="run-configuration"></a>Configurazione di esecuzione

È possibile definire un processo di training generico con Azure Machine Learning usando [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py). Viene quindi usata la configurazione di esecuzione dello script, insieme agli script di training per eseguire il training di un modello su una destinazione di calcolo.

È possibile iniziare con una configurazione di esecuzione per il computer locale e quindi passare a una configurazione per una destinazione di calcolo basata sul cloud in base alle proprie esigenze. Modificando la destinazione di calcolo, si modifica solo la configurazione di esecuzione in uso. Un'esecuzione registra inoltre le informazioni sul processo di training, ad esempio input, output e log.

* [Informazioni sulla configurazione di esecuzione](concept-azure-machine-learning-architecture.md#run-configurations)
* [Esercitazione: Eseguire il training del primo modello di Machine Learning](tutorial-1st-experiment-sdk-train.md)
* [Esempi: Jupyter Notebook e Python esempi di modelli di training](https://github.com/Azure/azureml-examples)
* [Procedura: configurare un'esecuzione di training](how-to-set-up-training-targets.md)

### <a name="automated-machine-learning"></a>Funzionalità automatizzate di Machine Learning

Definire le iterazioni, le impostazioni degli iperparametri, l'ingegneria delle funzionalità e altre impostazioni. Durante il training, Azure Machine Learning effettua tentativi in parallelo con diversi algoritmi e parametri. Il training si interrompe dopo aver raggiunto i criteri uscita definiti.

> [!TIP]
> Oltre a Python SDK, è possibile usare il Machine Learning automatizzato anche tramite [Azure Machine Learning Studio](https://ml.azure.com).

* [Informazioni su Machine Learning automatizzato](concept-automated-ml.md)
* [Esercitazione: Creare il primo modello di classificazione con apprendimento automatico](tutorial-first-experiment-automated-ml.md)
* [Esercitazione: Usare il servizio Machine Learning automatizzato per stimare le tariffe dei taxi](tutorial-auto-train-models.md)
* [Esempi: Esempi per il Machine Learning automatizzato con Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)
* [Procedura: Configurare esperimenti di ML automatizzato in Python](how-to-configure-auto-train.md)
* [Procedura: Eseguire il training automatico di un modello di previsione delle serie temporali](how-to-auto-train-forecast.md)
* [Procedura: Creare, esplorare e distribuire esperimenti di Machine Learning automatizzato con Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md)

### <a name="machine-learning-pipeline"></a>Pipeline di Machine Learning

Le pipeline di machine learning possono usare i metodi di training citati in precedenza. Le pipeline consentono perlopiù di creare un flusso di lavoro, pertanto comprendono più funzioni del semplice training dei modelli. In una pipeline è possibile eseguire il training di un modello usando Machine Learning automatizzato o le configurazioni di esecuzione.

* [Informazioni sulle pipeline di ML in Azure Machine Learning](concept-ml-pipelines.md)
* [Creare ed eseguire le pipeline di Machine Learning con Azure Machine Learning SDK](how-to-create-your-first-pipeline.md)
* [Esercitazione: Usare le pipeline di Azure Machine Learning per l'assegnazione del punteggio batch](tutorial-pipeline-batch-scoring-classification.md)
* [Esempi: Esempi per le pipeline di Machine Learning automatizzato con Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)
* [Esempi: Pipeline con Machine Learning automatizzato](https://aka.ms/pl-automl)

### <a name="understand-what-happens-when-you-submit-a-training-job"></a>Informazioni su cosa accade quando si invia un processo di training

Il ciclo di vita di formazione di Azure è composto da:

1. Comprime i file nella cartella del progetto, ignorando quelli specificati in _. amlignore_ o _. gitignore_
1. Scalabilità verticale del cluster di calcolo 
1. Compilazione o download di dockerfile nel nodo di calcolo 
    1. Il sistema calcola un hash di: 
        - Immagine di base 
        - Passaggi personalizzati di Docker (vedere [distribuire un modello usando un'immagine di base Docker personalizzata](./how-to-deploy-custom-docker-image.md))
        - YAML per la definizione conda (vedere [creare & usare gli ambienti software in Azure Machine Learning](./how-to-use-environments.md))
    1. Il sistema usa questo hash come chiave in una ricerca dell'area di lavoro Azure Container Registry (ACR)
    1. Se non viene trovato, viene cercata una corrispondenza nell'ACR globale
    1. Se non viene trovato, il sistema compila una nuova immagine (che verrà memorizzata nella cache e registrata con l'area di lavoro ACR)
1. Download del file di progetto compresso nell'archiviazione temporanea nel nodo di calcolo
1. Decompressione del file di progetto
1. Nodo di calcolo in esecuzione `python <entry script> <arguments>`
1. Salvataggio dei log, dei file di modello e di altri file scritti nell' `./outputs` account di archiviazione associato all'area di lavoro
1. Ridimensionamento del calcolo, inclusa la rimozione dell'archiviazione temporanea 

Se si sceglie di eseguire il training sul computer locale ("configura come esecuzione locale"), non è necessario usare Docker. È possibile usare Docker localmente se si sceglie (per un esempio, vedere la sezione [Configure ml pipeline](./how-to-debug-pipelines.md) ).

## <a name="r-sdk-preview"></a>R SDK (anteprima)

R SDK consente di usare il linguaggio R con Azure Machine Learning. L'SDK usa il pacchetto reticolare per l'associazione a SDK Python di Azure Machine Learning. Questo consente di accedere agli oggetti e ai metodi principali implementati in Python SDK da qualsiasi ambiente R.

Per altre informazioni, vedere gli articoli seguenti:

* [Esercitazione: Creare un modello di regressione logistica](tutorial-1st-r-experiment.md)
* [Informazioni di riferimento su Azure Machine Learning SDK per R](https://azure.github.io/azureml-sdk-for-r/index.html)

## <a name="azure-machine-learning-designer"></a>Finestra di progettazione di Azure Machine Learning

La finestra di progettazione consente di eseguire il training dei modelli utilizzando un'interfaccia di trascinamento nel Web browser.

+ [Informazioni sulla finestra di progettazione](concept-designer.md)
+ [Esercitazione: stimare il prezzo dell'automobile](tutorial-designer-automobile-price-train-score.md)

## <a name="many-models-solution-accelerator"></a>Acceleratore di soluzioni molti modelli

L'[acceleratore di soluzione molti modelli](https://aka.ms/many-models) (anteprima) si basa su Azure Machine Learning consente di eseguire il training, utilizzare e gestire centinaia o addirittura migliaia di modelli di Machine Learning.

Ad esempio, la creazione di un modello __per ogni istanza o unico__ negli scenari seguenti può produrre risultati migliori:

* Stima delle vendite per ogni singolo negozio
* Manutenzione predittiva per centinaia di pozzi di petrolio
* Adattamento di un'esperienza ai singoli utenti

Per altre informazioni, vedere [Acceleratore di soluzioni molti modelli](https://aka.ms/many-models) su GitHub.

## <a name="cli"></a>CLI

L'interfaccia della riga di comando di Machine Learning è un'estensione per l'interfaccia della riga di comando di Azure. Offre i comandi dell'interfaccia della riga di comando multipiattaforma per lavorare con Azure Machine Learning. In genere si usa l'interfaccia della riga di comando per automatizzare attività come ad esempio il training di un modello di Machine Learning.

* [Usare l'estensione dell'interfaccia della riga di comando per Azure Machine Learning](reference-azure-machine-learning-cli.md)
* [MLOps in Azure](https://github.com/microsoft/MLOps)

## <a name="vs-code"></a>Visual Studio Code

È possibile usare l'estensione VS Code per eseguire e gestire i processi di training. Per altre informazioni, vedere la Guida alle procedure per la [gestione delle risorse vs code](how-to-manage-resources-vscode.md#experiments) .

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [configurare un'esecuzione di training](how-to-set-up-training-targets.md).