---
title: Connettere reti virtuali con il peering reti virtuali - PowerShell | Microsoft Docs
description: Informazioni su come connettere reti virtuali con il peering reti virtuali.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: ''
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: b067dfd6d50b61614c2f3de2fa0e159cd645f9eb
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-powershell"></a>Connettere reti virtuali con il peering reti virtuali usando PowerShell

È possibile connettere due reti virtuali tra loro con il peering reti virtuali. Dopo che è stato eseguito il peering, le risorse delle due reti virtuali possono comunicare tra loro con la stessa larghezza di banda e la stessa latenza che sarebbero disponibili se si trovassero nella stessa rete virtuale. In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Creare due reti virtuali
> * Connettere due reti virtuali con un peering di reti virtuali
> * Distribuire una macchina virtuale in ogni rete virtuale
> * Stabilire la comunicazione tra le macchine virtuali

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Se si sceglie di installare e usare PowerShell in locale, per questo articolo è necessario il modulo Azure PowerShell 5.4.1 o versione successiva. Eseguire ` Get-Module -ListAvailable AzureRM` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Login-AzureRmAccount` per creare una connessione con Azure. 

## <a name="create-virtual-networks"></a>Creare reti virtuali

Prima di creare una rete virtuale, è necessario creare un gruppo di risorse per la rete virtuale e tutte le altre risorse create in questo articolo. Creare un gruppo di risorse con [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *stati uniti orientali*.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Creare una rete virtuale con [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). L'esempio seguente crea una rete virtuale denominata *myVirtualNetwork1* con prefisso di indirizzo *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork1 = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork1 `
  -AddressPrefix 10.0.0.0/16
```

Creare una configurazione di subnet con [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). L'esempio seguente crea una configurazione di subnet con prefisso di indirizzo 10.0.0.0/24:

```azurepowershell-interactive
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork1
```

Scrivere la configurazione della subnet nella rete virtuale con [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), che consente di creare la subnet:

```azurepowershell-interactive
$virtualNetwork1 | Set-AzureRmVirtualNetwork
```

Creare una rete virtuale con il prefisso di indirizzo 10.1.0.0/16 e una subnet:

```azurepowershell-interactive
# Create the virtual network.
$virtualNetwork2 = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork2 `
  -AddressPrefix 10.1.0.0/16

# Create the subnet configuration.
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork2

# Write the subnet configuration to the virtual network.
$virtualNetwork2 | Set-AzureRmVirtualNetwork
```

## <a name="peer-virtual-networks"></a>Eseguire il peering delle reti virtuali

Creare un peering con [Add-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/add-azurermvirtualnetworkpeering). L'esempio seguente crea il peering da *myVirtualNetwork1* a *myVirtualNetwork2*.

```azurepowershell-interactive
Add-AzureRmVirtualNetworkPeering `
  -Name myVirtualNetwork1-myVirtualNetwork2 `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id
```

Nell'output restituito dopo l'esecuzione del comando precedente si può osservare che il valore di **PeeringState** è *Initiated*. Il peering rimane nello stato *Initiated* finché non si crea il peering da *myVirtualNetwork2* a *myVirtualNetwork1*. Creare un peering da *myVirtualNetwork2* a *myVirtualNetwork1*. 

```azurepowershell-interactive
Add-AzureRmVirtualNetworkPeering `
  -Name myVirtualNetwork2-myVirtualNetwork1 `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id
```

Nell'output restituito dopo l'esecuzione del comando precedente si può osservare che il valore di **PeeringState** è *Connected*. Azure avrà modificato anche lo stato del peering *myVirtualNetwork1-myVirtualNetwork2* in *Connected*. Verificare che lo stato del peering *myVirtualNetwork1-myVirtualNetwork2* sia stato modificato in *Connected* con [Get-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering).

```azurepowershell-interactive
Get-AzureRmVirtualNetworkPeering `
  -ResourceGroupName myResourceGroup `
  -VirtualNetworkName myVirtualNetwork1 `
  | Select PeeringState
```

Le risorse in una rete virtuale non possono comunicare con le risorse nell'altra rete virtuale finché il valore di **PeeringState** relativo ai peering in entrambe le reti virtuali non è *Connected*. 

## <a name="create-virtual-machines"></a>Creare macchine virtuali

Creare una macchina virtuale in ogni rete virtuale per poter stabilire la comunicazione tra di esse in un passaggio successivo.

### <a name="create-the-first-vm"></a>Creare la prima VM

Creare una VM con [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). L'esempio seguente crea una macchina virtuale denominata *myVm1* nella rete virtuale *myVirtualNetwork1*. L'opzione `-AsJob` crea la macchina virtuale in background, pertanto è possibile continuare con il passaggio successivo. Quando richiesto, immettere il nome utente e la password con cui si vuole accedere alla macchina virtuale.

```azurepowershell-interactive
New-AzureRmVm `
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
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork2" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm2"
```

La creazione della VM richiede alcuni minuti. Non procedere con i passaggi successivi finché Azure non crea la macchina virtuale e restituisce l'output in PowerShell.

## <a name="communicate-between-vms"></a>Stabilire la comunicazione tra le macchine virtuali

È possibile connettersi all'indirizzo IP pubblico di una macchina virtuale da Internet. Usare [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) per restituire l'indirizzo IP pubblico di una VM. L'esempio seguente restituisce l'indirizzo IP pubblico della VM *myVm1*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
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

Quando il gruppo di risorse e tutte le risorse in esso contenute non sono più necessari, usare [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) per rimuoverli.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

**<a name="register"></a>Registrarsi per l'anteprima del peering reti virtuali globale**

Il peering reti virtuali nella stessa area è disponibile a livello generale. Il peering di reti virtuali in aree diverse è attualmente disponibile in anteprima. Vedere gli [aggiornamenti delle reti virtuali](https://azure.microsoft.com/updates/?product=virtual-network) per le aree disponibili. Per eseguire il peering di reti virtuali tra aree diverse, è prima necessario registrarsi per l'anteprima completando i passaggi seguenti all'interno della sottoscrizione in cui si trova ogni rete virtuale di cui si vuole eseguire il peering:

1. Registrare per l'anteprima la sottoscrizione in cui si trova ognuna delle reti virtuali di cui si vuole eseguire il peering immettendo i comandi seguenti:

    ```powershell-interactive
    Register-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    
    Register-AzureRmResourceProvider `
      -ProviderNamespace Microsoft.Network
    ```
2. Verificare di essere registrati per l'anteprima immettendo il comando seguente:

    ```powershell-interactive    
    Get-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    ```

    Se si tenta di eseguire il peering di reti virtuali in aree diverse prima di ricevere **Registered** come output di **RegistrationState** per entrambe le sottoscrizioni dopo l'immissione del comando precedente, il peering non riesce.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come connettere due reti virtuali con il peering reti virtuali. In questo articolo è stato illustrato come connettere due reti virtuali, nella stessa posizione di Azure, con il peering di reti virtuali. È anche possibile creare un peering di reti virtuali in [aree diverse](#register) e in [sottoscrizioni di Azure diverse](create-peering-different-subscriptions.md#portal) e creare [progettazioni di rete di tipo hub-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) con peering. Prima di eseguire il peering di reti virtuali di produzione, è consigliabile acquisire familiarità con la [panoramica del peering](virtual-network-peering-overview.md), la [gestione del peering](virtual-network-manage-peering.md) e i [limiti delle reti virtuali](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

È possibile [connettere il proprio computer a una rete virtuale](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) tramite una VPN e interagire con le risorse in una rete virtuale o in reti virtuali associate tramite peering. Vedere gli esempi di script riutilizzabili per completare molte delle attività illustrate negli articoli relativi alle reti virtuali.

> [!div class="nextstepaction"]
> [Esempi di script per le reti virtuali](../networking/powershell-samples.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
