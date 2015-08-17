<properties 
	pageTitle="Cos'è Azure Machine Learning Studio? | Microsoft Azure" 
	description="Panoramica di Azure ML Studio, uno strumento di trascinamento per la creazione rapida di modelli da una libreria di algoritmi e moduli pronta per l'uso." 
	services="machine-learning" 
	documentationCenter="" 
	authors="garyericson" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/10/2015" 
	ms.author="garye"/>

# Informazioni su Azure Machine Learning Studio

Microsoft Azure Machine Learning Studio è uno strumento di trascinamento collaborativo che consente di compilare, testare e distribuire soluzioni di analisi predittiva ai dati. Machine Learning Studio pubblica i modelli come servizi Web che possono essere facilmente usati da applicazioni personalizzate o strumenti di Business Intelligence, ad esempio Excel.

Machine Learning Studio (talvolta chiamato "Azure ML Studio") è il punto di incontro di scienza dei dati, analisi predittive, risorse cloud e dati.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Area di lavoro interattiva di Machine Learning Studio

Per sviluppare un modello di analisi predittiva, in genere si usano dati provenienti da una o più origini, che vengono trasformati e analizzati tramite varie funzioni di modifica dei dati e statistiche e da cui viene generato un set di risultati. Lo sviluppo di un modello come questo è un processo iterativo. Man mano che si modificano le varie funzioni e i relativi parametri, i risultati convergono fino a quando l'utente non è soddisfatto del modello sottoposto a training.

**Azure Machine Learning Studio** offre un'area di lavoro visiva e interattiva per eseguire facilmente le operazioni di compilazione, test e iterazione di un modello di analisi predittiva. È possibile trascinare la selezione di ***set di dati*** e ***moduli*** di analisi in un'***area di disegno*** interattiva, collegandoli tra loro per ottenere un ***esperimento*** da ***eseguire*** in Machine Learning Studio. Per eseguire l'iterazione della progettazione del modello, ***modificare*** l'esperimento, ***salvare*** una copia, se lo si desidera, e ripeterne l'esecuzione. Quando si è pronti, è possibile ***pubblicare*** l'esperimento come ***servizio Web***, in modo da consentire ad altri utenti di accedere al modello.

Non sono necessarie operazioni di programmazione, è sufficiente collegare visivamente i set di dati e i moduli per costruire un modello di analisi predittiva.

![Diagramma di Azure ML Studio: Creare esperimenti, leggere dati per molte origini, scrivere dati con punteggio, scrivere modelli.][ml-studio-overview]

## Introduzione a Machine Learning Studio

Quando si apre Machine Learning Studio per la prima volta, a sinistra sono presenti le schede seguenti:

- **Studio Home**: set di collegamenti alla documentazione e ad altre risorse.
- **EXPERIMENTS**: esperimenti creati, eseguiti e salvati come bozze. 
- **WEB SERVICES**: elenco di esperimenti pubblicati. 
- **SETTINGS**: insieme di impostazioni che è possibile usare per configurare l'account e le risorse. 

>[AZURE.NOTE]Quando si costruisce un esperimento, a sinistra dell'area di disegno viene visualizzato un elenco di lavoro dei set di dati e dei moduli disponibili. È l'elenco dei componenti usati per compilare il modello.

## Componenti di un esperimento

Un esperimento è costituito da set di dati che forniscono i dati a moduli di analisi, che sono collegati tra loro per costruire un modello di analisi predittiva. In particolare, un esperimento valido ha le caratteristiche seguenti:

- Ha almeno un set di dati e un modulo. 
- I set di dati possono essere collegati solo ai moduli. 
- I moduli possono essere collegati a set di dati o ad altri moduli. 
- Tutte le porte di input per i moduli devono avere un collegamento al flusso di dati. 
- Tutti i parametri necessari per un modulo devono essere impostati. 

Per un esempio di creazione di un esperimento semplice, vedere [Creare un semplice esperimento in Azure Machine Learning Studio](machine-learning-create-experiment.md). Per una procedura dettagliata più completa della creazione di una soluzione di analisi predittiva, vedere [Sviluppare una soluzione predittiva con Azure Machine Learning](machine-learning-walkthrough-develop-predictive-solution.md).

### Set di dati

Un set di dati include dati caricati in Machine Learning Studio per essere usati nel processo di modellazione. In Machine Learning Studio sono inclusi alcuni set di dati di esempio per provare a usare il programma. È possibile caricare altri set di dati in base alle esigenze. Ecco alcuni esempi di set di dati inclusi:

- **MPG data for various automobiles**: valori di consumo del carburante per le automobili identificate in base a numero di cilindri, potenza e così via. 
- **Breast cancer data**: dati relativi alla diagnosi del tumore al seno. 
- **Forest fires data**: dimensioni degli incendi boschivi nel nord-est del Portogallo. 

Quando si compila un esperimento, l'elenco di lavoro dei set di dati è disponibile a sinistra dell'area di disegno.

### Moduli

Un modulo è un algoritmo che è possibile applicare ai dati. Machine Learning Studio include diversi moduli, da funzioni di inserimento dei dati a processi di training, valutazione e convalida. Ecco alcuni esempi di moduli inclusi:

- [Convert to ARFF][convert-to-arff]\: converte un set di dati serializzato .NET nel formato ARFF (Attribute-Relation File Format). 
- [Elementary Statistics][elementary-statistics]\: calcola le statistiche elementari come media, deviazione standard e così via. 
- [Linear Regression][linear-regression]\: crea un modello di regressione lineare online basato su valori descent con sfumatura. 
- [Score Model][score-model]\: assegna un punteggio a un modello di classificazione sottoposto a training o di regressione. 

Quando si compila un esperimento, l'elenco di lavoro dei moduli è disponibile a sinistra dell'area di disegno.

Un modulo può avere un set di parametri che è possibile usare per configurare gli algoritmi interni del modulo. Quando si seleziona un modulo nell'area di disegno, i parametri del modulo sono visualizzati nel riquadro a destra dell'area di disegno. È possibile modificare i parametri in questo riquadro per ottimizzare il modello.


[ml-studio-overview]: ./media/machine-learning-what-is-ml-studio/azure-ml-studio-diagram.jpg


<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
 

<!---HONumber=August15_HO6-->