---
title: Procedure consigliate per la rete di Azure Service Fabric
description: Procedure consigliate e considerazioni di progettazione per la gestione della connettività di rete con Service Fabric di Azure.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 853e53d32f87f81e5db587de2654f83037930da7
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86261139"
---
# <a name="networking"></a>Rete

Quando si creano e si gestiscono cluster di Azure Service Fabric, viene fornita la connettività di rete per i nodi e le applicazioni. Le risorse di rete includono gli intervalli di indirizzi IP, le reti virtuali, i bilanciamenti del carico e i gruppi di sicurezza di rete. In questo articolo verranno illustrate le procedure consigliate per queste risorse.

Esaminare i [modelli di rete Service Fabric](./service-fabric-patterns-networking.md) di Azure per informazioni su come creare cluster che usano le funzionalità seguenti: rete virtuale o subnet esistente, indirizzo IP pubblico statico, servizio di bilanciamento del carico solo interno o servizio di bilanciamento del carico interno ed esterno.

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
È possibile effettuare il provisioning di un cluster di Service Fabric in [Linux con rete la accelerata](../virtual-network/create-vm-accelerated-networking-cli.md) e in [Windows con la rete accelerata](../virtual-network/create-vm-accelerated-networking-powershell.md).

La funzionalità rete accelerata è supportata per gli SKU delle serie di macchine virtuali di Azure: D/DSv2, D/DSv3, E/ESv3, F/FS, FSv2 e MS/MMS. La rete accelerata è stata testata con esito positivo usando lo SKU Standard_DS8_v3 il 23/01/2019 per un cluster Windows di Service Fabric e lo SKU Standard_DS12_v2 il 29/01/2019 per un cluster Linux di Service Fabric.

Per abilitare la rete accelerata in un cluster di Service Fabric esistente, è necessario prima [Ridimensionare un cluster di Service Fabric aggiungendo un set di scalabilità di macchine virtuali](./virtual-machine-scale-set-scale-node-type-scale-out.md) per poter eseguire le operazioni seguenti:
1. Effettuare il provisioning di un tipo di nodo con la rete accelerata abilitata
2. Eseguire la migrazione dei servizi e del relativo stato nel tipo di nodo sottoposto a provisioning con la rete accelerata abilitata

Per abilitare la rete accelerata in un cluster esistente è necessario ridimensionare l'infrastruttura perché l'abilitazione della rete accelerata genera tempi di inattività, in quanto è necessario [arrestare e deallocare tutte le macchine virtuali presenti in un set di disponibilità prima di abilitare la rete accelerata in qualsiasi interfaccia di rete esistente](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

## <a name="cluster-networking"></a>Distribuzione in rete dei cluster

* Per distribuire cluster di Service Fabric in una rete virtuale esistente è possibile seguire la procedura descritta in [Modelli di rete di Service Fabric](./service-fabric-patterns-networking.md).

* È opportuno usare gruppi di sicurezza di rete per i tipi di nodo che limitano il traffico in ingresso e in uscita al rispettivo cluster. Assicurarsi che nel gruppo di sicurezza di rete siano aperte tutte le porte necessarie. Ad esempio: ![ Service Fabric regole NSG][NSGSetup]

* Non è necessario che il tipo di nodo primario, contenente i servizi di sistema di Service Fabric, venga esposto tramite il bilanciamento del carico esterno, ma può essere esposto da un [bilanciamento del carico interno](./service-fabric-patterns-networking.md#internal-only-load-balancer)

* Usare un [indirizzo IP pubblico statico](./service-fabric-patterns-networking.md#static-public-ip-address-1) per il cluster.

## <a name="application-networking"></a>Distribuzione in rete delle applicazioni

* Per eseguire carichi di lavoro di contenitori Windows, usare la [modalità di rete aperta](./service-fabric-networking-modes.md#set-up-open-networking-mode) per semplificare le comunicazioni tra i servizi.

* Usare un proxy inverso come [Traefik](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/) o il [proxy inverso di Service Fabric](./service-fabric-reverseproxy.md) per esporre le porte delle applicazioni comuni, ad esempio 80 o 443.

* Per i contenitori di Windows ospitati in computer gapped che non possono effettuare il pull dei livelli di base dall'archiviazione cloud di Azure, eseguire l'override del comportamento del livello esterno, usando il flag [--Allow-nondistributable-artefatts](/virtualization/windowscontainers/about/faq#how-do-i-make-my-container-images-available-on-air-gapped-machines) nel daemon docker.

## <a name="next-steps"></a>Passaggi successivi

* Creare un cluster nelle VM o nei computer che eseguono Windows Server: [Creazione di cluster di Service Fabric per Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Creare un cluster nelle VM o nei computer che eseguono Linux: [Creare un cluster Linux](service-fabric-cluster-creation-via-portal.md)
* Informazioni sulle [Opzioni di supporto Service Fabric](service-fabric-support.md)

[NSGSetup]: ./media/service-fabric-best-practices/service-fabric-nsg-rules.png
