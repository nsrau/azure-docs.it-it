---
title: 'Gateway VPN di Azure: verificare una connessione gateway'
description: Questo articolo descrive come verificare una connessione di Gateway VPN alla rete virtuale.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 05/16/2017
ms.author: cherylmc
ms.openlocfilehash: 40417b078577424b1ba13ce60f4f773a1c8fd4dc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75780148"
---
# <a name="verify-a-vpn-gateway-connection"></a>Verificare una connessione di Gateway VPN

Questo articolo illustra come verificare una connessione al gateway VPN nei modelli di distribuzione di Resource Manager e classico.

## <a name="azure-portal"></a>Portale di Azure

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="powershell"></a>PowerShell

Per verificare una connessione al gateway VPN nel modello di distribuzione di Resource Manager tramite PowerShell, installare la versione più recente dei [cmdlet di PowerShell per Azure Resource Manager](/powershell/azure/overview).

[!INCLUDE [PowerShell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per verificare una connessione al gateway VPN nel modello di distribuzione di Resource Manager tramite l'interfaccia della riga di comando di Azure, installare la versione più recente dei [comandi per l'interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/install-azure-cli), ovvero la versione 2.0 o successiva.

[!INCLUDE [CLI](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]


## <a name="azure-portal-classic"></a>Portale di Azure (classico)

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

## <a name="powershell-classic"></a>PowerShell (versione classica)

Per verificare una connessione al gateway VPN nel modello di distribuzione classico tramite PowerShell, installare le versioni più recenti dei cmdlet di Azure PowerShell. Assicurarsi di scaricare e installare il modulo [Gestione servizi](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0#azure-service-management-cmdlets). Accedere al modello di distribuzione classica con "Add-AzureAccount".

[!INCLUDE [Classic PowerShell](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

## <a name="next-steps"></a>Passaggi successivi

* È possibile aggiungere macchine virtuali alla rete virtuale. Per i passaggi, vedere [Creare la prima macchina virtuale](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) .
