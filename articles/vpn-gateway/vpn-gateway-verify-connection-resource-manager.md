---
title: 'Gateway VPN di Azure: verificare una connessione gateway'
description: Questo articolo descrive come verificare una connessione di Gateway VPN alla rete virtuale.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/19/2020
ms.author: cherylmc
ms.custom: devx-track-azurecli
ms.openlocfilehash: b59294d07ef64875cb6fbd3e3a49dec61d8b8135
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659404"
---
# <a name="verify-a-vpn-gateway-connection"></a>Verificare una connessione di Gateway VPN

Questo articolo illustra come verificare una connessione al gateway VPN nei modelli di distribuzione di Resource Manager e classico.

## <a name="azure-portal"></a>Portale di Azure

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="powershell"></a>PowerShell

Per verificare una connessione al gateway VPN nel modello di distribuzione di Resource Manager tramite PowerShell, installare la versione più recente dei [cmdlet di PowerShell per Azure Resource Manager](/powershell/azure/).

[!INCLUDE [PowerShell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per verificare una connessione al gateway VPN nel modello di distribuzione di Resource Manager tramite l'interfaccia della riga di comando di Azure, installare la versione più recente dei [comandi per l'interfaccia della riga di comando](/cli/azure/install-azure-cli), ovvero la versione 2.0 o successiva.

[!INCLUDE [CLI](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]

## <a name="azure-portal-classic"></a>Portale di Azure (classico)

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

## <a name="powershell-classic"></a>PowerShell (versione classica)

Per verificare una connessione al gateway VPN nel modello di distribuzione classico tramite PowerShell, installare le versioni più recenti dei cmdlet di Azure PowerShell. Assicurarsi di scaricare e installare il modulo [Gestione servizi](/powershell/azure/servicemanagement/install-azure-ps?#azure-service-management-cmdlets). Accedere al modello di distribuzione classica con "Add-AzureAccount".

[!INCLUDE [Classic PowerShell](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

## <a name="next-steps"></a>Passaggi successivi

* È possibile aggiungere macchine virtuali alla rete virtuale. Per i passaggi, vedere [Creare la prima macchina virtuale](../virtual-machines/windows/quick-create-portal.md) .