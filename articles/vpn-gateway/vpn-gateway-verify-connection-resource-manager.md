---
title: Verificare una connessione di Gateway VPN | Documentazione Microsoft
description: Questo articolo descrive come verificare una connessione di Gateway VPN alla rete virtuale.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 7e3d1043-caa9-4472-96d3-832f4e2c91ee
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/30/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: b5bad71095e4b7e3b26df15780467526200ffa10
ms.openlocfilehash: 68d94a6402b1497f65c4d03fb987ba800e86c2a3


---
# <a name="verify-a-vpn-gateway-connection"></a>Verificare una connessione di Gateway VPN
È possibile verificare la connessione di Gateway VPN alla rete virtuale tramite il portale e PowerShell. Questo articolo contiene procedure per il modello di distribuzione Resource Manager e di distribuzione classica.

## <a name="verify-using-the-azure-portal"></a>Verificare tramite il portale di Azure

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="verify-using-powershell"></a>Verificare tramite PowerShell

Per eseguire la verifica con PowerShell, installare la versione più recente dei cmdlet PowerShell per Azure Resource Manager. Per informazioni sull'installazione dei cmdlet di PowerShell, vedere [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs) . Per altre informazioni sull'uso dei cmdlet di Resource Manager, vedere [Uso di Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md).

### <a name="log-in-to-your-azure-account"></a>Accedere all'account Azure
1. Aprire la console di PowerShell con privilegi elevati e connettersi al proprio account.
   
        Login-AzureRmAccount
2. Controllare le sottoscrizioni per l'account.
   
        Get-AzureRmSubscription 
3. Specificare la sottoscrizione da usare.
   
        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

### <a name="verify-your-connection"></a>Verificare la connessione

[!INCLUDE [Powershell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="verify-using-the-azure-portal-classic"></a>Verificare tramite il portale di Azure (distribuzione classica)
[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


## <a name="verify-using-powershell-classic"></a>Verificare tramite PowerShell (distribuzione classica)
Per eseguire la verifica con PowerShell, installare le versioni più recenti dei cmdlet Azure PowerShell. Assicurarsi di scaricare e installare entrambe le versioni di Resource Manager e Gestione dei servizi. Per informazioni sull'installazione dei cmdlet di PowerShell, vedere [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs) . 

### <a name="log-in-to-your-azure-account"></a>Accedere all'account Azure
1. Aprire la console di PowerShell con privilegi elevati e connettersi al proprio account.
   
        Login-AzureRmAccount
2. Controllare le sottoscrizioni per l'account.
   
        Get-AzureRmSubscription 
3. Specificare la sottoscrizione da usare.
   
        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
4. Eseguire l'accesso per usare i cmdlet di Gestione dei servizi per il modello di distribuzione classica.

        Add-AzureAccount

### <a name="verify-your-connection"></a>Verificare la connessione
[!INCLUDE [Classic PowerShell](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

## <a name="next-steps"></a>Passaggi successivi
* È possibile aggiungere macchine virtuali alla rete virtuale. Per i passaggi, vedere [Creare una macchina virtuale](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) .




<!--HONumber=Jan17_HO5-->


