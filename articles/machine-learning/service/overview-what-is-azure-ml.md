---
title: Analisi di simulazione
titleSuffix: Azure Machine Learning service
description: Panoramica del servizio Azure Machine Learning, una soluzione integrata di data science end-to-end destinata a data scientist professionisti per lo sviluppo, la sperimentazione e la distribuzione di applicazioni per analisi avanzate su scala cloud.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: overview
ms.reviewer: jmartens
author: garyericson
ms.author: garye
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 14ba6438fe5b797d0070dde773855b9b84322a2c
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2019
ms.locfileid: "54263582"
---
# <a name="what-is-azure-machine-learning-service"></a>Informazioni sul servizio Azure Machine Learning

Il servizio Azure Machine Learning è un servizio cloud che è possibile usare per eseguire il training di modelli di Machine Learning, distribuirli, automatizzarli e gestirli, il tutto su vasta scala, grazie all'ampia portata del cloud.

## <a name="what-is-machine-learning"></a>Che cos'è l'apprendimento automatico?

Machine Learning è una tecnica di analisi scientifica dei dati che consente ai computer di usare i dati esistenti per prevedere comportamenti, tendenze e risultati futuri. Con l'apprendimento automatico, i computer apprendono senza essere programmati in modo esplicito.

Queste previsioni o stime di Machine Learning possono rendere più intelligenti le app e i dispositivi. Quando si effettuano acquisti online, ad esempio, l'apprendimento automatico consiglia altri prodotti che potrebbero interessare in base a ciò che si è acquistato. Quando si usa la carta di credito, l'apprendimento automatico confronta la transazione con un database di transazioni e consente di rilevare eventuali frodi. Infine, quando il robot aspirapolvere aspira la polvere in una stanza, l'apprendimento automatico gli consente di decidere se il lavoro è stato completato.

## <a name="what-is-azure-machine-learning-service"></a>Informazioni sul servizio Azure Machine Learning

Il servizio Azure Machine Learning offre un ambiente basato su cloud utile per sviluppare, eseguire il training, testare, distribuire, gestire e tenere traccia dei modelli di Machine Learning.

[![Flusso di lavoro del servizio Azure Machine Learning](./media/overview-what-is-azure-ml/aml.png)] (./media/overview-what-is-azure-ml/aml.png#lightbox)

Il servizio Azure Machine Learning offre supporto completo per le tecnologie open source. È quindi possibile usare decine di migliaia di pacchetti Python open source con componenti di apprendimento automatico. Queste soluzioni includono, ad esempio, TensorFlow e Scikit-learn.
Il supporto per gli strumenti avanzati semplifica l'esplorazione interattiva dei dati, la loro trasformazione e quindi lo sviluppo e il test dei modelli. Tra gli strumenti supportati vi sono [Jupyter Notebook](http://jupyter.org) e l'estensione [Azure Machine Learning per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai#overview).
Il servizio Azure Machine Learning include anche funzionalità per [automatizzare la generazione e l'ottimizzazione dei modelli](tutorial-auto-train-models.md), per creare modelli in modo semplice, efficiente e accurato.

Usando il servizio Azure Machine Learning, è possibile avviare il training nel computer locale e quindi ampliarlo nel cloud. Grazie alla disponibilità di molte [destinazioni di calcolo](how-to-set-up-training-targets.md), ad esempio l'ambiente di calcolo di Azure Machine Learning e [Azure Databricks](/azure/azure-databricks/what-is-azure-databricks), e a [servizi avanzati per l'ottimizzazione degli iperparametri](how-to-tune-hyperparameters.md), è possibile creare modelli migliori in modo più rapido, sfruttando la potenza del cloud.

Dopo aver ottenuto il modello più idoneo, è possibile distribuirlo in un contenitore come Docker. È quindi possibile eseguire in modo semplice la distribuzione in Istanze di Azure Container o nel servizio Azure Kubernetes. In alternativa, è possibile usare il contenitore nelle proprie distribuzioni, in locale o nel cloud. Per altre informazioni, vedere l'articolo su [come e dove eseguire la distribuzione](how-to-deploy-and-where.md).

È possibile gestire i modelli distribuiti e tenere traccia delle diverse esecuzioni sperimentate per individuare la soluzione migliore.
Dopo la distribuzione, il modello può restituire stime [in tempo reale](how-to-consume-web-service.md) o [in modo asincrono](how-to-run-batch-predictions.md) su grandi quantità di dati.

È inoltre possibile usare [pipeline di apprendimento automatico](concept-ml-pipelines.md) avanzate per collaborare a tutti i passaggi di preparazione dei dati, training del modello, valutazione e distribuzione.

## <a name="what-can-i-do-with-azure-machine-learning-service"></a>Quali operazioni si possono eseguire con Azure Machine Learning?

Il servizio Azure Machine Learning può eseguire il training di un modello e ottimizzarlo automaticamente.
Per un esempio, vedere[Eseguire il training di un modello di regressione con apprendimento automatico](tutorial-auto-train-models.md).

Usando l'<a href="https://aka.ms/aml-sdk" target="_blank">SDK</a> di Azure Machine Learning per Python insieme ai pacchetti Python open source, è possibile compilare ed eseguire autonomamente il training di modelli di apprendimento automatico e Deep Learning estremamente precisi e avanzati in un'area di lavoro del servizio Azure Machine Learning.
È possibile scegliere tra i numerosi componenti di apprendimento automatico disponibili nei pacchetti Python open source, ad esempio:

- <a href="https://scikit-learn.org/stable/" target="_blank">Scikit-learn</a>
- <a href="https://www.tensorflow.org" target="_blank">Tensorflow</a>
- <a href="https://pytorch.org" target="_blank">PyTorch</a>
- <a href="https://www.microsoft.com/en-us/cognitive-toolkit/" target="_blank">CNTK</a>
- <a href="http://mxnet.io" target="_blank">MXNet</a>

Dopo aver creato un modello, è possibile usarlo per creare un contenitore, ad esempio Docker, che può essere distribuito in locale per i test. Al termine dei test, il modello può essere distribuito come servizio Web di produzione in Istanze di Azure Container o nel servizio Azure Kubernetes. Per altre informazioni, vedere l'articolo su [come e dove eseguire la distribuzione](how-to-deploy-and-where.md).

È quindi possibile gestire i modelli distribuiti usando l'[SDK di Azure Machine Learning per Python](https://aka.ms/aml-sdk) o il [portale di Azure](https://portal.azure.com/).
È possibile valutare le metriche del modello, ripetere il training, ridistribuire le nuove versioni del modello e al tempo stesso tenere traccia degli esperimenti del modello.

Per iniziare a usare il servizio Azure Machine Learning, vedere [Passaggi successivi](#next-steps).

## <a name="how-is-azure-machine-learning-service-different-from-machine-learning-studio"></a>Quali sono le differenze tra il servizio Azure Machine Learning e Machine Learning Studio?

Azure Machine Learning Studio è un'area di lavoro collaborativa e grafica con selezione e trascinamento degli elementi, in cui è possibile compilare, testare e distribuire soluzioni di apprendimento automatico senza dover scrivere codice. Usa algoritmi di apprendimento automatico e moduli di gestione dei dati precompilati e preconfigurati.

Azure Machine Learning Studio è adatta a sperimentare modelli di apprendimento automatico in modo semplice e rapido quando gli algoritmi di apprendimento automatico predefiniti sono sufficienti per le soluzioni dell'utente.

Il servizio Azure Machine Learning è adatto agli ambienti Python, quando è necessario disporre di maggior controllo sugli algoritmi di apprendimento automatico oppure si intende usare le librerie di apprendimento automatico open source.

> [!NOTE]
> I modelli creati in Azure Machine Learning Studio non possono essere distribuiti né gestiti tramite il servizio Azure Machine Learning.

## <a name="free-trial"></a>Versione di prova gratuita

Se non si dispone di una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Accedere alla [versione gratuita o a pagamento del servizio Azure Machine Learning](http://aka.ms/AMLFree).

Si ricevono così crediti da spendere in servizi di Azure. Quando i crediti saranno esauriti, sarà possibile mantenere l'account e usare i [servizi di Azure gratuiti](https://azure.microsoft.com/free/). Verranno applicati addebiti alla carta di credito solo se l'utente modifica le impostazioni e richiede esplicitamente l'addebito. In alternativa, è possibile [attivare i vantaggi per i sottoscrittori di MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F), in modo da accumulare ogni mese crediti che possono essere usati per i servizi di Azure a pagamento.

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare, creare un'area di lavoro del servizio Machine Learning [tramite il portale di Azure](quickstart-get-started.md) o [in Python](quickstart-create-workspace-with-python.md).

- Eseguire l'esercitazione completa [Eseguire il training di un modello di classificazione delle immagini con il servizio Azure Machine Learning](tutorial-train-models-with-aml.md).

- [Usare Azure Machine Learning per generare e ottimizzare automaticamente un modello](tutorial-auto-train-models.md).

- Per preparare i dati, usare [ Azure Machine Learning Data Prep SDK](https://aka.ms/data-prep-sdk).

- Vedere le [pipeline di apprendimento automatico](/azure/machine-learning/service/concept-ml-pipelines) per compilare, ottimizzare e gestire gli scenari di Machine Learning.

- Leggere l'articolo di approfondimento [Informazioni sul funzionamento del servizio Azure Machine Learning: architettura e concetti](concept-azure-machine-learning-architecture.md).

- Per altre informazioni, vedere [altri prodotti di apprendimento automatico Microsoft](./overview-more-machine-learning.md).


<!-- 

An intro to AML or an end-to-end quickstart video could go here.

In this 9-minute video, learn how you can benefit your app. You'll learn about key features and what a typical workflow looks like. 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ 0-3 minutes covers key features and use-cases.
+ 3-4 minutes covers service provisioning. 
+ 4-6 minutes covers Import Data wizard used to create an index using the built-in real estate dataset.

-->
