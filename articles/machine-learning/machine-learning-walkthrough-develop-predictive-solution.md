<properties
	pageTitle="Soluzione predittiva per il rischio di credito con Machine Learning | Microsoft Azure"
	description="Una procedura dettagliata che mostra come creare una soluzione di analisi predittiva per la valutazione del rischio di credito in Azure Machine Learning Studio."
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
	ms.topic="get-started-article" 
	ms.date="07/10/2015"
	ms.author="garye"/>


# Procedura dettagliata: Sviluppare una soluzione di analisi predittiva per la valutazione del rischio di credito in Azure Machine Learning

Si supponga di dover prevedere il rischio di credito di un soggetto in base alle informazioni fornite in una richiesta di credito.

La valutazione del rischio di credito è naturalmente un problema complesso, ma semplificando i parametri della questione è possibile usarlo come esempio per illustrare come si può usare Microsoft Azure Machine Learning con Machine Learning Studio e il servizio Web di Machine Learning per creare questo tipo di soluzione di analisi predittiva.

In questa procedura dettagliata verrà descritto il processo di sviluppo di un modello di analisi predittiva in Machine Learning Studio e la successiva pubblicazione come servizio Web di Azure Machine Learning. Verranno usati dati sul rischio di credito disponibili pubblicamente, quindi si passerà allo sviluppo e all'esecuzione del training di un modello predittivo in base a tali dati. Infine si pubblicherà il modello come un servizio Web che può essere usato da altri utenti per la valutazione del rischio di credito.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Aprire Machine Learning Studio: [https://studio.azureml.net/Home](https://studio.azureml.net/Home). Per altre informazioni introduttive su Machine Learning Studio, vedere la [home page di Microsoft Azure Machine Learning Studio ](https://studio.azureml.net/).

Per creare una soluzione di valutazione del rischio di credito, seguire la procedura seguente:

1.	[Creare un'area di lavoro di Machine Learning](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	[Caricare i dati esistenti](machine-learning-walkthrough-2-upload-data.md)
3.	[Creare un nuovo esperimento](machine-learning-walkthrough-3-create-new-experiment.md)
4.	[Eseguire il training e valutare i modelli](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.	[Pubblicare il servizio Web](machine-learning-walkthrough-5-publish-web-service.md)
6.	[Accedere al servizio Web](machine-learning-walkthrough-6-access-web-service.md)

Questa procedura dettagliata è basata su una versione semplificata dell'esperimento di esempio [Credit risk prediction](../machine-learning-sample-credit-risk-prediction.md) incluso in Machine Learning Studio.
 

<!---HONumber=July15_HO4-->