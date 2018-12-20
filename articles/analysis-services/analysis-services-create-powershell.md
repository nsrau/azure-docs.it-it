---
title: 'Avvio rapido: creare un server di Azure Analysis Services tramite PowerShell | Microsoft Docs'
description: Informazioni su come creare un server di Azure Analysis Services tramite PowerShell
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 10/18/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 3971311bff90d0b005acd516336e13766028cccf
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2018
ms.locfileid: "53001565"
---
# <a name="quickstart-create-a-server---powershell"></a>Avvio rapido: creare un server: PowerShell

Questa guida introduttiva descrive l'uso di PowerShell dalla riga di comando, per la creazione di un server di Azure Analysis Services nella sottoscrizione di Azure.

## <a name="prerequisites"></a>Prerequisiti

- **Sottoscrizione di Azure**: visitare la pagina [Versione di prova gratuita di Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) per creare un account.
- **Azure Active Directory**: la sottoscrizione deve essere associata a un tenant Azure Active Directory ed è necessario avere un account in tale directory. Per altre informazioni, vedere [Autenticazione e autorizzazioni utente](analysis-services-manage-users.md).
- **Modulo Azure PowerShell 4.0 o versioni successive**. Per trovare la versione, eseguire ` Get-Module -ListAvailable AzureRM`. Per eseguire l'installazione o l'aggiornamento, vedere [Installare il modulo di Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="import-azurermanalysisservices-module"></a>Importare il modulo AzureRm.AnalysisServices

Per creare un server nella sottoscrizione, usare il modulo [AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices) del componente. Caricare il modulo AzureRm.AnalysisServices nella sessione di PowerShell.

```powershell
Import-Module AzureRM.AnalysisServices
```

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere alla sottoscrizione di Azure usando il comando [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount). Seguire le istruzioni visualizzate sullo schermo.

```powershell
Connect-AzureRmAccount
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md) è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite come gruppo. Quando si crea il server, è necessario specificare un gruppo di risorse nella sottoscrizione. Se non si ha già un gruppo di risorse, è possibile crearne uno nuovo tramite il comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). L'esempio seguente crea un gruppo di risorse denominato `myResourceGroup` nell'area Stati Uniti occidentali.

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "WestUS"
```

## <a name="create-a-server"></a>Creare un server

Creare un nuovo server usando il comando [New-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver). L'esempio seguente crea un server denominato myServer in myResourceGroup nell'area Stati Uniti occidentali al livello D1 (gratuito) e specifica philipc@adventureworks.com come amministratore del server.

```powershell
New-AzureRmAnalysisServicesServer -ResourceGroupName "myResourceGroup" -Name "myserver" -Location WestUS -Sku D1 -Administrator "philipc@adventure-works.com"
```

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile rimuovere il server dalla sottoscrizione usando il comando [Remove-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver). Se si prevede di passare ad altre esercitazioni introduttive ed esercitazioni in questa raccolta, non rimuovere il server. L'esempio seguente rimuove il server creato nel passaggio precedente.


```powershell
Remove-AzureRmAnalysisServicesServer -Name "myserver" -ResourceGroupName "myResourceGroup"
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva si è appreso come creare un server nella sottoscrizione di Azure utilizzando PowerShell. Una volta creato il server, è consigliabile garantirne la sicurezza configurando un firewall (facoltativo). È inoltre possibile aggiungere un modello di dati di esempio al server, direttamente dal portale. Un modello di esempio è utile per avere informazioni sulla configurazione dei ruoli del database modello e sul test delle connessioni client. Per altre informazioni, continuare con l'esercitazione per l'aggiunta di un modello di esempio.

> [!div class="nextstepaction"]
> [Avvio rapido: configurare il firewall del server - Portale](analysis-services-qs-firewall.md)      
> [!div class="nextstepaction"]
> [Esercitazione: aggiungere un modello di esempio al server](analysis-services-create-sample-model.md)