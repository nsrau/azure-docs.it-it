---
title: 'Esercitazione: Aggiungere un segmento di rete NSX-T nella soluzione Azure VMware'
description: Informazioni su come creare un segmento di rete NSX-T da usare per le macchine virtuali in vCenter.
ms.topic: tutorial
ms.date: 11/09/2020
ms.openlocfilehash: 8ecb37a42e2986bd1c6261b8fe6c23382323b31d
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335049"
---
# <a name="tutorial-add-a-network-segment-in-azure-vmware-solution"></a>Esercitazione: Aggiungere un segmento di rete nella soluzione Azure VMware 

Le macchine virtuali (VM) create in vCenter vengono posizionate sui segmenti di rete creati in NSX-T e sono visibili in vCenter.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Spostarsi in NSX-T Manager per aggiungere segmenti di rete
> * Aggiungere un nuovo segmento di rete
> * Osservare il nuovo segmento di rete in vCenter

## <a name="prerequisites"></a>Prerequisiti

Un cloud privato della soluzione Azure VMware con accesso alle interfacce di vCenter e NSX-T Manager. Per altre informazioni, vedere l'esercitazione [Configurare la rete](tutorial-configure-networking.md).

## <a name="add-a-network-segment"></a>Aggiungere un segmento di rete

[!INCLUDE [add-network-segment-steps](includes/add-network-segment-steps.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato creato un segmento di rete NSX-T da usare per le macchine virtuali in vCenter. 

In particolare, è possibile: 

- [Creare e gestire il protocollo DHCP per la soluzione Azure VMware](manage-dhcp.md)
- [Creare una raccolta contenuto per distribuire macchine virtuali nella soluzione Azure VMware](deploy-vm-content-library.md) 
- [Associare gli ambienti locali a un cloud privato](tutorial-expressroute-global-reach-private-cloud.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
