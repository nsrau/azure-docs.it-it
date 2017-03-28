---
title: Informazioni sul servizio database SQL di Azure | Documentazione Microsoft
description: 'Get an introduction to SQL Database: technical details and capabilities of Microsoft''s relational database management system (RDBMS) in the cloud.'
keywords: introduzione a sql,intro a sql,informazioni sul database sql
services: sql-database
documentationcenter: 
author: shontnew
manager: jhubbard
editor: cgronlun
ms.assetid: c561f600-a292-4e3b-b1d4-8ab89b81db48
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 03/17/2017
ms.author: shkurhek
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: c505844cc2b7c745a1106b3c446833fb206ca98a
ms.lasthandoff: 03/18/2017

---
# <a name="what-is-sql-database-introduction-to-sql-database"></a>Informazioni sul database SQL Introduzione al Database SQL
Il database SQL è un servizio di database relazionale sul cloud Microsoft basato sul motore Microsoft SQL Server, leader di mercato, e in grado di gestire carichi di lavoro di importanza strategica. Il database SQL offre prestazioni prevedibili a più livelli di servizio, scalabilità dinamica senza tempi di inattività, continuità aziendale predefinita e protezione dei dati, con esigenze di amministrazione quasi nulle. Queste funzionalità consentono di concentrarsi sullo sviluppo rapido di app e accelerare i tempi di mercato, piuttosto che allocare tempo e risorse preziose alla gestione delle macchine virtuali e dell'infrastruttura. Poiché il database SQL è basato sul motore di [SQL Server](https://msdn.microsoft.com/library/bb545450.aspx), il database SQL supporta gli strumenti, le librerie e le API di SQL Server esistenti. Ciò consente quindi di sviluppare con facilità nuove soluzioni, spostare le soluzioni SQL Server esistenti ed estendere le soluzioni SQL Server esistenti sul cloud Microsoft senza che siano necessarie nuove competenze.

Questo articolo è un'introduzione dei concetti fondamentali sul database SQL e delle funzionalità correlate a prestazioni, scalabilità e gestibilità, con collegamenti per esplorare i dettagli. Per iniziare, vedere queste guide introduttive:
 - [Creare un database SQL nel portale di Azure](sql-database-get-started-portal.md)  
 - [Creare un database SQL con l'interfaccia della riga di comando di Azure](sql-database-get-started-cli.md)
 - [Creare un database SQL usando PowerShell](sql-database-get-started-powershell.md)

Per un set di esempi dell'interfaccia della riga di comando di Azure e di PowerShell, vedere:
 - [Esempi dell'interfaccia della riga di comando di Azure per un database SQL di Azure](sql-database-cli-samples.md)
 - [Esempi di Azure PowerShell per database SQL di Azure](sql-database-powershell-samples.md)

## <a name="adjust-performance-and-scale-without-downtime"></a>Regolare le prestazioni e scalabilità senza tempi di inattività
Il servizio database SQL offre tre livelli di servizio: Basic, Standard e Premium. Ogni livello di servizio offre [diversi livelli di prestazioni e capacità](sql-database-service-tiers.md) per supportare i carichi di lavoro del database, da quelli leggeri a quelli pesanti. È possibile creare la prima app in un database di piccole dimensioni per un costo mensile conveniente e quindi [modificare il livello di servizio](sql-database-service-tiers.md) manualmente o a livello di codice in qualsiasi momento per soddisfare le esigenze della soluzione. È possibile eseguire questa operazione senza tempi di inattività per l'app o per i clienti. La scalabilità dinamica consente al database di rispettare i requisiti in continua evoluzione relativi alle risorse e di pagare solo le risorse necessarie quando necessario.

## <a name="elastic-pools-to-maximize-resource-utilization"></a>I pool elastici ottimizzano l'utilizzo delle risorse
Per molte aziende e app, la possibilità di creare singoli database e connettere o disconnettere prestazioni su richiesta è sufficiente, specialmente se i modelli d'uso sono relativamente prevedibili. Ma se si dispone di modelli di utilizzo imprevedibili, può risultare difficile gestire i costi e il modello aziendale. I [pool elastici](sql-database-elastic-pool.md) sono stati progettati per risolvere questo problema. Il concetto è semplice. È possibile allocare le risorse relative alle prestazioni a un pool invece che a un database singolo e quindi pagare le risorse relative alle prestazioni collettive del pool invece di pagare le prestazioni del database singolo. Con i pool elastici non è necessario concentrarsi sul ridimensionamento delle prestazioni del database in base alla fluttuazione della richiesta delle risorse. I database nel pool utilizzano le risorse relative alle prestazioni del pool elastico in base alla necessità. I database nel pool utilizzano ma non superano i limiti del pool, quindi il costo rimane prevedibile, indipendentemente dall'utilizzo dei singoli database. Inoltre, è possibile [aggiungere e rimuovere i database al pool](sql-database-elastic-pool-manage-portal.md), con la scalabilità dell’app da un numero limitato di database a migliaia, tutto all'interno di un budget che è possibile controllare. È infine possibile controllare le risorse minime e massime disponibili per i database del pool per verificare che nessun database del pool usi tutte le risorse del pool e che a ogni database nel pool sia garantita una quantità minima di risorse. Per altre informazioni sui modelli di progettazione per applicazioni SaaS con pool elastici, vedere l'articolo relativo ai [modelli di progettazione per applicazioni SaaS multi-tenant con database SQL di Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).

## <a name="blend-single-databases-with-pooled-databases"></a>Unire database singoli e database nel pool
In entrambi i casi, ovvero database singoli o database nel pool, sono disponibili molte opzioni. È possibile usare database singoli e pool elastici e modificare i livelli di servizio dei database singoli e dei pool elastici in modo semplice e rapido per adattarli alle proprie esigenze. Inoltre, con la potenza e la portata di Azure, è possibile combinare e integrare altri servizi di Azure con il database SQL per soddisfare le esigenze esclusive di progettazione delle app, promuovere l'efficienza in termini di costi e di risorse e sfruttare nuove opportunità commerciali.

## <a name="monitoring-and-alerting"></a>Monitoraggio e avviso
In che modo è possibile confrontare le prestazioni relative dei database singoli e dei pool elastici? Come si conosce il giusto arresto quando si connette e si disconnette? È possibile usare il [monitoraggio predefinito delle prestazioni](sql-database-performance.md) e gli strumenti relativi agli [avvisi](sql-database-insights-alerts-portal.md), insieme alle valutazioni delle prestazioni in base a [Unità di transazione di database (DTU) per database singoli e unità di transazione di database elastico (eDTU) per pool elastici](sql-database-what-is-a-dtu.md). Usando questi strumenti, è possibile valutare rapidamente l'impatto dell'aumento o della riduzione delle prestazioni in base alle esigenze correnti o previste relative alle prestazioni. Per altre informazioni, vedere [Opzioni e prestazioni disponibili in ogni livello di servizio del database SQL](sql-database-service-tiers.md) .

## <a name="keep-your-app-and-business-running"></a>Mantenere l'applicazione e l’esecuzione dell’azienda
Il settore di Azure che ha una accordo sul livello di disponibilità del servizio del 99,99% [(SLA)](http://azure.microsoft.com/support/legal/sla/), fornito da una rete globale di datacenter gestiti da Microsoft, consente di mantenere l'applicazione in esecuzione 24 ore su 24, 7 giorni su 7. Con ogni database SQL, si sfruttano i vantaggi della sicurezza, della tolleranza di errore e della [protezione dei dati](sql-database-automated-backups.md) predefinite che sarebbe altrimenti necessario acquistare o progettare, creare e gestire. Con il database SQL, ogni livello di servizio offre un set completo di funzionalità di continuità aziendale e opzioni che è possibile usare per diventare e rimanere operativi. È possibile usare un [ripristino temporizzato](sql-database-recovery-using-backups.md) per riportare un database a uno stato precedente, risalente anche a 35 giorni fa. È possibile configurare la [conservazione del backup a lungo termine](sql-database-long-term-retention.md) per archiviare i backup in un archivio sicuro per un massimo di 10 anni. Se nel data center che ospita i database si verifica un'interruzione, è inoltre possibile ripristinare i database dalle [copie con ridondanza geografica di backup recenti](sql-database-recovery-using-backups.md). Se necessario, è anche possibile configurare [repliche leggibili con ridondanza geografica](sql-database-geo-replication-overview.md) in una o più aree per il failover rapido in caso di interruzione del data center. È anche possibile usare le repliche per prestazioni di lettura più veloci in diverse aree geografiche o per [aggiornamenti dell'applicazione senza tempi di inattività](sql-database-manage-application-rolling-upgrade.md). 

![Replica geografica del database SQL](./media/sql-database-technical-overview/azure_sqldb_map.png)

Vedere [Continuità aziendale](sql-database-business-continuity.md) per informazioni dettagliate sulle diverse funzionalità per la continuità aziendale disponibili per i diversi livelli di servizio.

## <a name="secure-your-data"></a>Protezione dei dati
Il server SQL vanta una tradizione di sicurezza dei dati che rispetta i Database SQL con le funzionalità che consentono di monitorare l'attività, proteggere i dati e limitare l'accesso. Vedere [Protezione del database SQL](sql-database-security-overview.md) per una rapida panoramica delle opzioni di sicurezza disponibili nel Database SQL. Vedere il [Centro di sicurezza per il motore di database di SQL Server e il database SQL di Azure](https://msdn.microsoft.com/library/bb510589) per una visione più completa delle funzionalità di protezione. Visitare il [Centro protezione di Azure](https://azure.microsoft.com/support/trust-center/security/) per informazioni sulla sicurezza della piattaforma Azure.

## <a name="next-steps"></a>Passaggi successivi
Dopo avere letto l'introduzione al database SQL e avere ottenuto informazioni sul database SQL, è possibile:

* Vedere la [pagina relativa ai prezzi](https://azure.microsoft.com/pricing/details/sql-database/) per informazioni sui prezzi e per confrontare e calcolare il prezzo per database singoli e per pool elastici.
* Informazioni sui [pool elastici](sql-database-elastic-pool.md).
* Per iniziare, [creare il primo database](sql-database-get-started.md).
* Creare la prima app in C#, Java, Node.js, PHP, Python o Ruby: [Raccolte di connessioni per database SQL e SQL Server](sql-database-libraries.md)

