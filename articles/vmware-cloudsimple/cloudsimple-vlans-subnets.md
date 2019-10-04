---
title: VLAN e subnet nella soluzione VMware di Azure di CloudSimple
description: Informazioni sulle VLAN e sulle subnet nel cloud privato CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 7af191893d6b3cf1c38e5ff44a7a8a04509347a8
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69543790"
---
# <a name="vlans-and-subnets-overview"></a>Panoramica di VLAN e subnet

CloudSimple fornisce una rete per area in cui viene distribuito il servizio CloudSimple.  La rete è un singolo spazio di indirizzi TCP di livello 3 con routing abilitato per impostazione predefinita.  Tutti i cloud privati e le subnet creati in questa area possono comunicare tra loro senza alcuna configurazione aggiuntiva.  È possibile creare gruppi di porte distribuite in vCenter usando le VLAN.

![Topologia di rete CloudSimple](media/cloudsimple-network-topology.png)

## <a name="vlans"></a>VLAN

Viene creata una VLAN (rete di livello 2) per ogni cloud privato.  Il traffico di livello 2 rimane entro i limiti di un cloud privato, consentendo di isolare il traffico locale all'interno del cloud privato.  Una VLAN creata nel cloud privato può essere utilizzata per creare gruppi di porte distribuiti solo in tale cloud privato.  Una VLAN creata in un cloud privato viene configurata automaticamente in tutti i commutatori connessi agli host di un cloud privato.

## <a name="subnets"></a>Subnet

È possibile creare una subnet quando si crea una VLAN definendo lo spazio degli indirizzi della subnet. Un indirizzo IP dallo spazio di indirizzi viene assegnato come gateway della subnet. Per ogni cliente e area viene assegnato un singolo spazio di indirizzi privato di livello 3. È possibile configurare qualsiasi spazio di indirizzi non sovrapposto RFC 1918, con la rete locale o la rete virtuale di Azure, nell'area di rete.

Per impostazione predefinita, tutte le subnet possono comunicare tra loro, riducendo il sovraccarico della configurazione per il routing tra cloud privati. I dati East-West tra PC nella stessa area rimangono nella stessa rete di livello 3 e vengono trasferiti tramite l'infrastruttura di rete locale all'interno dell'area. Non è necessario alcun uscita per la comunicazione tra cloud privati in un'area. Questo approccio elimina qualsiasi riduzione delle prestazioni della rete WAN/in uscita per la distribuzione di carichi di lavoro diversi in cloud privati diversi.

## <a name="vspherevsan-subnets-cidr-range"></a>intervallo CIDR delle subnet vSphere/rete VSAN

Un cloud privato viene creato come ambiente VMware stack isolato (host ESXi, vCenter, rete VSAN e NSX) gestito da un server vCenter.  I componenti di gestione vengono distribuiti nella rete selezionata per la CIDR delle subnet vSphere/rete VSAN.  L'intervallo CIDR di rete è diviso in subnet diverse durante la distribuzione.

* Prefisso intervallo CIDR per le subnet vSphere/rete VSAN minimo: **/24**
* Prefisso intervallo CIDR per le subnet vSphere/rete VSAN massimo: **/21**

> [!IMPORTANT]
> Gli indirizzi IP nell'intervallo CIDR vSphere/rete VSAN sono riservati per l'uso da parte dell'infrastruttura di cloud privato.  Non usare l'indirizzo IP in questo intervallo in una macchina virtuale.

### <a name="vspherevsan-subnets-cidr-range-limits"></a>limiti di intervallo CIDR per le subnet vSphere/rete VSAN

La selezione delle dimensioni dell'intervallo CIDR della subnet vSphere/rete VSAN ha un effetto sulle dimensioni del cloud privato.  La tabella seguente illustra il numero massimo di nodi che è possibile avere in base alle dimensioni del CIDR di vSphere/rete VSAN subnet.

| Lunghezza prefisso CIDR per le subnet vSphere/rete VSAN specificata | Numero massimo di nodi |
|---------------------------------------------------|-------------------------|
| /24 | 26 |
| /23 | 58 |
| /22 | 118 |
| /21 | 220 |

### <a name="management-subnets-created-on-a-private-cloud"></a>Subnet di gestione create in un cloud privato

Quando si crea un cloud privato, vengono create le subnet di gestione seguenti.

* **Gestione del sistema**. VLAN e subnet per la rete di gestione, il server DNS e il server vCenter degli host ESXi.
* **VMotion**. VLAN e subnet per host ESXi rete.
* **RETE VSAN**. VLAN e subnet per la rete rete VSAN degli host ESXi.
* **NsxtEdgeUplink1**. VLAN e subnet per il collegamento VLAN a una rete esterna.
* **NsxtEdgeUplink2**. VLAN e subnet per il collegamento VLAN a una rete esterna.
* **NsxtEdgeTransport**. VLAN e subnet per le zone di trasporto controllano la portata delle reti di livello 2 in NSX-T.
* **NsxtHostTransport**. VLAN e subnet per la zona di trasporto host.

### <a name="management-network-cidr-range-breakdown"></a>Suddivisione intervallo CIDR rete di gestione

l'intervallo CIDR delle subnet vSphere/rete VSAN specificato è diviso in più subnet.  Nella tabella seguente viene illustrato un esempio di suddivisione dei prefissi consentiti.  Nell'esempio viene utilizzato 192.168.0.0 come intervallo CIDR.

Esempio:

| Prefisso/CIDR della subnet vSphere/rete VSAN specificato | 192.168.0.0/21 | 192.168.0.0/22 | 192.168.0.0/23 | 192.168.0.0/24 |
|---------------------------------|----------------|----------------|----------------|----------------|
| Gestione del sistema | 192.168.0.0/24 | 192.168.0.0/24 | 192.168.0.0/25 | 192.168.0.0/26 |
| vMotion | 192.168.1.0/24 | 192.168.1.0/25 | 192.168.0.128/26 | 192.168.0.64/27 |
| vSAN | 192.168.2.0/24 | 192.168.1.128/25 | 192.168.0.192/26 | 192.168.0.96/27 |
| Trasporto host NSX-T | 192.168.4.0/23 | 192.168.2.0/24 | 192.168.1.0/25 | 192.168.0.128/26 |
| Trasporto Edge NSX-T | 192.168.7.208/28 | 192.168.3.208/28 | 192.168.1.208/28 | 192.168.0.208/28 |
| Uplink1 Edge NSX-T | 192.168.7.224/28 | 192.168.3.224/28 | 192.168.1.224/28 | 192.168.0.224/28 |
| Uplink2 Edge NSX-T | 192.168.7.240/28 | 192.168.3.240/28 | 192.168.1.240/28 | 192.168.0.240/28 |

## <a name="next-steps"></a>Passaggi successivi

* [Creare e gestire VLAN e subnet](create-vlan-subnet.md)
