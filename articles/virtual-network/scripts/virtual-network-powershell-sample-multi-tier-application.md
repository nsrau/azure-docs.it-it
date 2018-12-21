---
title: Esempio di script di Azure PowerShell - Creare una rete per applicazioni multilivello | Microsoft Docs
description: Esempio di script di Azure PowerShell - Creare una rete virtuale per applicazioni multilivello.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 12/13/2018
ms.author: jdial
ms.openlocfilehash: ebc0e18ecea61705ea8cb925a25d4233bfc9a2cb
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2018
ms.locfileid: "53340505"
---
# <a name="create-a-network-for-multi-tier-applications-script-sample"></a>Esempio di script per la creazione di una rete per applicazioni multilivello

Questo script di esempio crea una rete virtuale con subnet front-end e back-end. Il traffico verso la subnet front-end è limitato a HTTP e SSH, mentre il traffico verso la subnet back-end è limitato a MySQL sulla porta 3306. Dopo aver eseguito lo script saranno presenti due macchine virtuali, una in ogni subnet in cui è possibile distribuire server Web e software MySQL.

È possibile eseguire lo script da Azure [Cloud Shell](https://shell.azure.com/powershell) o da un'installazione di PowerShell locale. Se si usa PowerShell in locale, per questo script è necessario il modulo AzureRM PowerShell versione 5.4.1 o successiva. Per trovare la versione installata, eseguire `Get-Module -ListAvailable AzureRM`. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzureRmAccount` per creare una connessione con Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

<!-- gitHub issue https://github.com/MicrosoftDocs/azure-docs/issues/17748 --> Un ID di subnet viene assegnato dopo aver creato una rete virtuale; in particolare, utilizzando il cmdlet New-AzureRmVirtualNetwork con l'opzione -Subnet. Se si configura la subnet usando il cmdlet New-AzureRmVirtualNetworkSubnetConfig prima della chiamata a New-AzureRmVirtualNetwork, l'ID di subnet non sarà visibile fino a dopo la chiamata di New-AzureRmVirtualNetwork.

[!code-azurepowershell-interactive[main](../../../powershell_scripts/virtual-network/virtual-network-multi-tier-application/virtual-network-multi-tier-application.ps1  "Virtual network for multi-tier application")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Eseguire questo comando per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse, una rete virtuale e i gruppi di sicurezza di rete. Ogni comando della tabella seguente include collegamenti alla documentazione specifica del comando:

| Comando | Note |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Consente di creare una rete virtuale e una subnet front-end di Azure. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Consente di creare una subnet back-end. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Consente di creare un indirizzo IP pubblico per accedere alla macchina virtuale da Internet. |
| [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | Consente di creare interfacce di rete virtuale e di associarle alle subnet front-end e back-end della rete virtuale. |
| [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) | Consente di creare gruppi di sicurezza di rete associati alle subnet front-end e back-end. |
| [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) |Consente di creare regole del gruppo di sicurezza di rete che consentono o bloccano porte specifiche su subnet specifiche. |
| [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | Consente di creare macchine virtuali e associa una NIC a ogni VM. Questo comando specifica anche l'immagine della macchina virtuale da usare e le credenziali di amministrazione. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Consente di eliminare un gruppo di risorse e tutte le risorse in esso contenute. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).

Altri esempi di script di PowerShell della rete virtuale sono disponibili in [Virtual network PowerShell samples](../powershell-samples.md) (Esempi di PowerShell della rete virtuale).
