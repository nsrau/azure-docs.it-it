---
title: Database SQL di Azure - Ottimizzazione automatica | Microsoft Docs
description: Il database SQL di Azure analizza le query SQL e si adatta automaticamente al carico di lavoro dell'utente.
services: sql-database
documentationcenter: 
author: jovanpop-msft
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/19/2017
ms.author: jovanpop
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: 64abcf680199938c54be5005ee96670a8e580270
ms.contentlocale: it-it
ms.lasthandoff: 09/20/2017

---
# <a name="automatic-tuning-in-azure-sql-database"></a>Ottimizzazione automatica nel database SQL di Azure

Il database SQL di Azure è un servizio dati completamente gestito che monitora le query eseguite sul database e migliora automaticamente le prestazioni del relativo carico di lavoro. Il database SQL di Azure include un meccanismo di intelligence incorporato per l'[ottimizzazione automatica](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) in grado di ottimizzare automaticamente le query e migliorarne le prestazioni, adattando in modo dinamico il database al carico di lavoro. L'ottimizzazione automatica nel database SQL di Azure può essere una delle funzionalità più importanti che è possibile abilitare nel database SQL di Azure per ottimizzare le prestazioni delle query.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-in-the-Enterprise/Enabling-Azure-SQL-Database-Auto-Tuning-at-Scale-for-Microsoft-IT/player]
>

## <a name="automatic-tuning-options"></a>Opzioni di ottimizzazione automatica

Le opzioni di [ottimizzazione automatica](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) disponibili nel database SQL di Azure sono le seguenti:
 1. **CREATE INDEX** (Crea indice), che identifica quali sono gli indici in grado di migliorare le prestazioni del carico di lavoro, li crea e verifica che migliorino le prestazioni delle query.
 2. **DROP INDEX** (Elimina indice), che identifica gli indici ridondanti e duplicati e gli indici che non sono stati usati per lungo tempo.
 3. **PLAN REGRESSION CORRECTION** (Correzione della regressione dei piani), che identifica le query SQL che usano un piano di esecuzione ma che sono più lente rispetto a un precedente piano valido e quindi applica l'ultimo piano valido noto anziché il piano con regressione.

Il database SQL di Azure identifica le raccomandazioni relative a **CREATE INDEX** (Crea indice), **DROP INDEX** (Elimina indice) e **PLAN REGRESSION CORRECTION** (Correzione della regressione dei piani) che possono ottimizzare il database e le visualizza nel portale di Azure. Per altre informazioni sull'identificazione degli indici che devono essere modificati, vedere [Find index recommendations in Azure portal](sql-database-advisor-portal.md) (Trovare raccomandazioni per gli indici nel portale di Azure). È possibile applicare le raccomandazioni manualmente tramite il portale o lasciare che sia il database SQL di Azure ad applicarle automaticamente, monitorare il carico di lavoro dopo la modifica e verificare che la raccomandazione abbia migliorato le prestazioni del carico di lavoro.

Le opzioni di ottimizzazione automatica possono essere attivate o disattivate in modo indipendente per ogni database, oppure possono essere configurate nel server logico e applicate a ogni database che eredita le impostazioni dal server. Il metodo consigliato per la configurazione dell'ottimizzazione automatica è quello che prevede la configurazione delle opzioni di [ottimizzazione automatica](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) sul server e l'eredità delle impostazioni nei database presenti nel server, in quanto semplifica la gestione delle opzioni di ottimizzazione automatica su un numero elevato di database.

Vedere questo articolo che descrive la procedura per [abilitare l'ottimizzazione automatica](sql-database-automatic-tuning-enable.md) tramite il portale di Azure.

## <a name="next-steps"></a>Passaggi successivi

- Per abilitare l'ottimizzazione automatica nel database SQL di Azure e delegare la gestione completa del carico di lavoro alla funzionalità di ottimizzazione automatica, vedere [Abilitare l'ottimizzazione automatica](sql-database-automatic-tuning-enable.md).
- Per usare l'ottimizzazione manuale, è possibile esaminare le [raccomandazioni per l'ottimizzazione nel portale di Azure](sql-database-advisor-portal.md) e applicare manualmente quelle che consentono di migliorare le prestazioni delle query.
- Altre informazioni sull'intelligence integrata per l'ottimizzazione del [database SQL di Azure](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/).
- Altre informazioni sull'[ottimizzazione automatica](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) neI database SQL di Azure e SQL Server 2017.

