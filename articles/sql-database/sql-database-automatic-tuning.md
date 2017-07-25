---
title: Database SQL - Ottimizzazione automatica | Microsoft Docs
description: Il database SQL analizza le query SQL e si adatta automaticamente al carico di lavoro dell&quot;utente.
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
ms.date: 06/05/2017
ms.author: jovanpop
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: f5552793db2d89542782b7d9e8f996314f62e429
ms.contentlocale: it-it
ms.lasthandoff: 06/17/2017


---
# <a name="automatic-tuning"></a>Ottimizzazione automatica

Il database SQL di Azure è un servizio dati completamente gestito che consente di monitorare le query eseguite sul database e di migliorare automaticamente le prestazioni del carico di lavoro. Il database SQL di Azure include un meccanismo di intelligence incorporato che consente di ottimizzare automaticamente le query e migliorarne le prestazioni, adattando in modo dinamico il database al carico di lavoro. L'ottimizzazione automatica nel database SQL di Azure può essere una delle funzionalità più importanti che è possibile abilitare nel database SQL di Azure per ottimizzare le prestazioni delle query.

Vedere questo articolo che descrive la procedura per [abilitare l'ottimizzazione automatica](sql-database-automatic-tuning-enable.md) tramite il portale di Azure.

## <a name="why-automatic-tuning"></a>Perché optare per l'ottimizzazione automatica?

Una delle attività principali per l'amministrazione classica dei database è il monitoraggio del carico di lavoro, per identificare le query SQL critiche, gli indici che devono essere aggiunti per migliorare le prestazioni e gli indici usati raramente. Il database SQL di Azure offre informazioni dettagliate per le query e gli indici che occorre monitorare. Il monitoraggio costante dei database è tuttavia un'attività complessa e tediosa, in particolare quando sono coinvolti molti database. Potrebbe essere impossibile gestire in modo efficiente un numero enorme di database anche con tutti gli strumenti e i report resi disponibili dal database SQL di Azure e nel portale di Azure. Invece di gestire manualmente il monitoraggio e l'ottimizzazione del database, è possibile prendere in considerazione la possibilità di delegare alcune delle azioni di monitoraggio e ottimizzazione al database SQL di Azure, tramite la funzionalità di ottimizzazione automatica. 


> [!VIDEO https://channel9.msdn.com/Blogs/Azure-in-the-Enterprise/Enabling-Azure-SQL-Database-Auto-Tuning-at-Scale-for-Microsoft-IT/player]
>

## <a name="how-does-automatic-tuning-work"></a>Come funziona l'ottimizzazione automatica

Il database SQL di Azure include un processo di monitoraggio e analisi delle prestazioni continuo che raccoglie costantemente informazioni sulle caratteristiche del carico di lavoro e identifica potenziali problemi e miglioramenti.

![Processo di ottimizzazione automatica](./media/sql-database-automatic-tuning/tuning-process.png)

Questo processo consente l'adattamento dinamico del database SQL di Azure al carico di lavoro mediante l'individuazione degli indici e dei piani che potrebbero migliorare le prestazioni dei carichi di lavoro e degli indici con effetti sui carichi di lavori. In base ai risultati raccolti, l'ottimizzazione automatica applica le azioni di ottimizzazione che consentono di migliorare le prestazioni del carico di lavoro. Inoltre, il database SQL di Azure esegue un monitoraggio costante delle prestazioni dopo qualsiasi modifica apportata dall'ottimizzazione automatica, per assicurarsi che consenta di migliorare le prestazioni del carico di lavoro. Qualsiasi azione che non introduce miglioramenti delle prestazioni viene annullata automaticamente. Il processo di verifica è una funzionalità chiave che assicura che qualsiasi modifica apportata dall'ottimizzazione automatica non diminuisca le prestazioni del carico di lavoro.

Nel database SQL di Azure sono disponibili due contesti di ottimizzazione automatica:

 -  **Gestione automatica degli indici** che consente di identificare gli indici da aggiungere al database e quelli che è consigliabile rimuovere.
 -  **Correzione automatica dei piani** (presto disponibile, già presente in SQL Server 2017) che consente di identificare i piani problematici e correggere i problemi di prestazioni dei piani SQL.

## <a name="automatic-index-management"></a>Gestione automatica degli indici

Nel database SQL di Azure la gestione degli indici è semplice perché il database SQL di Azure raccoglie informazioni sul carico di lavoro e assicura che i dati vengano indicizzati sempre in modo ottimale. La progettazione appropriata degli indici è fondamentale per ottenere prestazioni ottimali del carico di lavoro e la gestione automatica degli indici risulta utile per ottimizzare gli indici. La gestione automatica degli indici consente di correggere i problemi di prestazioni nei database indicizzati in modo non corretto oppure di gestire e migliorare gli indici nello schema del database esistente. 

### <a name="why-do-you-need-index-management"></a>Utilità della gestione degli indici

Gli indici consentono di velocizzare alcune delle query che leggono dati dalle tabelle, ma possono rallentare le query che aggiornano i dati. È necessario valutare con attenzione quando creare un indice e quali colonne includervi. Alcuni indici potrebbero non essere più necessari dopo un certo periodo di tempo. Per questo motivo, è consigliabile individuare ed eliminare periodicamente gli indici che non portano alcun vantaggio. Se si ignorano gli indici inutilizzati, le prestazioni delle query di aggiornamento dei dati potrebbero risultare ridotte, senza alcun vantaggio per le query di lettura dei dati. Gli indici inutilizzati influiscono anche sulle prestazioni complessive del sistema perché risultano necessarie operazioni di registrazione superflue per aggiornamenti aggiuntivi.

Per individuare il set ottimale di indici che consentono di migliorare le prestazioni delle query di lettura dei dati dalle tabelle con un impatto minimo per gli aggiornamenti, possono essere necessarie operazioni di analisi continue e complesse.

Il database SQL di Azure usa i meccanismi di intelligence incorporati e regole avanzate per analizzare le query, identificare gli indici che sarebbero ottimali per i carichi di lavoro correnti e gli indici che potrebbero essere rimossi. Il database SQL di Azure assicura di avere a disposizione il set minimo necessario di indici per l'ottimizzazione delle query di lettura dei dati, con un impatto minimo sulle altre query.

### <a name="how-to-identify-indexes-that-need-to-be-changed-in-your-database"></a>Come identificare gli indici che devono essere modificati nel database

Il database SQL di Azure semplifica il processo di gestione degli indici. In alternativa al processo manuale noioso di analisi del carico di lavoro e di monitoraggio degli indici, il database SQL di Azure consente di analizzare il carico di lavoro, identificare le query che potrebbero essere eseguite più rapidamente con un nuovo indice e identificare gli indici inutilizzati o duplicati. Per altre informazioni sull'identificazione degli indici che devono essere modificati, vedere [Find index recommendations in Azure portal](sql-database-advisor-portal.md) (Trovare raccomandazioni per gli indici nel portale di Azure).

### <a name="automatic-index-management-considerations"></a>Considerazioni sulla gestione automatica degli indici

Se si appura che le regole predefinite consentono di migliorare le prestazioni del database, è possibile delegare al database SQL di Azure la gestione automatica degli indici.

Le azioni richieste per creare gli indici necessari nei database SQL di Azure potrebbero usare risorse e influire temporaneamente sulle prestazioni del carico di lavoro. Per ridurre al minimo l'impatto della creazione degli indici sulle prestazioni del carico di lavoro, il database SQL di Azure individua la finestra temporale appropriata per qualsiasi operazione di gestione degli indici. L'azione di ottimizzazione viene posticipata se il database ha bisogno di risorse per l'esecuzione del carico di lavoro e viene avviata quando per il database è disponibile una quantità di risorse inutilizzate sufficiente per essere usata per l'attività di manutenzione. Una funzionalità importante nella gestione automatica degli indici e la verifica delle azioni. Quando il database SQL di Azure crea o elimina un indice, un processo di monitoraggio analizza le prestazioni del carico di lavoro per verificare che l'azione migliori effettivamente le prestazioni. Se non introduce miglioramenti significativi, l'azione viene annullata immediatamente. In questo modo, il database SQL di Azure garantisce che le azioni automatiche non influiscano negativamente sulle prestazioni del carico di lavoro. Gli indici creati tramite l'ottimizzazione automatica sono trasparenti per l'operazione di manutenzione sullo schema sottostante. Le modifiche dello schema, ad esempio l'eliminazione o ridenominazione delle colonne, non vengono impedite dalla presenza di indici creati automaticamente. Gli indici creati automaticamente dal database SQL di Azure vengono eliminati immediatamente in seguito all'eliminazione di tabelle o colonne correlate.

## <a name="automatic-plan-choice-correction"></a>Correzione automatica del piano

La correzione automatica del piano è una nuova funzionalità di ottimizzazione automatica aggiunta in SQL Server 2017 CTP2.0 che identifica i piani SQL con comportamento errato. Questa opzione di ottimizzazione automatica sarà presto disponibile nel database SQL di Azure. Per altre informazioni, vedere [Ottimizzazione automatica in SQL Server 2017](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning).

## <a name="next-steps"></a>Passaggi successivi

- Per abilitare l'ottimizzazione automatica nel database SQL di Azure e delegare la gestione completa del carico di lavoro alla funzionalità di ottimizzazione automatica, vedere [Abilitare l'ottimizzazione automatica](sql-database-automatic-tuning-enable.md).
- Per usare l'ottimizzazione manuale, è possibile esaminare le [raccomandazioni per l'ottimizzazione nel portale di Azure](sql-database-advisor-portal.md) e applicare manualmente quelle che consentono di migliorare le prestazioni delle query.

