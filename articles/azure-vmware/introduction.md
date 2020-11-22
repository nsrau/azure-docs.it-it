---
title: Introduzione
description: Informazioni sulle funzionalità e i vantaggi della soluzione Azure VMware per distribuire e gestire i carichi di lavoro basati su VMware in Azure.
ms.topic: overview
ms.date: 11/11/2020
ms.openlocfilehash: 57edfc5786dfc95070b66eb9c8e2e038bafdcd35
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94534654"
---
# <a name="what-is-azure-vmware-solution"></a>Che cos'è la soluzione Azure VMware?

La soluzione Azure VMware offre cloud privati che contengono cluster vSphere, creati da un'infrastruttura di Azure bare metal dedicata. La distribuzione minima iniziale è di tre host, ma è possibile aggiungere altri host uno alla volta, fino a un massimo di 16 host per cluster.  Tutti i cloud privati di cui è stato effettuato il provisioning includono vCenter Server, vSAN, vSphere e NSX-T. È possibile eseguire la migrazione dei carichi di lavoro dagli ambienti locali, distribuire nuove macchine virtuali e utilizzare i servizi di Azure dai cloud privati.

La soluzione Azure VMware è una soluzione approvata da VMware con convalide e test continuativi di miglioramenti e aggiornamenti. La gestione e la manutenzione del software e dell'infrastruttura dei cloud privati è responsabilità di Microsoft. In questo modo è possibile concentrarsi sullo sviluppo e sull'esecuzione di carichi di lavoro nei cloud privati. 

Il diagramma mostra l'adiacenza tra cloud privati e reti virtuali in Azure, servizi di Azure e ambienti locali. L'accesso di rete da cloud privati a servizi di Azure o reti virtuali offre l'integrazione basata su contratti di servizio degli endpoint servizio di Azure. Copertura globale ExpressRoute connette l'ambiente locale al cloud privato della soluzione Azure VMware. 

![Immagine dell'adiacenza del cloud privato della soluzione Azure VMware ad Azure e all'ambiente locale](./media/adjacency-overview-drawing-final.png)

## <a name="hosts-clusters-and-private-clouds"></a>Host, cluster e cloud privati

I cloud privati e i cluster della soluzione Azure VMware vengono creati da un host di infrastruttura di Azure bare metal iperconvergente. Gli host di livello elevato sono dotati di 576 GB di RAM e di processori dual core Intel a 18 core a 2,3 GHz. Gli host di livello elevato sono dotati di due gruppi di dischi vSAN con un livello di capacità di rete vSAN non elaborato di 15,36 TB (unità SSD) e un livello di cache di rete vSAN di 3,2 TB (NVMe).

I nuovi cloud privati vengono distribuiti tramite il portale di Azure o l'interfaccia della riga di comando di Azure.

## <a name="networking"></a>Rete

[!INCLUDE [avs-networking-description](includes/azure-vmware-solution-networking-description.md)]

Per altre informazioni, vedere i [concetti relativi alla rete](concepts-networking.md).

## <a name="access-and-security"></a>Accesso e sicurezza

Per maggiore sicurezza, i cloud privati della soluzione Azure VMware usano il controllo degli accessi in base al ruolo di vSphere. È possibile integrare le funzionalità LDAP per l'accesso SSO di vSphere con Azure Active Directory. Per altre informazioni, vedere i [concetti relativi ad accesso e identità](concepts-identity.md).  

Per impostazione predefinita, la crittografia dei dati inattivi della rete vSAN è abilitata e viene usata per fornire la sicurezza dell'archivio dati della rete vSAN. Per altre informazioni, vedere i [concetti relativi all'archiviazione](concepts-storage.md).

## <a name="host-and-software-lifecycle-maintenance"></a>Manutenzione del ciclo di vita di software e host

Gli aggiornamenti regolari del cloud privato della soluzione Azure VMware e del software VMware garantiscono che nei cloud privati siano in esecuzione i set di funzionalità, di sicurezza e di stabilità più recenti. Per altre informazioni, vedere [Aggiornamenti dei cloud privati](concepts-upgrades.md).

## <a name="monitoring-your-private-cloud"></a>Monitoraggio del cloud privato

Una volta distribuita la soluzione Azure VMware nella sottoscrizione, i [log di Monitoraggio di Azure](../azure-monitor/overview.md) vengono generati automaticamente. 

Nel cloud privato è possibile:
- Raccogliere i log presenti in ogni macchina virtuale.
- [Scaricare e installare l'agente MMA](../azure-monitor/platform/log-analytics-agent.md#installation-options) in macchine virtuali Linux e Windows.
- Abilitare l'[estensione Diagnostica di Azure](../azure-monitor/platform/diagnostics-extension-overview.md).
- [Creare ed eseguire nuove query](../azure-monitor/platform/data-platform-logs.md#log-queries).
- Eseguire le stesse query eseguite in genere nelle macchine virtuali.

I modelli di monitoraggio nella soluzione Azure VMware sono simili alle macchine virtuali di Azure nella piattaforma IaaS. Per altre informazioni e per le procedure, vedere [Monitoraggio delle macchine virtuali di Azure con Monitoraggio di Azure](../azure-monitor/insights/monitor-vm-azure.md).

## <a name="next-steps"></a>Passaggi successivi

Il passaggio successivo prevede l'approfondimento dei [Concetti principali sul cloud privato e sui cluster](concepts-private-clouds-clusters.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-private-clouds-clusters]: ./concepts-private-clouds-clusters.md
