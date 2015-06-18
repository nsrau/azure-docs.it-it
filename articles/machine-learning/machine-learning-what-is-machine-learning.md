<properties 
    pageTitle="Cos'è Azure Machine Learning? | Microsoft Azure" 
    description="Panoramica sul servizio Azure Machine Learning." 
    services="machine-learning" 
    documentationCenter="" 
    authors="tedway" 
    manager="neerajkh" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/22/2015" 
    ms.author="tedway;olgali"/>


# Che cos'è l'apprendimento automatico?
L'apprendimento automatico è ormai usato in moltissime situazioni. Quando si effettuano acquisti online, l'apprendimento automatico consente di consigliare altri prodotti in base a ciò che si è acquistato. Quando si usa la carta di credito, l'apprendimento automatico consente alla banca di rilevare eventuali frodi e inviare una notifica all'utente se la transazione sembra sospetta. L'apprendimento automatico è il processo di creazione di modelli che apprendono da dati esistenti ed eseguono analisi predittive sui dati futuri.

## Cos'è Azure Machine Learning?
Azure Machine Learning è un potente servizio di analisi predittive basate su cloud che consente di creare rapidamente soluzioni di analisi. Azure Machine Learning è stato creato per democratizzare l'apprendimento automatico: sono state eliminate le operazioni complesse relative alla creazione e alla distribuzione della tecnologia di apprendimento automatico per renderla accessibile a tutti gli utenti. È un servizio completamente gestito, pertanto non è necessario acquistare un hardware né gestire manualmente macchine virtuali.

È possibile usare lo strumento basato su browser [Machine Learning Studio](machine-learning-what-is-ml-studio.md) per creare rapidamente e automatizzare i flussi di lavoro di apprendimento automatico. È possibile trascinare e rilasciare centinaia di [librerie di Machine Learning](https://msdn.microsoft.com/library/azure/f5c746fd-dcea-4929-ba50-2a79c4c067d7) esistenti per avviare le soluzioni di analisi predittive e, facoltativamente, aggiungere script [R](machine-learning-r-quickstart.md) e [Python](machine-learning-execute-python-scripts.md) personalizzati per estendere tali soluzioni. Lo strumento funziona in qualsiasi browser e consente di sviluppare e scorrere rapidamente le soluzioni. ![Esperimenti di analisi predittive nel cloud con Azure Machine Learning Studio](./media/machine-learning-what-is-machine-learning/AzureMLStudio.png)

È possibile individuare e creare facilmente [servizi Web](machine-learning-publish-a-machine-learning-web-service.md), [eseguire e ripetere il training dei modelli tramite le API](machine-learning-retrain-models-programmatically.md), [gestire gli endpoint](machine-learning-create-endpoint.md) e [ridimensionare servizi Web](machine-learning-scaling-endpoints.md) in base alle esigenze del cliente e configurare la diagnostica per il monitoraggio e il debug dei servizi. Le nuove funzionalità includono:

- La possibilità di creare un modulo R personalizzato e configurabile, di incorporare i propri script R di training/predizioni e aggiungere script Python usando un ampio ecosistema di librerie, ad esempio numpy, scipy, pandas o scikit-learn. Sarà ora possibile eseguire il training su terabyte di dati mediante [Learning with Counts][learning-with-counts], usare la stima PCA o SVM a una classe per il rilevamento di anomalie e modificare, filtrare e ripulire facilmente i dati tramite la già nota soluzione SQLite. 
- [La Raccolta della community di Machine Learning](machine-learning-gallery-how-to-use-contribute-publish.md) consente di scoprire e usare interessanti esperimenti creati da altri utenti. È possibile porre domande o inviare commenti sugli esperimenti presenti nella [Raccolta](http://gallery.azureml.net) oppure pubblicare i propri esperimenti. È possibile condividere collegamenti a esperimenti interessanti tramite i canali di social networking come LinkedIn e Twitter. La raccolta è un ottimo modo per iniziare a usare Azure Machine Learning e imparare da altri utenti nella community. ![Provare gli esempi di esperimenti predittivi o pubblicare il proprio esperimento nella Raccolta di Azure Machine Learning](./media/machine-learning-what-is-machine-learning/AzureMLGallery.png)
- È possibile acquistare le [app per Marketplace](https://datamarket.azure.com/browse?query=machine+learning) tramite una sottoscrizione di Azure e utilizzare servizi Web completati per indicazioni, analisi del testo e rilevamento delle anomalie direttamente da Azure Marketplace. 
- Una guida dettagliata per passare dai dati non elaborati a un servizio Web di facile utilizzo per semplificare il percorso per i dati basati su cloud. È stata aggiunta la possibilità di usare strumenti di ampia diffusione, ad esempio iPython Notebook e Python Tools per Visual Studio con Azure Machine Learning.

## Attività iniziali
È possibile apprendere le nozioni di base sulle analisi predittive e sull'apprendimento automatico tramite un'[esercitazione dettagliata](machine-learning-create-experiment.md) e [gli esempi](machine-learning-sample-experiments.md). Per iniziare a usare Machine Learning e provare gli esperimenti in Studio non è necessaria alcuna sottoscrizione di Azure né una carta di credito.


<!-- Module References -->
[learning-with-counts]: https://msdn.microsoft.com/library/azure/81c457af-f5c0-4b2d-922c-fdef2274413c/

<!--HONumber=54--> 