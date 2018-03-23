---
title: Creare una rete virtuale di Azure - PowerShell | Microsoft Docs
description: Apprendere a creare rapidamente una rete virtuale usando PowerShell. Una rete virtuale consente alle risorse di Azure, come le macchine virtuali, di comunicare privatamente tra loro e con Internet.
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
ms.date: 03/09/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 13d36e6861a30473e6cb5d54d94a3c23a1e4cc59
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="create-a-virtual-network-using-powershell"></a>Creare una rete virtuale usando PowerShell

Una rete virtuale consente alle risorse di Azure, come le macchine virtuali (VM), di comunicare privatamente tra loro e con Internet. Questo articolo illustra come creare una rete virtuale. Dopo avere creato una rete virtuale, si distribuiscono due VM nella rete virtuale. Quindi si effettua la connessione a una VM da Internet e si comunica privatamente tra le due VM.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-powershell.md)]

Se si sceglie di installare e usare PowerShell in locale, per questo articolo è necessario il modulo AzureRM PowerShell 5.4.1 o versione successiva. Per trovare la versione installata, eseguire ` Get-Module -ListAvailable AzureRM`. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Login-AzureRmAccount` per creare una connessione con Azure.

## <a name="create-a-virtual-network"></a>Crea rete virtuale

Prima di poter creare una rete virtuale, è necessario creare un gruppo di risorse per contenere la rete virtuale. Creare un gruppo di risorse con [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *stati uniti orientali*.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

Creare una rete virtuale con [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). L'esempio seguente crea una rete virtuale predefinita denominata *myVirtualNetwork* nella posizione *EastUS*:

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Le risorse di Azure vengono distribuite in una subnet all'interno di una rete virtuale, pertanto è necessario creare una subnet. Creare una configurazione di subnet con [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). 

```azurepowershell-interactive
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name default `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Scrivere la configurazione di subnet nella rete virtuale con [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), che consente di creare la subnet nella rete virtuale:

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="create-virtual-machines"></a>Creare macchine virtuali

Creare due VM nella rete virtuale:

### <a name="create-the-first-vm"></a>Creare la prima VM

Creare una VM con [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Quando si esegue il comando seguente, vengono chieste le credenziali. I valori immessi sono configurati come nome utente e password per la VM. L'opzione `-AsJob` crea la VM in background, pertanto è possibile continuare con il passaggio successivo.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "default" `
    -Name "myVm1" `
    -AsJob
```

Viene restituito un output simile all'output di esempio seguente e Azure inizia a creare la VM in background.

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command                  
--     ----            -------------   -----         -----------     --------             -------                  
1      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmVM     
```

### <a name="create-the-second-vm"></a>Creare la seconda VM 

Immettere il comando seguente:

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "default" `
  -Name "myVm2"
```

La creazione della VM richiede alcuni minuti. Non proseguire con il passaggio successivo fino a quando il comando precedente non sarà stato eseguito e l'output non sarà stato restituito a PowerShell.

## <a name="connect-to-a-vm-from-the-internet"></a>Connettersi a una VM da Internet

Usare [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) per restituire l'indirizzo IP pubblico di una VM. L'esempio seguente restituisce l'indirizzo IP pubblico della VM *myVm1*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Sostituire `<publicIpAddress>` nel comando seguente con l'indirizzo IP pubblico restituito dal comando precedente e quindi immettere il comando seguente: 

```
mstsc /v:<publicIpAddress>
```

Viene creato e scaricato nel computer un file Remote Desktop Protocol con estensione rdp. Aprire il file con estensione rdp scaricato. Quando richiesto, selezionare **Connetti**. Immettere il nome utente e la password specificati al momento della creazione della VM. Potrebbe essere necessario selezionare **Altre opzioni**, quindi **Usa un altro account** per specificare le credenziali immesse al momento della creazione della VM. Selezionare **OK**. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Se viene visualizzato l'avviso, selezionare **Sì** o **Continua** per procedere con la connessione.

## <a name="communicate-privately-between-vms"></a>Comunicare privatamente tra VM

Da PowerShell nella VM *myVm1* immettere `ping myvm2`. Il ping non riesce, dal momento che usa il protocollo Internet Control Message Protocol (ICMP) e ICMP non è consentito in Windows Firewall per impostazione predefinita.

Per consentire a *myVm2* di effettuare il ping in *myVm1* in un passaggio successivo, immettere il comando seguente da PowerShell, che consente il protocollo ICMP in ingresso in Windows Firewall:

```powershell
New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
```

Chiudere la connessione Desktop remoto a *myVm1*. 

Completare nuovamente i passaggi descritti in [Connettersi a una VM da Internet](#connect-to-a-vm-from-the-internet), ma questa volta connettersi a *myVm2*. 

Da un prompt dei comandi nella VM *myVm2* immettere `ping myvm1`.

Si ricevono risposte da *myVm1*, poiché il protocollo ICMP è stato consentito in Windows Firewall nella VM *myVm1* in un passaggio precedente.

Chiudere la connessione Desktop remoto a *myVm2*.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il gruppo di risorse e tutte le risorse in esso contenute non sono più necessari, è possibile usare [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) per rimuoverli:

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono state create una rete virtuale predefinita e due VM. È stata effettuata la connessione a una VM da Internet e si è comunicato privatamente tra le due VM. Per altre informazioni sulle impostazioni della rete virtuale, vedere [Manage a virtual network](manage-virtual-network.md) (Gestire una rete virtuale). 

Per impostazione predefinita, Azure consente comunicazioni private senza restrizioni tra le macchine virtuali, ma permette solo connessioni Desktop remoto in ingresso alle VM Windows da Internet. Per informazioni su come consentire o limitare i diversi tipi di comunicazione di rete da e verso le VM, passare all'esercitazione successiva.

> [!div class="nextstepaction"]
> [Filtrare il traffico di rete](virtual-networks-create-nsg-arm-ps.md)
