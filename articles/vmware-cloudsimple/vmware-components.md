---
title: Soluzione VMware di Azure di CloudSimple-componenti VMware del cloud privato
description: Descrive la modalità di installazione dei componenti VMware in un cloud privato
author: sharaths-cs
ms.author: dikamath
ms.date: 04/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 89bc9c07ae74da1a4269a505627a7626e478ef99
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812191"
---
# <a name="private-cloud-vmware-components"></a>Componenti VMware del cloud privato

Un cloud privato è un ambiente VMware stack isolato (host ESXi, vCenter, rete VSAN e NSX) gestito da un server vCenter in un dominio di gestione.  Il servizio CloudSimple consente di distribuire VMware in modo nativo nell'infrastruttura bare metal di Azure in località di Azure.  I cloud privati sono integrati con il resto del cloud di Azure.  Un cloud privato viene distribuito con i componenti dello stack VMware seguenti:

* **VMware ESXi-** Hypervisor nei nodi dedicati di Azure
* **VMware vCenter-** Appliance per la gestione centralizzata dell'ambiente vSphere del cloud privato
* **Rete VSAN VMware-** Soluzione di infrastruttura iperconvergente
* **Data Center VMware NSX-** Software di sicurezza e virtualizzazione di rete  

## <a name="vmware-component-versions"></a>Versioni del componente VMware

Un cloud privato VMware stack viene distribuito con la versione del software seguente.

| Componente | Version | Versione con licenza |
|-----------|---------|------------------|
| ESXi | 6.7 U1 | Enterprise Plus |
| vCenter | 6.7 U1 | Standard vCenter |
| vSAN | 6.7 | Enterprise |
| Data Center NSX | 2.3 | Avanzate |

## <a name="esxi"></a>ESXi

VMware ESXi viene installato nei nodi CloudSimple di cui è stato effettuato il provisioning quando si crea un cloud privato.  ESXi fornisce l'hypervisor per la distribuzione di macchine virtuali (VM) del carico di lavoro.  I nodi forniscono un'infrastruttura iperconvergente (calcolo e archiviazione) nel cloud privato.  I nodi fanno parte del cluster vSphere nel cloud privato.  Ogni nodo dispone di quattro interfacce di rete fisiche connesse alla rete di un sottosistema.  Due interfacce di rete fisiche vengono usate per creare un **Switch vSphere distribuito (VDS)** in vCenter e due vengono usate per creare un compartitore **virtuale gestito da NSX (N-VDS)** .  Le interfacce di rete vengono configurate in modalità attivo-attivo per la disponibilità elevata.

Scopri di più su VMware ESXi

## <a name="vcenter-server-appliance"></a>Appliance server vCenter

vCenter Server Appliance (VCSA) fornisce le funzioni di autenticazione, gestione e orchestrazione per la soluzione VMware di CloudSimple. VCSA con Embedded Platform Services controller (PSC) viene distribuito quando si crea il cloud privato.  VCSA viene distribuito nel cluster vSphere creato quando si distribuisce il cloud privato.  Ogni cloud privato ha il proprio VCSA.  L'espansione di un cloud privato aggiunge i nodi a VCSA nel cloud privato.

### <a name="vcenter-single-sign-on"></a>Single Sign-on vCenter

Il controller Embedded Platform Services in VCSA è associato a un **dominio Single Sign-on di vCenter**.  Il nome di dominio è **cloudsimple. local**.  Per accedere a **CloudOwner@cloudsimple.com** vCenter viene creato un utente predefinito.  È possibile aggiungere le origini di identità locali/Azure Active Directory [per vCenter](https://docs.azure.cloudsimple.com/set-vcenter-identity/).

## <a name="vsan-storage"></a>archiviazione rete VSAN

I cloud privati vengono creati con l'archiviazione rete VSAN completamente configurata, locale al cluster.  Per creare un cluster vSphere con l'archivio dati rete VSAN, sono necessari almeno tre nodi dello stesso SKU.  La deduplicazione e la compressione sono abilitate nell'archivio dati rete VSAN per impostazione predefinita.  In ogni nodo del cluster vSphere vengono creati due gruppi di dischi. Ogni gruppo di dischi contiene un disco della cache e tre dischi di capacità.

Nel cluster vSphere viene creato un criterio di archiviazione rete VSAN predefinito e applicato all'archivio dati rete VSAN.  Questi criteri determinano il modo in cui gli oggetti di archiviazione della macchina virtuale vengono sottoposti a provisioning e allocati nell'archivio dati per garantire il livello di servizio necessario.  I criteri di archiviazione definiscono gli **errori da tollerare (ITF)** e il **metodo di tolleranza di errore**.  È possibile creare nuovi criteri di archiviazione e applicarli alle macchine virtuali. Per mantenere il contratto di contratto, è necessario mantenere la capacità di riserva del 25% nell'archivio dati rete VSAN.  

### <a name="default-vsan-storage-policy"></a>Criteri di archiviazione rete VSAN predefiniti

La tabella seguente illustra i parametri predefiniti dei criteri di archiviazione rete VSAN.

| Numero di nodi nel cluster vSphere | ITF | Metodo di tolleranza errore |
|------------------------------------|-----|--------------------------|
| 3 e 4 nodi | 1 | RAID 1 (mirroring)-crea 2 copie |
| da 5 a 16 nodi | 2 | RAID 1 (mirroring)-crea 3 copie |

## <a name="nsx-data-center"></a>Data Center NSX

NSX Data Center offre funzionalità di virtualizzazione di rete, micro segmentazione e sicurezza di rete nel cloud privato.  È possibile configurare tutti i servizi supportati dal Data Center di NSX nel cloud privato tramite NSX.  Quando si crea un cloud privato, vengono installati e configurati i componenti NSX seguenti.

* Gestione NSXT
* Zone di trasporto
* Profilo uplink host e perimetrale
* Commuter logico per trasporto Edge, EXT1 e ext2
* Pool IP per il nodo trasporto ESXi
* Pool IP per il nodo Trasporto Edge
* Nodi perimetrali
* Regola anti-affinità DRS per le VM controller e Edge
* Router di livello 0
* Abilitare BGP nel router tier0

## <a name="vsphere-cluster"></a>cluster vSphere

Gli host ESXi sono configurati come cluster per garantire la disponibilità elevata del cloud privato.  Quando si crea un cloud privato, i componenti di gestione di vSphere vengono distribuiti nel primo cluster.  Viene creato un pool di risorse per i componenti di gestione e tutte le macchine virtuali di gestione vengono distribuite in questo pool di risorse. Non è possibile eliminare il primo cluster per compattare il cloud privato.  il cluster vSphere offre disponibilità elevata per le macchine virtuali che usano **vSphere ha**.  Gli errori da tollerare si basano sul numero di nodi disponibili nel cluster.  È possibile utilizzare la formula ```Number of nodes = 2N+1``` in ```N``` cui è il numero di errori da tollerare.

### <a name="vsphere-cluster-limits"></a>limiti del cluster vSphere

| Risorsa | Limite |
|----------|-------|
| Numero minimo di nodi per la creazione di un cloud privato (primo cluster vSphere) | 3 |
| Numero massimo di nodi in un cluster vSphere in un cloud privato | 16 |
| Numero massimo di nodi in un cloud privato | 64 |
| Numero massimo di cluster vSphere in un cloud privato | 21 |
| Numero minimo di nodi in un nuovo cluster vSphere | 3 |

## <a name="vmware-infrastructure-maintenance"></a>Manutenzione dell'infrastruttura VMware

Talvolta è necessario apportare modifiche alla configurazione dell'infrastruttura VMware. Attualmente, questi intervalli possono verificarsi ogni 1-2 mesi, ma la frequenza dovrebbe ridursi nel tempo. Questo tipo di manutenzione può essere in genere eseguito senza interrompere il consumo normale dei servizi CloudSimple. Durante un intervallo di manutenzione VMware, i servizi seguenti continuano a funzionare senza alcun effetto:

* Piano e applicazioni di gestione VMware
* accesso a vCenter
* Tutte le funzionalità di rete e di archiviazione
* Tutto il traffico di Azure

## <a name="updates-and-upgrades"></a>Aggiornamenti e aggiornamenti

CloudSimple è responsabile della gestione del ciclo di vita del software VMware (ESXi, vCenter, PSC e NSX) nel cloud privato.

Gli aggiornamenti software includono:

* **Patch**. Patch di sicurezza o correzioni di bug rilasciate da VMware.
* **Aggiornamenti**. Modifica della versione secondaria di un componente dello stack VMware.
* **Aggiornamenti**. Modifica della versione principale di un componente dello stack VMware.

CloudSimple testa una patch di sicurezza critica non appena diventa disponibile da VMware. Per SLA, CloudSimple esegue il rollup della patch di sicurezza negli ambienti di cloud privato entro una settimana.

CloudSimple fornisce aggiornamenti di manutenzione trimestrale per i componenti software VMware. Quando è disponibile una nuova versione principale del software VMware, CloudSimple collabora con i clienti per coordinare una finestra di manutenzione adatta per l'aggiornamento.  

## <a name="next-steps"></a>Passaggi successivi

* [Manutenzione e aggiornamenti di CloudSimple](cloudsimple-maintenance-updates.md)