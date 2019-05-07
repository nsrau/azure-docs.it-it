---
title: Moduli di PowerShell per Azure Machine Learning Studio
titleSuffix: Azure Machine Learning Studio
description: Usare PowerShell per creare e gestire aree di lavoro, esperimenti, servizi Web e altro ancora in Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.date: 04/25/2019
ms.openlocfilehash: bee42f8a9582908963c0eef95a2fd04742cd425e
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65205669"
---
# <a name="powershell-modules-for-azure-machine-learning-studio"></a>Moduli di PowerShell per Azure Machine Learning Studio

Con i moduli di PowerShell, è possibile gestire a livello di codice le risorse e gli asset di Studio, ad esempio le aree di lavoro, i set di dati e i servizi Web.

È possibile interagire con le risorse di Studio usando tre moduli di PowerShell:

* [Azure PowerShell Az](#az-rm) rilasciato nel 2018, include tutte le funzionalità di AzureRM, anche se con nomi di cmdlet diversi
* [AzureRM](#az-rm) rilasciato nel 2016, sostituito da PowerShell Az
* [Modulo classico di PowerShell per Azure Machine Learning](#classic) rilasciato nel 2016

Anche se questi moduli di PowerShell hanno alcune analogie, ognuno è progettato per determinati scenari. Questo articolo descrive le differenze tra i moduli di PowerShell e offre indicazioni per la scelta del modulo più adatto alle proprie esigenze.  

Per visualizzare le risorse supportate da ogni modulo, controllare la [tabella relativa al supporto](#support-table) seguente. 

## <a name="az-rm"></a> Azure PowerShell Az e AzureRM

Az è ora il modulo previsto per PowerShell per l'interazione con Azure e include tutte le funzionalità precedenti di AzureRM. AzureRM continuerà a ricevere correzioni di bug, ma non riceverà nuovi cmdlet o funzioni.  Az e AzureRM gestiscono soluzioni distribuite usando il modello di distribuzione **Azure Resource Manager**. Queste risorse includono le aree di lavoro di Studio e i "Nuovi" servizi web di Studio. 

PowerShell-classica può essere installato insieme a Az o AzureRM per coprire entrambi i tipi di risorsa "nuovo" e "classico". Non è tuttavia consigliabile che Az e AzureRM siano installati nello stesso momento. Per semplificare la scelta, tenere presente che è preferibile usare Az per tutte le distribuzioni future.  Altre informazioni su Az e il percorso di migrazione in AzureRM [Introduzione ad Azure PowerShell Az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).

Per iniziare a usare Az, seguire le [istruzioni di installazione per Azure Az](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="classic"></a> Modulo classico di PowerShell

Il [modulo classico di PowerShell](https://aka.ms/amlps) per Studio consente di gestire le risorse distribuite tramite il **modello di distribuzione classica**. Queste risorse includono gli asset utente Studio, servizi web "classici" e gli endpoint servizio web "classico".

Tuttavia, Microsoft consiglia di utilizzare il modello di distribuzione Resource Manager per tutte le risorse future per semplificare la distribuzione e la gestione delle risorse. Per altre informazioni sui modelli di distribuzione, vedere l'articolo [Distribuzione Azure Resource Manager o classica](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).

Per iniziare a usare il modulo classico di PowerShell, scaricare il [pacchetto della versione](https://github.com/hning86/azuremlps/releases) da GitHub e seguire le [istruzioni per l'installazione](https://github.com/hning86/azuremlps/blob/master/README.md). Le istruzioni illustrano come sbloccare la DLL scaricata/decompressa e quindi importarla nell'ambiente di PowerShell.

PowerShell-classica può essere installato insieme a Az o AzureRM per coprire entrambi i tipi di risorsa "nuovo" e "classico".

## <a name="support-table"></a> Tabella relativa al supporto di PowerShell


| | **Modulo Az** |  **PowerShell - Classica** |
| --- | --- | --- |
| Creare o eliminare le aree di lavoro | [Modelli di Resource Manager](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) |  |
| Gestire i piani di impegno dell'area di lavoro | [New-AzMlCommitmentPlan](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlcommitmentplan) | |
| Gestire gli utenti dell'area di lavoro |  | [Add-AmlWorkspaceUsers](https://github.com/hning86/azuremlps#add-amlworkspaceusers)|
| Gestire i servizi Web | [New-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlwebservice) <br>("new" servizi web)|| [New-AmlWebService](https://github.com/hning86/azuremlps#manage-classic-web-service) <br>(servizi web "classico") |
| Gestire gli endpoint servizio web/chiavi |  [Get-AzMlWebServiceKey](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservicekey)|  [Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#manage-classic-web-servcie-endpoint)|
| Gestire i modelli di training/set di dati utente| | [Get-AmlDataset](https://github.com/hning86/azuremlps#manage-user-assets-dataset-trained-model-transform) |
| Gestire esperimenti di utente |  | [Start-AmlExperiment](https://github.com/hning86/azuremlps#manage-experiment) |
| Gestire moduli personalizzati | | [New-AmlCustomModule](https://github.com/hning86/azuremlps#manage-custom-module) |


## <a name="next-steps"></a>Passaggi successivi
Consultare la documentazione completa questi modulo di PowerShell:
* [PowerShell - Classica](https://aka.ms/amlps)
* [Modulo Azure PowerShell Az](https://docs.microsoft.com/powershell/module/az.machinelearning/#machine_learning)
