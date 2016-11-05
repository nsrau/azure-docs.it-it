---
title: Il database SQL di Azure crea app multi-tenant con isolamento ed efficienza
description: Informazioni su come compilare app multi-tenant con il database SQL di Azure
keywords: ''
services: sql-database
documentationcenter: ''
author: CarlRabeler
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 07/19/2016
ms.author: carlrab

---
# Compilare app multi-tenant tramite il database SQL di Azure con isolamento ed efficienza
## Sfruttare i pool elastici per compilare app multi-tenant più efficienti
Gli sviluppatori di SaaS che scrivono app multi-tenant e gestiscono vari clienti si trovano spesso a dover trovare un equilibrio tra vari compromessi a livello di prestazioni, gestione e sicurezza per i clienti. Con i pool di database elastici del database SQL non è più necessario scendere a compromessi di questo tipo. Questi pool permettono di gestire e monitorare app multi-tenant e di ottenere i vantaggi legati all'isolamento di un cliente per database. Vedere [Schemi progettuali per applicazioni SaaS multi-tenant con il database SQL di Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).

![build-multi-tenant-apps](./media/sql-database-build-multi-tenant-apps/sql-database-build-multi-tenant-apps.png)

## Ridimensionamento automatico del controllo
I pool permettono di ridimensionare automaticamente le prestazioni e la capacità di archiviazione per i database elastici in tempo reale. È possibile controllare le prestazioni assegnate a un pool, aggiungere o rimuovere database elastici su richiesta e definire le prestazioni dei database elastici senza influire sul costo complessivo del pool. Ciò significa che non è necessario preoccuparsi della gestione dell'utilizzo di singoli database.

[Leggere la documentazione](sql-database-elastic-pool.md)

## Gestione intelligente dell'ambiente
Le indicazioni di ridimensionamento predefinite identificano attivamente i database che possono trarre vantaggio dai pool. Tali indicazioni consentono l'analisi di simulazione per realizzare rapidamente l'ottimizzazione e soddisfare gli obiettivi di prestazioni. I dashboard avanzati di monitoraggio delle prestazioni e risoluzione dei problemi permettono di visualizzare utilizzo cronologico dei pool.

[Leggere la documentazione](sql-database-elastic-pool-guidance.md)

## Prestazioni e prezzi adatti alle esigenze
I pool Basic, Standard e Premium offrono un'ampia gamma di prestazioni, risorse di archiviazione e opzioni relative ai prezzi. I pool possono contenere fino a 400 database elastici. Il ridimensionamento automatico dei database elastici può arrivare fino a 1000 unità di transazione di database elastico (eDTU).

[Leggere la documentazione](https://azure.microsoft.com/pricing/details/sql-database/?b=16.50)

## Strumenti elastici
Oltre ai pool elastici, sono disponibili funzionalità del database SQL che permettono di gestire le attività operative in più database:

**Eseguire query e creare report tra database. Le** [query su database elastico](sql-database-elastic-query-overview.md) permettono di eseguire query o report tra database nel pool elastico e di accedere simultaneamente ai dati remoti archiviati in diversi database del pool.

** Eseguire transazioni tra database. Le** [transazioni di database elastico](sql-database-elastic-transactions-overview.md) permettono di eseguire operazioni e transazioni che comprendono più database nei database SQL, ad esempio durante l'elaborazione di transazioni finanziarie tra database o quando si aggiornano gli ordini e l'inventario in un database.

** Eseguire le stesse operazioni in più database. I** [processi di database elastico](sql-database-elastic-jobs-overview.md) eseguono operazioni amministrative come la ricompilazione degli indici o l'aggiornamento degli schemi in ogni database del pool elastico.

Passare alla home page per vedere cos'altro può offrire il database SQL. [Fare clic qui per provare](https://azure.microsoft.com/services/sql-database/)

## Passaggi successivi
Creare una [sottoscrizione gratuita ad Azure](https://azure.microsoft.com/get-started/) e [il primo database SQL di Azure](sql-database-get-started.md).

## Risorse aggiuntive
Esplorare tutte le [funzionalità del database SQL](https://azure.microsoft.com/services/sql-database/).

Consultare la [panoramica tecnica sul database SQL](sql-database-technical-overview.md).

<!---HONumber=AcomDC_0803_2016-->