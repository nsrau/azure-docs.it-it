---
title: Indice delle indicazioni tecniche sulla resilienza | Microsoft Docs
description: "Indice degli articoli tecnici per comprendere e progettare applicazioni resilienti a disponibilità elevata e tolleranza di errore e per pianificare il ripristino di emergenza e la continuità aziendale"
services: 
documentationcenter: na
author: adamglick
manager: saladki
editor: 
ms.assetid: 0eafb464-810a-4539-905e-8c91e5f3c09e
ms.service: resiliency
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: aglick
translationtype: Human Translation
ms.sourcegitcommit: 0c23ee550d8ac88994e8c7c54a33d348ffc24372
ms.openlocfilehash: 7618f94574fd02d29a55ccbe8723a0228ad64f7f


---
# <a name="azure-resiliency-technical-guidance"></a>Indicazioni tecniche sulla resilienza di Azure
## <a name="introduction"></a>Introduzione
Per soddisfare i requisiti di disponibilità elevata e ripristino di emergenza è necessario:

* Avere una conoscenza tecnica approfondita delle funzionalità della piattaforma cloud
* Saper creare correttamente l'architettura di un servizio distribuito

Questa serie di articoli illustra il primo punto, ovvero le funzionalità e le limitazioni della piattaforma Azure rispetto alla resilienza, a volte definita continuità aziendale. Se si è interessati al secondo punto, vedere la serie di articoli incentrata su [ripristino di emergenza e disponibilità elevata per le applicazioni Azure](https://aka.ms/drtechguide). Anche se questa serie di articoli accenna ai modelli di progettazione e architettura, non è l'obiettivo principale della serie. È possibile consultare il materiale nella sezione [Risorse aggiuntive](#additional-resources) per indicazioni sulla progettazione.

Le informazioni sono organizzate negli articoli seguenti:

* [Ripristino da errori locali](resiliency-technical-guidance-recovery-local-failures.md).
  I componenti hardware, ad esempio unità, server e dispositivi di rete, possono essere soggetti a errori. Durante i picchi di carico è possibile che le risorse si esauriscano. Questo articolo descrive le funzionalità offerte da Azure per gestire la disponibilità elevata in queste condizioni.
* [Ripristino dopo un'interruzione di servizio di un'area di Azure](resiliency-technical-guidance-recovery-loss-azure-region.md).
  Gli errori generalizzati sono rari, ma teoricamente possibili. Intere aree possono risultare isolate a causa di errori di rete oppure fisicamente danneggiate a causa di calamità naturali. Questo articolo illustra come usare le funzionalità di Azure per creare applicazioni da usare in aree geograficamente diverse.
* [Ripristino dall'ambiente locale ad Azure](resiliency-technical-guidance-recovery-on-premises-azure.md).
  Il cloud modifica in modo significativo l'economia del ripristino di emergenza, consentendo alle organizzazioni di usare Azure per creare un secondo sito per il ripristino. Questa operazione può essere eseguita a un costo decisamente ridotto rispetto a quello di compilazione e gestione di un data center secondario. Questo articolo descrive le funzionalità offerte da Azure per estendere un data center locale al cloud.
* [Ripristino dal danneggiamento o dall'eliminazione accidentale dei dati](resiliency-technical-guidance-recovery-data-corruption.md).
  Le applicazioni possono contenere bug che danneggiano i dati. Gli operatori possono erroneamente eliminare dati importanti. Questo articolo illustra le funzionalità offerte da Azure per il backup dei dati e il ripristino a un punto precedente nel tempo.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Ripristino di emergenza e disponibilità elevata per le applicazioni basate su Microsoft Azure](resiliency-disaster-recovery-high-availability-azure-applications.md).
  Questo articolo fa una panoramica dettagliata delle disponibilità e del ripristino di emergenza. Illustra la replica manuale per i dati di riferimento e transazionali. Le sezioni finali forniscono un riepilogo dei diversi tipi di topologie di ripristino di emergenza estese alle aree di Azure per il massimo livello di disponibilità.
* [Elenco di controllo per la disponibilità elevata](resiliency-high-availability-checklist.md).
  Questo articolo elenca una serie di funzionalità, servizi e progettazioni da usare per aumentare la resilienza e la disponibilità dell'applicazione.
* [Materiale sussidiario sulla resilienza dei servizi di Microsoft Azure](resiliency-service-guidance-index.md).
  Questo articolo è un indice di servizi di Azure e offre collegamenti per il materiale sussidiario sul ripristino di emergenza e la progettazione.
* [Panoramica: Continuità aziendale del cloud e ripristino di emergenza del database con database SQL](../sql-database/sql-database-business-continuity.md).
  Questo articolo illustra le tecniche del database SQL di Azure per la disponibilità. Tratta principalmente le strategie di backup e ripristino. Se si usa il database SQL Azure nel servizio cloud, è consigliabile esaminare questo documento e le relative risorse correlate.
* [Disponibilità elevata e ripristino di emergenza per SQL Server nelle macchine virtuali di Azure](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md).
  Questo articolo descrive le opzioni di disponibilità presenti quando si usa l'infrastruttura distribuita come servizio (IaaS) per ospitare i servizi di database. Illustra Gruppi di disponibilità AlwaysOn, mirroring del database, log shipping e backup e ripristino. Numerose esercitazioni illustrano come usare queste tecniche.
* [Procedure consigliate per la progettazione di servizi su larga scala nei servizi cloud di Azure](https://azure.microsoft.com//blog/best-practices-for-designing-large-scale-services-on-windows-azure/).
  Questo articolo è incentrato sullo sviluppo di architetture cloud altamente scalabili. Molte delle tecniche che si usano per migliorare la scalabilità migliorano anche la disponibilità. Se l'applicazione non è scalabile in caso di aumento del carico, la scalabilità diventa un problema di disponibilità.
* [Backup e ripristino per SQL Server in Macchine virtuali di Azure](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md).
  Questo articolo offre materiale sussidiario su come eseguire backup e ripristino di Microsoft SQL Server in Macchine virtuali di Azure.
* [Failsafe: Guidance for resilient cloud architectures](https://channel9.msdn.com/Series/FailSafe)(FailSafe: Materiale sussidiario per architetture cloud resilienti).
  Questo articolo fornisce indicazioni per la creazione di architetture cloud resilienti, linee guida per implementare queste architetture nelle tecnologie Microsoft e soluzioni per l'implementazione di queste architetture per scenari specifici.
* [Case study tecnico: uso delle tecnologie cloud per migliorare il ripristino di emergenza](https://www.microsoft.com/itshowcase/Article/Content/737/Using-cloud-technologies-to-improve-disaster-recovery).
  Questo case study illustra come Microsoft IT usa Azure per migliorare il ripristino di emergenza.

## <a name="next-steps"></a>Passaggi successivi
Questo articolo fa parte di una serie relativa al materiale sussidiario sulla resilienza di Azure. Se si è interessati ad altri articoli di questa serie, iniziare con [Ripristino da errori locali](resiliency-technical-guidance-recovery-local-failures.md).




<!--HONumber=Jan17_HO2-->


