---
title: Monitoraggio del portale di gestione del carico di lavoro
description: Indicazioni per il monitoraggio del portale di gestione del carico di lavoro in Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 13b0dc3af524b16430408f8a920c7477c412414d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91362730"
---
# <a name="azure-synapse-analytics--workload-management-portal-monitoring"></a>Azure Synapse Analytics - Monitoraggio del portale di gestione del carico di lavoro

Questo articolo illustra come monitorare l'utilizzo delle risorse e l'attività di query del [gruppo di carico di lavoro](sql-data-warehouse-workload-isolation.md#workload-groups).
Per informazioni dettagliate su come configurare Esplora metriche di Azure, vedere l'articolo [Introduzione a Esplora metriche di Azure](../../azure-monitor/platform/metrics-getting-started.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).  Per informazioni dettagliate su come monitorare l'utilizzo delle risorse di sistema, vedere la sezione [Utilizzo delle risorse](sql-data-warehouse-concept-resource-utilization-query-activity.md#resource-utilization) nella documentazione relativa al monitoraggio di Azure Synapse Analytics.
Sono disponibili due diverse categorie di metriche del gruppo di carico di lavoro per il monitoraggio della gestione del carico di lavoro: allocazione delle risorse e attività di query.  Queste metriche possono essere suddivise e filtrate in base al gruppo di carico di lavoro.  Le metriche possono essere suddivise e filtrate a seconda che siano definite dal sistema (gruppi di carico di lavoro della classe di risorse) o definite dall'utente (create dall'utente con la sintassi [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)).

## <a name="workload-management-metric-definitions"></a>Definizioni delle metriche di gestione del carico di lavoro

|Nome misurazione                    |Descrizione  |Tipo di aggregazione |
|-------------------------------|-------------|-----------------|
|Percentuale limite di risorse effettiva | La *percentuale limite di risorse effettiva* è un limite rigido sulla percentuale di risorse accessibili dal gruppo di carico di lavoro, prendendo in considerazione la *percentuale risorse minima effettiva* allocata per altri gruppi di carico di lavoro. La metrica *Percentuale limite di risorse effettiva* viene configurata usando il parametro `CAP_PERCENTAGE_RESOURCE` della sintassi [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  Il valore effettivo viene descritto qui.<br><br>Ad esempio, se un gruppo di carico di lavoro `DataLoads` viene creato con `CAP_PERCENTAGE_RESOURCE` = 100 e un altro gruppo di carico di lavoro viene creato con una percentuale risorse minima effettiva del 25%, la *percentuale limite di risorse effettiva* per il gruppo di carico di lavoro `DataLoads` è del 75%.<br><br>La *percentuale limite di risorse effettiva* determina il limite superiore della concorrenza (e quindi la velocità effettiva potenziale) che un gruppo di carico di lavoro può ottenere.  Se è richiesta un'ulteriore velocità effettiva oltre a quella attualmente segnalata dalla metrica *Percentuale limite di risorse effettiva*, aumentare `CAP_PERCENTAGE_RESOURCE`, diminuire `MIN_PERCENTAGE_RESOURCE` per altri gruppi di carico di lavoro o aumentare il numero di istanze per aggiungere altre risorse.  La riduzione di `REQUEST_MIN_RESOURCE_GRANT_PERCENT` può aumentare la concorrenza, ma potrebbe non aumentare la velocità effettiva complessiva.| Min, Avg, Max |
|Percentuale risorse minima effettiva |La *percentuale risorse minima effettiva* è la percentuale minima di risorse riservate e isolate per il gruppo di carico di lavoro che prende in considerazione il livello di servizio minimo.  La metrica Percentuale risorse minima effettiva viene configurata usando il parametro `MIN_PERCENTAGE_RESOURCE` della sintassi [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  Il valore effettivo viene descritto [qui](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#effective-values).<br><br>Usare il tipo di aggregazione Sum quando questa metrica non è filtrata e non è suddivisa per monitorare l'isolamento del carico di lavoro totale configurato nel sistema.<br><br>La *percentuale risorse minima effettiva* determina il limite inferiore della concorrenza garantita (e quindi la velocità effettiva garantita) che un gruppo di carico di lavoro può ottenere.  Se sono richieste ulteriori risorse garantite oltre a quelle attualmente segnalate dalla metrica *Percentuale risorse minima effettiva*, aumentare il parametro `MIN_PERCENTAGE_RESOURCE` configurato per il gruppo di carico di lavoro.  La riduzione di `REQUEST_MIN_RESOURCE_GRANT_PERCENT` può aumentare la concorrenza, ma potrebbe non aumentare la velocità effettiva complessiva. |Min, Avg, Max|
|Query attive del gruppo di carico di lavoro  |Questa metrica riporta le query attive all'interno del gruppo di carico di lavoro.  L'uso di questa metrica non filtrata e non suddivisa consente di visualizzare tutte le query attive in esecuzione nel sistema.|SUM         |
|Allocazione del gruppo di carico di lavoro per percentuale risorse massima |Questa metrica visualizza la percentuale di allocazione delle risorse rispetto alla *percentuale limite di risorse effettiva* per gruppo di carico di lavoro.  Questa metrica consente di utilizzare in modo efficace il gruppo di carico di lavoro.<br><br>Si consideri un gruppo di carico di lavoro `DataLoads` con una *percentuale limite di risorse effettiva* del 75% e `REQUEST_MIN_RESOURCE_GRANT_PERCENT` configurato al 25%.  Il valore di *Allocazione del gruppo di carico di lavoro per percentuale risorse massima* filtrato per `DataLoads` sarebbe del 33% (25%/75%) se fosse eseguita una singola query in questo gruppo di carico di lavoro.<br><br>Usare questa metrica per identificare l'utilizzo di un gruppo di carico di lavoro.  Un valore prossimo al 100% indica che vengono usate tutte le risorse disponibili per il gruppo di carico di lavoro.  Inoltre, la metrica *Query accodate del gruppo di carico di lavoro* per lo stesso gruppo di carico di lavoro indicante un valore maggiore di zero specifica che il gruppo di carico di lavoro utilizzerebbe ulteriori risorse qualora allocate.  Viceversa, se questa metrica è costantemente bassa e la metrica *Query attive del gruppo di carico di lavoro* è bassa, il gruppo di carico di lavoro non viene utilizzato.  Questa situazione è particolarmente problematica se la *percentuale limite di risorse effettiva* è maggiore di zero, in quanto indicherebbe un [isolamento del carico di lavoro sottoutilizzato](#underutilized-workload-isolation).|Min, Avg, Max |
|Allocazione del gruppo di carico di lavoro per percentuale di sistema | Questa metrica visualizza la percentuale di allocazione delle risorse rispetto all'intero sistema.<br><br>Si consideri un gruppo di carico di lavoro `DataLoads` con `REQUEST_MIN_RESOURCE_GRANT_PERCENT` configurato al 25%.  Il valore di *Allocazione del gruppo di carico di lavoro per percentuale di sistema* filtrato per `DataLoads` sarebbe del 25% (25%/100%) se fosse eseguita una singola query in questo gruppo di carico di lavoro.|Min, Avg, Max |
|Timeout query del gruppo di carico di lavoro |Indica le query per il gruppo di carico di lavoro che hanno raggiunto il timeout.  I timeout della query segnalati da questa metrica indicano solo quelli dopo l'avvio dell'esecuzione della query (non è incluso il tempo di attesa attribuibile alle attese di risorse o blocchi).<br><br>Il timeout della query viene configurato usando il parametro `QUERY_EXECUTION_TIMEOUT_SEC` nella sintassi [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  L'aumento del valore potrebbe ridurre il numero di timeout della query.<br><br>Prendere in considerazione l'aumento del parametro `REQUEST_MIN_RESOURCE_GRANT_PERCENT` per il gruppo di carico di lavoro per ridurre il numero di timeout e allocare più risorse per ogni query.  Si noti che aumentando `REQUEST_MIN_RESOURCE_GRANT_PERCENT` si riduce la quantità di concorrenza per il gruppo di carico di lavoro. |SUM |
|Query accodate del gruppo di carico di lavoro | Indica le query per il gruppo di carico di lavoro attualmente accodate in attesa dell'avvio dell'esecuzione.  Le query possono essere accodate perché sono in attesa di risorse o blocchi.<br><br>È possibile che le query siano in attesa per diversi motivi.  Se il sistema è in overload e la richiesta di concorrenza è superiore a quella disponibile, le query verranno accodate.<br><br>Prendere in considerazione l'aggiunta di altre risorse al gruppo di carico di lavoro aumentando il parametro `CAP_PERCENTAGE_RESOURCE` nell'istruzione [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  Se `CAP_PERCENTAGE_RESOURCE` è maggiore della metrica *Percentuale limite di risorse effettiva*, l'isolamento del carico di lavoro configurato per un altro gruppo di carico di lavoro influirà sulle risorse allocate a questo gruppo di carico di lavoro.  Valutare la possibilità di ridurre `MIN_PERCENTAGE_RESOURCE` per altri gruppi di carico di lavoro o di aumentare l'istanza per aggiungere altre risorse. |SUM |

## <a name="monitoring-scenarios-and-actions"></a>Scenari di monitoraggio e azioni

Di seguito è riportata una serie di configurazioni dei grafici per evidenziare l'utilizzo della metrica di gestione del carico di lavoro per la risoluzione dei problemi, insieme alle azioni associate per risolvere tali problemi.

### <a name="underutilized-workload-isolation"></a>Isolamento del carico di lavoro sottoutilizzato

Si considerino il gruppo di carico di lavoro e la configurazione del classificatore seguenti in cui viene creato un gruppo di carico di lavoro denominato `wgPriority` e viene eseguito il mapping di `membername` *TheCEO* usando il classificatore del carico di lavoro `wcCEOPriority`.  Per il gruppo di carico di lavoro `wgPriority` è stato configurato un isolamento del carico di lavoro del 25% (`MIN_PERCENTAGE_RESOURCE` = 25).  A ogni query inviata da *TheCEO* viene assegnato il 5% delle risorse di sistema (`REQUEST_MIN_RESOURCE_GRANT_PERCENT` = 5).

```sql
CREATE WORKLOAD GROUP wgPriority
WITH ( MIN_PERCENTAGE_RESOURCE = 25
      ,CAP_PERCENTAGE_RESOURCE = 50
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5);

CREATE WORKLOAD CLASSIFIER wcCEOPriority
WITH ( WORKLOAD_GROUP = 'wgPriority'
      ,MEMBERNAME = 'TheCEO');
```

Il grafico seguente viene configurato come indicato:<br>
Metrica 1: *Percentuale risorse minima effettiva* (aggregazione Avg, `blue line`)<br>
Metrica 2: *Allocazione del gruppo di carico di lavoro per percentuale di sistema* (aggregazione Avg, `purple line`)<br>
Filtro: [Gruppo di carico di lavoro] = `wgPriority`<br>
![Screenshot mostra un grafico con due metriche e un filtro.](./media/sql-data-warehouse-workload-management-portal-monitor/underutilized-wg.png)
Il grafico mostra che con l'isolamento del carico di lavoro del 25% viene usato in media solo il 10%.  In questo caso, il valore del parametro `MIN_PERCENTAGE_RESOURCE` potrebbe essere ridotto a un valore compreso tra 10 e 15 per consentire ad altri carichi di lavoro nel sistema di utilizzare le risorse.

### <a name="workload-group-bottleneck"></a>Collo di bottiglia del gruppo di carico di lavoro

Si considerino il gruppo di carico di lavoro e la configurazione del classificatore seguenti in cui viene creato un gruppo di carico di lavoro denominato `wgDataAnalyst` e viene eseguito il mapping di `membername` *DataAnalyst* usando il classificatore del carico di lavoro `wcDataAnalyst`.  Per il gruppo di carico di lavoro `wgDataAnalyst` sono stati configurati un isolamento del carico di lavoro del 6% (`MIN_PERCENTAGE_RESOURCE` = 6) e un limite di risorse del 9% (`CAP_PERCENTAGE_RESOURCE` = 9).  A ogni query inviata da *DataAnalyst* viene assegnato il 3% delle risorse di sistema (`REQUEST_MIN_RESOURCE_GRANT_PERCENT` = 3).

```sql
CREATE WORKLOAD GROUP wgDataAnalyst  
WITH ( MIN_PERCENTAGE_RESOURCE = 6
      ,CAP_PERCENTAGE_RESOURCE = 9
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 3);

CREATE WORKLOAD CLASSIFIER wcDataAnalyst
WITH ( WORKLOAD_GROUP = 'wgDataAnalyst'
      ,MEMBERNAME = 'DataAnalyst');
```

Il grafico seguente viene configurato come indicato:<br>
Metrica 1: *Percentuale limite di risorse effettiva* (aggregazione Avg, `blue line`)<br>
Metrica 2: *Allocazione del gruppo di carico di lavoro per percentuale risorse massima* (aggregazione Avg, `purple line`)<br>
Metrica 3: *Query accodate del gruppo di carico di lavoro* (aggregazione Sum, `turquoise line`)<br>
Filtro: [Gruppo di carico di lavoro] = `wgDataAnalyst`<br>
![Screenshot mostra un grafico con le tre metriche e il filtro.](./media/sql-data-warehouse-workload-management-portal-monitor/bottle-necked-wg.png)
Il grafico mostra che con un limite del 9% sulle risorse, il gruppo del carico di lavoro è pari al 90% + usato (dal *gruppo di carico di lavoro allocato per la metrica massima percentuale di risorse*).  Si verifica un accodamento costante delle query come indicato dalla metrica *Query accodate del gruppo di carico di lavoro*.  In questo caso, l'aumento di `CAP_PERCENTAGE_RESOURCE` a un valore superiore al 9% consentirà l'esecuzione simultanea di più query.  L'aumento di `CAP_PERCENTAGE_RESOURCE` presuppone che siano disponibili risorse sufficienti e non isolate da altri gruppi di carico di lavoro.  Verificare il limite aumentato controllando la metrica *Percentuale limite di risorse effettiva*.  Se si desidera una maggiore velocità effettiva, provare anche ad aumentare `REQUEST_MIN_RESOURCE_GRANT_PERCENT` a un valore superiore a 3.  L'aumento di `REQUEST_MIN_RESOURCE_GRANT_PERCENT` potrebbe consentire una maggiore velocità di esecuzione delle query.

## <a name="next-steps"></a>Passaggi successivi

- [Avvio rapido: Configurare l'isolamento del carico di lavoro tramite T-SQL](quickstart-configure-workload-isolation-tsql.md)<br>
- [CREATE WORKLOAD GROUP (Transact-SQL)](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)<br>
- [CREATE WORKLOAD CLASSIFIER (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)<br>
- [Monitoraggio dell'utilizzo delle risorse](sql-data-warehouse-concept-resource-utilization-query-activity.md)
