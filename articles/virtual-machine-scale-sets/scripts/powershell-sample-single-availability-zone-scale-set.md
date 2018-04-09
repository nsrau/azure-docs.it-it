---
title: Esempi di Azure PowerShell - Set di scalabilità a singola zona | Microsoft Docs
description: Esempi di Azure PowerShell
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 4e4f6d26c119a113bab0220828c847f438e7bc7a
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
# <a name="create-a-single-zone-virtual-machine-scale-set-with-powershell"></a>Creare un set di scalabilità di macchine virtuali a singola zona con PowerShell
Questo script crea un set di scalabilità di macchine virtuali che esegue Windows Server 2016 in una singola zona di disponibilità. Dopo aver eseguito lo script, è possibile accedere alla macchina virtuale tramite RDP.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio
[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.ps1 "Create single-zone scale set")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione
Eseguire questo comando per rimuovere il gruppo di risorse, il set di scalabilità e tutte le risorse correlate.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script
Questo script usa i comandi seguenti per creare la distribuzione. Ogni elemento della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Crea un oggetto di configurazione che definisce la subnet della rete virtuale. |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Crea una rete virtuale e una subnet. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Crea un indirizzo IP pubblico statico. |
| [New-AzureRmLoadBalancerFrontendIpConfig](/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig) | Crea un oggetto di configurazione che definisce il front-end del servizio di bilanciamento del carico, incluso l'indirizzo IP pubblico. |
| [New-AzureRmLoadBalancerBackendAddressPoolConfig](/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig) | Crea un oggetto di configurazione che definisce il back-end del servizio di bilanciamento del carico. |
| [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer) | Crea un servizio di bilanciamento del carico in base agli oggetti di configurazione front-end e back-end. |
| [Add-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/new-azurermloadbalancerprobeconfig) | Crea un probe di integrità del servizio di bilanciamento del carico per monitorare il traffico sulla porta TCP 80. Se vengono rilevati due errori consecutivi a intervalli di 15 secondi, l'endpoint viene considerato non integro. |
| [Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerruleconfig) | Crea un oggetto di configurazione che definisce regole di bilanciamento del carico per la distribuzione del traffico sulla porta TCP 80 dal pool front-end al pool back-end. |
| [Set-AzureRmLoadBalancer](/powershell/module/azurerm.network/set-azurermloadbalancer) | Aggiorna il servizio di bilanciamento del carico con probe di integrità e regole di bilanciamento del carico. |
| [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) | Crea un oggetto di configurazione che definisce una regola del gruppo di sicurezza di rete per consentire traffico sulla porta TCP 80. |
| [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) | Crea un gruppo e una regola di sicurezza di rete. |
| [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) | Aggiorna la subnet della rete virtuale da associare al gruppo di sicurezza di rete. Le istanze di macchina virtuale connesse alla subnet ereditano le regole del gruppo di sicurezza di rete. |
| [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork) | Aggiorna la rete virtuale con la configurazione della subnet e del gruppo di sicurezza di rete. |
| [New-AzureRmVmssIpConfig](/powershell/module/azurerm.compute/new-azurermvmssipconfig) | Crea un oggetto di configurazione che definisce le informazioni dell'indirizzo IP del set di scalabilità di macchine virtuali per connettere le istanze di macchina virtuale al pool back-end del servizio di bilanciamento del carico e al pool NAT in ingresso.
| [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) | Crea un oggetto di configurazione che definisce il numero di istanze di macchina virtuale in un set di scalabilità, le dimensioni delle istanze di macchina virtuale e la modalità dei criteri di aggiornamento. |
| [Set-AzureRmVmssStorageProfile](/powershell/module/azurerm.compute/set-azurermvmssstorageprofile) | Crea un oggetto di configurazione che definisce l'immagine di macchina virtuale da usare per le istanze di macchina virtuale. |
| [Set-AzureRmVmssOsProfile](/powershell/module/azurerm.compute/set-azurermvmssosprofile) | Crea un profilo di configurazione che definisce il nome dell'istanza di macchina virtuale e le credenziali dell'utente. |
| [Add-AzureRmVmssNetworkInterfaceConfiguration](/powershell/module/azurerm.compute/add-azurermvmssnetworkinterfaceconfiguration) | Crea un oggetto di configurazione che definisce la scheda di interfaccia di rete virtuale per ogni istanza di macchina virtuale. |
| [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss) | Combina tutti gli oggetti di configurazione per creare il set di scalabilità di macchine virtuali. |
| [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) | Ottiene informazioni su un set di scalabilità di macchine virtuali. |
| [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) | Aggiunge un'estensione per VM per Script personalizzato per l'installazione di un'applicazione Web di base. |
| [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) | Aggiorna il modello del set di scalabilità di macchine virtuali per l'applicazione dell'estensione di VM. |
| [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) | Ottiene informazioni sull'indirizzo IP pubblico assegnato usato dal servizio di bilanciamento del carico. |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Rimuove un gruppo di risorse e tutte le risorse contenute al suo interno. |

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).

Altri esempi di script PowerShell per i set di scalabilità di macchine virtuali sono disponibili nella [documentazione dei set di scalabilità di macchine virtuali di Azure](../powershell-samples.md).