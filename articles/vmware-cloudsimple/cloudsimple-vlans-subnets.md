---
title: VLAN e subnet nella soluzione di VMware da CloudSimple - Azure
description: Informazioni sulle VLAN e subnet in un cloud privato CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e88977cc4d99df176116e6be7d8e06adb6297782
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65209570"
---
# <a name="vlans-and-subnets-overview"></a>Panoramica di VLAN e subnet

CloudSimple fornisce una rete per ogni area in cui viene distribuito il servizio CloudSimple.  La rete è un singolo spazio di indirizzi TCP Layer 3 con routing abilitato per impostazione predefinita.  Tutti i cloud privati e le subnet create in quest'area possono comunicare tra loro senza alcuna configurazione aggiuntiva.  È possibile creare gruppi di porte distribuite in vCenter con le reti VLAN.

## <a name="vlans"></a>VLAN

VLAN (rete di livello 2) vengono create per ogni cloud privato.  Il traffico di livello 2 rimane all'interno del limite di un cloud privato, permettendo di isolare il traffico locale nel cloud privato.  Una VLAN creata nel cloud privato è utilizzabile per creare i gruppi di porte distribuite solo nel cloud privato.  Una VLAN creata in un cloud privato viene configurata automaticamente in tutte le opzioni connessione agli host di un cloud privato.

## <a name="subnets"></a>Subnet

Quando si crea una VLAN, definendo lo spazio degli indirizzi della subnet, è possibile creare una subnet. Come gateway subnet viene assegnato un indirizzo IP dallo spazio degli indirizzi. Un singolo spazio di indirizzi privato di livello 3 viene assegnato a singoli clienti e area geografica. È possibile configurare spazio indirizzi non sovrapposti qualsiasi RFC 1918 con la rete locale o una rete virtuale di Azure nella propria area di rete.

Tutte le subnet possono comunicare tra loro per impostazione predefinita, riducendo la configurazione di un sovraccarico per il routing tra cloud privati. Est-ovest dati tra i computer nella stessa area rimangano nella stessa rete di livello 3 e trasferisce l'infrastruttura di rete locale all'interno dell'area. Nessun traffico in uscita è obbligatorio per la comunicazione tra cloud privati in un'area. Questo approccio consente di eliminare eventuali problemi di prestazioni WAN/in uscita nella distribuzione di carichi di lavoro diversi in diversi cloud privati.

## <a name="vspherevsan-subnets-cidr-range"></a>intervallo CIDR subnet vSphere/vSAN

Viene creato un Cloud privato come uno stack VMware isolato (host ESXi vCenter, vSAN e NSX) ambiente gestito da un server vCenter.  Gestione componenti vengono distribuiti nella rete selezionata per **vSphere/vSAN subnet CIDR**.  Intervallo CIDR della rete è suddivisa in subnet diverse durante la distribuzione.

Prefisso di intervallo minima vSphere/vSAN subnet CIDR: **/24** intervallo di subnet di vSphere/vSAN massimo CIDR prefisso:   **/21**

### <a name="vspherevsan-subnets-cidr-range-limits"></a>i limiti di intervallo vSphere/vSAN subnet CIDR

Selezionare le dimensioni dell'intervallo CIDR subnet vSphere/vSAN influisce sulla dimensione della Cloud privata.  Nella tabella seguente mostra il numero massimo di nodi che è possibile avere in base alla dimensione di vSphere/vSAN subnet CIDR.

| Lunghezza del prefisso subnet specificata di vSphere/vSAN CIDR | Numero massimo di nodi |
|---------------------------------------------------|-------------------------|
| /24 | 26 |
| /23 | 58 |
| /22 | 118 |
| /21 | 220 |

### <a name="management-subnets-created-on-a-private-cloud"></a>Subnet di gestione creata in un Cloud privato

Subnet di gestione seguenti vengono create quando si crea un Cloud privato. 

* **Gestione del sistema** -VLAN e subnet per la gestione degli host ESXi di rete, server DNS, server vCenter.
* **VMotion** -VLAN e subnet per rete vMotion degli host ESXi.
* **Rete VSAN** -VLAN e subnet per la rete vSAN degli host ESXi.
* **NsxtEdgeUplink1** -VLAN e subnet per uplink VLAN a una rete esterna.
* **NsxtEdgeUplink2** -VLAN e subnet per uplink VLAN a una rete esterna.
* **NsxtEdgeTransport** -VLAN e subnet per le zone di trasporto controllano la copertura delle reti di livello 2 in NSX-T.
* **NsxtHostTransport** -VLAN e subnet per l'host del trasporto zona.

### <a name="management-network-cidr-range-breakdown"></a>Suddivisione intervallo CIDR rete di gestione

intervallo CIDR subnet vSphere/vSAN specificato è suddiviso in più subnet.  Tabella riportata di seguito viene illustrato un esempio mostra la scomposizione di prefissi consentiti.  Nell'esempio viene usato **192.168.0.0** come intervallo CIDR.

Esempio:

| Specificato vSphere/vSAN subnet/prefisso della CIDR | 192.168.0.0/21 | 192.168.0.0/22 | 192.168.0.0/23 | 192.168.0.0/24 |
|---------------------------------|----------------|----------------|----------------|----------------|
| Gestione del sistema | 192.168.0.0/24 | 192.168.0.0/24 | 192.168.0.0/25 | 192.168.0.0/26 |
| vMotion | 192.168.1.0/24 | 192.168.1.0/25 | 192.168.0.128/26 | 192.168.0.64/27 |
| vSAN | 192.168.2.0/24 | 192.168.1.128/25 | 192.168.0.192/26 | 192.168.0.96/27 |
| Trasporto Host NSX-T | 192.168.4.0/23 | 192.168.2.0/24 | 192.168.1.0/25 | 192.168.0.128/26 |
| Trasporto Edge NSX-T | 192.168.7.208/28 | 192.168.3.208/28 | 192.168.1.208/28 | 192.168.0.208/28 |
| NSX-T Edge Uplink1 | 192.168.7.224/28 | 192.168.3.224/28 | 192.168.1.224/28 | 192.168.0.224/28 |
| Uplink2 Edge NSX-T | 192.168.7.240/28 | 192.168.3.240/28 | 192.168.1.240/28 | 192.168.0.240/28 |

## <a name="next-steps"></a>Passaggi successivi

* [Creare e gestire le VLAN e subnet](https://docs.azure.cloudsimple.com/create-vlan-subnet/)