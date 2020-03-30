---
title: Procedure consigliate per la rete di Azure Service FabricAzure Service Fabric networking best practices
description: Procedure consigliate e considerazioni di progettazione per la gestione della connettività di rete tramite Azure Service Fabric.Best practices and design considerations for managing network connectivity using Azure Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: de2a74ad2d61de18d2150b72be3251e5b5583f2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551795"
---
# <a name="networking"></a>Rete

Quando si creano e si gestiscono cluster di Azure Service Fabric, viene fornita la connettività di rete per i nodi e le applicazioni. Le risorse di rete includono gli intervalli di indirizzi IP, le reti virtuali, i bilanciamenti del carico e i gruppi di sicurezza di rete. In questo articolo verranno illustrate le procedure consigliate per queste risorse.

Esaminare i modelli di rete di Azure [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking) per informazioni su come creare cluster che usano le funzionalità seguenti: rete virtuale o subnet esistente, Indirizzo IP pubblico statico, servizio di bilanciamento del carico solo interno o servizio di bilanciamento del carico interno ed esterno.

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

La rete accelerata è supportata per gli SKU di Serie di macchine virtuali di Azure: D/DSv2, D/DSv3, E/ESv3, F/FS, FSv2 e Ms/Mms. La rete accelerata è stata testata con esito positivo usando lo SKU Standard_DS8_v3 il 23/01/2019 per un cluster Windows di Service Fabric e lo SKU Standard_DS12_v2 il 29/01/2019 per un cluster Linux di Service Fabric.

Per abilitare la rete accelerata in un cluster di Service Fabric esistente, è necessario prima [Ridimensionare un cluster di Service Fabric aggiungendo un set di scalabilità di macchine virtuali](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out) per poter eseguire le operazioni seguenti:
1. Effettuare il provisioning di un tipo di nodo con la rete accelerata abilitata
2. Eseguire la migrazione dei servizi e del relativo stato nel tipo di nodo sottoposto a provisioning con la rete accelerata abilitata

Per abilitare la rete accelerata in un cluster esistente è necessario ridimensionare l'infrastruttura perché l'abilitazione della rete accelerata genera tempi di inattività, in quanto è necessario [arrestare e deallocare tutte le macchine virtuali presenti in un set di disponibilità prima di abilitare la rete accelerata in qualsiasi interfaccia di rete esistente](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms).

## <a name="cluster-networking"></a>Distribuzione in rete dei cluster

* Per distribuire cluster di Service Fabric in una rete virtuale esistente è possibile seguire la procedura descritta in [Modelli di rete di Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking).

* È opportuno usare gruppi di sicurezza di rete per i tipi di nodo che limitano il traffico in ingresso e in uscita al rispettivo cluster. Assicurarsi che nel gruppo di sicurezza di rete siano aperte tutte le porte necessarie. Ad esempio: regole del gruppo di sicurezza di rete di Service FabricFor example: ![Service Fabric NSG Rules][NSGSetup]

* Non è necessario che il tipo di nodo primario, contenente i servizi di sistema di Service Fabric, venga esposto tramite il bilanciamento del carico esterno, ma può essere esposto da un [bilanciamento del carico interno](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#internal-only-load-balancer)

* Usare un [indirizzo IP pubblico statico](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#static-public-ip-address-1) per il cluster.

## <a name="application-networking"></a>Distribuzione in rete delle applicazioni

* Per eseguire carichi di lavoro di contenitori Windows, usare la [modalità di rete aperta](https://docs.microsoft.com/azure/service-fabric/service-fabric-networking-modes#set-up-open-networking-mode) per semplificare le comunicazioni tra i servizi.

* Usare un proxy inverso come [Traefik](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/) o il [proxy inverso di Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy) per esporre le porte delle applicazioni comuni, ad esempio 80 o 443.

* Per i contenitori di Windows ospitati in computer con copia aerea che non possono eseguire il pull dei livelli di base dall'archiviazione cloud di Azure, eseguire l'override del comportamento del livello esterno usando il flag --allow-nondistributable-artifacts nel daemon Docker.For Windows Containers hosted on air-gapped machines that can't pull base layers from Azure cloud storage, override the foreign layer behavior, by using the [--allow-nondistributable-artifacts](https://docs.microsoft.com/virtualization/windowscontainers/about/faq#how-do-i-make-my-container-images-available-on-air-gapped-machines) flag in the Docker daemon.

## <a name="next-steps"></a>Passaggi successivi

* Creare un cluster nelle VM o nei computer che eseguono Windows Server: [Creazione di cluster di Service Fabric per Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Creare un cluster nelle VM o nei computer che eseguono Linux: [Creare un cluster Linux](service-fabric-cluster-creation-via-portal.md)
* Informazioni sulle opzioni di supporto di [Service Fabric](service-fabric-support.md)

[NSGSetup]: ./media/service-fabric-best-practices/service-fabric-nsg-rules.png
