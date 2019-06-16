---
title: Ottimizzare la raccolta di statistiche query in un Database di Azure per PostgreSQL - Server singolo
description: Questo articolo descrive come è possibile ottimizzare raccolta statistiche query in un Database di Azure per PostgreSQL - Server singolo
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 7425ee7916fd71625f336a7af35f6481d1ed2474
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65068967"
---
# <a name="optimize-query-statistics-collection-on-an-azure-database-for-postgresql---single-server"></a>Ottimizzare la raccolta delle statistiche di query su un Database di Azure per PostgreSQL - Server singolo
Questo articolo descrive come ottimizzare la raccolta di statistiche query in un server di Database di Azure per PostgreSQL.

## <a name="use-pgstatsstatements"></a>Usare pg_stats_statements
**Pg_stat_statements** è un'estensione di PostgreSQL abilitata per impostazione predefinita in Database di Azure per PostgreSQL. Questa estensione consente di tenere traccia delle statistiche di esecuzione per tutte le istruzioni SQL eseguite da un server. Questo modulo interessa ogni esecuzione di query e ha quindi un impatto di un certo rilievo sulle prestazioni. L'abilitazione di **pg_stat_statements** comporta operazioni di scrittura del testo delle query su file su disco.

Se si hanno query univoche con testo particolarmente lungo o non si esegue attivamente il monitoraggio di **pg_stat_statements**, disabilitare **pg_stat_statements** per ottenere prestazioni ottimali. A tale scopo, modificare l'impostazione su `pg_stat_statements.track = NONE`.

Per alcuni carichi di lavoro dei clienti è stato riscontrato un miglioramento delle prestazioni fino al 50% in seguito alla disabilitazione di **pg_stat_statements**. Disabilitando pg_stat_statements si ha tuttavia lo svantaggio di non poter risolvere i problemi relativi alle prestazioni.

Per impostare `pg_stat_statements.track = NONE`:

- Nel portale di Azure passare alla [pagina di gestione delle risorse PostgreSQL e selezionare il pannello dei parametri del server](howto-configure-server-parameters-using-portal.md).

  ![Pannello dei parametri del server PostgreSQL](./media/howto-optimize-query-stats-collection/pg_stats_statements_portal.png)

- Usare il comando dell'[interfaccia della riga di comando di Azure](howto-configure-server-parameters-using-cli.md) az postgres server configuration set con i parametri `--name pg_stat_statements.track --resource-group myresourcegroup --server mydemoserver --value NONE`.

## <a name="use-the-query-store"></a>Usare Query Store 
La funzionalità [Query Store](concepts-query-store.md) di Database di Azure per PostgreSQL offre un metodo più efficace per tenere traccia delle statistiche query. È consigliabile usare questa funzionalità come alternativa a *pg_stats_statements*. 

## <a name="next-steps"></a>Passaggi successivi
È consigliabile impostare `pg_stat_statements.track = NONE` nel [portale di Azure](howto-configure-server-parameters-using-portal.md) o usare l'[interfaccia della riga di comando di Azure](howto-configure-server-parameters-using-cli.md).

Per altre informazioni, vedere: 
- [Scenari di utilizzo dell'archivio query](concepts-query-store-scenarios.md) 
- [Procedure consigliate per Query Store](concepts-query-store-best-practices.md) 
