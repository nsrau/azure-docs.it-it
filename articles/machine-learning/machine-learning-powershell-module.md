---
title: Modulo PowerShell per Machine Learning | Microsoft Docs
description: Il modulo PowerShell per Azure Machine Learning è disponibile in modalità di anteprima pubblica. Usare PowerShell per creare e gestire aree di lavoro, esperimenti, servizi Web e altro ancora.
keywords: esperimento,regressione lineare,algoritmi di machine learning,esercitazione su machine learning,tecniche di modellazione predittiva,esperimento di analisi scientifica dei dati
services: machine-learning
documentationcenter: ''
author: hning86
manager: jhubbard
editor: cgronlun

ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 08/05/2016
ms.author: garye;haining

---
# Modulo PowerShell per Microsoft Azure Machine Learning
Il modulo PowerShell per Azure Machine Learning è uno strumento avanzato che consente di usare Windows PowerShell per gestire aree di lavoro, esperimenti, set di dati, servizi Web e altro.

È possibile visualizzare la documentazione e scaricare il modulo, con il codice sorgente completo, all'indirizzo [https://aka.ms/amlps](https://aka.ms/amlps).

## Cos'è il modulo PowerShell per Machine Learning?
Il modulo PowerShell per Machine Learning è un modulo DLL basato su .NET che consente di gestire completamente aree di lavoro, esprimenti, set di dati, servizi Web ed endpoint di servizio Web di Azure Machine Learning da Windows PowerShell. Con il modulo, è possibile scaricare il codice sorgente completo che include un [livello API C#](https://github.com/hning86/azuremlps/blob/master/code/AzureMLSDK.cs) del tutto separato. Ciò consente di fare riferimento a questa DLL dal proprio progetto .NET e di gestire Azure Machine Learning tramite il codice .NET. La DLL dipende anche dalle API REST sottostanti che è possibile sfruttare direttamente dal client preferito.

## Quali operazioni è possibile eseguire con il modulo PowerShell?
Ecco alcune delle attività che è possibile eseguire con questo modulo PowerShell. Vedere la [documentazione completa](https://aka.ms/amlps) per queste e altre funzioni.

* Effettuare il provisioning di una nuova area di lavoro usando un certificato di gestione ([New-AmlWorkspace](https://github.com/hning86/azuremlps#new-amlworkspace))
* Esportare e importare un file JSON che rappresenta un grafico di un esperimento ([Export-AmlExperimentGraph](https://github.com/hning86/azuremlps#export-amlexperimentgraph) e [Import-AmlExperimentGraph](https://github.com/hning86/azuremlps#import-amlexperimentgraph))
* Eseguire un esperimento ([Start-AmlExperiment](https://github.com/hning86/azuremlps#start-amlexperiment))
* Creare un servizio Web al di fuori di un esperimento predittivo ([New-AmlWebService](https://github.com/hning86/azuremlps#new-amlwebservice))
* Creare un nuovo endpoint in un servizio Web pubblicato ([Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint))
* Richiamare un endpoint di servizio Web RRS e/o BES ([Invoke-AmlWebServiceRRSEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) e [Invoke-AmlWebServicBESEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint))

Ecco un rapido esempio dell'uso di PowerShell per eseguire un esperimento esistente:

        #Find the first Experiment named “xyz”
        $exp = (Get-AmlExperiment | where Description -eq ‘xyz’)[0]
        #Run the Experiment
        Start-AmlExperiment -ExperimentId $exp.ExperimentId 

Per un caso d'uso più approfondito, vedere questo articolo sull'uso del modulo PowerShell per automatizzare un'attività richiesta molto di frequente: [Creare più modelli di Machine Learning ed endpoint di servizio Web da un esperimento usando PowerShell](machine-learning-create-models-and-endpoints-with-powershell.md).

## Come iniziare?
Per iniziare a usare PowerShell per Machine Learning, scaricare il [pacchetto della versione](https://github.com/hning86/azuremlps/releases) da GitHub e seguire le [istruzioni per l'installazione](https://github.com/hning86/azuremlps/blob/master/README.md). Sarà necessario sbloccare la DLL scaricata/decompressa e quindi importarla nell'ambiente di PowerShell. La maggior parte dei cmdlet richiede di specificare l'ID area di lavoro, il token di autorizzazione per l'area di lavoro e l'area di Azure in cui si trova l'area di lavoro. Il modo più semplice per specificarli consiste nell'usare un file config.json predefinito, illustrato in dettaglio nelle istruzioni di installazione. È anche possibile clonare l'albero di Git e modificare/compilare il codice in locale usando Visual Studio.

## Passaggi successivi
Il modulo PowerShell verrà continuamente migliorato ed esteso durante questo periodo di anteprima. Monitorare i [blog di Cortana Intelligence e di Machine Learning](https://blogs.technet.microsoft.com/machinelearning/) per altre informazioni e notizie aggiornate.

<!---HONumber=AcomDC_0914_2016-->