---
title: Moduli di PowerShell
titleSuffix: ML Studio (classic) - Azure
description: Usare PowerShell per creare e gestire aree di lavoro, esperimenti, servizi Web e altro ancora di Azure Machine Learning Studio (classici).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 04/25/2019
ms.openlocfilehash: 6afd222730a9864e0b8edd681b1ce919b03c3be8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204291"
---
# <a name="powershell-modules-for-azure-machine-learning-studio-classic"></a>Moduli di PowerShell per Azure Machine Learning Studio (classico)PowerShell modules for Azure Machine Learning Studio (classic)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Usando i moduli di PowerShell, è possibile gestire a livello di codice le risorse e le risorse di Studio (classiche), ad esempio aree di lavoro, set di dati e servizi Web.Using PowerShell modules, you can programmatically manage your Studio (classic) resources and assets such as workspaces, datasets, and web services.

È possibile interagire con le risorse di Studio (classiche) usando tre moduli di PowerShell:You can interact with Studio (classic) resources using three Powershell modules:

* [Azure PowerShell Az](#az-rm) rilasciato nel 2018, include tutte le funzionalità di AzureRM, anche se con nomi di cmdlet diversi
* [AzureRM](#az-rm) rilasciato nel 2016, sostituito da PowerShell AzAzureRM released in 2016, replaced by PowerShell Az
* [Modulo classico di PowerShell per Azure Machine Learning](#classic) rilasciato nel 2016

Anche se questi moduli di PowerShell presentano alcune somiglianze, ognuno è progettato per scenari specifici. Questo articolo descrive le differenze tra i moduli di PowerShell e offre indicazioni per la scelta del modulo più adatto alle proprie esigenze.  

Per visualizzare le risorse supportate da ogni modulo, controllare la [tabella relativa al supporto](#support-table) seguente. 

## <a name="azure-powershell-az-and-azurerm"></a><a name="az-rm"></a> Azure PowerShell Az e AzureRM

Az è ora il modulo previsto per PowerShell per l'interazione con Azure e include tutte le funzionalità precedenti di AzureRM. AzureRM continuerà a ricevere correzioni di bug, ma non riceverà nuovi cmdlet o funzioni.  Az e AzureRM gestiscono soluzioni distribuite usando il modello di distribuzione **Azure Resource Manager**. Queste risorse includono le aree di lavoro di Studio (classica) e i servizi Web "Nuovi" di Studio (versione classica). 

PowerShell classic può essere installato insieme ad Az o AzureRM per coprire i tipi di risorse "nuovi" e "classici". Non è tuttavia consigliabile che Az e AzureRM siano installati nello stesso momento. Per semplificare la scelta, tenere presente che è preferibile usare Az per tutte le distribuzioni future.  Altre informazioni su Az e AzureRM e sul percorso di migrazione sono introduttive in [Azure PowerShell Az.](https://docs.microsoft.com/powershell/azure/new-azureps-module-az)

Per iniziare a usare Az, seguire le [istruzioni di installazione per Azure Az](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="powershell-classic"></a><a name="classic"></a>Versione classica di PowerShell

Il [modulo classico](https://aka.ms/amlps) di PowerShell di Studio (classico) consente di gestire le risorse distribuite utilizzando il modello di **distribuzione classica.** Queste risorse includono risorse utente di Studio (classiche), servizi Web "classici" ed endpoint di servizi Web "classici".

Tuttavia, Microsoft consiglia di utilizzare il modello di distribuzione di Resource Manager per tutte le risorse future per semplificare la distribuzione e la gestione delle risorse. Per altre informazioni sui modelli di distribuzione, vedere l'articolo [Distribuzione Azure Resource Manager o classica](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).

Per iniziare a usare il modulo classico di PowerShell, scaricare il [pacchetto della versione](https://github.com/hning86/azuremlps/releases) da GitHub e seguire le [istruzioni per l'installazione](https://github.com/hning86/azuremlps/blob/master/README.md). Le istruzioni illustrano come sbloccare la DLL scaricata/decompressa e quindi importarla nell'ambiente di PowerShell.

PowerShell classic può essere installato insieme ad Az o AzureRM per coprire i tipi di risorse "nuovi" e "classici".

## <a name="powershell-support-table"></a><a name="support-table"></a> Tabella relativa al supporto di PowerShell


| | **Modulo Az** |  **PowerShell - Classica** |
| --- | --- | --- |
| Creare/eliminare aree di lavoro | [Modelli di Gestione risorse](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) |  |
| Gestire i piani di impegno dell'area di lavoro | [New-AzMlCommitmentPlan](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlcommitmentplan) | |
| Gestire gli utenti dell'area di lavoro |  | [Add-AmlWorkspaceUsers](https://github.com/hning86/azuremlps#add-amlworkspaceusers)|
| Gestire i servizi Web | [New-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlwebservice) <br>("nuovi" servizi web)|| [New-AmlWebService](https://github.com/hning86/azuremlps#manage-classic-web-service) <br>(servizi web classici") |
| Gestire gli endpoint/chiavi del servizio WebManage web service endpoints/keys |  [Get-AzMlWebServiceKey](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservicekey)|  [Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#manage-classic-web-servcie-endpoint)|
| Gestire set di dati utente/modelli addestratiManage user datasets/trained models| | [Get-AmlDataset](https://github.com/hning86/azuremlps#manage-user-assets-dataset-trained-model-transform) |
| Gestire gli esperimenti degli utenti |  | [Start-AmlExperiment](https://github.com/hning86/azuremlps#manage-experiment) |
| Gestire moduli personalizzati | | [New-AmlCustomModule](https://github.com/hning86/azuremlps#manage-custom-module) |


## <a name="next-steps"></a>Passaggi successivi
Consultare la documentazione completa del modulo PowerShell seguenti:Consult the full documentation these PowerShell module:
* [PowerShell - Classica](https://aka.ms/amlps)
* [Modulo Azure PowerShell Az](https://docs.microsoft.com/powershell/module/az.machinelearning/#machine_learning)
