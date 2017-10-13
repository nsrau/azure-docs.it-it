---
title: Spostare una VM (classica) o un'istanza del ruolo di Servizi cloud in un'altra subnet - Azure PowerShell | Documentazione Microsoft
description: Informazioni su come spostare le VM (classiche) e le istanze del ruolo di Servizi cloud in un'altra subnet mediante PowerShell.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
ms.assetid: de4135c7-dc5b-4ffa-84cc-1b8364b7b427
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b094f8338394ef2e84cad3070936d715411326a4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="move-a-vm-classic-or-cloud-services-role-instance-to-a-different-subnet-using-powershell"></a>Spostare una VM (classica) o un'istanza del ruolo di Servizi cloud in un'altra subnet mediante PowerShell
È possibile usare PowerShell per spostare le proprie VM (classiche) da una subnet a un'altra nella stessa rete virtuale (VNet). Le istanze del ruolo possono essere spostate modificando il file CSCFG invece di usare PowerShell.

> [!NOTE]
> Questo articolo spiega come spostare solo le VM distribuite tramite il modello di distribuzione classica.
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
