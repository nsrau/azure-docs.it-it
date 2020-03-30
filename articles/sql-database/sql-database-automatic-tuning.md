---
title: Panoramica dell'ottimizzazione automatica
description: Il database SQL di Azure analizza le query SQL e si adatta automaticamente al carico di lavoro dell'utente.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: d4762c86268353ff0464ff3919250cd86f0038d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214109"
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Ottimizzazione automatica nel database SQL di Azure

L'ottimizzazione automatica del database SQL di Azure offre prestazioni di picco e carichi di lavoro stabili tramite l'ottimizzazione continua delle prestazioni basata sull'ia-atura e sull'apprendimento automatico.

L'ottimizzazione automatica è un servizio per prestazioni intelligenti completamente gestito che usa l'intelligenza artificiale incorporata per monitorare le query eseguite su un database e ne migliora automaticamente le prestazioni. Questo risultato viene raggiunto adattando in modo dinamico il database ai carichi di lavoro modificabili e applicando le raccomandazioni di ottimizzazione. L'ottimizzazione automatica apprende orizzontalmente da tutti i database in Azure grazie all'intelligenza artificiale e migliora in modo dinamico le azioni di ottimizzazione. Più a lungo un database viene eseguito con l'ottimizzazione automatica attivata, migliori sono le prestazioni.

L'ottimizzazione automatica del database SQL di Azure potrebbe essere una delle funzionalità più importanti che è possibile abilitare per fornire carichi di lavoro di database stabili e con prestazioni di picco.

## <a name="what-can-automatic-tuning-do-for-you"></a>Cosa può fare l'ottimizzazione automatica per te

- Ottimizzazione automatizzata delle prestazioni per i database SQL di Azure
- Verifica automatizzata dei miglioramenti delle prestazioni
- Ripristino dello stato precedente automatizzato e correzione automatica
- Cronologia ottimizzazione
- Ottimizzazione degli script di azione T-SQL per le distribuzioni manuali
- Monitoraggio delle prestazioni dei carichi di lavoro proattivo
- Capacità di aumentare il numero di istanze in centinaia di migliaia di database
- Impatto positivo sulle risorse DevOps e sul costo totale di proprietà

## <a name="safe-reliable-and-proven"></a>Sicura, affidabile e collaudata

Le operazioni di ottimizzazione applicate ai database nel database SQL di Azure sono completamente sicure per le prestazioni dei carichi di lavoro più intensi. Il sistema è stato progettato in modo che non interferisca con i carichi di lavoro degli utenti. Le raccomandazioni di ottimizzazione automatizzata vengono applicate solo in caso di sottoutilizzo. Il sistema può anche disabilitare temporaneamente le operazioni di ottimizzazione per proteggere le prestazioni dei carichi di lavoro. In tal caso, il messaggio "Disabilitato dal sistema" verrà visualizzato nel portale di Azure.In such case, "Disabled by the system" message will be shown in Azure portal. L'ottimizzazione automatica interessa i carichi di lavoro con la priorità delle risorse più elevata.

I meccanismi di ottimizzazione automatica sono ormai avanzati e sono stati perfezionati in diversi milioni di database in esecuzione su Azure. Le operazioni di ottimizzazione automatizzata applicate vengono verificate automaticamente per assicurare che sussista un miglioramento delle prestazioni dei carichi di lavoro. Le raccomandazioni per le prestazioni peggiorate vengono rilevate in modo dinamico e ripristinate tempestivamente. La cronologia di ottimizzazione registrata offre una chiara analisi dei miglioramenti apportati a ogni database SQL di Azure.

![Come funziona l'ottimizzazione automatica](./media/sql-database-automatic-tuning/how-does-automatic-tuning-work.png)

L'ottimizzazione automatica del database SQL di Azure condivide la logica di base con il motore di ottimizzazione automatica di SQL Server.Azure SQL Database automatic tuning is sharing its core logic with the SQL Server automatic tuning engine. Per altre informazioni tecniche sul meccanismo di intelligenza artificiale incorporata, vedere [SQL Server automatic tuning](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) (Ottimizzazione automatica di SQL Server).

Per una panoramica del funzionamento dell'ottimizzazione automatica e per gli scenari di utilizzo tipici, vedere il video incorporato:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-automatic-tuning/player]

## <a name="enable-automatic-tuning"></a>Abilitare l'ottimizzazione automatica

È possibile [abilitare l'ottimizzazione automatica per](sql-database-automatic-tuning-enable.md) i database singoli e in pool nel portale di Azure o usando l'istruzione [Alter DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) T-SQL. L'ottimizzazione automatica per i database di istanza in una distribuzione di istanze gestite viene abilitata utilizzando l'istruzione [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-mi-current) T-SQL.

## <a name="automatic-tuning-options"></a>Opzioni di ottimizzazione automatica

Le opzioni di ottimizzazione automatica disponibili nel database SQL di Azure sono le seguenti:

| Opzione di sintonizzazione automatica | Supporto dei database singoli e in pool | Supporto del database dell'istanza |
| :----------------------------- | ----- | ----- |
| **CREATE INDEX:** identifica gli indici che possono migliorare le prestazioni del carico di lavoro, crea gli indici e verifica automaticamente che le prestazioni delle query siano migliorate. | Sì | No |
| **DROP INDEX** - Identifica ogni giorno gli indici ridondanti e duplicati, ad eccezione degli indici univoci e degli indici non utilizzati per un lungo periodo di tempo (>90 giorni). Si noti che questa opzione non è compatibile con le applicazioni che usano cambi di partizione e hint di indice. L'eliminazione degli indici inutilizzati non è supportata per i livelli di servizio Premium e Business Critical. | Sì | No |
| **FORCE LAST GOOD PLAN** (correzione automatica del piano) - Identifica le query SQL utilizzando un piano di esecuzione più lento rispetto al piano valido precedente e le query che utilizzano l'ultimo piano valido noto anziché il piano regredito. | Sì | Sì |

### <a name="automatic-tuning-for-single-and-pooled-databases"></a>Ottimizzazione automatica per database singoli e in pool

L'ottimizzazione automatica per database singoli e in pool utilizza i consigli di preparazione del database **CREATE INDEX**, **DROP INDEX**e FORCE LAST **GOOD PLAN** per ottimizzare le prestazioni del database. Per altre informazioni, vedere Consigli di [Advisor di database nel portale](sql-database-advisor-portal.md)di Azure , in [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction)e nell'API [REST](https://docs.microsoft.com/rest/api/sql/serverautomatictuning).

È possibile applicare manualmente i suggerimenti per l'ottimizzazione usando il portale di Azure oppure consentire l'ottimizzazione automatica in modo autonomo alle di applicare autonomamente i suggerimenti di ottimizzazione. I vantaggi di consentire al sistema di applicare in modo autonomo le opzioni di ottimizzazione consistono nella convalida automatica della presenza di un aumento delle prestazioni del carico di lavoro e, se non vengono rilevati miglioramenti significativi delle prestazioni, nel ripristino dello stato prima dell'applicazione delle opzioni di ottimizzazione. Si noti che, in caso di query interessate da opzioni di ottimizzazione che non vengono eseguite di frequente, la fase di convalida può richiedere fino a 72 ore per impostazione predefinita.

Se si applicano raccomandazioni di ottimizzazione tramite T-SQL, la convalida automatica delle prestazioni e i meccanismi di storno non sono disponibili. Le raccomandazioni applicate in questo modo rimarranno attive e visualizzate nell'elenco delle raccomandazioni di ottimizzazione per 24-48 ore. prima che il sistema li ritiri automaticamente. Se si vuole rimuovere un suggerimento prima, è possibile eliminarlo dal portale di Azure.If you would to remove a recommendation before, you can discard it from Azure portal.

Le opzioni di ottimizzazione automatica possono essere abilitate o disabilitate in modo indipendente per ogni database, oppure possono essere configurate nei server di database SQL e applicate a ogni database che eredita le impostazioni dal server. I server di database SQL possono ereditare le impostazioni predefinite di Azure per le impostazioni di ottimizzazione automatica. Le impostazioni predefinite di Azure in questo momento sono FORCE_LAST_GOOD_PLAN (abilitata), CREATE_INDEX (abilitata) e DROP_INDEX (disabilitata).

> [!IMPORTANT]
> A partire da marzo, 2020 le modifiche alle impostazioni predefinite di Azure per l'ottimizzazione automatica avranno effetto come segue:
>
> - Le nuove impostazioni predefinite di Azure saranno FORCE_LAST_GOOD_PLAN, abilitato, CREATE_INDEX, disabilitato e DROP_INDEX disabilitato.
> - I server esistenti senza preferenze di ottimizzazione automatica configurate verranno configurati automaticamente per INHERIT le nuove impostazioni predefinite di Azure.Existing servers with no automatic tuning preferences configured will be automatically configured to INHERIT the new Azure defaults. Questo vale per tutti i clienti che attualmente hanno impostazioni del server per l'ottimizzazione automatica in uno stato indefinito.
> - I nuovi server creati verranno configurati automaticamente in INHERIT le nuove impostazioni predefinite di Azure (a differenza delle versioni precedenti, quando la configurazione di ottimizzazione automatica era in uno stato non definito al momento della creazione del nuovo server).

La configurazione delle opzioni di ottimizzazione automatica in un server e l'ereditarietà delle impostazioni per i database appartenenti al server padre è un metodo consigliato per la configurazione dell'ottimizzazione automatica in quanto semplifica la gestione delle opzioni di ottimizzazione automatica per un numero elevato di database.

Per informazioni sulla creazione di notifiche e-mail per suggerimenti di ottimizzazione automatica, vedere [Notifiche tramite posta elettronica per l'ottimizzazione automatica](sql-database-automatic-tuning-email-notifications.md).

### <a name="automatic-tuning-for-instance-databases"></a>Ottimizzazione automatica per i database di esempio

L'ottimizzazione automatica dei database di istanza in una distribuzione di istanze gestite supporta solo **FORCE LAST GOOD PLAN**. Per ulteriori informazioni sulla configurazione delle opzioni di ottimizzazione automatica tramite T-SQL, vedere [L'ottimizzazione automatica introduce](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) la correzione automatica del piano e [Correzione automatica del piano](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning?view=sql-server-ver15#automatic-plan-correction).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sull'intelligenza incorporata usata nell'ottimizzazione automatica, vedere [Intelligenza artificiale ottimizza i database SQL](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/)di Azure.
