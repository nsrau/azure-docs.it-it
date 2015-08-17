<properties 
	pageTitle="Esempi di servizi Web Machine Learning compilati con R | Microsoft Azure" 
	description="Trovare un insieme utile di esempi di servizi Web creati con codice R e Machine Learning e pubblicati in Azure Marketplace." 
	keywords="csharp,r code,web services examples"
	services="machine-learning" 
	documentationCenter="" 
	authors="jaymathe" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/24/2015" 
	ms.author="jaymathe"/>


#Esempi di servizi Web che utilizzano codice R in Azure Machine Learning e pubblicati in Microsoft Azure Marketplace

Questo articolo contiene servizi Web di esempio che sono stati creati utilizzando Azure Machine Learning e che sono stati pubblicati in Azure Marketplace. A ogni servizio Web è associata una documentazione completa, che include set di dati di esempio per il test dei servizi e una spiegazione della modalità in cui un utente può creare un servizio analogo da solo.

Azure Machine Learning Studio permette agli utenti di scrivere codice R e, selezionando alcuni pulsanti, di pubblicare tale codice come servizio Web, affinché possa essere usato da altri utenti e dispositivi in tutto il mondo.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


Dalla produzione di semplici calcolatrici, che forniscono funzionalità statistiche, alla creazione di predittori personalizzati per l'analisi dei sentimenti per il data mining dei testi, ad esempio, i nuovi utenti e gli utenti esperti di R possono usufruire della semplicità con cui gli utenti di Azure Machine Learning possono rendere operativo il codice R. Benché questi servizi Web possano essere usati dagli utenti, potenzialmente tramite un'app mobile o un sito Web, la loro finalità consiste anche nel fungere da esempio dell'uso di Machine Learning per rendere operativi script R per fini analitici e per la creazione di servizi Web basati sul codice R.

Ogni esempio include codice C# per l'utilizzo del servizio Web.


![Diagramma del codice R in Azure Machine Learning: soluzioni R per l’uso proprietario o pubblicate in Azure Marketplace.][1]

Esaminare gli scenari seguenti:

##Scenario 1: Modello generico 
Un utente usa un modello generico, applicabile a nuovi dati dell'utente, ad esempio una previsione di base dei dati di una serie temporale o un metodo R personalizzato con funzionalità di analisi avanzate. L'utente pubblica il modello come servizio Web, in modo che altri utenti possano usarlo con i propri dati.



* [Binary Classifier](machine-learning-r-csharp-binary-classifier.md)
* [Modello di cluster](machine-learning-r-csharp-cluster-model.md)
* [Multivariate Linear Regression](machine-learning-r-csharp-multivariate-linear-regression.md)
* [Forecasting-Exponential Smoothing](machine-learning-r-csharp-forecasting-exponential-smoothing.md)
* [Previsioni - ETS + STL](machine-learning-r-csharp-retail-demand-forecasting.md)
* [Previsioni - Modello autoregressivo integrato a media mobile (ARIMA)](machine-learning-r-csharp-arima.md)
* [Analisi di sopravvivenza](machine-learning-r-csharp-survival-analysis.md)


##Scenario 2 : Modello di training - Dati specifici 
Un utente ha a disposizione dati che forniscono previsioni utili tramite il codice R, ad esempio un ampio campione di questionari relativi alla personalità raggruppati in cluster tramite un algoritmo k-means per prevedere il tipo di personalità dell'utente oppure i dati relativi a un sondaggio sulla salute che possono essere usati per prevedere il rischio di un individuo a livello di cancro ai polmoni tramite un pacchetto R dell'analisi di sopravvivenza. L'utente pubblica i dati tramite un servizio Web che prevede l'esito relativo a un nuovo utente.

##Scenario 3 Modello di ttraining - Dati generici 
Un utente ha a disposizione dati generici, ad esempio un corpus di testo, che permettono la creazione di un servizio Web che è possibile creare e applicare in modo generico a diversi tipi di casi e scenari.

* [Analisi dei sentimenti basata sul lessico](machine-learning-r-csharp-lexicon-based-sentiment-analysis.md)

##Scenario 4: Calcolatrice avanzata 
Un utente fornisce calcoli o simulazioni avanzate, che non richiedono alcun modello di training o alcun adattamento di modelli ai dati dell'utente.

* [Difference in Proportions Test](machine-learning-r-csharp-difference-in-two-proportions.md)
* [Normal Distribution Suite](machine-learning-r-csharp-normal-distribution.md)
* [Binomial Distribution Suite](machine-learning-r-csharp-binomial-distribution.md)

##Domande frequenti
Per le domande frequenti relative all'uso del servizio Web o alla pubblicazione nel Marketplace, vedere [qui](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-web-service-examples/machine-learning-r-code-options-for-using-and-sharing-cloud.png


 

<!---HONumber=August15_HO6-->