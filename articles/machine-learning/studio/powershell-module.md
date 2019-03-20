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
ms.date: 01/25/2019
ms.openlocfilehash: bd3a82f326cdf7f51e8842e45333ff2bd647c260
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58092753"
---
# <a name="powershell-modules-for-azure-machine-learning-studio"></a>Moduli di PowerShell per Azure Machine Learning Studio

Con i moduli di PowerShell, è possibile gestire a livello di codice le risorse e gli asset di Studio, ad esempio le aree di lavoro, i set di dati e i servizi Web.

È possibile interagire con le risorse di Studio usando tre moduli di PowerShell:

* [Azure PowerShell Az](#az-rm) rilasciato nel 2018, include tutte le funzionalità di AzureRM, anche se con nomi di cmdlet diversi
* [AzureRM](#az-rm) rilasciato nel 2016
* [Modulo classico di PowerShell per Azure Machine Learning](#classic) rilasciato nel 2016

Nonostante alcune analogie, ogni modulo è progettato per determinati scenari. Questo articolo descrive le differenze tra i moduli di PowerShell e offre indicazioni per la scelta del modulo più adatto alle proprie esigenze.

## <a name="choosing-modules"></a> Scelta dei moduli

La scelta tra i moduli di PowerShell disponibili dipende dal tipo di risorse gestite.

Per visualizzare le risorse supportate da ogni modulo, controllare la [tabella relativa al supporto](#support-table) seguente. Poiché il modulo classico di PowerShell può essere installato in parallelo con il modulo Az o AzureRM, è possibile installare due moduli e garantire quindi la copertura per tutti i tipi di risorse (modulo classico con Az o modulo classico con AzureRM)

Non è tuttavia consigliabile che Az e AzureRM siano installati nello stesso momento. Per semplificare la scelta, tenere presente che è preferibile usare Az per tutte le distribuzioni future. Usare AzureRM solo se nell'ambiente in uso sono presenti condizioni particolari che lo richiedono.

Per altre informazioni sulle differenze tra Az e AzureRM, nonché sul percorso di migrazione fornito, vedere [Introduzione al modulo AZ di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).

## <a name="az-rm"></a> Azure PowerShell Az e AzureRM

Az e AzureRM gestiscono soluzioni distribuite usando il modello di distribuzione **Azure Resource Manager**. Queste risorse includono le aree di lavoro e i nuovi servizi Web di Studio. Per gestire le risorse distribuite tramite il modello di distribuzione classica, è necessario usare il modulo classico di PowerShell. Per altre informazioni sui modelli di distribuzione, vedere l'articolo [Distribuzione Azure Resource Manager o classica](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).

Az è ora il modulo previsto per PowerShell per l'interazione con Azure e include tutte le funzionalità precedenti di AzureRM. AzureRM continuerà a ricevere correzioni di bug, ma non riceverà nuovi cmdlet o funzioni. Anche se è presente un percorso di aggiornamento da AzureRM, se si verificano problemi con Az quando si utilizza Studio, segnalare il problema e passare ad AzureRM.

Per iniziare a usare Az, seguire le [istruzioni di installazione per Azure Az](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="classic"></a> Modulo classico di PowerShell

Il [modulo classico di PowerShell](https://aka.ms/amlps) per Studio consente di gestire le risorse distribuite tramite il **modello di distribuzione classica**. Queste risorse includono gli asset utente di Studio, i servizi Web classici e gli endpoint classici del servizio Web.

Per semplificare la distribuzione e la gestione delle risorse, è tuttavia consigliabile usare il modello di distribuzione Resource Manager per tutte le nuove risorse. Per altre informazioni sui modelli di distribuzione, vedere l'articolo [Distribuzione Azure Resource Manager o classica](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).

Per iniziare a usare il modulo classico di PowerShell, scaricare il [pacchetto della versione](https://github.com/hning86/azuremlps/releases) da GitHub e seguire le [istruzioni per l'installazione](https://github.com/hning86/azuremlps/blob/master/README.md). Le istruzioni illustrano come sbloccare la DLL scaricata/decompressa e quindi importarla nell'ambiente di PowerShell.

## <a name="support-table"></a> Tabella relativa al supporto di PowerShell

 **Aree di lavoro di Studio** | **Modulo Az** |  **Modulo AzureRM** | **PowerShell - Classica** |
| --- | --- | --- | --- |
| Creare o eliminare aree di lavoro | [Modelli di Resource Manager](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) | [Modelli di Resource Manager](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) |  |
| Gestire gli utenti dell'area di lavoro |  |  | [Add-AmlWorkspaceUsers](https://github.com/hning86/azuremlps#add-amlworkspaceusers)|
| Gestire i piani di impegno | [New-AzMlCommitmentPlan](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlcommitmentplan) | [New-AzureRmMlCommitmentPlan](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/new-azurermmlcommitmentplan) |
|||
| **Servizi Web** | **Modulo Az** | **Modulo AzureRM** | **PowerShell - Classica** |
| Gestire i servizi Web | [New-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlwebservice) <br> (Nuovi servizi Web) | [New-AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/new-azurermmlwebservice) <br> (Nuovi servizi Web) |[New-AmlWebService](https://github.com/hning86/azuremlps#manage-classic-web-service) <br> (Servizi Web classici) |
| Gestire endpoint o chiavi |  [Get-AzMlWebServiceKeys](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservicekeys) <br> (Nuovi servizi Web) | [Get-AzureRmMlWebServiceKeys](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/get-azurermmlwebservicekeys) <br> (Nuovi servizi Web) | [Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#manage-classic-web-servcie-endpoint) <br> (Servizi Web classici) |
|||
| **Risorse utente** | **Modulo Az** | **Modulo AzureRM** | **PowerShell - Classica** |
| Gestire set di dati o modelli con training |  |  | [Get-AmlDataset](https://github.com/hning86/azuremlps#manage-user-assets-dataset-trained-model-transform) |
| Gestire esperimenti |  |  | [Start-AmlExperiment](https://github.com/hning86/azuremlps#manage-experiment) |
| Gestire moduli personalizzati |  |  | [New-AmlCustomModule](https://github.com/hning86/azuremlps#manage-custom-module) |


## <a name="next-steps"></a>Passaggi successivi
Per la documentazione completa relativa ai moduli di PowerShell, controllare i collegamenti seguenti:
* [Modulo AzureRM](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/#machine_learning)
* [PowerShell - Classica](https://aka.ms/amlps)
* [Modulo Azure PowerShell Az](https://docs.microsoft.com/powershell/module/az.machinelearning/#machine_learning)
