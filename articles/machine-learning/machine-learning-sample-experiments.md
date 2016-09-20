<properties
    pageTitle="Copiare gli esperimenti di esempio di apprendimento automatico | Microsoft Azure"
    description="Informazioni su come usare gli esperimenti di apprendimento automatico per creare nuovi esperimenti con Cortana Intelligence Gallery e Microsoft Azure Machine Learning."
    services="machine-learning"
    documentationCenter=""
    authors="cjgronlund"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/17/2016"
    ms.author="cgronlun;chhavib;olgali"/> 

# Copiare gli esperimenti di esempio per creare nuovi esperimenti di apprendimento automatico
Questo articolo illustra come iniziare con gli esperimenti di esempio di [Cortana Intelligence Gallery](http://gallery.cortanaintelligence.com/) invece di creare esperimenti di apprendimento automatico da zero. È possibile usare gli esempi per compilare la propria soluzione di apprendimento automatico.

Nella raccolta sono disponibili esperimenti di esempio del team di Microsoft Azure Machine Learning, oltre a esempi condivisi dalla community di Machine Learning. È anche possibile porre domande o inviare commenti sugli esperimenti disponibili.

Per informazioni su come usare la raccolta, guardare il video di 3 minuti [Copiare il lavoro di altre persone per l'analisi scientifica dei dati](machine-learning-data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) della serie [Analisi scientifica dei dati per principianti](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md).

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Trovare un esperimento da copiare in Cortana Intelligence Gallery

Per visualizzare gli esperimenti disponibili, passare a [Cortana Intelligence Gallery](http://gallery.cortanaintelligence.com/) e fare clic su **Experiments** (Esperimenti) nella parte superiore della pagina.

### Trovare gli esperimenti più recenti o richiesti

In questa pagina è possibile visualizzare gli esperimenti **aggiunti di recente**, scorrere per esaminare quelli **più richiesti** o la versione più recente degli **esperimenti Microsoft più richiesti**.

### Cercare un esperimento che soddisfa requisiti specifici

Per esplorare tutti gli esperimenti:

1. Fare clic su **Browse all** (Esplora tutto) nella parte superiore della pagina.
2. In **Refine by** (Affina per) selezionare **Experiment** (Esperimento) per visualizzare tutti gli esperimenti nella raccolta.
3. È possibile trovare esperimenti che soddisfano i requisiti in due modi diversi:
    * **Selezionare i filtri a sinistra.** Ad esempio, per esaminare gli esperimenti che fanno uso di un algoritmo di rilevamento delle anomalie basato su PCA, selezionare **Experiment** (Esperimento) in **Categories** (Categorie) e **PCA-Based Anomaly Detection** (Rilevamento anomalie basato su PCA) in **Algorithms Used** (Algoritmi usati). Se l'algoritmo non è visualizzato, fare clic su **Show all** (Mostra tutto) nella parte inferiore dell'elenco.<br></br>![](./media/machine-learning-sample-experiments/refine-the-view.png)
    *  **Usare la casella di ricerca.** Ad esempio, per trovare esperimenti Microsoft sul riconoscimento di cifre che usano un algoritmo di macchine a vettori di supporto a due classi, immettere "digit recognition" nella casella di ricerca. Selezionare quindi i filtri **Experiment** (Esperimento), **Microsoft content only** (Solo contenuto Microsoft) e **Two-Class Support Vector Machine** (Macchina a vettori di supporto a due classi): ![](./media/machine-learning-sample-experiments/search-for-experiments.png)
4. Per altre informazioni su un esperimento, fare clic su di esso.
5. Per eseguire e/o modificare l'esperimento, fare clic su **Open in Studio** (Apri in Studio) nella pagina dell'esperimento.

    > [AZURE.NOTE] Per aprire un esperimento in Machine Learning Studio, è necessario accedere con le credenziali dell'account Microsoft. Se non si dispone ancora di un'area di lavoro di Machine Learning, verrà creata un'area di lavoro di valutazione gratuita. [Informazioni sulla versione di valutazione gratuita di Machine Learning.](https://azure.microsoft.com/pricing/details/machine-learning/)

    ![](./media/machine-learning-sample-experiments/example-experiment.png) 


## Usare un modello in Machine Learning Studio

È anche possibile creare un nuovo esperimento in Machine Learning Studio usando come modello un esempio di Cortana Intelligence Gallery.

1. Accedere a [Studio](https://studio.azureml.net) con le credenziali dell'account Microsoft e quindi fare clic su **New** (Nuovo) per creare un nuovo esperimento.
2. Esplorare il contenuto e fare clic per selezionare un esempio.

Viene creato un nuovo esperimento nell'area di lavoro usando l'esperimento di esempio come modello.

## Passaggi successivi
- [Preparare i dati](machine-learning-data-science-import-data.md)
- [Provare a usare R nell'esperimento](machine-learning-r-quickstart.md)
- [Esaminare gli esperimenti di esempio R](machine-learning-r-csharp-web-service-examples.md)
- [Distribuire un servizio Web di Azure Machine Learning](machine-learning-publish-a-machine-learning-web-service.md)
- [Esplorare applicazioni pronte per l'uso](https://datamarket.azure.com/browse?query=machine+learning)

<!---HONumber=AcomDC_0914_2016-->