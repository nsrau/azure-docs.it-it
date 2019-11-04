---
title: Metodi di training del modello
titleSuffix: Azure Machine Learning
description: Informazioni sui diversi metodi che è possibile usare per eseguire il training del modello con Azure Machine Learning. Gli estimatori forniscono un modo semplice per lavorare con i Framework più diffusi, ad esempio Scikit-learn, TensorFlow, keras, PyTorch e Chainer. Machine Learning pipeline semplificano la pianificazione di esecuzioni automatiche, l'uso di ambienti di calcolo eterogeneo e il riutilizzo di parti del flusso di lavoro. Le configurazioni di esecuzione forniscono un controllo granulare sulle destinazioni di calcolo in cui viene eseguito il processo di training.
services: machine-learning
ms.service: machine-learning
author: Blackmist
ms.author: larryfr
ms.subservice: core
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: ac7ae0d7933e1d1b4d716eb157bf74152155a969
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497343"
---
# <a name="train-models-with-azure-machine-learning"></a>Eseguire il training di modelli con Azure Machine Learning

Azure Machine Learning offre diversi modi per eseguire il training dei modelli, dalle soluzioni Code First che usano l'SDK a soluzioni di codice basso, ad esempio Machine Learning automatizzato e la finestra di progettazione visiva. Usare l'elenco seguente per determinare quale metodo di training è adatto alle proprie proprie attività:

+ [SDK di Azure Machine Learning per Python](#python-sdk): Python SDK offre diversi modi per eseguire il training dei modelli, ognuno con funzionalità diverse.

    | Metodo di training | Description |
    | ----- | ----- |
    | [Configurazione di esecuzione](#run-configuration) | Un **modo generico per** eseguire il training di modelli consiste nell'usare uno script di training ed eseguire la configurazione. La configurazione di esecuzione fornisce le informazioni necessarie per configurare l'ambiente di training utilizzato per il training del modello. È possibile eseguire una configurazione di esecuzione, uno script di training e una destinazione di calcolo (ambiente di training) ed eseguire un processo di training. |
    | [Machine Learning automatizzato](#automated-machine-learning) | Automatizzato Machine Learning consente di eseguire il **training di modelli senza approfondite Data Science o conoscenze di programmazione**. Per gli utenti con data science e la programmazione, fornisce un modo per risparmiare tempo e risorse automatizzando la selezione dell'algoritmo e l'ottimizzazione degli iperparametri. Non è necessario preoccuparsi di definire una configurazione di esecuzione quando si usa Machine Learning automatizzato. |
    | [Estimatori](#estimators) | Le classi Estimator **semplificano il training di modelli basati su Framework di apprendimento automatico diffusi**. Sono disponibili classi Estimator per **Scikit-learn**, **PyTorch**, **TensorFlow**e **Chainer**. È disponibile anche uno strumento di stima generico che può essere usato con Framework che non dispongono già di una classe Estimator dedicata. Non è necessario preoccuparsi di definire una configurazione di esecuzione quando si usano gli estimatori. |
    | [Pipeline di Machine Learning](#machine-learning-pipeline) | Le pipeline non sono un metodo di training diverso, ma un **modo per definire un flusso di lavoro usando passaggi modulari e riutilizzabili**che possono includere il training come parte del flusso di lavoro. Le pipeline di Machine Learning supportano l'uso automatico di Machine Learning, gli estimatori e la configurazione di esecuzione per eseguire il training di modelli. Poiché le pipeline non sono focalizzate in modo specifico sul training, i motivi per usare una pipeline sono più diversi rispetto agli altri metodi di training. In genere, è possibile usare una pipeline nei casi seguenti:<br>* Si desidera **pianificare processi automatici** , ad esempio processi di training con esecuzione prolungata o preparazione dei dati.<br>* Usare **più passaggi** coordinati tra le risorse di calcolo eterogenee e le posizioni di archiviazione.<br>* Utilizzare la pipeline come **modello riutilizzabile** per scenari specifici, ad esempio la ripetizione del training o il Punteggio batch.<br>* le **origini dati di rilevamento e versione, gli input e gli output** del flusso di lavoro.<br>* Il flusso **di lavoro viene implementato da team diversi che lavorano su passaggi specifici in modo indipendente**. I passaggi possono quindi essere Uniti in una pipeline per implementare il flusso di lavoro. |

+ **Finestra di progettazione**: Azure Machine Learning Designer (anteprima) offre un punto di ingresso semplice in machine learning per la creazione di modelli di prova o per gli utenti con poca esperienza di codifica. Consente di eseguire il training di modelli usando un'interfaccia utente basata sul Web di trascinamento. È possibile usare il codice Python come parte della progettazione o eseguire il training di modelli senza scrivere codice.

+ **Interfaccia**della riga di comando: l'interfaccia della riga di comando di Machine Learning fornisce comandi per attività comuni con Azure Machine Learning e viene spesso usata per **lo script e l'automazione delle attività**. Ad esempio, dopo aver creato uno script di training o una pipeline, è possibile usare l'interfaccia della riga di comando per avviare un'esecuzione di training in base a una pianificazione o quando vengono aggiornati i file di dati usati per il training. Per i modelli di training, fornisce i comandi che inviano processi di training. Può inviare processi usando le configurazioni di esecuzione o le pipeline.

Ognuno di questi metodi di training può utilizzare diversi tipi di risorse di calcolo per il training. Queste risorse sono definite collettivamente come destinazioni di [__calcolo__](concept-azure-machine-learning-architecture.md#compute-targets). Una destinazione di calcolo può essere un computer locale o una risorsa cloud, ad esempio un Azure Machine Learning calcolo, Azure HDInsight o una macchina virtuale remota.

## <a name="python-sdk"></a>Python SDK

Il Azure Machine Learning SDK per Python consente di compilare ed eseguire flussi di lavoro di Machine Learning con Azure Machine Learning. È possibile interagire con il servizio da una sessione interattiva di Python, notebook Jupyter, Visual Studio Code o altro IDE.

* [Che cos'è l'SDK Azure Machine Learning per Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Installare/aggiornare l'SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [Configurare un ambiente di sviluppo per Azure Machine Learning](how-to-configure-environment.md)

### <a name="run-configuration"></a>Configurazione di esecuzione

È possibile definire un processo di training generico con Azure Machine Learning usando [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py). Viene quindi usata la configurazione di esecuzione, insieme agli script di training per eseguire il training di un modello in una destinazione di calcolo.

È possibile iniziare con una configurazione di esecuzione per il computer locale e quindi passare a uno per una destinazione di calcolo basata sul cloud in base alle esigenze. Quando si modifica la destinazione di calcolo, si modifica solo la configurazione di esecuzione che si usa. Un'esecuzione registra anche le informazioni sul processo di training, ad esempio gli input, gli output e i log.

* [Che cos'è una configurazione di esecuzione?](concept-azure-machine-learning-architecture.md#run-configurations)
* [Esercitazione: eseguire il training del primo modello ML](tutorial-1st-experiment-sdk-train.md)
* [Esempi: Jupyter Notebook esempi di modelli di training](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training)
* [Procedura: impostare e usare le destinazioni di calcolo per il training del modello](how-to-set-up-training-targets.md)

### <a name="automated-machine-learning"></a>Funzionalità automatizzate di Machine Learning

Definire le iterazioni, le impostazioni iperparametri, conteggi e altre impostazioni. Durante il training, Azure Machine Learning tenta in parallelo diversi algoritmi e parametri. Il training si interrompe dopo aver raggiunto i criteri di uscita definiti. Non è necessario preoccuparsi di definire una configurazione di esecuzione quando si usano gli estimatori.

> [!TIP]
> In aggiunta per Python SDK è possibile usare anche Machine Learning Machine Learning con [Azure Machine Learning Studio](https://ml.azure.com).

* [Che cos'è Machine Learning automatico?](concept-automated-ml.md)
* [Esercitazione: creare il primo modello di classificazione con Machine Learning automatico](tutorial-first-experiment-automated-ml.md)
* [Esercitazione: usare Machine Learning automatizzato per prevedere le tariffe dei taxi](tutorial-auto-train-models.md)
* [Esempi: esempi di Jupyter Notebook per Machine Learning automatizzato](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)
* [Procedura: configurare esperimenti di Machine Learning automatici in Python](how-to-configure-auto-train.md)
* [Procedura: eseguire il training di un modello di previsione delle serie temporali](how-to-auto-train-forecast.md)
* [Procedura: creare, esplorare e distribuire esperimenti di Machine Learning automatici con [Azure Machine Learning Studio](how-to-create-portal-experiments.md)

### <a name="estimators"></a>Estimatori

Gli estimatori rendono più semplice il training di modelli con i Framework di ML più diffusi. Se si usa **Scikit-learn**, **PyTorch**, **TensorFlow**o **Chainer**, è consigliabile usare un estimatore per il training. È disponibile anche uno strumento di stima generico che può essere usato con Framework che non dispongono già di una classe Estimator dedicata. Non è necessario preoccuparsi di definire una configurazione di esecuzione quando si usano gli estimatori.

* [Che cosa sono gli estimatori?](concept-azure-machine-learning-architecture.md#estimators)
* [Esercitazione: eseguire il training di modelli di classificazione delle immagini con i dati MNIST e Scikit-learn usando Azure Machine Learning](tutorial-train-models-with-aml.md)
* [Esempi: Jupyter Notebook esempi di utilizzo degli estimatori](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning)
* [Procedura: creare estimatori nel training](how-to-train-ml-models.md)

### <a name="machine-learning-pipeline"></a>Pipeline di Machine Learning

Le pipeline di machine learning possono usare i metodi di formazione indicati in precedenza (configurazione di esecuzione, estimatori e Machine Learning automatica). Le pipeline sono altre informazioni sulla creazione di un flusso di lavoro, quindi comprendono più del training dei modelli. In una pipeline è possibile eseguire il training di un modello usando Machine Learning automatizzati, gli estimatori o le configurazioni di esecuzione.

* [Che cosa sono le pipeline di ML in Azure Machine Learning?](concept-ml-pipelines.md)
* [Creare ed eseguire pipeline di Machine Learning con Azure Machine Learning SDK](how-to-create-your-first-pipeline.md)
* [Esercitazione: usare pipeline di Azure Machine Learning per il Punteggio batch](tutorial-pipeline-batch-scoring-classification.md)
* [Esempi: Jupyter Notebook esempi per le pipeline di Machine Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)
* [Esempi: pipeline con Machine Learning automatizzato](https://aka.ms/pl-automl)
* [Esempi: pipeline con estimatori](https://aka.ms/pl-estimator)

## <a name="azure-machine-learning-designer"></a>Finestra di progettazione Azure Machine Learning

La finestra di progettazione consente di eseguire il training dei modelli usando un'interfaccia di trascinamento nel Web browser.

+ [Che cos'è la finestra di progettazione?](concept-designer.md)
+ [Esercitazione: stimare il prezzo dell'automobile](tutorial-designer-automobile-price-train-score.md)
+ [Regressione: stima del prezzo](how-to-designer-sample-regression-automobile-price-basic.md)
+ [Classificazione: stima del reddito](how-to-designer-sample-classification-predict-income.md)
+ [Classificazione: stima varianza, appetency e vendita](how-to-designer-sample-classification-churn.md)
+ [Classificazione con script R personalizzato: prevedere i ritardi dei voli](how-to-designer-sample-classification-flight-delay.md)
+ [Classificazione del testo: set di dati di Wikipedia SP 500](how-to-designer-sample-text-classification.md)

## <a name="cli"></a>Interfaccia della riga di comando

L'interfaccia della riga di comando di Machine Learning è un'estensione di Azure CLI. Fornisce i comandi dell'interfaccia della riga di comando multipiattaforma per lavorare con Azure Machine Learning. In genere si usa l'interfaccia della riga di comando per automatizzare le attività, ad esempio il training di un modello di machine learning.

* [Usare l'estensione CLI per Azure Machine Learning](reference-azure-machine-learning-cli.md)
* [MLOps in Azure](https://github.com/microsoft/MLOps)

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [configurare gli ambienti di training](how-to-set-up-training-targets.md).
