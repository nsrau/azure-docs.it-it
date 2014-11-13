<properties title="Sviluppare una soluzione predittiva con Azure Machine Learning" pageTitle="Sviluppare una soluzione predittiva con Machine Learning | Azure" description="Procedura dettagliata per creare un semplice esperimento di analisi predittiva in Azure Machine Learning Studio" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/06/2014" ms.author="garye" />

# Sviluppare una soluzione predittiva con Azure Machine Learning

Si supponga di dover prevedere il rischio di credito di un soggetto in base alle informazioni fornite in una richiesta di credito.

Si tratta di un problema complesso, ma semplificando i parametri della questione è possibile usarlo come esempio per illustrare come si può usare Microsoft Azure Machine Learning con ML Studio e il servizio ML API per creare questo tipo di soluzione di analisi predittiva.

In questa procedura dettagliata verranno descritti i processi di sviluppo di un modello di analisi predittivo in ML Studio e di pubblicazione del progetto nel servizio ML API. Verranno usati dati sul rischio di credito disponibili pubblicamente, quindi si passerà allo sviluppo e all'addestramento di un modello predittivo in base a tali dati e infine il modello verrà pubblicato come un servizio Web che può essere usato da altri utenti.

Attenersi alla procedura descritta di seguito.

1.  [Creare un'area di lavoro ML][Creare un'area di lavoro ML]
2.  [Caricare i dati esistenti][Caricare i dati esistenti]
3.  [Creare un nuovo esperimento][Creare un nuovo esperimento]
4.  [Addestrare e valutare i modelli][Addestrare e valutare i modelli]
5.  [Pubblicare il servizio Web][Pubblicare il servizio Web]
6.  [Accedere al servizio Web][Accedere al servizio Web]

Questa procedura dettagliata è basata su una versione semplificata dell'
[esperimento di esempio per la predizione del rischio di credito][esperimento di esempio per la predizione del rischio di credito].

  [Creare un'area di lavoro ML]: ../machine-learning-walkthrough-1-create-ml-workspace/
  [Caricare i dati esistenti]: ../machine-learning-walkthrough-2-upload-data/
  [Creare un nuovo esperimento]: ../machine-learning-walkthrough-3-create-new-experiment/
  [Addestrare e valutare i modelli]: ../machine-learning-walkthrough-4-train-and-evaluate-models/
  [Pubblicare il servizio Web]: ../machine-learning-walkthrough-5-publish-web-service/
  [Accedere al servizio Web]: ../machine-learning-walkthrough-6-access-web-service/
  [esperimento di esempio per la predizione del rischio di credito]: ../machine-learning-sample-credit-risk-prediction/
