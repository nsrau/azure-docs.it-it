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
ms.date: 03/30/2020
ms.openlocfilehash: 7488fd5f8a572788933856f03bb0ad4351885704
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80518218"
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Ottimizzazione automatica nel database SQL di Azure

L'ottimizzazione automatica del database SQL di Azure offre prestazioni ottimali e carichi di lavoro stabili tramite l'ottimizzazione delle prestazioni continua basata su intelligenza artificiale e machine learning.

L'ottimizzazione automatica è un servizio per prestazioni intelligenti completamente gestito che usa l'intelligenza artificiale incorporata per monitorare le query eseguite su un database e ne migliora automaticamente le prestazioni. Questo risultato viene raggiunto adattando in modo dinamico il database ai carichi di lavoro modificabili e applicando le raccomandazioni di ottimizzazione. L'ottimizzazione automatica apprende orizzontalmente da tutti i database in Azure grazie all'intelligenza artificiale e migliora in modo dinamico le azioni di ottimizzazione. Maggiore è il tempo di esecuzione di un database con l'ottimizzazione automatica, migliore sarà il rendimento.

L'ottimizzazione automatica del database SQL di Azure potrebbe essere una delle funzionalità più importanti che è possibile abilitare per fornire carichi di lavoro di database stabile e di picco.

## <a name="what-can-automatic-tuning-do-for-you"></a>Cosa può fare l'ottimizzazione automatica

- Ottimizzazione automatizzata delle prestazioni per i database SQL di Azure
- Verifica automatizzata dei miglioramenti delle prestazioni
- Ripristino dello stato precedente automatizzato e correzione automatica
- Cronologia ottimizzazione
- Ottimizzazione degli script di azione T-SQL per le distribuzioni manuali
- Monitoraggio delle prestazioni dei carichi di lavoro proattivo
- Capacità di aumentare il numero di istanze in centinaia di migliaia di database
- Impatto positivo sulle risorse DevOps e sul costo totale di proprietà

## <a name="safe-reliable-and-proven"></a>Sicura, affidabile e collaudata

Le operazioni di ottimizzazione applicate ai database nel database SQL di Azure sono completamente sicure per le prestazioni dei carichi di lavoro più intensi. Il sistema è stato progettato in modo che non interferisca con i carichi di lavoro degli utenti. Le raccomandazioni di ottimizzazione automatizzata vengono applicate solo in caso di sottoutilizzo. Il sistema può anche disabilitare temporaneamente le operazioni di ottimizzazione per proteggere le prestazioni dei carichi di lavoro. In tal caso, il messaggio "disabilitato dal sistema" verrà visualizzato in portale di Azure. L'ottimizzazione automatica interessa i carichi di lavoro con la priorità delle risorse più elevata.

I meccanismi di ottimizzazione automatica sono ormai avanzati e sono stati perfezionati in diversi milioni di database in esecuzione su Azure. Le operazioni di ottimizzazione automatizzata applicate vengono verificate automaticamente per assicurare che sussista un miglioramento delle prestazioni dei carichi di lavoro. Le raccomandazioni per le prestazioni peggiorate vengono rilevate in modo dinamico e ripristinate tempestivamente. La cronologia di ottimizzazione registrata offre una chiara analisi dei miglioramenti apportati a ogni database SQL di Azure.

![Come funziona l'ottimizzazione automatica](./media/sql-database-automatic-tuning/how-does-automatic-tuning-work.png)

L'ottimizzazione automatica del database SQL di Azure condivide la logica di base con il motore di ottimizzazione automatica SQL Server. Per altre informazioni tecniche sul meccanismo di intelligenza artificiale incorporata, vedere [SQL Server automatic tuning](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) (Ottimizzazione automatica di SQL Server).

Per una panoramica del funzionamento dell'ottimizzazione automatica e per gli scenari di utilizzo tipici, vedere il video incorporato:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-automatic-tuning/player]

## <a name="enable-automatic-tuning"></a>Abilitare l'ottimizzazione automatica

È possibile [abilitare l'ottimizzazione automatica per i database singoli e in pool nel portale di Azure](sql-database-automatic-tuning-enable.md) o tramite l'istruzione T-SQL [ALTER database](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) . Per abilitare l'ottimizzazione automatica per i database di istanza in una distribuzione istanza gestita, è possibile usare l'istruzione T-SQL [ALTER database](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-mi-current) .

## <a name="automatic-tuning-options"></a>Opzioni di ottimizzazione automatica

Le opzioni di ottimizzazione automatica disponibili nel database SQL di Azure sono le seguenti:

| Opzione di ottimizzazione automatica | Supporto dei database singoli e in pool | Supporto del database dell'istanza |
| :----------------------------- | ----- | ----- |
| **Create index** : identifica gli indici che possono migliorare le prestazioni del carico di lavoro, crea gli indici e verifica automaticamente che le prestazioni delle query siano migliorate. | Sì | No |
| **Drop index** : identifica gli indici ridondanti e duplicati al giorno, tranne gli indici univoci e gli indici che non sono stati usati per molto tempo (>90 giorni). Si noti che questa opzione non è compatibile con le applicazioni che usano cambi di partizione e hint di indice. L'eliminazione degli indici inutilizzati non è supportata per i livelli di servizio Premium e business critical. | Sì | No |
| **Forza ultimo piano valido** (correzione automatica del piano): identifica le query SQL usando un piano di esecuzione più lento rispetto al piano corretto precedente e le query che usano l'ultimo piano valido noto anziché il piano regressione. | Sì | Sì |

### <a name="automatic-tuning-for-single-and-pooled-databases"></a>Ottimizzazione automatica per database singoli e in pool

Per ottimizzare le prestazioni del database, l'ottimizzazione automatica per i database singoli e in pool utilizza le indicazioni **create index**, **Drop index**e **Force Last Brave** database Advisor. Per altre informazioni, vedere [consigli di Advisor per database nel portale di Azure](sql-database-advisor-portal.md), in [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction)e nell' [API REST](https://docs.microsoft.com/rest/api/sql/serverautomatictuning).

È possibile applicare manualmente le indicazioni di ottimizzazione utilizzando il portale di Azure oppure è possibile consentire l'ottimizzazione automatica di applicare le indicazioni di ottimizzazione in modo autonomo. I vantaggi di consentire al sistema di applicare in modo autonomo le opzioni di ottimizzazione consistono nella convalida automatica della presenza di un aumento delle prestazioni del carico di lavoro e, se non vengono rilevati miglioramenti significativi delle prestazioni, nel ripristino dello stato prima dell'applicazione delle opzioni di ottimizzazione. Si noti che, in caso di query interessate da opzioni di ottimizzazione che non vengono eseguite di frequente, la fase di convalida può richiedere fino a 72 ore per impostazione predefinita.

Se si applicano le indicazioni di ottimizzazione tramite T-SQL, la convalida automatica delle prestazioni e i meccanismi di inversione non sono disponibili. Le raccomandazioni applicate in questo modo rimarranno attive e visualizzate nell'elenco delle raccomandazioni per l'ottimizzazione per 24-48 ore. prima del ritiro automatico del sistema. Se si desidera rimuovere prima una raccomandazione, è possibile eliminarla dalla portale di Azure.

Le opzioni di ottimizzazione automatica possono essere abilitate o disabilitate in modo indipendente per ogni database, oppure possono essere configurate nei server di database SQL e applicate a ogni database che eredita le impostazioni dal server. I server di database SQL possono ereditare le impostazioni predefinite di Azure per le impostazioni di ottimizzazione automatica. Le impostazioni predefinite di Azure in questo momento sono FORCE_LAST_GOOD_PLAN (abilitata), CREATE_INDEX (abilitata) e DROP_INDEX (disabilitata).

> [!IMPORTANT]
> A partire da marzo, 2020 le modifiche apportate alle impostazioni predefinite di Azure per l'ottimizzazione automatica diverranno effettive come segue:
>
> - Le nuove impostazioni predefinite di Azure verranno FORCE_LAST_GOOD_PLAN = Enabled, CREATE_INDEX = disabled e DROP_INDEX = disabled.
> - I server esistenti senza preferenze di ottimizzazione automatica configurata verranno automaticamente configurati per EREDITAre le nuove impostazioni predefinite di Azure. Questo vale per tutti i clienti che dispongono attualmente di impostazioni server per l'ottimizzazione automatica in uno stato non definito.
> - I nuovi server creati verranno automaticamente configurati in modo da EREDITAre le nuove impostazioni predefinite di Azure (a differenza di quanto prima quando la configurazione dell'ottimizzazione automatica era in uno stato non definito durante la creazione di un nuovo server).

La configurazione delle opzioni di ottimizzazione automatica in un server e l'ereditarietà delle impostazioni per i database appartenenti al server padre è un metodo consigliato per la configurazione dell'ottimizzazione automatica in quanto semplifica la gestione delle opzioni di ottimizzazione automatica per un numero elevato di database.

Per informazioni sulla creazione di notifiche di posta elettronica per le indicazioni di ottimizzazione automatica, vedere [notifiche tramite posta elettronica per l'ottimizzazione automatica](sql-database-automatic-tuning-email-notifications.md).

### <a name="automatic-tuning-for-instance-databases"></a>Ottimizzazione automatica per i database dell'istanza

L'ottimizzazione automatica per i database di istanza in una distribuzione di istanze gestite supporta solo **la forzatura dell'ultimo piano valido**. Per ulteriori informazioni sulla configurazione delle opzioni di ottimizzazione automatica tramite T-SQL, vedere la pagina relativa all' [ottimizzazione automatica introduce la correzione](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) automatica dei piani e la [correzione automatica dei piani](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning?view=sql-server-ver15#automatic-plan-correction).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sull'Intelligence incorporata usata nell'ottimizzazione automatica, vedere [intelligenza artificiale ottimizza i database SQL di Azure](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/).
- Per informazioni sul funzionamento dell'ottimizzazione automatica, vedere [indicizzazione automatica di milioni di database in database SQL di Microsoft Azure](https://www.microsoft.com/en-us/research/uploads/prod/2019/02/autoindexing_azuredb.pdf).
