---
title: Creare un server di Azure Analysis Services tramite PowerShell | Microsoft Docs
description: Informazioni su come creare un server di Azure Analysis Services tramite PowerShell
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
ms.assetid: 
ms.service: analysis-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 08/01/2017
ms.author: owend
ms.custom: mvc
ms.openlocfilehash: cb42fd3ed51364cf478848cc51ebbb2f175e96d2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-analysis-services-server-by-using-powershell"></a>Creare un server di Azure Analysis Services tramite PowerShell

Questa esercitazione introduttiva illustra l'uso di PowerShell dalla riga di comando per la creazione di un server di Azure Analysis Services in un [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md) nella sottoscrizione di Azure.

Questa attività richiede il modulo Azure PowerShell 4.0 o versioni successive. Per trovare la versione, eseguire ` Get-Module -ListAvailable AzureRM`. Per eseguire l'installazione o l'aggiornamento, vedere [Installare il modulo di Azure PowerShell](/powershell/azure/install-azurerm-ps). 

> [!NOTE]
> La creazione di un server può avere come risultato un nuovo servizio fatturabile. Per altre informazioni, vedere [Azure Analysis Services Prezzi](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="prerequisites"></a>Prerequisiti
Per completare l'esercitazione introduttiva, sono necessari gli elementi seguenti:

* **Sottoscrizione di Azure**: visitare la pagina [Versione di valutazione gratuita di Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) per creare un account.
* **Azure Active Directory**: la sottoscrizione deve essere associata a un tenant Azure Active Directory ed è necessario avere un account in tale directory. Per altre informazioni, vedere [Autenticazione e autorizzazioni utente](analysis-services-manage-users.md).

## <a name="import-azurermanalysisservices-module"></a>Importare il modulo AzureRm.AnalysisServices
Per creare un server nella sottoscrizione, usare il modulo [AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices) del componente. Caricare il modulo AzureRm.AnalysisServices nella sessione di PowerShell.

```powershell
Import-Module AzureRM.AnalysisServices
```

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere alla sottoscrizione di Azure usando il comando [Add-AzureRmAccount](/powershell/module/azurerm.profile/add-azurermaccount). Seguire le istruzioni visualizzate sullo schermo.

```powershell
Add-AzureRmAccount
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse
 
Un [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md) è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite come gruppo. Quando si crea il server, è necessario specificare un gruppo di risorse nella sottoscrizione. Se non si ha già un gruppo di risorse, è possibile crearne uno nuovo tramite il comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). L'esempio seguente crea un gruppo di risorse denominato `myResourceGroup` nell'area Stati Uniti occidentali.

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "West US"
```

## <a name="create-a-server"></a>Creare un server

Creare un nuovo server usando il comando [New-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver). L'esempio seguente crea un server denominato myServer in myResourceGroup nell'area Stati Uniti occidentali al livello D1 e specifica philipc@adventureworks.com come amministratore del server.

```powershell
New-AzureRmAnalysisServicesServer -ResourceGroupName "myResourceGroup" -Name "myServer" -Location West US -Sku D1 -Administrator "philipc@adventure-works.com"
```

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile rimuovere il server dalla sottoscrizione usando il comando [Remove-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver). Se si prevede di passare ad altre esercitazioni introduttive ed esercitazioni in questa raccolta, non rimuovere il server. L'esempio seguente rimuove il server creato nel passaggio precedente.


```powershell
Remove-AzureRmAnalysisServicesServer -Name "myServer" -ResourceGroupName "myResourceGroup"
```

## <a name="next-steps"></a>Passaggi successivi
[Gestire Azure Analysis Services con PowerShell](analysis-services-powershell.md)   
[Distribuire un modello da SSDT](analysis-services-deploy.md)   
[Creare un modello nel portale di Azure](analysis-services-create-model-portal.md)