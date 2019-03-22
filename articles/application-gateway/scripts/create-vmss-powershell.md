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
ms.openlocfilehash: fc35d442be7eea0406e4ba55876c53d7d94fee01
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57310149"
---
# <a name="manage-web-traffic-with-azure-powershell"></a>Gestire il traffico Web con Azure PowerShell

Questo script crea un gateway applicazione che usa un set di scalabilità di macchine virtuali per server back-end. Il gateway applicazione può quindi essere configurato per gestire il traffico Web. Dopo aver eseguito lo script, è possibile testare il gateway applicazione tramite il relativo indirizzo IP pubblico.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/application-gateway/create-vmss/create-vmss.ps1 "Create application gateway")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Eseguire il comando seguente per rimuovere il gruppo di risorse, il gateway applicazione e tutte le risorse correlate.

```powershell
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare la distribuzione. Ogni elemento della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Crea la configurazione della subnet. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Crea la rete virtuale con le configurazioni di subnet. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Crea l'indirizzo IP pubblico per il gateway applicazione. |
| [New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration) | Crea la configurazione che associa una subnet al gateway applicazione. |
| [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig) | Crea la configurazione che assegna un indirizzo IP pubblico al gateway applicazione. |
| [New-AzApplicationGatewayFrontendPort](/powershell/module/az.network/new-azapplicationgatewayfrontendport) | Assegna una porta da usare per accedere al gateway applicazione. |
| [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool) | Crea un pool back-end per un gateway applicazione. |
| [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsettings) | Configura le impostazioni per un pool back-end. |
| [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) | Crea un listener. |
| [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule) | Crea una regola di routing. |
| [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku) | Specifica il livello e la capacità per un gateway applicazione. |
| [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway) | Creare un gateway applicazione. |
| [Set-AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) | Crea un profilo di archiviazione per il set di scalabilità. |
| [Set-AzVmssOsProfile](/powershell/module/az.compute/set-azvmssosprofile) | Definisce il sistema operativo per il set di scalabilità. |
| [Add-AzVmssNetworkInterfaceConfiguration](/powershell/module/az.compute/add-azvmssnetworkinterfaceconfiguration) | Definisce l'interfaccia di rete per il set di scalabilità. |
| [New-AzVmss](/powershell/module/az.compute/new-azvm) | Crea un set di scalabilità di macchine virtuali. |
| [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) | Ottiene l'indirizzo IP pubblico del gateway applicazione. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Rimuove un gruppo di risorse e tutte le risorse contenute al suo interno. | 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).

Altri esempi di script di PowerShell per il gateway applicazione sono reperibili nella [documentazione sul gateway applicazione di Azure](../powershell-samples.md).
