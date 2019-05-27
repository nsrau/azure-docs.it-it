---
title: 'Avvio rapido: creare un server di Azure Analysis Services tramite PowerShell | Microsoft Docs'
description: Informazioni su come creare un server di Azure Analysis Services tramite PowerShell
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b3b23509197dd1d91dbd74c57b3c732151c51a97
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66142978"
---
# <a name="quickstart-create-a-server---powershell"></a>Avvio rapido: creare un server: PowerShell

Questa guida introduttiva descrive l'uso di PowerShell dalla riga di comando, per la creazione di un server di Azure Analysis Services nella sottoscrizione di Azure.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Sottoscrizione di Azure**: visitare la pagina [Versione di prova gratuita di Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) per creare un account.
- **Azure Active Directory**: la sottoscrizione deve essere associata a un tenant Azure Active Directory ed è necessario avere un account in tale directory. Per altre informazioni, vedere [Autenticazione e autorizzazioni utente](analysis-services-manage-users.md).
- **Azure PowerShell**. Per trovare la versione installata, eseguire `Get-Module -ListAvailable Az`. Per eseguire l'installazione o l'aggiornamento, vedere [Installare il modulo di Azure PowerShell](/powershell/azure/install-Az-ps).

## <a name="import-azanalysisservices-module"></a>Importare il modulo Az.AnalysisServices

Per creare un server nella sottoscrizione, usare il modulo [Az.AnalysisServices](/powershell/module/az.analysisservices). Caricare il moduloAz.AnalysisServices nella sessione di PowerShell.

```powershell
Import-Module Az.AnalysisServices
```

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere alla sottoscrizione di Azure usando il comando [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Seguire le istruzioni visualizzate sullo schermo.

```powershell
Connect-AzAccount
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md) è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite come gruppo. Quando si crea il server, è necessario specificare un gruppo di risorse nella sottoscrizione. Se non si ha già un gruppo di risorse, è possibile crearne uno nuovo usando il comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). L'esempio seguente crea un gruppo di risorse denominato `myResourceGroup` nell'area Stati Uniti occidentali.

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "WestUS"
```

## <a name="create-a-server"></a>Creare un server

Creare un nuovo server usando il comando [New-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver). L'esempio seguente crea un server denominato myServer in myResourceGroup nell'area Stati Uniti occidentali al livello D1 (gratuito) e specifica philipc@adventureworks.com come amministratore del server.

```powershell
New-AzAnalysisServicesServer -ResourceGroupName "myResourceGroup" -Name "myserver" -Location WestUS -Sku D1 -Administrator "philipc@adventure-works.com"
```

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile rimuovere il server dalla sottoscrizione usando il comando [Remove-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver). Se si prevede di passare ad altre esercitazioni introduttive ed esercitazioni in questa raccolta, non rimuovere il server. L'esempio seguente rimuove il server creato nel passaggio precedente.


```powershell
Remove-AzAnalysisServicesServer -Name "myserver" -ResourceGroupName "myResourceGroup"
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva si è appreso come creare un server nella sottoscrizione di Azure utilizzando PowerShell. Una volta creato il server, è consigliabile garantirne la sicurezza configurando un firewall (facoltativo). È inoltre possibile aggiungere un modello di dati di esempio al server, direttamente dal portale. Un modello di esempio è utile per avere informazioni sulla configurazione dei ruoli del database modello e sul test delle connessioni client. Per altre informazioni, continuare con l'esercitazione per l'aggiunta di un modello di esempio.

> [!div class="nextstepaction"]
> [Avvio rapido: configurare il firewall del server - Portale](analysis-services-qs-firewall.md)      
> [!div class="nextstepaction"]
> [Esercitazione: aggiungere un modello di esempio al server](analysis-services-create-sample-model.md)
