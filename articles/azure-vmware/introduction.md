---
title: Introduzione
description: Informazioni sulle funzionalità e i vantaggi della soluzione Azure VMware per distribuire e gestire i carichi di lavoro basati su VMware in Azure.
ms.topic: overview
ms.date: 05/04/2020
ms.openlocfilehash: 1875f535a5ca3e346362409c143da82cb8c07a12
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94488588"
---
# <a name="what-is-azure-vmware-solution"></a>Che cos'è la soluzione Azure VMware?

La soluzione Azure VMware offre cloud privati in Azure. I cloud privati contengono cluster vSphere, creati da un'infrastruttura di Azure bare metal dedicata. È possibile dimensionare i cluster di cloud privati da 3 a 16 host, con la possibilità di avere più cluster in un singolo cloud privato. Per tutti i cloud privati viene effettuato il provisioning con server vCenter, rete vSAN, vSphere e NSX-T. È possibile eseguire la migrazione dei carichi di lavoro dagli ambienti locali, creare o distribuire nuove macchine virtuali e utilizzare i servizi di Azure dai cloud privati.

La soluzione Azure VMware è una soluzione approvata da VMware con convalide e test continuativi di miglioramenti e aggiornamenti. L'infrastruttura e il software del cloud privato vengono gestiti e mantenuti da Microsoft, per consentire di concentrarsi sullo sviluppo e sull'esecuzione di carichi di lavoro nei cloud privati.

Il diagramma seguente mostra l'adiacenza tra cloud privati e reti virtuali in Azure, servizi di Azure e ambienti locali. L'accesso di rete da cloud privati a servizi di Azure o reti virtuali offre l'integrazione basata su contratti di servizio degli endpoint servizio di Azure. L'accesso al cloud privato da ambienti locali usa la copertura globale ExpressRoute per una connessione privata e sicura.

![Immagine dell'adiacenza del cloud privato della soluzione Azure VMware ad Azure e all'ambiente locale](./media/adjacency-overview-drawing-final.png)

## <a name="hosts-clusters-and-private-clouds"></a>Host, cluster e cloud privati

I cloud privati e i cluster della soluzione Azure VMware vengono creati da un host di infrastruttura di Azure bare metal iperconvergente. Gli host di livello elevato sono dotati di 576 GB di RAM e di processori dual core Intel a 18 core a 2,3 GHz. Gli host di livello elevato sono dotati di due gruppi di dischi vSAN con un livello di capacità di rete vSAN non elaborato totale di 15,36 TB (unità SSD) e un livello di cache di rete vSAN di 3,2 TB (NVMe).

I nuovi cloud privati vengono distribuiti tramite il portale di Azure o l'interfaccia della riga di comando di Azure.

## <a name="networking"></a>Rete

[!INCLUDE [avs-networking-description](includes/azure-vmware-solution-networking-description.md)]

Per altre informazioni sulla rete e sull'interconnettività, vedere l'articolo relativo ai [Concetti sulla rete](concepts-networking.md).

## <a name="access-and-security"></a>Accesso e sicurezza

Per maggiore sicurezza, i cloud privati della soluzione Azure VMware usano il controllo degli accessi in base al ruolo di vSphere. Le funzionalità LDAP per l'accesso SSO di vSphere possono essere integrate con Azure Active Directory. Per altre informazioni sulle identità e sui privilegi, vedere l'articolo relativo ai [Concetti su accesso e identità](concepts-identity.md).

La crittografia dei dati inattivi della rete vSAN è abilitata per impostazione predefinita e viene usata per fornire la sicurezza dell'archivio dati della rete vSAN. Questo argomento viene descritto più dettagliatamente nell'articolo relativo ai [Concetti sull'archiviazione](concepts-storage.md).

## <a name="host-and-software-lifecycle-maintenance"></a>Manutenzione del ciclo di vita di software e host

Gli aggiornamenti regolari del cloud privato della soluzione Azure VMware e del software VMware garantiscono che nei cloud privati siano in esecuzione i set di funzionalità, di sicurezza e di stabilità più recenti. Altre informazioni sulla manutenzione e sugli aggiornamenti della piattaforma sono disponibili nell'articolo relativo ai [Concetti sull'aggiornamento](concepts-upgrades.md).

## <a name="monitoring-your-private-cloud"></a>Monitoraggio del cloud privato

Una volta distribuita la soluzione Azure VMware nella sottoscrizione, i [log di Monitoraggio di Azure](../azure-monitor/overview.md) vengono generati automaticamente. È inoltre possibile raccogliere i log in ognuna delle macchine virtuali nel cloud privato. È possibile [scaricare e installare l'agente MMA](../azure-monitor/platform/log-analytics-agent.md#installation-options) nelle macchine virtuali Linux e Windows in esecuzione nei cloud privati della soluzione Azure VMware, nonché abilitare l'[estensione Diagnostica di Azure](../azure-monitor/platform/diagnostics-extension-overview.md). È anche possibile eseguire le stesse query che si eseguono normalmente nelle macchine virtuali. Per altre informazioni sulla creazione di query, vedere [Panoramica dei log di Monitoraggio di Azure](../azure-monitor/platform/data-platform-logs.md). I modelli di monitoraggio nella soluzione Azure VMware sono simili alle macchine virtuali di Azure nella piattaforma IaaS. Per altre informazioni e procedure, vedere [Monitoraggio delle macchine virtuali di Azure con Monitoraggio di Azure](../azure-monitor/insights/monitor-vm-azure.md).

## <a name="next-steps"></a>Passaggi successivi

Il passaggio successivo prevede l'approfondimento dei [Concetti principali sul cloud privato e sui cluster](concepts-private-clouds-clusters.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-private-clouds-clusters]: ./concepts-private-clouds-clusters.md
