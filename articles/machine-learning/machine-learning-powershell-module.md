---
title: Modulo PowerShell per Machine Learning | Documentazione Microsoft
description: "Il modulo PowerShell per Azure Machine Learning è disponibile in modalità di anteprima pubblica. Usare PowerShell per creare e gestire aree di lavoro, esperimenti, servizi Web e altro ancora."
keywords: esperimento,regressione lineare,algoritmi di machine learning,esercitazione su machine learning,tecniche di modellazione predittiva,esperimento di analisi scientifica dei dati
services: machine-learning
documentationcenter: 
author: hning86
manager: jhubbard
editor: cgronlun
ms.assetid: a9001cc2-3aa0-47e1-b175-1f76408ba1d1
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2016
ms.author: garye;haining
translationtype: Human Translation
ms.sourcegitcommit: 247d370c1f80729856e53690045991127ad54351
ms.openlocfilehash: ee3255493760917b2a96facfabe17120764cb638
ms.lasthandoff: 03/02/2017


---
# <a name="powershell-module-for-microsoft-azure-machine-learning"></a>Modulo PowerShell per Microsoft Azure Machine Learning
Il modulo PowerShell per Azure Machine Learning è uno strumento avanzato che consente di usare Windows PowerShell per gestire aree di lavoro, esperimenti, set di dati classici, servizi Web e altro.

È possibile visualizzare la documentazione e scaricare il modulo, con il codice sorgente completo, all'indirizzo [https://aka.ms/amlps](https://aka.ms/amlps). 

> [!NOTE]
> Il modulo PowerShell di Azure Machine Learning è attualmente in modalità di anteprima. Il modulo verrà continuamente migliorato ed esteso durante questo periodo di anteprima. Monitorare i [blog di Cortana Intelligence e di Machine Learning](https://blogs.technet.microsoft.com/machinelearning/) per altre informazioni e notizie aggiornate.

## <a name="what-is-the-machine-learning-powershell-module"></a>Cos'è il modulo PowerShell per Machine Learning?
Il modulo PowerShell per Machine Learning è un modulo DLL basato su .NET che consente di gestire completamente aree di lavoro, esperimenti, set di dati, servizi Web classici ed endpoint di servizio Web classici di Azure Machine Learning da Windows PowerShell. 

Con il modulo, è possibile scaricare il codice sorgente completo che include un [livello API C#](https://github.com/hning86/azuremlps/blob/master/code/AzureMLSDK.cs) del tutto separato. È possibile fare riferimento a questa DLL dal proprio progetto .NET e gestire Azure Machine Learning tramite il codice .NET. La DLL dipende anche dalle API REST sottostanti che è possibile usare direttamente dal client preferito.

## <a name="what-can-i-do-with-the-powershell-module"></a>Quali operazioni è possibile eseguire con il modulo PowerShell?
Ecco alcune delle attività che è possibile eseguire con questo modulo PowerShell. Vedere la [documentazione completa](https://aka.ms/amlps) per queste e altre funzioni.

* Effettuare il provisioning di una nuova area di lavoro usando un certificato di gestione ([New-AmlWorkspace](https://github.com/hning86/azuremlps#new-amlworkspace))
* Esportare e importare un file JSON che rappresenta un grafico di un esperimento ([Export-AmlExperimentGraph](https://github.com/hning86/azuremlps#export-amlexperimentgraph) e [Import-AmlExperimentGraph](https://github.com/hning86/azuremlps#import-amlexperimentgraph))
* Eseguire un esperimento ([Start-AmlExperiment](https://github.com/hning86/azuremlps#start-amlexperiment))
* Creare un servizio Web al di fuori di un esperimento predittivo ([New-AmlWebService](https://github.com/hning86/azuremlps#new-amlwebservice))
* Creare un endpoint in un servizio Web pubblicato ([Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint))
* Richiamare un endpoint di servizio Web RRS e/o BES ([Invoke-AmlWebServiceRRSEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) e [Invoke-AmlWebServicBESEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint))

Ecco un rapido esempio dell'uso di PowerShell per eseguire un esperimento esistente:

        #Find the first Experiment named “xyz”
        $exp = (Get-AmlExperiment | where Description -eq ‘xyz’)[0]
        #Run the Experiment
        Start-AmlExperiment -ExperimentId $exp.ExperimentId 

Per un caso d'uso più approfondito, vedere questo articolo sull'uso del modulo PowerShell per automatizzare un'attività richiesta di frequente: [Creare più modelli di Machine Learning ed endpoint di servizio Web da un esperimento usando PowerShell](machine-learning-create-models-and-endpoints-with-powershell.md).

## <a name="how-do-i-get-started"></a>Come iniziare?
Per iniziare a usare PowerShell per Machine Learning, scaricare il [pacchetto della versione](https://github.com/hning86/azuremlps/releases) da GitHub e seguire le [istruzioni per l'installazione](https://github.com/hning86/azuremlps/blob/master/README.md). Le istruzioni illustrano come sbloccare la DLL scaricata/decompressa e quindi importarla nell'ambiente di PowerShell. La maggior parte dei cmdlet richiede di specificare l'ID area di lavoro, il token di autorizzazione per l'area di lavoro e l'area di Azure in cui si trova l'area di lavoro. Il modo più semplice per specificare i valori è tramite un file config.json predefinito. Le istruzioni illustrano anche come configurare questo file. 

Se si desidera, è possibile clonare l'albero GIT, modificare il codice e compilarlo in locale utilizzando Visual Studio.

## <a name="next-steps"></a>Passaggi successivi
La documentazione completa per il modulo PowerShell è disponibile in [https://aka.ms/amlps](https://aka.ms/amlps). 

Per un esempio esteso di come usare il modulo in uno scenario reale, consultare il caso d'uso approfondito, [Creare più modelli di Machine Learning ed endpoint di servizio Web da un esperimento usando PowerShell](machine-learning-create-models-and-endpoints-with-powershell.md).

