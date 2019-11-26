---
title: Garantire la disponibilità elevata dell'applicazione durante l'esecuzione in VMware in Azure
description: Descrive le funzionalità di disponibilità elevata di CloudSimple per risolvere scenari comuni di errore dell'applicazione per le applicazioni in esecuzione in un cloud privato CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a3eed033ba6a1a6f9237116a53ec7751ae906fe4
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74206535"
---
# <a name="ensure-application-high-availability-when-running-in-vmware-on-azure"></a>Garantire la disponibilità elevata dell'applicazione durante l'esecuzione in VMware in Azure

La soluzione CloudSimple offre disponibilità elevata per le applicazioni in esecuzione su VMware nell'ambiente Azure. La tabella seguente elenca gli scenari di errore e le funzionalità di disponibilità elevata associate.

| Scenario di errore | Applicazione protetta | Funzionalità disponibilità elevata piattaforma | Funzionalità disponibilità elevata VMware | Funzionalità disponibilità elevata di Azure |
------------ | ------------- | ------------ | ------------ | ------------- |
| Errore del disco | SÌ | Sostituzione rapida del nodo non riuscito | [Informazioni sui criteri di archiviazione predefiniti di rete VSAN](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-C228168F-6807-4C2A-9D74-E584CAF49A2A.html) |
| Errore della ventola | SÌ | Ventilatori ridondanti, sostituzione rapida del nodo non riuscito |  |  |
| Errore NIC | SÌ | NIC ridondante, sostituzione rapida del nodo non riuscito
| Errore di alimentazione dell'host | SÌ | Alimentatore ridondante |  |  |
| Errore host ESXi | SÌ | Sostituzione rapida del nodo non riuscito | [Disponibilità elevata VMware vSphere](https://www.vmware.com/products/vsphere/high-availability.html) |  |  |
| Errore della macchina virtuale | SÌ | [Servizi di bilanciamento del carico](load-balancers.md)  | [Disponibilità elevata VMware vSphere](https://www.vmware.com/products/vsphere/high-availability.html) | Azure Load Balancer per le macchine virtuali VMware senza stato |
| Errore della porta di commutazione foglia | SÌ | SCHEDA di interfaccia di rete ridondante |  |  |
| Errore switch foglia | SÌ | Commutatori foglia ridondanti |  |  |
| Errore rack | SÌ | Gruppi di posizionamento |  |  |
| Connettività di rete al controller di dominio locale | SÌ  | Servizi di rete ridondanti |  | Circuiti ER ridondanti |
| Connettività di rete ad Azure | SÌ | |  | Circuiti ER ridondanti |
| Errore del Data Center | SÌ |  |  | Zone di disponibilità |
| Errore a livello di area | SÌ  |  |  | Aree di Azure |

La soluzione VMware di Azure di CloudSimple offre le seguenti funzionalità di disponibilità elevata.

## <a name="fast-replacement-of-failed-node"></a>Sostituzione rapida del nodo non riuscito

Il software del piano di controllo CloudSimple monitora costantemente l'integrità dei cluster VMware e rileva quando un nodo ESXi ha esito negativo. Viene quindi aggiunto automaticamente un nuovo host ESXi al cluster VMware interessato dal pool di nodi immediatamente disponibili e viene eliminato il nodo in errore dal cluster. Questa funzionalità garantisce che la capacità di riserva nel cluster VMware venga ripristinata rapidamente in modo da ripristinare la resilienza del cluster fornita da rete VSAN e VMware HA.

## <a name="placement-groups"></a>Gruppi di posizionamento

Un utente che crea un cloud privato può selezionare un'area di Azure e un gruppo di posizionamento all'interno dell'area selezionata. Un gruppo di posizionamento è un set di nodi distribuiti tra più rack, ma all'interno dello stesso segmento di rete di rotazione. I nodi all'interno dello stesso gruppo di posizionamento possono raggiungere reciprocamente un massimo di due hop di commutazione aggiuntivi. Un gruppo di posizionamento è sempre all'interno di una singola zona di disponibilità di Azure e si estende su più rack. Il piano di controllo CloudSimple distribuisce i nodi di un cloud privato tra più rack in base al massimo sforzo. I nodi in gruppi di posizionamento diversi sono sicuramente posizionati in rack diversi.

## <a name="availability-zones"></a>Zone di disponibilità

Le zone di disponibilità sono un'offerta a disponibilità elevata che protegge le applicazioni e i dati dagli errori dei data center. Le zone di disponibilità sono posizioni fisiche speciali all'interno di un'area di Azure. Ogni zona è costituita da uno o più data center dotati di impianti indipendenti per l'alimentazione, il raffreddamento e la connettività di rete. Ogni area ha una zona di disponibilità. Per ulteriori informazioni, vedere [che cosa sono zone di disponibilità in Azure?](../availability-zones/az-overview.md).

## <a name="redundant-azure-expressroute-circuits"></a>Circuiti ExpressRoute di Azure ridondanti

La connettività dei data center ad Azure vNet usando ExpressRoute ha circuiti ridondanti per fornire collegamenti di connettività di rete a disponibilità elevata.

## <a name="redundant-networking-services"></a>Servizi di rete ridondanti

Tutti i servizi di rete CloudSimple per il cloud privato, tra cui VLAN, firewall, indirizzi IP pubblici, Internet e VPN, sono progettati per essere a disponibilità elevata e in grado di supportare il contratto di servizio.

## <a name="azure-layer-7-load-balancer-for-stateless-vmware-vms"></a>Load Balancer di Azure Layer 7 per macchine virtuali VMware senza stato

Gli utenti possono inserire un'Load Balancer di livello 7 di Azure davanti alle macchine virtuali di livello Web senza stato in esecuzione nell'ambiente VMware per ottenere la disponibilità elevata per il livello Web.

## <a name="azure-regions"></a>Aree di Azure

Un'area di Azure è un set di data center distribuiti in un perimetro definito dalla latenza e connessi tramite una rete a bassa latenza regionale dedicata. Per informazioni dettagliate, vedere [aree di Azure](https://azure.microsoft.com/global-infrastructure/regions).
