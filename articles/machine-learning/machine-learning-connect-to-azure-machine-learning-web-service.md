<properties
	pageTitle="Connessione a un servizio Web di Machine Learning | Microsoft Azure"
	description="Effettuare la connessione a un servizio Web di Azure Machine Learning con c# o Python utilizzando una chiave di autorizzazione."
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="jhubbard"
	editor="cgronlun" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/06/2016" 
	ms.author="garye" />


# Connessione a un servizio Web di Azure Machine Learning
L'esperienza dello sviluppatore di Azure Machine Learning è un'API del servizio Web per eseguire stime dai dati di input in tempo reale o in modalità batch. È possibile usare Azure Machine Learning Studio per creare stime e distribuire un servizio Web di Azure Machine Learning.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Per informazioni su come creare e distribuire un servizio Web di Machine Learning tramite Machine Learning Studio:

- Per un'esercitazione su come creare un esperimento in Machine Learning Studio, vedere l'articolo su come [creare il primo esperimento](machine-learning-create-experiment.md).
- Per dettagli su come distribuire un servizio Web, vedere [Distribuire un servizio Web di Azure Machine Learning](machine-learning-publish-a-machine-learning-web-service.md).
- Per altre informazioni su Machine Learning in generale, accedere alla [Documentazione su Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

## Servizio Web Azure Machine Learning ##

Con il servizio Web di Azure Machine Learning, un'applicazione esterna comunica con un modello di valutazione del flusso di lavoro di Machine Learning in tempo reale. Una chiamata al servizio Web di Machine Learning restituisce i risultati della stima a un'applicazione esterna. Per effettuare una chiamata al servizio Web di Machine Learning, passare una chiave API creata quando si distribuisce una stima. Il servizio Web di Machine Learning è basato su REST, una scelta di architettura diffusa per progetti di programmazione Web.

Azure Machine Learning dispone di due tipi di servizi:

- Servizio di richiesta-risposta (RRS). Un servizio a latenza bassa e altamente scalabile che offre un'interfaccia ai modelli senza stato creati e distribuiti da Machine Learning Studio.
- Servizio esecuzione batch (BES). Un servizio asincrono che valuta un batch di record di dati.

Per altre informazioni sui servizi Web di Machine Learning, vedere [Distribuire un servizio Web di Azure Machine Learning](machine-learning-publish-a-machine-learning-web-service.md).

## Ottenere una chiave di autorizzazione Azure Machine Learning ##

Quando si distribuisce l'esperimento, vengono generate le chiavi API per il servizio Web. Il luogo da cui le chiavi vengono recuperate varia a seconda se l'esperimento è stato distribuito come nuovo servizio Web o come servizio Web classico.

## Servizio Web classico ##

 È possibile recuperare una chiave da Machine Learning Studio o dal portale di Azure.

### Machine Learning Studio ###

1. In Machine Learning Studio fare clic su **WEB SERVICES** (Servizi Web) a sinistra.
2. Fare clic su un servizio Web. La **chiave API** si trova nella scheda **DASHBOARD**.

### Portale di Azure ###

1. Fare clic su **MACHINE LEARNING** a sinistra.
2. Fare clic su un'area di lavoro.
3. Fare clic su **WEB SERVICES**.
4. Fare clic su un servizio Web.
5. Fare clic su un endpoint. La "CHIAVE API" si trova in basso a destra.


## Nuovo servizio Web 

Per recuperare la chiave API per un nuovo servizio Web di Machine Learning, è necessario accedere al portale [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/quickstart) (Servizi Web Microsoft Azure Machine Learning).

1. Nel portale Microsoft Azure Machine Learning Web Services (Servizi Web Microsoft Azure Machine Learning), fare clic su **WEB SERVICES** (Servizi Web) nel menu in alto.
2. Selezionare il servizio Web per il quale si desidera recuperare la chiave.
3. Nel menu in alto fare clic su **Consume** (Uso).
4. Copiare e salvare la **Chiave primaria**.

## <a id="connect"></a>Connettersi a un servizio Web di Machine Learning

È possibile connettersi a un servizio Web di Machine Learning usando qualsiasi linguaggio di programmazione che supporta la risposta e la richiesta HTTP. È possibile visualizzare gli esempi in C#, Python e R da una pagina della Guida del servizio Web di Machine Learning.

**Guida per l'API di Machine Learning** Una Guida per l'API di Machine Learning viene creata quando si distribuisce un servizio Web. Vedere [Procedura dettagliata di Azure Machine Learning - Distribuire il servizio Web](machine-learning-walkthrough-5-publish-web-service.md). La Guida per l'API di Machine Learning contiene i dettagli su un servizio Web di stima.

**Per visualizzare la Guida per l'API di Machine Learning per un servizio Web classico** in Machine Learning Studio:

1. Fare clic su **WEB SERVICES**.
2. Fare clic su un servizio Web.
3. Fare clic sulla **API help page** (Pagina della Guida per l'API) - **Richiesta/risposta** o **Esecuzione batch**.

**Per visualizzare la Guida per l'API di Machine Learning per un nuovo servizio Web** nel portale Azure Machine Learning Web Services (Servizi Web Microsoft Azure Machine Learning):

1. Fare clic su **WEB SERVICES** (Servizi Web) nel menu in alto.
2. Selezionare il servizio Web per il quale si desidera recuperare la chiave.

Fare clic su **Consume** (Uso) per ottenere l'URI per i servizi Richiesta/Risposta ed Esecuzione in batch, nonché il codice di esempio in C#, R e Python.

Fare clic su **Swagger API** (API Swagger) per ottenere la documentazione basata su Swagger per le API chiamate dagli URI specificati.

### Esempio C# ###

Per connettersi a un servizio Web di Machine Learning, usare **HttpClient** con ScoreData. ScoreData contiene FeatureVector, un vettore n-dimensionale di funzioni numeriche che rappresentano ScoreData. Effettuare l'autenticazione al servizio di Machine Learning con una chiave API.

Per connettersi a un servizio Web di Machine Learning, è necessario installare il pacchetto NuGet **Microsoft.AspNet.WebApi.Client**.

**Installare Microsoft.AspNet.WebApi.Client Nuget su Visual Studio**

1. Pubblicare il set di dati di download dal servizio Web UCI: Adult 2 class dataset.
2. Fare clic su **Strumenti** > **Gestione pacchetto NuGet** > **Console di gestione pacchetti**.
2. Scegliere **Install-Package Microsoft.AspNet.WebApi.Client**.

**Per eseguire l'esempio di codice**

1. Pubblicare l'esperimento "Sample 1: Download dataset from UCI: Adult 2 class dataset", che fa parte della raccolta di esempi di Machine Learning.
2. Assegnare la chiave API con la chiave da un servizio Web. Vedere la sezione precedente **Ottenere una chiave di autorizzazione di Azure Machine Learning**.
3. Assegnare l'URI del servizio con l'URI della richiesta.


### Esempio Python ###

Per connettersi a un servizio Web di Machine Learning, usare la libreria **urllib2** con ScoreData. ScoreData contiene FeatureVector, un vettore n-dimensionale di funzioni numeriche che rappresentano ScoreData. Effettuare l'autenticazione al servizio di Machine Learning con una chiave API.


**Per eseguire l'esempio di codice**

1. Distribuire l'esperimento "Sample 1: Download dataset from UCI: Adult 2 class dataset", che fa parte della raccolta di esempi di Machine Learning.
2. Assegnare la chiave API con la chiave da un servizio Web. Vedere la sezione precedente **Ottenere una chiave di autorizzazione di Azure Machine Learning**.
3. Assegnare l'URI del servizio con l'URI della richiesta.

<!---HONumber=AcomDC_0914_2016-->