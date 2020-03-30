---
title: Metodi di formazione del modello
titleSuffix: Azure Machine Learning
description: Informazioni sui diversi metodi che è possibile usare per eseguire il training del modello con Azure Machine Learning.Learn the different methods you can use to train model with Azure Machine Learning. Gli stimatori forniscono un modo semplice per lavorare con framework popolari come Scikit-learn, TensorFlow, Keras, PyTorch e Chainer. Le pipeline di Machine Learning semplificano la pianificazione di esecuzioni incustodite, l'utilizzo di ambienti di calcolo eterogenei e il riutilizzo di parti del flusso di lavoro. Le configurazioni di esecuzione forniscono inoltre un controllo granulare sulle destinazioni di calcolo in cui viene eseguito il processo di training.
services: machine-learning
ms.service: machine-learning
author: Blackmist
ms.author: larryfr
ms.subservice: core
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 68ad9cc47d68f7bc3ae952f7e458781cdc1c4ab2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79129761"
---
# <a name="train-models-with-azure-machine-learning"></a>Eseguire il training di modelli con Azure Machine Learning

Azure Machine Learning offre diversi modi per eseguire il training dei modelli, dalle prime soluzioni di codice che usano l'SDK alle soluzioni a basso codice, ad esempio l'apprendimento automatico e la finestra di progettazione visiva. Utilizzare l'elenco seguente per determinare il metodo di allenamento più adatto alle persone:

+ [Azure Machine Learning SDK per Python:](#python-sdk)Python SDK offre diversi modi per eseguire il training dei modelli, ognuno con funzionalità diverse.

    | Metodo di formazione | Descrizione |
    | ----- | ----- |
    | [Configurazione di esecuzione](#run-configuration) | Un **modo generico per eseguire** il training dei modelli consiste nell'utilizzare uno script di training e eseguire la configurazione. La configurazione di esecuzione fornisce le informazioni necessarie per configurare l'ambiente di training utilizzato per eseguire il training del modello. È possibile eseguire una configurazione di esecuzione, lo script di training e una destinazione di calcolo (l'ambiente di training) ed eseguire un processo di training. |
    | [Apprendimento automatico automatizzato](#automated-machine-learning) | L'apprendimento automatico consente di eseguire il training di **modelli senza un'ampia conoscenza scientifica dei dati o di programmazione.** Per le persone con un background di data science e programmazione, fornisce un modo per risparmiare tempo e risorse automatizzando la selezione degli algoritmi e l'ottimizzazione degli iperparametri. Non è necessario preoccuparsi di definire una configurazione di esecuzione quando si usa l'apprendimento automatico automatizzato. |
    | [Estimatori](#estimators) | Le classi di stima semplificano il **training dei modelli basati su framework**di apprendimento automatico più diffusi. Sono disponibili classi di stima per **Scikit-learn**, **PyTorch**, **TensorFlow**e **Chainer**. Esiste anche uno stimatore generico che può essere utilizzato con framework che non dispongono già di una classe di stima dedicata. Non è necessario preoccuparsi di definire una configurazione di esecuzione quando si utilizzano gli stimatori. |
    | [Pipeline di apprendimento automatico](#machine-learning-pipeline) | Le pipeline non sono un metodo di training diverso, ma un **modo per definire un flusso di lavoro utilizzando passaggi modulari e riutilizzabili,** che possono includere il training come parte del flusso di lavoro. Le pipeline di apprendimento automatico supportano l'apprendimento automatico automatico, gli stimatori e la configurazione di esecuzione per eseguire il training dei modelli. Poiché le pipeline non sono incentrate in modo specifico sulla formazione, le ragioni per l'utilizzo di una pipeline sono più varie rispetto agli altri metodi di training. In genere, è possibile usare una pipeline quando:Generally, you might use a pipeline when:<br>Si desidera **pianificare processi automatici,** ad esempio processi di training a esecuzione prolungata o preparazione dei dati.<br>- Utilizzare **più passaggi** che sono coordinati tra risorse di elaborazione eterogenee e posizioni di archiviazione.<br>Utilizzare la pipeline come **modello riutilizzabile** per scenari specifici, ad esempio la riqualificazione o il punteggio in batch.<br>* **Tenere traccia e versione origini dati, input e output** per il flusso di lavoro.<br>Il flusso di lavoro viene **implementato da diversi team che lavorano su passaggi specifici in modo indipendente.** I passaggi possono quindi essere uniti in una pipeline per implementare il flusso di lavoro. |

+ [Azure Machine Learning SDK per Python:](#r-sdk)l'SDK usa il pacchetto di reticolato per l'associazione a Python SDK di Azure Machine Learning.Azure Machine Learning SDK for Python : The SDK uses the reticular package to bind to Azure Machine Learning's Python SDK. In questo modo è possibile accedere agli oggetti e ai metodi di base implementati in Python SDK da qualsiasi ambiente R.

+ **Designer**: Progettazione di Azure Machine Learning (anteprima) offre un punto di ingresso semplice nell'apprendimento automatico per la creazione di prove di concetti o per gli utenti con poca esperienza di codifica. Consente di eseguire il training dei modelli usando un'interfaccia utente basata sul Web. È possibile usare il codice Python come parte della progettazione o eseguire il training di modelli senza scrivere codice.

+ **CLI:** l'interfaccia della riga di comando per l'apprendimento automatico fornisce comandi per attività comuni con Azure Machine Learning e viene spesso usata per **l'esecuzione di script e l'automazione delle attività.** Ad esempio, dopo aver creato uno script di training o una pipeline, è possibile usare l'interfaccia della riga di comando per avviare un'esecuzione di training in base a una pianificazione o quando i file di dati usati per il training vengono aggiornati. Per i modelli di training, fornisce comandi che inviano processi di training. Può inviare processi utilizzando configurazioni di esecuzione o pipeline.

Ognuno di questi metodi di training può usare diversi tipi di risorse di calcolo per il training. Collettivamente, queste risorse vengono definite destinazioni di [__calcolo__](concept-azure-machine-learning-architecture.md#compute-targets). Una destinazione di calcolo può essere un computer locale o una risorsa cloud, ad esempio un calcolo di Azure Machine Learning, Azure HDInsight o una macchina virtuale remota.

## <a name="python-sdk"></a>Python SDK

Azure Machine Learning SDK per Python consente di creare ed eseguire flussi di lavoro di Machine Learning con Azure Machine Learning.The Azure Machine Learning SDK for Python allows you to build and run machine learning workflows with Azure Machine Learning. È possibile interagire con il servizio da una sessione interattiva di Python, Blocchi appunti di Jupyter, codice di Visual Studio o altro IDE.

* [Che cos'è Azure Machine Learning SDK per PythonWhat is the Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Installare/aggiornare l'SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [Configurare un ambiente di sviluppo per Azure Machine Learning](how-to-configure-environment.md)

### <a name="run-configuration"></a>Configurazione di esecuzione

È possibile definire un processo di formazione generico con Azure Machine Learning usando [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py). Viene quindi usata la configurazione di esecuzione, insieme agli script di training per eseguire il training di un modello in una destinazione di calcolo.

È possibile iniziare con una configurazione di esecuzione per il computer locale e quindi passare a uno per una destinazione di calcolo basata su cloud in base alle esigenze. Quando si modifica la destinazione di calcolo, si modifica solo la configurazione di esecuzione in uso. Un'esecuzione registra anche informazioni sul processo di training, ad esempio gli input, gli output e i log.

* [Che cos'è una configurazione di esecuzione?](concept-azure-machine-learning-architecture.md#run-configurations)
* [Esercitazione: Addestrare il primo modello di MLTutorial: Train your first ML model](tutorial-1st-experiment-sdk-train.md)
* [Esempi: Jupyter Notebook esempi di modelli di formazione](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training)
* [Procedura: Impostare e usare gli obiettivi di calcolo per il training del modelloHow to: Set up and use compute targets for model training](how-to-set-up-training-targets.md)

### <a name="automated-machine-learning"></a>Funzionalità automatizzate di Machine Learning

Definire le iterazioni, le impostazioni degli iperparametri, la featurization e altre impostazioni. Durante il training, Azure Machine Learning tenta diversi algoritmi e parametri in parallelo. L'allenamento si interrompe una volta che raggiunge i criteri di uscita definiti. Non è necessario preoccuparsi di definire una configurazione di esecuzione quando si utilizzano gli stimatori.

> [!TIP]
> Oltre a Python SDK, è anche possibile usare Il Machine ML automatizzato tramite [Azure Machine Learning Studio.](https://ml.azure.com)

* [Informazioni su Machine Learning automatizzato](concept-automated-ml.md)
* [Esercitazione: Creare il primo modello di classificazione con l'apprendimento automatico automatizzatoTutorial: Create your first classification model with automated machine learning](tutorial-first-experiment-automated-ml.md)
* [Esercitazione: Utilizzare l'apprendimento automatico automatico per prevedere le tariffe dei taxiTutorial: Use automated machine learning to predict taxi fares](tutorial-auto-train-models.md)
* [Esempi: esempi di blocchi appunti di Jupyter per l'apprendimento automatico](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)
* [Procedura: configurare esperimenti mL automatizzati in PythonHow to: Configure automated ML experiments in Python](how-to-configure-auto-train.md)
* [Procedura: ademfare automaticamente un modello previsionale di serie temporaliHow to: Autotrain a time-series forecast model](how-to-auto-train-forecast.md)
* [Procedura: Creare, esplorare e distribuire esperimenti automatizzati di apprendimento automatico con Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md)

### <a name="estimators"></a>Estimatori

Gli stimatori semplificano il training dei modelli utilizzando framework ML più diffusi. Se si utilizza **Scikit-learn**, **PyTorch**, **TensorFlow**o **Chainer**, è consigliabile utilizzare uno stimatore per l'allenamento. Esiste anche uno stimatore generico che può essere utilizzato con framework che non dispongono già di una classe di stima dedicata. Non è necessario preoccuparsi di definire una configurazione di esecuzione quando si utilizzano gli stimatori.

* [Cosa sono gli stimatori?](concept-azure-machine-learning-architecture.md#estimators)
* [Esercitazione: Eseguire il training dei modelli di classificazione delle immagini con i dati MNIST e l'apprendimento con Azure Machine LearningTutorial: Train image classification models with MNIST data and scikit-learn using Azure Machine Learning](tutorial-train-models-with-aml.md)
* [Esempi: Esempi di Jupyter Notebook sull'uso degli stimatori](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning)
* [Procedura: Creare stimatori nella formazioneHow to: Create estimators in training](how-to-train-ml-models.md)

### <a name="machine-learning-pipeline"></a>Pipeline di apprendimento automatico

Le pipeline di apprendimento automatico possono usare i metodi di training menzionati in precedenza (configurazione eseguita, stimatori e apprendimento automatico automatizzato). Le pipeline riguardano più informazioni sulla creazione di un flusso di lavoro, pertanto comprendono molto di più della semplice formazione dei modelli. In una pipeline è possibile eseguire il training di un modello usando l'apprendimento automatico automatico, gli stimatori o eseguire configurazioni.

* [Che cosa sono le pipeline di Machine Learning in Azure Machine Learning?](concept-ml-pipelines.md)
* [Creare ed eseguire pipeline di Machine Learning con Azure Machine Learning SDKCreate and run machine learning pipelines with Azure Machine Learning SDK](how-to-create-your-first-pipeline.md)
* [Esercitazione: Usare le pipeline di Azure Machine Learning per il punteggio in batchTutorial: Use Azure Machine Learning Pipelines for batch scoring](tutorial-pipeline-batch-scoring-classification.md)
* [Esempi: esempi di Jupyter Notebook per pipeline di apprendimento automaticoExamples: Jupyter Notebook examples for machine learning pipelines](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)
* [Esempi: Pipeline con apprendimento automatico automatizzatoExamples: Pipeline with automated machine learning](https://aka.ms/pl-automl)
* [Esempi: Pipeline con stimatori](https://aka.ms/pl-estimator)

## <a name="r-sdk"></a>R SDK

The R SDK enables you to use the R language with Azure Machine Learning. L'SDK usa il pacchetto di reticolatura per l'associazione all'SDK Python di Azure Machine Learning.The SDK uses the retilate package to bind to Azure Machine Learning's Python SDK. In questo modo è possibile accedere agli oggetti e ai metodi di base implementati in Python SDK da qualsiasi ambiente R.

Per altre informazioni, vedere gli articoli seguenti:

* [Esercitazione: Creare un modello di regressione logisticaTutorial: Create a logistic regression model](tutorial-1st-r-experiment.md)
* [Azure Machine Learning SDK per R di riferimento](https://azure.github.io/azureml-sdk-for-r/index.html)

## <a name="azure-machine-learning-designer"></a>Finestra di progettazione di Azure Machine Learning

La finestra di progettazione consente di eseguire il training dei modelli utilizzando un'interfaccia di trascinamento della selezione nel Web browser.

+ [Cos'è il designer?](concept-designer.md)
+ [Tutorial : Prevedere il prezzo dell'automobile](tutorial-designer-automobile-price-train-score.md)
+ [Regressione: prevedere il prezzo](how-to-designer-sample-regression-automobile-price-basic.md)
+ [Classificazione: Prevedere il reddito](how-to-designer-sample-classification-predict-income.md)
+ [Classificazione: prevedere la varianza, l'appetency e l'up-selling](how-to-designer-sample-classification-churn.md)
+ [Classificazione con script R personalizzato: prevedere i ritardi dei voli](how-to-designer-sample-classification-flight-delay.md)
+ [Classificazione testo: Wikipedia SP 500 Dataset](how-to-designer-sample-text-classification.md)

## <a name="cli"></a>CLI

L'interfaccia della riga di comando per l'apprendimento automatico è un'estensione per l'interfaccia della riga di comando di Azure.The machine learning CLI is an extension for the Azure CLI Fornisce comandi CLI multipiattaforma per l'uso con Azure Machine Learning.It provides cross-platform CLI commands for working with Azure Machine Learning. In genere, si usa l'interfaccia della riga di comando per automatizzare le attività, ad esempio il training di un modello di apprendimento automatico.

* [Usare l'estensione CLI per Azure Machine LearningUse the CLI extension for Azure Machine Learning](reference-azure-machine-learning-cli.md)
* [MLOps on Azure](https://github.com/microsoft/MLOps)

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come configurare gli ambienti di [formazione.](how-to-set-up-training-targets.md)
