---
title: Panoramica di SAP HANA in Azure (istanze di grandi dimensioni) | Microsoft Docs
description: Panoramica su come distribuire SAP HANA in Azure (istanze di grandi dimensioni).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c177dbad1145dee6eda3202d8076997cc7673dfc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60477801"
---
#  <a name="what-is-sap-hana-on-azure-large-instances"></a>SAP HANA in Azure (istanze Large)

SAP HANA in Azure (istanze Large) è una soluzione esclusiva per Azure. Oltre a rendere disponibili macchine virtuali (VM) per distribuire ed eseguire SAP HANA, Azure offre la possibilità di eseguire e distribuire SAP HANA su server bare metal dedicati per i clienti. La soluzione SAP HANA in Azure (istanze Large) si basa su hardware bare metal host/server non condiviso che viene assegnato ai clienti. L'hardware del server è incorporato in stamp più grandi contenenti l'infrastruttura di calcolo/server, di rete e di archiviazione. L'intera infrastruttura è dotata di certificazione HANA TDI (HANA Tailored Data Center Integration). SAP HANA in Azure (istanze Large) offre diversi SKU o dimensioni per i server, Le unità possono avere 36 core CPU Intel e 768 GB di memoria e arrivare fino a unità con un massimo di 480 core CPU Intel e 24 TB di memoria.

L'isolamento dei clienti nel modulo dell'infrastruttura viene realizzato in base a tenant, nel modo seguente:

- **Rete**: Isolamento dei clienti nello stack dell'infrastruttura tramite reti virtuali per ogni cliente assegnato tenant. Un tenant viene assegnato a un singolo cliente. Un cliente può avere più tenant. L'isolamento dei tenant basato su rete impedisce le comunicazioni di rete tra i tenant a livello del modulo dell'infrastruttura, anche se i tenant appartengono allo stesso cliente.
- **Componenti di archiviazione**: Isolamento tramite macchine virtuali di archiviazione che sono assegnati volumi di archiviazione. I volumi di archiviazione possono essere assegnati a una sola macchina virtuale di archiviazione. Una macchina virtuale di archiviazione viene assegnata esclusivamente a un solo tenant nello stack dell'infrastruttura con certificazione SAP HANA TDI. I volumi di archiviazione assegnati a una macchina virtuale di archiviazione sono pertanto accessibili solo in uno specifico tenant correlato e non sono visibili tra i diversi tenant distribuiti.
- **Server o host**: Un server o un'unità host non viene condivisa tra clienti o tenant. Un server o un host distribuito a un cliente è un'unità di calcolo bare metal atomica assegnata a un solo tenant. *Non* vengono usati partizionamenti hardware o software con i quali un cliente potrebbe trovarsi a condividere un host o un server con un altro cliente. I volumi di archiviazione assegnati alla macchina virtuale di archiviazione del tenant specifico vengono montati in tale server. A un tenant può essere assegnata in modo esclusivo una o più unità server di SKU diversi.
- In un modulo dell'infrastruttura SAP HANA in Azure (istanze Large) più tenant diversi vengono distribuiti e isolati l'uno dall'altro in base a concetti definiti a livello di rete, archiviazione e calcolo. 


Queste unità server bare metal sono supportate solo per l'esecuzione di SAP HANA. Il livello applicazione SAP o il livello middleware dei carichi di lavoro viene eseguito nelle macchine virtuali. I moduli dell'infrastruttura che eseguono le unità SAP HANA in Azure (istanze Large) sono connessi ai backbone dei servizi di rete di Azure. In questo modo, viene fornita connettività a bassa latenza tra le unità SAP HANA in Azure (istanze Large) e le macchine virtuali.

Questo è uno dei documenti dedicati a SAP HANA in Azure (istanze Large) che presenta l'architettura di base, le responsabilità, i servizi offerti e, a livello generale, le funzionalità della soluzione. Per la maggior parte delle altre aree, ad esempio rete e connettività, sono disponibili altri quattro documenti di approfondimento. La documentazione di SAP HANA in Azure (istanze Large) non tratta gli aspetti relativi all'installazione di SAP NetWeaver o alle distribuzioni di SAP NetWeaver nelle VM. SAP NetWeaver in Azure viene trattato in una documentazione separata disponibile nello stesso contenitore di documenti Azure. 


I vari documenti della guida per HANA (istanza di grandi dimensioni) trattano le aree seguenti:

- [Panoramica e architettura di SAP HANA in Azure (istanze Large)](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Infrastruttura e connettività a SAP HANA (istanze Large) in Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Installare e configurare SAP HANA (istanze Large) in Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Disponibilità elevata e ripristino di emergenza di SAP HANA (istanze Large) in Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Risoluzione dei problemi e monitoraggio di SAP HANA in Azure (istanze Large)](troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Configurazione della disponibilità elevata in SUSE con STONITH](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/ha-setup-with-stonith)
- [Backup e ripristino del sistema operativo per SKU di tipo II](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/os-backup-type-ii-skus)

**Passaggi successivi**
- Consultare [Conoscere i termini](hana-know-terms.md)