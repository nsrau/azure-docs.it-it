---
title: Azure VMware Solutions (AVS)-AVS private cloud VMware Components
description: Viene descritto come installare i componenti VMware in AVS private cloud
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3ea6c22c3957f72a0a416ce7ae42c62ff5a0791a
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77016783"
---
# <a name="avs-private-cloud-vmware-components"></a>Componenti di VMware del cloud privato AVS

Un cloud privato AVS è un ambiente VMware stack (host ESXi, vCenter, rete VSAN e NSX) isolato gestito da un server vCenter in un dominio di gestione. Il servizio AVS ti permette di distribuire VMware in modo nativo nell'infrastruttura bare metal di Azure in località di Azure. I cloud privati AVS sono integrati con il resto del cloud di Azure. Viene distribuito un cloud privato AVS con i componenti dello stack VMware seguenti:

* **VMware ESXi-** Hypervisor nei nodi dedicati di Azure
* **VMware vCenter-** Appliance per la gestione centralizzata di AVS private cloud vSphere Environment
* **Rete VSAN VMware-** Soluzione di infrastruttura iperconvergente
* **Data Center VMware NSX-** Software di sicurezza e virtualizzazione di rete  

## <a name="vmware-component-versions"></a>Versioni del componente VMware

Uno stack VMware del cloud privato AVS viene distribuito con la versione del software seguente.

| Componente | Versione | Versione con licenza |
|-----------|---------|------------------|
| ESXi | 6.7 U2 | Enterprise Plus |
| vCenter | 6.7 U2 | Standard vCenter |
| vSAN | 6.7 | Organizzazioni |
| Data Center NSX | 2.4.1 | Funzionalità avanzate |

## <a name="esxi"></a>ESXi

VMware ESXi viene installato nei nodi AVS con provisioning quando si crea un cloud privato AVS. ESXi fornisce l'hypervisor per la distribuzione di macchine virtuali (VM) del carico di lavoro. I nodi forniscono un'infrastruttura iperconvergente (calcolo e archiviazione) nel cloud privato AVS. I nodi fanno parte del cluster vSphere nel cloud privato AVS. Ogni nodo dispone di quattro interfacce di rete fisiche connesse alla rete di un sottosistema. Due interfacce di rete fisiche vengono usate per creare un **Switch vSphere distribuito (VDS)** in vCenter e due vengono usate per creare un compartitore **virtuale gestito da NSX (N-VDS)** . Le interfacce di rete vengono configurate in modalità attivo-attivo per la disponibilità elevata.

Scopri di più su VMware ESXi

## <a name="vcenter-server-appliance"></a>Appliance server vCenter

vCenter Server Appliance (VCSA) fornisce le funzioni di autenticazione, gestione e orchestrazione per le soluzioni VMware (AVS). VCSA con Embedded Platform Services controller (PSC) viene distribuito quando si crea il cloud privato AVS. VCSA viene distribuito nel cluster vSphere creato quando si distribuisce il cloud privato AVS. Ogni cloud privato AVS ha il proprio VCSA. L'espansione di un cloud privato AVS aggiunge i nodi a VCSA nel cloud privato AVS.

### <a name="vcenter-single-sign-on"></a>Single Sign-On vCenter

Il controller Embedded Platform Services in VCSA è associato a un **dominio Single Sign-on di vCenter**. Il nome di dominio è **AVS. local**. Per accedere a vCenter viene creato un **CloudOwner@AVS.com** utente predefinito. È possibile aggiungere le origini di identità locali/Azure Active Directory [per vCenter](set-vcenter-identity.md).

## <a name="vsan-storage"></a>archiviazione rete VSAN

I cloud privati AVS vengono creati con l'archiviazione rete VSAN completamente configurata, locale al cluster. Per creare un cluster vSphere con l'archivio dati rete VSAN, sono necessari almeno tre nodi dello stesso SKU. La deduplicazione e la compressione sono abilitate nell'archivio dati rete VSAN per impostazione predefinita. In ogni nodo del cluster vSphere vengono creati due gruppi di dischi. Ogni gruppo di dischi contiene un disco della cache e tre dischi di capacità.

Nel cluster vSphere viene creato un criterio di archiviazione rete VSAN predefinito e applicato all'archivio dati rete VSAN. Questi criteri determinano il modo in cui gli oggetti di archiviazione della macchina virtuale vengono sottoposti a provisioning e allocati nell'archivio dati per garantire il livello di servizio necessario. I criteri di archiviazione definiscono gli **errori da tollerare (ITF)** e il **metodo di tolleranza di errore**. È possibile creare nuovi criteri di archiviazione e applicarli alle macchine virtuali. Per mantenere il contratto di contratto, è necessario mantenere la capacità di riserva del 25% nell'archivio dati rete VSAN. 

### <a name="default-vsan-storage-policy"></a>Criteri di archiviazione rete VSAN predefiniti

La tabella seguente illustra i parametri predefiniti dei criteri di archiviazione rete VSAN.

| Numero di nodi nel cluster vSphere | ITF | Metodo di tolleranza errore |
|------------------------------------|-----|--------------------------|
| 3 e 4 nodi | 1 | RAID 1 (mirroring)-crea 2 copie |
| da 5 a 16 nodi | 2 | RAID 1 (mirroring)-crea 3 copie |

## <a name="nsx-data-center"></a>Data Center NSX

NSX Data Center offre funzionalità di virtualizzazione di rete, micro segmentazione e sicurezza di rete nel cloud privato AVS. È possibile configurare tutti i servizi supportati da NSX Data Center nel cloud privato AVS tramite NSX. Quando si crea un cloud privato AVS, vengono installati e configurati i componenti NSX seguenti.

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

Gli host ESXi sono configurati come cluster per garantire la disponibilità elevata del cloud privato AVS. Quando si crea un cloud privato AVS, i componenti di gestione di vSphere vengono distribuiti nel primo cluster. Viene creato un pool di risorse per i componenti di gestione e tutte le macchine virtuali di gestione vengono distribuite in questo pool di risorse. Non è possibile eliminare il primo cluster per compattare il cloud privato AVS. il cluster vSphere offre disponibilità elevata per le macchine virtuali che usano **vSphere ha**. Gli errori da tollerare si basano sul numero di nodi disponibili nel cluster. È possibile utilizzare la formula ```Number of nodes = 2N+1``` dove ```N``` è il numero di errori da tollerare.

### <a name="vsphere-cluster-limits"></a>limiti del cluster vSphere

| Gruppi | Limite |
|----------|-------|
| Numero minimo di nodi per la creazione di un cloud privato AVS (primo cluster vSphere) | 3 |
| Numero massimo di nodi in un cluster vSphere in un cloud privato AVS | 16 |
| Numero massimo di nodi in un cloud privato AVS | 64 |
| Numero massimo di cluster vSphere in un cloud privato AVS | 21 |
| Numero minimo di nodi in un nuovo cluster vSphere | 3 |

## <a name="vmware-infrastructure-maintenance"></a>Manutenzione dell'infrastruttura VMware

Talvolta è necessario apportare modifiche alla configurazione dell'infrastruttura VMware. Attualmente, questi intervalli possono verificarsi ogni 1-2 mesi, ma la frequenza dovrebbe ridursi nel tempo. Questo tipo di manutenzione può essere in genere eseguito senza interrompere il consumo normale dei servizi AVS. Durante un intervallo di manutenzione VMware, i servizi seguenti continuano a funzionare senza alcun effetto:

* Piano e applicazioni di gestione VMware
* accesso a vCenter
* Tutte le funzionalità di rete e di archiviazione
* Tutto il traffico di Azure

## <a name="updates-and-upgrades"></a>Aggiornamenti e aggiornamenti

AVS è responsabile della gestione del ciclo di vita del software VMware (ESXi, vCenter, PSC e NSX) nel cloud privato AVS.

Gli aggiornamenti software includono:

* **Patch**. Patch di sicurezza o correzioni di bug rilasciate da VMware.
* **Aggiornamenti**. Modifica della versione secondaria di un componente dello stack VMware.
* **Aggiornamenti**. Modifica della versione principale di un componente dello stack VMware.

AVS testa una patch di sicurezza critica non appena diventa disponibile da VMware. Per SLA, AVS esegue il rollup della patch di sicurezza per gli ambienti di cloud privato AVS entro una settimana.

AVS fornisce aggiornamenti di manutenzione trimestrale per i componenti software VMware. Quando è disponibile una nuova versione principale del software VMware, AVS collabora con i clienti per coordinare una finestra di manutenzione adatta per l'aggiornamento. 

## <a name="next-steps"></a>Passaggi successivi

* [Manutenzione e aggiornamenti AVS](cloudsimple-maintenance-updates.md)
