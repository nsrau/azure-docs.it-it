---
author: MikeRayMSFT
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 11/25/2018
ms.author: mikeray
ms.openlocfilehash: e81cdb478a63e1e584aef2c32754bd321d245365
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2018
ms.locfileid: "52440238"
---
## <a name="start-your-powershell-session"></a>Avviare la sessione di PowerShell
Prima di tutto è necessario che sia installata ed eseguita la versione più recente di [Azure PowerShell](https://msdn.microsoft.com/library/mt619274.aspx) . Per informazioni dettagliate, vedere [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs).

> [!NOTE]
> Negli esempi inclusi in questo argomento viene usato il [modello di distribuzione Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md), quindi vengono impiegati gli esempi di [cmdlet di Azure Resource Manager](https://msdn.microsoft.com/library/azure/mt125356.aspx). 
> 
> 

Eseguire il cmdlet [**Connect-AzureRmAccount**](https://docs.microsoft.com/powershell/module/azurerm.profile/connect-azurermaccount) per visualizzare una schermata di accesso in cui immettere le credenziali. Usare le stesse credenziali usate per l'accesso al portale di Azure.

    Connect-AzureRmAccount

Se si hanno più sottoscrizioni, usare il cmdlet [**Set-AzureRmContext**](https://docs.microsoft.com/powershell/module/azurerm.profile/set-azurermcontext) per selezionare la sottoscrizione da usare nella sessione di PowerShell. Per visualizzare la sottoscrizione in uso nella sessione corrente di PowerShell, eseguire [**Get-AzureRmContext**](https://docs.microsoft.com/powershell/module/azurerm.profile/get-azurermcontext). Per visualizzare tutte le sottoscrizioni, eseguire [**Get-AzureRmSubscription**](https://docs.microsoft.com/powershell/module/servicemanagement/azurerm.profile/get-azurermsubscription).

    Set-AzureRmContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'

