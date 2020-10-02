---
title: Procedure consigliate per la rete di Azure Service Fabric
description: Regole e considerazioni di progettazione per la gestione della connettività di rete con Service Fabric di Azure.
author: chrpap
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: chrpap
ms.openlocfilehash: b8db69792b31fd82646757423e669e39e8539d06
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2020
ms.locfileid: "91630703"
---
# <a name="networking"></a>Rete

Quando si creano e si gestiscono cluster di Azure Service Fabric, viene fornita la connettività di rete per i nodi e le applicazioni. Le risorse di rete includono gli intervalli di indirizzi IP, le reti virtuali, i bilanciamenti del carico e i gruppi di sicurezza di rete. In questo articolo verranno illustrate le procedure consigliate per queste risorse.

Esaminare i [modelli di rete Service Fabric](./service-fabric-patterns-networking.md) di Azure per informazioni su come creare cluster che usano le funzionalità seguenti: rete virtuale o subnet esistente, indirizzo IP pubblico statico, servizio di bilanciamento del carico solo interno o servizio di bilanciamento del carico interno ed esterno.

## <a name="infrastructure-networking"></a>Distribuzione in rete dell'infrastruttura
Ottimizzare le prestazioni della macchina virtuale con la rete accelerata, dichiarando la proprietà *enableAcceleratedNetworking* nel modello di gestione risorse, il frammento di codice seguente è un set di scalabilità di macchine virtuali NetworkInterfaceConfigurations che consente la rete accelerata:

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

La funzionalità rete accelerata è supportata per gli SKU delle serie di macchine virtuali di Azure: D/DSv2, D/DSv3, E/ESv3, F/FS, FSv2 e MS/MMS. La funzionalità rete accelerata è stata testata correttamente usando lo SKU Standard_DS8_v3 su 01/23/2019 per un cluster Windows Service Fabric e usando Standard_DS12_v2 su 01/29/2019 per un cluster Service Fabric Linux.

Per abilitare la rete accelerata in un cluster di Service Fabric esistente, è necessario prima [Ridimensionare un cluster di Service Fabric aggiungendo un set di scalabilità di macchine virtuali](./virtual-machine-scale-set-scale-node-type-scale-out.md) per poter eseguire le operazioni seguenti:
1. Effettuare il provisioning di un tipo di nodo con la rete accelerata abilitata
2. Eseguire la migrazione dei servizi e del relativo stato nel tipo di nodo sottoposto a provisioning con la rete accelerata abilitata

Per abilitare la rete accelerata in un cluster esistente è necessario ridimensionare l'infrastruttura perché l'abilitazione della rete accelerata genera tempi di inattività, in quanto è necessario [arrestare e deallocare tutte le macchine virtuali presenti in un set di disponibilità prima di abilitare la rete accelerata in qualsiasi interfaccia di rete esistente](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

## <a name="cluster-networking"></a>Distribuzione in rete dei cluster

* Per distribuire cluster di Service Fabric in una rete virtuale esistente è possibile seguire la procedura descritta in [Modelli di rete di Service Fabric](./service-fabric-patterns-networking.md).

* Per i tipi di nodo è consigliabile usare i gruppi di sicurezza di rete (gruppi) per limitare il traffico in ingresso e in uscita al cluster. Assicurarsi che nel gruppo di sicurezza di rete siano aperte tutte le porte necessarie. 

* Non è necessario che il tipo di nodo primario, contenente i servizi di sistema di Service Fabric, venga esposto tramite il bilanciamento del carico esterno, ma può essere esposto da un [bilanciamento del carico interno](./service-fabric-patterns-networking.md#internal-only-load-balancer)

* Usare un [indirizzo IP pubblico statico](./service-fabric-patterns-networking.md#static-public-ip-address-1) per il cluster.

## <a name="network-security-rules"></a>Regole di sicurezza di rete

Di seguito sono riportate le regole di base per un blocco di sicurezza di un cluster di Service Fabric gestito di Azure. L'impossibilità di aprire le porte seguenti o di approvare l'IP/URL impedirà il corretto funzionamento del cluster e potrebbe non essere supportato. Con questo set di regole è strettamente necessario usare gli [aggiornamenti automatici delle immagini del sistema operativo](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md). in caso contrario, sarà necessario aprire porte aggiuntive.

### <a name="inbound"></a>In ingresso 
|Priorità   |Nome               |Porta        |Protocollo  |Source (Sorgente)             |Destination       |Azione   
|---        |---                |---         |---       |---                |---               |---
|3900       |Azure              |19080       |TCP       |Internet           |VirtualNetwork    |Consenti
|3910       |Client             |19000       |TCP       |Internet           |VirtualNetwork    |Consenti
|3920       |Cluster            |1025-1027   |TCP       |VirtualNetwork     |VirtualNetwork    |Consenti
|3930       |Effimera          |49152-65534 |TCP       |VirtualNetwork     |VirtualNetwork    |Consenti
|3940       |Applicazione        |20000-30000 |TCP       |VirtualNetwork     |VirtualNetwork    |Consenti
|3950       |SMB                |445         |TCP       |VirtualNetwork     |VirtualNetwork    |Consenti
|3960       |RDP                |3389-3488   |TCP       |Internet           |VirtualNetwork    |Nega
|3970       |SSH                |22          |TCP       |Internet           |VirtualNetwork    |Nega
|3980       |Endpoint personalizzato    |80          |TCP       |Internet           |VirtualNetwork    |Consenti
|4100       |Blocca in ingresso      |443         |Qualsiasi       |Qualsiasi                |Qualsiasi               |Allow

Ulteriori informazioni sulle regole di sicurezza in ingresso:

* **Azure**. Questa porta viene usata da Service Fabric Explorer per esplorare e gestire il cluster e viene usata anche dal provider di risorse Service Fabric per eseguire query sulle informazioni sul cluster per visualizzare nel portale di gestione di Azure. Se questa porta non è accessibile dal provider di risorse Service Fabric, verrà visualizzato un messaggio come ' nodes not found ' o ' UpgradeServiceNotReachable ' nel portale di Azure e l'elenco di nodi e applicazioni verrà visualizzato come vuoto. Ciò significa che se si vuole avere visibilità del cluster in Azure portale di gestione, il servizio di bilanciamento del carico deve esporre un indirizzo IP pubblico e il NSG deve consentire il traffico in entrata 19080.  

* **Client**. Endpoint della connessione client per le API come REST/PowerShell/CLI. 

* **Cluster**. Utilizzato per la comunicazione tra nodi; non deve mai essere bloccato.

* **Effimero**. Service Fabric usa una parte di queste porte dell'applicazione, mentre le rimanenti sono a disposizione del sistema operativo. Esegue anche il mapping di questo intervallo all'intervallo esistente presente nel sistema operativo, quindi per tutti gli scopi è possibile usare gli intervalli specificati nell'esempio qui. È necessario assicurarsi che la differenza tra la porta iniziale e la porta finale sia almeno 255. Questo intervallo è condiviso con il sistema operativo e possono quindi verificarsi dei conflitti se la differenza è troppo bassa. Per visualizzare l'intervallo di porte dinamiche configurato, eseguire *netsh int ipv4 show Dynamic Port TCP*. Queste porte non sono necessarie per i cluster Linux.

* **Applicazione**. L'intervallo di porte dell'applicazione deve essere abbastanza grande da soddisfare il requisito di endpoint delle applicazioni. Questo intervallo deve essere esclusivo dall'intervallo di porte dinamico del computer, ad esempio l'intervallo ephemeralPorts impostato nella configurazione. Service Fabric usa queste porte ogni volta che sono necessarie nuove porte e si occupa dell'apertura del firewall per queste porte nei nodi.

* **SMB**. Il protocollo SMB viene utilizzato dal servizio ImageStore per due scenari. Questa porta è necessaria per scaricare i pacchetti dal ImageStore dai nodi e per replicarli tra le repliche. 

* **RDP**. Facoltativo, se è necessario il protocollo RDP da Internet o VirtualNetwork per gli scenari JumpBox. 

* **SSH**. Facoltativo, se è richiesto SSH da Internet o VirtualNetwork per gli scenari JumpBox.

* **Endpoint personalizzato**. Esempio per l'applicazione per abilitare un endpoint accessibile da Internet.

### <a name="outbound"></a>In uscita

|Priorità   |Nome               |Porta        |Protocollo  |Source (Sorgente)             |Destination       |Azione   
|---        |---                |---         |---       |---                |---               |---
|3900       |Rete            |Qualsiasi         |TCP       |VirtualNetwork     |VirtualNetwork    |Consenti
|3910       |Provider di risorse  |443         |TCP       |VirtualNetwork     |ServiceFabric     |Consenti
|3920       |Aggiornamento            |443         |TCP       |VirtualNetwork     |Internet          |Consenti
|3950       |Blocca in uscita     |Qualsiasi         |Qualsiasi       |Qualsiasi                |Qualsiasi               |Nega

Ulteriori informazioni sulle regole di sicurezza in uscita:

* **Rete**: Canale di comunicazione per le subnet e per le altre reti virtuali.

* **Provider di risorse**. Connessione da parte di UpgradeService per l'esecuzione di tutte le distribuzioni ARM da parte del provider di risorse Service Fabric.

* **Aggiornamento**. Il servizio di aggiornamento che usa l'indirizzo download.microsoft.com per ottenere i bit, è necessario per l'installazione, la ricreazione dell'immagine e gli aggiornamenti del runtime. Il servizio opera con indirizzi IP dinamici. Nello scenario di un servizio di bilanciamento del carico "solo interno", è necessario aggiungere al modello un servizio di bilanciamento del carico esterno aggiuntivo con una regola che consente il traffico in uscita per la porta 443. Facoltativamente, questa porta può essere bloccata dopo una corretta installazione, ma in questo caso il pacchetto di aggiornamento deve essere distribuito ai nodi o la porta deve essere aperta per il breve periodo di tempo, quindi è necessario un aggiornamento manuale.

Usare il firewall di Azure con il [log di flusso NSG](../network-watcher/network-watcher-nsg-flow-logging-overview.md) e [analisi del traffico](../network-watcher/traffic-analytics.md) per tenere traccia dei problemi con il blocco di sicurezza. Il modello ARM [Service fabric con NSG](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) è un valido esempio per iniziare. 


## <a name="application-networking"></a>Distribuzione in rete delle applicazioni

* Per eseguire carichi di lavoro di contenitori Windows, usare la [modalità di rete aperta](./service-fabric-networking-modes.md#set-up-open-networking-mode) per semplificare le comunicazioni tra i servizi.

* Usare un proxy inverso come [Traefik](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/) o il [proxy inverso di Service Fabric](./service-fabric-reverseproxy.md) per esporre le porte delle applicazioni comuni, ad esempio 80 o 443.

* Per i contenitori di Windows ospitati in computer gapped che non possono effettuare il pull dei livelli di base dall'archiviazione cloud di Azure, eseguire l'override del comportamento del livello esterno, usando il flag [--Allow-nondistributable-artefatts](/virtualization/windowscontainers/about/faq#how-do-i-make-my-container-images-available-on-air-gapped-machines) nel daemon docker.

## <a name="next-steps"></a>Passaggi successivi

* Creare un cluster nelle VM o nei computer che eseguono Windows Server: [Creazione di cluster di Service Fabric per Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Creare un cluster nelle VM o nei computer che eseguono Linux: [Creare un cluster Linux](service-fabric-cluster-creation-via-portal.md)
* Informazioni sulle [Opzioni di supporto Service Fabric](service-fabric-support.md)

[NSGSetup]: ./media/service-fabric-best-practices/service-fabric-nsg-rules.png
