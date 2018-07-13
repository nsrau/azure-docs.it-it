---
title: Diagnosticare un problema di routing di rete di una macchina virtuale - Azure PowerShell | Microsoft Docs
description: In questo articolo si apprenderà come diagnosticare un problema di routing di rete di una macchina virtuale usando la funzionalità Hop successivo di Azure Network Watcher.
services: network-watcher
documentationcenter: network-watcher
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose virtual machine (VM) network routing problem that prevents communication to different destinations.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: f793a201b3fbf57ac2f420c4f4e57a230bc11468
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38299025"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-powershell"></a>Diagnosticare un problema di routing di rete di una macchina virtuale - Azure PowerShell

In questo articolo si distribuisce una macchina virtuale e quindi si controllano le comunicazioni verso un indirizzo IP e un URL. Viene determinata la causa di un errore di comunicazione e si apprende come è possibile risolverlo.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-powershell.md)]

Se si sceglie di installare e usare PowerShell in locale, per questo articolo è necessario il modulo AzureRM PowerShell 5.4.1 o versione successiva. Per trovare la versione installata, eseguire `Get-Module -ListAvailable AzureRM`. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Login-AzureRmAccount` per creare una connessione con Azure.

## <a name="create-a-vm"></a>Creare una macchina virtuale

Prima di poter creare una macchina virtuale, è necessario creare un gruppo di risorse per contenerla. Creare un gruppo di risorse con [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *stati uniti orientali*.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

Creare la macchina virtuale con [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Quando si esegue questo passaggio vengono chieste le credenziali. I valori immessi sono configurati come nome utente e password per la VM.

```azurepowershell-interactive
$vM = New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVm" `
    -Location "East US"
```

La creazione della VM richiede alcuni minuti. Non continuare a seguire i passaggi rimanenti fino a quando non viene creata la macchina virtuale e PowerShell non restituisce l'output.

## <a name="test-network-communication"></a>Testare la comunicazione di rete

Per testare la comunicazione di rete con Network Watcher è necessario prima abilitare un network watcher nell'area in cui si trova la macchina virtuale da testare, quindi usare la funzionalità Hop successivo di Network Watcher per testare la comunicazione.

## <a name="enable-network-watcher"></a>Abilitare Network Watcher

Se si ha già un'istanza di Network Watcher abilitata nell'area Stati Uniti orientali, usare [Get-AzureRmNetworkWatcher](/powershell/module/azurerm.network/get-azurermnetworkwatcher) per recuperare Network Watcher. L'esempio seguente recupera un'istanza di Network Watcher esistente denominata *NetworkWatcher_eastus* che si trova nel gruppo di risorse *NetworkWatcherRG*:

```azurepowershell-interactive
$networkWatcher = Get-AzureRmNetworkWatcher `
  -Name NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG
```

Se non si ha già un'istanza di Network Watcher abilitata nell'area Stati Uniti orientali, usare [New-AzureRmNetworkWatcher](/powershell/module/azurerm.network/new-azurermnetworkwatcher) per creare un'istanza di Network Watcher in tale area:

```azurepowershell-interactive
$networkWatcher = New-AzureRmNetworkWatcher `
  -Name "NetworkWatcher_eastus" `
  -ResourceGroupName "NetworkWatcherRG" `
  -Location "East US"
```

### <a name="use-next-hop"></a>Usare la funzionalità Hop successivo

Azure crea automaticamente le route per le destinazioni predefinite. È possibile creare route personalizzate per eseguire l'override delle route predefinite. In alcuni casi, le route personalizzate possono generare un errore di comunicazione. Per testare il routing da una macchina virtuale, usare il comando [Get-AzureRmNetworkWatcherNextHop](/powershell/module/azurerm.network/get-azurermnetworkwatchernexthop) per determinare l'hop di routing successivo quando il traffico è destinato a un indirizzo specifico.

Testare le comunicazioni in uscita dalla macchina virtuale a uno degli indirizzi IP di www.bing.com:

```azurepowershell-interactive
Get-AzureRmNetworkWatcherNextHop `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $VM.Id `
  -SourceIPAddress 192.168.1.4 `
  -DestinationIPAddress 13.107.21.200
```

Dopo alcuni secondi, l'output indica che **NextHopType** è **Internet** e che **RouteTableId** è **System Route**. Questo risultato informa l'utente che è presente una route valida per la destinazione.

Testare le comunicazioni in uscita dalla macchina virtuale verso 172.31.0.100:

```azurepowershell-interactive
Get-AzureRmNetworkWatcherNextHop `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $VM.Id `
  -SourceIPAddress 192.168.1.4 `
  -DestinationIPAddress 172.31.0.100
```

L'output indica che **None** è **NextHopType** e che **RouteTableId** è **System Route**. Questo risultato informa che, nonostante sia presente una route di sistema valida per la destinazione, non vi è alcun hop successivo per indirizzare il traffico alla destinazione.

## <a name="view-details-of-a-route"></a>Visualizzare i dettagli di una route

Per analizzare ulteriormente il routing, esaminare le route valide per l'interfaccia di rete con il comando [Get-AzureRmEffectiveRouteTable](/powershell/module/azurerm.network/get-azurermeffectiveroutetable):

```azurepowershell-interactive
Get-AzureRmEffectiveRouteTable `
  -NetworkInterfaceName myVm `
  -ResourceGroupName myResourceGroup |
  Format-table
```

Viene restituito l'output che include il testo seguente:

```powershell
Name State  Source  AddressPrefix           NextHopType NextHopIpAddress
---- -----  ------  -------------           ----------- ----------------
     Active Default {192.168.0.0/16}        VnetLocal   {}              
     Active Default {0.0.0.0/0}             Internet    {}              
     Active Default {10.0.0.0/8}            None        {}              
     Active Default {100.64.0.0/10}         None        {}              
     Active Default {172.16.0.0/12}         None        {}              
```

Come si può osservare nell'output precedente, la route con **AaddressPrefix** **0.0.0.0/0** instrada tutto il traffico non destinato agli indirizzi all'interno dei prefissi degli indirizzi dell'altra route con hop successivo **Internet**. Come si può notare sempre nell'output, nonostante vi sia una route predefinita per il prefisso 172.16.0.0/12, che include l'indirizzo 172.31.0.100, il valore di **nextHopType** è **None**. Azure crea una route predefinita per 172.16.0.0/12, ma non specifica alcun tipo di hop successivo fino a quando non c'è un motivo per farlo. Se, ad esempio, è stato aggiunto l'intervallo di indirizzi 172.16.0.0/12 allo spazio di indirizzi della rete virtuale, Azure modifica **nextHopType** in **Rete virtuale** per la route. Il segno di spunta indica **Rete virtuale** come **nextHopType**.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il gruppo di risorse e tutte le risorse in esso contenute non sono più necessari, è possibile usare [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) per rimuoverli:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stata creata una macchina virtuale ed è stato diagnosticato un problema di routing di rete dalla macchina virtuale. Si è appreso che Azure crea più route predefinite e testa il routing verso due destinazioni diverse. Per altre informazioni, vedere il [routing in Azure](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) e come [creare route personalizzate](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

Per le connessioni delle macchine virtuali in uscita è anche possibile determinare la latenza, il traffico di rete consentito e negato tra la macchina virtuale e un endpoint tramite la funzionalità di [risoluzione dei problemi di connessione](network-watcher-connectivity-powershell.md) di Network Watcher. È possibile monitorare la comunicazione nel corso del tempo tra una macchina virtuale e un endpoint, ad esempio un indirizzo IP o un URL, usando la funzionalità di monitoraggio connessione di Network Watcher. Per informazioni vedere [Monitorare una connessione di rete](connection-monitor.md).