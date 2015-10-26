<properties
   pageTitle="Che cos'è il Database SQL | Microsoft Azure"
   description="Informazioni sui dettagli tecnici e sulle funzionalità del database SQL di Azure, sul sistema di gestione di database relazionali (RDBMS) di Microsoft e sulla soluzione PaaS nel cloud."
   services="sql-database"
   documentationCenter=""
   authors="shontnew"
   manager="jeffreyg"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/30/2015"
   ms.author="shkurhek"/>

# Introduzione al Database SQL

Il database SQL è un servizio di database relazionale nel cloud basato sul motore di Microsoft SQL Server leader nel mercato e con funzionalità di importanza strategica. Il database SQL offre prestazioni prevedibili, scalabilità senza tempi di inattività, continuità aziendale e protezione dei dati con esigenze di amministrazione quasi nulle. È possibile concentrarsi sullo sviluppo rapido di app e accelerare i tempi di mercato, piuttosto che gestire le macchine virtuali e l’infrastruttura. Poiché si basa sul motore [SQL Server](https://msdn.microsoft.com/library/bb545450.aspx), il Database SQL supporta strumenti del server SQL, librerie e API esistenti, il che rende più facile spostare ed estendere al cloud.

In questo articolo vengono introdotti i concetti di base e le funzionalità di Database SQL correlate a prestazioni, scalabilità e gestibilità, con collegamenti per esplorare i dettagli. Se si è pronti per passare, è possibile [Creare un database SQL](sql-database-get-started.md) o [Creare un pool di database elastici](sql-database-elastic-pool-portal.md) in pochi minuti. Se si desiderano ulteriori approfondimenti, guardare questo video di 30 minuti.


> [AZURE.VIDEO azurecon-2015-get-started-with-azure-sql-database]


## Regolare le prestazioni e scalabilità senza tempi di inattività
I database SQL sono disponibile in anteprima per i *livelli di servizio* Basic, Standard e Premium. Ogni livello di servizio offre [diversi livelli di prestazioni e capacità](sql-database-service-tiers.md) per supportare i carichi di lavoro del database, da quelli leggeri a quelli pesanti. È possibile compilare la prima app su un database di piccole dimensioni per pochi soldi mese, poi [modificare il livello di servizio](sql-database-scale-up.md) manualmente o a livello di codice in qualsiasi momento mentre l'app diventa virale in tutto il mondo, senza tempi di inattività per l'applicazione e gli utenti.

Per molte aziende e app, la possibilità di creare database e connettere o disconnettere prestazioni del database singolo su richiesta è sufficiente, specialmente se i modelli di utilizzo sono relativamente prevedibili. Ma se si dispone di modelli di utilizzo imprevedibili, può risultare difficile gestire i costi e il modello aziendale.

[Pool di database elastici](sql-database-elastic-pool.md) nel Database SQL per risolvere questo problema. Il concetto è semplice. È possibile allocare delle prestazioni a un pool e pagare le prestazioni collettive dei pool di piuttosto che le prestazioni del database singolo. Non è necessario connettere o disconnettere le prestazioni del database. I database nel pool, chiamati *database elastici*, aumentano e diminuiscono automaticamente per soddisfare la domanda. I database elastici consumano, ma non superano i limiti del pool, pertanto il costo rimane prevedibile anche se l'utilizzo del database non lo è. Inoltre, è possibile [aggiungere e rimuovere i database al pool](sql-database-elastic-pool-portal.md), con la scalabilità dell’app da un numero limitato di database a migliaia, tutto all'interno di un budget che è possibile controllare.

In qualsiasi caso-o singolo o elastico-non è vincolante. È possibile mescolare database singoli con pool di database elastici e modificare i livelli di servizio di singoli database e pool per creare progettazioni innovative. Inoltre, con la potenza e la portata di Azure, è possibile combinare e integrare i servizi di Azure con il database SQL per soddisfare le esigenze esclusive di progettazione di app moderne dell'azienda, promuovere l'efficienza in termini di costi e di risorse e sfruttare nuove opportunità commerciali.

Ma come è possibile confrontare le prestazioni relative di database e i pool di database? Come si conosce il giusto arresto quando si connette e si disconnette? La risposta è l'unità di transazione di database (DTU) per singoli database e il valore di DTU elastica (eDTU) per i database elastici e i pool di database.

## Comprendere i DTU

La Database Transaction Unit (DTU) è l’unità di misura nel database SQL che rappresenta la potenza relativa dei database basata su una misura del mondo reale: la transazione del database. Qui si è utilizzata una serie di operazioni che sono tipiche per una richiesta di elaborazione di una transazione in linea (OLTP) e si è quindi misurato il numero di transazioni completate al secondo in condizioni di carico totale (è possibile leggere i dettagli approfonditi in [Panoramica del Benchmark](https://msdn.microsoft.com/library/azure/dn741327.aspx)).

Un database Basic ha 5 DTU, ovvero può completare 5 transazioni al secondo, mentre un database Premium P11 ha 1750 DTU.

![Singolo database DTU](./media/sql-database-technical-overview/single_db_dtus.png)

Il valore di DTU per singoli database si traduce direttamente in eDTU per i database elastici. Ad esempio, un database in un pool di database elastici Basic offre fino a 5 eDTU. Ovvero la stessa prestazione di un database singolo Basic. La differenza è che il database elastico non utilizzerà eDTU dal pool fino a quando necessario.

![Pool elastici ed eDTU](./media/sql-database-technical-overview/sqldb_elastic_pools.png)

Un semplice esempio: Si prenda in considerazione un pool di database elastici Basic con 1000 DTU ed inserire 800 database in esso. Finché vengono utilizzati solo 200 degli 800 database in qualsiasi momento (5DTU X 200 = 1000), non si raggiungerà la capacità del pool, e le prestazioni del database non perderanno qualità. Questo esempio è semplificato per maggiore chiarezza. La matematica reale è un po' più complessa. Il portale esegue i calcoli matematici per l'utente e fornisce un'indicazione in base all'utilizzo cronologico di database. Vedere [Considerazioni sul prezzo e sulle prestazioni per un pool di database elastici](sql-database-elastic-pool-guidance.md) per informazioni sulle indicazioni o per eseguire i calcoli matematici personalmente.

## Mantenere l'applicazione e l’esecuzione dell’azienda

Il settore di Azure che ha una accordo sul livello di disponibilità del servizio del 99,99% [(SLA)](http://azure.microsoft.com/support/legal/sla/), fornito da una rete globale di datacenter gestiti da Microsoft, consente di mantenere l'applicazione in esecuzione 24 ore su 24, 7 giorni su 7. Con ogni database SQL, si sfruttano i vantaggi della protezione dati incorporata, la tolleranza di errore e la protezione dei dati che sarebbe altrimenti necessario progettare, acquistare, compilare e gestire. Anche in questo caso, a seconda delle esigenze dell'azienda, si possono chiedere ulteriori livelli di protezione per garantire che l'app e l'azienda possano ripristinarsi rapidamente in caso di emergenza, di errore o di altri elementi. Con il Database SQL, ogni livello di servizio offre un diverso menu di funzionalità, che è possibile utilizzare per iniziare ed eseguire. È possibile utilizzare un ripristino temporizzato per riportare un database a uno stato precedente, anche per 35 giorni. Inoltre, se nel datacenter che ospita i database si verifica un'interruzione, è possibile eseguire il failover alle repliche del database in un'area diversa. Oppure è possibile utilizzare le repliche per gli aggiornamenti o la rilocazione in aree diverse.

![Replica geografica del Database SQL](./media/sql-database-technical-overview/azure_sqldb_map.png)


Vedere [la continuità aziendale](sql-database-business-continuity.md) per informazioni dettagliate sulle funzionalità di continuità aziendale diversa disponibile per diversi livelli di servizio.

## Protezione dei dati
Il server SQL vanta una tradizione di sicurezza dei dati continua che rispetta i Database SQL con le funzionalità che consentono di monitorare l'attività, proteggere i dati e limitare l'accesso. Vedere [Protezione del database SQL](sql-database-security.md) per una rapida panoramica delle opzioni di sicurezza disponibili nel Database SQL. Vedere il [Centro sicurezza per il motore di Database del server SQL e il Database SQL](https://msdn.microsoft.com/library/bb510589) per una visione più completa delle funzionalità di protezione. Visitare il [Centro protezione di Azure](http://azure.microsoft.com/support/trust-center/security/) per informazioni sulla sicurezza della piattaforma Azure.

## Passaggi successivi

- Vedere la [pagina dei prezzi](http://azure.microsoft.com/pricing/details/sql-database/) per i prezzi e i calcoli del database singolo e del database elastico.

- Per iniziare, [creare il primo database](sql-database-get-started.md). Poi costruire la prima app in [C#](sql-database-connect-query.md), [Java](sql-database-develop-java-simple-windows.md), [Node.js](sql-database-develop-nodejs-simple-windows.md), [PHP](sql-database-develop-php-retry-windows.md), [Python](sql-database-develop-python-simple-windows.md), o [Ruby](sql-database-develop-ruby-simple-linux).

<!---HONumber=Oct15_HO3-->