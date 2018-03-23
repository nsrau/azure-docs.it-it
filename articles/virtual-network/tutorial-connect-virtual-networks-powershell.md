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
ms.date: 03/06/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: c7b3fa2b566ab02e7fb4a03055db83f1545895e8
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-powershell"></a>Connettere reti virtuali con il peering reti virtuali usando PowerShell

È possibile connettere due reti virtuali tra loro con il peering reti virtuali. Dopo che è stato eseguito il peering, le risorse delle due reti virtuali possono comunicare tra loro con la stessa larghezza di banda e la stessa latenza che sarebbero disponibili se si trovassero nella stessa rete virtuale. Questo articolo illustra la creazione e il peering di due reti virtuali. Si apprenderà come:

> [!div class="checklist"]
> * Creare due reti virtuali
> * Creare un peering tra le reti virtuali
> * Testare il peering

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Se si sceglie di installare e usare PowerShell in locale, per questo articolo è necessario il modulo Azure PowerShell 3.6 o versione successiva. Eseguire ` Get-Module -ListAvailable AzureRM` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Login-AzureRmAccount` per creare una connessione con Azure. 

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

Il prefisso di indirizzo per la rete virtuale *myVirtualNetwork2* non si sovrappone a quello della rete virtuale *myVirtualNetwork1*. Non è possibile eseguire il peering di reti virtuali i cui prefissi di indirizzo si sovrappongono.

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

I peering vengono stabiliti tra due reti virtuali, ma non sono transitivi. Di conseguenza, se si vuole eseguire il peering da *myVirtualNetwork2* a *myVirtualNetwork3*, ad esempio, è necessario creare un peering aggiuntivo tra le reti virtuali *myVirtualNetwork2* e *myVirtualNetwork3*. Anche se *myVirtualNetwork1* è connessa tramite peering a *myVirtualNetwork2*, le risorse in *myVirtualNetwork1* potrebbero accedere alle risorse di *myVirtualNetwork3* solo se *myVirtualNetwork1* fosse connessa tramite peering anche con *myVirtualNetwork3*. 

Prima di eseguire il peering di reti virtuali di produzione, è consigliabile acquisire familiarità con la [panoramica del peering](virtual-network-peering-overview.md), la [gestione del peering](virtual-network-manage-peering.md) e i [limiti delle reti virtuali](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Questo articolo illustra un peering tra due reti virtuali nella stessa sottoscrizione e località, ma è possibile eseguire il peering anche di reti virtuali in [aree diverse](#register) e in [sottoscrizioni di Azure diverse](create-peering-different-subscriptions.md#powershell). Si possono anche creare [progettazioni di rete di tipo hub-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) con peering.

## <a name="test-peering"></a>Testare il peering

Per testare la comunicazione di rete tra macchine virtuali in reti virtuali diverse tramite un peering, distribuire una macchina virtuale in ogni subnet e quindi avviare la comunicazione tra le macchine virtuali. 

### <a name="create-virtual-machines"></a>Creare macchine virtuali

Creare una macchina virtuale in ogni rete virtuale per poter convalidare la comunicazione reciproca in un passaggio successivo.

Creare una macchina virtuale con [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). L'esempio seguente crea una macchina virtuale denominata *myVm1* nella rete virtuale *myVirtualNetwork1*. L'opzione `-AsJob` crea la macchina virtuale in background, pertanto è possibile continuare con il passaggio successivo. Quando richiesto, immettere il nome utente e la password con cui si vuole accedere alla macchina virtuale.

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

Azure assegna automaticamente 10.0.0.4 come indirizzo IP privato della macchina virtuale, perché 10.0.0.4 è il primo indirizzo IP disponibile nella subnet *Subnet1* di *myVirtualNetwork1*. 

Creare una macchina virtuale nella rete virtuale *myVirtualNetwork2*.

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork2" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm2"
```

La creazione della macchina virtuale richiede alcuni minuti. Anche se non è visibile nell'output restituito, Azure ha assegnato 10.1.0.4 come indirizzo IP privato della macchina virtuale, perché 10.1.0.4 è il primo indirizzo IP disponibile nella subnet *Subnet1* di *myVirtualNetwork2*. 

Non procedere con i passaggi successivi finché Azure non crea la macchina virtuale e restituisce l'output in PowerShell.

### <a name="test-virtual-machine-communication"></a>Testare la comunicazione tra le macchine virtuali

È possibile connettersi all'indirizzo IP pubblico di una macchina virtuale da Internet. Usare [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) per restituire l'indirizzo IP pubblico di una macchina virtuale. L'esempio seguente restituisce l'indirizzo IP pubblico della macchina virtuale *myVm1*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

Usare il comando seguente per creare una sessione desktop remoto con la macchina virtuale *myVm1* dal computer locale. Sostituire `<publicIpAddress>` con l'indirizzo IP restituito dal comando precedente.

```
mstsc /v:<publicIpAddress>
```

Viene creato, scaricato nel computer e aperto un file Remote Desktop Protocol con estensione rdp. Immettere il nome utente e la password e quindi fare clic su **OK**. Per specificare le credenziali immesse durante la creazione della macchina virtuale, potrebbe essere necessario selezionare **More choices** (Altre opzioni) e quindi **Usa un account diverso**. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Fare clic su **Sì** o **Continua** per procedere con la connessione.

Al prompt dei comandi abilitare il ping tramite Windows Firewall per poter effettuare il ping di questa macchina virtuale da *myVm2* in un passaggio successivo.

```
netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
```

Anche se in questo articolo viene usato il ping a scopo di test, non è consigliabile consentire il protocollo ICMP tramite Windows Firewall per le distribuzioni di produzione.

Per connettersi alla macchina virtuale *myVm2*, immettere il comando seguente al prompt dei comandi nella macchina virtuale *myVm1*:

```
mstsc /v:10.1.0.4
```

Dato che è stato abilitato il ping in *myVm1*, è ora possibile effettuare il ping in base all'indirizzo IP dal prompt dei comandi nella macchina virtuale *myVm2*:

```
ping 10.0.0.4
```

Si riceveranno quattro risposte. Se si effettua il ping in base al nome della macchina virtuale (*myVm1*) anziché in base all'indirizzo IP, il ping non riesce perché *myVm1* è un nome host sconosciuto. La risoluzione dei nomi predefinita di Azure funziona tra macchine virtuali all'interno della stessa rete virtuale, ma non tra macchine virtuali in reti virtuali diverse. Per risolvere i nomi tra reti virtuali, è necessario [distribuire un server DNS personalizzato](virtual-networks-name-resolution-for-vms-and-role-instances.md) oppure usare [domini privati di DNS di Azure](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Disconnettere le sessioni RDP con *myVm1* e *myVm2*.

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

In questo articolo è stato illustrato come connettere due reti virtuali con il peering reti virtuali. È possibile [connettere il proprio computer a una rete virtuale](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) tramite una VPN e interagire con le risorse in una rete virtuale o in reti virtuali associate tramite peering.

Vedere gli esempi di script riutilizzabili per completare molte delle attività illustrate negli articoli relativi alle reti virtuali.

> [!div class="nextstepaction"]
> [Esempi di script per le reti virtuali](../networking/powershell-samples.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
