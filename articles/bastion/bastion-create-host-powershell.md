---
title: Creare un host Bastion usando Azure Powershell Documenti Microsoft
description: In questo articolo viene illustrato come creare un host di Azure Bastion
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 43d834f0c834696cd4a836466c9663fe7c31a392
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520507"
---
# <a name="create-an-azure-bastion-host-using-azure-powershell"></a>Creare un host Bastion di Azure usando Azure PowerShellCreate an Azure Bastion host using Azure PowerShell

Questo articolo illustra come creare un host Bastion di Azure usando PowerShell.This article shows you how to create an Azure Bastion host using PowerShell. Dopo aver eseguito il provisioning del servizio Bastion di Azure nella rete virtuale, l'esperienza RDP/SSH senza interruzioni è disponibile per tutte le macchine virtuali nella stessa rete virtuale. La distribuzione di Azure Bastion viene effettuata per rete virtuale e non per sottoscrizione/account o macchina virtuale.

Facoltativamente, è possibile creare un host di Azure Bastion usando il portale di Azure.Optionally, you can create an Azure Bastion host by using the [Azure portal](bastion-create-host-portal.md).

## <a name="before-you-begin"></a>Prima di iniziare

Verificare di possedere una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile attivare i [vantaggi per i sottoscrittori di MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Creare un host Bastion

Questa sezione consente di creare una nuova risorsa Bastion di Azure usando Azure PowerShell.This section helps you create a new Azure Bastion resource using Azure PowerShell.

1. Creare una rete virtuale e una subnet di Azure Bastion.Create a virtual network and an Azure Bastion subnet. È necessario creare la subnet di Azure Bastion usando il valore del nome **AzureBastionSubnet**. Questo valore consente ad Azure di sapere in quale subnet distribuire le risorse Bastion.This value lets Azure know which subnet to deploy the Bastion resources to. Questo è diverso da una subnet del gateway. È necessario utilizzare una subnet di almeno /27 o subnet più grande (/27, /26 e così via). Creare **AzureBastionSubnet** senza tabelle di route o deleghe. Se si usano gruppi di sicurezza di rete in **AzureBastionSubnet**, vedere l'articolo Usare i gruppi di [sicurezza di rete.](bastion-nsg.md)

   ```azurepowershell-interactive
   $subnetName = "AzureBastionSubnet"
   $subnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
   $vnet = New-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myBastionRG" -Location "westeurope" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
   ```

2. Creare un indirizzo IP pubblico per Azure Bastion.Create a public IP address for Azure Bastion. L'indirizzo IP pubblico è l'indirizzo IP pubblico della risorsa Bastion su cui si accede a RDP/SSH (tramite la porta 443). L'indirizzo IP pubblico deve trovarsi nella stessa area della risorsa Bastion che si sta creando.

   ```azurepowershell-interactive
   $publicip = New-AzPublicIpAddress -ResourceGroupName "myBastionRG" -name "myPublicIP" -location "westeurope" -AllocationMethod Static -Sku Standard
   ```

3. Creare una nuova risorsa Bastion di Azure in AzureBastionSubnet della rete virtuale. La creazione e la distribuzione della risorsa Bastion richiede circa 5 minuti.

   ```azurepowershell-interactive
   $bastion = New-AzBastion -ResourceGroupName "myBastionRG" -Name "myBastion" -PublicIpAddress $publicip -VirtualNetwork $vnet
   ```

## <a name="next-steps"></a>Passaggi successivi

* Leggi le domande frequenti sul [Bastion](bastion-faq.md) per ulteriori informazioni.

* Per usare i gruppi di sicurezza di rete con la subnet di Azure Bastion, vedere [Usare i gruppi](bastion-nsg.md)di sicurezza di rete.
