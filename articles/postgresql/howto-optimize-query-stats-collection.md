---
title: Ottimizzare la raccolta di statistiche query in un database di Azure per la raccolta di statistiche query del server PostgreSQL
description: Questo articolo descrive come ottimizzare la raccolta di statistiche query in un database di Azure per il server PostgreSQL.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/25/2018
ms.openlocfilehash: df54693aee9a9a23b8202c90a6c23008ff7a7cb6
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2018
ms.locfileid: "53548715"
---
# <a name="optimize-query-statistics-collection-in-azure-database-for-postgresql-server"></a>Ottimizzare la raccolta di statistiche query in un database di Azure per il server PostgreSQL 
Questo articolo descrive come ottimizzare la raccolta di statistiche query in un database di Azure per il server PostgreSQL.

## <a name="using-pgstatsstatements"></a>Uso di pg_stats_statements
**Pg_stat_statements** è un'estensione di PostgreSQL abilitata per impostazione predefinita nel database di Azure per PostgreSQL. Questa estensione fornisce un modo per tenere traccia delle statistiche di esecuzione di tutte le istruzioni SQL eseguite da un server. Tuttavia, questo modulo interessa ogni esecuzione delle query ed è pertanto caratterizzato da un considerevole impatto sulle prestazioni. L'abilitazione di **pg_stat_statements** comporta operazioni di scrittura del testo delle query su file su disco.

Per i clienti che usano query univoche con testo di query di particolare lunghezza oppure che non eseguono un monitoraggio attivo di **pg_stat_statements** si consiglia di disabilitare **pg_stat_statements** per l'ottimizzazione delle prestazioni impostando `pg_stat_statements.track = NONE`.

In alcuni carichi di lavoro dei clienti è stato riscontrato un miglioramento delle prestazioni fino al 50% in seguito alla disabilitazione di **pg_stat_statements**. Tuttavia, il compromesso derivante dalla disabilitazione pg_stat_statements è rappresentato dall'impossibilità di risolvere i problemi relativi alle prestazioni.

Per impostare `pg_stat_statements.track = NONE`:

- Nel portale di Azure passare alla [pagina di gestione delle risorse PostgreSQL e selezionare il pannello dei parametri del server](howto-configure-server-parameters-using-portal.md).

![Pannello dei parametri del server PostgreSQL](./media/howto-optimize-query-stats-collection/pg_stats_statements_portal.png)

- Uso dell'[interfaccia della riga di comando di Azure](howto-configure-server-parameters-using-cli.md), az postgres server configuration set`--name pg_stat_statements.track --resource-group myresourcegroup --server mydemoserver --value NONE`.

## <a name="using-query-store"></a>Uso di Query Store 
La funzionalità [Query Store](concepts-query-store.md) disponibile nel database di Azure per PostgreSQL rappresenta il metodo più efficiente ed efficace per tenere traccia delle statistiche delle query, consigliato come alternativa all'uso di *pg_stats_statements*. 

## <a name="next-steps"></a>Passaggi successivi
È consigliabile impostare `pg_stat_statements.track = NONE` nel [portale di Azure](howto-configure-server-parameters-using-portal.md) o usando l'[interfaccia della riga di comando di Azure](howto-configure-server-parameters-using-cli.md).

Vedere gli [scenari di uso di Query Store](concepts-query-store-scenarios.md) e le [procedure consigliate per Query Store](concepts-query-store-best-practices.md) per altre informazioni. 
