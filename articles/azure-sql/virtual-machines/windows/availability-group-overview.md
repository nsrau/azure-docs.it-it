---
title: Panoramica dei gruppi di disponibilità AlwaysOn di SQL Server
description: Questo articolo offre una panoramica sui gruppi di disponibilità AlwaysOn di SQL Server in macchine virtuali di Azure.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.topic: overview
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/07/2020
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: eb17b8286ce994146c1fa9867cd8131a909c8ace
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146689"
---
# <a name="always-on-availability-group-on-sql-server-on-azure-vms"></a>Gruppi di disponibilità AlwaysOn per SQL Server in Macchine virtuali di Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Questo articolo presenta i gruppi di disponibilità AlwaysOn per SQL Server in Macchine virtuali di Azure. 

## <a name="overview"></a>Panoramica

I gruppi di disponibilità AlwaysOn in Macchine virtuali di Azure sono simili ai [gruppi di disponibilità AlwaysOn locali](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). Tuttavia, poiché le macchine virtuali sono ospitate in Azure, è necessario considerare anche altri aspetti, come la ridondanza di VM e il traffico di routing nella rete di Azure. 

Il diagramma seguente illustra un gruppo di disponibilità per SQL Server in Macchine virtuali di Azure:

![Gruppo di disponibilità](./media/availability-group-overview/00-EndstateSampleNoELB.png)


## <a name="vm-redundancy"></a>Ridondanza di VM 

Per aumentare la ridondanza e la disponibilità elevata, le VM di SQL Server devono trovarsi nello stesso [set di disponibilità](../../../virtual-machines/windows/tutorial-availability-sets.md#availability-set-overview) o in diverse [zone di disponibilità](../../../availability-zones/az-overview.md).

Un set di disponibilità è un raggruppamento di risorse configurate in modo tale che non ne siano presenti due nella stessa zona di disponibilità. Ciò impedisce l'impatto su più risorse nel gruppo durante l'implementazione delle distribuzioni. 


## <a name="connectivity"></a>Connettività 

In una tradizionale distribuzione locale i client si connettono al listener del gruppo di disponibilità usando il nome di rete virtuale (VNN, Virtual Network Name), quindi il listener instrada il traffico alla replica di SQL Server appropriata nel gruppo di disponibilità. Tuttavia, esiste un requisito aggiuntivo per instradare il traffico nella rete di Azure. 

Con SQL Server in Macchine virtuali di Azure, configurare un [servizio di bilanciamento del carico](availability-group-vnn-azure-load-balancer-configure.md) per instradare il traffico al listener del gruppo di disponibilità oppure, se si usa SQL Server 2019 CU8 e versioni successive, è possibile configurare un [listener del nome di rete distribuita (DNN, Distributed Network Name)](availability-group-distributed-network-name-dnn-listener-configure.md) per sostituire il tradizionale listener di VNN del gruppo di disponibilità. 


### <a name="vnn-listener"></a>Listener di VNN 

Usare un'istanza di [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) per instradare il traffico dal client al tradizionale listener del nome di rete virtuale (VNN) del gruppo di disponibilità nella rete di Azure. 

Il servizio di bilanciamento del carico contiene gli indirizzi IP per il listener di VNN. Se sono presenti più gruppi di disponibilità, è necessario un listener di VNN per ognuno. Un servizio di bilanciamento del carico può supportare più listener.

Per iniziare, vedere come [configurare un servizio di bilanciamento del carico](availability-group-vnn-azure-load-balancer-configure.md). 

### <a name="dnn-listener"></a>Listener di DNN

SQL Server 2019 CU8 introduce il supporto per il listener del nome di rete distribuita (DNN). Il listener di DNN sostituisce il tradizionale listener del gruppo di disponibilità, evitando la necessità di un'istanza di Azure Load Balancer che instradi il traffico nella rete di Azure. 

Il listener di DNN è la soluzione di connettività HADR consigliata in Azure, perché semplifica la distribuzione, riduce la manutenzione e i costi e riduce il tempo di failover in caso di errore. 

Usare il listener di DNN per sostituire un listener di VNN esistente o in alternativa usarlo in combinazione con un listener di VNN esistente in modo che il gruppo di disponibilità abbia due punti di connessione distinti, uno che usa il nome del listener di VNN (e la porta se non predefinita) e l'altro che usa il nome e la porta del listener di DNN. Questo approccio può rivelarsi utile per evitare la latenza di failover del servizio di bilanciamento del carico, pur sfruttando le funzionalità di SQL Server che dipendono dal listener di VNN, ad esempio gruppi di disponibilità, Service Broker o FileStream. Per altre informazioni, vedere [Listener di DNN e interoperabilità con le funzionalità di SQL Server](availability-group-dnn-interoperability.md)

Per iniziare, vedere come [configurare un listener di DNN](availability-group-distributed-network-name-dnn-listener-configure.md).


## <a name="deployment"></a>Distribuzione 

Sono disponibili diverse opzioni per la distribuzione di un gruppo di disponibilità in SQL Server in Macchine virtuali di Azure, alcune con più automazione di altre. 

La tabella seguente include un confronto delle opzioni disponibili: 

| |**[Portale di Azure](availability-group-azure-portal-configure.md)**|**[Interfaccia della riga di comando di Azure/PowerShell](./availability-group-az-commandline-configure.md)**|**[Modelli di avvio rapido](availability-group-quickstart-template-configure.md)**|**[Manuale](availability-group-manually-configure-prerequisites-tutorial.md)** | 
|---------|---------|---------|--------- |---------|
|**Versione di SQL Server** |2016 e successive |2016 e successive|2016 e successive|2012 e successive|
|**Edizione di SQL Server** |Enterprise |Enterprise |Enterprise |Enterprise, Standard|
|**Versione di Windows Server**| 2016 e successive | 2016 e successive | 2016 e successive | Tutti| 
|**Crea automaticamente il cluster**|Sì|Sì | Sì |No|
|**Crea automaticamente il gruppo di disponibilità** |Sì |No|No|No|
|**Crea il listener e il servizio di bilanciamento del carico in modo indipendente** |No|No|No|Sì|
|**È possibile creare un listener di DNN con questo metodo?**|No|No|No|Sì|
|**Configurazione del quorum WSFC**|Cloud di controllo|Cloud di controllo|Cloud di controllo|Tutti|
|**Ripristino di emergenza con più aree** |No|No|No|Sì|
|**Supporto per più subnet** |Sì|Sì|Sì|Sì|
|**Supporto di un dominio dell'applicazione esistente**|Sì|Sì|Sì|Sì|
|**Ripristino di emergenza con più zone nella stessa area**|Sì|Sì|Sì|Sì|
|**Gruppo di disponibilità distribuito senza AD**|No|No|No|Sì|
|**Gruppo di disponibilità distribuito senza cluster** |No|No|No|Sì|
||||||



## <a name="considerations"></a>Considerazioni 

In un cluster di failover guest di macchine virtuali IaaS di Azure è consigliabile usare una sola scheda di rete per ogni server (nodo del cluster) e una sola subnet. La ridondanza fisica della rete di Azure rende superfluo l'uso di altre schede di rete e subnet in un cluster guest di macchine virtuali IaaS di Azure. Anche se il report di convalida del cluster avviserà che i nodi sono raggiungibili solo in una rete, tale avviso potrà essere tranquillamente ignorato per i cluster di failover guest delle macchine virtuali IaaS di Azure. 

## <a name="next-steps"></a>Passaggi successivi

Vedere le [procedure consigliate per disponibilità elevata e ripristino di emergenza](hadr-cluster-best-practices.md) e quindi iniziare a distribuire un gruppo di disponibilità con il [portale di Azure](availability-group-azure-portal-configure.md), l'[interfaccia della riga di comando di Azure/PowerShell](./availability-group-az-commandline-configure.md), i [modelli di avvio rapido](availability-group-quickstart-template-configure.md) oppure [manualmente](availability-group-manually-configure-prerequisites-tutorial.md).

In alternativa, è possibile distribuire un [gruppo di disponibilità senza cluster](availability-group-clusterless-workgroup-configure.md) o un gruppo di disponibilità [in più aree](availability-group-manually-configure-multiple-regions.md).