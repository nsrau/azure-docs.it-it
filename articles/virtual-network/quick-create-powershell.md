---
title: 'Creare una rete virtuale: guida introduttiva - Azure PowerShell'
titlesuffix: Azure Virtual Network
description: Questa guida introduttiva illustra come creare una rete virtuale usando il portale di Azure. Una rete virtuale consente alle risorse di Azure, come le macchine virtuali, di comunicare privatamente tra loro e con Internet.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
tags: azure-resource-manager
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.service: virtual-network
ms.devlang: ''
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/04/2018
ms.author: jdial
ms.openlocfilehash: 725e03ded6d6f2e3b5d7a41d2053f418a5933ef8
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2019
ms.locfileid: "54023244"
---
# <a name="quickstart-create-a-virtual-network-using-powershell"></a>Guida introduttiva: Creare una rete virtuale usando PowerShell

Una rete virtuale consente alle risorse di Azure, come le macchine virtuali (VM), di comunicare privatamente tra loro e con Internet. Questa guida introduttiva illustra come creare una rete virtuale. Dopo avere creato una rete virtuale, si distribuiscono due VM nella rete virtuale. Si effettua quindi la connessione alle VM da Internet e si stabilisce una comunicazione privata tramite la rete virtuale.

Se non si ha una sottoscrizione di Azure, creare ora un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-powershell.md)]

Se si decide invece di installare e usare PowerShell in locale, questa guida introduttiva richiede l'uso del modulo AzureRM PowerShell, versione 5.4.1 o successiva. Per trovare la versione installata, eseguire `Get-Module -ListAvailable AzureRM`. Consultare [Installazione del modulo Azure PowerShell](/powershell/azure/install-azurerm-ps) per informazioni relative a installazione e aggiornamento.

Infine, se si esegue PowerShell in locale, sarà necessario eseguire anche `Connect-AzureRmAccount`. Questo comando crea una connessione con Azure.

## <a name="create-a-resource-group-and-a-virtual-network"></a>Creare un gruppo di risorse e una rete virtuale

Esiste un numero limitato di passaggi da esaminare nei dettagli per configurare il gruppo di risorse e la rete virtuale.

### <a name="create-the-resource-group"></a>Creare il gruppo di risorse.

Per poter creare una rete virtuale, è prima necessario creare un gruppo di risorse per l'hosting della rete virtuale. Creare un gruppo di risorse con [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup). Questo esempio crea un gruppo di risorse denominato *myResourceGroup* nella posizione *eastus*:

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

### <a name="create-the-virtual-network"></a>Creare la rete virtuale

Creare una rete virtuale con [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). Questo esempio crea una rete virtuale predefinita denominata *myVirtualNetwork* nella posizione *EastUS*:

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Aggiungere una subnet

Azure distribuisce risorse in una subnet all'interno di una rete virtuale, pertanto è necessario creare una subnet. Creare una configurazione di una subnet denominata *default* con [Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig):

```azurepowershell-interactive
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name default `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

### <a name="associate-the-subnet-to-the-virtual-network"></a>Associare la subnet alla rete virtuale

È possibile scrivere la configurazione della subnet nella rete virtuale con [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork). Questo comando crea la subnet:

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="create-virtual-machines"></a>Creare macchine virtuali

Creare due VM nella rete virtuale.

### <a name="create-the-first-vm"></a>Creare la prima VM

Creare la prima macchina virtuale con [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Quando si esegue il comandi seguente, viene chiesto di immettere le credenziali. Immettere un nome utente e una password per la macchina virtuale:

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "default" `
    -Name "myVm1" `
    -AsJob
```

L'opzione `-AsJob` consente di creare la macchina virtuale in background. È possibile continuare con il passaggio successivo.

Quando Azure inizia a creare la macchina virtuale in background, il risultato sarà qualcosa di simile a quanto segue:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmVM
```

### <a name="create-the-second-vm"></a>Creare la seconda VM

Creare la seconda macchina virtuale con questo comando:

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "default" `
  -Name "myVm2"
```

Sarà necessario creare un altro utente e un'altra password. Azure richiede alcuni minuti per creare la macchina virtuale.

> [!IMPORTANT]
> Prima di continuare con i passaggi successivi, attendere che Azure abbia finito.  La fine della procedura sarà segnalata dalla restituzione di output in PowerShell.

## <a name="connect-to-a-vm-from-the-internet"></a>Connettersi a una VM da Internet

Usare [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) per restituire l'indirizzo IP pubblico di una VM. Questo esempio restituisce l'indirizzo IP pubblico della VM *myVm1*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Aprire un prompt dei comandi nel computer locale. Eseguire il comando `mstsc`. Sostituire `<publicIpAddress>` con l'indirizzo IP pubblico ottenuto nell'ultimo passaggio:

> [!NOTE]
> Se questi comandi sono in esecuzione da un prompt di PowerShell nel computer locale, con il modulo AzureRM PowerShell versione 5.4.1 o successiva, è possibile continuare in tale interfaccia.

```cmd
mstsc /v:<publicIpAddress>
```

Un file Remote Desktop Protocol (*.rdp*) viene scaricato sul computer, mentre si apre un Desktop remoto.

1. Quando richiesto, selezionare **Connetti**.

1. Immettere il nome utente e la password specificati al momento della creazione della VM.

    > [!NOTE]
    > Potrebbe essere necessario selezionare **Altre opzioni** > **Usa un altro account** per specificare le credenziali immesse al momento della creazione della macchina virtuale.

1. Selezionare **OK**.

1. Si potrebbe ricevere un avviso del certificato. Se sì, selezionare **Sì** oppure **Continua**.

## <a name="communicate-between-vms"></a>Stabilire la comunicazione tra le macchine virtuali

1. Nel desktop remoto di *myVm1*, aprire PowerShell.

1. Immettere `ping myVm2`.

    Si otterrà un risultato simile a quanto segue:

    ```powershell
    PS C:\Users\myVm1> ping myVm2

    Pinging myVm2.ovvzzdcazhbu5iczfvonhg2zrb.bx.internal.cloudap
    Request timed out.
    Request timed out.
    Request timed out.
    Request timed out.

    Ping statistics for 10.0.0.5:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
    ```

    Il ping ha esito negativo perché usa il protocollo ICMP (Internet Control Message Protocol). Per impostazione predefinita, il protocollo ICMP non dispone dell'autorizzazione del firewall di Windows.

1. Per consentire a *myVm2* di eseguire il ping su *myVm1* in un passaggio successivo, immettere questo comando:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

    Questo comando permette il passaggio di dati in ingresso da parte del protocollo ICMP attraverso il firewall di Windows.

1. Chiudere la connessione Desktop remoto a *myVm1*.

1. Ripetere i passaggi di [Connettersi a una macchina virtuale da internet](#connect-to-a-vm-from-the-internet). Questa volta, connettersi a *myVm2*.

1. Da un prompt dei comandi nella VM *myVm2* immettere `ping myvm1`.

    Si otterrà un risultato simile a quanto segue:

    ```cmd
    C:\windows\system32>ping myVm1

    Pinging myVm1.e5p2dibbrqtejhq04lqrusvd4g.bx.internal.cloudapp.net [10.0.0.4] with 32 bytes of data:
    Reply from 10.0.0.4: bytes=32 time=2ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128

    Ping statistics for 10.0.0.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 2ms, Average = 0ms
    ```

    Si ricevono risposte da *myVm1*, poiché il protocollo ICMP è stato consentito in Windows Firewall nella VM *myVm1* in un passaggio precedente.

1. Chiudere la connessione Desktop remoto a *myVm2*.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non si ha più bisogno della rete e delle macchine virtuali, usare [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) per rimuovere il gruppo di risorse e tutte le risorse contenute in esso:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva sono state create una rete virtuale predefinita e due VM. È stata effettuata la connessione a una VM da Internet ed è stata stabilita una comunicazione privata tra la VM e un'altra VM. Per altre informazioni sulle impostazioni della rete virtuale, vedere [Manage a virtual network](manage-virtual-network.md) (Gestire una rete virtuale).

Azure consente comunicazioni private senza restrizioni tra le macchine virtuali. Tuttavia, per impostazione predefinita, Azure permette solo connessioni Desktop remoto in ingresso alle VM Windows da Internet. Per altre informazioni sui diversi tipi di comunicazioni della rete delle macchine virtuali, vedere l'esercitazione [Filtrare il traffico di rete](tutorial-filter-network-traffic.md).
