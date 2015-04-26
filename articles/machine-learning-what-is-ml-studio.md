<properties 
	pageTitle="Cos'è Azure Machine Learning Studio? | Azure" 
	description="Panoramica di Azure Machine Learning Studio e dei relativi componenti di base." 
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
	ms.date="09/11/2014" 
	ms.author="garye"/>

# Cos'è Azure Machine Learning Studio?

Microsoft Azure Machine Learning Studio è un ambiente di sviluppo visivo di collaborazione che consente di compilare, testare e distribuire soluzioni analitiche predittive applicate ai dati. Il servizio e l'ambiente di sviluppo di Machine Learning è basato sul cloud, offre risorse di calcolo e flessibilità di memoria ed elimina i problemi di configurazione e installazione perché l'utente lavora tramite il Web browser. 

ML Studio è il punto di incontro di scienza dei dati, analisi predittive, risorse cloud e dati.

## Spazio di lavoro interattivo di ML Studio

Per sviluppare un modello di analisi predittiva, in genere si usano dati provenienti da una o più origini, che vengono trasformati e analizzati tramite varie funzioni di modifica dei dati e statistiche e da cui viene generato un set di risultati. Lo sviluppo di un modello come questo è un processo iterativo: man mano che si modificano le varie funzioni e i relativi parametri, i risultati convergono fino a quando l'utente non è soddisfatto del modello sottoposto a training.

**ML Studio** offre uno spazio di lavoro visivo e interattivo per compilare facilmente, testare ed eseguire l'iterazione di un modello di analisi predittiva. È possibile trascinare la selezione di ***set di dati*** e ***moduli*** di analisi in un'***area di disegno***, collegandoli tra loro per ottenere un ***esperimento*** da inviare a ML Studio per l'***esecuzione***. Per eseguire l'iterazione del progetto di modulo, si ***modifica*** l'esperimento, si ***salva*** una copia, se lo si desidera, e lo si invia di nuovo. Quando si è pronti, è possibile ***pubblicare*** l'esperimento come ***servizio Web***, in modo da consentire l'accesso al modello da parte di altri utenti. 

Non sono necessarie operazioni di programmazione, è sufficiente collegare visivamente i set di dati e i moduli per costruire un modello di analisi predittiva.

![ML Studio Overview][ml-studio-overview]

## Introduzione a ML Studio

Quando si apre ML Studio per la prima volta, a sinistra sono presenti le schede seguenti:

- **Home page di Studio**: set di collegamenti a documentazione e altre risorse.
- **ESPERIMENTI**: esperimenti creati, eseguiti e salvati come bozze. 
- **SERVIZI WEB**: elenco di esperimenti pubblicati. 
- **IMPOSTAZIONI**: raccolta di impostazioni che è possibile usare per configurare l'account e le risorse. 

>[WACOM.NOTE] Quando si costruisce un esperimento, viene visualizzato un elenco di lavoro dei set di dati e moduli disponibili	a sinistra dell'area di disegno. È l'elenco dei componenti usati per compilare il modello.

## Componenti di un esperimento

Un esperimento è costituito da set di dati che forniscono i dati ai moduli di analisi, che sono collegati tra loro per costruire un modello di analisi predittiva.. In particolare, un esperimento valido ha le caratteristiche seguenti:

- Ha almeno un set di dati e un modulo. 
- I set di dati possono essere collegati solo ai moduli. 
- I moduli possono essere collegati a set di dati o ad altri moduli. 
- Tutte le porte di input per i moduli devono avere un collegamento al flusso di dati. 
- Tutti i parametri necessari per un modulo devono essere impostati. 

Per un esempio di creazione di un esperimento semplice, vedere [Creare un semplice esperimento in Azure Machine Learning Studio](http://azure.microsoft.com/documentation/articles/machine-learning-create-experiment/). 
Per una procedura dettagliata più completa della creazione di una soluzione di analisi predittiva, vedere [Sviluppare una soluzione predittiva con Azure Machine Learning](http://azure.microsoft.com/documentation/articles/machine-learning-walkthrough-develop-predictive-solution/).

### Set di dati

Un set di dati include dati caricati in ML Studio per poter essere usati nel processo di modellazione.  In ML Studio sono inclusi alcuni set di dati di esempio per provare a usare il programma. È possibile caricare altri set di dati in base alle esigenze.  Ecco alcuni esempi di set di dati inclusi:

- **Dati relativi al consumo di carburante per diverse automobili**: valori di consumo del carburante per le automobili identificate per numero di cilindri, potenza e così via. 
- **Dati relativi al tumore al seno**: dati relativi alla diagnosi del tumore al seno: 
- **Dati relativi agli incendi nei boschi**: dimensioni degli incendi boschivi nel nord-est del Portogallo. 

Quando si compila un esperimento, l'elenco di lavoro dei set di dati è disponibile a sinistra dell'area di disegno. 

### Moduli

Un modulo è un algoritmo che è possibile applicare ai dati.  ML Studio include diversi moduli, da funzioni di inserimento dei dati a processi di training, valutazione e convalida.  Ecco alcuni esempi di moduli inclusi:

- **Conversione in formato ARFF**: converte un set di dati serializzato .NET in formato ARFF. 
- **Statistiche elementari**: calcola le statistiche elementari come la media, la deviazione standard e così via. 
- **Regressione lineare**: crea un modello di regressione lineare online basato su valori descent con sfumatura. 
- **Modello di punteggio**: assegna un punteggio a un modello di classificazione sottoposto a training o di regressione. 

Quando si compila un esperimento, l'elenco di lavoro dei moduli è disponibile a sinistra dell'area di disegno. 

Un modulo può avere un set di parametri che è possibile usare per configurare gli algoritmi interni del modulo. Quando si seleziona un modulo nell'area di disegno,  i parametri del modulo sono visualizzati nel riquadro a destra dell'area di disegno. È possibile modificare i parametri in questo riquadro per ottimizzare il modello.


[ml-studio-overview]:./media/machine-learning-what-is-ml-studio/context.jpg

<!--HONumber=46--> 

<!--HONumber=46--> 
