<properties title="Sample Web Services Built with R on Azure ML and Published to Marketplace" pageTitle="Servizi Web di esempio creati con R in Azure ML e pubblicati nel Marketplace | Azure" description="Sample Web Services Built with R on Azure ML and Published to Marketplace" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="jaymathe" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/13/2014" ms.author="jaymathe" /> 


#Servizi Web di esempio creati con R in Azure ML e pubblicati nel Marketplace




Azure Machine Learning Studio permette agli utenti di scrivere codice R e, selezionando alcuni pulsanti, di pubblicare il codice come servizio Web, in modo che possa essere usato da altri utenti e dispositivi in tutto il mondo. Dalla produzione di semplici calcolatrici che forniscono funzionalità statistiche alla creazione di predittori personalizzati per l'analisi del sentiment per il data mining dei testi, ad esempio, i nuovi utenti e gli utenti esperti di R possono usufruire della semplicità con cui gli utenti di Azure ML possono rendere operativo il codice R. Benché questi servizi Web possano essere usati dagli utenti, potenzialmente tramite un'app mobile o un sito Web, la finalità di questi servizi Web consiste anche nel fungere da esempio dell'uso di Azure ML per rendere operativi script R per fini analitici e per la creazione di servizi Web basati sul codice R.

In questa pagina è disponibile un ampio set di servizi Web di esempio creati tramite Azure ML e pubblicati in Microsoft Azure Marketplace. Ogni servizio Web è associato a documentazione completa, che include set di dati di esempio per il test dei servizi e che illustra in che modo gli utenti possono creare un servizio analogo. 

![Workflow][1]

Esaminare gli scenari seguenti:

####Scenario 1: Modello generico 
Un utente usa un modello generico, applicabile a nuovi dati dell'utente, ad esempio una previsione di base dei dati di una serie temporale o un metodo R personalizzato con funzionalità di analisi avanzate. L'utente pubblica il modello come servizio Web, in modo che altri utenti possano usarlo con i propri dati.

* [Binary Classifier](http://azure.microsoft.com/it-it/documentation/articles/machine-learning-r-csharp-binary-classifier/)
* [Modello di cluster](http://azure.microsoft.com/it-it/documentation/articles/machine-learning-r-csharp-cluster-model/)
* [Multivariate Linear Regression](http://azure.microsoft.com/it-it/documentation/articles/machine-learning-r-csharp-multivariate-linear-regression/)
* [Forecasting-Exponential Smoothing](http://azure.microsoft.com/it-it/documentation/articles/machine-learning-r-csharp-forecasting-exponential-smoothing/)
* [Previsioni - ETS + STL](http://azure.microsoft.com/it-it/documentation/articles/machine-learning-r-csharp-retail-demand-forecasting/)
* [Previsioni - Modello autoregressivo integrato a media mobile (ARIMA)](http://azure.microsoft.com/it-it/documentation/articles/machine-learning-r-csharp-arima/)
* [Analisi di sopravvivenza](http://azure.microsoft.com/it-it/documentation/articles/machine-learning-r-csharp-survival-analysis/)

####Scenario 2: Modello sottoposto a training - Dati specifici 
Un utente ha a disposizione dati che forniscono previsioni utili tramite il codice R, ad esempio un campione ampio di questionari relativi alla personalità raggruppati in cluster tramite un algoritmo k-means per prevedere il tipo di personalità dell'utente oppure i dati relativi a un sondaggio sulla salute che possono essere usati per prevedere il rischio di un individuo a livello di cancro ai polmoni tramite un pacchetto R dell'analisi di sopravvivenza. L'utente pubblica i dati tramite un servizio Web che prevede l'esito relativo a un nuovo utente.

####Scenario 3: Modello sottoposto a training - Dati generici 
Un utente ha a disposizione dati generici, ad esempio un corpus di testo, che permettono la creazione di un servizio Web applicabile in modo generico a diversi tipi di cause e di scenari.

* [Analisi del sentiment basata sul lessico](http://azure.microsoft.com/it-it/documentation/articles/machine-learning-r-csharp-lexicon-based-sentiment-analysis/)

####Scenario 4: Calcolatrice avanzata 
Un utente fornisce calcoli o simulazioni avanzate, che non richiedono alcun modello sottoposto a training o alcun adattamento di modelli ai dati dell'utente.

* [Difference in Proportions Test](http://azure.microsoft.com/it-it/documentation/articles/machine-learning-r-csharp-difference-in-two-proportions/)
* [Normal Distribution Suite](http://azure.microsoft.com/it-it/documentation/articles/machine-learning-r-csharp-normal-distribution/)
* [Binomial Distribution Suite](http://azure.microsoft.com/it-it/documentation/articles/machine-learning-r-csharp-binomial-distribution/)

##Domande frequenti
Per le domande frequenti relative all'uso del servizio Web o alla pubblicazione nel Marketplace, vedere [qui](http://azure.microsoft.com/it-it/documentation/articles/machine-learning-marketplace-faq).

[1]: ./media/machine-learning-r-csharp-web-service-examples/base1.png


