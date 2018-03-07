---
title: Reti virtuali di Azure e macchine virtuali Windows | Microsoft Docs
description: 'Esercitazione: gestire reti virtuali di Azure e macchine virtuali Windows con Azure PowerShell'
services: virtual-machines-windows
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/27/2018
ms.author: davidmu
ms.custom: mvc
ms.openlocfilehash: 3a59d85ea19ba6670ffbb60aa9b764560a3567a0
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="manage-azure-virtual-networks-and-windows-virtual-machines-with-azure-powershell"></a>Gestire reti virtuali di Azure e macchine virtuali Windows con Azure PowerShell

Le macchine virtuali di Azure usano la rete di Azure per la comunicazione di rete interna ed esterna. Questa esercitazione illustra la distribuzione di due macchine virtuali e la configurazione della rete di Azure per tali VM. Gli esempi in questa esercitazione presuppongono che le VM ospitino un'applicazione Web con un back-end di database, ma nell'esercitazione non viene distribuita un'applicazione. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una rete virtuale e una subnet
> * Creare un indirizzo IP pubblico
> * Creare una macchina virtuale front-end
> * Proteggono il traffico di rete
> * Creare la VM back-end



Questa esercitazione richiede il modulo AzureRM.Compute 4.3.1 o versioni successive. Eseguire `Get-Module -ListAvailable AzureRM.Compute` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="vm-networking-overview"></a>Panoramica della rete per le VM

Le reti virtuali di Azure consentono connessioni di rete sicure tra macchine virtuali, Internet e altri servizi di Azure come il database SQL di Azure. Le reti virtuali sono suddivise in segmenti logici denominati subnet. Le subnet vengono usate per controllare il flusso di rete e come limite di sicurezza. Quando si distribuisce una VM, questa include in genere un'interfaccia di rete virtuale collegata a una subnet.

Durante il completamento di questa esercitazione è possibile visualizzare queste risorse create:

![Rete virtuale con due subnet](./media/tutorial-virtual-network/networktutorial.png)

- *myVNet*: la rete virtuale usata dalle macchine virtuali per comunicare tra loro e con Internet.
- *myFrontendSubnet*: la subnet in *myVNet* usata dalle risorse front-end.
- *myPublicIPAddress*: l'indirizzo IP pubblico usato per accedere a *myFrontendVM* da Internet.
- *myFrontentNic*: l'interfaccia di rete usata da *myFrontendVM* per comunicare con *myBackendVM*.
- *myFrontendVM*: la macchina virtuale usata per la comunicazione tra Internet e *myBackendVM*.
- *myBackendNSG*: il gruppo di sicurezza di rete che controlla la comunicazione tra *myFrontendVM* e *myBackendVM*.
- *myBackendSubnet*: la subnet associata a *myBackendNSG* e usata dalle risorse back-end.
- *myBackendNic*: l'interfaccia di rete usata da *myBackendVM* per comunicare con *myFrontendVM*.
- *myBackendVM*: la macchina virtuale che usa la porta 1433 per comunicare con *myFrontendVM*.


## <a name="create-a-virtual-network-and-subnet"></a>Creare una rete virtuale e una subnet

Per questa esercitazione viene creata una singola rete virtuale con due subnet: una subnet front-end per ospitare un'applicazione Web e una subnet back-end per ospitare un server di database.

Per poter creare una rete virtuale è prima necessario creare un gruppo di risorse con [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). L'esempio seguente crea un gruppo di risorse denominato *myRGNetwork* nella posizione *EastUS*:

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myRGNetwork -Location EastUS
```

### <a name="create-subnet-configurations"></a>Creare configurazioni subnet

Creare una configurazione di subnet denominata *myFrontendSubnet* con [New AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig):

```azurepowershell-interactive
$frontendSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myFrontendSubnet `
  -AddressPrefix 10.0.0.0/24
```

Creare poi una configurazione di subnet denominata *myBackendSubnet*:

```azurepowershell-interactive
$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24
```

### <a name="create-virtual-network"></a>Creare una rete virtuale

Creare una rete virtuale denominata *myVNet* che usa *myFrontendSubnet* e *myBackendSubnet* con [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork):

```azurepowershell-interactive
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $frontendSubnet, $backendSubnet
```

A questo punto, è stata creata una rete che è stata segmentata in due subnet, una per i servizi front-end e un'altra per i servizi back-end. Nella sezione successiva, le macchine virtuali verranno create e connesse a queste subnet.

## <a name="create-a-public-ip-address"></a>Creare un indirizzo IP pubblico

Un indirizzo IP pubblico consente alle risorse di Azure di essere accessibili in Internet. Il metodo di allocazione dell'indirizzo IP pubblico può essere configurato come dinamico o statico. Per impostazione predefinita, un indirizzo IP pubblico viene allocato in modo dinamico. Gli indirizzi IP dinamici vengono rilasciati quando una VM viene deallocata. Questo comportamento determina la modifica dell'indirizzo IP durante qualsiasi operazione che includa la deallocazione di una VM.

Il metodo di allocazione può essere impostato come statico affinché l'indirizzo IP resti assegnato a una macchina virtuale anche con lo stato deallocato. Quando si usa un indirizzo IP con allocazione statica, l'indirizzo IP non può essere specificato e viene invece allocato da un pool di indirizzi disponibili.

Creare un indirizzo IP pubblico denominato *myPublicIPAddress* con [New AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress):

```azurepowershell-interactive
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -AllocationMethod Dynamic `
  -Name myPublicIPAddress
```

È possibile modificare il parametro -AllocationMethod impostandolo su `Static` per assegnare un indirizzo IP pubblico statico.

## <a name="create-a-front-end-vm"></a>Creare una macchina virtuale front-end

Per comunicare in una rete virtuale, una macchina virtuale deve avere un'interfaccia di rete (NIC) virtuale. Creare un'interfaccia di rete con [New AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface):

```azurepowershell-interactive
$frontendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontend `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

Impostare il nome utente e la password necessari per l'account amministratore della macchina virtuale con [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential). Usare queste credenziali per connettersi alla macchina virtuale in una procedura aggiuntiva:

```azurepowershell-interactive
$cred = Get-Credential
```

Creare le macchine virtuali usando [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm).

```azurepowershell-interactive
New-AzureRmVM `
   -Credential $cred `
   -Name myFrontend `
   -PublicIpAddressName myPublicIPAddress `
   -ResourceGroupName myRGNetwork `
   -Location "EastUS" `
   -Size Standard_D1 `
   -SubnetName myFrontendSubnet `
   -VirtualNetworkName myVNet
```

## <a name="secure-network-traffic"></a>Proteggono il traffico di rete

Un gruppo di sicurezza di rete (NSG) contiene un elenco di regole di sicurezza che consentono o rifiutano il traffico di rete verso le risorse connesse a reti virtuali di Azure. I gruppi di sicurezza di rete possono essere associati a subnet o singole interfacce di rete. Quando un gruppo di sicurezza di rete è associato a un'interfaccia di rete, si applica solo alla VM associata. Quando un gruppo di sicurezza di rete è associato a una subnet, le regole si applicano a tutte le risorse connesse alla subnet.

### <a name="network-security-group-rules"></a>Regole dei gruppi di sicurezza di rete

Le regole dei gruppi di sicurezza di rete definiscono le porte di rete su cui il traffico viene consentito o negato. Le regole possono includere intervalli di indirizzi IP di origine e di destinazione in modo da controllare il traffico tra subnet o sistemi specifici. Le regole dei gruppi di sicurezza di rete possono includere anche una priorità, compresa tra 1 e 4096. Le regole vengono valutate in ordine di priorità. Una regola con priorità 100 viene valutata prima di una con priorità 200.

Tutti i gruppi di sicurezza di rete contengono un set di regole predefinite. Le regole predefinite non possono essere eliminate, ma poiché hanno la priorità più bassa, è possibile eseguirne l'override con le regole create dall'utente.

- **Rete virtuale**: il traffico che ha origine e termina in una rete virtuale è consentito sia in ingresso che in uscita.
- **Internet**: il traffico in uscita è consentito, mentre il traffico in ingresso viene bloccato.
- **Servizio di bilanciamento del carico**: viene consentito al servizio di bilanciamento del carico di Azure di verificare tramite probe l'integrità delle VM e delle istanze del ruolo. Se non si usa un set con bilanciamento del carico, è possibile eseguire l'override di questa regola.

### <a name="create-network-security-groups"></a>Creare gruppi di sicurezza di rete

Creare una regola in ingresso denominata *myFrontendNSGRule* per consentire il traffico Web in ingresso su *myFrontendVM* con [New AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig):

```azurepowershell-interactive
$nsgFrontendRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myFrontendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow
```

È possibile limitare il traffico interno verso *myBackendVM* solo da *myFrontendVM* creando un gruppo di sicurezza di rete per la subnet back-end. Nell'esempio seguente viene creata una regola del gruppo di sicurezza di rete denominata *myBackendNSGRule*:

```azurepowershell-interactive
$nsgBackendRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myBackendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix 10.0.0.0/24 `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 1433 `
  -Access Allow
```

Aggiungere un gruppo di sicurezza di rete denominato *myFrontendNSG* con [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup):

```azurepowershell-interactive
$nsgFrontend = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontendNSG `
  -SecurityRules $nsgFrontendRule
```

Aggiungere ora un gruppo di sicurezza di rete denominato *myBackendNSG* con New-AzureRmNetworkSecurityGroup:

```azurepowershell-interactive
$nsgBackend = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackendNSG `
  -SecurityRules $nsgBackendRule
```

Aggiungere i gruppi di sicurezza di rete alle subnet:

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Name myVNet
$frontendSubnet = $vnet.Subnets[0]
$backendSubnet = $vnet.Subnets[1]
$frontendSubnetConfig = Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name myFrontendSubnet `
  -AddressPrefix $frontendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgFrontend
$backendSubnetConfig = Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name myBackendSubnet `
  -AddressPrefix $backendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgBackend
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

## <a name="create-a-back-end-vm"></a>Creare una macchina virtuale back-end

Il modo più semplice per creare la macchina virtuale back-end per questa esercitazione consiste nell'usare un'immagine di SQL Server. Questa esercitazione si limita a creare la VM con il server di database e non include informazioni sull'accesso al database.

Creare *myBackendNic*:

```azurepowershell-interactive
$backendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackend `
  -SubnetId $vnet.Subnets[1].Id
```

Impostare il nome utente e la password necessari per l'account amministratore della VM con Get-Credential:

```azurepowershell-interactive
$cred = Get-Credential
```

Creare *myBackendVM*.

```azurepowershell-interactive
New-AzureRmVM `
   -Credential $cred `
   -Name myBackend `
   -ImageName "MicrosoftSQLServer:SQL2016SP1-WS2016:Enterprise:latest" `
   -ResourceGroupName myRGNetwork `
   -Location "EastUS" `
   -SubnetName myFrontendSubnet `
   -VirtualNetworkName myVNet
```

L'immagine, in cui è installato SQL Server, non viene usata in questa esercitazione. È inclusa per illustrare come è possibile configurare una VM per il traffico Web e una VM per la gestione di database.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state create e protette reti di Azure in relazione a macchine virtuali. 

> [!div class="checklist"]
> * Creare una rete virtuale e una subnet
> * Creare un indirizzo IP pubblico
> * Creare una macchina virtuale front-end
> * Proteggono il traffico di rete
> * Creare una macchina virtuale back-end

Passare all'esercitazione successiva per informazioni sul monitoraggio della protezione dei dati nelle macchine virtuali con Backup di Azure.

> [!div class="nextstepaction"]
> [Eseguire il backup di macchine virtuali Windows in Azure](./tutorial-backup-vms.md)
