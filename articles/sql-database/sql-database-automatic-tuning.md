---
title: Database SQL di Azure - Ottimizzazione automatica | Microsoft Docs
description: Il database SQL di Azure analizza le query SQL e si adatta automaticamente al carico di lavoro dell'utente.
services: sql-database
documentationcenter: 
author: jovanpop-msft
manager: drasumic
editor: danimir
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: On Demand
ms.date: 09/19/2017
ms.author: jovanpop
ms.openlocfilehash: 1e884754682ecab4cdf097bd75caa6fcf2e0a29c
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2017
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Ottimizzazione automatica nel database SQL di Azure

L'ottimizzazione automatica del database SQL di Azure offre prestazioni ottimali e carichi di lavoro stabili tramite l'ottimizzazione delle prestazioni continua per mezzo dell'intelligenza artificiale.

L'ottimizzazione automatica è un servizio completamente gestito che usa l'intelligenza artificiale incorporata per monitorare le query eseguite su un database e ne migliora automaticamente le prestazioni. Questo risultato viene raggiunto adattando in modo dinamico il database ai carichi di lavoro modificabili e applicando le raccomandazioni di ottimizzazione. L'ottimizzazione automatica apprende orizzontalmente da tutti i database in Azure grazie all'intelligenza artificiale e migliora in modo dinamico le azioni di ottimizzazione. Quanto più a lungo un database SQL di Azure viene eseguito con l'ottimizzazione automatica attivata, tanto migliori sono le prestazioni.

L'ottimizzazione automatica del database SQL di Azure potrebbe essere una delle funzionalità più importanti che è possibile abilitare per garantire carichi di lavoro con prestazioni ottimali e stabili.

## <a name="what-can-automatic-tuning-do-for-you"></a>Vantaggi dell'ottimizzazione automatica

- Ottimizzazione delle prestazioni automatizzata per i database SQL di Azure
- Verifica automatizzata dei miglioramenti delle prestazioni
- Ripristino dello stato precedente automatizzato e correzione automatica
- Log della cronologia dell'ottimizzazione
- Ottimizzazione degli script di azione T-SQL per le distribuzioni manuali
- Monitoraggio delle prestazioni dei carichi di lavoro proattivo
- Capacità di aumentare il numero di istanze in centinaia di migliaia di database
- Impatto positivo sulle risorse DevOps e sul costo totale di proprietà

## <a name="safe-reliable-and-proven"></a>Sicura, affidabile e collaudata

Le operazioni di ottimizzazione applicate ai database SQL di Azure sono assolutamente sicure per le prestazioni dei carichi di lavoro più elevati. Il sistema è stato progettato in modo che non interferisca con i carichi di lavoro degli utenti. Le raccomandazioni di ottimizzazione automatizzata vengono applicate solo in caso di sottoutilizzo. Il sistema può anche disabilitare temporaneamente le operazioni di ottimizzazione per proteggere le prestazioni dei carichi di lavoro. In tal caso, nel portale di Azure verrà visualizzato il messaggio "Disabled by the system" (Disabilitate dal sistema). L'ottimizzazione automatica interessa i carichi di lavoro con la priorità delle risorse più elevata.

I meccanismi di ottimizzazione automatica sono avanzati e sono stati perfezionati in centinaia di migliaia di database in esecuzione in Azure. Le operazioni di ottimizzazione automatizzata applicate vengono verificate automaticamente per assicurare che sussista un miglioramento delle prestazioni dei carichi di lavoro. Le raccomandazioni per le prestazioni peggiorate vengono rilevate in modo dinamico e ripristinate tempestivamente. Il log della cronologia dell'ottimizzazione offre una chiara analisi dei miglioramenti dell'ottimizzazione apportati a ogni database SQL di Azure. 

Per una panoramica del funzionamento dell'ottimizzazione automatica e gli scenari di utilizzo tipici, vedere il video incorporato:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-Automatic-Tuning/player]
>

L'ottimizzazione automatica del database SQL di Azure condivide la logica principale con il motore di ottimizzazione automatica di SQL Server. Per altre informazioni tecniche sul meccanismo di intelligenza artificiale incorporata, vedere [SQL Server automatic tuning](https://docs.microsoft.com/en-us/sql/relational-databases/automatic-tuning/automatic-tuning) (Ottimizzazione automatica di SQL Server).

## <a name="use-automatic-tuning"></a>Usare l'ottimizzazione automatica

L'ottimizzazione automatica deve essere abilitata manualmente nella sottoscrizione. Per abilitare l'ottimizzazione automatica usando il portale di Azure, vedere [Abilitare l'ottimizzazione automatica](sql-database-automatic-tuning-enable.md).

L'ottimizzazione automatica può funzionare in modo autonomo grazie all'applicazione automatica di raccomandazioni di ottimizzazione, inclusa la verifica automatizzata dei miglioramenti delle prestazioni. 

Per un maggiore controllo, è possibile disattivare l'applicazione automatica delle raccomandazioni di ottimizzazione che possono essere applicate manualmente tramite il portale di Azure. È anche possibile usare la soluzione solo per visualizzare le raccomandazioni di ottimizzazione automatizzata e applicarle manualmente tramite gli script e gli strumenti preferiti. 

## <a name="automatic-tuning-options"></a>Opzioni di ottimizzazione automatica

Le opzioni di ottimizzazione automatica disponibili nel database SQL di Azure sono le seguenti:
 1. **CREATE INDEX** (Crea indice), che identifica quali sono gli indici in grado di migliorare le prestazioni del carico di lavoro, li crea e verifica che migliorino le prestazioni delle query.
 2. **DROP INDEX** (Elimina indice), che identifica gli indici ridondanti e duplicati e gli indici che non sono stati usati per lungo tempo.
 3. **FORCE LAST GOOD PLAN** (Forza piano valido più recente), che identifica le query SQL che usano un piano di esecuzione, ma che sono più lente rispetto a un precedente piano valido e quindi applica l'ultimo piano valido noto invece del piano con regressione.

Il database SQL di Azure identifica le raccomandazioni relative a **CREATE INDEX** (Crea indice), **DROP INDEX** (Elimina indice) e **FORCE LAST GOOD PLAN** (Forza piano valido più recente) che possono ottimizzare il database e le visualizza nel portale di Azure. Per altre informazioni sull'identificazione degli indici che devono essere modificati, vedere [Find index recommendations in Azure portal](sql-database-advisor-portal.md) (Trovare raccomandazioni per gli indici nel portale di Azure). È possibile applicare le raccomandazioni manualmente tramite il portale o lasciare che sia il database SQL di Azure ad applicarle automaticamente, monitorare il carico di lavoro dopo la modifica e verificare che la raccomandazione abbia migliorato le prestazioni del carico di lavoro.

Le opzioni di ottimizzazione automatica possono essere attivate o disattivate in modo indipendente per ogni database, oppure possono essere configurate nel server logico e applicate a ogni database che eredita le impostazioni dal server. Il metodo consigliato per la configurazione dell'ottimizzazione automatica è quello che prevede la configurazione delle opzioni di ottimizzazione automatica sul server e l'eredità delle impostazioni nei database presenti nel server, perché semplifica la gestione delle opzioni di ottimizzazione automatica su un numero elevato di database.

## <a name="next-steps"></a>Passaggi successivi

- Per consentire all'ottimizzazione automatica nel database SQL di Azure di gestire il carico di lavoro, vedere [Abilitare l'ottimizzazione automatica](sql-database-automatic-tuning-enable.md).
- Per esaminare e applicare manualmente le raccomandazioni di ottimizzazione automatica, vedere [Trovare e applicare raccomandazioni per le prestazioni](sql-database-advisor-portal.md).
- Per altre informazioni sull'intelligenza artificiale incorporata usata nell'ottimizzazione automatica, vedere [Artificial Intelligence tunes Azure SQL Databases (L'intelligenza artificiale ottimizza i database SQL di Azure)](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/).
- Per altre informazioni sul funzionamento dell'ottimizzazione automatica nel database SQL di Azure e in SQL Server 2017, vedere [SQL Server automatic tuning (Ottimizzazione automatica di SQL Server)](https://docs.microsoft.com/en-us/sql/relational-databases/automatic-tuning/automatic-tuning).
