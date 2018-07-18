---
title: Esempio di script di Azure PowerShell - Gestire il traffico Web | Microsoft Docs
description: Esempio di script di Azure PowerShell - Gestire il traffico Web con un gateway applicazione e un set di scalabilità di macchine virtuali.
services: application-gateway
documentationcenter: networking
author: vhorne
manager: jpconnock
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/29/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: ef6a4171c582e3eb82fbcc73171797e1c806de05
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/03/2018
ms.locfileid: "32770605"
---
# <a name="manage-web-traffic-with-azure-powershell"></a>Gestire il traffico Web con Azure PowerShell

Questo script crea un gateway applicazione che usa un set di scalabilità di macchine virtuali per server back-end. Il gateway applicazione può quindi essere configurato per gestire il traffico Web. Dopo aver eseguito lo script, è possibile testare il gateway applicazione tramite il relativo indirizzo IP pubblico.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/application-gateway/create-vmss/create-vmss.ps1 "Create application gateway")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Eseguire il comando seguente per rimuovere il gruppo di risorse, il gateway applicazione e tutte le risorse correlate.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroupAG
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare la distribuzione. Ogni elemento della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Crea la configurazione della subnet. |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Crea la rete virtuale con le configurazioni di subnet. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Crea l'indirizzo IP pubblico per il gateway applicazione. |
| [New-AzureRmApplicationGatewayIPConfiguration](/powershell/module/azurerm.network/new-azurermapplicationgatewayipconfiguration) | Crea la configurazione che associa una subnet al gateway applicazione. |
| [New-AzureRmApplicationGatewayFrontendIPConfig](/powershell/module/azurerm.network/new-azurermapplicationgatewayfrontendipconfig) | Crea la configurazione che assegna un indirizzo IP pubblico al gateway applicazione. |
| [New-AzureRmApplicationGatewayFrontendPort](/powershell/module/azurerm.network/new-azurermapplicationgatewayfrontendport) | Assegna una porta da usare per accedere al gateway applicazione. |
| [New-AzureRmApplicationGatewayBackendAddressPool](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendaddresspool) | Crea un pool back-end per un gateway applicazione. |
| [New-AzureRmApplicationGatewayBackendHttpSettings](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendhttpsettings) | Configura le impostazioni per un pool back-end. |
| [New-AzureRmApplicationGatewayHttpListener](/powershell/module/azurerm.network/new-azurermapplicationgatewayhttplistener) | Crea un listener. |
| [New-AzureRmApplicationGatewayRequestRoutingRule](/powershell/module/azurerm.network/new-azurermapplicationgatewayrequestroutingrule) | Crea una regola di routing. |
| [New-AzureRmApplicationGatewaySku](/powershell/module/azurerm.network/new-azurermapplicationgatewaysku) | Specifica il livello e la capacità per un gateway applicazione. |
| [New-AzureRmApplicationGateway](/powershell/module/azurerm.network/new-azurermapplicationgateway) | Creare un gateway applicazione. |
| [Set-AzureRmVmssStorageProfile](/powershell/module/azurerm.compute/set-azurermvmssstorageprofile) | Crea un profilo di archiviazione per il set di scalabilità. |
| [Set-AzureRmVmssOsProfile](/powershell/module/azurerm.compute/set-azurermvmssosprofile) | Definisce il sistema operativo per il set di scalabilità. |
| [Add-AzureRmVmssNetworkInterfaceConfiguration](/powershell/module/azurerm.compute/add-azurermvmssnetworkinterfaceconfiguration) | Definisce l'interfaccia di rete per il set di scalabilità. |
| [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvm) | Crea un set di scalabilità di macchine virtuali. |
| [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) | Ottiene l'indirizzo IP pubblico del gateway applicazione. |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Rimuove un gruppo di risorse e tutte le risorse contenute al suo interno. | 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).

Altri esempi di script di PowerShell per il gateway applicazione sono reperibili nella [documentazione sul gateway applicazione di Azure](../powershell-samples.md).
