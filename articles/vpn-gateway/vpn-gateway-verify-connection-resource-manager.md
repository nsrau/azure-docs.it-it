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
ms.date: 04/24/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: a67ea630cc8b5a3e9deab7733aa5cd2055949ec0
ms.lasthandoff: 04/27/2017


---
# <a name="verify-a-vpn-gateway-connection"></a>Verificare una connessione di Gateway VPN

Questo articolo illustra come verificare una connessione al gateway VPN nei modelli di distribuzione di Resource Manager e classico.

## <a name="verify-using-the-azure-portal"></a>Verificare tramite il portale di Azure

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="verify-using-powershell"></a>Verificare tramite PowerShell

Per eseguire la verifica con PowerShell, installare la versione più recente dei cmdlet PowerShell per Azure Resource Manager. Per informazioni sull'installazione dei cmdlet di PowerShell, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview) . Per altre informazioni sull'uso dei cmdlet di Resource Manager, vedere [Uso di Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md).

### <a name="log-in-to-your-azure-account"></a>Accedere all'account Azure
1. Aprire la console di PowerShell con privilegi elevati e connettersi al proprio account.

  ```powershell
  Login-AzureRmAccount
  ```
2. Controllare le sottoscrizioni per l'account.

  ```powershell
  Get-AzureRmSubscription
  ``` 
3. Specificare la sottoscrizione da usare.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```

### <a name="verify-your-connection"></a>Verificare la connessione

[!INCLUDE [PowerShell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="verify-using-the-azure-cli"></a>Eseguire la verifica tramite l'interfaccia della riga di comando di Azure

Per eseguire la verifica tramite l'interfaccia della riga di comando di Azure, installare la versione più recente dei comandi dell'interfaccia della riga di comando (2.0 o versione successiva). Per informazioni sull'installazione dei comandi dell'interfaccia della riga di comando, vedere [Install Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (Installare l'interfaccia della riga di comando di Azure 2.0).

### <a name="log-in-to-your-azure-account"></a>Accedere all'account Azure

1. Accedere alla sottoscrizione di Azure con il comando [az login](/cli/azure/#login) e seguire le istruzioni visualizzate.

  ```azurecli
  az login
  ```
2. Se si hanno più sottoscrizioni Azure, elencare le sottoscrizioni per l'account.

  ```azurecli
  Az account list --all
  ```
3. Specificare la sottoscrizione da usare.

  ```azurecli
  Az account set --subscription
  <replace_with_your_subscription_id>
  ```

### <a name="verify-your-connection"></a>Verificare la connessione

[!INCLUDE [CLI](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]

## <a name="verify-using-the-azure-portal-classic"></a>Verificare tramite il portale di Azure (distribuzione classica)
[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


## <a name="verify-using-powershell-classic"></a>Verificare tramite PowerShell (distribuzione classica)
Per eseguire la verifica con PowerShell, installare le versioni più recenti dei cmdlet Azure PowerShell. Assicurarsi di scaricare e installare entrambe le versioni di Resource Manager e Gestione dei servizi. Per informazioni sull'installazione dei cmdlet di PowerShell, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview) . 

### <a name="log-in-to-your-azure-account"></a>Accedere all'account Azure
1. Aprire la console di PowerShell con privilegi elevati e connettersi al proprio account.

  ```powershell
  Login-AzureRmAccount
  ```
2. Controllare le sottoscrizioni per l'account.

  ```powershell
  Get-AzureRmSubscription 
  ```
3. Specificare la sottoscrizione da usare.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```
4. Eseguire l'accesso per usare i cmdlet di Gestione dei servizi per il modello di distribuzione classica.

  ```powershell
  Add-AzureAccount
  ```

### <a name="verify-your-connection"></a>Verificare la connessione
[!INCLUDE [Classic PowerShell](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

## <a name="next-steps"></a>Passaggi successivi
* È possibile aggiungere macchine virtuali alla rete virtuale. Per i passaggi, vedere [Creare una macchina virtuale](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) .


