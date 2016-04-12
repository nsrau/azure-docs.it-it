<properties
	pageTitle="Informazioni sul database SQL Introduzione al database SQL | Microsoft Azure"
	description="Introduzione al database SQL, con dettagli tecnici e funzionalità relative al sistema di gestione di database relazionali (RDBMS) Microsoft sul cloud."
	keywords="introduzione a sql,intro a sql,informazioni sul database sql"
	services="sql-database"
	documentationCenter=""
	authors="shontnew"
	manager="jeffreyg"
	editor="cgronlun"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="03/29/2016"
   ms.author="shkurhek"/>

# Informazioni sul database SQL Introduzione al Database SQL

Il database SQL è un servizio di database relazionale nel cloud basato sul motore di Microsoft SQL Server leader nel mercato e con funzionalità di importanza strategica. Il database SQL offre prestazioni prevedibili, scalabilità senza tempi di inattività, continuità aziendale e protezione dei dati con esigenze di amministrazione quasi nulle. È possibile concentrarsi sullo sviluppo rapido di app e accelerare i tempi di mercato, piuttosto che gestire le macchine virtuali e l’infrastruttura. Poiché si basa sul motore [SQL Server](https://msdn.microsoft.com/library/bb545450.aspx), il Database SQL supporta strumenti del server SQL, librerie e API esistenti, il che rende più facile spostare ed estendere al cloud.

Questo articolo è un'introduzione dei concetti fondamentali sul database SQL e delle funzionalità correlate a prestazioni, scalabilità e gestibilità, con collegamenti per esplorare i dettagli. Se si è pronti per iniziare, è possibile [Creare il primo database SQL di Azure](sql-database-get-started.md) o [Creare un pool di database elastici](sql-database-elastic-pool-create-portal.md) in pochi minuti. Se si desiderano ulteriori approfondimenti, guardare questo video di 30 minuti.

> [AZURE.VIDEO azurecon-2015-get-started-with-azure-sql-database]

## Regolare le prestazioni e scalabilità senza tempi di inattività

I database SQL sono disponibile in anteprima per i *livelli di servizio* Basic, Standard e Premium. Ogni livello di servizio offre [diversi livelli di prestazioni e capacità](sql-database-service-tiers.md) per supportare i carichi di lavoro del database, da quelli leggeri a quelli pesanti. È possibile compilare la prima app su un database di piccole dimensioni per pochi soldi mese, poi [modificare il livello di servizio](sql-database-scale-up.md) manualmente o a livello di codice in qualsiasi momento mentre l'app diventa virale in tutto il mondo, senza tempi di inattività per l'applicazione e gli utenti.

Per molte aziende e app, la possibilità di creare database e connettere o disconnettere prestazioni del database singolo su richiesta è sufficiente, specialmente se i modelli di utilizzo sono relativamente prevedibili. Ma se si dispone di modelli di utilizzo imprevedibili, può risultare difficile gestire i costi e il modello aziendale.

I [pool elastici](sql-database-elastic-pool.md) nel database SQL consentono di risolvere questo problema. Il concetto è semplice. È possibile allocare delle prestazioni a un pool e pagare le prestazioni collettive dei pool di piuttosto che le prestazioni del database singolo. Non è necessario connettere o disconnettere le prestazioni del database. I database nel pool, chiamati *database elastici*, aumentano e diminuiscono automaticamente per soddisfare la domanda. I database elastici consumano, ma non superano i limiti del pool, pertanto il costo rimane prevedibile anche se l'utilizzo del database non lo è. Inoltre, è possibile [aggiungere e rimuovere i database al pool](sql-database-elastic-pool-manage-portal.md), con la scalabilità dell’app da un numero limitato di database a migliaia, tutto all'interno di un budget che è possibile controllare.

In qualsiasi caso-o singolo o elastico-non è vincolante. È combinare mescolare database singoli con pool di database elastici e modificare i livelli di servizio di singoli database e pool per creare progettazioni innovative. Inoltre, con la potenza e la portata di Azure, è possibile combinare e integrare i servizi di Azure con il database SQL per soddisfare le esigenze esclusive di progettazione di app moderne dell'azienda, promuovere l'efficienza in termini di costi e di risorse e sfruttare nuove opportunità commerciali.

Ma come è possibile confrontare le prestazioni relative di database e i pool di database? Come si conosce il giusto arresto quando si connette e si disconnette? La risposta è l'unità di transazione di database (DTU) per singoli database e il valore di DTU di database elastico (eDTU) per i database elastici e i pool di database. Per altre informazioni, vedere [Opzioni e prestazioni disponibili in ogni livello di servizio del database SQL](sql-database-service-tiers.md)

## Mantenere l'applicazione e l’esecuzione dell’azienda

Il settore di Azure che ha una accordo sul livello di disponibilità del servizio del 99,99% [(SLA)](http://azure.microsoft.com/support/legal/sla/), fornito da una rete globale di datacenter gestiti da Microsoft, consente di mantenere l'applicazione in esecuzione 24 ore su 24, 7 giorni su 7. Con ogni database SQL, si sfruttano i vantaggi della protezione dati incorporata, la tolleranza di errore e la protezione dei dati che sarebbe altrimenti necessario progettare, acquistare, compilare e gestire. Anche in questo caso, a seconda delle esigenze dell'azienda, si possono chiedere ulteriori livelli di protezione per garantire che l'app e l'azienda possano ripristinarsi rapidamente in caso di emergenza, di errore o di altri elementi. Con il database SQL ogni livello di servizio offre un diverso menu di funzionalità, che è possibile usare per iniziare subito e rimanere operativi. È possibile utilizzare un ripristino temporizzato per riportare un database a uno stato precedente, anche per 35 giorni. Inoltre, se nel datacenter che ospita i database si verifica un'interruzione, è possibile eseguire il failover alle repliche del database in un'area diversa. Oppure è possibile utilizzare le repliche per gli aggiornamenti o la rilocazione in aree diverse.

![Replica geografica del Database SQL](./media/sql-database-technical-overview/azure_sqldb_map.png)


Vedere [Continuità aziendale](sql-database-business-continuity.md) per informazioni dettagliate sulle diverse funzionalità per la continuità aziendale disponibili per i diversi livelli di servizio.

## Protezione dei dati
Il server SQL vanta una tradizione di sicurezza dei dati continua che rispetta i Database SQL con le funzionalità che consentono di monitorare l'attività, proteggere i dati e limitare l'accesso. Vedere [Protezione del database SQL](sql-database-security.md) per una rapida panoramica delle opzioni di sicurezza disponibili nel Database SQL. Vedere il [Centro di sicurezza per il motore di database di SQL Server e il database SQL di Azure](https://msdn.microsoft.com/library/bb510589) per una visione più completa delle funzionalità di protezione. Visitare il [Centro protezione di Azure](https://azure.microsoft.com/support/trust-center/security/) per informazioni sulla sicurezza della piattaforma Azure.

## Passaggi successivi
Dopo avere letto l'introduzione al database SQL e avere ottenuto informazioni sul database SQL, è possibile:

- Vedere la [pagina relativa ai prezzi](https://azure.microsoft.com/pricing/details/sql-database/) per informazioni sui prezzi e per confrontare e calcolare il prezzo di un database singolo e un database elastico.
- Informazioni sui [pool elastici](sql-database-elastic-pool.md).
- Per iniziare, [creare il primo database](sql-database-get-started.md).
- [Connettersi al database SQL con SQL Server Management Studio ed eseguire una query T-SQL di esempio](sql-database-connect-query-ssms.md)
- Compilare la prima app in [C#](sql-database-connect-query.md), [Java](sql-database-develop-java-simple-windows.md), [Node.js](sql-database-develop-nodejs-simple-windows.md), [PHP](sql-database-develop-php-retry-windows.md), [Python](sql-database-develop-python-simple-windows.md) o [Ruby](sql-database-develop-ruby-simple-linux).
- Vedere un indice dei titoli e le descrizioni in [Tutti gli argomenti per il servizio di database SQL di Azure](sql-database-index-all-articles.md).

<!---HONumber=AcomDC_0330_2016-->