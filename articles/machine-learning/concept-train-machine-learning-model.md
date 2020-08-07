---
title: Creare ed eseguire il training dei modelli
titleSuffix: Azure Machine Learning
description: Informazioni sui diversi metodi che è possibile usare per eseguire il training del modello con Azure Machine Learning. Gli oggetti estimator offrono un modo semplice per lavorare con i framework più diffusi, ad esempio Scikit-learn, TensorFlow, Keras, PyTorch e Chainer. Le pipeline di Machine Learning semplificano la pianificazione delle esecuzioni automatiche, l'uso di ambienti di calcolo eterogenei e il riutilizzo di parti del flusso di lavoro. Le configurazioni di esecuzione forniscono un controllo granulare sulle destinazioni di calcolo in cui viene eseguito il processo di training.
services: machine-learning
ms.service: machine-learning
author: Blackmist
ms.author: larryfr
ms.subservice: core
ms.topic: conceptual
ms.date: 05/13/2020
ms.custom: devx-track-python
ms.openlocfilehash: dee74c787f6546494d12ea582eab383fbd99079d
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87876904"
---
# <a name="train-models-with-azure-machine-learning"></a>Eseguire il training di modelli con Azure Machine Learning

Azure Machine Learning offre diversi metodi per eseguire il training dei modelli, dalle soluzioni Code First che usano l'SDK a soluzioni con basso uso di codice come il Machine Learning automatizzato e la finestra di progettazione visiva. Usare l'elenco seguente per determinare il metodo di training più adatto:

+ [Azure Machine Learning SDK per Python](#python-sdk): Python SDK offre diversi metodi per eseguire il training dei modelli, ognuno con diverse capacità.

    | Metodo di training | Descrizione |
    | ----- | ----- |
    | [Configurazione di esecuzione](#run-configuration) | Un **metodo generico per eseguire il training dei modelli** è usare uno script di training e la configurazione di esecuzione. La configurazione di esecuzione offre le informazioni necessarie per configurare l'ambiente di training usato per il training del modello. Con una configurazione di esecuzione, uno script di training e una destinazione di calcolo (l'ambiente di training) è possibile eseguire un processo di training. |
    | [Machine Learning automatizzato](#automated-machine-learning) | Il Machine Learning automatizzato consente di **eseguire il training dei modelli senza conoscenze estese di data science o programmazione**. Per gli utenti con esperienza in data science e programmazione, fornisce un metodo per risparmiare tempo e risorse automatizzando la selezione dell'algoritmo e l'ottimizzazione degli iperparametri. Quando si usa Machine Learning automatizzato non è necessario definire una configurazione di esecuzione. |
    | [Oggetti estimator](#estimators) | Le classi estimator **facilitano il training dei modelli basati su framework di Machine Learning comuni**. Sono disponibili classi estimator per **Scikit-learn**, **PyTorch**, **TensorFlow**, **Chainer**e **Ray RLlib**. È inoltre disponibile un oggetto estimator generico che può essere usato con framework che non dispongono di una classe estimator dedicata. Quando si usano gli oggetti estimator non è necessario definire una configurazione di esecuzione. |
    | [Pipeline di Machine Learning](#machine-learning-pipeline) | Le pipeline non costituiscono un metodo di training diverso, bensì un **modo per definire un flusso di lavoro attraverso passaggi modulari e riutilizzabili** che possono includere il training come parte del flusso di lavoro. Le pipeline di Machine Learning supportano l'uso del Machine Learning automatizzato, degli oggetti estimator e della configurazione di esecuzione per l'esecuzione del training dei modelli. Poiché le pipeline non sono incentrate specificatamente sul training, i motivi per l'uso di una pipeline sono più vasti rispetto agli altri metodi di training. In genere è possibile usare una pipeline nel caso in cui:<br>* Si desideri **pianificare processi automatici** come processi di training con esecuzione prolungata o preparazione dei dati.<br>* Si usino **più passaggi** coordinati nelle varie risorse di calcolo e posizioni di archiviazione.<br>* Si usi la pipeline come **modello riutilizzabile** per scenari specifici, come la riesecuzione del training o il punteggio batch.<br>* **Tracciamento e versione di origini dati, input e output** per il flusso di lavoro.<br>* Il flusso di lavoro è **implementato da diversi team che lavorano su specifici passaggi in modo indipendente**. I passaggi possono quindi essere uniti in una pipeline per implementare il flusso di lavoro. |

+ [Azure Machine Learning SDK per r](#r-sdk): l'SDK per r usa il pacchetto reticolare per eseguire l'associazione all'SDK Python di Azure Machine Learning. In questo modo è possibile accedere agli oggetti e ai metodi principali implementati in Python SDK da qualsiasi ambiente R.

+ **Finestra di progettazione**: La finestra di progettazione di Azure Machine Learning (anteprima) offre un semplice punto di ingresso nel Machine Learning per la creazione di modelli di verifica o per gli utenti con scarsa esperienza in codifica. Consente di eseguire il training dei modelli sfruttando un'interfaccia utente basata sul Web con trascinamento della selezione. È possibile usare il codice Python come parte della progettazione o eseguire il training dei modelli senza scrivere alcun codice.

+ **Interfaccia della riga di comando**: L'interfaccia della riga di comando di Machine Learning fornisce i comandi per le attività comuni con Azure Machine Learning e viene spesso usata per **attività di script e automatizzazione**. Ad esempio, dopo aver creato uno script o una pipeline di training, è possibile usare l'interfaccia della riga di comando per avviare l'esecuzione del training in base a una pianificazione o quando vengono aggiornati i file di dati usati per il training. Per i modelli di training, fornisce i comandi che inviano processi di training. Consente di inviare processi usando le configurazioni di esecuzione o le pipeline.

Ognuno di questi metodi consente l'uso di diversi tipi di risorse di calcolo per il training. Complessivamente, queste risorse vengono definite [__destinazioni di calcolo__](concept-azure-machine-learning-architecture.md#compute-targets). Una destinazione di calcolo può essere un computer locale o una risorsa cloud, come un ambiente di calcolo di Azure Machine Learning, Azure HDInsight o una macchina virtuale remota.

## <a name="python-sdk"></a>Python SDK

Azure Machine Learning SDK per Python consente di creare ed eseguire flussi di lavoro di Machine Learning con Azure Machine Learning. È possibile interagire con il servizio da una sessione interattiva di Python, notebook Jupyter, Visual Studio Code o altro IDE.

* [Informazioni su Azure Machine Learning SDK per Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Installare/aggiornare l'SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [Configurare un ambiente di sviluppo per Azure Machine Learning](how-to-configure-environment.md)

### <a name="run-configuration"></a>Configurazione di esecuzione

È possibile definire un processo di training generico con Azure Machine Learning usando [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py). La configurazione di esecuzione viene quindi usata insieme agli script di training per eseguire il training di un modello su una destinazione di calcolo.

È possibile iniziare con una configurazione di esecuzione per il computer locale e quindi passare a una configurazione per una destinazione di calcolo basata sul cloud in base alle proprie esigenze. Modificando la destinazione di calcolo, si modifica solo la configurazione di esecuzione in uso. Un'esecuzione registra inoltre le informazioni sul processo di training, ad esempio input, output e log.

* [Informazioni sulla configurazione di esecuzione](concept-azure-machine-learning-architecture.md#run-configurations)
* [Esercitazione: Eseguire il training del primo modello di Machine Learning](tutorial-1st-experiment-sdk-train.md)
* [Esempi: Esempi dei modelli di training con Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training)
* [Procedura: Configurare e usare le destinazioni di calcolo per il training del modello](how-to-set-up-training-targets.md)

### <a name="automated-machine-learning"></a>Funzionalità automatizzate di Machine Learning

Definire le iterazioni, le impostazioni degli iperparametri, l'ingegneria delle funzionalità e altre impostazioni. Durante il training, Azure Machine Learning effettua tentativi in parallelo con diversi algoritmi e parametri. Il training si interrompe dopo aver raggiunto i criteri uscita definiti. Quando si usano gli oggetti estimator non è necessario definire una configurazione di esecuzione.

> [!TIP]
> Oltre a Python SDK, è possibile usare il Machine Learning automatizzato anche tramite [Azure Machine Learning Studio](https://ml.azure.com).

* [Informazioni su Machine Learning automatizzato](concept-automated-ml.md)
* [Esercitazione: Creare il primo modello di classificazione con apprendimento automatico](tutorial-first-experiment-automated-ml.md)
* [Esercitazione: Usare il servizio Machine Learning automatizzato per stimare le tariffe dei taxi](tutorial-auto-train-models.md)
* [Esempi: Esempi per il Machine Learning automatizzato con Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)
* [Procedura: Configurare esperimenti di ML automatizzato in Python](how-to-configure-auto-train.md)
* [Procedura: Eseguire il training automatico di un modello di previsione delle serie temporali](how-to-auto-train-forecast.md)
* [Procedura: Creare, esplorare e distribuire esperimenti di Machine Learning automatizzato con Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md)

### <a name="estimators"></a>Oggetti estimator

Gli oggetti estimator facilitano l'esecuzione del training dei modelli con framework ML comuni. Se si usa **Scikit-learn**, **PyTorch**, **TensorFlow**, **Chainer** o **Ray RLlib** è consigliabile usare un oggetto estimator per il training. È inoltre disponibile un oggetto estimator generico che può essere usato con framework che non dispongono di una classe estimator dedicata. Quando si usano gli oggetti estimator non è necessario definire una configurazione di esecuzione.

* [Informazioni sugli oggetti estimator](concept-azure-machine-learning-architecture.md#estimators)
* [Esercitazione: Eseguire il training di modelli di classificazione delle immagini con dati MNIST e scikit-learn usando Azure Machine Learning](tutorial-train-models-with-aml.md)
* [Esempi: Esempi d'uso degli oggetti estimator con Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning)
* [Procedura: Creare oggetti estimator durante il training](how-to-train-ml-models.md)

### <a name="machine-learning-pipeline"></a>Pipeline di Machine Learning

Le pipeline di Machine Learning possono usare i metodi di training indicati in precedenza (configurazione di esecuzione, oggetti estimator e Machine Learning automatizzato). Le pipeline consentono perlopiù di creare un flusso di lavoro, pertanto comprendono più funzioni del semplice training dei modelli. In una pipeline è possibile eseguire il training di un modello usando il Machine Learning automatizzato, gli oggetti estimator o le configurazioni di esecuzione.

* [Informazioni sulle pipeline di ML in Azure Machine Learning](concept-ml-pipelines.md)
* [Creare ed eseguire le pipeline di Machine Learning con Azure Machine Learning SDK](how-to-create-your-first-pipeline.md)
* [Esercitazione: Usare le pipeline di Azure Machine Learning per l'assegnazione del punteggio batch](tutorial-pipeline-batch-scoring-classification.md)
* [Esempi: Esempi per le pipeline di Machine Learning automatizzato con Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)
* [Esempi: Pipeline con Machine Learning automatizzato](https://aka.ms/pl-automl)
* [Esempi: Pipeline con oggetti estimator](https://aka.ms/pl-estimator)

### <a name="understand-what-happens-when-you-submit-a-training-job"></a>Informazioni su cosa accade quando si invia un processo di training

Il ciclo di vita di formazione di Azure è composto da:

1. Comprime i file nella cartella del progetto, ignorando quelli specificati in _. amlignore_ o _. gitignore_
1. Scalabilità verticale del cluster di calcolo 
1. Compilazione o download di dockerfile nel nodo di calcolo 
    1. Il sistema calcola un hash di: 
        - Immagine di base 
        - Passaggi personalizzati di Docker (vedere [distribuire un modello usando un'immagine di base Docker personalizzata](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-custom-docker-image))
        - YAML per la definizione conda (vedere [creare & usare gli ambienti software in Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-use-environments))
    1. Il sistema usa questo hash come chiave in una ricerca dell'area di lavoro Azure Container Registry (ACR)
    1. Se non viene trovato, viene cercata una corrispondenza nell'ACR globale
    1. Se non viene trovato, il sistema compila una nuova immagine (che verrà memorizzata nella cache e registrata con l'area di lavoro ACR)
1. Download del file di progetto compresso nell'archiviazione temporanea nel nodo di calcolo
1. Decompressione del file di progetto
1. Nodo di calcolo in esecuzione`python <entry script> <arguments>`
1. Salvataggio dei log, dei file di modello e di altri file scritti nell' `./outputs` account di archiviazione associato all'area di lavoro
1. Ridimensionamento del calcolo, inclusa la rimozione dell'archiviazione temporanea 

Se si sceglie di eseguire il training sul computer locale ("configura come esecuzione locale"), non è necessario usare Docker. È possibile usare Docker localmente se si sceglie (per un esempio, vedere la sezione [Configure ml pipeline](https://docs.microsoft.com/azure/machine-learning/how-to-debug-pipelines#configure-ml-pipeline ) ).

## <a name="r-sdk"></a>R SDK

R SDK consente di usare il linguaggio R con Azure Machine Learning. L'SDK usa il pacchetto reticolare per l'associazione a SDK Python di Azure Machine Learning. Questo consente di accedere agli oggetti e ai metodi principali implementati in Python SDK da qualsiasi ambiente R.

Per altre informazioni, vedere gli articoli seguenti:

* [Esercitazione: Creare un modello di regressione logistica](tutorial-1st-r-experiment.md)
* [Informazioni di riferimento su Azure Machine Learning SDK per R](https://azure.github.io/azureml-sdk-for-r/index.html)

## <a name="azure-machine-learning-designer"></a>Finestra di progettazione di Azure Machine Learning

La finestra di progettazione consente di eseguire il training dei modelli utilizzando un'interfaccia di trascinamento nel Web browser.

+ [Informazioni sulla finestra di progettazione](concept-designer.md)
+ [Esercitazione: stimare il prezzo dell'automobile](tutorial-designer-automobile-price-train-score.md)
+ [Regressione: Stimare il prezzo](how-to-designer-sample-regression-automobile-price-basic.md)
+ [Classificazione: Stimare il reddito](how-to-designer-sample-classification-predict-income.md)
+ [Classificazione: Stimare varianza, appetibilità e upsell](how-to-designer-sample-classification-churn.md)
+ [Classificazione con script R personalizzato: Prevedere i ritardi dei voli](how-to-designer-sample-classification-flight-delay.md)
+ [Classificazione del testo: Set di dati SP 500 di Wikipedia](how-to-designer-sample-text-classification.md)

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

Informazioni su come [configurare gli ambienti di training](how-to-set-up-training-targets.md).
