---
title: Soluzione Azure di VMware da CloudSimple - i componenti VMware di cloud privato
description: Viene descritto come vengono installati i componenti VMware su cloud privato
author: sharaths-cs
ms.author: dikamath
ms.date: 04/30/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 62511118edb4f8b5061f90138bac2aa2b5d3cfe3
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165160"
---
# <a name="private-cloud-vmware-components"></a>Componenti VMware di cloud privato

Un cloud privato è uno stack VMware isolato (host ESXi vCenter, vSAN e NSX) ambiente gestito da un server vCenter in un dominio di gestione.  Il servizio CloudSimple consente di distribuire in modo nativo VMware sul ripristino bare metal dell'infrastruttura di Azure in posizioni di Azure.  I cloud privati sono integrati con il resto del Cloud di Azure.  Un cloud privato viene distribuito con i componenti dello stack VMware seguenti:

* **VMware ESXi -** nodi dedicati Hypervisor in Azure
* **Server VMware vCenter -** Appliance per la gestione centralizzata dell'ambiente di cloud privato vSphere
* **Rete vSAN di VMware -** soluzione di infrastruttura iperconvergente
* **Data Center di VMware NSX -** virtualizzazione e Software per la sicurezza di rete  

## <a name="vmware-component-versions"></a>Versioni del componente VMware

Stack di VMware un cloud privato viene distribuito con la versione del software seguente.

| Componente | Version | Versione con licenza |
|-----------|---------|------------------|
| ESXi | 6.7U1 | Enterprise Plus |
| vCenter | 6.7U1 | vCenter Standard |
| vSAN | 6.7 | Enterprise |
| NSX Data Center | 2.3 | Avanzate |

## <a name="esxi"></a>ESXi

VMware ESXi viene installato nei nodi CloudSimple sottoposte a provisioning, quando si crea un cloud privato.  ESXi fornisce l'hypervisor per la distribuzione di macchine virtuali del carico di lavoro (VM).  Nodi forniscono infrastruttura iperconvergente (calcolo e archiviazione) nel cloud privato.  I nodi sono una parte del cluster vSphere nel cloud privato.  Ogni nodo dispone di quattro interfacce di rete fisica connessa a Metti sotto di rete.  Due interfacce di rete fisica utilizzate per creare un **vSphere Distributed Switch (VDS)** in vCenter e due vengono usate per creare un **gestito NSX distribuito il commutatore virtuale (N-VDS)** .  Interfacce di rete sono configurate in modalità attivo-attivo per la disponibilità elevata.

Altre informazioni su VMware ESXi

## <a name="vcenter-server-appliance"></a>appliance di vCenter server

appliance di server vCenter (VCSA) fornisce le funzioni di autenticazione, la gestione e orchestrazione per la soluzione VMware da CloudSimple. VCSA con incorporata della piattaforma servizi Controller (PSC) viene distribuito quando si crea un cloud privato.  VCSA viene distribuito nel cluster vSphere che viene creato quando si distribuisce il proprio cloud privato.  Ogni cloud privato ha un proprio VCSA.  Espansione di un cloud privato aggiunge i nodi per il VCSA nel cloud privato.

### <a name="vcenter-single-sign-on"></a>vCenter single sign-on

Controller di servizi di piattaforma incorporata in VCSA è associato un **vCenter Single Sign-On dominio**.  È il nome di dominio **cloudsimple.local**.  Un utente predefinito **CloudOwner@cloudsimple.com** viene creato accedere a vCenter.  È possibile aggiungere on-premises o Azure active directory [origini di identità per vCenter](https://docs.azure.cloudsimple.com/set-vcenter-identity/).

## <a name="vsan-storage"></a>archiviazione di rete vSAN

I cloud privati vengono creati con archiviazione completamente configurata vSAN all-flash, locale al cluster.  Numero minimo di tre nodi dello stesso SKU è necessari per creare un cluster vSphere con archivio dati vSAN.  Deduplicazione e compressione siano abilitati nell'archivio dati vSAN per impostazione predefinita.  Vengono creati due gruppi di dischi in ogni nodo del cluster vSphere. Ogni gruppo di dischi contiene un disco della cache e tre i dischi di capacità.

Un criterio di archiviazione di rete vSAN predefinito viene applicato per l'archivio dati vSAN e creato nel cluster vSphere.  Questi criteri determinano come gli oggetti di archiviazione della macchina virtuale vengono effettuato il provisioning e allocati all'interno dell'archivio dati per garantire il livello di servizio richiesto.  I criteri di archiviazione definiscono la **errori da tollerare (FTT)** e il **metodo tolleranza di errore**.  È possibile creare nuovi criteri di archiviazione e applicarle alle macchine virtuali. Per mantenere il contratto di servizio, è necessario mantenere la capacità di riserva del 25% sull'archivio dati vSAN.  

### <a name="default-vsan-storage-policy"></a>Criteri di archiviazione predefinito rete vSAN

Tabella riportata di seguito viene illustrata l'impostazione predefinita i parametri dei criteri di archiviazione di rete vSAN.

| Numero di nodi nel Cluster vSphere | FTT | Metodo di tolleranza di errore |
|------------------------------------|-----|--------------------------|
| 3 e 4 nodi | 1 | RAID 1 (mirroring) - Crea 2 copie |
| da 5 a 16 nodi | 2 | RAID 1 (mirroring) - Crea 3 copie |

## <a name="nsx-data-center"></a>NSX Data Center

NSX Data Center offre la virtualizzazione di rete, Micro-segmentazione e funzionalità di sicurezza di rete nel cloud privato.  È possibile configurare tutti i servizi supportati da NSX Data Center sul cloud privato tramite NSX.  Quando si crea un cloud privato, i componenti NSX seguenti vengono installati e configurati.

* NSXT Manager
* Le zone di trasporto
* Host e il profilo Uplink Edge
* Commutatore logico per il trasporto Edge, Ext1 ed Ext2
* Pool di indirizzi IP per il nodo di trasporto ESXi
* Pool di indirizzi IP per il nodo di Trasporto Edge
* Nodi perimetrali
* Regola di DRS anti-affinity per controller e le macchine virtuali Edge
* Tier 0 Router
* Abilitare BGP sul Router Tier0

## <a name="vsphere-cluster"></a>cluster vSphere

Host ESXi sono configurati come cluster per assicurare disponibilità elevata del cloud privato.  Quando si crea un cloud privato, nel primo cluster vengono distribuiti i componenti di gestione di vSphere.  Per i componenti di gestione viene creato un pool di risorse e tutte le macchine virtuali di gestione vengono distribuite nel pool di risorse. Il primo cluster non può essere eliminato per compattare il cloud privato.  cluster vSphere garantisce un'elevata disponibilità per le macchine virtuali usando **vSphere a disponibilità elevata**.  Errori da tollerare si basano sul numero di nodi disponibili nel cluster.  È possibile usare la formula ```Number of nodes = 2N+1``` in cui ```N``` è il numero di errori da tollerare.

### <a name="vsphere-cluster-limits"></a>limiti di cluster vSphere

| Resource | Limite |
|----------|-------|
| Numero minimo di nodi per creare un cloud privato (primo cluster vSphere) | 3 |
| Numero massimo di nodi in un vSphere Cluster in un cloud privato | 16 |
| Numero massimo di nodi in un cloud privato | 64 |
| Numero massimo di vSphere cluster in un cloud privato | 21 |
| Numero minimo di nodi in un nuovo Cluster di vSphere | 3 |

## <a name="vmware-infrastructure-maintenance"></a>Manutenzione dell'infrastruttura VMware

In alcuni casi è necessario apportare modifiche alla configurazione dell'infrastruttura VMware. Attualmente, questi intervalli possono verificarsi ogni 1-2 mesi, ma la frequenza è previsto per rifiutare nel corso del tempo. Questo tipo di manutenzione può essere in genere eseguito senza interrompere il normale utilizzo dei servizi CloudSimple. Durante un intervallo di manutenzione di VMware, i servizi seguenti continuano a funzionare senza alcuna conseguenza:

* Le applicazioni e il piano di gestione di VMware
* accesso a vCenter
* Tutte le funzionalità di rete e archiviazione
* Tutto il traffico di Azure

## <a name="updates-and-upgrades"></a>Gli aggiornamenti

CloudSimple è responsabile della gestione del ciclo di vita del software di VMware (ESXi vCenter, PSC e NSX) in un cloud privato.

Gli aggiornamenti software includono:

* **Patch**. Patch di sicurezza o correzioni di bug rilasciate da VMware.
* **Gli aggiornamenti**. Modifica della versione secondaria di un componente dello stack VMware.
* **Gli aggiornamenti**. Modifica della versione principale di un componente dello stack VMware.

CloudSimple testa una patch di sicurezza critiche, non appena diventa disponibile da VMware. Per ogni contratto di servizio, CloudSimple implementa la patch di protezione per ambienti cloud privati all'interno di una settimana.

CloudSimple fornisce aggiornamenti di manutenzione trimestrale per i componenti software di VMware. Quando è disponibile una nuova versione principale del software di VMware, CloudSimple funziona con i clienti per coordinare una finestra di manutenzione appropriato per l'aggiornamento.  

## <a name="next-steps"></a>Passaggi successivi

* [Gli aggiornamenti e manutenzione CloudSimple](cloudsimple-maintenance-updates.md)