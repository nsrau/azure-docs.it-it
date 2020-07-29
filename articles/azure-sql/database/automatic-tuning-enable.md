---
title: Abilitare l'ottimizzazione automatica
description: È possibile abilitare l'ottimizzazione automatica sul database usando facilmente il portale di Azure.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 12/03/2019
ms.openlocfilehash: 94ffe2852380d238e9829a8ab0cc69ebe82b74fe
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87373505"
---
# <a name="enable-automatic-tuning-in-the-azure-portal-to-monitor-queries-and-improve-workload-performance"></a>Abilitare l'ottimizzazione automatica nel portale di Azure per monitorare le query e migliorare le prestazioni del carico di lavoro
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]


Il database SQL di Azure gestisce automaticamente i servizi dati che monitorano costantemente le query e identifica l'azione che è possibile eseguire per migliorare le prestazioni del carico di lavoro. È possibile esaminare le raccomandazioni e applicarle manualmente oppure delegare al database SQL di Azure l'applicazione automatica delle azioni correttive, condizione nota come **modalità di ottimizzazione automatica**.

L'ottimizzazione automatica può essere abilitata a livello di server o di database tramite:

- Il[portale di Azure](automatic-tuning-enable.md#azure-portal)
- Chiamate [API REST](automatic-tuning-enable.md#rest-api)
- Comandi [T-SQL](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current)

> [!NOTE]
> Per Istanza gestita SQL di Azure, l'opzione supportata FORCE_LAST_GOOD_PLAN può essere configurata solo solo tramite [T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management) . Le opzioni di configurazione basata su portale di Azure e di ottimizzazione automatica degli indici descritte in questo articolo non si applicano alle Istanza gestita SQL di Azure.

> [!NOTE]
> In questo momento la configurazione delle opzioni di ottimizzazione automatica tramite il modello ARM (Azure Resource Manager) non è supportata.

## <a name="enable-automatic-tuning-on-server"></a>Abilitare l'ottimizzazione automatica nel server

A livello di server è possibile scegliere di ereditare o meno la configurazione dell'ottimizzazione automatica da "Impostazioni predefinite di Azure". Le impostazioni predefinite di Azure sono FORCE_LAST_GOOD_PLAN abilitata, CREATE_INDEX è disabilitata e DROP_INDEX è disabilitata.

> [!IMPORTANT]
> A partire da marzo 2020 nuove impostazioni predefinite di Azure per l'ottimizzazione automatica sono le seguenti:
>
> - FORCE_LAST_GOOD_PLAN = Enabled, CREATE_INDEX = disabled e DROP_INDEX = disabled.
> - I server esistenti senza preferenze di ottimizzazione automatica configurati vengono configurati automaticamente per EREDITAre le impostazioni predefinite di Azure. Questo vale per tutti i clienti che dispongono attualmente di impostazioni server per l'ottimizzazione automatica in uno stato non definito.
> - I nuovi server creati verranno automaticamente configurati in modo da EREDITAre i valori predefiniti di Azure (a differenza di quanto avviene in precedenza quando la configurazione dell'ottimizzazione automatica era in uno stato non definito durante la creazione del nuovo server)

### <a name="azure-portal"></a>Portale di Azure

Per abilitare l'ottimizzazione automatica in un [Server](logical-servers.md) nel database SQL di Azure, passare al server nel portale di Azure e quindi selezionare **ottimizzazione automatica** nel menu.

![Server](./media/automatic-tuning-enable/server.png)

> [!NOTE]
> Si noti che in questo momento l'opzione **DROP_INDEX** non è compatibile con le applicazioni che usano il cambio di partizione e gli hint per gli indici e non deve essere abilitata in questi casi. L'eliminazione degli indici inutilizzati non è supportata per i livelli di servizio Premium e business critical.

Selezionare le opzioni di ottimizzazione automatica che si vuole abilitare e selezionare **applica**.

Le opzioni di ottimizzazione automatica in un server vengono applicate a tutti i database nel server stesso. Per impostazione predefinita, tutti i database ereditano la configurazione dal server padre corrispondente, ma è possibile sostituire questa impostazione e specificarne una distinta per ogni database.

### <a name="rest-api"></a>API REST

Per altre informazioni sull'uso di un'API REST per abilitare l'ottimizzazione automatica in un **Server**, vedere eseguire l' [ottimizzazione automatica del server e ottenere i metodi http](/rest/api/sql/serverautomatictuning).

## <a name="enable-automatic-tuning-on-an-individual-database"></a>Abilitare l'ottimizzazione automatica per un database singolo

Il database SQL di Azure consente di specificare singolarmente la configurazione di ottimizzazione automatica per ogni database. A livello di database è possibile scegliere di ereditare o meno la configurazione dell'ottimizzazione automatica dal server padre o da "Impostazioni predefinite di Azure". Le impostazioni predefinite di Azure sono impostate su FORCE_LAST_GOOD_PLAN è abilitato, CREATE_INDEX è disabilitato e DROP_INDEX è disabilitato.

> [!TIP]
> L'indicazione generale è quella di gestire la configurazione di ottimizzazione automatica a **livello di server** , in modo che le stesse impostazioni di configurazione possano essere applicate automaticamente a ogni database. Configurare l'ottimizzazione automatica in un singolo database solo se è necessario che per tale database le impostazioni siano diverse rispetto ai database che ereditano le impostazioni dallo stesso server.

### <a name="azure-portal"></a>Portale di Azure

Per abilitare l'ottimizzazione automatica in un **singolo database**, passare al database nel portale di Azure e selezionare **ottimizzazione automatica**.

Singole impostazioni di ottimizzazione automatica possono essere configurate separatamente per ogni database. È possibile configurare manualmente una singola opzione di ottimizzazione automatica o specificare che un'opzione deve ereditare le impostazioni dal server.

![Database](./media/automatic-tuning-enable/database.png)

Si noti che al momento l'opzione DROP_INDEX non è compatibile con applicazioni che usano i cambi di partizione e gli hint di indice e non deve essere abilitata in questi casi.

Dopo aver selezionato la configurazione desiderata, fare clic su **Applica**.

### <a name="rest-api"></a>API REST

Per altre informazioni sull'uso di un'API REST per abilitare l'ottimizzazione automatica in un singolo database, vedere [aggiornamento automatico del database SQL di Azure e metodi HTTP Get](/rest/api/sql/databaseautomatictuning).

### <a name="t-sql"></a>T-SQL

Per abilitare l'ottimizzazione automatica per un singolo database con Transact-SQL, connettersi al database ed eseguire la query seguente:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING = AUTO | INHERIT | CUSTOM
```

L'impostazione dell'ottimizzazione automatica su AUTO verrà applicata alle impostazioni predefinite di Azure. Se viene impostata su INHERIT, la configurazione dell'ottimizzazione automatica verrà ereditata dal server padre. Se si sceglie CUSTOM, l'ottimizzazione automatica dovrà essere configurata manualmente.

Per configurare le singole opzioni di ottimizzazione automatica con Transact-SQL, connettersi al database ed eseguire la query seguente:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON, CREATE_INDEX = ON, DROP_INDEX = OFF)
```

Se si imposta l'opzione di ottimizzazione singola su ON, qualsiasi impostazione ereditata dal database e Abilita l'opzione di ottimizzazione. L'impostazione di questa opzione su OFF consente inoltre di eseguire l'override di qualsiasi impostazione ereditata dal database e disabilitare l'opzione di ottimizzazione. L'opzione di ottimizzazione automatica, per la quale viene specificato il valore predefinito, erediterà la configurazione di ottimizzazione automatica dalle impostazioni a livello di server.  

> [!IMPORTANT]
> Nel caso della [replica geografica attiva](auto-failover-group-overview.md), l'ottimizzazione automatica deve essere configurata solo nel database primario. Le azioni di ottimizzazione applicate automaticamente, ad esempio la creazione o l'eliminazione di indici, verranno replicate automaticamente nel database secondario di sola lettura. Se si tenta di abilitare l'ottimizzazione automatica tramite T-SQL nel database secondario di sola lettura, si verificherà un errore perché una configurazione di ottimizzazione diversa per il database secondario di sola lettura non è supportata.
>

Per ulteriori informazioni sulle opzioni T-SQL per la configurazione dell'ottimizzazione automatica, vedere [Opzioni ALTER database set (Transact-SQL)](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current).

## <a name="disabled-by-the-system"></a>Disabilitazione da parte del sistema

L'ottimizzazione automatica esegue il monitoraggio di tutte le azioni eseguite a livello di database e in alcuni casi potrebbe non funzionare correttamente nel database. In questa situazione, l'opzione di ottimizzazione sarà disabilitata dal sistema. Nella maggior parte dei casi ciò accade perché Query Store non è abilitato o è in stato di sola lettura in un database specifico.

## <a name="permissions"></a>Autorizzazioni

Poiché l'ottimizzazione automatica è una funzionalità di Azure, per usarla è necessario usare i ruoli predefiniti di Azure. L'utilizzo dell'autenticazione SQL non sarà sufficiente per utilizzare la funzionalità del portale di Azure.

Per usare l'ottimizzazione automatica, l'autorizzazione minima necessaria per concedere all'utente è il ruolo predefinito [collaboratore database SQL](../../role-based-access-control/built-in-roles.md#sql-db-contributor) di Azure. È inoltre possibile considerare l'utilizzo di ruoli con privilegi più elevati, ad esempio collaboratore SQL Server, collaboratore SQL Istanza gestita, collaboratore e proprietario.

## <a name="configure-automatic-tuning-e-mail-notifications"></a>Configurare notifiche tramite posta elettronica per l'ottimizzazione automatica

Vedere la Guida all' [ottimizzazione automatica delle notifiche tramite posta elettronica](automatic-tuning-email-notifications-configure.md) .

## <a name="next-steps"></a>Passaggi successivi

- Leggere l'[articolo sull'ottimizzazione automatica](automatic-tuning-overview.md) per altre informazioni sull'ottimizzazione automatica e sull'utilità di questa funzione per il miglioramento delle prestazioni.
- Vedere le [Raccomandazioni per le prestazioni](database-advisor-implement-performance-recommendations.md) per una panoramica delle raccomandazioni per le prestazioni per il database SQL di Azure.
- Vedere l'articolo [Informazioni dettagliate prestazioni query](query-performance-insight-use.md) per informazioni sull'analisi dell'impatto sulle prestazioni delle query principali.
