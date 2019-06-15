---
title: Connettere reti virtuali con il peering reti virtuali - PowerShell | Microsoft Docs
description: In questo articolo viene illustrato come connettere reti virtuali con il peering di rete virtuale usando Azure PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: f5c90b7d79b31c321b00869c90b0261c0b4730d3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66727749"
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-powershell"></a>Connettere reti virtuali con il peering reti virtuali usando PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

È possibile connettere due reti virtuali tra loro con il peering reti virtuali. Dopo che è stato eseguito il peering, le risorse delle due reti virtuali possono comunicare tra loro con la stessa larghezza di banda e la stessa latenza che sarebbero disponibili se si trovassero nella stessa rete virtuale. In questo articolo viene spiegato come:

* Creare due reti virtuali
* Connettere due reti virtuali con un peering di reti virtuali
* Distribuire una macchina virtuale in ogni rete virtuale
* Stabilire la comunicazione tra le macchine virtuali

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare PowerShell in locale, per questo articolo è necessario il modulo Azure PowerShell 1.0.0 o versione successiva. Eseguire `Get-Module -ListAvailable Az` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

## <a name="create-virtual-networks"></a>Creare reti virtuali

Prima di creare una rete virtuale, è necessario creare un gruppo di risorse per la rete virtuale e tutte le altre risorse create in questo articolo. Creare un gruppo di risorse con [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *stati uniti orientali*.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Creare una rete virtuale con [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). L'esempio seguente crea una rete virtuale denominata *myVirtualNetwork1* con prefisso di indirizzo *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork1 = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork1 `
  -AddressPrefix 10.0.0.0/16
```

Creare una configurazione di subnet con [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). L'esempio seguente crea una configurazione di subnet con prefisso di indirizzo 10.0.0.0/24:

```azurepowershell-interactive
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork1
```

Scrivere la configurazione della subnet nella rete virtuale con [Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork), che consente di creare la subnet:

```azurepowershell-interactive
$virtualNetwork1 | Set-AzVirtualNetwork
```

Creare una rete virtuale con il prefisso di indirizzo 10.1.0.0/16 e una subnet:

```azurepowershell-interactive
# Create the virtual network.
$virtualNetwork2 = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork2 `
  -AddressPrefix 10.1.0.0/16

# Create the subnet configuration.
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork2

# Write the subnet configuration to the virtual network.
$virtualNetwork2 | Set-AzVirtualNetwork
```

## <a name="peer-virtual-networks"></a>Eseguire il peering delle reti virtuali

Creare un peering [Add-AzVirtualNetworkPeering](/powershell/module/az.network/add-azvirtualnetworkpeering). L'esempio seguente crea il peering da *myVirtualNetwork1* a *myVirtualNetwork2*.

```azurepowershell-interactive
Add-AzVirtualNetworkPeering `
  -Name myVirtualNetwork1-myVirtualNetwork2 `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id
```

Nell'output restituito dopo l'esecuzione del comando precedente si può osservare che il valore di **PeeringState** è *Initiated*. Il peering rimane nello stato *Initiated* finché non si crea il peering da *myVirtualNetwork2* a *myVirtualNetwork1*. Creare un peering da *myVirtualNetwork2* a *myVirtualNetwork1*.

```azurepowershell-interactive
Add-AzVirtualNetworkPeering `
  -Name myVirtualNetwork2-myVirtualNetwork1 `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id
```

Nell'output restituito dopo l'esecuzione del comando precedente si può osservare che il valore di **PeeringState** è *Connected*. Azure avrà modificato anche lo stato del peering *myVirtualNetwork1-myVirtualNetwork2* in *Connected*. Verificare che lo stato del peering per la *myVirtualNetwork1-myVirtualNetwork2* peering è diventato *connesso* con [Get-AzVirtualNetworkPeering](/powershell/module/az.network/get-azvirtualnetworkpeering).

```azurepowershell-interactive
Get-AzVirtualNetworkPeering `
  -ResourceGroupName myResourceGroup `
  -VirtualNetworkName myVirtualNetwork1 `
  | Select PeeringState
```

Le risorse in una rete virtuale non possono comunicare con le risorse nell'altra rete virtuale finché il valore di **PeeringState** relativo ai peering in entrambe le reti virtuali non è *Connected*.

## <a name="create-virtual-machines"></a>Creare macchine virtuali

Creare una macchina virtuale in ogni rete virtuale per poter stabilire la comunicazione tra di esse in un passaggio successivo.

### <a name="create-the-first-vm"></a>Creare la prima VM

Creare una VM con [New-AzVM](/powershell/module/az.compute/new-azvm). L'esempio seguente crea una macchina virtuale denominata *myVm1* nella rete virtuale *myVirtualNetwork1*. L'opzione `-AsJob` crea la macchina virtuale in background, pertanto è possibile continuare con il passaggio successivo. Quando richiesto, immettere il nome utente e la password con cui si vuole accedere alla macchina virtuale.

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork1" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm1" `
  -AsJob
```

### <a name="create-the-second-vm"></a>Creare la seconda VM

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork2" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm2"
```

La creazione della VM richiede alcuni minuti. Non procedere con i passaggi successivi finché Azure non crea la macchina virtuale e restituisce l'output in PowerShell.

## <a name="communicate-between-vms"></a>Stabilire la comunicazione tra le macchine virtuali

È possibile connettersi all'indirizzo IP pubblico di una macchina virtuale da Internet. Usare [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) per restituire l'indirizzo IP pubblico di una macchina virtuale. L'esempio seguente restituisce l'indirizzo IP pubblico della VM *myVm1*:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

Usare il comando seguente per creare una sessione di Desktop remoto con la macchina virtuale *myVm1* dal computer locale. Sostituire `<publicIpAddress>` con l'indirizzo IP restituito dal comando precedente.

```
mstsc /v:<publicIpAddress>
```

Viene creato, scaricato nel computer e aperto un file Remote Desktop Protocol con estensione rdp. Immettere il nome utente e la password e quindi fare clic su **OK**. Per specificare le credenziali immesse durante la creazione della macchina virtuale, potrebbe essere necessario selezionare **Altre opzioni** e quindi **Usa un account diverso**. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Fare clic su **Sì** o **Continua** per procedere con la connessione.

Nella macchina virtuale *myVm1* abilitare il protocollo ICMP (Internet Control Message Protocol) attraverso Windows Firewall, in modo che sia possibile effettuare il ping della macchina virtuale da *myVm2* in un passaggio successivo tramite PowerShell:

```powershell
New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
```

Anche se in questo articolo viene usato il ping per la comunicazione tra le macchine virtuali, non è consigliabile consentire il protocollo ICMP tramite Windows Firewall per le distribuzioni di produzione.

Per stabilire la connessione alla macchina virtuale *myVm2*, immettere il comando seguente da un prompt dei comandi nella macchina virtuale *myVm1*:

```
mstsc /v:10.1.0.4
```

Dato che è stato abilitato il ping in *myVm1*, è ora possibile effettuare il ping in base all'indirizzo IP dal prompt dei comandi nella macchina virtuale *myVm2*:

```
ping 10.0.0.4
```

Si riceveranno quattro risposte. Disconnettere le sessioni RDP con *myVm1* e *myVm2*.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessario, usare [Remove-AzResourcegroup](/powershell/module/az.resources/remove-azresourcegroup) per rimuovere il gruppo di risorse e tutte le risorse in esso contenute.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come connettere due reti nella stessa area di Azure con il peering di rete virtuale. È anche possibile creare un peering di reti virtuali in [aree supportate](virtual-network-manage-peering.md#cross-region) diverse e in [sottoscrizioni di Azure diverse](create-peering-different-subscriptions.md#powershell) e creare [progettazioni di rete di tipo hub-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) con peering. Per altre informazioni sul peering di rete virtuale, vedere [Peering di rete virtuale](virtual-network-peering-overview.md) e [Gestire un peering di rete virtuale](virtual-network-manage-peering.md).

È possibile [connettere il proprio computer a una rete virtuale](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) tramite una VPN e interagire con le risorse in una rete virtuale o in reti virtuali associate tramite peering. Vedere gli [esempi di script](powershell-samples.md) riutilizzabili per completare molte delle attività illustrate negli articoli relativi alle reti virtuali.