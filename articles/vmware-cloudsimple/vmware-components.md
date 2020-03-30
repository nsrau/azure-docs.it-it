---
title: Componenti VMware per cloud privato
titleSuffix: Azure VMware Solution by CloudSimple
description: Descrive come vengono installati i componenti VMware nel cloud privato
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9c9b80cd4d8a7a7ac5597d10bbb87095564bd461
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279507"
---
# <a name="private-cloud-vmware-components"></a>Componenti di Private Cloud VMware

Un cloud privato è un ambiente vMware isolato (host ESXi, vCenter, vSAN e NSX) gestito da un server vCenter in un dominio di gestione.  Il servizio CloudSimple consente di distribuire VMware in modo nativo nell'infrastruttura bare metal di Azure nelle posizioni di Azure.The CloudSimple service allows you to deploy VMware natively on Azure bare metal infrastructure in Azure locations.  I cloud privati sono integrati con il resto del cloud di Azure.Private Clouds are integrated with the rest of the Azure Cloud.  Un cloud privato viene distribuito con i seguenti componenti dello stack VMware:

* **VMware ESXi -** Hypervisor nei nodi dedicati di AzureHypervisor on Azure dedicated nodes
* **VMware vCenter -** Appliance per la gestione centralizzata dell'ambiente vSphere del cloud privato
* **VMware vSAN -** Soluzione di infrastruttura hyperconvergente
* **VMware NSX Data Center -** Software di sicurezza e virtualizzazione di rete  

## <a name="vmware-component-versions"></a>Versioni dei componenti VMware

Uno stack VMware cloud privato viene distribuito con la versione software seguente.

| Componente | Versione | Versione con licenza |
|-----------|---------|------------------|
| Esxi | 6.7U2 (in inglese) | Enterprise Plus |
| vCenter | 6.7U2 (in inglese) | Standard vCenter |
| VSAN (informazioni in stato ine | 6.7 | Enterprise |
| NSX Data Center | 2.4.1 | Avanzate |

## <a name="esxi"></a>Esxi

VMware ESXi viene installato nei nodi CloudSimple di cui è stato eseguito il provisioning quando si crea un cloud privato.  ESXi fornisce l'hypervisor per la distribuzione di macchine virtuali (VM) del carico di lavoro.  I nodi forniscono un'infrastruttura iperconvergente (calcolo e archiviazione) nel cloud privato.  I nodi fanno parte del cluster vSphere nel cloud privato.  Ogni nodo dispone di quattro interfacce di reti fisiche connesse alla rete sottoposta.  Due interfacce di rete fisica vengono utilizzate per creare un **vSphere Distributed Switch (VDS)** su vCenter e due per creare un **commutatore virtuale distribuito (N-VDS) gestito da NSX.**  Le interfacce di rete sono configurate in modalità attivo-attivo per la disponibilità elevata.Network interfaces are configured in active-active mode for high availability.

Scopri di più su VMware ESXi

## <a name="vcenter-server-appliance"></a>Appliance server vCenter

VCenter server appliance (VCSA) fornisce le funzioni di autenticazione, gestione e orchestrazione per VMware Solution by CloudSimple. VCSA con controller di servizi di piattaforma incorporato (PSC) viene distribuito quando si crea il cloud privato.  VCSA viene distribuito nel cluster vSphere creato quando si distribuisce il cloud privato.  Ogni cloud privato ha il proprio VCSA.  L'espansione di un cloud privato aggiunge i nodi al VCSA nel cloud privato.

### <a name="vcenter-single-sign-on"></a>Single Sign-On di vCenter

Embedded Platform Services Controller in VCSA è associato a un **dominio Single Sign-On vCenter.**  Il nome di dominio è **cloudsimple.local**.  Viene creato **CloudOwner@cloudsimple.com** un utente predefinito per l'accesso a vCenter.  È possibile aggiungere le origini di [identità](set-vcenter-identity.md)di Active Directory locali/Azure per vCenter .

## <a name="vsan-storage"></a>Archiviazione vSAN

I cloud privati vengono creati con l'archiviazione vSAN all-flash completamente configurata, locale nel cluster.  Sono necessari almeno tre nodi dello stesso SKU per creare un cluster vSphere con datastore vSAN.  La deduplicazione e la compressione sono abilitate nell'archivio dati vSAN per impostazione predefinita.  Vengono creati due gruppi di dischi in ogni nodo del cluster vSphere. Ogni gruppo di dischi contiene un disco cache e tre dischi di capacità.

Un criterio di archiviazione vSAN predefinito viene creato nel cluster vSphere e applicato all'archivio dati vSAN.  Questo criterio determina il modo in cui viene eseguito il provisioning e l'allocazione degli oggetti di archiviazione della macchina virtuale all'interno dell'archivio dati per garantire il livello di servizio richiesto.  I criteri di archiviazione definiscono gli **errori da tollerare (FTT)** e il metodo di **tolleranza**degli errori .  È possibile creare nuovi criteri di archiviazione e applicarli alle macchine virtuali. Per mantenere il servizio di archiviazione, è necessario mantenere la capacità di riserva del 25% nell'archivio dati vSAN.  

### <a name="default-vsan-storage-policy"></a>Criteri di archiviazione vSAN predefiniti

La tabella seguente mostra i parametri dei criteri di archiviazione vSAN predefiniti.

| Numero di nodi nel cluster vSphere | Itf | Metodo di tolleranza agli errori |
|------------------------------------|-----|--------------------------|
| 3 e 4 nodi | 1 | RAID 1 (mirroring) - crea 2 copie |
| Da 5 a 16 nodi | 2 | RAID 1 (mirroring) - crea 3 copie |

## <a name="nsx-data-center"></a>NSX Data Center

NSX Data Center offre funzionalità di virtualizzazione di rete, microsegmentazione e sicurezza di rete nel cloud privato.  È possibile configurare tutti i servizi supportati da NSX Data Center nel cloud privato tramite NSX.  Quando si crea un cloud privato, vengono installati e configurati i seguenti componenti NSX.

* Responsabile NSXT
* Aree di trasporto
* Profilo di Uplink host ed Edge
* Commutatore logico per Trasporto Edge, Ext1 ed Ext2
* Pool IP per il nodo di trasporto ESXi
* Nodo Pool IP per trasporto Edge
* Nodi perimetrali
* Regola anti-affinità DRS per macchine virtuali controller ed Edge
* Router di livello 0Tier 0 Router
* Abilitare BGP sul router di livello 0Enable BGP on Tier0 Router

## <a name="vsphere-cluster"></a>Cluster vSphere

Gli host ESXi sono configurati come cluster per garantire la disponibilità elevata del cloud privato.  Quando si crea un cloud privato, i componenti di gestione di vSphere vengono distribuiti nel primo cluster.  Viene creato un pool di risorse per i componenti di gestione e tutte le macchine virtuali di gestione vengono distribuite in questo pool di risorse. Il primo cluster non può essere eliminato per ridurre il cloud privato.  Il cluster vSphere offre disponibilità elevata per le macchine virtuali usando **vSphere HA**.  Gli errori da tollerare si basano sul numero di nodi disponibili nel cluster.  È possibile utilizzare ```Number of nodes = 2N+1``` ```N``` la formula in cui è il numero di errori da tollerare.

### <a name="vsphere-cluster-limits"></a>Limiti del cluster vSphere

| Risorsa | Limite |
|----------|-------|
| Numero minimo di nodi per creare un cloud privato (primo cluster vSphere)Minimum number of nodes to create a private cloud (first vSphere cluster) | 3 |
| Numero massimo di nodi in un cluster vSphere in un cloud privatoMaximum number of nodes in a vSphere Cluster on a private cloud | 16 |
| Numero massimo di nodi in un cloud privatoMaximum number of nodes in a private cloud | 64 |
| Numero massimo di cluster vSphere in un cloud privato | 21 |
| Numero minimo di nodi in un nuovo cluster vSphere | 3 |

## <a name="vmware-infrastructure-maintenance"></a>Manutenzione dell'infrastruttura VMware

Occasionalmente è necessario apportare modifiche alla configurazione dell'infrastruttura VMware. Attualmente, questi intervalli possono verificarsi ogni 1-2 mesi, ma si prevede che la frequenza diminuisca nel tempo. Questo tipo di manutenzione di solito può essere fatto senza interrompere il normale consumo dei servizi CloudSimple. Durante un intervallo di manutenzione di VMware, i seguenti servizi continuano a funzionare senza alcun impatto:

* Piano di gestione VMware e applicazioni
* Accesso al vCenter
* Tutte le reti e lo storage
* Tutto il traffico di AzureAll Azure traffic

## <a name="updates-and-upgrades"></a>Aggiornamenti e aggiornamenti

CloudSimple è responsabile della gestione del ciclo di vita del software VMware (ESXi, vCenter, PSC e NSX) nel cloud privato.

Gli aggiornamenti software includono:

* **Patches**. Patch di sicurezza o correzioni di bug rilasciate da VMware.
* **Aggiornamenti**. Modifica della versione secondaria di un componente dello stack VMware.
* **Aggiornamenti**. Modifica principale della versione di un componente dello stack VMware.

CloudSimple testa una patch di sicurezza critica non appena diventa disponibile da VMware. Per sLA, CloudSimple implementa la patch di sicurezza in ambienti cloud privati entro una settimana.

CloudSimple fornisce aggiornamenti di manutenzione trimestrali ai componenti software VMware. Quando è disponibile una nuova versione principale del software VMware, CloudSimple collabora con i clienti per coordinare una finestra di manutenzione adatta per l'aggiornamento.  

## <a name="next-steps"></a>Passaggi successivi

* [Manutenzione e aggiornamenti CloudSimple](cloudsimple-maintenance-updates.md)
