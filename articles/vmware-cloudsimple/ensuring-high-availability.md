---
title: Garantire la disponibilità elevata delle applicazioni durante l'esecuzione in VMware in AzureEnsure application high availability when running in VMware on Azure
description: Descrive le funzionalità di disponibilità elevata CloudSimple per risolvere scenari comuni di errore delle applicazioni per le applicazioni in esecuzione in un cloud privato CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a3eed033ba6a1a6f9237116a53ec7751ae906fe4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025351"
---
# <a name="ensure-application-high-availability-when-running-in-vmware-on-azure"></a>Garantire la disponibilità elevata delle applicazioni durante l'esecuzione in VMware in AzureEnsure application high availability when running in VMware on Azure

La soluzione CloudSimple offre un'elevata disponibilità per le applicazioni in esecuzione su VMware nell'ambiente Azure.The CloudSimple solution provides high availability for your applications running on VMware in the Azure environment. Nella tabella seguente sono elencati gli scenari di errore e le funzionalità di disponibilità elevata associate.

| Scenario di errore | Applicazione protetta? | Funzionalità HA della piattaforma | Funzionalità HA di VMware | Funzionalità DI disponibilità automatica di AzureAzure HA feature |
------------ | ------------- | ------------ | ------------ | ------------- |
| Errore del disco | YES | Sostituzione rapida del nodo non riuscito | [Informazioni sui criteri di archiviazione predefiniti di vSAN](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-C228168F-6807-4C2A-9D74-E584CAF49A2A.html) |
| Guasto della ventola | YES | Ventole ridondanti, sostituzione rapida del nodo guasto |  |  |
| Errore nic | YES | NIC ridondante, sostituzione rapida del nodo guastato
| Guasto dell'alimentazione dell'host | YES | Alimentazione ridondante |  |  |
| Errore dell'host ESXi | YES | sostituzione rapida del nodo guasto | [VMware vSphere Alta disponibilità](https://www.vmware.com/products/vsphere/high-availability.html) |  |  |
| Errore della macchina virtuale | YES | [Servizi di bilanciamento del carico](load-balancers.md)  | [VMware vSphere Alta disponibilità](https://www.vmware.com/products/vsphere/high-availability.html) | Azure Load Balancer for stateless VMware VMs |
| Errore porta interruttore foglia | YES | NIC ridondante |  |  |
| Guasto dell'interruttore foglia | YES | Interruttori a foglia ridondanti |  |  |
| Guasto rack | YES | Gruppi di posizionamento |  |  |
| Connettività di rete al controller di dominio localeNetwork Connectivity to on-premises DC | YES  | Servizi di rete ridondanti |  | Circuiti ER ridondanti |
| Connettività di rete ad AzureNetwork Connectivity to Azure | YES | |  | Circuiti ER ridondanti |
| Errore del data center | YES |  |  | Zone di disponibilità |
| Guasto regionale | YES  |  |  | Aree di Azure |

Azure VMware Solution by CloudSimple offre le seguenti funzionalità di disponibilità elevata.

## <a name="fast-replacement-of-failed-node"></a>Sostituzione rapida del nodo non riuscito

Il software del piano di controllo CloudSimple monitora continuamente l'integrità dei cluster VMware e rileva quando si verifica un errore di un nodo ESXi. Aggiunge quindi automaticamente un nuovo host ESXi al cluster VMware interessato dal pool di nodi immediatamente disponibili e porta il nodo in errore fuori dal cluster. Questa funzionalità garantisce che la capacità di riserva nel cluster VMware venga ripristinata rapidamente in modo da ripristinare la resilienza del cluster fornita da vSAN e VMware HA.

## <a name="placement-groups"></a>Gruppi di posizionamento

Un utente che crea un cloud privato può selezionare un'area di Azure e un gruppo di posizionamento all'interno dell'area selezionata. Un gruppo di posizionamento è un insieme di nodi distribuiti su più rack ma all'interno dello stesso segmento della rete spine. I nodi all'interno dello stesso gruppo di posizionamento possono raggiungersi l'un l'altro con un massimo di due hop di commutazione aggiuntivi. Un gruppo di posizionamento si trova sempre all'interno di una singola zona di disponibilità di Azure e si estende su più rack. Il piano di controllo CloudSimple distribuisce i nodi di un cloud privato su più rack in base al massimo sforzo. I nodi in diversi gruppi di posizionamento sono garantiti per essere collocati in rack diversi.

## <a name="availability-zones"></a>Zone di disponibilità

Le zone di disponibilità sono un'offerta a disponibilità elevata che protegge le applicazioni e i dati dagli errori del data center. Le zone di disponibilità sono posizioni fisiche speciali all'interno di un'area di Azure.Availability zones are special physical locations within an Azure region. Ogni zona è costituita da uno o più data center dotati di impianti indipendenti per l'alimentazione, il raffreddamento e la connettività di rete. Ogni area dispone di una zona di disponibilità. Per altre informazioni, vedere Che cosa sono le zone di [disponibilità in Azure?](../availability-zones/az-overview.md).

## <a name="redundant-azure-expressroute-circuits"></a>Circuiti di Azure ExpressRoute ridondanti

La connettività del data center alla rete virtuale di Azure tramite ExpressRoute dispone di circuiti ridondanti per fornire un collegamento di connettività di rete a disponibilità elevata.

## <a name="redundant-networking-services"></a>Servizi di rete ridondanti

Tutti i servizi di rete CloudSimple per il cloud privato (inclusi VLAN, firewall, indirizzi IP pubblici, Internet e VPN) sono progettati per essere altamente disponibili e in grado di supportare il servizio SLA.

## <a name="azure-layer-7-load-balancer-for-stateless-vmware-vms"></a>Azure Layer 7 Load Balancer for stateless VMware VMs

Gli utenti possono inserire un servizio di bilanciamento del carico di Livello 7 di Azure davanti alle macchine virtuali del livello Web senza stato in esecuzione nell'ambiente VMware per ottenere la disponibilità elevata per il livello Web.Users can put an Azure Layer 7 Load Balancer in front of the stateless web tier VMs running in the VMware environment to achieve high availability for the web tier.

## <a name="azure-regions"></a>Aree di Azure

Un'area di Azure è un set di data center distribuiti all'interno di un perimetro definito dalla latenza e connessi tramite una rete regionale dedicata a bassa latenza. Per informazioni dettagliate, vedere [Aree](https://azure.microsoft.com/global-infrastructure/regions)di Azure .For details, see Azure Regions .
