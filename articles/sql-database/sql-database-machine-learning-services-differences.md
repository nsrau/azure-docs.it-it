---
title: Anteprima delle differenze principali di Machine Learning Services (con R) nel database SQL di Azure (anteprima)
description: Questo argomento descrive le differenze principali tra Machine Learning Services nel database SQL di Azure (con R) ed SQL Server Machine Learning Services.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning-services
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 01/31/2019
ms.openlocfilehash: 4350fb0e75f140e120ba6cd2f074ffa1816a8fce
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56237485"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-and-sql-server"></a>Differenze principali tra Machine Learning Services nel database SQL di Azure ed SQL Server

Le funzionalità di Machine Learning Services (con R) nel database SQL di Azure sono simili a quelle di [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning). Di seguito sono riportate alcune differenze principali tra questi servizi.

## <a name="language-support"></a>Supporto per le lingue

SQL Server include il supporto per R e Python tramite il [framework di estendibilità](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework). Il database SQL non supporta entrambi i linguaggi. Le differenze principali sono le seguenti:

- Nel database SQL, R è l'unico linguaggio supportato. Al momento non è disponibile il supporto per Python.
- La versione di R è 3.4.4.
- Non è necessario configurare `external scripts enabled` tramite `sp_configure`. Dopo aver [effettuato la registrazione](sql-database-machine-learning-services-overview.md#signup) Machine Learning è abilitato per il database SQL.

## <a name="package-management"></a>Gestione dei pacchetti

L'installazione e la gestione dei pacchetti R funziona in modo diverso nel database SQL e in SQL Server. Le differenze sono le seguenti:

- I pacchetti R vengono installati tramite [sqlmlutils](https://github.com/Microsoft/sqlmlutils) o [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql).
- I pacchetti non possono eseguire chiamate di rete in uscita. Questa limitazione è simile alle [regole predefinite del firewall per Machine Learning Services](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) in SQL Server, ma non può essere modificata nel database SQL.
- Non è disponibile il supporto per i pacchetti che dipendono da runtime esterni, ad esempio Java, o richiedono l'accesso alle API del sistema operativo per l'installazione o l'utilizzo.

## <a name="resource-governance"></a>Governance delle risorse

Non è possibile limitare le risorse R mediante [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) e pool di risorse esterni. Le risorse R rappresentano una percentuale delle risorse del database SQL e dipendono dal livello di servizio scelto. Per altre informazioni, vedere [Modelli di acquisto del database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers).

## <a name="security-isolation"></a>Isolamento di sicurezza

Nel database SQL di Azure, Platform Abstraction Layer di SQL fornisce l'isolamento per i processi esterni. L'isolamento offre un livello aggiuntivo di sicurezza per l'esecuzione di script R.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni generali, vedere la documentazione di [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics)
- Per informazioni sull'uso di Machine Learning Services (con R) nel database SQL di Azure, vedere la [Guida di avvio rapido](sql-database-connect-query-r.md).
- Altre informazioni nelle [esercitazioni sul linguaggio R in SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sql-server-r-tutorials)