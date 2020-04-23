---
title: Aprire le porte a una macchina virtuale usando Azure PowerShellOpen ports to a VM using Azure PowerShell
description: Informazioni su come aprire una porta o creare un endpoint alla VM Windows tramite il modello di distribuzione di Azure Resource Manager e Azure PowerShell
author: cynthn
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.openlocfilehash: a0dcc53d84edb4dd697213106c02626df24acfd8
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869405"
---
# <a name="how-to-open-ports-and-endpoints-to-a-vm-in-azure-using-powershell"></a>Come aprire le porte e gli endpoint in una VM in Azure usando PowerShell
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Comandi rapidi
Per creare un gruppo di sicurezza di rete e le regole del controllo di accesso, è necessario che [sia installata la versione più recente di Azure PowerShell](/powershell/azureps-cmdlets-docs). È anche possibile eseguire questi passaggi usando il portale di Azure.You can also [perform these steps using the Azure portal](nsg-quickstart-portal.md).

Accedere all'account di Azure:

```powershell
Connect-AzAccount
```

Negli esempi seguenti sostituire i nomi dei parametri con i valori desiderati. I nomi dei parametri di esempio includono *myResourceGroup*, *myNetworkSecurityGroup* e *myVnet*.

Creare una regola con [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig). Nell'esempio seguente viene creata una regola denominata *myNetworkSecurityGroupRule* per consentire il traffico *tcp* sulla porta *80:*

```powershell
$httprule = New-AzNetworkSecurityRuleConfig `
    -Name "myNetworkSecurityGroupRule" `
    -Description "Allow HTTP" `
    -Access "Allow" `
    -Protocol "Tcp" `
    -Direction "Inbound" `
    -Priority "100" `
    -SourceAddressPrefix "Internet" `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 80
```

Creare quindi il gruppo di sicurezza di rete con [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup) e assegnare la regola HTTP appena creata come indicato di seguito. L'esempio seguente crea un gruppo di sicurezza di rete denominato *myNetworkSecurityGroup*:

```powershell
$nsg = New-AzNetworkSecurityGroup `
    -ResourceGroupName "myResourceGroup" `
    -Location "EastUS" `
    -Name "myNetworkSecurityGroup" `
    -SecurityRules $httprule
```

Ora si assegnerà il gruppo di sicurezza di rete a una subnet. L'esempio seguente assegna una rete virtuale esistente denominata *myVnet* alla variabile *$vnet* con [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork):

```powershell
$vnet = Get-AzVirtualNetwork `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVnet"
```

Associare il gruppo di sicurezza di rete alla subnet con [Set-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworksubnetconfig). L'esempio seguente associa la subnet denominata *mySubnet* al gruppo di sicurezza di rete:

```powershell
$subnetPrefix = $vnet.Subnets|?{$_.Name -eq 'mySubnet'}

Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name "mySubnet" `
    -AddressPrefix $subnetPrefix.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

Infine, aggiornare la rete virtuale con [Set-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetwork) affinché le modifiche abbiano effetto:

```powershell
Set-AzVirtualNetwork -VirtualNetwork $vnet
```


## <a name="more-information-on-network-security-groups"></a>Altre informazioni sui gruppi di sicurezza di rete
I comandi rapidi seguenti consentono di rendere operativo il traffico verso la VM. I gruppi di sicurezza di rete offrono numerose funzionalità efficienti e la necessaria granularità per controllare l'accesso alle risorse. Per altre informazioni, leggere l'articolo sulla [creazione di un gruppo di sicurezza di rete e di regole dell'elenco di controllo di accesso qui](tutorial-virtual-network.md#secure-network-traffic).

Per le applicazioni Web a disponibilità elevata, è consigliabile inserire le macchine virtuali dietro a un Azure Load Balancer. Il bilanciamento del carico distribuisce il traffico alle macchine virtuali, con un gruppo di sicurezza di rete che consente di filtrare il traffico. Per altre informazioni, vedere [Come bilanciare il carico per le macchine virtuali di Linux in Azure per creare un'applicazione a disponibilità elevata](tutorial-load-balancer.md).

## <a name="next-steps"></a>Passaggi successivi
In questo esempio viene creata una regola semplice per consentire il traffico HTTP. È possibile trovare informazioni sulla creazione di ambienti più dettagliati negli articoli seguenti:

* [Panoramica di Azure Resource ManagerAzure Resource Manager overview](../../azure-resource-manager/management/overview.md)
* [Definizione di gruppo di sicurezza di rete](../../virtual-network/security-overview.md)
* [Azure Load Balancer Overview](../../load-balancer/load-balancer-overview.md)

