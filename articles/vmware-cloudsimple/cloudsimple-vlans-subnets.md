---
title: VLAN e subnet in Azure VMware Solution by CloudSimple
description: Informazioni sulle VLAN e sulle subnet in CloudSimple Private Cloud
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2451fbb69636624db354006df2a7925ef9e75459
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024892"
---
# <a name="vlans-and-subnets-overview"></a>Panoramica delle VLAN e delle subnet

CloudSimple fornisce una rete per ogni area in cui viene distribuito il servizio CloudSimple.CloudSimple provides a network per region where your CloudSimple service is deployed.  La rete è un singolo spazio di indirizzi TCP Layer 3 con routing abilitato per impostazione predefinita.  Tutti i cloud privati e le subnet creati in questa area possono comunicare tra loro senza alcuna configurazione aggiuntiva.  È possibile creare gruppi di porte distribuite nel vCenter utilizzando le VLAN.

![Topologia di rete semplice cloud](media/cloudsimple-network-topology.png)

## <a name="vlans"></a>VLAN

Viene creata una VLAN (rete di livello 2) per ogni cloud privato.  Il traffico di livello 2 rimane entro i limiti di un cloud privato, consentendo di isolare il traffico locale all'interno del cloud privato.  Una VLAN creata nel cloud privato può essere utilizzata per creare gruppi di porte distribuite solo in tale cloud privato.  Una VLAN creata su un cloud privato viene configurata automaticamente su tutti gli switch connessi agli host di un cloud privato.

## <a name="subnets"></a>Subnet

È possibile creare una subnet quando si crea una VLAN definendo lo spazio di indirizzi della subnet. Un indirizzo IP dallo spazio di indirizzi viene assegnato come gateway di subnet. Viene assegnato un singolo spazio di indirizzi privato di livello 3 per cliente e area geografica. È possibile configurare qualsiasi spazio di indirizzi RFC 1918 non sovrapposto, con la rete locale o la rete virtuale di Azure, nell'area di rete.

Tutte le subnet possono comunicare tra loro per impostazione predefinita, riducendo l'overhead di configurazione per il routing tra cloud privati. I dati est-ovest tra PC nella stessa area rimangono nella stessa rete di livello 3 e si trasferiscono tramite l'infrastruttura di rete locale all'interno dell'area. Non è necessaria alcuna uscita per la comunicazione tra cloud privati in un'area. Questo approccio elimina qualsiasi riduzione delle prestazioni WAN/egress nella distribuzione di carichi di lavoro diversi in cloud privati diversi.

## <a name="vspherevsan-subnets-cidr-range"></a>Intervallo CIDR delle subnet vSphere/vSAN

Un cloud privato viene creato come un ambiente VMware isolato (host ESXi, vCenter, vSAN e NSX) gestito da un server vCenter.  I componenti di gestione vengono distribuiti nella rete selezionata per le subnet vSphere/vSAN CIDR.  L'intervallo CIDR di rete è suddiviso in diverse subnet durante la distribuzione.

* Prefisso intervallo CIDR delle subnet minime vSphere/vSAN: **/24**
* Prefisso intervallo CIDR delle subnet massime vSphere/vSAN: **/21Maximum** vSphere/vSAN subnets CIDR range prefix: /21

> [!CAUTION]
> Gli indirizzi IP nell'intervallo CIDR vSphere/vSAN sono riservati per l'utilizzo da parte dell'infrastruttura Private Cloud.  Non usare l'indirizzo IP in questo intervallo in qualsiasi macchina virtuale.

### <a name="vspherevsan-subnets-cidr-range-limits"></a>Limiti dell'intervallo CIDR delle subnet vSphere/vSAN

La selezione delle dimensioni dell'intervallo CIDR delle subnet vSphere/vSAN influisce sulle dimensioni del cloud privato.  Nella tabella seguente viene illustrato il numero massimo di nodi che è possibile avere in base alle dimensioni delle subnet vSphere/vSAN CIDR.

| Lunghezza prefisso CIDR delle subnet vSphere/vSAN specificate | Numero massimo di nodi |
|---------------------------------------------------|-------------------------|
| /24 | 26 |
| /23 | 58 |
| /22 | 118 |
| /21 | 220 |

### <a name="management-subnets-created-on-a-private-cloud"></a>Subnet di gestione create in un cloud privato

Le subnet di gestione seguenti vengono create quando si crea un cloud privato.

* **Gestione del sistema**. VLAN e subnet per la rete di gestione degli host ESXi, il server DNS, il server vCenter.
* **VMotion**. VLAN e subnet per la rete vMotion degli host ESXi.
* **VSAN**. VLAN e subnet per la rete vSAN degli host ESXi.
* **NsxtEdgeUplink1**. VLAN e subnet per uplink VLAN a una rete esterna.
* **NsxtEdgeUplink2**. VLAN e subnet per uplink VLAN a una rete esterna.
* **NsxtEdgeTransport**. VLAN e subnet per le zone di trasporto controllano la portata delle reti di livello 2 in NSX-T.
* **NsxtHostTransport**. VLAN e subnet per la zona di trasporto host.

### <a name="management-network-cidr-range-breakdown"></a>Gestione della rete di gestione Disgregazione gamma CIDR

L'intervallo CIDR specificato per le subnet vSphere/vSAN è diviso in più subnet.  Nella tabella seguente viene illustrato un esempio della suddivisione per i prefissi consentiti.  Nell'esempio viene utilizzato 192.168.0.0 come intervallo CIDR.

Esempio:

| Subnet vSphere/vSAN specificate CIDR/prefix | 192.168.0.0/21 | 192.168.0.0/22 | 192.168.0.0/23 | 192.168.0.0/24 |
|---------------------------------|----------------|----------------|----------------|----------------|
| Gestione del sistema | 192.168.0.0/24 | 192.168.0.0/24 | 192.168.0.0/25 | 192.168.0.0/26 |
| Vmotion | 192.168.1.0/24 | 192.168.1.0/25 | 192.168.0.128/26 | 192.168.0.64/27 |
| VSAN (informazioni in stato ine | 192.168.2.0/24 | 192.168.1.128/25 | 192.168.0.192/26 | 192.168.0.96/27 |
| Trasporto host NSX-T | 192.168.4.0/23 | 192.168.2.0/24 | 192.168.1.0/25 | 192.168.0.128/26 |
| Trasporto Edge NSX-T | 192.168.7.208/28 | 192.168.3.208/28 | 192.168.1.208/28 | 192.168.0.208/28 |
| NSX-T Bordo Uplink1 | 192.168.7.224/28 | 192.168.3.224/28 | 192.168.1.224/28 | 192.168.0.224/28 |
| NSX-T Bordo uplink2 | 192.168.7.240/28 | 192.168.3.240/28 | 192.168.1.240/28 | 192.168.0.240/28 |

## <a name="next-steps"></a>Passaggi successivi

* [Creare e gestire VLAN e subnet](create-vlan-subnet.md)
