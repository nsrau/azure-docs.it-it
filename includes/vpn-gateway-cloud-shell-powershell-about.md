---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/25/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b878d54f0f52768459dbfc810e47d294b9c8d996
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50097767"
---
Questo articolo usa i cmdlet di PowerShell. Per eseguire i cmdlet, è possibile usare Azure Cloud Shell, una shell interattiva gratuita. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. È sufficiente fare clic su **Copia** per copiare il codice, incollarlo in Cloud Shell e quindi premere INVIO per eseguirlo. Esistono alcuni modi per avviare Cloud Shell:

|  |   |
|-----------------------------------------------|---|
| Fare clic su **Prova** nell'angolo superiore destro di un blocco di codice. | ![Cloud Shell in questo articolo](./media/vpn-gateway-cloud-shell-powershell/cloud-shell-powershell-try-it.png) |
| Aprire Cloud Shell nel browser. | [![https://shell.azure.com/powershell](./media/vpn-gateway-cloud-shell-powershell/launchcloudshell.png)](https://shell.azure.com/powershell) |
| Fare clic sul pulsante **Cloud Shell** nel menu in alto a destra nel portale di Azure. | [![Cloud Shell nel portale](./media/vpn-gateway-cloud-shell-powershell/cloud-shell-menu.png)](https://portal.azure.com) |
|  |  |

Se non si vuole usare Azure Cloud Shell, è possibile installare PowerShell in locale. Se si sceglie di installare e usare PowerShell in locale, assicurarsi di installare la versione più recente dei cmdlet di PowerShell per Azure Resource Manager. I cmdlet di PowerShell vengono modificati di frequente e in genere è necessario aggiornarli per ottenere le funzionalità più recenti. Se non si aggiornano i cmdlet di PowerShell, i valori specificati potrebbero avere esito negativo. Per trovare la versione di PowerShell in esecuzione in locale, usare il cmdlet 'Get-Module -ListAvailable AzureRM'. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps). Per altre informazioni, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview).