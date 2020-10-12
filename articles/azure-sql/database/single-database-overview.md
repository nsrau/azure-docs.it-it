---
title: Che cos'è un singolo database?
description: Informazioni sul tipo di risorsa database singolo nel database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 04/08/2019
ms.openlocfilehash: 8f92fe8e4a4ebbc2d970bf28e415859249b9f67c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84343320"
---
# <a name="what-is-a-single-database-in-azure-sql-database"></a>Che cos'è un database singolo nel database SQL di Azure?
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Il tipo di risorsa database singolo crea un database nel database SQL di Azure con il proprio set di risorse e viene gestito tramite un [Server](logical-servers.md). Con un database singolo, ogni database è isolato e portatile. Ognuno ha il proprio livello di servizio nel modello di [acquisto basato su DTU](service-tiers-dtu.md) o nel [modello di acquisto basato su vCore](service-tiers-vcore.md) e una dimensione di calcolo garantita.

> [!IMPORTANT]
> Il database singolo è un tipo di risorsa per il database SQL di Azure. L'altro è il [pool elastico](elastic-pool-overview.md).

## <a name="dynamic-scalability"></a>Scalabilità dinamica

Puoi creare la tua prima app in un database di piccole dimensioni, a basso costo nel livello di calcolo senza server o in una piccola dimensione di calcolo nel livello di calcolo di cui è stato effettuato il provisioning. È possibile modificare il [livello di calcolo o di servizio](single-database-scale.md) manualmente o a livello di codice in qualsiasi momento per soddisfare le esigenze della soluzione. È possibile regolare le prestazioni senza tempi di inattività per l'app o per i clienti. La scalabilità dinamica consente al database di rispettare i requisiti in continua evoluzione relativi alle risorse e di pagare solo le risorse necessarie quando necessario.

## <a name="single-databases-and-elastic-pools"></a>Database singoli e pool elastici

Un database singolo può essere spostato all'interno o all'esterno di un [pool elastico](elastic-pool-overview.md) per la condivisione delle risorse. Per molte aziende e applicazioni, la possibilità di creare singoli database e aumentare o ridurre le prestazioni all'occorrenza è sufficiente, specialmente se i modelli d'utilizzo sono relativamente prevedibili. Ma se si dispone di modelli di utilizzo imprevedibili, può risultare difficile gestire i costi e il modello aziendale. I pool elastici sono stati progettati per risolvere questo problema. Il concetto è semplice. Si allocano le risorse relative alle prestazioni a un pool invece che a un database singolo e quindi si pagano le risorse relative alle prestazioni collettive del pool invece di pagare le prestazioni del database singolo.

## <a name="monitoring-and-alerting"></a>Monitoraggio e avviso

Usare gli strumenti di [monitoraggio delle prestazioni](performance-guidance.md) e di [avviso](alerts-insights-configure-portal.md)predefiniti, combinati con le valutazioni delle prestazioni. Usando questi strumenti, è possibile valutare rapidamente l'impatto dell'aumento o della riduzione delle prestazioni in base alle esigenze correnti o previste relative alle prestazioni. Inoltre, Database SQL può [generare log di metriche e di risorse](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) per semplificare il monitoraggio.

## <a name="availability-capabilities"></a>Funzionalità per la disponibilità

I database singoli e i pool elastici offrono molte caratteristiche di disponibilità. Per informazioni, vedere [Caratteristiche di disponibilità](sql-database-paas-overview.md#availability-capabilities).

## <a name="transact-sql-differences"></a>Differenze di Transact-SQL

La maggior parte delle funzionalità Transact-SQL usate dalle applicazioni è supportata in Microsoft SQL Server e nel database SQL di Azure. Ad esempio, i componenti SQL principali, come tipi di dati, operatori, funzioni di stringa, funzioni aritmetiche, logiche e del cursore funzionano allo stesso modo in SQL Server e nel database SQL. T-SQL presenta, tuttavia, alcune differenze negli elementi DDL (Data Definition Language) e DML (Data Manipulation Language), di conseguenza alcune istruzioni e query T-SQL sono supportate solo in parte (questo verrà descritto più avanti in questo articolo).

Inoltre, alcune funzionalità e sintassi non sono supportate perché il database SQL di Azure è progettato per isolare le funzionalità dalle dipendenze nel database master e nel sistema operativo. Di conseguenza, la maggior parte delle attività a livello di server non è appropriata al database SQL. Le opzioni e le istruzioni T-SQL non sono disponibili se configurano opzioni a livello di server, configurano componenti del sistema operativo o specificano file system configurazione. Quando sono necessarie tali funzionalità, spesso è disponibile un'alternativa appropriata dal database SQL o da un'altra funzionalità o un altro servizio di Azure.

Per altre informazioni, vedere [Risoluzione delle differenze di Transact-SQL durante la migrazione al database SQL](transact-sql-tsql-differences-sql-server.md).

## <a name="security"></a>Sicurezza

Il database SQL offre una gamma di funzionalità di [sicurezza e conformità incorporate](security-overview.md) che consentono all'applicazione di soddisfare i diversi requisiti di sicurezza e conformità.

> [!IMPORTANT]
> Il database SQL di Azure è stato certificato per diversi standard di conformità. Per altre informazioni, visitare il [Centro protezione di Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), dove è possibile trovare l'elenco più aggiornato di certificazioni di conformità del database SQL.

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare rapidamente a usare un singolo database, iniziare con la [Guida introduttiva a database singolo](quickstart-content-reference-guide.md).
- Per altre informazioni sulla migrazione di un database SQL Server in Azure, vedere [Migrazione al database SQL di Azure](migrate-to-database-from-sql-server.md).
- Per informazioni sulle funzionalità supportate, vedere [Azure SQL Database features](features-comparison.md) (Funzioni del database SQL di Azure).
