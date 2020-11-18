---
title: Concetti-cloud privati e cluster
description: Informazioni sulle funzionalità principali dei data center e dei cluster vSphere definiti dal software della soluzione VMware di Azure.
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: 0454ade503ce40de46363f65d36a64340219c0b1
ms.sourcegitcommit: 642988f1ac17cfd7a72ad38ce38ed7a5c2926b6c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94874016"
---
#  <a name="azure-vmware-solution-private-cloud-and-cluster-concepts"></a>Concetti relativi ai cluster e al cloud privato della soluzione VMware di Azure

La soluzione VMware di Azure offre cloud privati basati su VMware in Azure. I cloud privati contengono cluster compilati con host di Azure bare metal dedicati. Sono distribuiti e gestiti tramite il portale di Azure, l'interfaccia della riga di comando o PowerShell.  I cluster sottoposti a provisioning in cloud privati includono software VMware vSphere, vCenter, rete VSAN e NSX. Le distribuzioni software e hardware del cloud privato della soluzione VMware di Azure sono completamente integrate e automatizzate in Azure.

Esiste una relazione logica tra le sottoscrizioni di Azure, i cloud privati della soluzione VMware di Azure, i cluster rete VSAN e gli host. Il diagramma mostra una singola sottoscrizione di Azure con due cloud privati che rappresentano l'ambiente di sviluppo e di produzione.  In ognuno di questi cloud privati sono presenti due cluster. 

Questo articolo descrive tutti questi concetti.

![Immagine di due cloud privati in una sottoscrizione del cliente](./media/hosts-clusters-private-clouds-final.png)

>[!NOTE]
>A causa delle minori esigenze potenziali di un ambiente di sviluppo, usare cluster più piccoli con host con capacità inferiore. 

## <a name="private-clouds"></a>Cloud privati

I cloud privati contengono cluster rete VSAN compilati con host di Azure bare metal dedicati. Ogni cloud privato può avere più cluster gestiti dallo stesso server vCenter e dalla stessa gestione NSX-T. È possibile distribuire e gestire cloud privati nel portale, nell'interfaccia della riga di comando o in PowerShell. 

Come per le altre risorse, i cloud privati vengono installati e gestiti dall'interno di una sottoscrizione di Azure. Il numero di cloud privati all'interno di una sottoscrizione è scalabile. Inizialmente, esiste un limite di un cloud privato per ogni sottoscrizione.

## <a name="clusters"></a>Cluster
Per ogni cloud privato creato, per impostazione predefinita è disponibile un cluster rete VSAN. È possibile aggiungere, eliminare e ridimensionare i cluster usando il portale di Azure o tramite l'API.  Tutti i cluster hanno una dimensione predefinita di tre host e possono essere ridimensionati fino a 16 host.  Gli host utilizzati in un cluster devono corrispondere allo stesso tipo di host.

I cluster di valutazione sono disponibili per la valutazione e sono limitati a tre host. È disponibile un singolo cluster di valutazione per ogni cloud privato. È possibile ridimensionare un cluster di valutazione da un singolo host durante il periodo di valutazione.

Usare vSphere e NSX-T Manager per gestire la maggior parte degli altri aspetti della configurazione o dell'operazione del cluster. Tutte le archiviazioni locali di ogni host in un cluster sono sotto il controllo di rete VSAN.

## <a name="hosts"></a>Hosts

I cluster di cloud privati della soluzione VMware di Azure usano i nodi dell'infrastruttura bare metal iperconvergenti. Nella tabella seguente vengono illustrate le capacità della RAM, della CPU e del disco dell'host. 

| Host Type              |             CPU             |   RAM (GB)   |  Livello cache NVMe rete VSAN (TB, RAW)  |  livello di capacità SSD rete VSAN (TB, RAW)  |
| :---                   |            :---:            |    :---:     |               :---:              |                :---:               |
| High-End (HE)          |  Dual Intel 18 Core 2,3 GHz  |     576      |                3.2               |                15,20               |

Gli host usati per compilare o ridimensionare i cluster provengono da un pool isolato di host. Questi host hanno superato i test hardware e tutti i dati sono stati eliminati in modo sicuro. 

## <a name="vmware-software-versions"></a>Versioni del software VMware

Le versioni software correnti del software VMware usato nei cluster di cloud privato della soluzione VMware di Azure sono:

| Software              |    Versione   |
| :---                  |     :---:    |
| VCSA/vSphere/ESXi |    6,7 U3    | 
| ESXi                  |    6,7 U3    | 
| Rete VSAN                  |    6,7 U3    |
| NSX-T                 |      2.5     |

Per qualsiasi nuovo cluster in un cloud privato, la versione del software corrisponde a quella attualmente in esecuzione. Per ogni nuovo cloud privato in una sottoscrizione, viene installata la versione più recente dello stack di software.

È possibile trovare i criteri e i processi di aggiornamento generali per il software della piattaforma della soluzione VMware di Azure descritta in [aggiornamenti e aggiornamenti del cloud privato](concepts-upgrades.md).

## <a name="host-maintenance-and-lifecycle-management"></a>Gestione del ciclo di vita e manutenzione dell'host

La gestione del ciclo di vita e la manutenzione dell'host non hanno alcun effetto sulla capacità o sulle prestazioni dei cluster di cloud privati.  Esempi di manutenzione automatica degli host includono gli aggiornamenti del firmware e la riparazione o la sostituzione dell'hardware.

Microsoft è responsabile della gestione del ciclo di vita di appliance NSX-T, ad esempio NSX-T Manager e NSX-T Edge. Sono anche responsabili dell'avvio della configurazione di rete, ad esempio la creazione del gateway di livello 0 e l'abilitazione del routing North-South. Si è responsabili della configurazione di NSX-T SDN. Ad esempio, i segmenti di rete, le regole del firewall distribuite, i gateway di livello 1 e i bilanciamenti del carico.

> [!IMPORTANT]
> Non modificare la configurazione del gateway NSX-T Edge o di livello 0, in quanto ciò potrebbe causare una perdita di servizio.

## <a name="backup-and-restoration"></a>Backup e ripristino

Le configurazioni vCenter e NSX-T del cloud privato sono basate su una pianificazione di backup orario.  I backup vengono conservati per tre giorni. Se è necessario eseguire il ripristino da un backup, aprire una [richiesta di supporto](https://rc.portal.azure.com/#create/Microsoft.Support) nella portale di Azure per richiedere il ripristino.

## <a name="next-steps"></a>Passaggi successivi

Il passaggio successivo consiste nell'apprendere i [concetti relativi alla rete e all'interconnettività](concepts-networking.md).

<!-- LINKS - internal -->

<!-- LINKS - external-->
[VCSA versions]: https://kb.vmware.com/s/article/2143838
[ESXi versions]: https://kb.vmware.com/s/article/2143832
[vSAN versions]: https://kb.vmware.com/s/article/2150753

