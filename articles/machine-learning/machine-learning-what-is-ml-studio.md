<properties 
	pageTitle="Cos'è Azure Machine Learning Studio? | Microsoft Azure"
	description="Panoramica di Azure ML Studio, uno strumento di trascinamento per la creazione rapida di modelli da una libreria di algoritmi e moduli pronta per l'uso."
	keywords="azure machine learning,azure ml, ml studio"
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="09/09/2016"
	ms.author="garye"/>

# Informazioni su Azure Machine Learning Studio

Microsoft Azure Machine Learning Studio è uno strumento di trascinamento collaborativo che consente di compilare, testare e distribuire soluzioni di analisi predittiva ai dati. Machine Learning Studio pubblica i modelli come servizi Web che possono essere facilmente usati da applicazioni personalizzate o strumenti di Business Intelligence, ad esempio Excel.

Machine Learning Studio è il punto di incontro di scienza dei dati, analisi predittive, risorse cloud e dati.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Area di lavoro interattiva di Machine Learning Studio

Per sviluppare un modello di analisi predittiva, in genere si usano dati provenienti da una o più origini, che vengono trasformati e analizzati tramite varie funzioni di modifica dei dati e statistiche e da cui viene generato un set di risultati. Lo sviluppo di un modello come questo è un processo iterativo. Man mano che si modificano le varie funzioni e i relativi parametri, i risultati convergono fino a quando l'utente non è soddisfatto del modello sottoposto a training.

**Azure Machine Learning Studio** offre un'area di lavoro visiva e interattiva per eseguire facilmente le operazioni di compilazione, test e iterazione di un modello di analisi predittiva. È possibile trascinare la selezione di ***set di dati*** e ***moduli*** di analisi in un'area di disegno interattiva, collegandoli tra loro per ottenere un ***esperimento*** da eseguire in Machine Learning Studio. Per eseguire l'iterazione della progettazione del modello, modificare l'esperimento, salvare eventualmente una copia e ripeterne l'esecuzione. Quando si è pronti, è possibile convertire l'***esperimento di training*** in un ***esperimento predittivo*** e quindi pubblicarlo come un ***servizio Web*** in modo che altri utenti possano accedere al modello.

>[AZURE.TIP] Per scaricare e stampate un diagramma che offra una panoramica delle funzionalità di Machine Learning Studio, vedere [Diagramma della panoramica delle funzionalità di Azure Machine Learning Studio](machine-learning-studio-overview-diagram.md).

Non sono necessarie operazioni di programmazione, è sufficiente collegare visivamente i set di dati e i moduli per costruire un modello di analisi predittiva.

![Diagramma di Azure ML Studio: Creare esperimenti, leggere dati per molte origini, scrivere dati con punteggio, scrivere modelli.][ml-studio-overview]

## Introduzione a Machine Learning Studio

Quando si apre [Machine Learning Studio](https://studio.azureml.net) per la prima volta, viene visualizzata la pagina **Home**. Da qui è possibile visualizzare documentazione, video e webinar, nonché trovare altre risorse utili.

Nella parte superiore sono disponibili tre schede: **Home** (scheda iniziale), **Studio**, e **Gallery**.

### Studio

Fare clic sulla scheda **Studio**. Verrà richiesto di effettuare l'accesso usando il proprio account Microsoft oppure l'account aziendale o dell'istituto di istruzione. Una volta effettuato l'accesso, a sinistra verranno visualizzate le schede seguenti:

- **PROJECTS** (PROGETTI): raccolte di esperimenti, set di dati, blocchi appunti e altre risorse che rappresentano un solo progetto.
- **EXPERIMENTS** - esperimenti creati, eseguiti e salvati come bozze.
- **WEB SERVICES**: servizi Web distribuiti tramite gli esperimenti.
- **NOTEBOOKS** - notebook Jupyter creati.
- **DATASETS** - set di dati caricati in Studio.
- **TRAINED MODELS** - modelli sottoposti a training durante gli esperimenti e salvati in Studio.
- **SETTINGS**: insieme di impostazioni che è possibile usare per configurare l'account e le risorse.

### Raccolta

Fare clic sulla scheda **Gallery**. Verrà visualizzata Cortana Intelligence Gallery. La raccolta è un'area in cui una community di sviluppatori e data scientist condivide le soluzioni create usando i componenti di Cortana Intelligence Suite.

Per altre informazioni sulla raccolta, vedere [Condividere e trovare soluzioni in Cortana Intelligence Gallery](machine-learning-gallery-how-to-use-contribute-publish.md).

## Componenti di un esperimento

Un esperimento è costituito da set di dati che forniscono i dati a moduli di analisi, che sono collegati tra loro per costruire un modello di analisi predittiva. In particolare, un esperimento valido ha le caratteristiche seguenti:

- Ha almeno un set di dati e un modulo.
- I set di dati possono essere collegati solo ai moduli.
- I moduli possono essere collegati a set di dati o ad altri moduli.
- Tutte le porte di input per i moduli devono avere un collegamento al flusso di dati.
- Tutti i parametri necessari per ogni modulo devono essere impostati.

È possibile creare un esperimento da zero oppure è possibile usare un esperimento di esempio esistente come modello. Per altre informazioni, vedere [Usare gli esperimenti di esempio per creare nuovi esperimenti](machine-learning-sample-experiments.md).

Per un esempio di creazione di un esperimento semplice, vedere [Creare un semplice esperimento in Azure Machine Learning Studio](machine-learning-create-experiment.md).

Per una procedura dettagliata più completa della creazione di una soluzione di analisi predittiva, vedere [Sviluppare una soluzione predittiva con Azure Machine Learning](machine-learning-walkthrough-develop-predictive-solution.md).

### Set di dati

Un set di dati include dati caricati in Machine Learning Studio per essere usati nel processo di modellazione. In Machine Learning Studio sono inclusi alcuni set di dati di esempio per provare a usare il programma. È possibile caricare altri set di dati in base alle esigenze. Ecco alcuni esempi di set di dati inclusi:

- **MPG data for various automobiles**: valori di consumo del carburante per le automobili identificate in base a numero di cilindri, potenza e così via.
- **Breast cancer data**: dati relativi alla diagnosi del tumore al seno.
- **Forest fires data**: dimensioni degli incendi boschivi nel nord-est del Portogallo.

Quando si compila un esperimento, è possibile effettuare una selezione nell'elenco dei set di dati disponibile a sinistra dell'area di disegno.

Per un elenco dei set di dati di esempio inclusi in Machine Learning Studio, vedere [Usare i set di dati di esempio in Azure Machine Learning Studio](machine-learning-use-sample-datasets.md).

### Moduli

Un modulo è un algoritmo che è possibile applicare ai dati. Machine Learning Studio include diversi moduli, da funzioni di inserimento dei dati a processi di training, valutazione e convalida. Ecco alcuni esempi di moduli inclusi:

- [Convert to ARFF][convert-to-arff]\: converte un set di dati serializzato .NET nel formato ARFF (Attribute-Relation File Format).
- [Compute Elementary Statistics][elementary-statistics]\: calcola le statistiche elementari come media, deviazione standard e così via.
- [Linear Regression][linear-regression]\: crea un modello di regressione lineare online basato su valori descent con sfumatura.
- [Score Model][score-model]\: assegna un punteggio a un modello di classificazione sottoposto a training o di regressione.

Quando si compila un esperimento, è possibile effettuare una selezione nell'elenco dei moduli disponibile a sinistra dell'area di disegno.

Un modulo può avere un set di parametri che è possibile usare per configurare gli algoritmi interni del modulo. Quando si seleziona un modulo nell'area di disegno, i parametri del modulo sono visualizzati nel riquadro **Properties** a destra dell'area di disegno. È possibile modificare i parametri in questo riquadro per ottimizzare il modello.

Per altre informazioni di riferimento sull'ampia raccolta di algoritmi di Machine Learning disponibili, vedere [Come scegliere gli algoritmi per Microsoft Azure Machine Learning](machine-learning-algorithm-choice.md).

## Distribuzione di un servizio Web di analisi predittiva

Quando il modello di analisi predittiva è pronto, è possibile distribuirlo come servizio Web direttamente da Machine Learning Studio. Per altre informazioni su questo processo, vedere [Distribuire un servizio Web di Azure Machine Learning](machine-learning-publish-a-machine-learning-web-service.md).

[ml-studio-overview]: ./media/machine-learning-what-is-ml-studio/azure-ml-studio-diagram.jpg

<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/

<!---HONumber=AcomDC_0914_2016-->