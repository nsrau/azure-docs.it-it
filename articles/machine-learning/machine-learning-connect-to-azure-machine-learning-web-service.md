<properties
	pageTitle="Connessione a un servizio Web di Machine Learning | Microsoft Azure"
	description="Effettuare la connessione a un servizio Web di Azure Machine Learning con c# o Python utilizzando una chiave di autorizzazione."
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/09/2015" 
	ms.author="derrickv" />


# Connessione a un servizio Web di Azure Machine Learning
L'esperienza dello sviluppatore di Azure Machine Learning è un'API del servizio Web per eseguire stime dai dati di input in tempo reale o in modalità batch. È possibile usare Azure Machine Learning Studio per creare stime e distribuire un servizio Web di Azure Machine Learning.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Per informazioni su come creare e distribuire un servizio Web di Azure Machine Learning tramite Studio:

- [Distribuire un servizio Web di Machine Learning](machine-learning-publish-a-machine-learning-web-service.md)
- [Introduzione a ML Studio](http://azure.microsoft.com/documentation/videos/getting-started-with-ml-studio/)
- [Anteprima di Azure Machine Learning](https://studio.azureml.net/)
- [Centro di documentazione Machine Learning](http://azure.microsoft.com/documentation/services/machine-learning/)

## Servizio Web Azure Machine Learning ##

Con il servizio Web di Azure Machine Learning (ML), un'applicazione esterna comunica con un modello di valutazione del flusso di lavoro ML in tempo reale. Una chiamata al servizio Web ML restituisce i risultati della stima a un'applicazione esterna. Per effettuare una chiamata servizio Web ML, è possibile passare una chiave API creata quando si distribuisce una stima. Il servizio Web ML è basato su REST, una scelta di architettura diffusa per progetti di programmazione Web.

Azure Machine Learning dispone di due tipi di servizi:

- Servizio di richiesta-risposta (RRS). Un servizio a latenza bassa e altamente scalabile che offre un'interfaccia ai modelli senza stati creati e distribuiti da ML Studio.
- Servizio esecuzione batch (BES). Un servizio asincrono che valuta un batch di record di dati.

Per altre informazioni sui servizi Web di Azure Machine Learning, vedere [Distribuire un servizio Web Machine Learning](machine-learning-publish-a-machine-learning-web-service.md).

## Ottenere una chiave di autorizzazione Azure Machine Learning ##
È possibile ottenere una chiave API del servizio Web da un servizio Web ML. È possibile ottenerla da Microsoft Azure Machine Learning studio o dal portale di Azure classico.
### Microsoft Azure Machine Learning Studio ###
1. In Microsoft Azure Machine Learning Studio fare clic su **WEB SERVICES** a sinistra.
2. Fare clic su un servizio Web. La "chiave API" si trova nella scheda **DASHBOARD**.

### Portale di Azure classico ###

1. Fare clic su **MACHINE LEARNING** a sinistra.
2. Fare clic su un'area di lavoro.
3. Fare clic su **WEB SERVICES**.
4. Fare clic su un servizio Web.
5. Fare clic su un endpoint. La "CHIAVE API" si trova in basso a destra.

## <a id="connect"></a>Connettersi a un servizio Web di Azure Machine Learning

È possibile effettuare la connessione a un servizio Web di Azure Machine Learning utilizzando qualsiasi linguaggio di programmazione che supporta la risposta e richiesta HTTP. È possibile visualizzare gli esempi in c#, Python e R da una pagina della Guida del servizio Web ML di Azure.

### Per visualizzare una pagina della Guida API del servizio Web di Azure ML ###
Una pagina della Guida API ML Azure viene creata quando si distribuisce un servizio Web. Vedere [Procedura dettagliata di Azure Machine Learning - Distribuire il servizio Web](machine-learning-walkthrough-5-publish-web-service.md).


**Per visualizzare una pagina della Guida per l'API di Azure ML** In Microsoft Azure Machine Learning Studio:

1. Scegliere **WEB SERVICES**.
2. Scegliere un servizio Web.
3. Scegliere la **pagina della Guida per l'API** - **REQUEST/RESPONSE** o **BATCH EXECUTION**.


**Pagina della Guida per l'API di Azure ML** La pagina della Guida per l'API di Azure ML contiene i dettagli su un servizio Web di stima.



### Esempio C# ###

Per connettersi a un servizio Web di Azure ML, usare **HttpClient** per passare ScoreData. ScoreData contiene FeatureVector, un vettore n-dimensionale di funzioni numeriche che rappresentano ScoreData. Effettuare l'autenticazione al servizio Azure ML con una chiave API.

Per connettersi a un servizio Web di ML, è necessario installare il pacchetto NuGet **Microsoft.AspNet.WebApi.Client**.

**Installare Microsoft.AspNet.WebApi.Client Nuget su Visual Studio**

1. Pubblicare il set di dati di download dal servizio Web UCI: Adult 2 class dataset.
2. Fare clic su **Strumenti** > **Gestione pacchetto NuGet** > **Console di gestione pacchetti**.
2. Scegliere **Install-Package Microsoft.AspNet.WebApi.Client**.

**Per eseguire l'esempio di codice**

1. Pubblicare l'esperimento "Sample 1: Download dataset from UCI: Adult 2 class dataset", parte della raccolta di esempi di Azure ML.
2. Assegnare la chiave API con la chiave da un servizio Web. Scoprire come ottenere una chiave di autorizzazione ML di Azure.
3. Assegnare l'URI del servizio con l'URI della richiesta.


### Esempio Python ###

Per connettersi a un servizio Web di Azure ML, usare la libreria **urllib2** per passare ScoreData. ScoreData contiene FeatureVector, un vettore n-dimensionale di funzioni numeriche che rappresentano ScoreData. Effettuare l'autenticazione al servizio Azure ML con una chiave API.


**Per eseguire l'esempio di codice**

1. Pubblicare l'esperimento "Sample 1: Download dataset from UCI: Adult 2 class dataset", parte della raccolta di esempi di Azure ML.
2. Assegnare la chiave API con la chiave da un servizio Web. Scoprire come ottenere una chiave di autorizzazione ML di Azure.
3. Assegnare l'URI del servizio con l'URI della richiesta. Scoprire come ottenere un URI della richiesta.

<!---HONumber=AcomDC_1203_2015-->