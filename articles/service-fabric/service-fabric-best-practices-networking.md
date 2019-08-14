---
title: Procedure consigliate per la gestione della rete di Azure Service Fabric | Microsoft Docs
description: Procedure consigliate per la gestione della rete di Service Fabric.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: d221b828624e649a0d04a89c4394fe5a7fa857dd
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2019
ms.locfileid: "66237331"
---
# <a name="networking"></a>Rete

Quando si creano e si gestiscono cluster di Azure Service Fabric, viene fornita la connettività di rete per i nodi e le applicazioni. Le risorse di rete includono gli intervalli di indirizzi IP, le reti virtuali, i bilanciamenti del carico e i gruppi di sicurezza di rete. In questo articolo verranno illustrate le procedure consigliate per queste risorse.

Vedere [Modelli di rete di Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking) per informazioni su come creare cluster che usano le funzionalità seguenti: subnet o rete virtuale esistente, indirizzo IP pubblico statico, bilanciamento del carico esclusivamente interno o bilanciamento del carico interno ed esterno.

## <a name="infrastructure-networking"></a>Distribuzione in rete dell'infrastruttura
È possibile ottimizzare le prestazioni della macchina virtuale con la rete accelerata dichiarando la proprietà enableAcceleratedNetworking nel modello di Resource Manager; il frammento seguente appartiene a un set di scalabilità di macchine virtuali NetworkInterfaceConfigurations che supporta la rete accelerata:

```json
"networkInterfaceConfigurations": [
  {
    "name": "[concat(variables('nicName'), '-0')]",
    "properties": {
      "enableAcceleratedNetworking": true,
      "ipConfigurations": [
        {
        <snip>
        }
      ],
      "primary": true
    }
  }
]
```
È possibile effettuare il provisioning di un cluster di Service Fabric in [Linux con rete la accelerata](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) e in [Windows con la rete accelerata](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell).

La rete accelerata è supportata per le macchine virtuali di Azure con SKU serie: D/DSv2, D/DSv3, E/ESv3, F/FS, FSv2 e Ms/Mms. La rete accelerata è stata testata con esito positivo usando lo SKU Standard_DS8_v3 il 23/01/2019 per un cluster Windows di Service Fabric e lo SKU Standard_DS12_v2 il 29/01/2019 per un cluster Linux di Service Fabric.

Per abilitare la rete accelerata in un cluster di Service Fabric esistente, è necessario prima [Ridimensionare un cluster di Service Fabric aggiungendo un set di scalabilità di macchine virtuali](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out) per poter eseguire le operazioni seguenti:
1. Effettuare il provisioning di un tipo di nodo con la rete accelerata abilitata
2. Eseguire la migrazione dei servizi e del relativo stato nel tipo di nodo sottoposto a provisioning con la rete accelerata abilitata

Per abilitare la rete accelerata in un cluster esistente è necessario ridimensionare l'infrastruttura perché l'abilitazione della rete accelerata genera tempi di inattività, in quanto è necessario [arrestare e deallocare tutte le macchine virtuali presenti in un set di disponibilità prima di abilitare la rete accelerata in qualsiasi interfaccia di rete esistente](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms).

## <a name="cluster-networking"></a>Distribuzione in rete dei cluster

* Per distribuire cluster di Service Fabric in una rete virtuale esistente è possibile seguire la procedura descritta in [Modelli di rete di Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking).

* È opportuno usare gruppi di sicurezza di rete per i tipi di nodo che limitano il traffico in ingresso e in uscita al rispettivo cluster. Assicurarsi che nel gruppo di sicurezza di rete siano aperte tutte le porte necessarie. Ad esempio:  ![Regole del gruppo di sicurezza di rete di Service Fabric][NSGSetup]

* Non è necessario che il tipo di nodo primario, contenente i servizi di sistema di Service Fabric, venga esposto tramite il bilanciamento del carico esterno, ma può essere esposto da un [bilanciamento del carico interno](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#internal-only-load-balancer)

* Usare un [indirizzo IP pubblico statico](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#static-public-ip-address-1) per il cluster.

## <a name="application-networking"></a>Distribuzione in rete delle applicazioni

* Per eseguire carichi di lavoro di contenitori Windows, usare la [modalità di rete aperta](https://docs.microsoft.com/azure/service-fabric/service-fabric-networking-modes#set-up-open-networking-mode) per semplificare le comunicazioni tra i servizi.

* Usare un proxy inverso come [Traefik](https://docs.traefik.io/configuration/backends/servicefabric/) o il [proxy inverso di Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy) per esporre le porte delle applicazioni comuni, ad esempio 80 o 443.

* Per i contenitori di Windows ospitati in computer gapped che non possono effettuare il pull dei livelli di base dall'archiviazione cloud di Azure, eseguire l'override del comportamento del livello esterno, usando il flag [--Allow-nondistributable-artefatts](https://docs.microsoft.com/virtualization/windowscontainers/about/faq#how-do-i-make-my-container-images-available-on-air-gapped-machines) nel daemon docker.

## <a name="next-steps"></a>Passaggi successivi

* Creare un cluster nelle macchine virtuali o nei computer che eseguono Windows Server: [Creazione del cluster di Service Fabric per Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Creare un cluster nelle macchine virtuali o nei computer che eseguono Linux: [Creare un cluster Linux](service-fabric-cluster-creation-via-portal.md)
* Informazioni sulle [opzioni di supporto di Service Fabric](service-fabric-support.md)

[NSGSetup]: ./media/service-fabric-best-practices/service-fabric-nsg-rules.png
