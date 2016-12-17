---
title: Verificare una connessione al gateway | Documentazione Microsoft
description: Questo articolo illustra come verificare una connessione al gateway nel modello di distribuzione Resource Manager
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 7e3d1043-caa9-4472-96d3-832f4e2c91ee
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/14/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 420b193d558d00f574d24c87ab9db6e9e757a952


---
# <a name="verify-a-gateway-connection"></a>Verificare una connessione al gateway
È possibile verificare la connessione al gateway in diversi modi. Questo articolo illustra come verificare lo stato di una connessione al gateway di Resource Manager usando il portale di Azure e PowerShell.

## <a name="verify-using-powershell"></a>Verificare tramite PowerShell
È necessario installare la versione più recente dei cmdlet di PowerShell per Gestione risorse di Azure. Per informazioni sull'installazione dei cmdlet di PowerShell, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md) . Per altre informazioni sull'uso dei cmdlet di Resource Manager, vedere [Uso di Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md).

### <a name="step-1-log-in-to-your-azure-account"></a>Passaggio 1: accedere con il proprio account Azure
1. Aprire la console di PowerShell con privilegi elevati e connettersi al proprio account.
   
        Login-AzureRmAccount
2. Controllare le sottoscrizioni per l'account.
   
        Get-AzureRmSubscription 
3. Specificare la sottoscrizione da usare.
   
        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

### <a name="step-2-verify-your-connection"></a>Passaggio 2: verificare la connessione
[!INCLUDE [verify connection powershell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="verify-using-the-azure-portal"></a>Verificare tramite il portale di Azure
[!INCLUDE [verify connection portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="next-steps"></a>Passaggi successivi
* È possibile aggiungere macchine virtuali alla rete virtuale. Per i passaggi, vedere [Creare una macchina virtuale](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) .




<!--HONumber=Nov16_HO3-->


