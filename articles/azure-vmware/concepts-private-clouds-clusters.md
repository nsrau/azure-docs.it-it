---
title: Concetti-cloud privati e cluster
description: Informazioni sulle funzionalità principali dei data center e dei cluster vSphere definiti dal software VMware di Azure in una soluzione VMware in Azure di VMware.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 715293d9951876ff0f794f8f6b580093f89571b3
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91316869"
---
#  <a name="azure-vmware-solution-private-cloud-and-cluster-concepts"></a>Concetti relativi ai cluster e al cloud privato della soluzione VMware di Azure

La soluzione VMware di Azure offre cloud privati basati su VMware in Azure. I cloud privati sono compilati da cluster di host bare metal dedicati e vengono distribuiti e gestiti tramite il portale di Azure. Il provisioning dei cluster nei cloud privati viene effettuato con il software VMware vSphere, vCenter, rete VSAN e NSX. Le distribuzioni software e hardware del cloud privato della soluzione VMware di Azure sono completamente integrate e automatizzate in Azure.

Esiste una relazione logica tra le sottoscrizioni di Azure, i cloud privati della soluzione VMware di Azure, i cluster rete VSAN e gli host. Nel diagramma vengono visualizzati due cloud privati in una singola sottoscrizione di Azure. I cloud privati rappresentano uno sviluppo e un ambiente di produzione, ognuno con il proprio cloud privato. In ognuno di questi cloud privati sono disponibili due cluster. Per mostrare le potenziali esigenze inferiori di un ambiente di sviluppo, vengono usati cluster più piccoli con host con capacità inferiore. Tutti questi concetti sono descritti nelle sezioni riportate di seguito.

![Immagine di due cloud privati in una sottoscrizione del cliente](./media/hosts-clusters-private-clouds-final.png)

## <a name="private-clouds"></a>Cloud privati

I cloud privati contengono cluster rete VSAN compilati con host di Azure bare metal dedicati. Ogni cloud privato può avere più cluster, tutti gestiti dallo stesso server vCenter e gestione NSX-T. È possibile distribuire e gestire cloud privati nel portale, dall'interfaccia della riga di comando o con PowerShell. Come per le altre risorse, i cloud privati vengono installati e gestiti dall'interno di una sottoscrizione di Azure.

Il numero di cloud privati all'interno di una sottoscrizione è scalabile. Inizialmente, esiste un limite di un cloud privato per ogni sottoscrizione.

## <a name="clusters"></a>Cluster

Si creerà almeno un cluster rete VSAN in ogni cloud privato. Quando si crea un cloud privato, per impostazione predefinita è disponibile un cluster. È possibile aggiungere altri cluster a un cloud privato usando il portale di Azure o tramite l'API. Tutti i cluster hanno una dimensione predefinita di tre host e possono essere ridimensionati da 3 a 16 host. Il tipo di host utilizzato in un cluster deve essere dello stesso tipo. I tipi di host sono descritti nella sezione successiva.

I cluster di valutazione sono disponibili per la valutazione e sono limitati a tre host e a un singolo cluster di valutazione per ogni cloud privato. È possibile ridimensionare un cluster di valutazione da un singolo host durante il periodo di valutazione.

È possibile creare, eliminare e ridimensionare i cluster tramite il portale o l'API. Si usano ancora vSphere e NSX-T Manager per gestire la maggior parte degli altri aspetti della configurazione o dell'operazione del cluster. Tutte le archiviazioni locali di ogni host in un cluster sono sotto il controllo di rete VSAN.

## <a name="hosts"></a>Hosts

I nodi dell'infrastruttura bare metal iperconvergenti vengono usati nei cluster di cloud privati della soluzione VMware di Azure. Nella tabella seguente sono disponibili le capacità di RAM, CPU e disco dell'host. 

| Host Type              |             CPU             |   RAM (GB)   |  Livello cache NVMe rete VSAN (TB, RAW)  |  livello di capacità SSD rete VSAN (TB, RAW)  |
| :---                   |            :---:            |    :---:     |               :---:              |                :---:               |
| High-end (HE)          |  Dual Intel 18 Core 2,3 GHz  |     576      |                3.2               |                15,20               |

Gli host utilizzati per compilare o ridimensionare i cluster vengono acquisiti da un pool di host isolati. Questi host hanno superato i test hardware e tutti i dati sono stati eliminati in modo sicuro dai dischi flash. Quando si rimuove un host da un cluster, i dischi interni vengono cancellati in modo sicuro e gli host vengono inseriti nel pool isolato di host. Quando si aggiunge un host a un cluster, viene utilizzato un host purificato dal pool isolato.

## <a name="vmware-software-versions"></a>Versioni del software VMware

Le versioni software correnti del software VMware usato nei cluster di cloud privato della soluzione VMware di Azure sono:

| Software              |    Versione   |
| :---                  |     :---:    |
| VCSA/vSphere/ESXi |    6,7 U2    | 
| ESXi                  |    6,7 U2    | 
| Rete VSAN                  |    6,7 U2    |
| NSX-T                 |      2.5     |

Per qualsiasi nuovo cluster in un cloud privato, la versione del software corrisponderà a quella attualmente in esecuzione nel cloud privato. Per ogni nuovo cloud privato in una sottoscrizione del cliente, viene installata la versione più recente dello stack di software.

I criteri e i processi di aggiornamento generali per il software della piattaforma della soluzione VMware di Azure sono descritti nel documento concetti relativi agli aggiornamenti.

## <a name="host-maintenance-and-lifecycle-management"></a>Gestione del ciclo di vita e manutenzione dell'host

La gestione del ciclo di vita e la manutenzione dell'host vengono eseguite senza alcun effetto sulla capacità o sulle prestazioni dei cluster di cloud privati. Esempi di manutenzione automatica degli host includono gli aggiornamenti del firmware e la riparazione o la sostituzione dell'hardware.

Microsoft è responsabile della gestione del ciclo di vita di appliance NSX-T, ad esempio NSX-T Manager e NSX-T Edge. Microsoft è anche responsabile del bootstrap della configurazione di rete, ad esempio la creazione del gateway di livello 0 e l'abilitazione del routing nord-sud. In qualità di amministratore del cloud privato della soluzione VMware di Azure, l'utente è responsabile della configurazione di NSX-T SDN. Ad esempio, i segmenti di rete, le regole del firewall distribuite, i gateway di livello 1 e i bilanciamenti del carico.

> [!IMPORTANT]
> Un amministratore della soluzione VMware di Azure non deve modificare la configurazione del gateway NSX-T Edge o di livello 0. Questo può comportare una perdita di servizio.

## <a name="backup-and-restoration"></a>Backup e ripristino

Il backup delle configurazioni vCenter e NSX-T del cloud privato viene eseguito ogni ora. I backup vengono conservati per tre giorni. Il ripristino da un backup viene richiesto tramite una richiesta di servizio nel portale di Azure.

## <a name="next-steps"></a>Passaggi successivi

Il passaggio successivo consiste nell'apprendere i [concetti di rete e tra connettività](concepts-networking.md).

<!-- LINKS - internal -->

<!-- LINKS - external-->
[VCSA versions]: https://kb.vmware.com/s/article/2143838
[ESXi versions]: https://kb.vmware.com/s/article/2143832
[vSAN versions]: https://kb.vmware.com/s/article/2150753

