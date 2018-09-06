---
title: Rete accelerata con il ripristino di emergenza di macchine virtuali di Azure | Microsoft Docs
description: Procedura per abilitare Rete accelerata con Azure Site Recovery per il ripristino di emergenza di macchine virtuali di Azure
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/24/2018
ms.author: manayar
ms.openlocfilehash: af29333956fb962ed31133260ecbc9607de38fd3
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2018
ms.locfileid: "43052064"
---
# <a name="accelerated-networking-with-azure-virtual-machine-disaster-recovery"></a>Rete accelerata con il ripristino di emergenza di macchine virtuali di Azure

La funzionalità Rete accelerata abilita Single Root I/O Virtualization (SR-IOV) per le macchine virtuali (VM), migliorandone le prestazioni di rete. Questo percorso a prestazioni elevate esclude l'host dal percorso dati, riducendo così la latenza, l'instabilità e l'utilizzo della CPU e può essere usato con i carichi di lavoro di rete più impegnativi nei tipi di VM supportati. L'immagine seguente illustra le comunicazioni tra due VM, con e senza rete accelerata:

![Confronto](./media/azure-vm-disaster-recovery-with-accelerated-networking/accelerated-networking-benefit.png)

Azure Site Recovery consente di sfruttare i vantaggi di Rete accelerata per le macchine virtuali di Azure di cui viene eseguito il failover in un'altra area di Azure. Questo articolo descrive come abilitare Rete accelerata per le macchine virtuali di Azure replicate con Azure Site Recovery.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, assicurarsi di avere compreso:
-   L'[architettura di replica](azure-to-azure-architecture.md) delle macchine virtuali di Azure
-   L'[impostazione della replica](azure-to-azure-tutorial-enable-replication.md) per le macchine virtuali di Azure
-   La [procedura di failover](azure-to-azure-tutorial-failover-failback.md) delle macchine virtuali di Azure

## <a name="accelerated-networking-with-windows-vms"></a>Rete accelerata con VM Windows

Azure Site Recovery supporta l'abilitazione di Rete accelerata per le macchine virtuali replicate solo se questa funzionalità è abilitata nella macchina virtuale di origine, altrimenti è possibile leggere [questo articolo](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) per informazioni su come abilitare Rete accelerata per macchine virtuali Windows.

### <a name="supported-operating-systems"></a>Sistemi operativi supportati
Le distribuzioni seguenti sono supportate in modo nativo dalla raccolta di Azure:
* **Windows Server 2016 Datacenter**
* **Windows Server 2012 R2 Datacenter**

### <a name="supported-vm-instances"></a>Istanze di VM supportate
La funzionalità Rete accelerata è supportata nella maggior parte delle istanze di utilizzo generico e ottimizzate per il calcolo con 2 o più vCPU.  Queste serie supportate sono D/DSv2 e F/Fs

Nelle istanze che supportano l'hyperthreading, la Rete accelerata è supportata nelle istanze di macchine virtuali con 4 o più vCPU. Sono supportate le serie D/DSv3, E/ESv3, Fsv2 e Ms/Mms

Per altre informazioni sulle istanze di VM, vedere [Dimensioni per le macchine virtuali Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="accelerated-networking-with-linux-vms"></a>Rete accelerata con VM Linux

Azure Site Recovery supporta l'abilitazione di Rete accelerata per le macchine virtuali replicate solo se questa funzionalità è abilitata nella macchina virtuale di origine, altrimenti è possibile leggere [questo articolo](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms) per informazioni su come abilitare Rete accelerata per macchine virtuali Linux.

### <a name="supported-operating-systems"></a>Sistemi operativi supportati
Le distribuzioni seguenti sono supportate in modo nativo dalla raccolta di Azure:
* **Ubuntu 16.04**
* **SLES 12 SP3**
* **RHEL 7.4**
* **CentOS 7.4**
* **CoreOS Linux**
* **Debian "Stretch" con kernel backport**
* **Oracle Linux 7.4**

### <a name="supported-vm-instances"></a>Istanze di VM supportate
La funzionalità Rete accelerata è supportata nella maggior parte delle istanze di utilizzo generico e ottimizzate per il calcolo con 2 o più vCPU.  Queste serie supportate sono D/DSv2 e F/Fs

Nelle istanze che supportano l'hyperthreading, la rete accelerata è supportata nelle istanze di VM con 4 o più vCPU. Sono supportate le serie D/DSv3, E/ESv3, Fsv2 e Ms/Mms.

Per altre informazioni sulle istanze di VM, vedere [Dimensioni per le macchine virtuali Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="enabling-accelerated-networking-for-replicated-vms"></a>Abilitazione di Rete accelerata per le VM replicate

Quando si [abilita la replica](azure-to-azure-tutorial-enable-replication.md) per le macchine virtuali di Azure, Site Recovery rileva automaticamente se nelle interfacce di rete delle macchine virtuali è abilitata la funzionalità Rete accelerata. In caso affermativo, Site Recovery configura automaticamente Rete accelerata sulle interfacce di rete della macchina virtuale replicata.

Lo stato di Rete accelerata può essere verificato nella sezione **Interfacce di rete** delle impostazioni **Calcolo e rete** per la macchina virtuale replicata.

![Impostazione di Rete accelerata](./media/azure-vm-disaster-recovery-with-accelerated-networking/compute-network-accelerated-networking.png)

Se si è abilitata la funzionalità Rete accelerata nella macchina virtuale di origine dopo l'abilitazione della replica, è possibile abilitare Rete accelerata per le interfacce di rete della macchina virtuale replicata seguendo questa procedura:
1. Aprire le impostazioni **Calcolo e rete** per la macchina virtuale replicata
2. Fare clic sul nome dell'interfaccia di rete nella sezione **Interfacce di rete**
3. Selezionare **Abilitata** dall'elenco a discesa relativo a Rete accelerata sotto la colonna **Destinazione**

![Abilitare Rete accelerata](./media/azure-vm-disaster-recovery-with-accelerated-networking/network-interface-accelerated-networking-enabled.png)

La procedura appena illustrata deve essere seguita anche per le macchine virtuali replicate esistenti, in cui Rete accelerata non è stata abilitata automaticamente da Site Recovery.

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sui [vantaggi della funzionalità Rete accelerata](../virtual-network/create-vm-accelerated-networking-powershell.md#benefits)
- Altre informazioni sulle limitazioni e i vincoli della funzionalità Rete accelerata per [macchine virtuali Windows](../virtual-network/create-vm-accelerated-networking-powershell.md#limitations-and-constraints) e [macchine virtuali Linux](../virtual-network/create-vm-accelerated-networking-cli.md#limitations-and-constraints).
- Altre informazioni sui [piani di ripristino](site-recovery-create-recovery-plans.md) per automatizzare il failover delle applicazioni.
