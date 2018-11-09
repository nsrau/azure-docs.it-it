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
ms.openlocfilehash: 5100c5723cf3442520d969e3db6aa65439855551
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2018
ms.locfileid: "50230880"
---
# <a name="create-an-azure-firewall-test-environment"></a>Creare un ambiente di test di Firewall di Azure

Questo esempio di script crea un firewall e un ambiente di rete di test. La rete contiene una rete virtuale con tre subnet: *AzureFirewallSubnet*, *ServersSubnet* e *JumpboxSubnet*. ServersSubnet e JumpboxSubnet contengono ognuna un'istanza di Windows Server con 2 core.

Il firewall si trova in AzureFirewallSubnet ed è configurato con una raccolta di regole di applicazione con una singola regola che consente l'accesso a www.microsoft.com.

Viene creata una route definita dall'utente che punta al traffico di rete da ServersSubnet attraverso il firewall, in cui vengono applicate le regole del firewall.

È possibile eseguire lo script da Azure [Cloud Shell](https://shell.azure.com/powershell) o da un'installazione di PowerShell locale. 

Se si esegue PowerShell in locale, per questo script è necessaria la versione più recente del modulo AzureRM PowerShell (6.9.0 o successiva). Per trovare la versione installata, eseguire `Get-Module -ListAvailable AzureRM`. 

È possibile usare `PowerShellGet` se è necessario eseguire l'aggiornamento, che è incorporato in Windows 10 e Windows Server 2016.

> [!NOTE]
>In altre versioni di Windows è necessario installare `PowerShellGet` prima di poterlo usare. È possibile eseguire `Get-Module -Name PowerShellGet -ListAvailable | Select-Object -Property Name,Version,Path` per determinare se è installato nel sistema. Se l'output è vuoto, è necessario installare la versione più recente di [Windows Management Framework](https://www.microsoft.com/download/details.aspx?id=54616).

Per altre informazioni, vedere [Installare Azure PowerShell in Windows con PowerShellGet](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-6.4.0)

Qualsiasi installazione esistente di Azure PowerShell eseguita con l'Installazione guidata piattaforma Web andrà in conflitto con l'installazione di PowerShellGet ed è necessario rimuoverla.

Tenere presente che se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzureRmAccount` per creare una connessione con Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio


[!code-azurepowershell-interactive[main](../../../powershell_scripts/firewall/create-fw-test.ps1  "Create a firewall test environment")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Eseguire questo comando per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate:

```powershell
Remove-AzureRmResourceGroup -Name AzfwSampleScriptEastUS -Force
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse, una rete virtuale e i gruppi di sicurezza di rete. Ogni comando della tabella seguente include collegamenti alla documentazione specifica del comando:

| Comando | Note |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Crea un oggetto di configurazione di subnet |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Consente di creare una rete virtuale e una subnet front-end di Azure. |
| [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) | Crea regole di sicurezza da assegnare a un gruppo di sicurezza di rete. |
| [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) |Consente di creare regole del gruppo di sicurezza di rete che consentono o bloccano porte specifiche su subnet specifiche. |
| [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) | Consente di associare i gruppi di risorse di rete alla subnet. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Consente di creare un indirizzo IP pubblico per accedere alla macchina virtuale da Internet. |
| [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | Consente di creare interfacce di rete virtuale e di associarle alle subnet front-end e back-end della rete virtuale. |
| [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) | Crea una configurazione di VM. Questa configurazione include informazioni quali il nome della VM, il sistema operativo e le credenziali amministrative. La configurazione viene usata durante la creazione della VM. |
| [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | Creare una macchina virtuale. |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Rimuove un gruppo di risorse e tutte le risorse contenute al suo interno. |
|[New-AzureRmFirewall](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermfirewall?view=azurermps-6.9.0)| Crea un nuovo firewall di Azure.|
|[Get-AzureRmFirewall](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermfirewall?view=azurermps-6.9.0)|Recupera un oggetto Firewall di Azure.|
|[New-AzureRmFirewallApplicationRule](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermfirewallapplicationrule?view=azurermps-6.9.0)|Crea una nuova regola di applicazione di Firewall di Azure.|
|[Set-AzureRmFirewall](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermfirewall?view=azurermps-6.9.0)|Esegue il commit delle modifiche nell'oggetto Firewall di Azure.|


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).

