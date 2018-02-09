---
title: Creare una rete virtuale in Azure - PowerShell | Microsoft Docs
description: Apprendere a creare rapidamente una rete virtuale usando PowerShell. Una rete virtuale consente a molti tipi di risorse di Azure di comunicare privatamente tra loro.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: 
ms.topic: 
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 01/25/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 091e7e6cabf325cdd9d4289e7d22e71c583d91db
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="create-a-virtual-network-using-powershell"></a>Creare una rete virtuale usando PowerShell

Questo articolo illustra come creare una rete virtuale. Dopo aver creato una rete virtuale, si distribuiranno nella rete due macchine virtuali affinché comunichino privatamente tra loro.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-powershell.md)]

Se si sceglie di installare e usare PowerShell in locale, per questa esercitazione è necessario il modulo Azure PowerShell 5.1.1 o versione successiva. Per trovare la versione installata, eseguire ` Get-Module -ListAvailable AzureRM`. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Login-AzureRmAccount` per creare una connessione con Azure.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse di Azure con [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *stati uniti orientali*. Tutte le risorse di Azure vengono create all'interno di una posizione (o area) di Azure.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-virtual-network"></a>Crea rete virtuale

Creare una rete virtuale con [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). L'esempio seguente crea una rete virtuale predefinita denominata *myVirtualNetwork* nella posizione *EastUS*:

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/24
```

A tutte le reti virtuali vengono assegnati uno o più prefissi indirizzo. Lo spazio indirizzi è specificato nella notazione CIDR. Lo spazio indirizzi 10.0.0.0/24 include 10.0.0.0-10.0.0.254. Le reti virtuali contengono zero o più subnet. Le risorse vengono distribuite in una subnet all'interno di una rete virtuale. 

Creare una configurazione di subnet con [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). Tutte le subnet hanno un prefisso indirizzo che esiste all'interno del prefisso indirizzo della rete virtuale. In questo esempio viene creata una configurazione della subnet con lo stesso prefisso indirizzo della rete virtuale:

```azurepowershell-interactive
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name default `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Il prefisso indirizzo della subnet include 10.0.0.0-10.0.0.254, ma sono disponibili solo gli indirizzi 10.0.0.4-10.0.0.254 poiché Azure riserva i primi quattro indirizzi (0-3) e l'ultimo indirizzo di ogni subnet. Poiché il prefisso indirizzo della subnet corrisponde a quello della rete virtuale, in questa rete virtuale può esistere una sola subnet.

Scrivere la configurazione della subnet nella rete virtuale con [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), che consente di creare la subnet:

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="create-virtual-machines"></a>Creare macchine virtuali

Una rete virtuale consente a vari tipi di risorse di Azure di comunicare privatamente tra loro. Uno dei tipi di risorsa che è possibile distribuire in una rete virtuale è una macchina virtuale. Creare due macchine virtuali nella rete virtuale per poter convalidare e comprendere il funzionamento delle comunicazioni tra macchine virtuali in una rete virtuale in un passaggio successivo.

Creare una macchina virtuale con [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Quando si esegue questo passaggio vengono chieste le credenziali. I valori immessi sono configurati come nome utente e password per la macchina virtuale. La posizione in cui viene creata una macchina virtuale deve essere la stessa in cui si trova la rete virtuale. Sebbene sia così in questo articolo, non è necessario che la macchina virtuale si trovi nello stesso gruppo di risorse della rete virtuale. Il parametro `-AsJob` consente l'esecuzione del comando in background, pertanto è possibile continuare con l'attività successiva.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "default" `
    -Name "myVm1" `
    -AsJob
```

Viene restituito un output simile all'output di esempio seguente e Azure inizia a creare la macchina virtuale in background.

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command                  
--     ----            -------------   -----         -----------     --------             -------                  
1      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmVM     
```

Azure DHCP assegna automaticamente 10.0.0.4 alla macchina virtuale durante la creazione poiché è il primo indirizzo disponibile nella subnet *default*.

Creare una seconda macchina virtuale. 

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "default" `
  -Name "myVm2"
```
La creazione della macchina virtuale richiede alcuni minuti. Dopo la creazione, Azure restituisce un output sulla macchina virtuale creata. Anche se non è presente nell'output restituito, Azure ha assegnato *10.0.0.5* alla macchina virtuale *myVm2* poiché era l'indirizzo successivo disponibile nella subnet.

## <a name="connect-to-a-virtual-machine"></a>Connettersi a una macchina virtuale

Usare il comando [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) per restituire l'indirizzo IP pubblico di una macchina virtuale. Per impostazione predefinita, Azure assegna a ogni macchina virtuale un indirizzo IP pubblico e instradabile su Internet. L'indirizzo IP pubblico viene assegnato alla macchina virtuale da un [pool di indirizzi assegnato a ogni area di Azure](https://www.microsoft.com/download/details.aspx?id=41653). Azure sa quale indirizzo IP pubblico viene assegnato a una macchina virtuale, mentre il sistema operativo in esecuzione in una macchina virtuale non è a conoscenza di eventuali indirizzi IP pubblici assegnati. L'esempio seguente restituisce l'indirizzo IP pubblico della macchina virtuale *myVm1*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -Name myVm1 -ResourceGroupName myResourceGroup | Select IpAddress
```

Usare il comando seguente per creare una sessione desktop remoto con la macchina virtuale *myVm1* dal computer locale. Sostituire `<publicIpAddress>` con l'indirizzo IP restituito dal comando precedente.

```
mstsc /v:<publicIpAddress>
```

Viene creato, scaricato nel computer e aperto un file Remote Desktop Protocol con estensione rdp. Immettere il nome utente e la password specificati al momento della creazione della macchina virtuale e quindi fare clic su **OK**. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Fare clic su **Sì** o **Continua** per procedere con la connessione.

## <a name="validate-communication"></a>Convalidare la comunicazione

Il tentativo di eseguire il ping a una macchina virtuale di Windows ha esito negativo poiché per impostazione predefinita il ping non è consentito in Windows Firewall. Per consentire il ping per *myVm1*, immettere il comando seguente da un prompt dei comandi:

```
netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
```

Per convalidare la comunicazione con *myVm2*, immettere il comando seguente da un prompt dei comandi nella macchina virtuale *myVm1*. Specificare le credenziali usate durante la creazione della macchina virtuale e quindi completare la connessione:

```
mstsc /v:myVm2
```

La connessione desktop remoto ha esito positivo poiché a entrambe le macchine virtuali sono stati assegnati indirizzi IP privati dalla subnet *default* e poiché per impostazione predefinita il desktop remoto è consentito in Windows Firewall. È possibile connettersi a *myVm2* tramite il nome host poiché Azure garantisce automaticamente la risoluzione dei nomi DNS per tutti gli host all'interno di una rete virtuale. Da un prompt dei comandi eseguire il ping a *myVm1* da *myVm2*.

```
ping myvm1
```

Il ping ha esito positivo poiché è stato consentito in Windows Firewall nella macchina virtuale *myVm1* in un passaggio precedente. Per verificare le comunicazioni verso l'esterno su Internet, immettere il comando seguente:

```
ping bing.com
```

Si riceveranno quattro risposte da bing.com. Per impostazione predefinita, tutte le macchine virtuali in una rete virtuale possono comunicare verso l'esterno su Internet.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il gruppo di risorse e tutte le risorse in esso contenute non sono più necessari, usare il comando [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) per rimuoverli. Chiudere la sessione desktop remoto e quindi eseguire il comando seguente dal computer per eliminare il gruppo di risorse:

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stata distribuita una rete virtuale predefinita con una subnet e due macchine virtuali. Per informazioni su come creare una rete virtuale personalizzata con più subnet ed eseguire attività di gestione di base della rete virtuale, procedere all'esercitazione per la creazione e la gestione di una rete virtuale personalizzata.


> [!div class="nextstepaction"]
> [Create a custom virtual network and manage it](virtual-networks-create-vnet-arm-pportal.md#powershell) (Creare e gestire una rete virtuale personalizzata)
