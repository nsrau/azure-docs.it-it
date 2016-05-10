<properties
    pageTitle="Usare gli esperimenti di esempio per creare nuovi esperimenti | Microsoft Azure"
    description="Creare nuovi esperimenti di Azure Machine Learning da esperimenti di esempio ed esperimenti condivisi dalla community."
    services="machine-learning"
    documentationCenter=""
    authors="cjgronlund"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="04/28/2016"
    ms.author="chhavib;olgali"/>

# Usare gli esperimenti di esempio per creare nuovi esperimenti
Per creare un esperimento, è possibile cercare altri esempi nella [Cortana Intelligence Gallery](http://gallery.cortanaintelligence.com/). Cortana Intelligence Gallery contiene esperimenti di esempio messi a disposizione dal team di Machine Learning ed esperimenti condivisi dalla community estesa di Machine Learning. È anche possibile porre domande o inviare commenti sugli esperimenti disponibili.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Esplorare Cortana Intelligence Gallery
Per visualizzare gli esperimenti disponibili, passare a [Cortana Intelligence Gallery](http://gallery.cortanaintelligence.com/) e fare clic su **Experiments** nella parte superiore della pagina. In questa pagina è possibile visualizzare gli esperimenti **aggiunti di recente**, esaminare quelli **più richiesti** o vedere la versione più recente degli **esperimenti Microsoft più richiesti**.

Per esplorare tutti gli esperimenti:

1. Fare clic su **Browse all** nella parte superiore della pagina.
2. In **Refine by** selezionare **Experiment** per visualizzare tutti gli esperimenti nella raccolta.
3. È possibile restringere ancora l'elenco selezionando i filtri a sinistra. Ad esempio, per esaminare gli esperimenti che fanno uso di un algoritmo di rilevamento delle anomalie basato su PCA, selezionare **Experiment** in **Categories** e **PCA-Based Anomaly Detection** in **Algorithms Used**. Se l'algoritmo non è visualizzato, fare clic su **Show all** nella parte inferiore dell'elenco.<br></br> ![](./media/machine-learning-sample-experiments/refine-the-view.png) 
4. È possibile usare la casella di ricerca per restringere ancora di più le scelte. Ad esempio, per trovare esperimenti Microsoft sul riconoscimento di cifre che usano un algoritmo di macchine a vettori di supporto a due classi, immettere "digit recognition" nella casella di ricerca e selezionare **Experiment**, **Microsoft content only** e **Two-Class Support Vector Machine**: ![](./media/machine-learning-sample-experiments/search-for-experiments.png) 
5. Per altre informazioni su un esperimento, fare clic su di esso. Per eseguire e/o modificare l'esperimento, fare clic su **Open in Studio** nella pagina dell'esperimento.

> [AZURE.NOTE] Per aprire un esperimento in Machine Learning Studio, è necessario accedere con le credenziali dell'account Microsoft. Se non si dispone ancora di un'area di lavoro di Machine Learning, verrà creata un'area di lavoro di valutazione gratuita. [Informazioni sulla versione di valutazione gratuita di Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/).

![](./media/machine-learning-sample-experiments/example-experiment.png)


## Usare un modello in Machine Learning Studio

È anche possibile creare un nuovo esperimento in Machine Learning Studio usando come modello un esempio di Cortana Intelligence Gallery.

1. Accedere a [Studio](https://studio.azureml.net) con le credenziali dell'account Microsoft e quindi fare clic su **New*** per creare un nuovo esperimento.
2. Esplorare il contenuto e fare clic per selezionare un esempio.

Viene creato un nuovo esperimento nell'area di lavoro usando l'esperimento di esempio come modello.

## Passaggi successivi
- [Preparare i dati](machine-learning-data-science-import-data.md)
- [Provare a usare R nell'esperimento](machine-learning-r-quickstart.md)
- [Esaminare gli esperimenti di esempio R](machine-learning-r-csharp-web-service-examples.md)
- [Creare un'API del servizio Web](machine-learning-publish-a-machine-learning-web-service.md)
- [Esplorare applicazioni pronte per l'uso](https://datamarket.azure.com/browse?query=machine+learning)

<!---HONumber=AcomDC_0504_2016-->