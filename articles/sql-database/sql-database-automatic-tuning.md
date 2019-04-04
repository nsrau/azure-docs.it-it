---
title: Database SQL di Azure - Ottimizzazione automatica | Microsoft Docs
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
manager: craigg
ms.date: 03/06/2019
ms.openlocfilehash: 6e818da29b7ee0d17ebe4f8e523648146973fa63
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58905359"
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Ottimizzazione automatica nel database SQL di Azure

L'ottimizzazione automatica del database SQL di Azure garantisce massime prestazioni e carichi di lavoro stabili grazie al miglioramento continuo delle prestazioni basato sull'intelligenza artificiale e sull'apprendimento automatico.

L'ottimizzazione automatica è un servizio per prestazioni intelligenti completamente gestito che usa l'intelligenza artificiale incorporata per monitorare le query eseguite su un database e ne migliora automaticamente le prestazioni. Questo risultato viene raggiunto adattando in modo dinamico il database ai carichi di lavoro modificabili e applicando le raccomandazioni di ottimizzazione. L'ottimizzazione automatica apprende orizzontalmente da tutti i database in Azure grazie all'intelligenza artificiale e migliora in modo dinamico le azioni di ottimizzazione. Quanto più a lungo un database SQL di Azure viene eseguito con l'ottimizzazione automatica attivata, tanto migliori sono le prestazioni.

L'ottimizzazione automatica del database SQL di Azure potrebbe essere una delle funzionalità più importanti che è possibile abilitare per garantire carichi di lavoro del database con prestazioni ottimali e stabili.

## <a name="what-can-automatic-tuning-do-for-you"></a>Vantaggi dell'ottimizzazione automatica

- Ottimizzazione automatizzata delle prestazioni per i database SQL di Azure
- Verifica automatizzata dei miglioramenti delle prestazioni
- Ripristino dello stato precedente automatizzato e correzione automatica
- Cronologia ottimizzazione
- Ottimizzazione degli script di azione T-SQL per le distribuzioni manuali
- Monitoraggio delle prestazioni dei carichi di lavoro proattivo
- Capacità di aumentare il numero di istanze in centinaia di migliaia di database
- Impatto positivo sulle risorse DevOps e sul costo totale di proprietà

## <a name="safe-reliable-and-proven"></a>Sicura, affidabile e collaudata

Le operazioni di ottimizzazione applicate ai database SQL di Azure sono assolutamente sicure per le prestazioni dei carichi di lavoro più elevati. Il sistema è stato progettato in modo che non interferisca con i carichi di lavoro degli utenti. Le raccomandazioni di ottimizzazione automatizzata vengono applicate solo in caso di sottoutilizzo. Il sistema può anche disabilitare temporaneamente le operazioni di ottimizzazione per proteggere le prestazioni dei carichi di lavoro. In tal caso, nel portale di Azure verrà visualizzato il messaggio "Disabled by the system" (Disabilitate dal sistema). L'ottimizzazione automatica interessa i carichi di lavoro con la priorità delle risorse più elevata.

I meccanismi di ottimizzazione automatica sono ormai avanzati e sono stati perfezionati in diversi milioni di database in esecuzione su Azure. Le operazioni di ottimizzazione automatizzata applicate vengono verificate automaticamente per assicurare che sussista un miglioramento delle prestazioni dei carichi di lavoro. Le raccomandazioni per le prestazioni peggiorate vengono rilevate in modo dinamico e ripristinate tempestivamente. La cronologia di ottimizzazione registrata offre una chiara analisi dei miglioramenti apportati a ogni database SQL di Azure. 

![Come funziona l'ottimizzazione automatica](./media/sql-database-automatic-tuning/how-does-automatic-tuning-work.png)

L'ottimizzazione automatica del database SQL di Azure condivide la logica principale con il motore di ottimizzazione automatica di SQL Server. Per altre informazioni tecniche sul meccanismo di intelligenza artificiale incorporata, vedere [SQL Server automatic tuning](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) (Ottimizzazione automatica di SQL Server).

## <a name="use-automatic-tuning"></a>Usare l'ottimizzazione automatica

L'ottimizzazione automatica deve essere abilitato per la sottoscrizione. Per abilitare l'ottimizzazione automatica usando il portale di Azure, vedere [Abilitare l'ottimizzazione automatica](sql-database-automatic-tuning-enable.md).

L'ottimizzazione automatica può funzionare in modo autonomo grazie all'applicazione automatica di raccomandazioni di ottimizzazione, inclusa la verifica automatizzata dei miglioramenti delle prestazioni. 

Per un maggiore controllo, è possibile disattivare l'applicazione automatica delle raccomandazioni di ottimizzazione che possono essere applicate manualmente tramite il portale di Azure. È anche possibile usare la soluzione solo per visualizzare le raccomandazioni di ottimizzazione automatizzata e applicarle manualmente tramite gli script e gli strumenti preferiti. 

Per una panoramica del funzionamento dell'ottimizzazione automatica e per gli scenari di utilizzo tipici, vedere il video incorporato:


> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-Automatic-Tuning/player]
>

## <a name="automatic-tuning-options"></a>Opzioni di ottimizzazione automatica

Le opzioni di ottimizzazione automatica disponibili nel database SQL di Azure sono le seguenti:

| Opzione di ottimizzazione automatica | Supporto dei database singoli e in pool | Supporto dell'istanza del database |
| :----------------------------- | ----- | ----- |
| **CREATE INDEX** -identifica gli indici che potrebbero migliorare le prestazioni del carico di lavoro, consente di creare indici e verifica automaticamente che sono migliorate le prestazioni delle query. | Sì | No  | 
| **DROP INDEX** -identifica gli indici ridondanti e duplicati ogni giorno, ad eccezione di indici univoci e gli indici non utilizzati per molto tempo (> 90 giorni). Si noti che al momento l'opzione non è compatibile con le applicazioni che usano hint di indice e cambi di partizione. | Sì | No  |
| **IMPOSIZIONE dell'ultimo piano valido** (correzione automatica dei piani) - query SQL identifica tramite piano di esecuzione che è più lento rispetto al precedente piano valido e di query con l'ultimo piano valido noto anziché il piano con regressione. | Sì | Sì |

L'ottimizzazione automatica identifica le opzioni **CREATE INDEX** (Crea indice), **DROP INDEX** (Elimina indice) e **FORCE LAST GOOD PLAN** (Forza piano valido più recente) che consentono di ottimizzare le prestazioni del database, di visualizzarle nel [portale di Azure](sql-database-advisor-portal.md) e di esporle tramite [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) e l'[API REST](https://docs.microsoft.com/rest/api/sql/serverautomatictuning). Per altre informazioni sull'imposizione dell'ultimo piano valido e configurare le opzioni di ottimizzazione automatica tramite T-SQL, vedere [ottimizzazione automatica introduce la correzione automatica del piano](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/).

È possibile applicare manualmente le opzioni di ottimizzazione usando il portale o è possibile consentirne l'applicazione autonoma da parte dell'ottimizzazione automatica. I vantaggi di consentire al sistema di applicare in modo autonomo le opzioni di ottimizzazione consistono nella convalida automatica della presenza di un aumento delle prestazioni del carico di lavoro e, se non vengono rilevati miglioramenti significativi delle prestazioni, nel ripristino dello stato prima dell'applicazione delle opzioni di ottimizzazione. Si noti che, in caso di query interessate da opzioni di ottimizzazione che non vengono eseguite di frequente, la fase di convalida può richiedere fino a 72 ore per impostazione predefinita.

Nel caso in cui si applichino manualmente le opzioni di ottimizzazione, la convalida automatica delle prestazioni e i meccanismi di inversione non sono disponibili. Inoltre, raccomandazioni applicate manualmente rimarrà attiva e mostrate nell'elenco delle raccomandazioni per 24-48 ore. prima che il sistema prelevi automaticamente li. Se si desidera rimuovere prima una raccomandazione, è possibile eliminarlo manualmente.

Le opzioni di ottimizzazione automatica possono essere abilitate o disabilitate in modo indipendente per ogni database, oppure possono essere configurate nei server di database SQL e applicate a ogni database che eredita le impostazioni dal server. I server di database SQL possono ereditare le impostazioni predefinite di Azure per le impostazioni di ottimizzazione automatica. Le impostazioni predefinite di Azure in questo momento sono FORCE_LAST_GOOD_PLAN (abilitata), CREATE_INDEX (abilitata) e DROP_INDEX (disabilitata).

Il metodo consigliato per la configurazione dell'ottimizzazione automatica è quello che prevede la configurazione delle opzioni di ottimizzazione automatica in un server e l'eredità delle impostazioni per i database appartenenti al server padre, poiché questo metodo semplifica la gestione delle opzioni di ottimizzazione automatica per un numero elevato di database.

## <a name="next-steps"></a>Passaggi successivi

- Per consentire all'ottimizzazione automatica nel database SQL di Azure di gestire il carico di lavoro, vedere [Abilitare l'ottimizzazione automatica](sql-database-automatic-tuning-enable.md).
- Per esaminare e applicare manualmente le raccomandazioni di ottimizzazione automatica, vedere [Trovare e applicare raccomandazioni per le prestazioni](sql-database-advisor-portal.md).
- Per informazioni su come usare T-SQL per applicare e visualizzare le opzioni di ottimizzazione automatica, vedere [Manage automatic tuning via T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) (Gestire l'ottimizzazione automatica tramite T-SQL).
- Per altre informazioni sulla generazione di notifiche tramite posta elettronica per i suggerimenti di ottimizzazione automatica, vedere [Notifiche tramite posta elettronica per l'ottimizzazione automatica](sql-database-automatic-tuning-email-notifications.md).
- Per altre informazioni sull'intelligenza artificiale incorporata nell'ottimizzazione automatica, vedere [Artificial Intelligence tunes Azure SQL Databases](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/) (L'intelligenza artificiale ottimizza i database SQL di Azure).
- Per altre informazioni sul funzionamento dell'ottimizzazione automatica nel database SQL di Azure e in SQL Server 2017, vedere [SQL Server automatic tuning](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) (Ottimizzazione automatica di SQL Server).
