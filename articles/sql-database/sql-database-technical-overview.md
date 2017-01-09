---
title: Informazioni sul database SQL Introduzione al database SQL | Documentazione Microsoft
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
ms.date: 11/08/2016
ms.author: shkurhek
translationtype: Human Translation
ms.sourcegitcommit: 145cdc5b686692b44d2c3593a128689a56812610
ms.openlocfilehash: 37534ff6366bd519cec50bc033b2bcd8f8bda5c7


---
# <a name="what-is-sql-database-introduction-to-sql-database"></a>Informazioni sul database SQL Introduzione al Database SQL
Il database SQL è un servizio di database relazionale nel cloud basato sul motore di Microsoft SQL Server leader nel mercato e con funzionalità di importanza strategica. Il database SQL offre prestazioni prevedibili, scalabilità senza tempi di inattività, continuità aziendale e protezione dei dati con esigenze di amministrazione quasi nulle. È possibile concentrarsi sullo sviluppo rapido di app e accelerare i tempi di mercato, piuttosto che gestire le macchine virtuali e l’infrastruttura. Poiché si basa sul motore [SQL Server](https://msdn.microsoft.com/library/bb545450.aspx) , il Database SQL supporta strumenti del server SQL, librerie e API esistenti, il che rende più facile spostare ed estendere al cloud.

Questo articolo è un'introduzione dei concetti fondamentali sul database SQL e delle funzionalità correlate a prestazioni, scalabilità e gestibilità, con collegamenti per esplorare i dettagli. Se si è pronti per iniziare, è possibile [Creare il primo database SQL](sql-database-get-started.md) o [Creare un pool elastico](sql-database-elastic-pool-create-portal.md) in pochi minuti. Se si desiderano ulteriori approfondimenti, guardare questo video di 30 minuti.

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON326/player]
> 
> 

## <a name="adjust-performance-and-scale-without-downtime"></a>Regolare le prestazioni e scalabilità senza tempi di inattività
I database SQL sono disponibile in anteprima per i *livelli di servizio*Basic, Standard e Premium. Ogni livello di servizio offre [diversi livelli di prestazioni e capacità](sql-database-service-tiers.md) per supportare i carichi di lavoro del database, da quelli leggeri a quelli pesanti. È possibile compilare la prima app su un database di piccole dimensioni per pochi soldi mese, poi [modificare il livello di servizio](sql-database-scale-up.md) manualmente o a livello di codice in qualsiasi momento mentre l'app diventa virale in tutto il mondo, senza tempi di inattività per l'applicazione e gli utenti.

Per molte aziende e app, la possibilità di creare database autonomi e connettere o disconnettere prestazioni su richiesta è sufficiente, specialmente se i modelli d'uso sono relativamente prevedibili. Ma se si dispone di modelli di utilizzo imprevedibili, può risultare difficile gestire i costi e il modello aziendale.

[pool elastici](sql-database-elastic-pool.md) nel database SQL consentono di risolvere questo problema. Il concetto è semplice. È possibile allocare le prestazioni desiderate a un pool e pagare per le prestazioni collettive del pool anziché del database autonomo. Non è necessario connettere o disconnettere le prestazioni del database. I database nel pool, chiamati *database elastici*, aumentano e diminuiscono automaticamente per soddisfare la domanda. I database elastici consumano, ma non superano i limiti del pool, pertanto il costo rimane prevedibile anche se l'utilizzo del database non lo è. Inoltre, è possibile [aggiungere e rimuovere i database al pool](sql-database-elastic-pool-manage-portal.md), con la scalabilità dell’app da un numero limitato di database a migliaia, tutto all'interno di un budget che è possibile controllare. Per altre informazioni sui modelli di progettazione per applicazioni SaaS con pool elastici, vedere l'articolo relativo ai [modelli di progettazione per applicazioni SaaS multi-tenant con database SQL di Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).

In qualsiasi caso-o singolo o elastico-non è vincolante. È possibile combinare database autonomi con pool elastici e modificare i livelli di servizio dei database e dei pool in modo semplice e rapido per adattarli alle proprie esigenze. Inoltre, con la potenza e la portata di Azure, è possibile combinare e integrare altri servizi di Azure con il database SQL per soddisfare le esigenze esclusive di progettazione delle app, promuovere l'efficienza in termini di costi e di risorse e sfruttare nuove opportunità commerciali.

Ma come è possibile confrontare le prestazioni relative di database e i pool di database? Come si conosce il giusto arresto quando si connette e si disconnette? La risposta risiede negli strumenti di monitoraggio delle prestazioni e di avviso predefiniti e nelle valutazioni delle prestazioni basate su DTU (Database Transaction Unit) per i database autonomi e su eDTU (elastic DTU) per i database e i pool di database elastici che consentono di valutare l'impatto dell'aumento o della riduzione delle prestazioni in base alle esigenze correnti o del progetto. Per altre informazioni, vedere [Opzioni e prestazioni disponibili in ogni livello di servizio del database SQL](sql-database-service-tiers.md) .

## <a name="keep-your-app-and-business-running"></a>Mantenere l'applicazione e l’esecuzione dell’azienda
Il settore di Azure che ha una accordo sul livello di disponibilità del servizio del 99,99% [(SLA)](http://azure.microsoft.com/support/legal/sla/), fornito da una rete globale di datacenter gestiti da Microsoft, consente di mantenere l'applicazione in esecuzione 24 ore su 24, 7 giorni su 7. Con ogni database SQL, si sfruttano i vantaggi della sicurezza, della tolleranza di errore e della protezione dei dati predefinite che sarebbe altrimenti necessario acquistare o progettare, creare e gestire. Anche in questo caso, a seconda delle esigenze dell'azienda, si possono chiedere ulteriori livelli di protezione per garantire che l'app e l'azienda possano ripristinarsi rapidamente in caso di emergenza, di errore o di altre interruzioni. Con il database SQL, ogni livello di servizio offre un set completo di funzionalità di continuità aziendale e opzioni che è possibile usare per diventare e rimanere operativi. È possibile utilizzare un ripristino temporizzato per riportare un database a uno stato precedente, anche per 35 giorni. Inoltre, se nel data center che ospita i database si verifica un'interruzione, è possibile ripristinare i database dalle copie con ridondanza geografica degli ultimi backup o eseguire il failover alle repliche di database in un'area diversa. È anche possibile usare le repliche per gli aggiornamenti o la rilocazione in aree diverse.

![Replica geografica del database SQL](./media/sql-database-technical-overview/azure_sqldb_map.png)

Vedere [Continuità aziendale](sql-database-business-continuity.md) per informazioni dettagliate sulle diverse funzionalità per la continuità aziendale disponibili per i diversi livelli di servizio.

## <a name="secure-your-data"></a>Protezione dei dati
Il server SQL vanta una tradizione di sicurezza dei dati continua che rispetta i Database SQL con le funzionalità che consentono di monitorare l'attività, proteggere i dati e limitare l'accesso. Vedere [Protezione del database SQL](sql-database-security.md) per una rapida panoramica delle opzioni di sicurezza disponibili nel Database SQL. Vedere il [Centro di sicurezza per il motore di database di SQL Server e il database SQL di Azure](https://msdn.microsoft.com/library/bb510589) per una visione più completa delle funzionalità di protezione. Visitare il [Centro protezione di Azure](https://azure.microsoft.com/support/trust-center/security/) per informazioni sulla sicurezza della piattaforma Azure.

## <a name="next-steps"></a>Passaggi successivi
Dopo avere letto l'introduzione al database SQL e avere ottenuto informazioni sul database SQL, è possibile:

* Vedere la [pagina relativa ai prezzi](https://azure.microsoft.com/pricing/details/sql-database/) per informazioni sui prezzi e per confrontare e calcolare il prezzo di un database autonomo e un database elastico.
* Informazioni sui [pool elastici](sql-database-elastic-pool.md).
* Per iniziare, [creare il primo database](sql-database-get-started.md).
* [Connettersi al database SQL con SQL Server Management Studio ed eseguire una query T-SQL di esempio](sql-database-connect-query-ssms.md)
* Creare la prima app in C#, Java, Node.js, PHP, Python o Ruby: [Raccolte di connessioni per database SQL e SQL Server](sql-database-libraries.md)



<!--HONumber=Dec16_HO2-->


