---
title: Selezione e ottimizzazione dell'algoritmo di ML automatizzato
titleSuffix: Azure Machine Learning service
description: Informazioni su come il servizio Azure Machine Learning è in grado di scegliere automaticamente un algoritmo e, a partire da questo, generare un modello per consentire all'utente di risparmiare tempo. Tale scelta viene effettuata usando i parametri e i criteri specificati per selezionare l'algoritmo migliore per il modello.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nacharya1
ms.author: nilesha
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 136141f5b598fd080edf3254fd01200f2742c763
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65235826"
---
# <a name="what-is-automated-machine-learning"></a>Informazioni sulle funzionalità automatizzate di Machine Learning

Apprendimento automatico, noto anche come AutoML, consente ai data Scientist, agli analisti e sviluppatori compilare modelli di Machine Learning con scalabilità elevata, efficienza e produttività durante tutte le qualità dei modelli di solo supporto. 

Machine Learning automatizzati compila un set di modelli di Machine Learning automaticamente, in modo intelligente la selezione modelli per il training e consiglia quello più adatto per l'utente. Lo sviluppo di modelli di apprendimento tradizionale è un numero di risorse che richiedono informazioni di dominio significativo e l'ora per produrre e confrontare decine di modelli. Con Machine Learning automatizzati, si sarà accelerare il tempo che necessario per ottenere modelli di Machine Learning pronti per la produzione con efficienza e semplicità eccezionali.

Dietro le quinte, dei dati di training vengano eseguiti con una funzione di destinazione definito e scorrere in modo intelligente le combinazioni di algoritmi di Machine Learning e selezione di funzionalità al momento. Quindi, base ai punteggi di training, il modello migliore adattato è identificato e consiglia all'utente. 

Sarà tuttavia ancora possibile controllare l'esperimento e trasparenza a ciò che accade. È possibile definire i vincoli e sperimentare basati sul tempo, la precisione o il numero di iterazioni, ad esempio gli obiettivi. È possibile visualizzare ogni modello che è stato generato per l'esperimento, il flusso di training per ogni iterazione e le funzionalità più influente per un determinato modello.

## <a name="how-automated-ml-works"></a>Funziona come automatizzati di Machine Learning

Usando **servizio di Azure Machine Learning**, è possibile progettare ed eseguire gli esperimenti di training di Machine Learning automatizzati con questi passaggi:

1. **Identificare il problema di Machine Learning** da risolvere: classificazione, le previsioni o regressione
   
1. **Specificare l'origine e il formato dei dati di training con etichette**: Le matrici Numpy o dataframe di Pandas

1. **Configurare la destinazione di calcolo per il training del modello**, ad esempio il [computer locale, Azure Machine Learning Calcola, le macchine virtuali remote o Azure Databricks](how-to-set-up-training-targets.md).  Informazioni su formazione automatizzato [su una risorsa remota](how-to-auto-train-remote.md).

1. **Configurare l'automatizzati di machine learning parametri** che determinano il numero di iterazioni su diversi modelli, le impostazioni degli iperparametri, advanced pre-elaborazione/definizione delle funzionalità e le metriche da esaminare durante la determinazione del modello migliore.  È possibile configurare le impostazioni per l'esperimento di training automatici [nel portale di Azure](how-to-create-portal-experiments.md) oppure [con il SDK](how-to-configure-auto-train.md).

1. **Inviare l'esecuzione di training.** 


[![Apprendimento automatico](./media/how-to-automated-ml/automated-machine-learning.png)](./media/how-to-automated-ml/automated-machine-learning.png#lightbox)

Durante il training, il servizio Azure Machine Learning crea numerose pipeline che tentano di usare i diversi algoritmi e parametri. Viene arrestata quando raggiunge i criteri di uscita definiti nell'esperimento. 

È anche possibile esaminare le informazioni di esecuzione registrate, contenenti metriche raccolte durante l'esecuzione. L'esecuzione di training produce un oggetto serializzato di Python (`.pkl` file) che contiene il modello e dati pre-elaborazione.


Durante la compilazione del modello è automatizzata, è anche possibile [informazioni su come importanti o utili funzionalità sono](how-to-configure-auto-train.md#explain) ai modelli generati. 

> [!VIDEO https://www.youtube.com/embed/l8c-4iDPE0M]

<a name="preprocess"></a>

## <a name="preprocessing"></a>Pre-elaborazione

In ogni automatizzato esperimento di machine learning, i dati viene pre-elaborati utilizzando i metodi predefiniti e, facoltativamente, tramite avanzate pre-elaborazione.

### <a name="automatic-preprocessing-standard"></a>Automatic pre-elaborazione (standard)
In ogni automatizzato esperimento di machine learning, i dati viene automaticamente ridimensionati o normalizzati offrono prestazioni adeguate per gli algoritmi.  Durante il training del modello, una delle seguenti tecniche scalano o normalizzano autonomamente verrà applicata a ogni modello.

|Ridimensionamento&nbsp;&&nbsp;normalizzazione| Descrizione |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Standardizzare funzionalità rimuovendo la media e ridimensionamento in base alla varianza unitaria  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Trasforma le funzionalità da ogni funzionalità per tale colonna minimo e massimo di ridimensionamento  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |    Ridimensionare ogni funzionalità mediante il valore assoluto massimo |  
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |   Questa funzionalità di Scaler dal relativo intervallo quantile |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) | Riduzione della dimensionalità lineare utilizzando scomposizione di valori singolari dei dati, proiettare su uno spazio dimensionale inferiore | 
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |    Questa trasformazione consente di eseguire la riduzione della dimensionalità lineare mezzo di scomposizione di valori singolari troncati (SVD). Contrariamente a PCA, questo strumento di stima non allineare al centro dati prima di calcolare la scomposizione di valori singolari. Ciò significa che può funzionare in modo efficiente con matrici scipy.sparse | 
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Ogni esempio (vale a dire, ogni riga della matrice dei dati) con almeno un componente diverso da zero viene ridimensionata indipendentemente da altri esempi in modo che sua norma (l1 o l2) è uguale a uno | 

### <a name="advanced-preprocessing-optional-featurization"></a>Advanced pre-elaborazione: definizione delle funzionalità facoltative

Avanzate pre-elaborazione aggiuntiva e definizione delle funzionalità sono anche disponibili, ad esempio mancanti imputation valori, la codifica e le trasformazioni. [Altre informazioni su quali definizione delle funzionalità è inclusa](how-to-create-portal-experiments.md#preprocess). Abilitare questa impostazione con:
+ Portale di Azure: Selezionando il **pre-elaborazione** casella di controllo la **impostazioni avanzate** [con questi passaggi](how-to-create-portal-experiments.md). 
+ Python SDK: Che specifica `"preprocess": True` per il [ `AutoMLConfig` classe](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).

## <a name="ensemble-models"></a>Modelli di insieme

È possibile eseguire il training di modelli di insieme usando automatizzati di machine learning con il [algoritmo di selezione Caruana insieme durante l'inizializzazione dell'insieme ordinato di](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf). Apprendimento dell'insieme migliora i risultati di machine learning e le prestazioni predittive combinando molti modelli rispetto all'utilizzo di modelli singoli. Come ultima iterazione di eseguire l'iterazione dell'insieme.

## <a name="use-with-onnx-in-c-apps"></a>Uso con ONNX in C# App

Con Azure Machine Learning, è possibile usare ML automatizzati per compilare un modello Python e averlo convertito nel formato ONNX. Il runtime ONNX supporta C#, è possibile utilizzare il modello compilato automaticamente in di C# le app senza richiedere alcuna ricodifica o uno qualsiasi delle latenze di rete che presentano endpoint REST. Provare a eseguire un esempio di questo flusso [in questo notebook di Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-with-onnx/auto-ml-classification-with-onnx.ipynb).

## <a name="automated-ml-across-microsoft"></a>Machine Learning automatizzati in Microsoft

Automatizzati di Machine Learning è disponibile anche in altre soluzioni Microsoft, ad esempio:
+ Nelle app .NET con Visual Studio e Visual Studio Code con [a ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview)
+ [In HDInsight](../../hdinsight/spark/apache-spark-run-machine-learning-automl.md), in cui la scalabilità orizzontale ML training automatizzate su Spark nei cluster HDInsight in parallelo. 
+ [In Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-automated)

## <a name="next-steps"></a>Passaggi successivi

Vedere esempi e informazioni su come creare modelli tramite le funzionalità automatizzate di Machine Learning:

+ Seguire il [esercitazione: Eseguire il training automatico di un modello di classificazione con Azure Automated Machine Learning](tutorial-auto-train-models.md)

+ Configurare le impostazioni per l'esperimento di training automatico: 
   + Nell'interfaccia del portale Azure [seguire questa procedura](how-to-create-portal-experiments.md).
   + Con Python SDK [seguire questa procedura](how-to-configure-auto-train.md).
  
 + Informazioni su come auto usando i dati delle serie temporali, train [seguire questa procedura](how-to-auto-train-forecast.md).

+ Provare [esempi di Notebook di Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)
