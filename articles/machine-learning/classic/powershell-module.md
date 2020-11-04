---
title: 'ML Studio (classico): moduli di PowerShell-Azure'
description: Usare PowerShell per creare e gestire aree di lavoro Azure Machine Learning Studio (classiche), esperimenti, servizi Web e altro ancora.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 04/25/2019
ms.openlocfilehash: f66363ec8b64dea1a076f81f4fc89bafe5ca4151
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322332"
---
# <a name="powershell-modules-for-azure-machine-learning-studio-classic"></a>Moduli di PowerShell per Azure Machine Learning Studio (versione classica)

**SI APPLICA A:**  ![Si applica a.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (versione classica) ![Non si applica a. ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


Usando i moduli di PowerShell, è possibile gestire a livello di codice le risorse di studio (classiche) e gli asset, ad esempio aree di lavoro, set di impostazioni e servizi Web.

È possibile interagire con le risorse di studio (classiche) usando tre moduli di PowerShell:

* [Azure PowerShell Az](#az-rm) rilasciato nel 2018, include tutte le funzionalità di AzureRM, anche se con nomi di cmdlet diversi
* [AzureRM](#az-rm) rilasciato in 2016, sostituito da PowerShell AZ
* [Modulo classico di PowerShell per Azure Machine Learning](#classic) rilasciato nel 2016

Anche se questi moduli di PowerShell presentano alcune analogie, ciascuna è progettata per scenari specifici. Questo articolo descrive le differenze tra i moduli di PowerShell e offre indicazioni per la scelta del modulo più adatto alle proprie esigenze.  

Per visualizzare le risorse supportate da ogni modulo, controllare la [tabella relativa al supporto](#support-table) seguente. 

## <a name="azure-powershell-az-and-azurerm"></a><a name="az-rm"></a> Azure PowerShell Az e AzureRM

Az è ora il modulo previsto per PowerShell per l'interazione con Azure e include tutte le funzionalità precedenti di AzureRM. AzureRM continuerà a ricevere correzioni di bug, ma non riceverà nuovi cmdlet o funzioni.  Az e AzureRM gestiscono soluzioni distribuite usando il modello di distribuzione **Azure Resource Manager**. Queste risorse includono le aree di lavoro di studio (classiche) e i nuovi servizi Web di studio (classico). 

È possibile installare PowerShell classico insieme a AZ o AzureRM per coprire sia i tipi di risorse "nuovi" che quelli "classici". Non è tuttavia consigliabile che Az e AzureRM siano installati nello stesso momento. Per semplificare la scelta, tenere presente che è preferibile usare Az per tutte le distribuzioni future.  Per altre informazioni, vedere AZ versus AzureRM e il percorso di migrazione in [Introduzione al Azure PowerShell AZ](/powershell/azure/new-azureps-module-az).

Per iniziare a usare Az, seguire le [istruzioni di installazione per Azure Az](/powershell/azure/install-az-ps).

## <a name="powershell-classic"></a><a name="classic"></a> PowerShell classico

Il [modulo classico di PowerShell](https://aka.ms/amlps) per studio (classico) consente di gestire le risorse distribuite con il **modello di distribuzione classica**. Queste risorse includono risorse utente Studio (classiche), servizi Web classici e endpoint di servizio Web "classici".

Tuttavia, Microsoft consiglia di utilizzare il modello di distribuzione Gestione risorse per tutte le risorse future per semplificare la distribuzione e la gestione delle risorse. Per altre informazioni sui modelli di distribuzione, vedere l'articolo [Distribuzione Azure Resource Manager o classica](../../azure-resource-manager/management/deployment-models.md).

Per iniziare a usare il modulo classico di PowerShell, scaricare il [pacchetto della versione](https://github.com/hning86/azuremlps/releases) da GitHub e seguire le [istruzioni per l'installazione](https://github.com/hning86/azuremlps/blob/master/README.md). Le istruzioni illustrano come sbloccare la DLL scaricata/decompressa e quindi importarla nell'ambiente di PowerShell.

È possibile installare PowerShell classico insieme a AZ o AzureRM per coprire sia i tipi di risorse "nuovi" che quelli "classici".

## <a name="powershell-support-table"></a><a name="support-table"></a> Tabella relativa al supporto di PowerShell


| Attività | **AZ** |  **PowerShell classico** |
| --- | --- | --- |
| Creare/eliminare aree di lavoro | [Modelli di Gestione risorse](./deploy-with-resource-manager-template.md) |  |
| Gestire i piani di impegno dell'area di lavoro | [New-AzMlCommitmentPlan](/powershell/module/az.machinelearning/new-azmlcommitmentplan) | |
| Gestire gli utenti dell'area di lavoro |  | [Add-AmlWorkspaceUsers](https://github.com/hning86/azuremlps#add-amlworkspaceusers)|
| Gestire i servizi Web | [New-AzMlWebService](/powershell/module/az.machinelearning/new-azmlwebservice) <br>(nuovi servizi Web)| [New-AmlWebService](https://github.com/hning86/azuremlps#manage-classic-web-service) <br>(servizi Web "classici") |
| Gestisci endpoint/chiavi servizio Web |  [Get-AzMlWebServiceKey](/powershell/module/az.machinelearning/get-azmlwebservicekey)|  [Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#manage-classic-web-servcie-endpoint)|
| Gestire i set di impostazioni utente/modelli sottoposti a training| | [Get-AmlDataset](https://github.com/hning86/azuremlps#manage-user-assets-dataset-trained-model-transform) |
| Gestire gli esperimenti utente |  | [Start-AmlExperiment](https://github.com/hning86/azuremlps#manage-experiment) |
| Gestire moduli personalizzati | | [New-AmlCustomModule](https://github.com/hning86/azuremlps#manage-custom-module) |


## <a name="next-steps"></a>Passaggi successivi
Consultare la documentazione completa di questi moduli di PowerShell:
* [PowerShell classico](https://aka.ms/amlps)
* [Azure PowerShell Az](/powershell/module/az.machinelearning/#machine_learning)