---
title: 'Esempio di script di Azure PowerShell: Creare un ambiente di test di Firewall di Azure'
description: 'Esempio di script di Azure PowerShell: Creare un ambiente di test di Firewall di Azure.'
services: virtual-network
author: vhorne
ms.service: firewall
ms.devlang: powershell
ms.topic: sample
ms.date: 8/13/2018
ms.author: victorh
ms.openlocfilehash: df7897e5b0941f1763f1a10e51d49827bd2ca63d
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839272"
---
# <a name="create-an-azure-firewall-test-environment"></a>Creare un ambiente di test di Firewall di Azure

Questo esempio di script crea un firewall e un ambiente di rete di test. La rete contiene una rete virtuale con tre subnet: *AzureFirewallSubnet*, *ServersSubnet* e *JumpboxSubnet*. ServersSubnet e JumpboxSubnet contengono ognuna un'istanza di Windows Server con 2 core.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Il firewall si trova in AzureFirewallSubnet ed è configurato con una raccolta di regole di applicazione con una singola regola che consente l'accesso a `www.microsoft.com`.

Viene creata una route definita dall'utente che punta al traffico di rete da ServersSubnet attraverso il firewall, in cui vengono applicate le regole del firewall.

È possibile eseguire lo script da Azure [Cloud Shell](https://shell.azure.com/powershell) o da un'installazione di PowerShell locale. 

Se si esegue PowerShell in locale, per questo script è necessario Azure PowerShell. Per trovare la versione installata, eseguire `Get-Module -ListAvailable Az`. 

È possibile usare `PowerShellGet` se è necessario eseguire l'aggiornamento, che è incorporato in Windows 10 e Windows Server 2016.

> [!NOTE]
>In altre versioni di Windows è necessario installare `PowerShellGet` prima di poterlo usare. È possibile eseguire `Get-Module -Name PowerShellGet -ListAvailable | Select-Object -Property Name,Version,Path` per determinare se è installato nel sistema. Se l'output è vuoto, è necessario installare la versione più recente di [Windows Management Framework](https://www.microsoft.com/download/details.aspx?id=54616).

Per altre informazioni, vedere [Installare Azure PowerShell](/powershell/azure/install-Az-ps).

Qualsiasi installazione esistente di Azure PowerShell eseguita con l'Installazione guidata piattaforma Web andrà in conflitto con l'installazione di PowerShellGet ed è necessario rimuoverla.

Tenere presente che se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio


[!code-azurepowershell-interactive[main](../../../powershell_scripts/firewall/create-fw-test.ps1  "Create a firewall test environment")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Eseguire questo comando per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate:

```powershell
Remove-AzResourceGroup -Name AzfwSampleScriptEastUS -Force
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse, una rete virtuale e i gruppi di sicurezza di rete. Ogni comando della tabella seguente include collegamenti alla documentazione specifica del comando:

| Comando | Note |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Crea un oggetto di configurazione di subnet |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Consente di creare una rete virtuale e una subnet front-end di Azure. |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) | Crea regole di sicurezza da assegnare a un gruppo di sicurezza di rete. |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) |Consente di creare regole del gruppo di sicurezza di rete che consentono o bloccano porte specifiche su subnet specifiche. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Consente di associare i gruppi di risorse di rete alla subnet. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Consente di creare un indirizzo IP pubblico per accedere alla macchina virtuale da Internet. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Consente di creare interfacce di rete virtuale e di associarle alle subnet front-end e back-end della rete virtuale. |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | Crea una configurazione di VM. Questa configurazione include informazioni quali il nome della VM, il sistema operativo e le credenziali amministrative. La configurazione viene usata durante la creazione della VM. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Creare una macchina virtuale. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Rimuove un gruppo di risorse e tutte le risorse contenute al suo interno. |
|[New-AzFirewall](https://docs.microsoft.com/powershell/module/az.network/new-azfirewall)| Crea un nuovo firewall di Azure.|
|[Get-AzFirewall](https://docs.microsoft.com/powershell/module/az.network/get-azfirewall)|Recupera un oggetto Firewall di Azure.|
|[New-AzFirewallApplicationRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallapplicationrule)|Crea una nuova regola di applicazione di Firewall di Azure.|
|[Set-AzFirewall](https://docs.microsoft.com/powershell/module/az.network/set-azfirewall)|Esegue il commit delle modifiche nell'oggetto Firewall di Azure.|

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).

