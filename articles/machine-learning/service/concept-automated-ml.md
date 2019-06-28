---
title: What ' s automatizzati di Machine Learning / automl
titleSuffix: Azure Machine Learning service
description: Informazioni su come il servizio Azure Machine Learning è in grado di scegliere automaticamente un algoritmo e, a partire da questo, generare un modello per consentire all'utente di risparmiare tempo. Tale scelta viene effettuata usando i parametri e i criteri specificati per selezionare l'algoritmo migliore per il modello.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nacharya1
ms.author: nilesha
ms.date: 06/20/2019
ms.custom: seodec18
ms.openlocfilehash: b9fe8ff710cbfe7fbb4a4d8bd351028bb50efcb0
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/22/2019
ms.locfileid: "67331745"
---
# <a name="what-is-automated-machine-learning"></a>Informazioni sulle funzionalità automatizzate di Machine Learning

Apprendimento automatico, noto anche come autoML, è il processo di automazione delle attività che richiedeva molto tempo e iterativa di sviluppo del modello di apprendimento automatico. Consente ai data Scientist, agli analisti e sviluppatori compilare modelli di Machine Learning con scalabilità elevata, efficienza e produttività durante tutte le qualità dei modelli di solo supporto.

Lo sviluppo di modelli di apprendimento tradizionale è un numero di risorse, che richiedono informazioni di dominio significativo e l'ora per produrre e confrontare decine di modelli. Applicare ML automatizzato quando si vuole che Azure Machine Learning per addestrare e ottimizzare un modello usando la metrica di destinazione che si specifica. Il servizio scorre quindi gli algoritmi di Machine Learning abbinati con selezioni di funzionalità, dove ogni iterazione produce un modello con un punteggio di training. Maggiore sarà il punteggio, migliore sarà il modello viene considerato "adatta" i dati.

Con automatizzati di machine learning, si sarà accelerare il tempo che necessario per ottenere modelli di Machine Learning pronti per la produzione con efficienza e semplicità eccezionali.

## <a name="when-to-use-automated-ml"></a>Quando usare automatizzati di Machine Learning

Machine Learning automatizzati rivoluziona il processo di sviluppo del modello di apprendimento e offre ai propri utenti, indipendentemente dalla loro esperienza di analisi scientifica dei dati, per identificare una pipeline di end-to-end machine learning per qualsiasi problema.

I data Scientist, agli analisti e sviluppatori per settori diversi possono usare ML automatizzati per:

+ Implementare soluzioni di machine learning senza una profonda conoscenza di programmazione
+ Risparmiare tempo e risorse
+ Usare le procedure consigliate analisi scientifica dei dati
+ Fornire agile risoluzione dei problemi

## <a name="how-automated-ml-works"></a>Funziona come automatizzati di Machine Learning

Usando **servizio di Azure Machine Learning**, è possibile progettare ed eseguire gli esperimenti di training di Machine Learning automatizzati con questi passaggi:

1. **Identificare il problema di Machine Learning** da risolvere: classificazione, le previsioni o regressione

1. **Specificare l'origine e il formato dei dati di training con etichette**: Le matrici Numpy o dataframe di Pandas

1. **Configurare la destinazione di calcolo per il training del modello**, ad esempio il [computer locale, Azure Machine Learning Calcola, le macchine virtuali remote o Azure Databricks](how-to-set-up-training-targets.md).  Informazioni su formazione automatizzato [su una risorsa remota](how-to-auto-train-remote.md).

1. **Configurare l'automatizzati di machine learning parametri** che determinano il numero di iterazioni su diversi modelli, le impostazioni degli iperparametri, advanced pre-elaborazione/definizione delle funzionalità e le metriche da esaminare durante la determinazione del modello migliore.  È possibile configurare le impostazioni per l'esperimento di training automatici [nel portale di Azure](how-to-create-portal-experiments.md) oppure [con il SDK](how-to-configure-auto-train.md).

1. **Inviare l'esecuzione di training.**

  ![Apprendimento automatico](./media/how-to-automated-ml/automl-concept-diagram2.png)

Durante il training, il servizio di Azure Machine Learning crea un numero di nelle pipeline parallele che prova diversi algoritmi e parametri. Viene arrestata quando raggiunge i criteri di uscita definiti nell'esperimento.

È anche possibile esaminare le informazioni registrate di esecuzione, quale [contiene metriche](how-to-understand-accuracy-metrics.md) raccolte durante l'esecuzione. L'esecuzione di training produce un oggetto serializzato di Python (`.pkl` file) che contiene il modello e dati pre-elaborazione.

Durante la compilazione del modello è automatizzata, è anche possibile [informazioni su come importanti o utili funzionalità sono](how-to-configure-auto-train.md#explain) ai modelli generati.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>Pre-elaborazione

In ogni automatizzato esperimento di machine learning, i dati viene pre-elaborati utilizzando i metodi predefiniti e, facoltativamente, tramite avanzate pre-elaborazione.

### <a name="automatic-preprocessing-standard"></a>Automatic pre-elaborazione (standard)

In ogni automatizzato esperimento di machine learning, i dati viene automaticamente ridimensionati o normalizzati offrono prestazioni adeguate per gli algoritmi.  Durante il training del modello, una delle seguenti tecniche scalano o normalizzano autonomamente verrà applicata a ogni modello.

|Ridimensionamento&nbsp;&&nbsp;normalizzazione| Descrizione |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Standardizzare funzionalità rimuovendo la media e ridimensionamento in base alla varianza unitaria  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Trasforma le funzionalità da ogni funzionalità per tale colonna minimo e massimo di ridimensionamento  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Ridimensionare ogni funzionalità mediante il valore assoluto massimo |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |Questa funzionalità di Scaler dal relativo intervallo quantile |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Riduzione della dimensionalità lineare utilizzando scomposizione di valori singolari dei dati, proiettare su uno spazio dimensionale inferiore |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Questa trasformazione consente di eseguire la riduzione della dimensionalità lineare mezzo di scomposizione di valori singolari troncati (SVD). Contrariamente a PCA, questo strumento di stima non allineare al centro dati prima di calcolare la scomposizione di valori singolari. Ciò significa che può funzionare in modo efficiente con matrici scipy.sparse |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Ciascun esempio (vale a dire, ogni riga della matrice dei dati) con almeno un componente diverso da zero è nuovamente in scala indipendentemente da altri esempi in modo che sua norma (l1 o l2) è uguale a uno |

### <a name="advanced-preprocessing-optional-featurization"></a>Advanced pre-elaborazione: definizione delle funzionalità facoltative

Avanzate pre-elaborazione aggiuntiva e definizione delle funzionalità sono anche disponibili, ad esempio mancanti imputation valori, la codifica e le trasformazioni. [Altre informazioni su quali definizione delle funzionalità è inclusa](how-to-create-portal-experiments.md#preprocess). Abilitare questa impostazione con:

+ Portale di Azure: Selezionando il **pre-elaborazione** casella di controllo la **impostazioni avanzate** [con questi passaggi](how-to-create-portal-experiments.md).

+ Python SDK: Che specifica `"preprocess": True` per il [ `AutoMLConfig` classe](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).


## <a name="time-series-forecasting"></a>Previsione di serie temporali
Creazione di previsioni è parte integrante di qualsiasi azienda, sia che si tratti di richiesta dei ricavi, inventario, vendite o dei clienti. È possibile usare Machine Learning automatizzati per combinare le tecniche e approcci e ottenere una consigliati e di alta qualità serie temporali di previsione. 

Un esperimento di serie temporali automatizzato viene considerato come un problema di regressione multivariata. Ultimi valori di serie temporali sono "trasformato tramite pivot" per diventare quote aggiuntive per il regressore insieme alle altre variabili predittive. Questo approccio, a differenza dei metodi di serie ora classiche, presenta un vantaggio di naturale incorporare più variabili contestuali e sulla loro relazione uno a altro durante il training. Automatizzati di Machine Learning crea un modello singolo, ma spesso internamente sottoposto a branching per tutti gli elementi in orizzonti il set di dati e la stima. In questo modo sono disponibili per stimare i parametri di modello più dati e generalizzazione di serie non visti diventa possibile. 

Altre informazioni e un esempio di [automatizzati di machine learning per serie storiche](how-to-auto-train-forecast.md).

## <a name="ensemble-models"></a>Modelli di insieme

È possibile eseguire il training di modelli di insieme usando automatizzati di machine learning con il [algoritmo di selezione Caruana insieme durante l'inizializzazione dell'insieme ordinato di](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf). Apprendimento dell'insieme migliora i risultati di machine learning e le prestazioni predittive combinando molti modelli rispetto all'utilizzo di modelli singoli. Come ultima iterazione di eseguire l'iterazione dell'insieme.

## <a name="use-with-onnx-in-c-apps"></a>Uso con ONNX in C# App

Con Azure Machine Learning, è possibile usare ML automatizzati per compilare un modello Python e averlo convertito nel formato ONNX. Il runtime ONNX supporta C#, è possibile utilizzare il modello compilato automaticamente in di C# le app senza richiedere alcuna ricodifica o uno qualsiasi delle latenze di rete che presentano endpoint REST. Provare a eseguire un esempio di questo flusso [in questo notebook di Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-with-onnx/auto-ml-classification-with-onnx.ipynb).

## <a name="automated-ml-across-microsoft"></a>Machine Learning automatizzati in Microsoft

Automatizzati di Machine Learning è disponibile anche in altre soluzioni Microsoft, ad esempio:

|Integrazioni|Descrizione|
|------------|-----------|
|[ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview)|Selezione automatica del modello e il training delle app .NET con Visual Studio e Visual Studio Code con a ML.NET automatizzati di Machine Learning (anteprima).|
|[HDInsight](../../hdinsight/spark/apache-spark-run-machine-learning-automl.md)|La scalabilità orizzontale ML training automatizzate su Spark nei cluster HDInsight in parallelo.|
|[PowerBI](https://docs.microsoft.com/power-bi/service-machine-learning-automated)|Richiamare i modelli di machine learning direttamente in Power BI (anteprima).|
|[SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)|Creare nuovi modelli di machine learning sui dati nei cluster di SQL Server 2019 dei big Data.|

## <a name="next-steps"></a>Passaggi successivi

Vedere gli esempi e informazioni su come creare modelli di uso dell'apprendimento automatico:

+ Seguire il [esercitazione: Eseguire il training automatico di un modello di classificazione con Azure Automated Machine Learning](tutorial-auto-train-models.md)

+ Configurare le impostazioni per l'esperimento di training automatico:
  + Nell'interfaccia del portale Azure [seguire questa procedura](how-to-create-portal-experiments.md).
  + Con Python SDK [seguire questa procedura](how-to-configure-auto-train.md).

+ Informazioni su come auto usando i dati delle serie temporali, train [seguire questa procedura](how-to-auto-train-forecast.md).

+ Provare [di esempi di Notebook di Jupyter per l'apprendimento automatico](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)
