---
title: Come spostare una macchina virtuale o un&quot;istanza del ruolo in un&quot;altra subnet
description: Informazioni su come spostare macchine virtuali e istanze del ruolo in un&quot;altra subnet
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: de4135c7-dc5b-4ffa-84cc-1b8364b7b427
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 264e02fa48486acd7a9701c497c4e1fa95a1ce4e


---
# <a name="how-to-move-a-vm-or-role-instance-to-a-different-subnet"></a>Come spostare una macchina virtuale o un'istanza del ruolo in un'altra subnet
È possibile usare PowerShell per spostare le proprie macchine virtuali da una subnet a un'altra nella stessa rete virtuale (VNet). Le istanze del ruolo possono essere spostate modificando il file CSCFG invece di usare PowerShell.

> [!NOTE]
> Le informazioni contenute in questo articolo riguardano le sole distribuzioni classiche di Azure.
> 
> 

Perché spostare le macchine virtuali in un'altra subnet? La migrazione in un'altra subnet è utile quando la subnet corrente è troppo piccola e non può essere espansa a causa delle macchine virtuali in esecuzione al suo interno. In tal caso, è possibile creare una nuova subnet più grande, migrarvi le macchine virtuali e al termine eliminare la precedente subnet ormai vuota.

## <a name="how-to-move-a-vm-to-another-subnet"></a>Come spostare una macchina virtuale in un'altra subnet
Per spostare una macchina virtuale, eseguire il cmdlet di PowerShell Set-AzureSubnet usando l'esempio seguente come modello. In tale esempio TestVM viene spostata dalla subnet corrente a Subnet-2. Ricordarsi di modificare l'esempio in base all'ambiente in uso. Si noti che, ogni volta che il cmdlet Update-AzureVM viene eseguito in una procedura, riavvierà la macchina virtuale come parte del processo di aggiornamento.

    Get-AzureVM –ServiceName TestVMCloud –Name TestVM `
    | Set-AzureSubnet –SubnetNames Subnet-2 `
    | Update-AzureVM

Se è stato specificato un indirizzo IP privato interno statico per la macchina virtuale, sarà necessario cancellare tale impostazione prima di poter spostare la macchina virtuale in una nuova subnet. In questo caso, usare quanto segue:

    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
    | Remove-AzureStaticVNetIP `
    | Update-AzureVM
    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
    | Set-AzureSubnet -SubnetNames Subnet-2 `
    | Update-AzureVM

## <a name="to-move-a-role-instance-to-another-subnet"></a>Per spostare un'istanza del ruolo in un'altra subnet
Per spostare un'istanza del ruolo, modificare il file CSCFG. Nell'esempio seguente "Role0" nella rete virtuale *VNETName* viene spostato dalla subnet corrente a *Subnet-2*. Poiché l'istanza del ruolo è già stata distribuita, sarà necessario solo modificare Subnet name = Subnet-2. Ricordarsi di modificare l'esempio in base all'ambiente in uso.

    <NetworkConfiguration>
        <VirtualNetworkSite name="VNETName" />
        <AddressAssignments>
           <InstanceAddress roleName="Role0">
                <Subnets><Subnet name="Subnet-2" /></Subnets>
           </InstanceAddress>
        </AddressAssignments>
    </NetworkConfiguration> 



<!--HONumber=Nov16_HO3-->


